---
layout: post
title:  "Game Programming Notes -- T3DLIB1_2"
date:   2019-03-18
banner_image: solo.jpg
tags: [Game Programming]
---
《3D游戏编程大师技巧》 笔记

T3DLIB1之DirectDraw接口

<!--more-->

## T3DLIB１

------

### DirectDraw接口

*DDraw_Init*

```c++
int DDraw_Init(int width, int height, int bpp, int windowed=0)
```

用于设置和初始化DirectDraw 可以使用任何分辨率和色深。如果要使用窗口显示，将最后一个参数设置位1；默认位全屏显示。如果成功执行，返回TRUE。

*DDraw_Shutdown*

```c++
int DDraw_Shutdown();
```

关闭DirectDraw并释放所有接口

```c++
LPDIRECTDRAWCLIPPER DDraw_Attach_Clipper(LPDIRECTDRAWSURFACE7 lpdds, int num_rects, LPRECT clip_list);
```

将一个裁剪区域与指定表面（通常位后缓存）关联起来。另外，还必须指定裁剪矩形列表及其包含的矩形数量。如果成功执行，则返回TRUE。

*DDraw_Create_Surface*

用于在系统内存、VRAM或AGP内存中创建一个常规离屏DirectDraw表面。默认为DDSCAPS_OFFSCREENPLAIN。可以使用逻辑OR将任何控制标与默认值组合起来。它们是标准的DirectDraw DDSCAP*标记，例如，DDSCAPS_SYSTEMMEMORY和DDSCAPS_VIDEOMEMORY分别代表系统内存和VRAM。

*DDraw_Flip*

```c++
int DDraw_Flip(void)
```

在全屏模式下，DDraw_Filp()将辅助表面复制到主表面；在窗口模式下，它将虚拟辅助缓存复制到窗口的客户区域。该函数将一直等待，直到可以执行复制，因此它可能不会立即返回。如果成功执行，则返回TRUE。

*DDraw_Wait_For_Vsync*

```c++
int DDraw_Wait_For_Vsync(void)
```

它将等待，直到下一个垂直空白间隔开始（光栅到达屏幕底部）。如果成功执行，则返回TRUE，否则返回FALSE。

*DDraw_Fill_Surface*

```c++
int DDraw_Fill_Surface(LPDIRECTDRAWSURFACE7 lpdds, // 需要填充的表面
	int color, //用于填充的颜色
	RECT *client);// 客户区域，NULL表示整个表面 
```

使用一种颜色来填充表面。该颜色的格式必须与指定表面的色深格式相同，例如，在256色模式下位1字节，在高颜色模式下位RGB描述符。如果要填充表面的一个子区域，可设置RECT*参数，以指定该区域；反则将填充整个表面。如果执行成功，则返回TRUE。

*DDraw_Lock_Surface*

```c++
UCHAR *DDraw_Lock_Surface(LPDIRECTDRAWSURFACE7 lpdds, int *lpitch)
```

锁定指定表面（如果可能），并返回指向该表面的指针，同时用该表面的内存跨距更新指定的lpitch变量。表面处于锁定状态时，可对它执行操作，将像素写入到其中，但是交换操作将被阻止，因此应该尽可能快地解锁表面。解锁表面后，内存指针和内存跨距可能无效，所以不应再使用它们。如果成功执行，则返回表面内存的非NULL地址，否则返回NULL值。

*DDraw_Unlock_Surface*

```c++
int DDraw_Unlock_Surface(LPDIRECTDRAWSURFACE7 lpdds);
```

将解除对表面的锁定，您只需提供表面的指针。如果成功执行，则返回TRUE。

*DDraw_Lock_Back_Surface<br>DDraw_Lock_Primary_Surface*

```c++
UCHAR *DDraw_Lock_back_Surface(void);
UCHAR *DDraw_Lock_Primary_Surface(vodi);
```

这两个函数用于锁定主渲染表面和辅助渲染表面。在多数情况下您只会锁定辅助表面，因为这是一个双缓存系统，但是提供锁定主渲染表的功能。如果调用DDraw_Lock_Primary_Surface()，则下列全局变量将成为有效变量：

```c++
extern UCHAR *primary_buffer; //指向主视频缓存的指针
extern int primary_lpitch; //内存行跨距
```

然后可以根据需求操作表面内存；但是，在锁定期间将组织交换，且所有硬件加速功能都被禁止。同样，调用DDraw_Lock_Back_Surface()函数将锁定辅助缓存表面，并使下列变量成为有效变量：

```c++
extern UCHAR *back_buffer; //指向后缓存的指针
extern int back_lpitch; //内存行跨距
```

注意：不要去修改这些全局变量——它们用于跟踪锁定函数中的状态变化，手工修改这些全局变量可能导致引擎崩溃。

*DDraw_Unlock_Back_Surface<br>DDraw_Unlock_Primary_Surface*

```c++
int DDraw_Unlock_Back_Surface(void);
int DDraw_Unlock_Primary_Surface(void);
```

用于解除对主缓存和辅助缓存表面的锁定。试图解锁一个未被锁定的表面，将不会有任何效果。如果成功执行，则返回TRUE。



{% include image_full.html imageurl="/images/posts/better-1.jpg" title="change" caption="change" %}


### Poetry

>你站在桥上看风景
>
>看风景的人在楼上看你
>
>明月装饰了你的窗子
>
>你装饰了别人的梦
>
><cite>― 卞之琳  断章 </cite>
