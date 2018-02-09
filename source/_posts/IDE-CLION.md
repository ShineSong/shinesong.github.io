---
title: 使用CLion进行ROS开发
date: 2017-12-05 10:21:17
tags: [ros,ide,manual]
categories: Trick
---

# CLion
基于Java的Clion可以充当嵌入Docker的IDE，也可以通过SSH X forward进行远程IDE调试，虽然很喜欢用QtCreator进行本地开发，但为了逐渐的迁移到Docker环境进行开发，要研究一下CLion的使用技巧。

## 用catkin_make直接构建的ROSWS
这种WS，在有ROS环境的term运行clion，直接打开build目录下的CMakeCache.txt即可。

## 用catkin_make_isoalted构建的ROSWS
还没有找到合适的办法。这种构建方式对IDE不是很友好。需要插件支持。或者，可以想办法改造ROSWS的CMakeLists.txt使得我们可以构建出混合的build工程。比如下文的cartographer_superbuild

## 用catkin tools构建的WS
catkin tools 是新一代的ws管理工具，比catkin_make更为先进易用，一般使用merge-devel

使用clion进行开发的话，首先从支持ROS的环境下(after `source ***/setup.*sh`)开启Clion

然后在ws下的src目录建立一个CMakeLists.txt, 内容填写

    cmake_minimum_required(VERSION 3.5)
    project(workspace)

    set(CMAKE_CXX_STANDARD 11)

    add_custom_target(DO_NOTHING)

    message(${CMAKE_BINARY_DIR})

    set(CATKIN_DEVEL_PREFIX "${CMAKE_BINARY_DIR}/devel")
    set(CMAKE_PREFIX_PATH "${CMAKE_BINARY_DIR}/devel;/opt/ros/kinetic")

    add_subdirectory(XXXX)

Clion会建立一个另外的build目录，与catkin tools的build/devel目录并不重合，但是没关系，可以用clion运行每个node，launch会麻烦一点，需要修改env var.