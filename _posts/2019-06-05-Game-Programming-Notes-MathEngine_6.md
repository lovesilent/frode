---
layout: post
title:  "Game Programming Notes -- Math_Engine_6"
date:   2019-06-05
banner_image: math.jpg
tags: [Game Programming]
---
《3D游戏编程大师技巧》 笔记

   Math_Engine—— 数学引擎API（2）

<!--more-->

## Math_Engine

------

### 数学引擎API清单（2）

下面介绍整个数学引擎API的下半部分

<u>3D平面支持函数</u>

该数学引擎中添加了对3D平面抽象实体的支持。实际上，在99%以上的时间，我们处理的都是封闭多边形（位于同一个平面中），因此后面可能需要对这些函数进行改进。现在，我们只要知道如何定义平面，然后使用平面操作即可。

现在采用点-法线方式来表示平面，如下图所示：

{% include image_caption.html imageurl="/images/posts/Define_3D_plane_point_normal.png" title="Define the 3D plane with a point-normal" caption="Define the 3D plane with a point-normal" %}
$$
nx*(x-x0)+ny*(y-y0)+nz*(z-z0)=0
$$
其中
$$
\mathbf{n}=<nx,ny,nz>, \quad \mathbf{p0}=<x0,y0,z0>
$$
使用这种格式时，我们将平面存储为平面的法线和平面上的一个点。法线不必是单位向量。下面是3D平面的结构：

```c++
typedef struct PLANE3D_TPY
{
    POINT3D p0; //平面上的一点
    VECTOR3D n; //平面上的法线（不必是单位向量）
} PLANE3D, *PLANE3D_PTR;
```

同样，数学库中的很多函数旨在简化一些经常需要执行的重复性计算，或者简化数学对象或几何对象的表示。

```c++
void PLANE3D_Init(PLANE3D_PTR plane, POINT3D_PTR p0, VECTOR3D_PTR normal, int normalize);
```

PLANE3D_Init()函数使用指定的点和法线来初始化一个3D平面。另外，该函数可以对指定的法线进行归一化，使其长度为1.0。要进行归一化，需要将normalize参数设置为TRUE；否则，它将设置为FALSE。在很多光照和背面消除计算中，知道多边形或平面的法线长度为1.0会很有帮助。

```c++
float Compute_Point_In_Plane3D(POINT3D_PTR pt, PLANE3D_PTR plane)
{
	//检测点是在平面上、正半空间还是负半空间
    float hs = plane->n.x*(pt->x - plane->p0.x) + plane->n.y*(pt->y - plane->p0.y) + plane->n.z*(pt->z - plane->p0.z);
    // 返回检测结果
    return(hs);
}
```

用途：它是一个很有用的函数。它判断指定点位于哪个半空间中。很多时候，需要判断某样东西位于平面的哪一边，该函数正好提供了这种功能。下图说明了该函数的逻辑。如果指定点位于平面上，该函数返回0.0；如果位于正半空间中，返回一个正数；如果位于负半空间中，则返回负值。

{% include image_caption.html imageurl="/images/posts/Half_space_lc.png" title="Half space logic coding" caption="Half space logic coding" %}

```c++
int Intersect_Parm_Line3D_Plane3D(PARMLINE3D_PTR pline, PLANE3D_PTR plane, float *t, POINT3D_PTR pt)
{
    //这个函数计算参数化直线与平面的交点
    //计算交点时，该函数将参数化直线视为无穷长
    //如果交点在线段pline上，则参数t的值位于范围【0，1】内
    //另外，如果不相交，该函数返回0，如果交点在线段上，返回1
    //如果交点不在线段上，2返回，如果线段位于平面上，则返回3
    
    //首先判断线段和平面是否平行
    //如果是，则它们不可能相交，除非线段位于平面上
    float plane_dot_line = VECTOR3D_Dot(&pline->v,&plane->n);
    if(fabs(plane_dot_line)<=EPSILON_E5)
    {
        //线段与平面平行
        //它是否在平面上
        if(fabs(Compute_Point_In_Plane3D(&pline->p0,plane))<=EPSILON_E5)
            return(PARM_LINE_INTERSECT_EVERYWHERE);
        else
            return(PARM_LINE_NO_INTERSECT);
    }
    
    // 可以按照下述方式计算交点对应的值
    // a*(x0+vx*t) + b*(y0+vy*t) + c*(z0+vz*t) + d =0
    // t=-(a*x0+b*y0+c*z0+d)/(a*vx+b*vy+c*vz)
    // x0、y0、z0、vx、vy、vz定义了线段
    // d=(-a*xp0-b*yp0-c*zp0), xp0、yp0、zp0定义了平面上的一个点
    *t=-(plane->n.x*pline->p0.x + 
         plane->n.y*pline->p0.y + 
         plane->n.z*pline->p0.z -
         plane->n.x*plane->p0.x - 
         plane->n.y*plane->p0.y -
         plane->n.z*plane->p0.z);
    // 将t带入参数化直线方程，以计算x、y、z
    pt->x = pline->p0.x + pline->v.x*(*t);
    pt->y = pline->p0.y + pline->v.y*(*t);
    pt->z = pline->p0.z + pline->v.z*(*t);
    
    //检测t是否在范围【0，1】内
    if(*t>=0.0 && *t<=1.0)
        return(PARM_LINE_INTERSECT_IN_SEGMENT);
    else
        return(PARM_LINE_INTERSECT_OUT_SEGMENT);
}
```

用途：该函数计算一条3D参数化直线与一个3D平面的交点，将交点处的参数值存储到t中，并将交点存储到pt中。然而，在使用这些数据之前，需要测试该函数的返回值，以确定是否存在交点。返回值与计算2D交点的函数相同，但增加了一个标记，用于指出点位于平面上。

```c++
#define PARM_LINE_NO_INTERSECT 0
#define PARM_LINE_INTERSECT_IN_SEGMENT 1
#define PARM_LINE_INTERSECT_OUT_SEGMENT 2
#define PARM_LINE_INTERSECT_EVERYWHERE 3
```

<u>四元数支持函数</u>

必须指出的是，编写四元数并不容易！问题在于必须确保它们能够正常工作。需要在草稿纸上进行双重检查，因为四元数没有太多的物理意义，无法以图形方式进行检查。然而，最终它们都能正常工作。四元函数使得很多操作都变得非常简单。例如，对于绕任意直线旋转的问题，如果使用四元数来求解将非常容易，而使用欧拉方程则非常复杂。前面介绍过，四元数就是一个超复数，它有4个分量，其中一个为实部（通常称为q0或w），另外3个为虚部（通常称为q1、q2、q3)。

```c++
//为了能够以多种访问形式来访问它们。下面的数据结构QUAT使用几个共用体实现了这种功能
typedef struct QUAT_TYP
{
	union
	{
		float M[4]; //按顺序w、x、y、z 以数组方式存储
        // “向量部分 + 实部“ 格式
		struct
		{
			float q0; //实部
			VECTOR3D qv;//虚部
		};
        struct
        {
            float w,x,y,z;
        };
	};
} QUAT, *QUAT_PTR;
```

```c++
void VECTOR3D_Theta_To_QUAT(QUAT_PTR q, VECTOR3D_PTR v, float theta)
{
	//使用一个3D方向向量和一个角度来初始化一个四元数
    //方向向量必须是单位向量，角度的单位为弧度
    
    float theta_div_2 = (0.5)*theta; //计算theta/2
    
    //计算四元数
    //预先计算以节省时间
    float sinf_theta=sinf(theta_div_2);
    q->x = sinf_theta * v->x;
    q->y = sinf_theta * v->y;
    q->z = sinf_theta * v->z;
    q->w = cosf(theta_div_2);
}// end VECTOR3D_Theta_To_QUAT
```

用途：该函数根据方向向量v和角度theta创建一个旋转四元数。下图说明了其中的关系。这个函数主要用于创建对点进行旋转的四元数。注意，方向向量v必须为单位向量。另外，4D向量版本将丢弃w分量。

{% include image_caption.html imageurl="/images/posts/Create_rotation_quaternion.png" title="Create a rotation quaternion" caption="Create a rotation quaternion" %}

```c++
void EulerZYX_To_QUAT(QUAT_PTR q, float theta_z, float theta_y, float theta_x)
{
    // 这个函数根据绕x、y、z旋转的角度，创建一个zyx顺序进行旋转对应的四元数
    // 注意，还有11个根据旋转角度创建四元数的函数
    
    //预先计算一些值
    float cos_z_2 = 0.5*cosf(theta_z);
    float cos_y_2 = 0.5*cosf(theta_y);
    float cos_x_2 = 0.5*cosf(theta_x);
    
    float sin_z_2 = 0.5*sinf*(theta_z);
    float sin_y_2 = 0.5*sinf*(theta_y);
    float sin_x_2 = 0.5*sinf*(theta_x);
    
    //计算四元数
    q->w = cos_z_2*cos_y_2*cos_x_2 + sin_z_2*sin_y_2*sin_x_2;
    q->x = cos_z_2*cos_y_2*sin_x_2 - sin_z_2*sin_y_2*cos_x_2;
    q->y = cos_z_2*sin_y_2*cos_x_2 + sin_z_2*cos_y_2*sin_x_2;
    q->x = sin_z_2*cos_y_2*cos_x_2 - cos_z_2*sin_y_2*sin_x_2;
    
} // EulerZYX_To_QUAT
```

用途：该函数根据z、y、x旋转的欧拉角创建一个旋转四元数。它是一种基本的相机转换。当然，旋转顺序共有6种（3的阶乘），但这种顺序是最常见的。使用这个函数可将欧拉旋转角转换为四元数。

```c++
void QUAT_To_VECTOR3D_Theta(QUAT_PTR q, VECTOR3D_PTR v, float *theta)
{
    // 这个函数将一个单位四元数转换为一个单位方向向量和一个绕该方向向量旋转的角度
    
    // 提取角度
    *theta = acosf(q->w);
    
    //预先计算以节省时间
    float sinf_theta_inv = 1.0/sinf(*theta);
    
    //计算向量
    v->x = q->x*sinf_theta_inv;
    v->y = q->y*sinf_theta_inv;
    v->z = q->z*sinf_theta_inv;
    
    //将角度乘以2
    *theta*=2;
}// end QUAT_To_VECTOR3D_Theta
```

用途：该函数将一个单位旋转四元数转换为一个单位3D向量和与一个绕该向量旋转的角度theta。

```c++
void QUAT_Mul(QUAT_PTR q1, QUAT_PTR q2, QUAT_PTR qprod)
{
    // 这个函数将两个四元数相乘
    // 先介绍一种蛮干方法
    /*
    qprod->w = q1->w*q2->w - q1->x*q2->x - q1->y*q2->y - q1->z * q2->z;
    qprod->x = q1->w*q2->x + q1->x*q2->w + q1->y*q2->z - q1->z*q2->y;
    qprod->y = q1->w*q2->y - q1->x*q2->z + q1->y*q2->w - q1->z*q2->x;
    qprod->z = q1->w*qw->z + q1->x*q2->y - q1->y*q2->x + q1->z*q2->w;
    */
    
    // 然后介绍下述先计算共用因子的方法，以减少乘法运算次数
    float prd_0 = (q1->z - q1->y) * (q2->y - q2->z);
    float prd_1 = (q1->w + q1->x) * (q2->w - q2->x);
    float prd_2 = (q1->w - q1->x) * (q2->y + q2->z);
    float prd_3 = (q1->y + q1->z) * (q2->w - q2->x);
    float prd_4 = (q1->z - q1->x) * (q2->x - q2->y);
    float prd_5 = (q1->z + q1->x) * (q2->x + q2->y);
    float prd_6 = (q1->w + q1->y) * (q2->w - q2->z);
    float prd_7 = (q1->w - q1->y) * (q2->w + q2->z);
    
    float prd_8 = prd_5 + prd_6 + prd_7;
    float prd_9 = 0.5 * (prd_4 + prd_8);
    
    // 现在使用临时乘积计算最后结果
    qprod->w = prd_0 + prd_9 - prd_5;
    qprod->x = prd_1 + prd_9 - prd_8;
    qprod->y = prd_2 + prd_9 - prd_7;
    qprod->z = prd_3 + prd_9 - prd_6;
} // end QUAT_Mul
```

最开始使用蛮力方法，根据乘法定义将四元数相乘（共16次乘法，12次加法）；然后使用一些代数公式，将计算简化为9次乘法和27次加法。通常，可能后一种方法更好，但在浮点处理器上可能并非如此。

<u>定点数支持函数</u>

在数学引擎中添加一些操作定点数的函数是值得的。而且，在奔腾以上的处理器上，浮点数单元的速度与整数单元一样快，甚至更快，因此不再使用定点数来执行3D数学运算。然而，很多时候，同时使用浮点数单元和整数单元可以编写出真正优秀的算法，因此了解如何表示和操作定点数仍是件好事。

```c++
// 与定点数运算相关的常量
#define FIXP16_SHIFT 16
#define FIXP16_MAG 65536
#define FIXP16_DP_MASK 0x0000ffff
#define FIXP16_WP_MASK 0xffff0000
#define FIXP16_ROUND_UP 0x00008000

// 1 使用整数来创建定点数，只需将整数左移FIXP16_SHIFT位，将其放到定点数的整数部分中。但切记不要让定点数溢出。
FIXP16 fp1 = (100 << FIXP16);

//2 使用浮点数创建定点数，整数的二进制表示与浮点数的二进制表示有很大的不同。因此，需要对浮点数进行缩放，缩放比例与对整数进行移位的程度相同。
FIXP16 fp1 = (int)(100.4*65536);

//3 转换回浮点数
float ((float)fp)/65536.0;
```

关于加/减法、乘/除法也就不再详细介绍了。

<u>方程求解支持函数</u>

接下来介绍两个很有用的函数，能够求解A*X=B形式的方程组。

```c++
int Solve_2X2_System(MATRIX2X2_PTR A, MATRIX1X2_PTR X, MATRIX1X2_PTR B)
{
	//使用克莱姆法则和行列式计算X=A(-1)*B
    //1 计算A的行列式的值
    float det_A = Mat_Det_2X2(A);
    
    //检测det_A是否为0，如果是，则方程组无解
    if(fabs(det_A)<EPSILON_E5)
        return(0);
    
    //2 分别将矩阵A中的X、Y列替换为矩阵B，得到分子矩阵以计算x和y的解
    MATRIX2X2 work_mat; //工作矩阵
    
    // 求解 x
    
    // 将A复制到工作矩阵中
    MAT_COPY_2X2(A, &work_mat);
    
    // 替换X列
    MAT_COLUMN_SWAP_2X2(&work_mat,0,B);
    
    // 计算替换后的行列式值
    float det_ABx = Mat_Det_2X2(&work_mat);
    
    // 计算X的解
    X->M00 = det_ABx/det_A;
    
    // 求解Y
    
    // 将A复制到工作矩阵中
    MAT_COPY_2X2(A, &work_mat);
    
    // 替换Y列
    MAT_COLUMN_SWAP_2X2(&work_mat,1,B);
    
    //计算替换后的行列式值
    float det_ABy = Mat_Det_2X2(&work_mat);
    
    // 计算Y的解
    X->M01 = det_ABy/det_A;
    
    // 成功返回 
    return(1);
} // end Solve_2X2_System
```

用途：该函数用于求解方程组A*X=B，其中A为2x2，X为1x2，B为1x2矩阵。如果有解，将其存储在B中，且函数返回1；否则函数返回0，且B未定义。另外，请参考函数代码，查看函数的内部结构。该函数使用克莱姆法则来求解方程组。



{% include image_full.html imageurl="/images/posts/naiguan.jpg" title="Avengers: End Game" caption="奶罐" %}


### Poetry

>你要相信你自己是最好的，
>
>不是第二，第三，
>
>你就是第一！
>
><cite>― 星爷： 新喜剧之王</cite>
