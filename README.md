# rm_auto_aim

## Overview

RoboMaster 装甲板自瞄算法模块

<img src="docs/rm_vision.svg" alt="rm_vision" width="200" height="200">

该项目为 [rm_vision](https://github.com/FaterYU/rm_vision) 的子模块

若有帮助请Star这个项目，感谢~

### License

The source code is released under a [MIT license](rm_auto_aim/LICENSE).

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)

Origin Author: Chen Jun

Develop Author: Zheng Yu

运行环境：Ubuntu 22.04 / ROS2 Humble (未在其他环境下测试)

![Build Status](https://github.com/FaterYU/rm_auto_aim/actions/workflows/ros_ci.yml/badge.svg)

## 新增特性及可能的后续优化方向

- [x] 识别器灯条识别由旋转外接矩形更换为最小二乘法
- [x] 识别器调试话题加入角点信息，供后续数据录制使用
- [x] 识别器灯条增加外接矩形中二值化占比过滤条件，减少将大块白光识别为灯条所占用资源
- [x] 识别器增加任务触发条件，适配能量机关等多任务切换
- [x] 跟踪器增加视野内多目标间切换功能
- [x] 跟踪器扩展卡尔曼滤波过程噪声矩阵中增加平移与旋转参数负相关关系
- [x] 可视化适配 Foxglove 新版本
- [x] 控制端选板及开火控制逻辑
- [ ] 跟踪器扩展卡尔曼滤波运动模型由匀速模型改为匀加速模型
- [ ] 识别器角点PNP结合激光测距传感器自动调参
- [ ] 处理装甲板的灯条边缘受装甲板物理结构遮挡问题
- [ ] 预测效果评价器

## 选板逻辑

控制端代码示例 (C)： [Choose armor for rm_vision (github.com)](https://gist.github.com/FaterYU/7b5d65d3fb08545b815e6d70d1dbb562)

## Building from Source

### Building

在 Ubuntu 22.04 环境下安装 [ROS2 Humble](https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debians.html)

创建 ROS 工作空间后 clone 项目，使用 rosdep 安装依赖后编译代码

	cd ros_ws/src
	git clone https://github.com/FaterYU/rm_auto_aim.git
	cd ..
	rosdep install --from-paths src --ignore-src -r -y
	colcon build --symlink-install --packages-up-to auto_aim_bringup

### Testing

Run the tests with

	colcon test --packages-up-to auto_aim_bringup

## Packages

- [armor_detector](armor_detector)

	订阅相机参数及图像流进行装甲板的识别并解算三维位置，输出识别到的装甲板在输入frame下的三维位置 (一般是以相机光心为原点的相机坐标系)

- [armor_tracker](armor_tracker)

	订阅识别节点发布的装甲板三维位置及机器人的坐标转换信息，将装甲板三维位置变换到指定惯性系（一般是以云台中心为原点，IMU 上电时的 Yaw 朝向为 X 轴的惯性系）下，然后将装甲板目标送入跟踪器中，输出跟踪机器人在指定惯性系下的状态

- auto_aim_interfaces

	定义了识别节点和处理节点的接口以及定义了用于 Debug 的信息

- auto_aim_bringup

	包含启动识别节点和处理节点的默认参数文件及 launch 文件
