---
layout: post
title:  "Game Programming Notes -- Math_Engine_4"
date:   2019-04-10
banner_image: math.jpg
tags: [Game Programming]
---
《3D游戏编程大师技巧》 笔记

   Math_Engine—— 函数原型

<!--more-->

## Math_Engine

------

### 函数原型

介绍所有的函数原型。这里不具体展开它们的功能，只是对其有个大致了解。

```c++
// 通用三角函数
float Fast_Sin(float theta);

float Fast_Cos(float theta);

// 距离函数（来着T3DLIB1.CPP|H)
int Fast_Distance_2D(int x, int y);

float Fast_Distance_3D(float x, float y, float z);

// 极坐标、柱面坐标和球面坐标函数
void POLAR2D_To_POINT2D(POLAR2D_PTR polar, POINT2D_PTR rect);

void POLAR2D_To_RectXY(POLAR2D_PTR polar, float *x, float *y);

void POINT2D_To_POLAR2D(POINT2D_PTR rect, POLAR2D_PTR polar);

void POINT2D_To_PolarRTh(POINT2D_PTR rect, float *r, float *theta);

void CYLINDRICAL3D_To_POINT3D(CYLINDRICAL3D_PTR cy1, POINT3D_PTR rect);

void CYLINDRICAL3D_To_RectXYZ(CYLINDRICAL3D_PTR cy1, float *x, float *y, float *z);

void POINT3D_To_CYLINDRICAL3D(POINT3D_PTR rect, CYLINDRICAL3D_PTR cy1);

void POINT3D_To_CylindricalRThz(POINT3D_PTR rect, float *r, float *theta, float *z);

void SPHERICAL3D_To_POINT3D(SPHERICAL3D_PTR sph, POINT3D_PTR rect);

void SPHERICAL3D_To_RectXYZ(SPHERICAL3D_PTR sph, float *x, float *y, float *z);

void POINT3D_To_SPHERICAL3D(POINT3D_PTR rect, SPHERICAL3D_PTR sph);

void POINT3D_To_SphericalPThPh(POINT3D_PTR rect, float *p, float *theta, float *phi);

// 2D向量函数
void VECTOR2D_Add(VECTOR2D_PTR va, VECTOR2D_PTR vb, VECTOR2D_PTR vsum);

VECTOR2D VECTOR2D_Add(VECTOR2D_PTR va, VECTOR2D_PTR vb);

void VECTOR2D_Sub(VECTOR2D_PTR va, VECTOR2D_PTR vb, VECTOR2D_PTR vdiff);

VECTOR2D VECTOR2D_Sub(VECTOR2D_PTR va, VECTOR2D_PTR vb);

void VECTOR2D_Scale(float k, VECTOR2D_PTR va);

void VECTOR2D_Scale(float k, VECTOR2D_PTR, VECTOR2D_PTR vscaled);

float VECTOR2D_Dot(VECTOR2D_PTR va, VECTOR2D_PTR vb);

float VECTOR2D_Length(VECTOR2D_PTR va);

float VECTOR2D_Length_Fast(VECTOR2D_PTR va);

void VECTOR2D_Normalize(VECTOR2D_PTR va);

void VECTOR2D_Normalize(VECTOR2D_PTR va, VECTOR2D_PTR vn);

void VECTOR2D_Build(VECTOR2D_PTR init, VECTOR_PTR term, VECTOR2D_PTR result);

float VECTOR2D_CosTh(VECTOR2D_PTR va, VECTOR2D_PTR vb);

void VECTOR2D_Print(VECTOR2D_PTR va, char *name);

// 3D向量函数
void VECTOR3D_Add(VECTOR3D_PTR va, VECTOR3D_PTR vb, VECTOR3D_PTR vsum);

VECTOR3D VECTOR3D_Add(VECTOR3D_PTR va, VECTOR3D_PTR vb);

void VECTOR3D_Sub(VECTOR3D_PTR va, VECTOR3D_PTR vb, VECTOR3D_PTR vdiff);

VECTOR3D VECTOR3D_Sub(VECTOR3D_PTR va, VECTOR3D_PTR vb);

void VECTOR3D_Scale(float k, VECTOR3D_PTR va);

void VECTOR3D_Scale(float k, VECTOR3D_PTR va, VECTOR3D_PTR vscaled);

float VECTOR3D_Dot(VECTOR3D_PTR va, VECTOR3D_PTR vb);

void VECTOR3D_Cross(VECTOR3D_PTR va, VECTOR3D_PTR vb, VECTOR3D_PTR vn);

VECTOR3D VECTOR3D_Cross(VECTOR3D_PTR va, VECTOR3D_PTR vb);

float VECTOR3D_Length(VECTOR3D_PTR va);

float VECTOR3D_Length_Fast(VECTOR3D_PTR va);

void VECTOR3D_Normalize(VECTOR3D_PTR va);

void VECTOR3D_Normalize(VECTOR3D_PTR va, VECTOR3D_PTR vn);

void VECTOR3D_Build(VECTOR3D_PTR init, VECTOR3D_PTR term, VECTOR3D_PTR result);

float VECTOR3D_CosTh(VECTOR3D_PTR va, VECTOR3D_PTR vb);

void VECTOR3D_Print(VECTOR3D_PTR va, char *name);

// 4D向量函数
void VECTOR4D_Add(VECTOR4D_PTR va, VECTOR4D_PTR vb, VECTOR4D_PTR vsum);

VECTOR4D VECTOR4D_Add(VECTOR4D_PTR va, VECTOR4D_PTR vb);

void VECTOR4D_Sub(VECTOR4D_PTR va, VECTOR4D_PTR vb, VECTOR4D_PTR vdiff);

VECTOR4D VECTOR4D_Sub(VECTOR4D_PTR va, VECTOR4D_PTR vb);

void VECTOR4D_Scale(float k, VECTOR4D_PTR va);

void VECTOR4D_Scale(float k, VECTOR4D_PTR va, VECTOR4D_PTR vscaled);

float VECTOR4D_Dot(VECTOR4D_PTR va, VECTOR4D_PTR vb);

void VECTOR4D_Cross(VECTOR4D_PTR va, VECTOR4D_PTR vb, VECTOR4D_PTR vn);

VECTOR4D VECTOR4D_Cross(VECTOR4D_PTR va, VECTOR4D_PTR vb);

float VECTOR4D_Length(VECTOR4D_PTR va);

float VECTOR4D_Length_Fast(VECTOR4D_PTR va);

void VECTOR4D_Normalize(VECTOR4D_PTR va);

void VECTOR4D_Normalize(VECTOR4D_PTR va, VECTOR4D_PTR vn);

void VECTOR4D_Build(VECTOR4D_PTR init, VECTOR4D_PTR term, VECTOR4D_PTR result);

float VECTOR4D_CosTh(VECTOR4D_PTR va, VECTOR4D_PTR vb);

void VECTOR4D_Print(VECTOR4D_PTR va, char *name);

// 2x2矩阵函数
void Mat_Init_2x2(MATRIX2X2_PTR ma,
                 float m00, float m01, float m10, float m11);

void Print_Mat_2x2(MATRIX2X2_PTR ma, char *name);

float Mat_Det_2x2(MATRIX2X2_PTR m);

void Mat_Add_2x2(MATRIX2X2_PTR ma, MATRIX2X2_PTR mb, MATRIX2X2 msum);

void Mat_Mul_2x2(MATRIX2X2_PTR ma, MATRIX2X2_PTR mb, MATRIX2X2_PTR mprod);

int Mat_Inverse_2x2(MATRIX2X2_PTR m, MATRIX2X2_PTR mi);

int Solve_2x2_System(MATRIX2X2_PTR A, MATRIX1X2_PTR X, MATRIX1X2_PTR B);

// 3x3矩阵函数
// 来自T3DLIB1.CPP|H
int Mat_Mul_1x3_3x2(MATRIX1X3_PTR ma,
                   MATRIX3X2_PTR mb,
                   MATRIX1X2_PTR mprod);

// 来自T3DLIB1.CPP|H
int Mat_Mul_1x3_3x3(MATRIX1X3_PTR ma,
                   MATRIX3X3_PTR mb,
                   MATRIX3X3_PTR mprod);
// 来自T3DLIB1.CPP|H
int Mat_Mul_3x3(MATRIX3X3_PTR ma,
                MATRIX3X3_PTR mb,
                MATRIX3X3_PTR mprod);

// 来自T3DLIB1.CPP|H
inline int Mat_Init_3x2(MATRIX3X2_PTR ma,
                       float m00, float m01,
                       float m10, float m11,
                       float m20, float m21);

void Mat_Add_3x3(MATRIX3X3_PTR ma,
                 MATRIX3X3_PTR mb,
                 MATRIX3X3_PTR msum);

void Mat_Mul_VECTOR3D_3X3(VECTOR3D_PTR m,
                         MATRIX3X3_PTR mb,
                         VECTOR3D_PTR vprod);

int Mat_Inverse_3X3(MATRIX3X3_PTR m,
                   MATRIX3X3_PTR mi);

void Mat_Init_3X3(MATRIX3X3_PTR ma,
                 float m00, float m01, float m02,
                 float m10, float m11, float m12,
                 float m20, float m21, float m22);

void Print_Mat_3x3(MATRIX3X3_PTR ma, char *name);

float Mat_Det_3x3(MATRIX3X3_PTR m);

int Solve_3x3_System(MATRIX3X3_PTR A, MATRIX1X3_PTR X,  MATRIX1X3_PTR B);

// 4x4矩阵函数
void Mat_Add_4x4(MATRIX4X4_PTR ma,
                MATRIX4X4_PTR mb,
                MATRIX4X4_PTR msum);

void Mat_Mul_4x4(MATRIX4X4_PTR ma,
                MATRIX4X4_PTR mb,
                MATRIX4X4_PTR mprod);

void Mat_Mul_1x4_4x4(MATRIX1X4_PTR ma,
                 MATRIX4X4_PTR mb,
                 MATRIX1X4_PTR mprod);

void Mat_Mul_VECTOR3D_4x4(VECTOR3D_PTR va,
                         MATRIX4X4 mb,
                         VECTOR3D_PTR vprod);

void Mat_Mul_VECTOR3D_4x3(VECTOR3D_PTR va,
                         MATRIX4X3_PTR mb,
                         VECTOR3D_PTR vprod);

void Mat_Mul_VECTOR4D_4x4(VECTOR4D_PTR va,
                         MATRIX4X4_PTR mb,
                         VECTOR4D_PTR vprod);

void Mat_Mul_VECTOR4D_4x3(VECTOR4D_PTR va,
                         MATRIX4X3_PTR mb,
                         VECTOR4D_P vprod);

int Mat_Inverse_4x4(MATRIX4X4_PTR m, MATRIX4X4_PTR mi);

void Mat_Init_4x4(MATRIX4X4_PTR ma,
                 float m00, float m01, float m02, float m03,
                 float m10, float m11, float m12, float m13,
                 float m20, float m21, float m22, float m23,
                 float m30, float m31, float m32, float m33);

void Print_Mat_4x4(MATRIX4X4_PTR ma, char *name);

// 4元数函数
void QUAT_Add(QUAT_PTR q1, QUAT_PTR q2, QUAT_PTR qsum);

void QUAT_Sub(QUAT_PTR q1, QUAT_PTR q2, QUAT_PTR qdiff);

void QUAT_Conjugate(QUAT_PTR q, QUAT_PTR qconj);

void QUAT_Scale(QUAT_PTR q, float scale, QUAT_PTR qs);

void QUAT_Scale(QUAT_PTR q, float scale);

float QUAT_Norm(QUAT_PTR q);

float QUAT_Norm2(QUAT_PTR q);

void QUAT_Normalize(QUAT_PTR q, QUAT_PTR qn);

void QUAT_Normalize(QUAT_PTR q);

void QUAT_Unit_Inverse(QUAT_PTR q, QUAT_PTR qi);

void QUAT_Unit_Inverse(QUAT_PTR q);

void QUAT_Mul(QUAT_PTR q1, QUAT_PTR q2, QUAT_PTR qprod);

void QUAT_Triple_Product(QUAT_PTR q1, QUAT_PTR q2, QUAT_PTR q, QUAT_PTR qprod);

void VECTOR3D_Theta_To_QUAT(QUAT_PTR q, VECTOR3D_PTR v, float theta);

void VECTOR4D_Theta_To_QUAT(QUAT_PTR q, VECTOR4D_PTR v, float theta);

void EulerZYX_To_QUAT(QUAT_PTR q, float theta_z, float theta_y, theta_x);

void QUAT_To_VECTOR3D_Theta(QUAT_PTR q, VECTOR3D_PTR v, float *theta);

void QUAT_Print(QUAT_PTR q, char *name);

// 2D参数化直线
void Init_Parm_Line2D(POINT2D_PTR p_init, POINT2D_PTR p_term, PARMLINE2D_PTR p);

void Compute_Parm_Line2D(PARMLINE2D_PTR p, float t, POINT2D_PTR pt);

int Intersect_Parm_Lines2D(PARMLINE2D_PTR p1, PARMLINE2D_PTR p2, float *t1, float *t2);

int Intersect_Parm_Lines2D(PARMLINE2D_PTR p1, PARMLINE2D_PTR p2, POINT2D_PTR pt);

// 3D参数化直线
void PLANE3D_Init(PLANE3D_PTR plane, POINT3D_PTR p0, VECTOR3D_PTR normal, int normalize);

float Compute_Point_In_Plane3D(POINT3D_PTR pt, PLANE3D_PTR plane);

int Intersect_Parm_Line3D_Plane3D(PARMLINE3D_PTR pline, PLANE3D_PTR plane, float *t, POINT3D_PTR pt);

// 定点数函数
FIXP16 FIXP16_MUL(FIXP16 fp1, FIXP16 fp2);

FIXP16 FIXP16_DIV(FIXP16 fp1, FIXP16 fp2);

void FIXP16_Print(FIXP16 fp);
```

### 全局变量

数学引擎中的全局变量不多，实际上仅有的几个全局变量都来自T3DLIB.CPP\|H中最基本的数学支持。这些全局变量是正弦和余弦查找表，如下表所示：

```c++
// 用于存储查找表，来自T3DLIB1.CPP|H
extern float cos_look[361];  //以便能够存储0-360度的值
extern float sin_look[361];
```

注意，这些查找表包含361个元素，分别代表[0...360]度。虽然360度与0度是一回事，但添加这个元素可简化某些算法的编写工作，且节省了一次比较操作。初始化这些表，只需在应用程序开头调用函数Build_Sin_Cos_Tables。



{% include image_full.html imageurl="/images/posts/yahh.jpg" title="yahh" caption="yahh" %}


### Poetry

>It wasn't what I saw that stopped me, Max.
>
>It was what I didn't see.
>
>You understand that?
>
>What I didn't see.
>
>In all that sprawling city there was everything except an end.
>
><cite>― 海上钢琴师</cite>
