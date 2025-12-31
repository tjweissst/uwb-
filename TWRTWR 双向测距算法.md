# TWRTWR 双向测距算法

### TWR 双向测距算法核心步骤与公式

该算法基于**Poll-Response-Final 三次报文交互**实现，核心是采集双向交互的时间戳，计算信号飞行时间（ToF）后换算距离，以下是对应步骤和公式：

#### 一、 核心交互步骤（节点 A 与节点 B）



1. **Poll 阶段**

* 节点 A 发送 Poll 报文，记录发送时间戳 `poll_tx_ts`

* 节点 B 接收 Poll 报文，记录接收时间戳 `poll_rx_ts`

1. **Response 阶段**

* 节点 B 发送 Response 应答报文，记录发送时间戳 `resp_tx_ts`

* 节点 A 接收 Response 报文，记录接收时间戳 `resp_rx_ts`

1. **Final 阶段**

* 节点 A 发送 Final 报文，记录发送时间戳 `final_tx_ts`，并将 `poll_tx_ts`、`resp_rx_ts`、`final_tx_ts` 打包到报文中

* 节点 B 接收 Final 报文，记录接收时间戳 `final_rx_ts`，同时解析报文中的三个时间戳

#### 二、 核心计算公式



1. **定义时间差参数**



| 参数                                  | 物理意义                               |
| ----------------------------------- | ---------------------------------- |
| $Ra = resp\_rx\_ts - poll\_tx\_ts$  | 节点 A 从发送 Poll 到接收 Response 的总耗时    |
| $Rb = final\_rx\_ts - resp\_tx\_ts$ | 节点 B 从发送 Response 到接收 Final 的总耗时   |
| $Da = resp\_tx\_ts - poll\_rx\_ts$  | 节点 B 接收 Poll 到发送 Response 的内部处理耗时  |
| $Db = final\_tx\_ts - resp\_rx\_ts$ | 节点 A 接收 Response 到发送 Final 的内部处理耗时 |



1. **计算飞行时间原始值**

   $tof\_dtu = \frac{Ra \times Rb - Da \times Db}{Ra + Rb + Da + Db}$

   注：该值的单位是 DW1000 芯片的时间单元（dtu）

2. **转换为实际飞行时间**

   $tof = tof\_dtu \times DWT\_TIME\_UNITS$

   注：`DWT_TIME_UNITS` 是 DW1000 时间单元到秒的换算系数

3. **计算最终距离**

   $distance = tof \times SPEED\_OF\_LIGHT$

   注：`SPEED_OF_LIGHT` 为光速（约 $3\times10^8m/s$）；可选步骤：调用 `dwt_getrangebias` 函数减去距离偏差值，提升测距精度

#### 三、 关键补充说明



* 时间戳为 64 位数值，代码中转为 32 位计算时，即使时钟溢出也不影响差值结果

* 三次报文交互的标识符分别为 `P`（Poll）、`A`（Response）、`F`（Final），用于节点识别报文类型

需要我帮你把这些公式和步骤对应到帖子里的**代码片段**，做一份对照注释吗？

> （注：文档部分内容可能由 AI 生成）