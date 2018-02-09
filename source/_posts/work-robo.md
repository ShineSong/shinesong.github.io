---
title: Robotics Research
date: 2018-02-09 14:32:37
tags: [Robotics, Research, Manual]
categories: Work
---

Version|Update|Revised by
---|---|---
v1.0|2018-02-09|SxSong


# 简介
总结半年以来, 学习和研究Robotics方面的进展, 以及对现有工作的一些总结. 便于自己反思和后续工作的交接传递.
机器人感知控制算法基于ROS kinetic版本构建, 系统版本Ubuntu 16.04 Xenial. PC端使用Docker分离各种开源方案的运行环境,[Docker](#Docker)这一节会详细介绍.

移动平台的采用西工大[Handsfree](https://hands-free.github.io/)小组的Stone底盘,调试技巧见[Handsfree节](#Handsfree).
机器人控制平台采用Nvidia Jetson TX2 kit, 调试技巧见[TX2节](#TX2).
机器人的硬件布局和说明见[Hardware节](#Hardware).

# Docker
Docker可以视为一个轻量级的虚拟机, 并且高效很多. Docker在静态状态下的表现是一个镜像(image),类比于可执行文件. 运行的docker可以是一个容器(container), 类比与进程.

Docker通过layer来减少冗余. 在Dockerfile中一般一个操作命令就会形成一个Layer(实践感觉,没有查阅文档). 通过Commit

---

# TX2
使用电池组件供电,电压控制在12v-19v之间.
---

# Hardware
## Sensors
### VI-Sensor
![VI-Sensor](visensor.jpg)
VI传感器由一个单目Rolling-Shutter和Low-End的IMU组合而成,Rolling-Shutter为 Sony IMX322, 180度鱼眼镜头, 通过USB与上位机连接.
目前以uvc_camera读取图像并转发为ROS消息, 配置图像分辨率为640x480@25fps, CPU资源消耗约40%.

由图可见, 本VI-Sensor安置了两枚IMU,一枚是MPU9250贴在相机背面,Z轴与光轴基本平行;另一枚MPU9255贴在传感器基座上,Z轴与光轴约有45度(设计角度)夹角. 两枚传感器均与上位机以I2C Bus连接.

传感器数据的读取通过rtimulib 和 rtimulib_ros 实现, **注意需要将用户添加到i2c group来获取访问总线权限**.
在TX2上以200Hz的频率发送原始数据,CPU消耗约10%左右.

### VLP-16
VLP-16固定在顶端,水平扫描.

驱动采用velodyne_driver包.

## Handsfree
对Handsfree的机器人模型定义(URDF)文件做了改造, 添加了自己的传感器, 形成以下几个包
- zobo_description 包含模型定义,用于发布机器人自身tf
- zobo_bringup 包含一些启动文件,主要是各种设备和imu
- zobo_gazebo 包含模拟所需要的启动文件

---
# V-SLAM
## ROVIOLI (maplab)

## VINS-Mono

## VI-MEAN

---

# L-SLAM
## LOAM

## BLAM

## Cartographer

## Segmatch