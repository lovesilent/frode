---
layout: post
title:  "Game Programming Notes -- T3DLIB1_5"
date:   2019-03-27
banner_image: solo.jpg
tags: [Game Programming]
---
《3D游戏编程大师技巧》 笔记

T3DLIB1之8位调色板函数、实用函数和BOB引擎

<!--more-->

## T3DLIB１

------

### 8位调色板函数

下列函数组成了8位调色板接口。仅当显示模式为8位时这些函数才有用。在窗口显示模式中，前10种颜色供Windows使用，您不能修改它们：当然，在全屏模式下可以修改它们。

另外，用于存储颜色的基本数据结构是Win32结构PALETTEENTRY

*tagPALETTEENTRY*

```c++
typedef struct tagPALETTEENTRY{
    BYTE peRed; //红色分量
    BYTE peGreen; //绿色分量
    BYTE peBlue; //蓝色分量
    BYTE peFlags; //标记，Windows颜色为PC_EXPLICIT，其他颜色为ＰＣ_NOCOLLAPSE
} PALETTEENTRY;
```

*Set_Palette_Entry*

```c++
int Set_Palette_Entry{
    int color_index, //要修改其颜色的索引
    LPPALETTEENTRY color); //颜色
}
```

该函数修改掉色版中的一种颜色。您只需要提供一个0~255的颜色索引和一个包含颜色的PALETTEENTRY指针，在下一帧中更新将生效。另外，该函数还将更新阴影调色板。该函数的运行速度很慢，如果要更新整个调色板，应使用Set_Palette()函数。如果成功执行，则返回TRUE；否则返回FALSE。

*Get_Palette_Entry*

```c++
int Get_Palette_Entry(
		int color_index, //要检索的颜色索引
		LPPALETTEENTRY color); //用于存储颜色
```

从当前调色板中取回一个调色板项，速度非常快，因为它从基于RAM的阴影调色板中获取数据。因此，可以尽情地调用该函数，而不会影响硬件的性能。然而，如果使用函数Set_Palette_Entry()或Set_Paltte()来修改系统调色板，阴影调色板将不会相应地更新，从中获取的数据可能是无效数据。如果成功执行，返回TRUE；否则返回FALSE。

*Save_Palette_To_File*

```c++
int Save_Palette_To_file(
		char **filename, //要将调色板保存其中的文件的名称
		LPPALETTEENTRY palette); //要保存的调色板
```

该函数将指定调色板数据保存到磁盘上的ASCII文件中，供以后检索或处理。如果动态地生成了一个调色板并想将它保存到磁盘上，该函数将非常方便。然而，该函数假设调色板中的指针指向一个256项的调色板，使用时一定要小心。如果成功执行，返回TRUE；否则返回FALSE。

*Load_Palette_From_File*

```c++
int Load_Palette_From_File(
			char **filename, //要从中加载数据的文件
			LPPALETTEENTRY palette); //调色板
```

从磁盘加载以前通过Save_Palette_To_File()函数保存的256调色板。您只需要提供文件名和用于存储调色板的数据结构，该函数将把调色板从磁盘加载到数据结构中。然而，该函数不会将调色板项加载到硬件调色板中，您必须自己通过Set_Palette()函数手工完成这种加载。如果成功执行，返回TRUE；否则返回FALSE。

*Set_Palette*

```c++
int Set_Palette(LPPALETTEENTRY set_palette); //要加载到硬件中的调色板
```

将指定调色板数据加载到硬件调色板中，同时更新阴影调色板。如果成功执行，返回TRUE；否则返回FALSE。

*Save_Palette*

```c++
int Save_Palette(LPPALETTEENTRY sav_palette); //调色板变量
```

扫描硬件调色板，将其保存到sav_palette中，以便能够将它保存到磁盘中或对其进行处理，save_palette必须有足够的存储空间来存储256种颜色。

*Rotate_Colors*

```c++
int Rotate_Colors(int start_index, //起始索引（0~255）
                  int end_index); //终止索引（0~255）
```

以循环的方式旋转颜色项。它直接操纵硬件调色板。如果成功执行，返回TRUE；否则返回FALSE。

*Blink_Colors*

```c++
int Blink_Colors(int command, //闪光灯引擎命令
                BLINKER_PTR new_light, //闪光灯数据
                int id); //闪光灯ID
```

Blink_Colors()函数用于创建异步调色板动画。

### 实用函数

*Get_Clock*

```c++
DWORD Get_Clock(void);
```

该函数返回Windows启动后过去的时间，单位为毫秒。

*Start_Clock*

```c++
DWORD Start_Clock(void);
```

该函数调用Get_Clock()函数，并将时间储存在一个全局变量中。然后您可以调用Wait_Clock()，它在您调用Start_Clock()后等待指定的毫秒数。该函数返回调用时的时钟值。

*Wait_Clock*

```c++
DWORD Wait_Clock(DWORD count); //等待多长时间，单位为毫秒
```

该函数在调用Start_Clock()后等待指定的毫秒数。该函数返回调用时的时钟计数。然而，在指定时间到达之前，该函数不会返回。

*Collision_Test*

```c++
int Collision_Test(int x1, int y1, //物体1的左上角坐标
                  int w1, int h1, //物体1的宽度和高度
                  int x2, int y2, //物体2左上角坐标
                  int w2, int h2); //物体2的宽度和高度
```

该函数检测指定的两个矩形是否重叠。您必须提供每个矩形的左上角坐标以及宽度和高度。如果成功执行，返回TRUE；否则返回FALSE。

*Color_Scan*

```c++
int Color_Scan(int x1, int y1, //矩形的左上角坐标
              int x2, int y2, //矩形的右下角坐标
              UCHAR scan_start, //起始扫描颜色
              UCHAR scan_end, //终止扫描颜色
              int scan_lpitch); // 内存跨距
```

另一种碰撞检测算法，它扫描一个矩形，以检测一个8位值或某个范围内的一系列值。可以使用它来判断某个颜色索引是否出现在某个区域中。当然，它只适用于8位图像，但其源代码很容易扩展到16位或更高的颜色模式。如果发现指定颜色，则返回TRUE。

### BOB(Blitter对象)引擎

虽然通过少量编程，可以用BITMAP_IMAGE类型做任何事情，但它不是最好的方式——它没有使用DirectDraw表面，所以不支持硬件加速。因此，需要使用叫做BOB(Blitter对象)的类型，它非常类似于子画面。子画面(sprite)是一个可以在屏幕上移动而不会影响屏幕背景的对象。在这里，将动画对象称为BOB，而不是子画面。

BOB引擎时使用DirectDraw表面和2D加速的典范。

<u>BOB数据结构</u>

```c++
typedef struct BOB_TYP
{
    int state; 
    int anim_state;
    int attr;
    float x,y;
    float xv,yv;
    int width, height;
    int width_fill;
    int bpp;
    int counter_1;
    int counter_2;
    int max_count_1;
    int max_count_2;
    int varsI[16];//深度位16的整数栈
    float varsF[16]; //深度位16的浮点数栈
    int curr_frame;
    int num_frames;
    int curr_animations;
    int anim_counter; //用于给动作切换定时
    int anim_index; //动作元素索引
    int anim_count_max; //几帧之后再切换动作
    int *animations[MAX_BOB_ANIMATIONS]; //动作
    // 位图图像DD表面
    LPDIRECTDRAWSURFACE7 images[MAX_BOB_FRAMES];
} BOB, *BOB_PTR;
```

BOB是由一个或多个DirectDraw表面（当前最多64个）表示的图形对象  。可以移动BOB、绘制BOB、使用BOB制作动画、使之处于运动状态。BOB考虑当前DirectDraw裁剪矩形，因此将被裁剪和加速。

BOB引擎支持8位或16位图像和动画序列，您可以加载一组帧和一个动画序列，然后该序列将可以播放帧中的内容。这是一种非常棒的功能。另外，BOB能够在8位或16位颜色模式下工作，大多数函数有其内部逻辑，能够检测到系统当前的位深。唯一需要显示地为16位颜色模式调用不同版本地函数是Load_Frame_BOB*()(加载BOB)和Draw_BOB*()(绘制BOB)。 

*Create_BOB*

```c++
int Create_BOB(BOB_PTR bob, //指向要创建的BOB的指针
               int x, int y, //BOB的初始位置
               int num_frames, //总帧数
               int attr, //属性
               int mem_flags=0, //表面内存标记，0表示VRAM
               USHORT color_key_value=0, //颜色键值
               //根据bbp的设置被解释为8位索引或16位RGB值
               int bpp=8); //bits per pixel, default is 8
```

创建一个BOB对象，并对它进行设置。该函数分配所有内部变量，并为每帧创建一个单独的DirectDraw表面。大多数变量的含义都是不言自明的，唯一需要解释的一个参数是属性变量attr。

*Destroy_BOB*

```c++
int Destroy_BOB(BOB_PTR bob);//指向要删除的BOB的指针
```

将删除以前创建的BOB。

*Draw_BOB*

```c++
int Draw_BOB(BOB_PTR bob, //指向要绘制的BOB指针
            LPDIRECTDRAWSURFACE7 dest); //目标表面

int Draw_BOB16(BOB_PTR bob,
              LPDIRECTDRAWSURFACE7 dest);
```

功能非常强大的函数，它在指定的DirectDraw表面上绘制指定的BOB。将在当前位置绘制BOB中的当前帧。

*Draw_Scaled_BOB*

```c++
int Draw_Scaled_BOB(BOB_PTR bob, //指向要绘制的BOB的指针
                   int swidth, int sheight, //新的BOB宽度和高度
                   LPDIRECTDRAWSURFACE7 dest); //目标表面

int Draw_Scaled_BOB16(BOB_PTR bob, //指向要绘制的BOB的指针
                     int swidth, int sheight,
                     LPDIRECTDRAWSURFACE7 dest);
```

可以指定绘制出的BOB的宽度和高度，这样之前的BOB将相应地被缩放。如果有硬件加速功能，这将是一种缩放BOB以获得3D效果地有效方法。

*Load_Frame_BOB*

```c++
int Load_Frame_BOB(BOB_PTR bob, //指向要将帧加载到其中的BOB的指针
                  BITMAP_FILE_PTR bitmap, //指向想要从中扫描数据的位图的指针
                  int frame, //将图像加载到哪一帧中
                  int cx, int cy, //用单元格或坐标表示的扫描位置
                  int mode); //扫描模式，与Load_Frame_Bitmap() 中相同

int Load_Frame_BOB16(BOB_PTR bob,
                    BITMAP_FILE_PTR bitmap,
                    int frame,
                    int cx, int cy,
                    int mode);
```

该函数的功能与Load_Frame_Bitmap完全相同。唯一的区别就是，新增了指定将图像加载到哪一帧中的功能。

*Load_Animation_BOB*

```c++
int Load_Animation_BOB(BOB_PTR bob, //bob to load animation into
                      int anim_index, //which to animation to load 0..15
                      int num_frames, //number of frames of animation
                      int *sequence); //指向存储动画序列的数组的指针
```

该函数用于设置BOB内部的16个数组之一，这些数组包含了动画序列。

*Set_Pos_BOB*

```c++
int Set_Pos_BOB(BOB_PTR bob, //指向要设置其位置的BOB的指针
               int x, int y); //新位置
```

该函数是一种设置BOB位置的简单方法。它除了对内部变量(x,y)赋值外没做任何其他事情。

*Set_Vel_BOB*

```c++
int Set_Vel_BOB(BOB_PTR bob, //指向要设置其速度的BOB的指针
               int xv, int yv); //新速度
```

每个BOB都由一个(xv, yv)指定的速度。该函数根据传入的参数改变这两个值。仅当您使用函数Move_BOB移动BOB时，BOB中的速度值才具有意义。然而，也可以使用变量xv和yv来跟踪BOB的速度。

*Set_Anim_Speed*

```c++
int Set_Anim_Speed(BOB_PTR bob, //BOB指针
                  int speed); //动画速度
```

该函数将BOB的内部动画速度设置为anim_count_max。这个数值越大，动画速度越慢；然而，仅当使用内部BOB函数Animate_BOB时才有意义。当然，必须创建了一个包含多帧的BOB。

*Set_Animation_BOB*

```c++
int Set_Animation_BOB(BOB_PTR bob, //指向要设置其当前动作的BOB的指针
                     int anim_index); //动作索引
```

该函数设置BOB将播放的动作，并将当前帧设置为当前动作的第一帧。

*Animate_BOB*

```c++
int Animate_BOB(BOB_PTR bob);//执行BOB的指针
```

该函数使用BOB来生成动画。通常，将在每帧中调用该函数一次，以更新BOB动画。

*Move_BOB*

```c++
int Move_BOB(BOB_PTR bob); //指向要移动的BOB的指针
```

该函数根据Set_Vel_BOB函数设置的(xv, yv)来移动BOB，根据属性，BOB可能从屏幕边界反弹回来、环回到屏幕另一侧或仅仅向前移动。与Animate_BOB函数类似，可以在主循环中调用Animate_BOB函数之后（或之前）调用一次。

*Hide_BOB*

```c++
int Hide_BOB(BOB_PTR bob); //指向要隐藏的BOB的指针
```

该函数将BOB的内部可见性标记设置为０，之后Draw_BOB函数将不再显示该BOB。

*Show_BOB*

```c++
int Show_BOB(BOB_PTR bob); //指向要显示的BOB的指针
```

该函数将BOB的内部可见标记设置为1，使之被绘制出来（相当于撤销Hide_BOB函数调用）。

```c++
Collision_BOBS(BOB_PTR bob1, //指向第一个BOB的指针
              BOB_PTR bob2) //指向第二个BOB的指针
```

该函数检测两个BOB的包围矩形是否重叠，可用于在游戏中的碰撞检测，以判断玩家BOB是否被导弹BOB或者其他东西击中。



{% include image_full.html imageurl="/images/posts/better-1.jpg" title="change" caption="change" %}


### Poetry

>乡下小孩子怕寂寞，
>
>枕头边养一只蝈蝈；
>
>长大了在城里操劳，
>
>他买了一个夜明表。
>
>小时候他常常羡艳，
>
>墓草做蝈蝈的家园；
>
>如今他死了三小时，
>
>夜明表还不曾休止。
>
><cite>― 卞之琳  寂寞 </cite>
