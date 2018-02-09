---
title: C++ Tricks
date: 2018-02-09 14:44:06
tags:
categories: Trick
---

# Building
## error adding symbols: DSO missing from command line
if your binutils>=2.22, linker cannot found the symbol that you don't specified in command line. (Even if this symbol is linked by another lib)

OR

you can fix this by adding `--allow-shlib-undefined`

---

## /usr/bin/ld: cannot find -lopencv_dep_cudart
编译OpenCV 2.4.13.5出错, 一直在报
`/usr/bin/ld: cannot find -lopencv_dep_cudart`

参考了各大网站论坛issue, 发现
-DCUDA_USE_STATIC_CUDA_RUNTIME=OFF 和 -DCUDA_USE_STATIC_CUDA_RUNTIME=false 根本不起作用(起码对我来说)

### Way 1
在Nvidia论坛上找到了一个patch, work!

    +++ /usr/share/OpenCV/OpenCVConfig.cmake        2017-07-27 05:34:06.129390969 +0000
    @@ -281,6 +281,11 @@
        set_target_properties("opencv_dep_${_tmp}" PROPERTIES IMPORTED_LOCATION "${l}")
        endif()
    endforeach()
    +
    +  # HACK jwatte 2017-07-26 trying to find the dynamic library
    +  add_library("opencv_dep_cudart" UNKNOWN IMPORTED)
    +  set_target_properties("opencv_dep_cudart" PROPERTIES IMPORTED_LOCATION /usr/local/cuda-8.0/targets/aarch64-linux/lib/libcudart.so)
    +
    endif()

### Way 2
其实修改系统目录下的`/usr/share/cmake-3.5/Modules/FindCUDA.cmake`文件更加一劳永逸

---

# Coding

# IDE

## Clion
See this [page](/2018/01/17/IDE-Eclipse/)

## Eclipse
See this [page](/2018/01/17/IDE-Eclipse/)
