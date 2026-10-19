# stm32h743_EtherCAT-FDCAN

面向多协议机器人控制的 STM32H743 项目基础代码。项目原规划以 H743 作为主站，结合 EtherCAT、FDCAN、RS485、Modbus 与机器人空间计算，逐步实现位置、速度和阻抗控制。

**当前阶段：外设初始化与机器人学源码整理。** 现有仓库尚未包含完整 EtherCAT 主站、RS485/Modbus 协议栈或上述控制模式的业务实现。

## 已有内容与计划

| 模块 | 仓库现状 |
| --- | --- |
| STM32 HAL 外设 | GPIO、FDCAN1、TIM3、TIM5、USART1 初始化源码 |
| PWM | main 中启动 TIM5 部分通道并设置比较值 |
| FDCAN | 当前使用 Classic CAN 帧格式的初始化，尚未形成应用收发流程 |
| 机器人计算 | 矩阵、DH 连杆、正逆运动学、雅可比和逆动力学相关源码 |
| MATLAB | PUMA560、SCARA、UR 模型脚本 |
| EtherCAT 主站 | 规划，未见主站协议栈和应用入口 |
| RS485 / Modbus | 规划，未见协议实现 |
| FreeRTOS / 总线可视化 | 原 README 中提出的后续方向 |
| 电机指令封装与位置、速度、阻抗控制 | 后续目标，未形成完整实现 |

## 目录导航

| 路径 | 用途 |
| --- | --- |
| [fdcan.ioc](fdcan.ioc) | CubeMX 项目配置 |
| [Core/Src/main.c](Core/Src/main.c) | 外设初始化入口，当前 while 循环为空 |
| [Core/Src/fdcan.c](Core/Src/fdcan.c) | FDCAN1 外设配置 |
| [Core/Inc/](Core/Inc/) | 外设与系统头文件 |
| [src/](src/) | 矩阵、机器人学与工具代码 |
| [toolbox/](toolbox/) | 与 src 对应的另一份算法代码 |
| [matlab/](matlab/) | 机器人模型脚本 |
| [Middlewares/](Middlewares/) | 部分 ARM DSP 文件，不能据此认定依赖完整 |

## 开发准备

`.ioc` 中记录芯片为 `STM32H743IIT6`（LQFP176），固件包为 `STM32Cube FW_H7 V1.12.1`，目标工具链为 MDK-ARM V5，并包含 `X-CUBE-ALGOBUILD 1.4.0` 相关配置。

当前仓库未提供完整 HAL/CMSIS Drivers 目录、启动文件和 Keil 工程文件，不能保证直接克隆即可构建。可先用 CubeMX 打开 `fdcan.ioc`，核对板卡与软件包，再在独立目录生成工程，手工整合已有源码和 DSP 依赖。机器人库为 C++，需在构建中正确处理 C/C++ 接口和数学库配置。

当前 `fdcan.c` 使用 PI9（FDCAN1_RX）与 PA12（FDCAN1_TX），与 [h743_fdcan](https://github.com/kisawvan/h743_fdcan) 的 PA11/PA12 配置不同，不能直接照搬接线。

## 建议推进顺序

1. 补全可复现工程，验证串口、PWM 和基础 CAN 通信。
2. 统一 src/toolbox，补充机器人计算的独立对照算例。
3. 根据实际硬件确定 EtherCAT 主站方案，再加入协议栈与节点通信。
4. 逐步封装电机命令和反馈，接入 RTOS、可视化与控制算法。
5. 记录编译环境、接线、抓包和实机测试结果。

本文仅按仓库现状整理，不将项目规划视作已完成的通信或控制能力；本次未执行构建或硬件验证。
