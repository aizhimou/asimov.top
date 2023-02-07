---
title: "FFmpeg 编码和编辑入门"
subtitle: ""
summary: "一篇文章搞懂 FFmpeg 的基本原理、入门路线及常用操作。"
date: 2021-06-12T21:23:46+08:00
tags: ['FFmpeg']
categories: ['Media']
featuredImage: https://ahmadawais.com/wp-content/uploads/2021/05/FFmpeg.jpg
featuredImagePreview: https://ahmadawais.com/wp-content/uploads/2021/05/FFmpeg.jpg
draft: false
---

本文主要内容翻译自一篇介绍 FFmpeg 入门知识的英文 Web 幻灯片，加入了一些个人理解与学习注释。

我翻译这篇文章的目的，一是学习 FFmpeg 相关的知识，二是练习英文翻译能力。但是受限于个人水平，有些英文专业名词翻译成中文有些怪异，碰到觉得翻译不好的地方，文中会附上原文词语，建议结合原文更好理解。

原文作者是一名视频质量与体验质量研究员，同时也是 Universität Ilmenau 的一名博士生和助理研究员。原文写得非常棒，文本质量和排版都比我的译文更好，建议有英文基础的朋友直接阅读原文，链接：[FFMPEG ENCODING AND EDITING COURSE](https://slhck.info/ffmpeg-encoding-course)

## 课程目标
- 基本概念
- 安装 ffmpeg 和相关工具
- 编码视频
- 使用过滤器（滤镜）
- 分析视频

## 前置要求
- 这份幻灯片（原文是 web 幻灯片的形式）
- 已安装 ffmpeg、ffprobe、ffplay
- 一些样例视频，比如：[Big Buck Bunny](http://distribution.bbb3d.renderfarming.net/video/mp4/bbb_sunflower_1080p_60fps_normal.mp4)

## 资源
如果你需要一些样例视频用做测试，可以参考 VQEG (Video Quality Experts Group) 的说明：https://www.its.bldrdoc.gov/vqeg/video-datasets-and-organizations.aspx

# FFMPEG 简介

## 关于

- 用于多媒体编辑、转换... 的免费开源软件
- 开始于 2000 年
- 持续维护和开发至今

类似或相关（且有用）的框架：

- [ImageMagick](https://www.imagemagick.org/)
- [MLT Framework](https://www.mltframework.org/)

## 工具
FFmpeg 是一系列软件包和类库的组合，包括：

- 命令行工具：ffmpeg、ffprobe、ffplay
- 类库：libavformat, libavcodec, libavfilter, …

其中类库被许多项目使用（比如 VLC、MLT Framework 等）并且可以在 C/C++ 代码中使用

## 类库

- libavformat：读取和写入容器格式（AVI, MKV, MP4, …）
- libavcodec：读取和写入编解码器（H.264, H.265, VP9, …）
- libavfilter：各种个样的视频和音频过滤器
- 以及其他...

关于如何以编程的方式使用类库的样例：
[http://leixiaohua1020.github.io/#ffmpeg-development-examples](http://leixiaohua1020.github.io/#ffmpeg-development-examples)

以及一些其他有用的（非 FFmpeg 的）类库：

- OpenCV: 面向更多类型的信号处理
- Python: MoviePy, pyav, scikit-video

## 架构

简化的 FFmpeg 整体架构

![image](https://img.lifelog.cool/FFmpeg-Encoding-and-Editing-Course-1.png)

## 安装 / 编译

| 安装方式  | 优势 | 劣势 |
| -------------- | ---------------------------------- | ------------------ |
| 从源码编译 | 可以自定义所有配置，工具，编解码器 | 费时间，升级困难   |
| 下载预编译版本 | 简单且快速 | 不提供所有编解码器，需要手动升级 |
| 包管理器安装 | 简单且快速 | 可能不是最新版本，也不提供所有编解码器 |

- 获取源代码和预编译版本：http://ffmpeg.org/download.html
- Windows 预编译版本：https://ffmpeg.org/download.html#build-windows

## 获取帮助

- 官方文档：https://ffmpeg.org/ffmpeg-all.html
- Wiki 知识库：http://trac.ffmpeg.org/wiki
-  IRC 聊天室：#ffmpeg
-  邮件列表：https://lists.ffmpeg.org/mailman/listinfo/ffmpeg-user/
-  Stack Overflow: https://stackoverflow.com/ and use #ffmpeg
-  Super User: http://superuser.com/ and use #ffmpeg
-  … 或者问我

# 基础视频编码概念

## 容器格式（Container Formats）

容器包含媒体数据。比如：

- MP4: MPEG-4 Part 14 容器，用于 H.264, H.264, AAC 音频, …
- MKV: 可以包含几乎所有媒体编码格式的万能容器
- WebM: MKV 容器的子集，主要用于网络媒体流
- AVI: 传统容器

在 FFmpeg 中查看支持的容器：

```bash
$ ffmpeg -formats
File formats:
 D. = Demuxing supported
 .E = Muxing supported
 --
 D  3dostr          3DO STR
  E 3g2             3GP2 (3GPP2 file format)
  E 3gp             3GP (3GPP file format)
 D  4xm             4X Technologies
  E a64             a64 - video for Commodore 64
 D  aa              Audible AA format files
 ...
```

## 编解码器（Codecs）

- 编解码器 = 编码器 / 解码器
- 如何编码和解码视频、音频...的规范
- 通常不是如何编码 / 压缩数据的规范
- 有时候 “编解码器” 被用于直接指代 编码 / 解码的软件

译者按：Codec 可以理解为编码协议，不同的协议处理数据（音视频）的规则不一样，协议有免费开源的，自然也有商用私有的。

查看 FFmpeg 支持的编解码器：

```bash
$ ffmpeg -codecs
Codecs:
 D..... = Decoding supported
 .E.... = Encoding supported
 ..V... = Video codec
 ..A... = Audio codec
 ..S... = Subtitle codec
 ...I.. = Intra frame-only codec
 ....L. = Lossy compression
 .....S = Lossless compression
 -------
 D.VI.. 012v                 Uncompressed 4:2:2 10-bit
 D.V.L. 4xm                  4X Movie
 D.VI.S 8bps                 QuickTime 8BPS video
 .EVIL. a64_multi            Multicolor charset for Commodore 64 (encoders: a64multi )
 .EVIL. a64_multi5           Multicolor charset for Commodore 64, extended with 5th color (colram) (encoders: a64multi5 )
 D.V..S aasc                 Autodesk RLE
 D.VIL. aic                  Apple Intermediate Codec
```

## 最重要的有损编解码器（Lossy Codecs）

目前主要使用的，由 ITU / ISO 标准化：

- 🎥 H.262 / MPEG-2 Part H: 主要是广播、电视使用，用于向后兼容
- 🎥 H.264 / MPEG-4 Part 10: 当今视频编码器的事实标准（2018年）
- 🎥 H.265 / HEVC / MPEG-H: H.264 的继任者，视频质量提升 50%
- 🔊 MP3 / MPEG-2 Audio Layer III: 曾经是音频编码的事实标准
- 🔊 AAC / ISO/IEC 14496-3:2009: 高级音频编码标准

免授权费（免版权的）主要竞争者：

- 🎥 VP8: 由 Google 提出的开源免费编解码器（如今使用并不多）
- 🎥 VP9: VP8 的继任者，几乎和 H.265 一样好
- 🎥 AV1: VP9 的继任者，声称比 H.265 更好

## 最重要的无损编解码器（Lossless Codecs）

无损编解码器在存档、编辑...领域非常有用
无损编码 = 在较小的文件体积下没有压缩损失

- 🎥 Raw YUV, HuffYUV, FFV1, ffvhuff …
- 🔊 Raw PCM, FLAC, ALAC, …

当然，还有 “视觉无损” 编解码器：

- 🎥 Apple ProRes, Avid DNxHD, JPEG2000, high-quality H.264/H.265, ...
- High bitrate and usually only I-frames（高码率帧内压缩）

## 编码器（Encoders）

- 编码器是输出编码后字节流的实际软件
- 编码器会影响视频的质量和性能，一些编码器会好于另一些（有些免费，有些不免费）

译者按：Encoder 可以理解为 Codec（编码协议）的具体实现方式，不同的实现方式各有优劣，有免费开源的实现，也有商用私有的实现。 

例如：

- 🎥 libx264: 最受欢迎的 H.264 编码器
- 🎥 NVENC: NVIDIA GPU 专用的 H.264 编码器
- 🎥 libx265: 免费开源的 HEVC（H.265）编码器
- 🎥 libvpx: Google 推出的 VP8 和 VP9 编码器
- 🎥 libaom: AV1 编码器
- 🔊 libfdk-aac: AAC 编码器
- 🔊 aac: 原生（默认） FFmpeg AAC 编码器
- …

总的来说，编码器方面有很多竞争。

查看 FFmpeg 支持的编码器：

```bash
$ ffmpeg -encoders
Encoders:
 V..... = Video
 A..... = Audio
 S..... = Subtitle
 .F.... = Frame-level multithreading
 ..S... = Slice-level multithreading
 ...X.. = Codec is experimental
 ....B. = Supports draw_horiz_band
 .....D = Supports direct rendering method 1
 ------
 V..... a64multi             Multicolor charset for Commodore 64 (codec a64_multi)
 V..... a64multi5            Multicolor charset for Commodore 64, extended with 5th color (colram) (codec a64_multi5)
 V..... alias_pix            Alias/Wavefront PIX image
...
```

## 像素格式（Pixel Formats）

- 视频流中原始像素的表示
- 指定亮度 / 色彩和饱和度的采样顺序

![image](https://img.lifelog.cool/FFmpeg-Encoding-and-Editing-Course-2.png)

查看 FFmpeg 支持的像素格式：

```bash
➜  ~ ffmpeg -pix_fmts --hide_banner
Pixel formats:
I.... = Supported Input  format for conversion
.O... = Supported Output format for conversion
..H.. = Hardware accelerated format
...P. = Paletted format
....B = Bitstream format
FLAGS NAME            NB_COMPONENTS BITS_PER_PIXEL BIT_DEPTHS
-----
IO... yuv420p                3             12      8-8-8
IO... yuyv422                3             16      8-8-8
IO... rgb24                  3             24      8-8-8
IO... bgr24                  3             24      8-8-8
IO... yuv422p                3             16      8-8-8
IO... yuv444p                3             24      8-8-8
IO... yuv410p                3              9      8-8-8
IO... yuv411p                3             12      8-8-8
...
```

译者按：这块我也没看明白，先放着吧 😂，我比较关心视频编码（数据压缩）的部分 

# 用 FFmpeg 命令行工具编码

## 基本语法

```bash
ffmpeg <global-options> <input-options> -i <input> <output-options> <output>
```

- 全局参数（global-options）用于日志输出，覆盖文件等
- 输入参数（input-options）用于读取文件
- 输出参数（output-options）用于：
	- 转换（编码格式，视频质量等）
	- 过滤器（filtering）
	- 流映射（stream mapping）
	- ...

完整的帮助 / 参考文档：`ffmpeg -h full` 或者 `man ffmpeg` 但是内容非常非常多！

## 转码和转换（TRANSCODING AND TRANSMUXING）

从一种编码格式转码到另一种编码格式（Transcoding），比如用 libx264 编码器转为 H.264 编码格式

```bash
ffmpeg -i <input> -c:v libx264 output.mp4
```

从一种视频容器转换为另一种视频容器，不用重新编码

```bash
ffmpeg -i input.mp4 -c copy output.mkv
```

ffmpeng 会从输入中获取一个视频、音频和字幕流并将其映射到输出文件中。

注释：

- -c 设置编码器（参考 ffmpeg -encoders)
- -c 复制比特流（copy only copies bitstream）
- -c:v 设置视频编码器
- -c:a 设置音频编码器
- -an 和 -vn 分别是禁用音频和视频流

### 转码的背后（TRANSCODING BACKGROUND）

节选自 [http://ffmpeg.org/ffmpeg-all.html](http://ffmpeg.org/ffmpeg-all.html)：
> ffmpeg […] read[s] input files and get packets containing encoded data from them. When there are multiple input files, ffmpeg tries to keep them synchronized […]. 
> Encoded packets are then passed to the decoder. […] The decoder produces uncompressed frames […] which can be processed further by filtering […]. After filtering, the frames are passed to the encoder, which encodes them and outputs encoded packets. Finally those are passed to the muxer, which writes the encoded packets to the output file.

译者按：这段专业名词太多，尝试翻译了几次都感觉太僵硬太奇怪了。其实就是前面那个架构图的文字描述。

大概意思是：ffmpeg 从文件中读取编码过的数据包，如果有多个文件，ffmpeg 会尝试同步多个文件。然后这些编码过的数据包（encoded packets）会被发送到解码器（decoder），解码器将这些数据包解码为原始视频帧（uncompressed frames），然后就可以使用各种过滤器（filters）来处理这些视频帧（frames），然后这些处理过后的视频帧率会被发送到编码器（encoder），编码器会将他们编码为数据包（encoded packets），最后这些数据包会被发送到混流器（muxer），最终混流器将这些数据包输出为文件。

## 查找和剪切（SEEKING AND CUTTING）

从 <起始点（start）> 用 <持续时间（duration）> 或者 <结束点（end）> 来剪切视频

```bash
ffmpeg -ss <start> -i <input> -t <duration> -c copy <output>
ffmpeg -ss <start> -i <input> -to <end> -c copy <output>
```

例如：

```bash
ffmpeg -ss 00:01:50 -i <input> -t 10.5 -c copy <output>
ffmpeg -ss 2.5 -i <input> -to 10 -c copy <output>
```

### 使用 SEEKNG 注意事项

- 重新编码视频时，seeking 总是可以精确到时间戳
- 当拷贝视频流而没有经过重新编码时（-c copy），ffmpeg 可能会包含未显示但是必要的帧
- 使用 `-c copy` 剪切视频时，可能会产生黑帧开头的视频（在某些播放器上）
- 详情可以查看：
	- [http://trac.ffmpeg.org/wiki/Seeking](http://trac.ffmpeg.org/wiki/Seeking)
	- [https://superuser.com/questions/138331/using-ffmpeg-to-cut-up-video](https://superuser.com/questions/138331/using-ffmpeg-to-cut-up-video)

译者按：之所以在剪切视频时，转码于不转码存在时间准确性上的差别，是因为转码时，原视频的每一帧都会被 ffmpeg 读取出来（也就是 decode），因此可以准确的定位到时间点，但是如果没有转码，直接复制视频流的时候，ffmpeg 并不会逐帧分析视频，而是采用“关键帧”来定位时间，当设定的时间点上没有关键帧时，就会出现一些时间偏差。

## 设置质量

- 输出文件的质量取决于编码器默认设置于原材料（也就是原视频）的质量
- 不要在未设置任何质量水平的情况下进行编码
- 通常情况下，你需要选择一个目标比特率或者质量水平
- 目标比特率取决于视频的类型，大小和帧率

### 设置质量的参数

可能会使用到的参数（仅示例）

- -b:v 或者 -b:a 设置比特率
	- 比如：-b:v 1000K = 1000kbit/s, -b:v 8M = 8Mbit/s
- q:v 或者 -q:a 设置固定质量参数
	- 比如：-q:a 2 设置为原生 AAC 编码器

一些编码方式的示例：

- -crf 用来设置 libx264/libx265 的 恒定码率因子（[Constant Rate Factor](http://slhck.info/video/2017/02/24/crf-guide.html)）
- vbr 为 FDK-AAC 编码器设置固定质量
- 还有很多其他参数，可以使用 `ffmpeg -h encoder=libx264 for examples` 查看，如下

```bash
➜  ~ ffmpeg -h encoder=libx264 for examples -hide_banner
Encoder libx264 [libx264 H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10]:
    General capabilities: dr1 delay threads 
    Threading capabilities: other
    Supported pixel formats: yuv420p yuvj420p yuv422p yuvj422p yuv444p yuvj444p nv12 nv16 nv21 yuv420p10le yuv422p10le yuv444p10le nv20le gray gray10le
libx264 AVOptions:
  -preset            <string>     E..V....... Set the encoding preset (cf. x264 --fullhelp) (default "medium")
  -tune              <string>     E..V....... Tune the encoding params (cf. x264 --fullhelp)
  -profile           <string>     E..V....... Set profile restrictions (cf. x264 --fullhelp) 
  -fastfirstpass     <boolean>    E..V....... Use fast settings when encoding first pass (default true)
  -level             <string>     E..V....... Specify level (as defined by Annex A)
  -passlogfile       <string>     E..V....... Filename for 2 pass stats
  -wpredp            <string>     E..V....... Weighted prediction for P-frames
  -a53cc             <boolean>    E..V....... Use A53 Closed Captions (if available) (default true)
  -x264opts          <string>     E..V....... x264 options
  -crf               <float>      E..V....... Select the quality for constant quality mode (from -1 to FLT_MAX) (default -1)
  -crf_max           <float>      E..V....... In CRF mode, prevents VBV from lowering quality beyond this point. (from -1 to FLT_MAX) (default -1)
  -qp                <int>        E..V....... Constant quantization parameter rate control method (from -1 to INT_MAX) (default -1)
  -aq-mode           <int>        E..V....... AQ method (from -1 to INT_MAX) (default -1)
...
```

### 什么是 CRF？

- CRF = [Constant Rate Factor](http://slhck.info/video/2017/02/24/crf-guide.html)
- 在整个编码过程中，保持恒定的质量
- 在不太注重文件大小的情况下，适合以固定的质量存储视频

### 示例：转码为 H.264，Part 1

以固定质量（CRF）编码：

```bash
ffmpeg -i <input> -c:v libx264 -crf 23 -c:a aac -b:a 128k output.mkv
```

针对 H.264 编码，CRF 在 18 到 28 之间看起来“比较好”，CRF 值越低视频质量越好。（ HEVC 和 VP9 编码的 CRF 值不同）

### 示例：转码为 H.264，Part 2

双重编码（Two-pass）模式：

```bash
ffmpeg -y -i <input> -c:v libx264 -b:v 8M -pass 1 -c:a aac -b:a 128k -f mp4 /dev/null
ffmpeg -i <input> -c:v libx264 -b:v 8M -pass 2 -c:a aac -b:a 128k output.mp4
```

Windows 用户：用 `nul` 代替 `/dev/null`

更多细节，参考：[https://trac.ffmpeg.org/wiki/Encode/H.264](https://trac.ffmpeg.org/wiki/Encode/H.264)

## 码率控制

不同的控制方式：

- 固定码率（Constant Bitrate：CBR）
- 动态码率（Variable Bitrate：VBR）
	- 平均码率（Average Bitrate：ABR）
	- 恒定质量参数（Constant quantization parameter：CQP）
	- 基于心里视觉特征的恒定质量，比如 x264/x265/libvpx-vp9 编码器中使用 CRF（Constant quality, based on psychovisual properties）
	- 限制型码率（Constrained Bitrate：VBV）

具体在哪种情况下使用哪种码率模式，参考：[https://slhck.info/video/2017/03/01/rate-control.html](https://slhck.info/video/2017/03/01/rate-control.html)

**重要说明：合适的码率取决于内容特征！**

译者按：对视频来说，码率并非越高越好，不同内容特征的视频对码率的要求并不一样，具体可以参考这篇文章：[什么决定了你看到的画质](https://sspai.com/post/66001)

### 码率控制示例

![image](https://img.lifelog.cool/FFmpeg-Encoding-and-Editing-Course-3.png)

- 不同码率控制模式下，帧大小随时间变化图（已平滑化处理）
- 注意 ABR 模式下，在一开始对码率的错误估计

### 速度 VS 质量 VS 文件大小（SPEED VS. QUALITY VS. FILE SIZE）

有损编码模式总是在这三者之间选择

![image](https://img.lifelog.cool/FFmpeg-Encoding-and-Editing-Course-4.png)

例如：

- 你可以让视频质量又好，编码又快，但是输出文件会很大
- 你可以让视频质量又好，文件又小，但是编码速度会很慢
- 你可以让文件又小，编码又快，但是视频质量就会比较差

编者按：凡事需要做抉择的地方，不可能三角总在那儿等着你。视频编码时如何平衡速度、质量和文件大小，是最重要也是最复杂的课题之一，后面会学习/撰写/翻译更多的文章来介绍。

### X264 编码器中的速度/质量预设

使用预设来选择 libx264 的编码速度

```bash
ffmpeg -i <input> -c:v libx264 -crf 23 -preset ultrafast -an output.mkv
ffmpeg -i <input> -c:v libx264 -crf 23 -preset medium -an output.mkv
ffmpeg -i <input> -c:v libx264 -crf 23 -preset veryslow -an output.mkv
```

所有预设：ultrafast, superfast, veryfast, faster, fast, medium, slow, slower, veryslow

同等质量下，一些编码速度于文件体积的对比样例：

| Preset   | Encoding Time | File Size |
| -------- | ------------- | --------- |
| ultrafast | 4.85s | 15M  |
| medium | 24.13s | 5.2M  |
| veryslow | 112.23s | 4.9M  | 	
		
## 改变帧率

简单的通过丢弃帧和复制帧的方式来改变帧率：

```bash
ffmpeg -i <input> -r 24 <output>
```

更复杂的方法涉及到过滤器（filter）参考 fps, mpdecimate, minterpolate filters:

```bash
ffmpeg -i <input> -filter:v fps=24 <output>
```

## 轨道映射（STREAM MAPPING）

每一个文件都有一个唯一的 ID，从 0 开始

例如：

- `0:0` 是第一个输入文件的第一条轨道（Steam）
- `0:1` 是第一个输入文件的第二条轨道
- `2:a:0` 是第三个输入文件的第一条音轨
- ...

你可以将输入文件的轨道映射到输出文件中（map input streams to output）例如：将音频添加到视频中：

```bash
ffmpeg -i input.mp4 -i input.m4a -c copy -map 0:v:0 -map 1:a:0 output.mp4
```

更多细节请参考：[http://trac.ffmpeg.org/wiki/Map](http://trac.ffmpeg.org/wiki/Map)

## 简单过滤器（SIMPLE FILTERING）

FFmpeg 有很多视频、音频、字幕的过滤器（filter）：

```bash
ffmpeg -i <input> -filter:v "<filter1>,<filter2>,<filter3>" <output>
```

一个过滤器（滤镜）有一个名称，一些可选项，以及一些预设参数：

```bash
-filter:v <name>=<option1>=<value1>:<option2>=<value2>
```

### 缩放（SCALING）

缩放到 320 x 240

```bash
ffmpeg -i <input> -vf "scale=w=320:h=240" <output>
```

缩放到高度 240 且保持纵横比可被 2 整除

```bash
ffmpeg -i <input> -vf scale=w=-2:h=240 <output>
```

缩放到 1280 x 720，如果需要的话缩放到更小

```bash
ffmpeg -i <input> -vf "scale=1280:720:force_original_aspect_ratio=decrease" <output>
```

更多关于缩放的技巧：


- [http://trac.ffmpeg.org/wiki/Scaling%20(resizing)%20with%20ffmpeg](http://trac.ffmpeg.org/wiki/Scaling%20(resizing)%20with%20ffmpeg)
- [https://superuser.com/questions/547296/](https://superuser.com/questions/547296/)

### 填充（PADDING）

填充黑边到文件中，例如将 1920 x 800 的视频填充到 1920 x 1080：

```bash
ffmpeg -i <input> -vf "pad=1920:1080:(ow-iw)/2:(oh-ih)/2" <output>
```

![image](https://img.lifelog.cool/FFmpeg-Encoding-and-Editing-Course-5.png)

使用备注：

- 可以使用数学表达式
- ow 和 oh 是输出文件的宽和高
- iw 和 ih 是输入文件的宽和高

### 淡入淡出（FADING）

在特定的时间点使用指定持续时间的简单淡入淡出效果：

```bash
ffmpeg -i <input> -filter:v \
  "fade=t=in:st=0:d=5,fade=t=out:st=30:d=5" \
  <output>
```

译者按：使用 FFmpeg 命令行时，有时一行代码太长不方便编写和查看，就可以使用换行符分行编写，不同的 Shell 有不同的换行符，例如 “\” 是 Bash 的换行符，Windows 下 CMD 的换行符是 “^”，请注意替换。

### 文本绘图 / 文字水印（DRAWING TEXT）

用于在视频上加上文本的复杂系统：

```bash
ffmpeg -i <input> -vf \
drawtext="text='Test Text':x=100:y=50:\
fontsize=24:fontcolor=yellow:box=1:boxcolor=red" \
<output>
```

- 字体、大小、位置、颜色...都有都有各种相关的参数
- 文字烧录功能可以拓展，例如按帧数或者时间码烧录

更多详细信息，参考：[http://ffmpeg.org/ffmpeg-all.html#drawtext-1](http://ffmpeg.org/ffmpeg-all.html#drawtext-1)

## 复杂过滤器（COMPLEX FILTERING）

复杂过滤器不止一个输入或者输出：

```bash
ffmpeg -i <input1> -i <input2> -filter_complex \
    "[0:v:0][1:v:0]overlay[outv]" \
    -map "[outv]" <output>
```

过程拆解：

- 指定输入到过滤链（filterchain）（例如 [0:v:0][1:v:0]）
- 指定过滤链中的滤镜（例如：overlay）
- 指定过滤链的输出标签（例如：[outv]）
- 映射输出标签到输出文件
- 使用 ; 一次可以同时使用多个过滤链

更多详细内容，参考：[http://ffmpeg.org/ffmpeg-all.html#Filtergraph-syntax-1](http://ffmpeg.org/ffmpeg-all.html#Filtergraph-syntax-1)

### 拼接轨道（CONCATENATING STREAMS）

解码三个视频/音频轨道并拼接到另一个中：

![image](https://img.lifelog.cool/FFmpeg-Encoding-and-Editing-Course-6.png)

```bash
ffmpeg -i <input1> -i <input2> -i <input3> -filter_complex \
    "[0:0][0:1][1:0][1:1][2:0][2:1]concat=n=3:v=1:a=1[outv][outa]" \
    -map "[outv]" -map "[outa]" <output>
```

更多信息，参考：[http://trac.ffmpeg.org/wiki/Concatenate](http://trac.ffmpeg.org/wiki/Concatenate)（也适用于其他方法）

### 时间线编辑（TIMELINE EDITING）

仅在特定时间点启用过滤器

例如：

- 在左上角显示水印
- 仅在第 1 和第 2 秒显示

```bash
ffmpeg -i <video> -i <watermark> -filter_complex \
    "[0:v][1:v]overlay=10:10:enable='between(t,1,2)'[outv]" \
    -map "[outv]" <output>
```

更多用法，请参考：[http://ffmpeg.org/ffmpeg-all.html#Timeline-editing](http://ffmpeg.org/ffmpeg-all.html#Timeline-editing)

## 其他过滤器

除了前面介绍的那些过滤器，ffmpeg 生态中还有很多很多其他的过滤器。

例如：

- 使用选择过滤器检测场景变化
- 去除水印（delogo）
- 模糊、边缘检测和卷积滤波器
- 视频稳定
- 矢量示波器、直方图和其他信息
- 色度和 alpha 控制
- 字幕编辑
- ...

## 计算简单的质量指标

### PSNR (Peak Signal To Noise Ratio)

```bash
$ ffmpeg -i <degraded> -i <reference> -filter_complex psnr -f null /dev/null
[Parsed_psnr_0 @ 0x7fdb187045c0] PSNR y:33.437789 u:39.814416 v:39.319141 average:34.698320 min:29.305186 max:inf
```
译者按：PSNR 中文标准译名为“峰值信噪比”，是一个比较成熟的衡量压缩影像信噪比的指标，详情可以参考 [中文维基百科：峰值信噪比](https://zh.wikipedia.org/zh-cn/%E5%B3%B0%E5%80%BC%E4%BF%A1%E5%99%AA%E6%AF%94)

### SSIM ([Structural Similarity](https://en.wikipedia.org/wiki/SSIM))

```bash
$ ffmpeg -i <degraded> -i <reference> -filter_complex ssim -f null /dev/null
[Parsed_ssim_0 @ 0x7fbf0500b660] SSIM Y:0.925477 (11.277116) U:0.948906 (12.916325) V:0.946795 (12.740513) All:0.932935 (11.735054)
```

译者按：SSIM 中文一般译为“结构相似性”，是衡量两幅图像相似度的指标，其详细的科普和使用可以参考这篇文章：[使用SSIM量化评价视频编码质量](https://www.bilibili.com/read/cv7974104/)

译者按：除了原作者列出的 PSNR 和 SSIM 两个指标外，还有一个 Netflix 和 南加州大学联合开发的 [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion) （Video Multimethod Assessment Fusion）是目前 Netflix 主要使用的指标，详情还是可以参考前文提到的这篇文章：[什么决定了你看到的画质](https://sspai.com/post/66001)

# 使用 ffprobe 获取媒体信息

## 基本概念

基础语法：

```bash
ffprobe <input>
    [-select_streams <selection>]
    [-show_streams|-show_format|-show_frames|-show_packets]
    [-show_entries <entries>]
    [-of <output-format>]
```

解释：

- select_streams 用于指定选择视频或者音频
- show_ 用于选择显示哪种类型的信息
- show_entries 用于选择显示更少的信息
- of 用于设置输出格式

更多信息，参考：

- [https://ffmpeg.org/ffprobe.html](https://ffmpeg.org/ffprobe.html)
- [http://trac.ffmpeg.org/wiki/FFprobeTips](http://trac.ffmpeg.org/wiki/FFprobeTips)

## 实际 ffpobe 案例 Part 1

显示所有的流：

```bash
ffprobe <input> -show_streams
```

显示视频流信息：

```bash
ffprobe <input> -select_streams v -show_format
```

Show presentation timestamp and frame type of every frame, in CSV format (p=0 disables CSV section headers):

```bash
ffprobe <input> -select_streams v -show_frames \
-show_entries frame=pkt_pts_time,pict_type -of csv=p=0
```

译者按：第三个案例不太懂，我自己试了试，效果也有点奇怪，先放着吧 😂

## 实际 ffpobe 案例 Part 2

更改输出为 JSON 格式：

```bash
ffprobe <input> -select_streams v -show_packets -of json
```

获取文件中流的编号（nk = 1 禁用 keys）

```bash
ffprobe <input> -show_format -show_entries format=nb_streams -of compact=nk=1:p=0
```

以秒或 HH:MM:SS.ms 为单位获取视频长度

```bash
ffprobe <input> -show_format -show_entries format=duration -of compact=nk=1:p=0
ffprobe -sexagesimal <input> -show_format -show_entries format=duration -of compact=nk=1:p=0
```

以 Bit/s 为单位获取音频流

```bash
ffprobe <input> -select_streams a -show_entries stream=bit_rate -of compact=nk=1:p=0
```

译者按：其实这一章节的案例我个人觉得没有很大作用，平时查看媒体文件的信息基本上使用 `ffprobe <input> -hide_banner` 就够了。

# 检查视频编解码器

## 调试运动向量

在 FFmpeg 中使用 H.264 编解码器可视化运动向量的简单方法（不适用于其他编解码器）

```bash
ffplay -flags2 +export_mvs input.mp4 -vf codecview=mv=pf+bf+bb
ffmpeg -flags2 +export_mvs -i input.mp4 -vf codecview=mv=pf+bf+bb <output>
```

- pf – P 帧的向前预测运动向量
- bf – B 帧的向前预测运动向量
- bb – B 帧的向后预测运动向量

![image](https://img.lifelog.cool/FFmpeg-Encoding-and-Editing-Course-7.png)

跟多参考：[http://trac.ffmpeg.org/wiki/Debug/MacroblocksAndMotionVectors](http://trac.ffmpeg.org/wiki/Debug/MacroblocksAndMotionVectors)

译者按：运动向量这方面的知识点我也不懂，来日再补充吧 😂

## 视频流分析

图形化方式分析视频流的其他软件：

- [Elecard Stream Analyzer](https://www.elecard.com/products/video-analysis/stream-analyzer) (商业)
- [CodecVisa](http://www.codecian.com/) (商业)
- [Intel Video Pro Analyzer](https://software.intel.com/en-us/intel-video-pro-analyzer) (商业)
- [AOMAnalyzer](https://people.xiph.org/~mbebenita/analyzer/) (免费，针对 AV1/VP9 编码的视频)

# 总结

通过这节课程，你应该学到了以下知识点：

- 理解 FFmpeg 库，编解码器，容器，编码器...
- 编码视频和音频
- 使用基础的过滤器
- 读取流信息和元数据
- 如果你遇到了困难，会寻找帮助