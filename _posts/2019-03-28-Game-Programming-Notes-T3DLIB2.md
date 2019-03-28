---
layout: post
title:  "Game Programming Notes -- T3DLIB2"
date:   2019-03-28
banner_image: sekiro.jpg
tags: [Game Programming]
---
《3D游戏编程大师技巧》 笔记

T3DLIB2——围绕着DirectInput

<!--more-->

## T3DLIB 2

------

编写一套DirectInput的简单封装很容易，只需要创建一个API，它包含一个非常简单的接口和几个参数。该接口至少应该支持下列功能：

- 初始化DirectInput系统
- 设置并读取鼠标、键盘和手柄等
- 从任何输入设备读取数据
- 关闭和释放所有资源

该库中的全局变量

```c++
LPDIRECTINPUT8 lpdi; //dinput对象
LPDIRECTINPUTDEVICE8 lpdikey; //dinput键盘
LPDIRECTINPUTDEVICE8 lpdimouse; //dinput鼠标
LPDIRECTINPUTDEVICE8 lpdijoy; //dinput手柄
GUID joystickGUID; //主手柄的guid
char joyname[80]; //主手柄的名称

//所有输入都存储在这些记录中
UCHAR keyboard_state[256]; //键盘状态表
DIMOUSESTARE mouse_state; //鼠标状态
DIJOYSTATE joy_state; //手柄状态
int joystick_found; //指出手柄是否插入
```

来自键盘的输入存储在keyboard_state[]中，鼠标数据存放到mouse_state中，手柄数据存储在joy_state中。这些记录的结构都是标准DirectInput设备状态结构（键盘数据除外，它使用一个布尔型BYTES数组）。

```c++
//鼠标数据
typedef struct DIMOUSESTATE {
    LONG lX; //x坐标
    LONG lY; //y坐标
    LONG lZ; //z坐标
    BYTE rgbButtons[4]; //鼠标按钮的状态
} DIMOUSESTATE, *LPDIMOUSESTATE;

//手柄数据
typedef struct DJOYSTATE {
    LONG lX;
    LONG lY;
    LONG lZ;
    LONG LRx;
    LONG LRy;
    LONG LRz;
    LONG rglSlider[2]; //u、v滑块（slider）的位置
    DWORD rgdwPOV[4]; //视点状态
    BYTE rgbButtons[32]; //按钮0~31的状态
} DIJOYSTATE, *LPDIJOYSTATE;
```

*DInput_Init*

```c++
int DInput_Init(void);
```

该函数初始化DirectInput输入系统。它创建主COM对象，如果成功则返回TRUE，否则返回FALSE。同时，全局变量lpdi将成为有效变量。但是，该函数不创建任何设备。

*DInput_Shutdown*

```c++
void DInput_Shutdown(void);
```

该函数释放所有COM对象以及调用DInput_Init函数期间分配的所有资源。通常，应该在释放所有输入设备后调用Dinput_Shutdown函数。

*DInput_Init_Keyboard*

```c++
int DInput_Init_Keyboard(void)
```

该函数初始化键盘并开始获取键盘输入。它应该总是能够正常工作并返回TRUE，除非另一个DirectX应用程序以不合作方式接管了键盘。

*DInput_Init_Mouse*

```c++
int DInput_Init_Mouse(void)
```

该函数初始化鼠标并开始获取鼠标输入。该函数不带参数，如果成功返回TURE，否则返回FALSE。它应该总是能够正常工作，除非没有插入鼠标或另一个DirectX应用程序完全接管了鼠标。如果一切正常，lpdimouse将成为有效的接口指针。

*Dinput_Init_Joystick*

```c++
int Dinput_Init_Joystick(int min_x=-256, //最小x值
                        int max_x=256, //最大x值
                        int min_y=-256, //最小y值
                        int max_y=256, //最大y值
                        int dead_zone=10); //死区（dead zone）比例
```

该函数初始化游戏杆设备。该函数受5个参数，分别定义了从游戏杆发送回来的x、y范围以及死区比例。如果要使用默认值-256到256以及10%的死区，可以不提供参数，因为它们有默认值。如果该函数返回TRUE，表示已经发现游戏杆并对其进行了设置和初始化。调用该函数后，接口指针lpdijoy将有效。此外，字符串joyname[]将包含游戏杆设备的“友好”名称。

*DInput_Release_Joystick<br>DInput_Release_Mouse<br>DInput_Release_Keyboard*

```c++
void DInput_Release_Joystick(void);
void DInput_Release_Mouse(void);
void DInput_Release_Keyboard(void);
```

这些函数分别释放相应的输入设备。即使没有初始化这些设备，也可以调用这些函数，所以您尽可以在应用程序结束时调用它们。

*DInput_Read_Keyboard*

```c++
int DInput_Read_Keyboard(void);
```

该函数扫描键盘，并将数据存储到数组keyboard_state[]中，后者是一个包含256个元素的BYTE数组。这是标准DirectInput键盘状态数组要使它的意义很明确，必须使用DirectInput键盘常量DIK_*。键被按下时，相应元素的值为0x80.

*DInput_Read_Mouse*

```c++
int DInput_Read_Mouse(void);
```

该函数读取相对鼠标状态并将结果存储在mouse_state中，后者是一个DIMOUSESTATE结构。数据是相对delta模式。大多数情况下，只需查看mouse_state.1X、mouse_state.1Y和rgbButtons[0..2]（针对3个鼠标按钮的bool值）。下面的范例读取鼠标状态并据此移动光标和绘制图像。

*DInput_Read_Joystick*

```c++
int DInput_Read_Joystick(void);
```

该函数轮询游戏杆，然后将数据读取到joy_state中，后者是一个DIJOYSTATE结构。当然，如果没有游戏杆，该函数将返回FALSE且joy_state无效。如果成功，joy_state将包含游戏杆的状态信息。返回的数据将位于前面设置的范围内，而按钮值是存储在rgbButtons[]中的bool值。



{% include image_full.html imageurl="/images/posts/sekiro2.jpg" title="sekiro" caption="sekiro" %}


### Poetry

>半岛是大陆的纤手，
>
>遥指海上的三神山。
>
>小楼已有了三面水，
>
>可看而不可饮的。
>
>一股泉乃涌到庭心，
>
>人迹仍描到门前。
>
>昨夜里一点宝石，
>
>你望见的就是这里。
>
>用窗帘藏却大海吧，
>
>怕来客又遥望出帆。
>
><cite>― 卞之琳  半岛 </cite>
