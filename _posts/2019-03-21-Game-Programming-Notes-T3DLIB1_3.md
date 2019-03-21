---
layout: post
title:  "Game Programming Notes -- T3DLIB1_3"
date:   2019-03-21
banner_image: solo.jpg
tags: [Game Programming]
---
《3D游戏编程大师技巧》 笔记

T3DLIB1之2D多边形函数

<!--more-->

## T3DLIB１

------

### 2D多边形函数

*Draw_Triangle_2D*

```c++
void Draw_Triangle_2D(int x1, int y1, //三角形顶点
	int x2, int y2,
	int x3, int y3,
	int color, //8位颜色索引
	UCHAR *dest_buffer, //目标缓存
	int mempitch); //内存跨距

//定点高速版本，精度稍微低些
void Draw_TriangleFP_2D(int x1, int y1,
                       int x2, int y2,
                       int x3, int y3,
                       int color,
                       UCHAR *dest_buffer,
                       int mempitch);

//16位版本
void Draw_Triangle_2D16(int x1, int y1,
                       int x2, int y2,
                       int x3, int y3,
                       int color, //16位RGB颜色
                       UCHAR *dest_buffer,//目标缓存
                       int mempitch); //内存跨距
```

使用指定颜色在指定内存缓存中绘制一个填充三角形。这个三角形将根据全局变量指定的当前裁剪区域被裁剪，而不是使用DirectDraw裁剪区域进行裁剪，因为该函数使用软件（而不是使用硬件）来绘制直线。告诉版本在内部使用定点数学运算，速度快些，但精度差些。这些函数都没有返回值。

*Draw_QuadFP_2D*

```c++
inline void Draw_QuadFP_2D(int x0, int y0,
                          int x1, int y1,
                          int x2, int y2,
                          int x3, int y3,
                          int color, //8位颜色索引
                          UCHAR *dest_buffer, //目标视频缓存
                          int mempitch); //缓存的内存跨距
```

以两个三角形的方式绘制指定的四边形，只适用于8位颜色模式，没有16位颜色版本。该函数没有返回值。

*Draw_Filled_Polygon2D*

```c++
void Draw_Filled_polygon2D(
	POLYGON2D_PTR poly, //要渲染的多边形
	UCHAR *vbuffer, //视频缓存
	int mempitch);  //内存跨距

//16位版本
void Draw_Filled_Polygon2D16(
	POLYGON2D_PTR poly, //要渲染的多边形
	UCHAR *vbuffer, //视频缓存
	int mempitch); //内存跨距
```

在8位颜色模式或者16位颜色模式下，绘制一个n边填充多边形。该函数接受要渲染的多边形、指向视频缓存指针和内存跨距作位其参数。注意该函数相对于多边形的(x0, y0)进行渲染，因此一定要先对它们进行初始化。该函数没有返回值。

*Translate_Polygon2D*

```c++
int Translate_Polygon2D(
	POLYGON2D_PTR poly, //要平移的多边形
	int dx, int dy); //平移因子
```

平移指定多边形的原点(x0, y0)。注意该函数不会平移或修改多边形的顶点。如果成功执行，将返回TRUE。

*Rotate_Polygon2D*

```c++
int Rotate_Polygon2D(
	POLYGON2D_PTR poly, //poly to rotate
	int theta); //angle 0-359
```

以顺时针方向绕其原点旋转给定多边形。其中旋转角度必须位一个位于0~365之间的整数。如果成功执行，将返回TRUE。

*Scale_Polygon2D*

```c++
int Scale_Polygon2D(POLYGON2D_PTR poly, // poly to scale
                   float sx, float sy); // scale factors
```

分别在x轴和y轴方向，按此比例系统sx和yx缩放给定多边形。该函数没有返回值。

**2D图元处理函数**

*Draw_Clip_Line*

```c++
Draw_Clip_Line(int x0, int y0, //起点
              int x1, int y1, //终点
              int color, //8位
              UCHAR *dest_buffer, //视频缓存
              int lpitch); //内存跨距

//16位版本
int Draw_Clip_Line(int x0, int y0, //起点
                  int x1, int y1,
                  int color, //16位RGB颜色
                  UCHAR *dest_buffer, //视频缓存
                  int lpitch); //内存跨距
```

根据当前裁剪矩形对指定直线进行裁剪，然后在指定缓存中绘制裁剪后的直线（8位或者16位）。如果执行成功，返回TRUE 

*Clip_Line*

```c++
int Clip_Line(int &x1, int &y1, //起点
			int &x2, int &y2); // 终点
```

该函数大多数时候供内部调用，但您也可以通过调用它来根据当前裁剪矩形对指定的直线进行剪裁。该函数将修改指定的端点参数，如果您不想要这种效果，应保存这些参数。另外，该函数不绘制任何内容，它只以数学方式对端点进行裁剪，因此位深无关紧要。如果成功执行，则返回TRUE。

*Draw_Line*

```c++
int Draw_Line(int x0, int y0,
			int x1, int y1,
			UCHAR *vb_start,
			int lpitch);

// 16位版本
int Draw_Line16(int x0, int y0,
               int x1, int y1,
               int color,
               UCHAR *vb_start,
               int lpitch);
```

DrawLine函数绘制一条直线，不做任何裁剪，因此需要确保端点位于显示表面的有效坐标范围内。该函数执行裁剪操作的版本快些，因为它不执行裁剪操作。如果成功执行，则返回TRUE。

*Draw_Pixel*

```c++
inline int Draw_Pixel(int x, int y,
                     int color,
                     UCHAR *video_buffer,
                     int lpitch);

//16位版本
inline int Draw_Pixel16(int x, int y,
                       int color,
                       UCHAR *video_buffer,
                       int lpitch);
```

在指定显示表面的内存缓存中绘制一个像素。在大多数情况下，您不会基于像素来创建物体，因为多次第调用该函数将花费大量的时间。如果不考虑速度，该函数也可以完成相应的工作——至少它是内联的。如果执行成功，则返回TRUE。

*Draw_Rectangle*

```c++
int Draw_Rectangle(int x1, int y1,
                  int x2, int y2,
                  int color,
                  LPDIRECTDRAWSURFACE lpdds); //dd表面
```

在指定DirectDraw表面上绘制一个矩形。该表面必须未被锁定，调用该函数才能正常工作。该函数使用硬件加速，因此速度非常快，它可以在8位或16位颜色模式下工作。如果成功执行，则返回TRUE。

*HLine*

```c++
void HLine(int x1, int x2,
          int y,
          int color,
          UCHAR *vbuffer,
          int lpitch);

void HLine16(int x1, int x2,
            int y,
            int color,
            UCHAR *vbuffer,
            int lpitch);
```

以非常快的速度绘制一条水平直线。该函数没有返回值。

*VLine*

```c++
void VLine(int y1, int y2,
          int x,
          int color,
          UCHAR *vbuffer,
          int lpitch);

void VLine16(int y1, int y2,
            int x,
            int color,
            UCHAR *vbuffer,
            int lpitch);
```

以非常快的速度绘制一条垂直直线。它没有HLine函数那么快，但比Draw_Line()函数快，如果已知一条直线所有情况下都是垂直的，可使用该函数。该函数没有返回值。

*Screen_Transitions*

```c++
void Screen_Transitions(int effect,
                       UCHAR *vbuffer,
                       int lpitch);

//屏幕渐变命令
#define SCREEN_DARKNESS 0 //渐变位黑色
#define SCREEN_WHITENESS 1 //渐变位白色
#define SCREEN_SWIPE_X 2 //水平扫描
#define SCREEN_SWIPE_Y 3 //垂直扫描
#define SCREEN_DISOLVE 4 //像素淡出
#define SCREEN_SCRUNCH 5 //方块压缩
#define SCREEN_BLUENESS 6 //渐变位蓝色
#define SCREEN_REDNESS 7 //渐变为红色
#define SCREEN_GREENNESS 8 //渐变为绿色
```

函数执行上述头信息中列出的各种内存中屏幕渐变。这些变换时破坏性的，如果变换后需要它们，应保存图像和调色板。该函数只能在8位颜色模式下运行。该函数没有返回值。

*Draw_Text_GDI*

```c++
int Draw_Text_GDI(char **text, //以NULL结尾的字符串
                 int x, int y, //位置
                 COLORREF color, //RGB颜色
                 LPDIRECTDRAWSURFACE lpdds);

int Draw_Text_GDI(char **text,
                 int x, int y,
                 int color, // 8位颜色索引
                 LPDIRECTDRAWSURFACE lpdds);
```

将在8位或16位颜色模式下，使用指定颜色在指定表面的指定位置绘制GDI文本。该函数被重载，因此可以接受RGB()宏形式的COLORREF参数或8位颜色索引。注意，仅当目标表面未被锁定时该函数才管用，因为它将暂停锁定该表面，以执行GDI文本操作。如果成功执行，则返回TRUE。



{% include image_full.html imageurl="/images/posts/better-1.jpg" title="change" caption="change" %}


### Poetry

>天天下雨，自从你走了
>
>自从你来了，天天下雨
>
>两地友人雨，我乐意负责。
>
>第三处没消息，寄一把伞去？
>
>我的忧愁随草绿天涯：
>
>鸟安于巢吗？人安于客枕？
>
>想在天井里盛一只玻璃杯，
>
>明朝看天下雨今夜落几寸。
>
><cite>― 卞之琳  雨同我 </cite>
