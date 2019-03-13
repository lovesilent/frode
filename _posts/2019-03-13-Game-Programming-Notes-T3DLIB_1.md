---
layout: post
title:  "Game Programming Notes -- T3DLIB_1"
date:   2019-03-13
banner_image: solo.jpg
tags: [Game Programming]
---
《3D游戏编程大师技巧》 笔记

   T3DLIB库

- T3DLIB1.CPP/H -- DirectDraw 和图形算法
- T3DLIB2.CPP/H -- DirectInput
- T3DLIB3.CPP/H -- DirectSound 和 DirectMusic

<!--more-->

先从T3DLIB1开始。。。

依次描述“基本常量”、“工作宏”、“数据类型和结构”、以及“全局变量”。

## T3DLIB１

------

### 基本常量

- 屏幕相关设置
- 位图相关
- 像素格式
- BOB
- 屏幕渐变
- Blink_Colors
- 圆周率
- 定点数学

### 工作宏

- 异步方式读取键盘输入
- 颜色设定
- 位运算宏
- 初始化direct draw结构
- 比较函数
- 交换变量

### 数据类型和结构

<u>基本无符号类型</u>

```c++
typedef unsigned short USHORT
typedef unsigned short WORD
typedef unsigned char UCHAR
typedef unsigned char BYTE
typedef unsigned int QUAD
typedef unsigned int UINT
```

<u>.BMP文件位图的容器结构</u>

```c++
typedef struct BITMAP_FILE_TAG
{
    BITMAPFILEHEADER bitmapfileheader; //包含位图文件头
    BITMAPINFOHEADER bitmapinfoheader; //包含调色板在内的所有信息
    PALETTEENTRY palette[256]; // 存储调色板
    UCHAR *buffer; //指向数据的指针
} BITMAP_FILE, *BITMAP_FILE_PTR;
```

<u>BOB</u>

```c++
typedef struct BOB_TYP
{
    int state; // 状态
    int anim_state; //动作状态变量
    int attr; //属性
    float x,y; //位置
    float xv,yv; //速度
    int width, height; //宽度和高度
    int width_fill; //用于强制表面的宽度位8的倍数
    int bpp; //每个像素占用的位数
    int counter_1; //计数器
    int counter_2; 
    int max_count_1; //阈值
    int max_count_2;
    int varsI[16]; //深度位16的整数栈
    int varsF[16]; //深度位16的浮点数栈
    int curr_frame; //当前动画帧
    int num_frame; //总动画帧
    int curr_animaiton; //当前动作索引
    int anim_counter; //用于给动作切换定时
    int anim_index; //动作元素索引
    int anim_count_max; //几帧后再切换动作
    int *animations[MAX_BOB_ANIMATIONS]; //动作
    LPDIRECTDRAWSURFACE7 images[MAX_BOB_FRAMES]; //位图图像DD表面
} BOB, *BOB_PTR;
```

<u>简单位图图像</u>

```c++
typedef struct BITMAP_IMAGE_TYP
{
    int state; //状态
    int attr; //属性
    int x,y; //位置
    int width, height; //尺寸
    int num_bytes; //总字节数
    int bpp; //每个像素占用多少位
    UCHAR *buffer; //位图中的像素数
} BITMAP_IMAGE, *BITMAP_IMAGE_PTR;
```

<u>闪光灯结构</u>

```c++
typedef struct BLINKER_TYP
{
    //由用户设置
    int color_index; //不考虑的颜色索引
    PALETTEENTRY on_color; //开时颜色的RGB值
    PALETTEENTRY off_color; //关时颜色的RGB值
    int on_time; //持续开多少帧
    int off_time; //持续关多少帧
    
    //内部成员
    int counter; //状态切换计数器
    int state; //光源状态，1表示开，-1表示关，0表示无效
} BLINKER, *BLINKER_PTR;
```

<u>2D顶点</u>

```c++
typedef struct VERTEX2DI_TYP
{
    int x,y; //顶点坐标
} VERTEX2DI, *VERTEX2DI_PTR;
```

<u>2D多边形</u>

```c++
typedef struct POLYGON2D_TYP
{
    int state; //状态
    int num_verts; //顶点数
    int x0,y0; //多边形中心位置
    int xv,yv; //初始速度
    DWORD color; //索引或PALETTENTRY
    VERTEX2DF *vlist; //指向顶点列表的指针
} POLYGON2D, *POLYGON2D_PTR;
```

<u>矩阵定义</u>

```c++
typedef struct MATRIX3X3_TYP
{
    union
    {
        float M[3][3]; //以数组方式存储
        // 按先行后列的顺序以单独变量方式存储
        struct 
        {
            float M01,M01,M02;
            float M10,M11,M12;
            float M20,M21,M22;
        }; // end explictit names
    }; //end union
} MATRIX3X3, *MATRIX3X3_PTR;

typedef struct MATRIX1X3_TYP
{
    union
    {
        float M[3]; //以数组方式存储
        // 按先行后列的顺序以单独变量方式存储
        struct
        {
            float M00,M01,M02;
        }; // end explicit names;
    }; // end union
} MATRIX1X3, *MATRIX1X3_PTR;

typedef struct MATRIX3X2_TYP
{
    union
    {
        float M[3][2]; //以数组方式存储
        struct
        {
            float M00,M01;
            float M10,M11;
            float M20,M21;
        }; end explicit names
    }; // end union
} MATRIX3X2, *MATRIX3X2_PTR;

typedef struct MATRIX1X2_TYP
{
    union
    {
        float M[2]; //以数组方式存储
        struct
        {
            float M00, M01;
        }; // end explicit names
    }; // end union
} MATRIX1X2, *MATRIX1X2_PTR;
```

### 全局变量

```c++
FILE *fp_error; //通用错误文件
char error_filename[80]; //错误文件名
```

<u>使用的很多接口是7.0版本的</u>

```C++
LPDIRECTDRAW7 lpdd; //dd对象
LPDIRECTDRAWSURFACE7 lpddsprimary; //dd主缓存
LPDIRECTDRAWSURFACE7 lpdddback; //dd后缓存
LPDIRECTDRAWPALETTE lpddpal; //指向dd调色板的指针
LPDIRECTDRAWCLIPPER lpddclipper;  //用于后缓存的dd剪裁程序
LPDIRECTDRAWCLIPPER lpddclipperwin; //用于窗口的dd剪裁程序
PALETTEENTRY palette[256]; //调色板
PALETTEENTRY save_palette[256]; //用于保存调色板
DDUSRFACEDESC2 ddsd; //dd面描述结构
DDBLTFX ddbltfx; //用于填充
DDSCAPS2 ddscaps; // dd面功能结构
HRESULT ddrval; //dd调用返回的结果
UCHAR *primary_buffer; //主视频缓存
UCHAR *back_buffer; //辅助缓存
int primary_lpitch; //内存行跨距
int back_lpitch; 
BITMAP_FILE bitmap8bit; //8位的位图文件
BITMAP_FILE bitmap16bit; //16位的位图文件
BITMAP_FILE bitmap24bit; //24位的位图文件

DWORD start_clock_count; //用于计时
int windowed_mode; //记录dd是否处于窗口模式
```

<u>这些变量定义了软件裁剪时使用的裁剪矩形</u>

```C++
int min_clip_x, //裁剪举行
  max_clip_x,
  min_clip_y,
  max_clip_y;
```

<u>调用DD_Init()时将覆盖这些变量的值</u>

```C++
int screen_width, //屏幕宽度
  screen_height, //屏幕高度
  screen_bpp, //每个像素占用多少位
  screen_windowed; //是窗口模式应用程序吗？
int dd_pixel_format; //默认像素格式
int window_client_x0; //用于存储dd窗口模式，客户区域的左上角的位置
```

<u>用于储存查找表</u>

```
float cos_look[360];
float sin_look[360];
```

<u>指向RGB16颜色生成函数的指针，这两个函数分别生成5.5.5和5.6.5格式的颜色值</u>

```C++
USHORT (*RGB16Bit)(int r, int g, int b);
```

{% highlight c++ %}

USHORT (*RGB16Bit)(int r, int g, int b);

{% endhighlight%}



{% include image_full.html imageurl="/images/posts/better-1.jpg" title="change" caption="change" %}


### Poetry

>
>我如果爱你——
>
>绝不像攀援的凌霄花，
>
>借你的高枝炫耀自己；
>
>我如果爱你——
>
>绝不学痴情的鸟儿，
>
>为绿荫重复单调的歌曲；
>
>也不止像泉源，
>
>常年送来清凉的慰藉；
>
>也不止像险峰，
>
>增加你的高度，衬托你的威仪。
>
>甚至日光，
>
>甚至春雨。
>
>
>
>不，这些都还不够！
>
>我必须是你近旁的一株木棉，
>
>作为树的形象和你站在一起。
>
>根，紧握在地下；
>
>叶，相触在云里。
>
>每一阵风过，
>
>我们都互相致意，
>
>但没有人，
>
>听懂我们的言语。
>
>你有你的桐枝铁干，
>
>像刀，像剑，也像戟；
>
>我有我红硕的花朵，
>
>像沉重的叹息，
>
>又像英勇的火炬。
>
>
>
>我们分担寒潮、风雷、霹雳；
>
>我们共享雾霭、流岚、虹霓。
>
>仿佛永远分离，
>
>却又终身相依。
>
>这才是伟大的爱情，
>
>坚贞就在这里：
>
>爱——
>
>不仅爱你伟岸的身躯，
>
>也爱你坚持的位置，
>
>足下的土地。
>
><cite>― 舒婷  致橡树 </cite>
