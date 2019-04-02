---
layout: post
title:  "Game Programming Notes -- Math_Engine_3"
date:   2019-04-02
banner_image: math.jpg
tags: [Game Programming]
---
《3D游戏编程大师技巧》 笔记

   Math_Engine—— 宏和内联函数

<!--more-->

## Math_Engine

------

### 宏和内联函数

编写数学引擎时需要考虑的一个问题是，通过函数调用对数学对象执行操作时，函数调用花费的时间可能与实际数学运算花费时间相当。因此，绝对有必要使用宏和内联函数。然而，使用大型内联函数的问题是，内联函数必须在编译时（而不是链接时）可用。

当编译一个程序并在另一个模块中调用一个名为func()的函数时，编译器不需要函数func()的代码而只需要其原型：

```c++
int func(int x, int y);
```

有了函数原型，编译器便可以在编译代码中创建函数调用，然后链接器将可以找到函数func()，将调用解释为地址。

内联函数的问题在于，其实际代码必须在编译时可用。因此，实现大型内联函数的唯一方法是，使用一个头文件和一个名为*.inc的伪C/C++模块，并在前者中定义内联函数，手工将后者包含在要包含的模块中。这样，编译器就可以访问内联函数的实际代码。

{% include image_caption.html imageurl="/images/posts/Inline_process .png" title="Inline process related to compilers and linkers" caption="Inline process related to compilers and linkers" %}

编写数学引擎时，所有函数都应该是内联的；对于只有几行代码的函数，运行代码花费的时间往往与设置堆栈结构、执行调用并返回结果花费的时间差不多。

当函数体很小时，应将函数声明为内联的，但实现起来很麻烦，因为没有管理这些文件的好方法。新增一个.inc文件会增加很多麻烦。因此，可以尽可能地使用#define宏。

另外，如果内联函数不大，将它们放到头文件中也是可以的，因为大多数程序员不会到.H文件中查找源代码，而只到.CPP文件中查找源代码。另外，.H文件中包含较多的代码时，将导致头文件的编译时间加长（当然，使用预编译头文件可以解决这种问题）。当编写好引擎后，可能想将很多代码较少的函数转换为内联函数。这样做的问题在于，.CPP文件中的所有代码都将被移到.H文件中，这可能导致源代码的难以管理。

<u>通用宏</u>

```c++
// 计算两个表达式中较大和较小的一个
#define MIN(a,b) (((a) < (b)) ? (a) : (b))
#define MAX(a,b) (((a) > (b)) ? (a) : (b))

// 交换变量的值
#define SWAP(a,b,t) {t=a; a=b; b=t;}

// 数学宏
#define DEG_TO_RAD(ang) ((ang)*PI/180.0)
#define RAD_TO_DEG(rads) ((rads)*180.0/PI)

#define RAND_RANGE(x,y) ((x) + (rand() % ((y) - (x) + 1)))
```

<u>点和向量宏</u>

在2D、3D和4D空间中，点和向量的数据格式相同。因此，在这里将所有与点和向量相关的宏分成一组。

另外，有些宏是以内联函数的方式编写的，以帮助类型检查。还有很多函数都太长，无法编写成#define宏。

```c++
// 向量宏，4D向量的w被设置为1
// 向量归零宏
inline void VECTOR2D_ZERO(VECTOR2D_PTR v) {(v)->x = (v)->y = 0.0;}

inline void VECTOR3D_ZERO(VECTOR3D_PTR v) {(v)->x = (v)->y = (v)->z = 0.0;}

inline void VECTOR4D_ZERO(VECTOR4D_PTR v) {(v)->x = (v)->y - (v)->z = 0.0; (v)->w = 1.0;}

// 使用分量初始化向量的宏
inline void VECTOR2D_INITXY(VECTOR2D_PTR v, float x, float y) {(v)->x = (x); (v)->y = (y);}

inline void VECTOR3D_INITXYZ(VECTOR3D_PTR v, float x, float y, float z) {(v)->x = (x); (v)->y = (y); (v)->z = (z);}

inline void VECTOR4D_INITXYZ(VECTOR4D_PTR v, float x, float y, float z) {(v)->x = (x); (v)->y = (y); (v)->z = (z); (v)->w = 1.0;}

// 使用另一个向量来初始化向量的宏
inline void VECTOR2D_INIT(VECTOR2D_PTR vdst, VECTOR2D_PTR vsrc) {(vdst)->x = (vsrc)->x; (vdst)->y = (vsrc)->y;}

inline void VECTOR3D_INIT(VECTOR3D_PTR vdst, VECTOR3D_PTR vsrc) {(vdst)->x = (vsrc)->x; (vdst)->y = (vsrc)->y; (vdst)->z = (vsrc)->z;}

inline void VECTOR4D_INIT(VECTOR4D_PTR vdst, VECTOR4D_PTR vsrc) {(vdst)->x = (vsrc)->x; (vdst)->y = (vsrc)->y; (vdst)->z = (vsrc)->z; (vdst)->w = (vsrc)->w;}

// 复制向量的宏
inline void VECTOR2D_COPY(VECTOR2D_PTR vdst, VECTOR2D_PTR vsrc) {(vdst)->x = (vsrc)->x; (vdst)->y = (vsrc)->y;}

inline void VECTOR3D_COPY(VECTOR3D_PTR vdst, VECTOR3D_PTR vsrc) {(vdst)->x = (vsrc)->x; (vdst)->y = (vsrc)->y; (vdst)->z = (vsrc)->z;}

inline void VECTOR4D_COPY(VECTOR4D_PTR vdst, VECTOR4D_PTR vsrc) {(vdst)->x = (vsrc)->x; (vdst)->y = (vsrc)->y; (vdst)->z = (vsrc)->z; (vdst)->w = (vsrc)->w;}

// 初始化点的宏
inline void POINT2D_INIT(POINT2D_PTR vdst, POINT2D_PTR vsrc) {(vdst)->x = (vsrc)->x; (vdst)->y = (vsrc)->y;}

inline void POINT3D_INIT(POINT3D_PTR vdst, POINT3D_PTR vsrc) {(vdst)->x = (vsrc)->x; (vdst)->y = (vsrc)->y; (vdst)->z = (vsrc)->z;}

inline void POINT4D_INIT(POINT4D_PTR vdst, POINT4D_PTR vsrc) {(vdst)->x = (vsrc)->x; (vdst)->y = (vsrc)->y; (vdst)->z = (vsrc)->z; (vdst)->w = (vsrc)->w;}

// 复制点的宏
inline void POINT2D_COPY(POINT2D_PTR vdst, POINT2D_PTR vsrc) {(vdst)->x = (vsrc)->x; (vdst)->y = (vsrc)->y;}

inline void POINT3D_COPY(POINT3D_PTR vdst, POINT3D_PTR vsrc) {(vdst)->x = (vsrc)->x; (vdst)->y = (vsrc)->y; (vdst)->z = (vsrc)->z;}

inline void POINT4D_COPY(POINT4D_PTR vdst, POINT4D_PTR vsrc) {(vdst)->x = (vsrc)->x; (vdst)->y = (vsrc)->y; (vdst)->z = (vsrc)->z; (vdst)->w = (vsrc)->w;}
```

几乎完成了任何操作的宏：归零、使用各种数据类型初始化以及复制。INIT和COPY有许多其实是重复的。

<u>矩阵宏</u>

下面是矩阵宏。有些是#define宏，而另外一些是较短的内联函数。

```c++
// 矩阵宏
// 清空矩阵的宏
#define MAT_ZERO_2X2(m) {memset((void *)(m), 0, sizeof(MATRIX2X2));}

#define MAT_ZERO_3X3(m) {memset((void *)(m), 0, sizeof(MATRIX3X3));}

#define MAT_ZERO_4X4(m) {memset((void *)(m), 0, sizeof(MATRIX4X4));}

#define MAT_ZERO_4X3(m) {memset((void *)(m), 0, sizeof(MATRIX4X3));}
```

注意很多编译器在调用函数memset()时使用逐字节填充。可以通过对矩阵使用4字节清零来获得更快的速度。另外，一般来说，整数与相应浮点数的表示并不相同；例如，（float）5.0完全不同于（int）5.然而，32位浮点数0.0与32位整数0的表示是相同的，因此可以利用这种巧合，使用内存函数快速地清除浮点数组。

```c++
// 设置单位矩阵的宏
#define MAT_IDENTITY_2X2(m) {memcpy((void *)(m), (void *)&IMAT_2X2, sizeof(MATRIX2X2));}

#define MAT_IDENTITY_3X3(m) {memcpy((void *)(m), (void *)&IMAT_3X3, sizeof(MATRIX3X3));}

#define MAT_IDENTITY_4X4(m) {memcpy((void *)(m), (void *)&IMAT_4X4, sizeof(MATRIX4X4));}

#define MAT_IDENTITY_4X3(m) {memcpy((void *)(m), (void *)&IMAT_4X3, sizeof(MATRIX4X3));}

// 复制矩阵的宏
#define MAT_COPY_2X2(src_mat, dest_mat) {memcpy((void *)(dest_mat), (void *)(src_mat), sizeof(MATRIX2X2));}

#define MAT_COPY_3X3(src_mat, dest_mat) {memcpy((void *)(dest_mat), (void *)(src_mat), sizeof(MATRIX3X3));}

#define MAT_COPY_4X4(src_mat, dest_mat) {memcpy((void *)(dest_mat), (void *)(src_mat), sizeof(MATRIX4X4));}

#define MAT_COPY_4X3(src_mat, dest_mat) {memcpy((void *)(dest_mat), (void *)(src_mat), sizeof(MATRIX4X3));}

// 对矩阵进行转置的宏
inline void MAT_TRANSPOSE_3X3(MATRIX3X3_PTR m)
{
    MATRIX3X3 mt;
    mt.M00 = m->M00; mt.M01 = m->M10; mt.M02 = m->M20;
    mt.M10 = m->M01; mt.M11 = m->M11; mt.M12 = m->M21;
    mt.M20 = m->M02; mt.M21 = m->M12; mt.M22 = m->M22;
    memcpy((void *)m, (void *)&mt, sizeof(MATRIX3X3));
}

inline void MAT_TRANSPOSE_4X4(MATRIX4X4_PTR m)
{
    MATRIX4X4 mt;
    mt.M00 = m->M00; mt.M01 = m->M10; mt.M02 = m->M20; mt.M03 = m->M30;
    mt.M10 = m->M01; mt.M11 = m->M11; mt.M12 = m->M21; mt.M13 = m->M31;
    mt.M20 = m->M02; mt.M21 = m->M12; mt.M22 = m->M22; mt.M23 = m->M32;
    mt.M30 = m->M03; mt.M31 = m->M13; mt.M32 = m->M23; mt.M33 = m->M33;
    memcpy((void *)m, (void *)&mt, sizeof(MATRIX4X4));
}

inline void MAT_TRANSPOSE_3X3(MATRIX3X3_PTR m, MATRIX3X3_PTR mt)
{
    mt.M00 = m->M00; mt.M01 = m->M10; mt.M02 = m->M20;
    mt.M10 = m->M01; mt.M11 = m->M11; mt.M12 = m->M21;
    mt.M20 = m->M02; mt.M21 = m->M12; mt.M22 = m->M22;
}

inline void MAT_TRANSPOSE_4X4(MATRIX4X4_PTR m, MATRIX4X4_PTR mt)
{
    mt.M00 = m->M00; mt.M01 = m->M10; mt.M02 = m->M20; mt.M03 = m->M30;
    mt.M10 = m->M01; mt.M11 = m->M11; mt.M12 = m->M21; mt.M13 = m->M31;
    mt.M20 = m->M02; mt.M21 = m->M12; mt.M22 = m->M22; mt.M23 = m->M32;
    mt.M30 = m->M03; mt.M31 = m->M13; mt.M32 = m->M23; mt.M33 = m->M33;
}

// 小型内联函数，可以将其转换为宏
// 矩阵和向量列互换宏
inline void MAT_COLUMN_SWAP_2X2(MATRIX2X2_PTR m, int c, MATRIX1X2_PTR v) {m->M[0][c]=v->M[0]; m->M[1][c]=v->M[1];}

inline void MAT_COLUMN_SWAP_3X3(MATRIX3X3_PTR m, int c, MATRIX1X3_PTR v) {m->M[0][c]=v->M[0]; m->M[1][c]=v->M[1]; m->M[2][c]=v->M[2];}

inline void MAT_COLUMN_SWAP_4X4(MATRIX4X4_PTR m, int c, MATRIX1X4_PTR v) {m->M[0][c]=v->M[0]; m->M[1][c]=v->M[1]; m->M[2][c]=v->M[2]; m->M[3][c]=v->M[3];}

inline void MAT_COLUMN_SWAP_4X3(MATRIX4X3_PTR m, int c, MATRIX1X4_PTR v) {m->M[0][c]=v->M[0]; m->M[1][c]=v->M[1]; m->M[2][c]=v->M[2]; m->M[3][c]=v->M[3];}
```

注意所有矩阵函数都使用指针。在数学库的大多数函数中都采用这一方案。有些使用使用真正的对象是适当的，但是很多时候执行栈操作和复制会消耗太多的系统资源。

<u>四元数</u>

接下来介绍的是关于四元数的宏。所有四元数函数（包括T3DLIB4.CPP中的四元数函数）都应该是内联宏；它们将使得代码更加复杂，但是需要记住。下面是关于四元数的宏：

```c++
// 四元数宏
inline void QUAT_ZERO(QUAT_PTR q)
{
    (q)->x = (q)->y = (q)->z = (q)->w = 0.0;
}

inline void QUAT_INITWXYZ(QUAT_PTR q, float w, float x, float y, float z)
{
    (q)->w = (w); (q)->x = (x); (q)->y = (y); (q)->z = (z);
}

inline void QUAT_INIT_VECTOR3D(QUAT_PTR q, VECTOR3D_PTR v)
{
    (q)->w = 0; (q)->x = (v->x); (q)->y = (v->y); (q)->z = (v->z);
}

inline void QUAT_INIT(QUAT_PTR qdst, QUAT_PTR qsrc)
{
    (qdst)->w = (qsrc)->w; (qdst)->x = (qsrc)->x; (qdst)->y = (qsrc)->y; (qdst)->z = (qsrc)->z;
}

inline void QUAT_COPY(QUAT_PTR qdst, QUAT_PTR qsrc)
{
    (qdst)->x = (qsrc)->x; (qdst)->y = (qsrc)->y; (qdst)->z = (qsrc)->z; (qdst)->w = (qsrc)->w;
}
```

它们大多都是根据所需的格式初始化四元数的宏。

<u>定点数宏</u>

最后介绍定点数宏，用于对定点数进行转换和提取。其他定点数函数都将编写为真正的函数。它们本来也应该编写为内联函数，但乘法和除法函数需要很多内联汇编语言，不想将它们都放到头文件中。

```c++
// 从16.16格式的定点数中提取整数部分和小数部分
#define FIXP16_WP(fp) ((fp) >> FIXP16_SHIFT)
#define FIXP16_DP(fp) ((fp) && FIXP16_DP_MASK)

// 将整数和浮点数转换为16.16格式的定点数
#define INT_T0_FIXP16(i) ((i) << FIXP16_SHIFT)
#define FLOAT_T0_FIXP16(f) (((float)(f) * (float)FIXP16_MAG+0.5))

// 将定点数转换为浮点数
#define FIXP16_T0_FLOAT(fp) (((float)fp)/FIX16_MAG)
```



{% include image_full.html imageurl="/images/posts/yahh.jpg" title="yahh" caption="yahh" %}


### Poetry

>Some of us get dipped in flat,
>
>some in satin,
>
>some in gloss.
>
>But every once in a while you find someone who's iridescent,
>
>and when you do,
>
>nothing will ever compare.
>
><cite>― 怦然心动 </cite>
