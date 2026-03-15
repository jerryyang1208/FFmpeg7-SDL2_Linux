<div align="right">
  <a href="README_en.md">English</a>
</div>

<div align="center">

# FFmpeg7-SDL2 Linux 版本适配播放器

![FFmpeg](https://img.shields.io/badge/FFmpeg-7.0.2-green)
![SDL2](https://img.shields.io/badge/SDL2-2.30.6-blue)
![Platform](https://img.shields.io/badge/Platform-Linux-lightgrey)

</div>

这是一个基于 FFmpeg 和 SDL2 开发的跨平台音视频播放器，支持 Windows 和 Linux/WSL 环境（Windows 版本见本人另一个代码仓库 https://github.com/jerryyang1208/FFmpeg7-SDL2_Practice ）。本项目包含两个主要版本：软件解码版 Audio_Video_Player.cpp 和硬件加速版（支持 VAAPI）HW_Audio_Video_Player.cpp。


<pre>
ffmpeg+SDL2/
├── .vscode/                      # VS Code 配置文件
│   ├── c_cpp_properties.json     # C/C++ 插件配置
│   ├── launch.json               # 调试配置
│   ├── settings.json             # 编辑器设置
│   └── tasks.json                # 构建任务配置
├── Audio_Video_Player.cpp        # 软件解码版本（Linux/WSL）
├── CMakeLists.txt                # CMake 构建配置
├── .gitignore                    # Git 忽略文件
├── README.md                     # 项目说明
└── README_en.md                  # 项目说明英文版
</pre>

## ✨ 主要特性

- 多格式支持：基于 FFmpeg，支持几乎所有常见音视频格式（MP4, MKV, AVI, FLV, MOV 等）
- 音视频同步：以音频时钟为主，实现精确的音视频同步播放
- 自动旋转：支持视频旋转角度检测，自动应用 transpose 滤镜纠正方向
- 硬件加速（可选）：Linux 版本支持 VAAPI 硬件解码，大幅降低 CPU 占用
- 智能回退：硬件加速不可用时自动回退到软件解码，保证播放连续性
- 窗口自适应：支持窗口大小调整，保持视频原始宽高比，自动居中显示
- 播放进度显示：实时显示当前播放进度/总时长
- 友好的文件选择：支持命令行输入路径，支持 ~/ 和 ${VAR} 路径展开

## 🚀 编译运行

### 需要的依赖库

- FFmpeg (libavformat, libavcodec, libavutil, libswresample, libswscale, libavfilter)
- SDL2
- pkg-config
- C++11 兼容的编译器（g++ / clang）

### 安装依赖
<pre>
# 基础构建工具
sudo apt update
sudo apt install build-essential cmake pkg-config

# FFmpeg 开发库
sudo apt install libavcodec-dev libavformat-dev libavutil-dev \
                 libswresample-dev libswscale-dev libavfilter-dev

# SDL2 开发库
sudo apt install libsdl2-dev

# 硬件加速支持（可选，用于 VAAPI）
sudo apt install libva-dev libva-drm2 libva-x11-2
</pre>

### 使用 CMake 构建编译运行
从 Github 仓库下载拉取完整代码文件，修改 CMakeLists.txt 和 .vscode 内文件的路径配置后，直接 F5 或者按以下步骤在项目文件夹下构建编译运行：

<pre>
# 创建构建目录
mkdir build && cd build

# 配置并编译
cmake ..
make -j$(nproc)

# 运行软件版本
./AudioVideoPlayer
</pre>

此时终端会输出：**请输入视频文件路径 (支持 ~/ 和 ${VAR} 展开):** ，这时我们可以输入绝对路径或者从 ~ 开始的相对路径等均可，然后便可正常播放。
- 播放器窗口会自动打开，开始播放
- 关闭窗口或等待播放结束即可退出
- 终端会实时显示播放进度 Progress

## 📖 硬件加速版本

实际上 WSL（无论是 WSL1 还是 WSL2）本身不支持直接访问物理 GPU 的硬件加速解码功能。因为：
- WSL2 运行在轻量级虚拟机中，虽然支持 GPU 计算（如 CUDA、OpenCL），但视频编解码硬件加速需要底层的 DRM/DRI 设备节点访问。
- VAAPI 需要直接操作 /dev/dri/renderD* 设备，这些在 WSL 中默认不可用。
- 最新 WSLg 支持 GUI 加速，但主要是 OpenGL 渲染，不是视频解码，也不适用。
- 升级到 Linux 编译自定义内核可以启用 DRM 支持，但极其复杂完全没必要。

但这里仍撰写了硬件加速版本当作练习，且可以尝试在原生 Linux 环境下运行使用，cpp 代码文件放 tag 里，然后是整体硬件引入思路：

<pre>
在视频解码器初始化时尝试启用 VAAPI。
如果失败，干净地回退到软件解码，恢复解码器上下文为原始状态，确保后续播放正常。
成功启用硬件加速时，解码线程正确处理硬件帧（转移到软件帧，然后交给滤镜/渲染）。
保持原有功能（滤镜旋转、音视频同步、窗口大小适应等）不变。
</pre>

## 📄 许可证

本项目仅供学习交流使用，FFmpeg 和 SDL2 遵循其各自的开源许可证。

## 👤 作者

- GitHub: [@jerryyang1208](https://github.com/jerryyang1208)
- 知乎博客：https://www.zhihu.com/people/13-73-62-89-19

## ⭐ 致谢

- [FFmpeg](https://ffmpeg.org/) 社区
- [SDL](https://www.libsdl.org/) 开发团队
- [WSL](https://ubuntu.com/blog/tag/ubuntu-24-04-lts) 社区