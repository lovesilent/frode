---
layout: post
title:  "Game Programming Notes -- Math_Engine"
date:   2019-03-30
banner_image: math.jpg
tags: [Game Programming]
---
《3D游戏编程大师技巧》 笔记

   Math_Engine—— 数学引擎概述

<!--more-->

## Math_Engine

------

该数学引擎主要介绍下列内容

1. 数学引擎概述
2. 数学常量
3. 宏和内联函数
4. 函数原型
5. 全局变量
6. 坐标系支持
7. 向量支持
8. 矩阵支持
9. 2D和3D参数化直线支持
10. 3D平面支持
11. 四元数支持
12. 定点数支持
13. 浮点数支持

### 数学引擎概述

在一个数学引擎中实现所需的数学理论。这些3D引擎将以该数学引擎为基础，根据需要添加功能，对其进行修改或改进。它不是最高效的数学库，因为没有在底层做过多的优化。

<u>数学引擎的文件结构</u>

- T3DLIB4.CPP——C++源代码文件
- T3DLIB4.H——头文件

{% include image_caption.html imageurl="/images/posts/rel_math.png" title="The relationship between mathematics engine and game system" caption="The relationship between mathematics engine and game system" %}

该图描述了数学引擎与游戏系统中其他文件的关系。数学库T3DLIB4.CPP依赖于T3DLIB1.CPP中的某些数据结构，因此总是需要链接T3DLIB1.CPP\|H。而T3DLIB1.CPP\|H又依赖于DDRAW.H，因此必须在T3DLIB4.CPP中包含DDRAW.H。这意味着要在其他地方独立使用该数学库模块，需要将T3DLIB1.CPP中的几个函数和T3DLIB1.H中的#defines语句分别复制到T3DLIB4.CPP和T3DLIB4.H中。

数学引擎本身由很多函数组成（对它们测试并不容易），它们用于处理点、向量、直线、矩阵、四元数等。

<u>关于C++的最后说明</u>

使用复杂的C++编写一个库后，最后却发现它处于C+状态（C加上函数重载和一点点C++）。之所以不使用C++，是因为c语言版本与C++版本的速度一样快，甚至更快，但更易于理解。另一方面，可以将数学库或其他内容转换成C++。

### 数据结构和类型

下面介绍的数据引擎使用的数据结构和类型。在很多程度上说，90%的类型和数据结构都是全新的，但也使用了T3DLIB1\|H中的一些类型和数据结构，用于执行简单的矩阵运算。

该数学引擎支持很多数据类型，包括点、向量、矩阵、四元数、参数化直线、3D平面、极坐标、柱面坐标、球面坐标和定点数等。下面依次介绍这些数据结构。

<u>向量和点</u>

数学引擎支持2D、3D、4D点和向量，其中4D表示格式为(x, y, z, w)的齐次坐标。然而，大多数情况下，我们不会使用w(它总是1)。由于点和向量实际上是同一回事（至少，从数据上说是如此），因此使用相同的数据结构来存储它们。

另外，在结构中使用共用体，以支持多种命名规则。例如，有时候使用p.x、p.y、p.z来表示3D点p比较合适，但有时候需要使用以数组方式(p.M[0]、p.M[1]、p.M[2])访问x、y、z的算法。编写所有数据结构时，都遵循了这种方法。

```c++
// 不包含W分量的2D向量和点
typedef struct VECTOR2D_TYP
{
    union
    {
        float M[2]; //数组存储方式
        // 独立变量存储方式
        struct
        {
            float x,y;
        }; // end struct
    }; // end union
} VECTOR2D, POINT2D, *VECTOR2D_PTR, *POINT2D_PTR;

// 不包含W分量的3D向量和点
typedef struct VECTOR3D_TYP
{
    union
    {
        float M[3]; //数组存储方式
        // 独立变量存储方式
        struct
        {
            float x,y,z;
        }; // end struct
    }; //end union
} VECTOR3D, POINT3D, *VECTOR3D_PTR, *POINT3D_PTR;

// 包含W分量的4D齐次向量和点
typedef struct VECTOR4D_TYP
{
    union
    {
        float M[4];
        struct
        {
            float x,y,z,w;
        };
    };
} VECTOR4D, POINT4D, *VECTOR4D_PTR, *POINT4D_PTR;
```

下面是T3DLIB1.H中定义的顶点数据结构

```c++
// 2D顶点-int
typedef struct VERTEX2DI_TYP
{
    int x,y; //顶点坐标
} VERTEX2DI, *VERTEX2DI_PTR;

// 2D顶点-float
typedef struct VERTEX2DF_TYP
{
    float x,y; //顶点坐标
}
```

<u>参数化直线</u>

大多数情况下，使用直线的参数化表示的算法通常根据描述问题的点和变量，动态地创建参数化直线，但其实也需要一套显示参数化直线的函数。因此，这个数学库提供了对2D和3D参数化直线的支持。

```c++
// 2D参数化直线
typedef struct PARMLINE2D_TYP
{
    POINT2D p0; // 参数化直线的起点
    POINT2D p1; // 参数化直线的终点
    VECTOR2D v; // 线段的方向向量 |v|=|p0->p1|
} PARMLINE2D, *PARMLINE2D_PTR;
```

{% include image_caption.html imageurl="/images/posts/2D_line.png" title="2D parametric straight line model" caption="2D parametric straight line model" %}

p0代表起点，p1代表直线的终点，而v为向量p0->p1。3D参数化直线的表示也非常容易。

```c++
// 3D参数化直线
typedef struct PARMLINE3D_TYP
{
    POINT3D p0;
    POINT3D p1;
    VECTOR3D v;
} PARMLINE3D, *PARMLINE3D_PTR;
```

{% include image_caption.html imageurl="/images/posts/3D_line.png" title="3D parametric straight line model" caption="3D parametric straight line model" %}

p0、p1和v的含义与2D参数化直线相同。然而，图中使用的是左手坐标系。不过这并没有关系。直线就是直线，而不管在哪种坐标系中。只有在相同坐标系中定义和使用直线，就不会有问题。

<u>3D平面</u>

虽然在大多数时候，需要考虑3D平面，但通常是在多边形语境下考虑。由于以下几种原因，创建一个3D平面类型是个不错的选择：以便能够执行空间算法，简化裁剪算法的编写，有助于编写3D直线和3D平面间的碰撞检测算法。表示3D平面的方法有很多，但归根结底它们表示的都是相同的东西。我将使用“点-法线”形式（而不是显示形式）来表示3D平面。然而，这并不意味着不能以下述形式存储平面：
$$
a*x+b*y+c*z+d=0
$$
而只是意味着只需要编写一个函数，将它转化为点-法线的形式，以便存储。另外，对于很多计算来说，点-法线形式更适合，不需要进行形式转换。下面是将使用的数据结构：

```c++
// 3D 平面
typedef struct PLANE3D_TYP
{
    POINT3D p0; // 平面上的点
    VECTOR3D n; // 平面的发现（不必是单位向量）
} PLANE3D, *PLANE3D_PTR;
```

{% include image_caption.html imageurl="/images/posts/3D_planar.png" title="3D planar model" caption="3D planar model" %}

p0是平面上的一个点，向量n与平面垂直的。注意，向量n不必是单位向量。

<u>矩阵</u>

在数学引擎中，最大的一组数据结构是矩阵。它支持1x2、2x2、1x3、3x2、3x3、1x4、4x4和4x3矩阵。1xN矩阵实质上是一个包含n个分量的向量。可以通过强制数据类型转换，使得为一种数据类型编写的函数可用于另一种数据类型。注意，很多2x2、3x3矩阵都是在T3DLIB1.H中定义的，所有矩阵都以先行后列的形式表示，即
$$
[0...n][0...n]=[行-索引][列-索引]
$$

```c++
// 矩阵数据结构
// 4x4矩阵
typedef struct MATRIX4X4_TYP
{
    union
    {
        float M[4][4]; //数组存储方式
        // 按先行后列的方式来存储
        struct
        {
            float M00, M01, M02, M03;
            float M10, M11, M12, M13;
            float M20, M21, M22, M23;
            float M30, M31, M32, M33;
        }; // end explicit names
    }; // end union
} MATRIX4X4, *MATRIX4X4_PTR;

// 4x3矩阵
typedef struct MATRIX4X3_TYP
{
    union
    {
        float M[4][3]; // 以数组方式存储
        struct
        {
            float M00, M01, M02;
            float M10, M11, M12;
            float M20, M21, M22;
            float M30, M31, M32;
        }; // end explict names
    }; // end union
} MATRIX4X3, *MATRIX4X3_PTR;

// 其余的矩阵类似于上面的定义
```

<u>四元数</u>

下一种数据类型是四元数。四元数的形式如下：
$$
q=q_0+q_1*i+q_2*j+q_3*k
$$
或
$$
q=q_0+<q_1,q_2,q_3>
$$
或
$$
q=q_0+qv
$$
四元数由4个分量组成，实部为q0，向量部分为虚部。另外，由于我们将在旋转和相机语境下使用四元数，所有用实数q0（或w）和向量部分<x,y,z>来表示四元数有很多优点。这样可以使用其他VECTOR3D函数来对四元数执行操作——可能由于它们的数据结构类似。下面介绍四元数的数据结构：

```c++
// 4d四元数 /////////////////////////////////////
// 通过使用共同体，提供了多种处理四元数分量的方式
typedef struct QUAT_TYP
{
    union
    {
        float M[4]; // 按w、x、y、z的顺序以数组方式存储
        // 以向量部分和实部的格式存储
        struct
        {
            float q0; //实部
            VECTOR3D qv; //虚部（xi+yj+zk）
        };
    };
} QUAT, *QUAT_PTR;
```

该共用体支持以三种不同的方式访问四元数：以数组方式；浮点数和向量；显示名称w、x、y和z，采用那种方式更好取决于四元数进行处理的算法。

<u>角坐标系支持</u>

虽然大多数时候都使用笛卡尔坐标系，但有些时候需要一种更自然地表示问题的方法。例如，构建炮塔模型时，使用极坐标系最合适，应为需要相对于坐标轴的角度方向。因此，数学引擎支持所有2D和3D角坐标系以及这些坐标系之间的变换。支持的坐标系包括2D极坐标系、3D柱面坐标系和3D球面坐标系。所有角度都以弧度为单位，所有转换都是针对右手坐标系的，如果您使用左手坐标系，一定要小心——如果不考虑坐标系的右手性，得到的结果可能是正确结果求负。

{% include image_caption.html imageurl="/images/posts/Tmodel_polar.png" title="Turret model described by polar coordinates" caption="Turret model described by polar coordinates" %}

<u>2D极坐标</u>

2D极坐标系如图5.6所示。坐标系中的点用距离（离原点或极点的距离）和方向（角度）θ表示。因此，点p位于这样的射线上；相对于参考线（通常为x轴）的反时针角度为θ，且离原点的距离为r。该数据类型转换为C++后如下：

```c++
// 2D极坐标///////////////////
typedef struct POLAR2D_TYP
{
    float r; //半径
    float theta; //角度
} POLAR2D, *POLAR2D_PTR;
```

{% include image_caption.html imageurl="/images/posts/2D_polar.png" title="2D polar coordinate system" caption="2D polar coordinate system" %}

<u>3D柱面坐标</u>

3D柱面坐标类似于2D极坐标系，但增加了一个z轴变量。点被定义为p(r,θ,z)，表示点p离x-y平面的距离为z；它在x-y平面上的投影位于这样的射线上：相对于参考线（通常为x轴）的反时针角度为θ，且离原点的距离为r。

```c++
//3D cylindrical coordinates
typedef struct CYLINDRICAL3D_TYP
{
    float r; //半径
    float theta; //与z轴的夹角
    float z; //z坐标
} CYLINDRICAL3D, *CYLINDRICAL3D_PTR;
```

{% include image_caption.html imageurl="/images/posts/3D_cylindrical.png" title="3D cylindrical coordinate system" caption="3D cylindrical coordinate system" %}

<u>3D球面坐标</u>

3D球面坐标系是所有角坐标系中最复杂的。点p(ρ,φ,θ)由一个距离和两个角度定义。其中：

- ρ是点p到原点o的距离
- φ是原点到点p的线段与正z轴（这里使用的是右手坐标系）之间的夹角
- θ是原点o到点p的线段在x-y平面上的投影与正x轴之间的夹角，它正好是标准2D极角θ

{% include image_caption.html imageurl="/images/posts/3D_spherical .png" title="3D spherical coordinate system" caption="3D spherical coordinate system" %}

其数据结构为：

```c++
// 3D球面坐标/////////////////////////////////
typedef struct SPHERICAL3D_TYP
{
    float p; //到原点的距离
    float theta; //线段o->p和正z轴之间的夹角
    float phi; //线段o->p和x-y平面上的投影与正x轴之间的夹角
} SPHERICAL3D, *SPHERICAL3D_PTR;
```

<u>定点数</u>

最后一种类型比较难，但的确需要它。定点数是用于以有限的精度表示浮点数的整数，以避免进行浮点处理。现在对它们的需求已不再像以前那么强烈，因为奔腾和I64处理器可以像定点数一样迅速地执行浮点数运算。然而，有时候仍需要使用定点数，例如无法使用的浮点的内部循环、在您只想使用整数时或小型手持设备上时。

{% include image_caption.html imageurl="/images/posts/Fixed_point .png" title="Fixed point representation" caption="Fixed point representation" %}

下面是定点数的数据类型：

```c++
// 定点数数据类型 ////////////////////////
typedef int FIXP16;
typedef int *FIXP16_PTR;
```



{% include image_full.html imageurl="/images/posts/yahh.jpg" title="yahh" caption="yahh" %}


### Poetry

>人把自己置身于忙碌当中
>
>有一种麻木的踏实
>
>但丧失了真实
>
>你的青春也不过只有这些日子
>
>什么是真实？
>
>你看到什么
>
>听到什么
>
>做什么
>
>和谁在一起
>
>有一种从心灵深处
>
>满溢出来的不懊悔
>
>也不羞耻的平和与喜悦
>
><cite>― 无问西东 </cite>
