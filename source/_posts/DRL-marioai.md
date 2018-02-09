---
title: Mario-AI
tags:
categories: DL
date: 2017-12-12 08:56:12
---


在Youtube上看到这个小项目,找到了在[github](https://github.com/aleju/mario-ai)上的源码,开始折腾.

## 硬件要求

4G显存以上的NV卡

## 准备阶段

新开一个Docker,torch需要用到cuda和cudnn,直接从nvidia/cuda开始,创建自己的镜像.

按照官方的说明,装lua5.1,装torch,装lua的一些依赖包,装lsnes

### 在cuda9.0上编译torch

会提示不支持架构 SM_20, 只需要打开CMakeLists.txt,在if(CUDA_FOUND)段内找到sm_20的字样,改成sm_30即可.

### 编译lsnes

这个比较蛋疼了,遇到很多奇怪的问题.
首先是pkg-config报lua找不到.这个问题可以在option.build中,将LUA=lua改成LUA=lua5.1即可.

另一个比较蛋疼的问题`constexpr unsigned int nall::uclip(unsigned int) [with int bits = 24]`. 这个玩意儿貌似跟编译器有关,我用的ubuntu16.04+gcc5.4报这个问题,换成clang-3.8以后又报了另外的问题,而且数量更多. 将C++标准修改成[0x, 11, 14]都无效以后,我开始找其他的方法. 最后从[bnes-libretro](https://github.com/libretro/bnes-libretro/blob/0bfc2d17de14ecf66b7520a14f11134736d05716/nall/bit.hpp)找到一个版本的bit.hpp.

    #ifndef NALL_BIT_HPP
    #define NALL_BIT_HPP

    #include <nall/stdint.hpp>

    namespace nall {

    template<unsigned bits> inline uintmax_t uclamp(const uintmax_t x) {
    enum : uintmax_t { b = 1ull << (bits - 1), y = b * 2 - 1 };
    return y + ((x - y) & -(x < y));  //min(x, y);
    }

    template<unsigned bits> inline uintmax_t uclip(const uintmax_t x) {
    enum : uintmax_t { b = 1ull << (bits - 1), m = b * 2 - 1 };
    return (x & m);
    }

    template<unsigned bits> inline intmax_t sclamp(const intmax_t x) {
    enum : intmax_t { b = 1ull << (bits - 1), m = b - 1 };
    return (x > m) ? m : (x < -b) ? -b : x;
    }

    template<unsigned bits> inline intmax_t sclip(const intmax_t x) {
    enum : uintmax_t { b = 1ull << (bits - 1), m = b * 2 - 1 };
    return ((x & m) ^ b) - b;
    }

    namespace bit {
    constexpr inline uintmax_t mask(const char* s, uintmax_t sum = 0) {
        return (
        *s == '0' || *s == '1' ? mask(s + 1, (sum << 1) | 1) :
        *s == ' ' || *s == '_' ? mask(s + 1, sum) :
        *s ? mask(s + 1, sum << 1) :
        sum
        );
    }

    constexpr inline uintmax_t test(const char* s, uintmax_t sum = 0) {
        return (
        *s == '0' || *s == '1' ? test(s + 1, (sum << 1) | (*s - '0')) :
        *s == ' ' || *s == '_' ? test(s + 1, sum) :
        *s ? test(s + 1, sum << 1) :
        sum
        );
    }

    //lowest(0b1110) == 0b0010
    constexpr inline uintmax_t lowest(const uintmax_t x) {
        return x & -x;
    }

    //clear_lowest(0b1110) == 0b1100
    constexpr inline uintmax_t clear_lowest(const uintmax_t x) {
        return x & (x - 1);
    }

    //set_lowest(0b0101) == 0b0111
    constexpr inline uintmax_t set_lowest(const uintmax_t x) {
        return x | (x + 1);
    }

    //count number of bits set in a byte
    inline unsigned count(uintmax_t x) {
        unsigned count = 0;
        do count += x & 1; while(x >>= 1);
        return count;
    }

    //return index of the first bit set (or zero of no bits are set)
    //first(0b1000) == 3
    inline unsigned first(uintmax_t x) {
        unsigned first = 0;
        while(x) { if(x & 1) break; x >>= 1; first++; }
        return first;
    }

    //round up to next highest single bit:
    //round(15) == 16, round(16) == 16, round(17) == 32
    inline uintmax_t round(uintmax_t x) {
        if((x & (x - 1)) == 0) return x;
        while(x & (x - 1)) x &= x - 1;
        return x << 1;
    }
    }

    }

    #endif

## torch/init.lua:102: class nn.PrintSize has been already assigned a parent class 

注释掉layers中的printsize.这个类已经集成到nn中,重复定义会报错

## 总结和反思

- 首次接触可以眼见的RL
- 学习了映射内存一个区域做暂存盘的方法(利用tmpfs)