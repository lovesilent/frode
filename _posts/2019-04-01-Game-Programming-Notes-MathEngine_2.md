---
layout: post
title:  "Game Programming Notes -- Math_Engine_2"
date:   2019-04-01
banner_image: math.jpg
tags: [Game Programming]
---
《3D游戏编程大师技巧》 笔记

   Math_Engine—— 数学常量

<!--more-->

## Math_Engine

------

### 数学常量

数学引擎是基于一些常量的。这些常量是在T3DLIB4.H中定义的，但有些来自T3DLIB1.H。下面是在T3DLIB1.H中定义的数学常量：

```c++
// T3DLIB1.H 中定义的数学常量
// 与Pi相关的常量
#define PI ((float)3.141592654f)
#define PI2 ((float)6.283185307f)
#define PI_DIV_2 ((float)1.570796327f)
#define PI_DIV_4 ((float)0.785398163f)
#define PI_INV ((float)0.318309886f)

// 与定点数运算相关的常量
#define FIXP16_SHIFT 16
#define FIXP16_MAX 65536
#define FIXP16_DP_MASK 0x0000ffff
#define FIXP16_WP_MASK 0xffff0000
#define FIXP16_ROUND_UP 0x00008000
```

注意与PI和定点数运算相关的常量。我们将使用定点数格式16.16，后面介绍定点数函数时将更详细地讨论它们。下面是T3DLIB4.H中定义的数学常量，将它们分成了几组以便于解释：

```c++
// 针对于非常小的数的常量
#define EPSILON_E4 (float)(1E-4)
#define EPSILON_E5 (float)(1E-5)
#define EPSILON_E6 (float)(1E-6)
```

Epsilon常量有助于对很小的浮点数进行数学比较。例如，执行浮点数运算时，经常需要检测一个浮点数是否等于0.0。然而，经过几次浮点数运算后，精度将降低，很少会出现0.0的情况，因此一种常用的策略是检测是否接近于0.0，如下所示：

```c++
if(fabs(x)<0.00001)
{
    //足够接近于零
}// end if
```

下面的常量用于参数化直线函数及其返回值（后面介绍这些函数时将详细讨论）：

```c++
//用于参数化直线交点的常量
#define PARM_LINE_NO_INTERSECT 0
#define PARM_LINE_INTERSECT_IN_SEGMENT 1
#define PARM_LINE_INTERSECT_OUT_SEGMENT 2
#define PARM_LINE_INTERSECT_EVERYWHERE 3
```

下面是一些单位矩阵，用于简化矩阵的初始化：

```c++
// 单位矩阵
// 4x4单位矩阵
const MATRIX4X4 IMAT_4X4 = {
    1,0,0,0,
    0,1,0,0,
    0,0,1,0,
    0,0,0,1};
// 4x3单位矩阵（从数学上说，这是不正确的）
// 但在该引擎中将使用这种矩阵，并假设最后一列为[0 0 0 1]
const MATRIX4X3 IMAT_4X3 = {
    1,0,0,
    0,1,0,
    0,0,1,
    0,0,0};
// 3x3单位矩阵
const MATRIX3X3 IMAT_3X3 = {
    1,0,0,
    0,1,0,
    0,0,1};
// 2x2单位矩阵
const MATRIX2X2 IMAT_2X2 = {
    1,0,
    0,1};
```

虽然只有nxn矩阵才有单位矩阵，但在上面的定义中提供了一个4x3的单位矩阵，其实在使用过程中假设最后一列为[0 0 0 1]。



{% include image_full.html imageurl="/images/posts/yahh.jpg" title="yahh" caption="yahh" %}


### Poetry

>你要尽全力保护你的梦想
>
>那些嘲笑你的人
>
>他们必定失败
>
>他们想把你变成和他们一样的人
>
>如果你有梦想的话
>
>就要努力实现
>
><cite>― 当幸福来敲门 </cite>
