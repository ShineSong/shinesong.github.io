---
title: cartographer
tags:
---

# 1.0

# 改进

## 效率

### cartographer/io/points_batch.cc::RemovePoints 函数 不保顺序可以替换为更高效的版本

## 效果

### cartographer/mapping/imu_tracker.cc::AddImuLinearAccelerationObservation 利用磁力计融合的IMU数据代入Orientation的解算