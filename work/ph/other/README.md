下面是和MCAL相关的规范文档，基本都是芯片内部各控制器的驱动。大家简单看看文档的框架组成就可以。

1. 《AUTOSAR_SWS_MCUDriver.pdf》，和芯片时钟及模式相关
2. 《AUTOSAR_SWS_PortDriver.pdf》，和芯片Port（或者说是GPIO）相关
3. 《AUTOSAR_SWS_DIODriver.pdf》，是Port的别称，也是和IO相关
4. 《AUTOSAR_SWS_GPTDriver.pdf》，和芯片内部的定时器相关
5. 《AUTOSAR_SWS_LINDriver.pdf》，Lin控制器相关
6. 《AUTOSAR_SWS_LINTransceiverDriver.pdf》，Lin Transceiver控制器相关
7. 《AUTOSAR_SWS_CANDriver.pdf》，Can控制器相关
8. 《AUTOSAR_SWS_CANTransceiverDriver.pdf》，Can Transceiver控制器相关
9. 《AUTOSAR_SWS_ADCDriver.pdf》，和芯片内部的ADC相关
10. 《AUTOSAR_SWS_EEPROMDriver.pdf》，和EEPROM驱动相关
11. 《AUTOSAR_SWS_EthernetDriver.pdf》，和以太网控制器相关
12. 《AUTOSAR_SWS_FlashDriver.pdf》，和Flash驱动相关
13. 《AUTOSAR_SWS_FlashEEPROMEmulation.pdf》，基于EEPROM特性对Flash的抽象
14. 《AUTOSAR_SWS_ICUDriver.pdf》，和输入捕获单元相关
15. 《AUTOSAR_SWS_OCUDriver.pdf》，和输出相关
16. 《AUTOSAR_SWS_PWMDriver.pdf》，和PWM输出
17. 《AUTOSAR_SWS_SPIHandlerDriver.pdf》，和SPI输出
18. 《AUTOSAR_SWS_WatchdogDriver.pdf》，和片内看门狗

主要看7,8,9,10这几章

<br/>

# CANDriver

---

## 3.优先级反转的情况（Priority Inversion）

1. 如果仅使用单个发送缓冲器，则可能发生内部优先级反转。由于优先级低，存储在缓冲区中的消息会等待到“总线上的流量平静下来”。在等待时间内，此消息可以防止由同一微控制器生成的优先级较高的消息通过总线传输。
2. CAN 模块的内部处理时间必须是短到可以用最小的帧间距发送连续的消息，避免在上述所有情况下出现优先级反转。

---

## 4.CAN硬件单元（ CAN Hardware Unit）

CAN 硬件单元结合了一个或多个 CAN 控制器，这些控制器可以是位于晶片上或作为同类型的外部独立装置，并设有共用或独立的硬件对象。

![](https://s2.loli.net/2022/10/08/meXIc6pH8SZKgfO.png)

<br/>

<br/>

---

## 7.对其他模块的依赖

### 7.1	驱动服务的依赖

- 驱动注意如果 CAN 控制器是片上的，CAN 模块不能使用其他驱动的任何服务函数 
- CAN _ Init 将初始化 CAN 控制器使用的所有片上硬件资源。唯一的例外是数字 I/O 引脚配置(由 CAN 使用的引脚) ，这是由端口驱动程序完成的
- Mcu模块应配置与其他模块共享的寄存器设置。同时在初始化Can模块之前，需要先初始化Mcu模块。
- 如果使用片外 CAN 控制器，CAN 模块将使用其他 MCAL 驱动程序的服务。如果CAN模块使用了其他的MCAL驱动，需要确保，其他的MCAL模块在CAN模块之前初始化
- CAN 模块应该使用底层 MCAL 驱动程序的同步 API，并且不应该提供给 MCAL 驱动程序可以调用的回调函数因此，μC 与 CAN 硬件单元之间的连接类型只会影响实现，而不会影响 API。

### 7.2 对系统服务的依赖

1. 在特殊的硬件情况下，CAN可能会轮询硬件的事件。
2. CAN 模块应该使用系统服务提供的 OsCounter 进行超时检测，以防硬件在预期的时间内没有反应(硬件故障)以防止无止境的循环，等待硬件响应的 CAN 模块功能的阻塞时间应短于 CAN 主要功能(即 CAN _ Mainfunction _ Read)的触发时间，因为 CAN 主要功能不能用于此目的
3. Can 模块在其他模块之间进行交互(例如。诊断事件管理器(DEM)、默认错误跟踪器(DET)、 Ecu 状态管理器(ECUM) ，以直接方式使用 CanIf 模块。此文档从不指定请求的实际原点或通知的实际目的地。驱动程序只将 CanIf 模块视为起点和目的地

<br/>

### 7.3 Can模块用户

Can模块需与其他模块进行直接的交互。如：默认错误跟踪（DET），Ecu状态管理器（EcuM）和CanIf模块。驱动程序只将CanIf模块视为起点和目的地。

---

## 文件结构（File structure）

<br/>

![](https://s2.loli.net/2022/10/08/XkYxFawRIU6LEr8.png)

- Can.h的头文件应该要包含Can_GeneralTypes.h、ComStack_Types.h头文件
- Can_GeneralTypes.h头文件应该包含AUTOSAR CAN 模块 CAN、 CanIf 和 CanTrcv 之间共享的所有类型和常量
- CAN模块不应该提供回调函数
- Can 模块应该包含头文件 EcM _ Cbk.h，其中声明了 Can 模块在 Ecu State Manager 模块中调用的回调函数
- 片外 CAN 控制器的 CAN 模块实现应包括头文件 Spi.h。通过这种包含，API 访问外部 CAN 控制器的 SPI 模块包括在内
- CAN模块应包括头文件Os.h文件。通过该包含，将包括读取系统服务提供的OsCounter值(GetCounterValue)的API

---

## 驱动范围（Driver scope）

一个CAN模块提供访问一个CAN硬件单元（可能包含多个CAN 控制器）

- 对于不同类型的CAN硬件单元，应采用不同的CAN模块
- 如果在一个ECU中实现了多个CAN硬件单元(供应商相同或不同)，则应实现CAN模块的功能名称和全局变量，以便不会生成两个同名的功能。
  ```c_cpp
  <Can module name>_<vendorID>_<Vendor specific API name><driver abbreviation>()
  ```
- 只有在一个ECU上有多个CAN控制器的情况下才应用命名约定

---

## 驱动程序状态机（Driver State Machine）

CAN模块有个非常简单的程序状态机，只有两个状态`CAN_UNINIT `和`CAN_READY`

![](https://s2.loli.net/2022/10/08/JZXAlCv7ITWj6zr.png)

- 上电之后CAN模块的状态应该是`CAN_UNINIT`状态
- 在初始化硬件单元内的所有控制器之后，函数`Can_Init`将会改变Can模块的状态为`CAN_READY`
- `Can_DeInit`函数需在取消初始化硬件单元内的所有控制器之前，将Can模块更改为 `CAN_UNINIT`状态。
- `Can_Init`函数应根据其配置初始化所有CAN控制器。然后必须通过调用函数 `Can_SetControllerMode(CAN_CS_STARTED)`单独启动每个CAN控制器。
- 影响硬件单元内所有CAN控制器的硬件寄存器设置只能在函数`Can_Init`中设置。ECU状态管理器（EcuM）模块在运行时只能调用一次Can_Init函数。

---

## CAN控制器状态机（CAN Controller State Machine）

每个CAN控制器都有以硬件实现的复杂状态机。 四种基本状态：未初始化（UNINIT）、停止（STOPPED）、启动（STARTED）和睡眠（SLEEP）。

对于每个CAN控制器，在CAIF模块中实现相应的‘SOFTWARE’状态机，其状态如下：CAIF_CS_UNINIT、CAIF_CS_STOPPED、CAIF_CS_STARTED和CAIF_CS_SLEEP。显示了软件状态机的实现。任何CAN硬件访问都由CAN模块的功能封装，但CAN模块不会记住状态变化。

任何CAN硬件访问都被Can模块的功能封装，但Can模块并不记忆状态的变化。Can模块提供Can_Init、Can_SetBaudrate和Can_SetControllerMode等服务。这些服务执行必要的寄存器设置，最终会导致硬件CAN控制器状态发生所需的更改。

同时外部事件（external event）也能触发状态改变，这里有两种可能的外部事件：

- Bus-off事件。
- 硬件唤醒事件。

以上的事件可以通过中断或者在`Can_MainFunction_BusOff`或 `Can_MainFunction_Wakeup`函数中轮询的状态位实现。

Can模块需要进行必要的寄存器设置，以实现所需的行为（如：在Bus-Off的情况下无硬件恢复）。然后Can模块通过调用相应的回调函数来通知CanIf模块，相关的软件状态也会在此回调函数中被更改。

如果开发错误被启用并且上层请求不允许转换，Can模块将产生开发错误CAN_E_TRANSITION。但Can模块在执行Can_Write或调用回调函数之前，则不会检查实际状态。

---

## CAN控制器状态描述（CAN Controller State Description）

### CAN控制器未初始化状态（UNINIT）

在这种状态下，CAN控制器还未初始化。属于CAN模块的所有寄存器都处于复位状态，CAN中断被禁用，CAN控制器未参与CAN总线活动。

### CAN控制器停止状态（STOPPED）

在这种状态下，CAN控制器被初始化但不参与总线活动。 此外也不会发送错误帧和确认帧。对于许多进入初始化模式（Entering an initialization-mode）的Can控制器，会导致CAN控制器进入停止状态。

### 	

在这种状态下，CAN控制器已经处于具有完整功能的正常运行模式，也就是意味着它参与了网络。 对于许多离开初始化模式（Leaving the initialization-mode）的控制器，会导致CAN控制器进入启动状态。

###     CAN控制器睡眠状态（SLEEP）

对于支持睡眠模式的CAN硬件，只有硬件设置与的STOPPED状态不同，即能通过CAN硬件直接支持的CAN总线唤醒。

当CAN硬件支持睡眠模式，并被触发转换为SLEEP状态时，Can模块需要将控制器设置为SLEEP状态，以便于可以通过硬件来唤醒CAN总线。

当CAN硬件不支持睡眠模式，并被触发转换为SLEEP状态时，Can模块应模拟逻辑的SLEEP状态，并且仅当CAN控制器被软件触发转换为STOPPED状态后才从函数返回。CAN硬件应保持在STOPPED状态，而逻辑的SLEEP状态处于活动状态。

---

## CAN控制器状态转换(CAN Controller State Transitions)

状态转换由调用软件的`Can_SetControllerMode`函数触发，并将所需转换状态作为参数。 当触发状态成功转换后，回调函数`CanIf_ControllerModeIndication`会通知上层CanIf模块。 监控请求的状态是否完成，应该由上层CanIf模块负责，而不属于Can模块。

一些转换是由总线上的硬件事件触发的，这些转换通过回调函数进行通知。AUTOSAR标准未对无效转换的行为进行定义。 下图显示了所有有效的状态转换。

![](https://s2.loli.net/2022/10/08/RfMqFvHCIXW6da2.png)

### Can_Init函数引起的转换

1. 状态的变化：UNINIT -> STOPPED（适用于硬件单元中的所有控制器）
2. 由软件调用`Can_Init`函数触发
3. 对硬件单元内的所有CAN控制器进行配置，所有CAN控制器寄存器根据静态配置进行设置。

`Can_Init`函数应将所有CAN控制器设置为STOPPED状态。当进入`Can_Init`函数时，Can模块不在CAN_UNINIT状态或CAN控制器不在UNINIT状态时，将触发CAN_E_TRANSITION的错误。

### Can_SetBaudrate函数引起的状态转换

1. 状态的变化：STOPPED -> STOPPED; SLEEP -> SLEEP; STARTED -> STARTED
2. 由软件调用`Can_SetBaudrate`函数触发
3. 更改CAN控制器配置，所有CAN控制器寄存器根据静态配置进行设置。

如果调用`Can_SetBaudrate`函数后，将导致CAN控制器重新初始化，并且CAN控制器未处于STOPPED状态，则函数应返回E_NOT_OK。

如果需要重新初始化，`Can_SetBaudrate`函数需要将CAN控制器保持在STOPPED状态；如果需要重新初始化， `Can_SetBaudrate`函数也需要确保不会设置任何会导致CAN控制器参与网络的设置。

### Can_SetControllerMode函数引起的状态转换

软件可以使用`Can_SetControllerMode`函数来触发CAN控制器状态转换。 依赖于CAN硬件，寄存器设置更改后，转换到新的CAN 控制器状态，可能会存在时间的延迟。 Can模块在状态转换成功后，通知上层模块（CanIf_ControllerModeIndication）新状态。 监控请求的状态是否完成，应该由上层CanIf模块负责，而不属于Can模块。

`Can_Mainfunction_Mode`函数将轮询CAN状态寄存器的标志，直到该标志发出更改生效的信号。然后通过调用 `CanIf_ControllerModeIndication`函数通知上层模块，由CanIf模块的`ControllerId`引用的相应CAN控制器状态转换成功。

`Can_SetControllerMode`函数需要使用系统服务`GetCounterValue`，来进行超时监控以避免函数阻塞。如果表明状态更改标志一直没有生效，并且`CanTimeoutDuration`参数定义的最大时间已过，则应退出`Can_SetControllerMode`函数，但`Can_Mainfunction_Mode`应继续轮询状态更改标志。

`Can_Mainfunction_Mode`函数需要调用`CanIf_ControllerModeIndication`函数通知上层模块，由CanIf模块的`ControllerId`引用的相应CAN控制器状态转换成功，以防状态转换由`Can_SetControllerMode`函数再次触发。

#### Can_SetControllerMode(CAN_CS_STARTED)

1. 状态的变化：STOPPED -> STARTED
2. 由软件触发

`Can_SetControllerMode(CAN_CS_STARTED)` 需要通过设置硬件寄存器，以使CAN控制器能参与网络。

`Can_SetControllerMode(CAN_CS_STARTED) `需要等待一段有限的时间，已确保CAN控制器已经完全工作。在CAN控制器运行之前发起的传输请求会丢失。CAN控制器可操作性的唯一指标是接收到TX确认或RX指示。发送实体可能会收到确认超时，并且需要能够应对这种情况。

当`Can_SetControllerMode(CAN_CS_STARTED)` 被调用时，如果CAN控制器未处于STOPPED状态时，这需要被检测为一个无效的状态转换。

<br/>

#### Can_SetControllerMode(CAN_CS_STOPPED)

1. 状态的变化：STARTED -> STOPPED; SLEEP -> STOPPED
2. 由软件触发

`Can_SetControllerMode(CAN_CS_STOPPED)` 需要设置CAN硬件内部的配置，以使CAN控制器停止参与网络。`Can_SetControllerMode(CAN_CS_STOPPED)` 需要等待一段有限的时间，确保CAN控制器真正关闭，并且CAN控制器已经处于STOPPED状态。

如果CAN硬件不支持睡眠模式，则从SLEEP到STOPPED的转换，应从逻辑睡眠模式退出，但对CAN控制器状态其实没有影响，因为控制器在逻辑睡眠模式时，已经处于停止状态。

`Can_SetControllerMode(CAN_CS_STOPPED) `应取消所有的pending的消息。

#### Can_SetControllerMode(CAN_CS_SLEEP)

1. 状态的变化：STOPPED -> SLEEP
2. 由软件触发

`Can_SetControllerMode(CAN_CS_SLEEP)` 应将控制器设置为睡眠模式。

如果CAN硬件支持睡眠模式，`Can_SetControllerMode(CAN_CS_SLEEP) `将等待有限的时间，直到CAN控制器处于SLEEP状态，并确保CAN硬件能够被唤醒。如果CAN硬件不支持睡眠模式，Ca`n_SetControllerMode(CAN_CS_SLEEP)` 应将CAN控制器设置为逻辑睡眠模式。仅当调用 `Can_SetControllerMode(CAN_CS_STOPPED)` 时，CAN控制器才需要离开此逻辑睡眠模式。

当`Can_SetControllerMode(CAN_CS_SLEEP) `被调用，但CAN控制器既不在STOPPED状态也不处于SLEEP状态时，它应被检测为无效的状态转换。

<br/>

### Can_DeInit函数引起的状态转换

1. 状态的变化：STOPPED -> UNINIT; SLEEP -> UNINIT（适用于硬件单元中的所有控制器）。
2. 由软件调用`Can_DeInit`触发
3. 准备重新配置硬件单元内的所有CAN控制器。

`Can_DeInit`函数需要将所有CAN控制器设置为UNINIT状态。当`Can_DeInit`函数被调用，如果Can模块不在CAN_READY状态或者任何CAN控制器处于STARTED状态时，需要引发CAN_E_TRANSITION的错误。

<br/>

### 硬件事件引起的状态转换

#### 硬件唤醒（由CAN总线唤醒的事件触发）

1. 状态变化SLEEP->STOPPED
2. 由接收到L-PDU(Data Link Layer Protocol Data Unit)触发
3. 通过`EcuM_CheckWakeup`函数通知EcuM模块

这种状态转换只会发生在硬件支持睡眠模式的情况下。

在硬件唤醒（由CAN总线的唤醒事件触发）时，CAN控制器应转换为STOPPED状态，Can模块应在中断函数或者Can_MainFunction_Wakeup 中调用函数EcuM_CheckWakeup。Can模块不应进一步处理引起唤醒的L-PDU。

在睡眠转换期间，CAN总线唤醒的情况下，函数 Can_SetControllerMode(CAN_CS_STOPPED) 应返回 E_NOT_OK。

#### Bus-Off（由CAN控制器的状态变化触发）

1. 状态的变化：STARTED -> STOPPED。
2. 当CAN控制器达到Bus-Off状态，由硬件触发。
3. 当转换为STOPPED状态后，通过`CanIf_ControllerBusOff`函数通知CanIf模块，通过CanIf模块的ControllerId引用的相应CAN控制器通知状态已发生变化。

检测到Bus-Off后，CAN控制器应转换为STOPPED状态，Can模块需要确保CAN控制器不再参与网络，同时取消仍待处理（pending）的消息。

Can模块禁用或抑制自动的Bus-Off恢复。
