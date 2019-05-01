---
layout: post
title:  "Game Programming Notes -- Math_Engine_5"
date:   2019-05-01
banner_image: math.jpg
tags: [Game Programming]
---
《3D游戏编程大师技巧》 笔记

   Math_Engine—— 数学引擎API（1）

<!--more-->

## Math_Engine

------

### 数学引擎API清单（1）

下面介绍整个数学引擎API（它位于T3DLIB4.CPP\|H文件中）。

<u>三角函数</u>

```c++
float Fast_Sin(float theta)
{
    //该函数使用查找表sin_look[]
    //但能够通过插值计算负角度和小数角度的正弦
    //因此与查找表相比，精度更高，但速度可能低些
    
    //将角度转换为0~359的值
    theta=fmodf(theta, 360);
    //将角度转换为正值
    if(theta<0) theta+=360.0;
    
    //提取角度的整数部分和小数部分，以便进行插值计算
    int theta_int = (int)theta;
    float theta_frac = theta - theta_int;
    
    //使用查找表并根据小数部分进行插值来计算正弦
    //如果theta_int为359，则加1后将为360
    //但没有关系，一位内查找表中包含360度的正弦值
    return (sin_look[theta_int]+theta_frac*(sin_look[theta_int+1]-sin_look[theta_int]))
} // end Fast_Sin
```

该函数使用查找表sin_look[]并通过线性插值来计算参数的正弦值。与仅使用查找表相比，它们的精度更高；同时与使用C/C++内部数学库函数sin()和cos()相比，速度更快。参数theta必须以度为单位。

{% include image_caption.html imageurl="/images/posts/Precision_trigonometric.png" title="Improve the precision of the trigonometric lookup table by interpolation" caption="Improve the precision of the trigonometric lookup table by interpolation" %}



<u>坐标系支持函数</u>

这部分API函数处理笛卡尔坐标与极坐标、柱面坐标和球面坐标之间的转换。每个函数都是按照第4章介绍的数学理论来实现的。就不具体介绍了，仔细观察函数原型就能知道该函数的具体功能了。

<u>矩阵支持函数</u>

这套函数用于处理矩阵数学运算和矩阵变换。一般而言，3D引擎根据不同的需要使用3x3或4x4矩阵，但有时候也使用4x3矩阵，并对最后一列做出假设。我们有3D点和4D点数据结构，虽然有时候需要对矩阵数学进行很多优化，但在早期尽量一般化没什么坏处。另外，虽然作者尽可能地考虑了可能需要的各种矩阵乘法和矩阵变换，但肯定会有遗漏——但以后可以添加它们。最后，虽然数学引擎支持2x2、3x3和4x4矩阵，但一些较老的2x2矩阵支持位于T3DLIB１.CPP\|H模块中。

具体的函数在函数原型中都有相应体现。

```c++
void Mat_Init_2X2(MATRIX2X2_PTR ma, float m00, float m01, float m10, float m11);
```

该函数使用指定的浮点数按先行后列的顺序初始化矩阵ma。

```c++
void Mat_Add_2X2(MATRIX2X2_PTR ma, MATRIX2X2_PTR mb, MATRIX2X2_PTR msum);
```

该函数将两个矩阵相加（ma+mb)，并将结果存储到msum中。

```c++
void Mat_Mul_2X2(MATRIX2X2_PTR ma, MATRIX2X2_PTR mb, MATRIX2X2_PTR mprod);
```

该函数将两个矩阵相乘，并将结果存储到mprod中。这里矩阵显示地相乘，而不是用循环操作。这种方法更优化。

```c++
int Mat_Mul_1X2_3X2(MATRIX1X2_PTR ma, MATRIX3X2_PTR mb, MATRIX1X2_PTR mprod)
{
    // 这个函数将一个1x2矩阵与一个3x2矩阵相乘，并存储结果
    // 这里假设1x2矩阵的第三个元素为1，使这种矩阵乘法合法，即变成1x3 X 3x2
    for(int col=0; col<2; col++)
    {
        // 计算函数将一个1x2矩阵与一个3x2矩阵相乘，并存储结果
        // 这里假设1x2矩阵的第三个元素为1，使这种矩阵乘法合法，即变成1x3 X 3x2
        for(int col=0; col<2; col++)
        {
            // 计算ma中的行与mb中的列之间的点积
            float sum=0; //用于存储结果
            for(int index=0; index<2; index++)
            {
                // 累积下一对元素的乘积
                sum+=(ma->M[index]*mb->M[index][col]);
            } // end for index
        }
        // 累积最后一个元素与1的乘积
        sum+=mb->M[index][col];
        
        // 将结果存储到第col个元素中
        mprod->M[col]=sum;
    } // end for col
    return(1);
} // end Mat_Mul_1X2_3X2
```

用途：Mat_Mul_1X2_3X2函数是一个专用函数，将一个1x2矩阵（实际上是2D点）与一个3x2矩阵（表示旋转或平移）相乘。这种运算在数学上是未定义的，因为他们的内维度不相同。然而，如果假设1x2实际是一个1x3矩阵，其最后一个元素为1，将可以执行这种乘法。下图说明了执行该乘法的过程。

{% include image_caption.html imageurl="/images/posts/Uvmm_1232.png" title="Undefined vector-matrix multiplication (1x2)\*(3x2)" caption="Undefined vector-matrix multiplication (1x2)\*(3x2)" %}

![1556678843732](C:\Users\wuyu\Desktop\notes\1556678843732.png)

```c++
void Mat_Mul_VECTOR3D_4X3(VECTOR3D_PTR va, MATRIX4X3_PTR mb, VECTOR3D_PTR vprod);
```

用途：Mat_Mul_VECTOR3D_4x3将一个3D向量与一个4x3矩阵相乘。它假设在向量va中存在第四个元素，且等于1.0，以便于执行乘法运算。由于在4x3矩阵中只有三列，所以结果自然为3D向量。

<u>2D和3D参数化直线支持函数</u>

大多数时候，参数化直线需要在代码中使用一个向量和一个参数t手工编写参数化直线的代码。然而，既然大多数时候都将使用一个向量和一个参数t，为什么不将它们封装到一个结构（或没有方法的类）中，然后以此为基础创建常用函数，如计算交点的函数呢？因此，数学引擎提供了对2D和3D参数化直线的支持以及一整套相关的函数。

为帮助回顾，下面再次给出2D参数化直线的结构：

```c++
typedef struct PARMLINE2D_TYP
{
	POINT2D p0;
    POINT2D p1;
    VECTOR2D v; // 方向向量
} PARMLINE2D, *PARMLINE2D_PTR;
```

下面是3D参数化直线的结构：

```c++
typedef struct PARMLINE3D_TYP
{
    POINT3D p0;
    POINT3D p1;
    VECTOR3D v;
} PARMLINE3D, *PARMLINE3D_PTR;
```

2D版本和3D版本的唯一差别在于z坐标；除此之外，它们以相同的格式存储相同的信息。下面分别介绍这些函数。

```c++
void Init_Parm_Line2D(POINT2D_PTR p_init, POINT2D_PTR p_term, PARMLINE2D_PTR p);
```

用途：该函数根据指定的点计算它们之间的向量，并初始化一条2D参数化直线。注意，该向量是在函数内部生成的。

```c++
void Compute_Parm_LINE2D(PARMLINE2D_PTR p, float t, POINT2D_PTR pt);
```

用途：该函数计算2D参数化直线在参数t处的值，并将返回到指定的点中。参数t=0时，结果为起点p0；参数t=1时，结果为终点p1。也就是说，当t从0变化到1时，将从p0移动到p1。

```c++
int Intersect_Parm_Lines2D(PARMLINE2D_PTR p1, PARMLINE2D_PTR p2, float *t1, float *t2)
{
    //这个函数计算两条参数化线段的交点
    //并将t1和t2分别设置交点在p1和p2
    //然而，t值可能不在范围[0,1]内
    //这意味着线段本身没有相交，但它们对应的直线是相交的
    //函数的返回值为0，表示没有相交，1表示交点在线段上，
    //2表示相交，但交点不一定在线段上，3表示两条线段位于同一条直线上
    
    //我们知道两条线段的参数化方程，需要计算交点（如果有的话）对应的t1和t2
    
    //第1步：检测它们是否平行
    //如果一个方向向量是另一个向量与一个标量的乘积，则说明两条线段平行
    //除非它们重叠，否则不可能相交
    float det_p1p2 = (p1->v.x*p2->v.y - p1->v.y*p2->v.x);
    if(fabs(det_p1p2)<=EPSILON_E5)
    {
        //这表明两条线段要么根本不相交，要么位于同一条直线上
        //在后一种情况下，可能有一个或多个交点
        //现在暂时假设它们不相交，以后需要时再重新编写函数，以考虑重叠的情况
        return(PARM_LINE_NO_INTERSECT);
    } // end if
    
    //第2步：计算t1和t2的值
    //我们有两条以下述方式表示的线段
    //p=p0+v*t
    //p1=p10+v1*t1
    
    //p1.x = p10.x+v1.x*t1
    //p2.y = p10.y+v1.y*t1
    
    //p2=p20+v2*t2
    
    //p2.x = p20.x+v2.x*t2
    //p2.y = p20.y+v2.y*t2
    
    //前面也介绍过如何计算交点
    *t1 = (p2->v.x*(p1->p0.y-p2->p0.y) - p2->v.y*(p1->p0.x - p2->p0.x))/det_p1p2;
    *t2 = (p1->v.x*(p1->p0.y-p2->p0,y) - p1->v.y*(p1->p0.x - p2->p0.x))/det_p1p2;
    
    // 检查交点是否在线段上
    if((*t1>=0)&&(*t1<=1) && (*t2>=0) && (*t2<=1))
        return(PARM_LINE_INTERSECT_IN_SEGMENT);
    else
        return(PARM_LINE_INTERSECT_OUT_SEGMENT);
} // end Intersect_Parm_Line2D
```

用途：Intersect_Parm_Lines2D计算两条参数化直线p1和p2的交点，并将交点对应的参数值t1和t2分别存储到相应的变量中。根据两条直线是否相交以及交点是否在线段上，该函数有下列返回值。

```c++
#define PARM_LINE_NO_INTERSECT 0
#define PARM_LINE_INTERSECT_IN_SEGMENT 1
#define PARM_LINE_INTERSECT_OUT_SEGMENT 2
```

该函数不检测两条直线是否共线，因为这将极大地降低速度，同时有太多的情况（部分重叠、包含、只有一点重叠等）需要检测。因此，调用该函数前需要检查这些条件，或者在该函数中添加这些功能。请记住，这些参数化直线实际上是线段，同时非平行线总会在某一点相交，因此该函数将计算出其交点。



{% include image_full.html imageurl="/images/posts/Avengers_Endgame.jpg" title="Avengers: End Game" caption="Avengers: End Game" %}


### Poetry

>I love you three thousand times.
>
>I am Iron Man
>
><cite>― Avengers: Endgame</cite>
