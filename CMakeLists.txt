cmake_minimum_required(VERSION 3.10)
project(AudioVideoPlayer)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 查找依赖包
find_package(PkgConfig REQUIRED)

# FFmpeg 库
pkg_check_modules(AVFORMAT REQUIRED libavformat)
pkg_check_modules(AVCODEC REQUIRED libavcodec)
pkg_check_modules(AVUTIL REQUIRED libavutil)
pkg_check_modules(SWRESAMPLE REQUIRED libswresample)
pkg_check_modules(SWSCALE REQUIRED libswscale)
pkg_check_modules(AVFILTER REQUIRED libavfilter)

# SDL2 库
pkg_check_modules(SDL2 REQUIRED sdl2)

# 添加可执行文件
add_executable(${PROJECT_NAME} Audio_Video_Player.cpp)

# 链接库
target_link_libraries(${PROJECT_NAME}
    ${AVFORMAT_LIBRARIES}
    ${AVCODEC_LIBRARIES}
    ${AVUTIL_LIBRARIES}
    ${SWRESAMPLE_LIBRARIES}
    ${SWSCALE_LIBRARIES}
    ${AVFILTER_LIBRARIES}
    ${SDL2_LIBRARIES}
    pthread
    m
)

# 包含目录
target_include_directories(${PROJECT_NAME} PRIVATE
    ${AVFORMAT_INCLUDE_DIRS}
    ${AVCODEC_INCLUDE_DIRS}
    ${AVUTIL_INCLUDE_DIRS}
    ${SWRESAMPLE_INCLUDE_DIRS}
    ${SWSCALE_INCLUDE_DIRS}
    ${AVFILTER_INCLUDE_DIRS}
    ${SDL2_INCLUDE_DIRS}
)

# 编译选项
target_compile_options(${PROJECT_NAME} PRIVATE -Wall -Wextra -Wno-deprecated-declarations)