---
layout: post
title:  "Game Programming Notes -- T3DLIB1_4"
date:   2019-03-25
banner_image: solo.jpg
tags: [Game Programming]
---
《3D游戏编程大师技巧》 笔记

T3DLIB1之数学函数、错误函数和位图函数

<!--more-->

## T3DLIB１

------

### 数学函数和错误函数

*Fast_Distance_2D*

```c++
int Fast_Distance_2D(int x, int y);
```

使用一种快速近似方法己算从(0, 0)到(x, y)的距离。返回距离的误差在3.5%之内，并被截取位整数。它使用泰勒级数执行近似运算。

*Fast_Distance_3D*

```c++
float Fast_Distance_3D(float x, float y, float z);
```

使用一种快速近似方法己算从(0,0,0)到(x,y,z)的距离。该函数返回距离的误差在11%之内。

*Find_Bounding_Box_Poly2D*

```c++
int Find_Bounding_Box_Poly2D(
    POLYGON2D_PTR poly, //多边形
    float &min_x, float &max_x, //包围框
    float &min_y, float &max_y);
```

函数用于计算能够包围poly指定的多边形的最小矩形。如果成功执行，则返回TRUE。该函数的参数位引用参数。

*Open_Error_File*

```c++
int Open_Error_File(char *filename);
```

打开一个磁盘文件，用于接收通过Write_Error()函数发送的错误信息。如果成功执行，则返回TRUE。

*Close_Error_File*

```c++
int Close_Error_File(void);
```

关闭前面打开的错误文件。实际上，它将关闭文件流。如果没有错误文件打开，调用该函数时将不会发生任何事情。如果执行成功，则返回TRUE。

*Write_Error*

```c++
int Write_Error(char *string, ...); //错误格式字符串
```

将一条错误信息写入到前面打开的错误文件中。如果没有打开错误文件，该函数返回FALSE，且不会发生任何其他事情。注意该函数使用变量参数指示符(...)，因此可像使用printf()那样使用该函数。如果成功执行，则返回TRUE。

### 位图函数

*Load_Bitmap_File*

```c++
int Load_Bitmap_File(BITMAP_FILE_PTR bitmap, //位图文件指针
                    char *filename); //要从磁盘加载的.BMP文件
```

从磁盘加载一个.BMP格式的位图文件到指定的BITMAP_FILE结构中，以便能能够对其进行处理。该函数加载8位，16位和24位的位图以及8位.BMP文件的调色板信息。如果成功执行，则返回TRUE。

*Unload_Bitmap_File*

```c++
int Unload_Bitmap_File(BITMAP_FILE_PTR bitmap); //bitmap to close and unload
```

将释放为BITMAP_FILE分配的内存。复制图像或完成对位图的操作后，应调用该函数。可以重用该结构，但必须先释放其占用的内存。如果执行成功，则返回TRUE。

*Create_Bitmap*

```c++
int Create_Bitmap(BITMAP_IMAGE_PTR image, //位图图像
                 int x, int y, //起始位置
                 int width, int height, //位图大小
                 int bpp=8); //位图的位深
```

在指定位置按指定大小，创建一个8位或16位的系统内存位图。该位图最初位空，存储在BITMAP_IMAGE图像中。该位图不是DirectDraw表面，因此不能提供硬件加速功能或剪裁功能。除非将参数bpp设置为16以创建16位位图，否则该函数默认创建8位位图，如果成功执行，则返回TRUE。

*Destory_Bitmap*

```c++
int Destory_Bitmap(BITMAP_IMAGE_PTR image); //要删除的位图图像
```

释放创建（8位或16位）BITMAP_IMAGE对象时分配的内存。使用完对象后，应对其调用该函数——通常在游戏关闭期间或经过一场血战后对象被打死。如果成功执行，则返回TRUE。

*Load_Image_Bitmap*

```c++
int Load_Image_Bitmap(
	BITMAP_IAMGE_PTR image, //用于存储图像的位图
	BITMAP_FILE_PTR bitmap, //要加载的位图文件对象
	int cx, int cy, //从哪里开始扫描
	int mode); //图像扫描模式，基于单元格还是绝对坐标

// 16位版本
int Load_Image_Bitmap16(
	BITMAP_IAMGE_PTR image,
	BITMAP_FILE_PTR bitmap,
	int cx, int cy,
	int mode);

#define BITMAP_EXTRACT_MODE_CELL 0
#define BITMAP_EXTRACT_MODE_ABS 1
```

从BITMAP_FILE对象中扫描一副图像到指定的BITMAP_IMAGE存储区域中。当然，位图文件和位图对象必须具相同的位深（8位或16位），且必须使用合适版本的函数。 

要使用该函数，首先必须加载一个BITMAP_FILE对象。然后调用该函数，从存储在BITMAP_FILE中的位图数据中扫描出一幅图像。该函数有两种工作模式：单元格式和绝对模式。

在单元模式下(BITMAP_EXTRACT_MODE_CELL)，扫描图像时假设所有图像都存储在给定大小的模板.BMP文件中，单个格之间有宽度位1像素的边界，单元的大小通常为8x8、16x16、32x32、64x64等。

第二种模式是绝对坐标模式(BITMAP_EXTRACT_MODE_ABS)。在这种模式下，将根据参数cx和cy指定的坐标来扫描图像。要从相同.BMP文件中加载不同大小的图像时，可以使用这种模式。因此，您不能对它们进行模板化。

*Draw_Bitmap*

```c++
int Draw_Bitmap(BITMAP_IMAGE_PTR source_bitmap, //要绘制的位图
               UCHAR *dest_buffer, //视频缓存
               int lpitch, //内存跨距
               int transparent); //是否透明
//16位版本
int Draw_Bitmap(BITMAP_IMAGE_PTR source_bitmap, //要绘制的位图
               UCHAR *dest_buffer, //视频缓存
               int lpitch, //内存跨距
               int transparent); //是否透明
```

在8位或16位颜色模式下，以透明或不透明方式将指定位图绘制到目标内存表面上。如果参数transparent为1，则启用透明方式，不复制颜色索引为0或RGB为0.0.0的像素。如果成功执行，则返回TRUE。

*Flip_Bitmap*

```c++
int Flip_Bitmap(UCHAR *image, //要垂直翻转的图像位
               int bytes_per_line, //每行多少字节
               int height); //总行数（高度）
```

Flip_Bitmap()通常被内部调用，用于在加载期间将.BMP文件上下倒置，但您有时也可能想使用它来倒置图像(8位或16位)。该函数在内存中执行操作，逐行反转位图，所以原始数据将被反转，使用时需要要注意！如果成功执行，则返回TRUE。

*Copy_Bitmap*

```c++
int Copy_Bitmap(BITMAP_IMAGE_PTR dest_bitmap,
               int dest_x, int dest_y,
               BITMAP_IMAGE_PTR source_bitmap,
               int source_x, int source_y,
               int width, int height);
```

从一个位图复制一个矩形区域到另一个位图中。源位图和目标位图可以相同；但源区域和目标区域不能重叠，否则结果将不确定。如果成功执行，则返回TRUE。

*Scroll_Bitmap*

```c++
int Scroll_Bitmap(BITMAP_IMAGE_PTR image, int dx, int dy=0);
```

将指定位图水平滚动dx和垂直滚动dy。参数为正时将向右/下滚动，参数为负时向左/上滚动。如果成功执行，则返回TRUE。



{% include image_full.html imageurl="/images/posts/better-1.jpg" title="change" caption="change" %}


### Poetry

>象候鸟衔来了异方的种子，
>
>三桅船载来了一枝尺八。
>
>从夕阳里，从海西头，
>
>长安丸载来的海西客。
>
>夜半听楼下醉汉的尺八，
>
>想一个孤馆寄居的番客
>
>听了雁声，动了乡愁，
>
>得了慰藉于邻家的尺八。
>
>次朝在长安市的繁华里
>
>独访取一枝凄凉的竹管......
>
>（为什么年红灯的万花间，
>
>还飘着一缕凄凉的古香？）
>
>归去也，归去也，归去也——
>
>象候鸟衔来来异方的种子，
>
>三桅船载来了一枝尺八
>
>尺八乃成了三岛的花草。
>
>（为什么年红灯的万花间，
>
>还飘着一缕凄凉的古香？）
>
>归去也，归去也，归去也——
>
>海西人想带回失去的悲哀吗？
>
><cite>― 卞之琳  尺八 </cite>
