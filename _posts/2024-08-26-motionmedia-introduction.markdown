---
layout: post
title: MotionMedia - An Introduction
categories: motionmedia devlog
banner:
    image: "/assets/images/banners/motion-media-banner.jpeg"
    opacity: 1
    background: "#000"
    height: "100vh"
    min_height: "38vh"
    heading_style: "font-size: 4.25em; font-weight:bold;"
    subheading_style: "color: white"
tags: motionmedia introduction
top: 1
sidebar: []
---

## What is MotionMedia?

MotionMedia is a command line application made with C++ for the Raspberry Pi. The program will use the connected camera module to detect
people and a screen to display a video if there are people within the camera's field of view. There's isn't much yet but I hope I will be able to
present a demo soon!

Adding OpenCV to the project was incredibly painful because I was getting a weird string error when I was trying to build the application.
There were not any problems with the library build which was very weird. In the end, I removed OpenCV from the submodule list
and I installed it with Homebrew instead. Now I can build the application with OpenCV included using CMake.

```cmake
cmake_minimum_required(VERSION 3.27)
project("MotionMedia" LANGUAGES CXX VERSION 0.1 DESCRIPTION "MotionMedia is a computer vision based app that displays certain media based on detection")
set(CMAKE_CXX_STANDARD 20)
# add extra include directories
include_directories(/usr/local/Cellar/opencv/4.10.0_3/include/opencv4)

# find opencv
set(OpenCV_DIR "/usr/local/Cellar/opencv/4.10.0_3/lib/cmake/opencv4")
find_package(OpenCV REQUIRED)

# Build
add_executable(${PROJECT_NAME} ./src/main.cpp)

# Add opencv to linker this is for the homebrew version. I have been unable to 
# use it as a submodule due to a weird error with the libjpeg-turbo.
# It might fixed in the future.
target_link_libraries(${PROJECT_NAME} ${OPENCV_LIBS})
```