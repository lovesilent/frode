---
layout: post
title:  "Game Programming Notes -- T3DLIB3"
date:   2019-03-29
banner_image: sekiro.jpg
tags: [Game Programming]
---
《3D游戏编程大师技巧》 笔记

T3DLIB3——围绕着DirectSound 和 DirectMusic

<!--more-->

## T3DLIB3

------

### 头文件

头文件T3DLIB3.H包含了T3DLIB3.CPP使用的类型、宏和外部变量。下面是头文件中的#define语句

```c++
#define DM_NUM_SEGMENTS 64 //可存储到内存中midi节(segment)数
// midi对象状态常量
#define MIDI_NULL 0 //未加载
#define MIDI_LOADED 1 //已加载
#define MIDI_PLAYING 2 //已加载且正在播放
#define MIDI_STOPPED 3 //已加载，但没有播放

#define MAX_SOUNDS 256 //系统可同时加载的最大声音数

//数字声音对象状态常量
#define SOUND_NULL 0 // " "
#define SOUND_LOADED 1
#define SOUND_PLAYING 2
#define SOUND_STOPPED 3

//该文件中的宏不多，有一个用于将0~100转换为微软分贝数，还有一个用于将多字节字符转换为宽字符
#define DSVOLUME_TO_BE(volume) ((DWORD)(-30*(100-volume)))

//从多字节格式转换为Unicode
#define MULTI_TO_WIDE(x,y) MultiByteToWideChar(CP_ACP, MB_PRECOMPOSED, y, -1, x, _MAX_PATH)
```

### 类型

声音引擎只有两种类型：一种用于存储字样本，另一个用于存储MIDI节：

```c++
//用于存储单个声音
typedef struct pcm_sound_typ
{
    LPDIRECTSOUNDBUFFER dsbuffer; //directsound缓冲区 包含声音
    nt state; //状态
    int rate; //播放速度
    int size; //大小
    int id; 
} pcm_sound, *pcm_sound_ptr;
```

下面是DirectMusic 节类型

```c++
//directmusic MIDI 节
typedef struct DMUSTIC_MIDI_TYP
{
    IDirectMusicSegment *dm_segment; //directmusic节
    IDirectMusicSegmentState *dm_segstate; //状态
    int id; //ID
    int state; //MIDI歌曲的状态
} DMUSIC_MIDI, *DMUSIC_MIDI_PTR;
```

声音和MIDI节由引擎分别存储到上述两种结构中。

### 全局变量

<u>首先介绍DirectSound系统的全局变量</u>

```c++
LPDIRECTSOUND lpds; // directsound 接口指针
DSBUFFERDESC dsbd; // directsound 描述
DSCAPS dscaps; // directsound caps
HRESULT dsresult; // directsound 结果
DSBCAPS dsbcaps; // directsound 缓冲区 caps

pcm_sound sound_fx[MAX_SOUNDS]; //声音缓冲区数组
WAVEFORMATEX pcmwf; //通用波形格式结构
```

<u>DirectMusic的全局变量</u>

```c++
//directmusic 全局变量
IDirectMusicPerformance *dm_perf; //directmustic 性能管理器
IDirectMusicLoader *dm_loader; //directmusic 加载函数

//用于存储所有的directmusic midi对象
DMUSIC_MIDI dm_midi[DM_NUM_SEGMENTS];
int dm_active_id; //当前处于活动状态的midi节
```

<u>DirectSound API</u>

```c++
//函数原型
int DSound_Init(void);
int Dsound_Shutdown(void);
int DSound_Load_WAV(char *filename);
int DSound_Replicate_Sound(int source_id); //要复制的声音ID
int DSound_Play_Sound(int id, //要播放的声音的ID
                     int flags=0, //0或DSBPLAY_LOOPING
                     int volume=0,
                     int rate=0,
                     int pan=0);
int DSound_Stop_Sound(int id);
int DSound_Stop_All_Sounds(void);
int DSound_Delete_Sound(int id);
int DSound_Delete_All_Sounds(void);
int DSound_Status_Sound(int id);
int DSound_Set_Sound_Volume(int id, //声音的ID
                           int vol); //声音的取值范围0~100
int DSound_Set_Sound_Freq(int id, //声音ID
                         int freq); //新的播放速度，取值范围为0-100000
int DSound_Set_Sound_Pan(int id, //声音ID
                        int pan); //相对强度，取值范围为-10000到10000
```

<u>DirectMusic API</u>

```c++
int DMusic_Init(void);
int DMusic_Shutdown(void);
int DMusic_Load_MIDI(char *filename);
int DMusic_Delete_MIDI(int id);
int DMusic_Delete_All(void);
int DMusic_Play(int id);
int DMusic_Stop(int id);
int DMusic_Status_MIDI(int id);
```



{% include image_full.html imageurl="/images/posts/sekiro2.jpg" title="sekiro" caption="sekiro" %}


### Poetry

>看到和听到的，
>
>经常令你们沮丧，
>
>世俗是这样强大，
>
>强大到生不出改变它们的念头。
>
>可是如果有机会提前了解你们的人生，
>
>知道青春也不过只有这些日子，
>
>不知你们是否还会在意的事情，
>
>比如占有多少，
>
>才更荣耀，
>
>拥有什么，
>
>才能被爱。
>
>等你们长大，
>
>你们因绿芽冒出土地而喜悦，
>
>会对出生的朝阳欢呼雀跃，
>
>也会给别人善意和温暖，
>
>但是却会在赞美别的生命的同时，
>
>常常，
>
>甚至永远忘了自己的珍贵。
>
>愿你在被打击时，
>
>记起你的珍贵，
>
>抵抗恶意；
>
>愿你在迷茫时，
>
>坚信你的珍贵，
>
>爱你所爱，
>
>行你所行，
>
>听从你心，
>
>无问西东。
>
><cite>― 无问西东 </cite>
