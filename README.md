# 河北建筑工程学院 LuBan 战队 RoboMaster 2023 步兵代码

本仓库为 LuBan 战队 2023 赛季步兵机器人主控工程，基于 STM32F407 + FreeRTOS，包含底盘、云台、发射机构、姿态解算、裁判系统通信与上位机交互等核心功能模块。

## 1. 项目概览

- 主控芯片：STM32F407
- 软件框架：HAL + FreeRTOS（CMSIS-RTOS）
- 通信总线：CAN、USART、USB
- 主要能力：
	- 底盘控制与功率管理
	- 云台控制与行为切换
	- 射击机构控制
	- IMU 姿态解算（INS/AHRS）
	- 裁判系统通信与客户端 UI 绘制
	- 遥控器输入、设备掉线检测与校准流程

## 2. 目录说明

工程主要目录如下（仅列出高频使用部分）：

- application/
	- 机器人业务任务与行为逻辑（chassis_task、gimbal_task、shoot、detect_task 等）
- bsp/
	- 板级驱动封装（CAN、USART、SPI、I2C、PWM、LED 等）
- components/
	- 通用算法、控制器和设备驱动（PID、AHRS、BMI088 等）
- Inc/、Src/
	- STM32CubeMX 生成的外设初始化与系统入口代码
- Drivers/、Middlewares/
	- HAL/CMSIS/FreeRTOS 等第三方组件
- MDK-ARM/
	- Keil 工程文件（.uvprojx）与启动文件
- standard_tpye_c.ioc
	- CubeMX 工程配置文件

## 3. 任务架构（FreeRTOS）

当前工程在 freertos 中创建了以下核心任务：

- calibrate_task：传感器或机构校准流程
- chassis_task：底盘控制主任务
- gimbal_task：云台控制主任务
- INS_task：姿态解算任务（高优先级）
- detect_task：掉线/故障检测任务
- referee_usart_task：裁判系统串口通信任务
- voltage_task：电压采集任务
- servo_task：舵机任务
- led_flow_task：RGB 流水灯任务
- oled_task：OLED 信息显示任务
- usb_task：USB 通信任务

## 4. 开发环境

建议环境：

- Keil MDK-ARM 5（用于编译和下载）
- STM32CubeMX（用于维护 .ioc 配置）
- ST-Link/J-Link（用于程序下载与调试）

## 5. 快速上手

### 5.1 获取代码

1. 克隆或下载本仓库。
2. 使用 Keil 打开 MDK-ARM/standard_tpye_c.uvprojx。

### 5.2 编译

1. 在 Keil 中选择正确 Target。
2. 执行 Rebuild。
3. 确认无报错后生成 .axf/.hex 文件。

### 5.3 下载与运行

1. 连接调试器（ST-Link/J-Link）和主控板。
2. 在 Keil 中配置 Download/Debug 选项。
3. 下载程序并复位运行。

### 5.4 串口与裁判系统

- 工程默认包含裁判系统通信和客户端 UI 绘制代码。
- 若需自定义 UI，请根据实际机器人 ID 和操作手 ID 调整相关参数。

## 6. 代码维护建议

- 任务新增建议：
	- 在 application/ 下新增 task.c/task.h
	- 在 Src/freertos.c 中注册线程与优先级
	- 在 detect_task 中补充离线检测（如需要）
- 外设新增建议：
	- 优先在 bsp/ 中进行底层封装
	- 在 application/ 业务层调用，避免跨层耦合
- 参数管理建议：
	- PID、限幅、功率控制参数集中管理
	- 修改后记录测试工况与版本变化

## 7. 常见问题

### 7.1 编译通过但运行异常

- 检查硬件接线与供电
- 检查 CAN 终端电阻、设备 ID 和总线连通性
- 通过 detect_task 的状态判断关键设备是否掉线

### 7.2 裁判系统数据异常

- 检查串口波特率与接线方向
- 确认机器人 ID、客户端 ID 配置一致

### 7.3 姿态数据漂移或异常

- 先执行校准流程
- 检查 IMU 安装方向、减震与供电噪声

## 8. 许可证

请参考仓库中的 LICENSE 与 license.txt。

## 9. 致谢

感谢 RoboMaster 开源生态、历届队员代码沉淀以及参与调试与测试的队内成员。