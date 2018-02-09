---
title: TX2 Tricks
date: 2018-02-09 10:48:07
tags:
categories: Trick
---

# Neon accelerations

The flag '-mfpu=neon' won't help, Below should be optimal options and which also will make the compiler use Advanced SIMD (aka NEON) automatically.

`-O3 -ffast-math -flto -march=armv8-a+crypto -mcpu=cortex-a57+crypto`

---

# OpenCV 3.4

When building opencv3.4 on tx2, I got an error message. [1](https://github.com/opencv/opencv/issues/5205) [2](http://answers.opencv.org/question/54154/error-building-opencv-300-beta-on-jetson-tk1/):
`#error Please include the appropriate gl headers before including cuda_gl_interop.h `

Many people suffer from this error, some of them disable opengl support but I don't like to drop it. Finally, I amend cuda header to avoid this error assert.
Run `sudo vim /usr/local/cuda-8.0/include/cuda_gl_interop.h` and change to:

    /* 
    #if defined(__arm__) || defined(__aarch64__) 
    #ifndef GL_VERSION 
    #error Please include the appropriate gl headers before including cuda_gl_interop.h 
    #endif 
    #else 
    #include <GL/gl.h> 
    #endif 
    */  
    #include <GL/gl.h>  

Then another error occured:

`/usr/lib/gcc/aarch64-linux-gnu/5/../../../aarch64-linux-gnu/libGL.so: undefined reference to `drmCloseOnce` `

Inspect this file 
`lrwxrwxrwx 1 root root 13 Jan 15 19:46 libGL.so -> mesa/libGL.so`

Replace with [tegra/libGL.so](https://devtalk.nvidia.com/default/topic/946136/building-an-opengl-application/)

`sudo rm libGL.so && sudo ln -s /usr/lib/aarch64-linux-gnu/tegra/libGL.so libGL.so`

---

# XboxDrv

Run in background as daemon
`sudo xboxdrv -D --detach`

OR

Compile it as kernel module see below.

---

# CP210x serial driver for tx2

Compile cp210x module from kernel source tree. [Link](http://blog.csdn.net/gzj2013/article/details/77069803)

Config kernel with `make xconfig` (gui) or `make menuconfig` (cli)

---

# Disk empty? Clean it

Try this package: `ncdu`

---

# ZR300

The official solution is [Here](/2018/01/11/buildTX2KernelForZR300/).

But, I really think you should give up if your device is ZR300 and Jetson TX2 running L4T28.1

---

# Clean L4T Host side package

See this [page](/2018/01/18/dont-let-l4t-break-your-libs/)
