<div align="right">
  <a href="README.md">中文</a>
</div>

<div align="center">

# FFmpeg7-SDL2 Linux Version Adapted Player

![FFmpeg](https://img.shields.io/badge/FFmpeg-7.0.2-green)
![SDL2](https://img.shields.io/badge/SDL2-2.30.6-blue)
![Platform](https://img.shields.io/badge/Platform-Linux-lightgrey)

</div>

This is a cross-platform audio/video player developed based on FFmpeg and SDL2, supporting Windows and Linux/WSL environments (for the Windows version, see my other repository: https://github.com/jerryyang1208/FFmpeg7-SDL2_Practice ). This project includes two main versions: the software decoding version (Audio_Video_Player.cpp) and the hardware acceleration version (HW_Audio_Video_Player.cpp) supporting VAAPI.

<pre>
ffmpeg+SDL2/
├── .vscode/                      # VS Code configuration files
│   ├── c_cpp_properties.json     # C/C++ plugin configuration
│   ├── launch.json               # Debug configuration
│   ├── settings.json             # Editor settings
│   └── tasks.json                # Build task configuration
├── Audio_Video_Player.cpp        # Software decoding version (Linux/WSL)
├── CMakeLists.txt                # CMake build configuration
├── .gitignore                    # Git ignore file
├── README.md                     # Project documentation (Chinese)
└── README_en.md                  # Project documentation (English)
</pre>

## ✨ Key Features

- **Multi-format Support**: Based on FFmpeg, supports almost all common audio/video formats (MP4, MKV, AVI, FLV, MOV, etc.)
- **A/V Synchronization**: Uses audio clock as master for precise audio/video sync
- **Auto Rotation**: Detects video rotation metadata and automatically applies transpose filters to correct orientation
- **Hardware Acceleration (Optional)**: Linux version supports VAAPI hardware decoding, significantly reducing CPU usage
- **Intelligent Fallback**: Automatically falls back to software decoding when hardware acceleration is unavailable, ensuring continuous playback
- **Window Adaptive**: Supports window resizing while maintaining original aspect ratio with automatic centering
- **Playback Progress Display**: Real-time display of current playback progress/total duration
- **User-friendly File Selection**: Supports command-line path input with `~/` and `${VAR}` expansion

## 🚀 Build & Run

### Required Dependencies

- FFmpeg (libavformat, libavcodec, libavutil, libswresample, libswscale, libavfilter)
- SDL2
- pkg-config
- C++11 compatible compiler (g++ / clang)

### Install Dependencies
<pre>
# Basic build tools
sudo apt update
sudo apt install build-essential cmake pkg-config

# FFmpeg development libraries
sudo apt install libavcodec-dev libavformat-dev libavutil-dev \
                 libswresample-dev libswscale-dev libavfilter-dev

# SDL2 development library
sudo apt install libsdl2-dev

# Hardware acceleration support (optional, for VAAPI)
sudo apt install libva-dev libva-drm2 libva-x11-2
</pre>

### Build and Run with CMake
Download/clone the complete code files from the GitHub repository, modify the path configurations in CMakeLists.txt and files inside the `.vscode` folder, then press F5 directly or follow these steps to build, compile, and run in the project folder:

<pre>
# Create build directory
mkdir build && cd build

# Configure and compile
cmake ..
make -j$(nproc)

# Run the software version
./AudioVideoPlayer
</pre>

At this point, the terminal will output: **Please enter the video file path (supports ~/ and ${VAR} expansion):** . You can then enter an absolute path or a relative path starting from `~`, and playback will begin normally.
- The player window will open automatically and start playing
- Close the window or wait for playback to finish to exit
- The terminal will display real-time playback progress: `Progress`

## 📖 Hardware Acceleration Version

In reality, WSL (whether WSL1 or WSL2) itself does not support direct access to the physical GPU's hardware acceleration decoding capabilities. This is because:
- WSL2 runs in a lightweight virtual machine; although it supports GPU computing (e.g., CUDA, OpenCL), hardware-accelerated video encoding/decoding requires access to underlying DRM/DRI device nodes.
- VAAPI requires direct operation on `/dev/dri/renderD*` devices, which are not available by default in WSL.
- The latest WSLg supports GUI acceleration, but it's primarily for OpenGL rendering, not video decoding, and is not applicable.
- Upgrading to Linux and compiling a custom kernel could enable DRM support, but it's extremely complex and completely unnecessary.

However, a hardware acceleration version was still written as a practice exercise and can be used in native Linux environments. The C++ code file is tagged, and the overall hardware integration approach is as follows:

<pre>
Attempt to enable VAAPI during video decoder initialization.
If it fails, cleanly fall back to software decoding, restoring the decoder context to its original state to ensure subsequent playback is normal.
When hardware acceleration is successfully enabled, the decoding thread correctly handles hardware frames (transfers them to software frames, then passes them to filters/rendering).
Keep all original features (filter rotation, A/V sync, window resizing, etc.) unchanged.
</pre>

## 📄 License

This project is for learning and exchange purposes only. FFmpeg and SDL2 are subject to their respective open-source licenses.

## 👤 Author

- GitHub: [@jerryyang1208](https://github.com/jerryyang1208)
- Zhihu Blog：https://www.zhihu.com/people/13-73-62-89-19

## ⭐ Acknowledgements

- [FFmpeg](https://ffmpeg.org/) Community
- [SDL](https://www.libsdl.org/) Development Team
- [WSL](https://ubuntu.com/blog/tag/ubuntu-24-04-lts) Community