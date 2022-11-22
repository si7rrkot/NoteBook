# CAN 模块

[toc]

## CAN模块术语和缩写

|术语|描述|
|:--:|:--|
|CAN controller <br/>CAN控制器|A CAN controller serves exactly one physical channel.<br/> 一个CAN控制器正好服务于一个物理通道。|
|CAN Hardware Unit<br/>CAN硬件单元|A CAN Hardware Unit may consists of one or multiple CAN controllers of the same type and one or multiple CAN RAM areas.The CAN Hardware Unit is either on-chip,or an external device. The CAN Hardware Unit is represented by one CAN driver. <br/>一个CAN硬件单元可以由一个或多个相同类型的CAN控制器和一个或多个CAN RAM区域组成。CAN硬件单元要么是片上的，要么是一个外部设备。CAN硬件单元由一个CAN Driver表示。|
|CAN L-PDU|Data Link Layer Protocol Data Unit.Consists of Identifier, Data Length and Data (SDU).<br/>  数据链路层协议数据单元。由标识符、数据长度和数据(SDU)组成。|
|CAN L-SDU|Data Link Layer Service Data Unit. Data that is transported inside the L- PDU.  <br/>数据链路层服务数据单元。在L-PDU内传输的数据。|
|DLC|Data Length Code (part of CAN message describes the SDU length)  <br/>数据长度代码（CAN报文的一部分描述了SDU长度）|
|Hardware Object<br/>硬件对象|A CAN hardware object is defined as a PDU buffer inside the CAN RAM of the CAN hardware unit/CAN controller.A Hardware Object is defined as L-PDU buffer inside the CAN RAM of the CAN Hardware Unit. <br/>CAN硬件对象被定义为CAN硬件单元/CAN控制器的 CAN RAM内的PDU缓冲区。硬件对象被定义为CAN硬件单元的CAN RAM内的L-PDU缓冲区。|
|Hardware Receive Handle(HRH)<br/>硬件接收句柄|The Hardware Receive Handle (HRH) is defined and provided by the CAN Driver.Each HRH typically represents just one hardware object.The HRH can be used to optimize software filtering.<br/>硬件接收句柄(HRH)由CAN Driver定义和提供。每个HRH通常仅代表一个硬件对象。HRH可用于优化软件过滤。|
|Hardware Transmit Handle (HTH)<br/>硬件发送句柄|The Hardware Transmit Handle (HTH) is defined and provided by the CAN Driver. Each HTH typically represents just one or multiple hardware objects that are configured as hardware transmit buffer pool.  <br/>硬件发送句柄 (HTH) 由 CAN Driver定义和提供。每个HTH通常仅代表一个或多个配置为硬件发送缓冲池的硬件对象。|
|Inner Priority Inversion  <br/>内部优先级反转|Transmission of a high-priority L-PDU is prevented by the presence of a pending low-priority L-PDU in the same transmit hardware object. <br/> 同一发送硬件对象中存在未决的低优先级L-PDU会阻止高优先级L-PDU的发送|
|ISR|Interrupt Service Routine <br/> 中断服务程序|
|L-PDU Handle<br/>L-PDU句柄|The L-PDU handle is defined and placed inside the CanIf module layer.Typically each handle represents an L-PDU, which is a constant structure with information for Tx/Rx processing.<br/>L-PDU句柄被定义并放置在CanIf模块层内。通常每个句柄代表一个L-PDU，它是一个常量结构，包含用于Tx/Rx处理的信息。|
|MCAL|Microcontroller Abstraction Layer<br/> 微控制器抽象层|
|Outer Priority Inversion  <br/>外优先反转|A time gap occurs between two consecutive transmit L-PDUs.  In this case a lower priority L-PDU from another node can prevent sending the own higher priority L-PDU. Here the higher priority L-PDU cannot participate in arbitration during network access because the lower priority L-PDU already won the arbitration.  <br/>两个连续发送的L-PDU之间出现时间间隙。  在这种情况下，来自另一个节点的较低优先级L-PDU可以阻止发送自己的较高优先级 L-PDU。这里优先级较高的L-PDU在网络访问期间不能参与仲裁，因为较低优先级的 L-PDU 已经赢得了仲裁。|
|Physical Channel  <br/>物理通道|A physical channel represents an interface from a CAN controller to the CAN Network. Different physical channels of the CAN hardware unit may access different networks.  <br/>物理通道代表从CAN控制器到CAN网络的接口。CAN硬件单元的不同物理通道可以访问不同的网络。|
|Priority  <br/>优先级|The Priority of a CAN L-PDU is represented by the CAN Identifier. The lower the numerical value of the identifier, the higher the priority.  <br/>CAN L-PDU的优先级由CAN标识符表示。标识符的数值越小，优先级越高。|
|SFR|Special Function Register. Hardware register that controls the controller behavior.  <br/>特殊功能寄存器。控制控制器行为的硬件寄存器。|
|SPAL|Standard Peripheral Abstraction Layer  <br/>标准外围抽象层|
|ICOM|Intelligent Communication Controller  <br/>智能通讯控制器|

## 7.对其他模块的依赖

### 7.1    驱动服务的依赖

- 驱动注意如果 CAN 控制器是片上的，CAN 模块不能使用其他驱动的任何服务函数 
- `CAN _ Init` 将初始化 CAN 控制器使用的所有片上硬件资源。唯一的例外是数字 I/O 引脚配置(由 CAN 使用的引脚) ，这是由端口驱动程序完成的
- Mcu模块应配置与其他模块共享的寄存器设置。同时在初始化Can模块之前，需要先初始化Mcu模块。
- 如果使用片外 CAN 控制器，CAN 模块将使用其他 MCAL 驱动程序的服务。如果CAN模块使用了其他的MCAL驱动，需要确保，其他的MCAL模块在CAN模块之前初始化
- CAN 模块应该使用底层 MCAL 驱动程序的同步 API，并且不应该提供给 MCAL 驱动程序可以调用的回调函数因此，μC 与 CAN 硬件单元之间的连接类型只会影响实现，而不会影响 API。

---

### 7.2 对系统服务的依赖

1. 在特殊的硬件情况下，CAN可能会轮询硬件的事件。
2. CAN 模块应该使用系统服务提供的 OsCounter 进行超时检测，以防硬件在预期的时间内没有反应(硬件故障)以防止无止境的循环，等待硬件响应的 CAN 模块功能的阻塞时间应短于 CAN 主要功能(即 CAN _ Mainfunction _ Read)的触发时间，因为 CAN 主要功能不能用于此目的
3. Can 模块在其他模块之间进行交互(例如。诊断事件管理器(DEM)、默认错误跟踪器(DET)、 Ecu 状态管理器(ECUM) ，以直接方式使用 CanIf 模块。此文档从不指定请求的实际原点或通知的实际目的地。驱动程序只将 CanIf 模块视为起点和目的地

---

### 7.3 Can模块用户

Can模块需与其他模块进行直接的交互。如：默认错误跟踪（DET），Ecu状态管理器（EcuM）和CanIf模块。**驱动程序只将CanIf模块视为起点和目的地。**

---

## 8.功能规范

在L-PDU传输时，Can模块将L-PDU写入Can控制器硬件内部的适当缓冲区。在L-PDU接收时，Can模块以ID、数据长度和L-SDU指针为参数调用接收指示回调函数（RX indication callback function）。

Can模块需要提供了一个接口作为周期性处理函数，同时此函数由Basic Software Scheduler模块周期性地调用。此外，Can模块提供服务来控制CAN控制器（CAN controller）的状态。 通过回调函数通知Bus-Off和唤醒（Wakeup）事件。**Can 模块是访问硬件资源的基础软件模块。 因此它需要满足AUTOSAR_SRS_SPAL中指定的基础软件模块的要求。**

Can模块需要为所有所需的CAN硬件单元（CAN Hardware Unit）中断，**实现中断服务程序（interrupt service routine）**。同时Can模块也需要负责**禁用CAN控制器（CAN controller）中所有未使用的中断**。Can模块应**在中断服务程序结束时重置中断标志（如果重置动作不是由硬件自动完成）**。**Can模块也不得设置向量表条目的配置**（如：优先级）。

---

### 8.1 驱动程序

一个Can模块提供对一个CAN硬件单元（CAN Hardware Unit）的访问，该硬件单元可能由多个CAN控制器（CAN controller）组成。对于不同类型的CAN硬件单元，需要实现不同的CAN模块。

如果在一个ECU中实现了多个CAN硬件单元（相同或不同供应商），则Can模块需要确保函数名称和的全局变量的名称不能重复。

**命名约定如下：**

```
<Can module name><vendorID><Vendor specific API name><driver abbreviation>()
```

如果必须在一个ECU上支持多种不同的CAN控制器类型，则必须使用以上的命名约定。如果只使用一种控制器类型，则没有任何<**driver abbreviation**>扩展的原始命名约定已经足够了。

---

 ### 8.2 驱动程序状态机（Driver State Machine）

CAN模块有个非常简单的程序状态机，只有两个状态**CAN_UNINIT **和**CAN_READY**

![](https://s2.loli.net/2022/10/08/JZXAlCv7ITWj6zr.png)

- 上电之后CAN模块的状态应该是**CAN_UNINIT**状态
- 在初始化硬件单元内的所有控制器之后，函数`Can_Init`将会改变Can模块的状态为**CAN_READY**
- `Can_DeInit`函数需在取消初始化硬件单元内的所有控制器之前，将Can模块更改为 **CAN_UNINIT**状态。
- `Can_Init`函数应根据其配置初始化所有CAN控制器。然后必须通过调用函数 `Can_SetControllerMode(CAN_CS_STARTED)`单独启动每个CAN控制器。
- 影响硬件单元内所有CAN控制器的硬件寄存器设置只能在函数`Can_Init`中设置。ECU状态管理器（EcuM）模块在运行时只能调用一次Can_Init函数。

---

### 8.3 CAN控制器状态机（CAN Controller State Machine）

每个CAN控制器都有以硬件实现的复杂状态机。 四种基本状态：**未初始化（UNINIT）**、**停止（STOPPED）**、**启动（STARTED）**和**睡眠（SLEEP）**。

对于每个CAN控制器，在CAIF模块中实现相应的‘SOFTWARE’状态机，其状态如下：CAIF_CS_UNINIT、CAIF_CS_STOPPED、CAIF_CS_STARTED和CAIF_CS_SLEEP。显示了软件状态机的实现。任何CAN硬件访问都由CAN模块的功能封装，但CAN模块不会记住状态变化。

任何CAN硬件访问都被Can模块的功能封装，但Can模块并不记忆状态的变化。Can模块提供Can_Init、Can_SetBaudrate和Can_SetControllerMode等服务。这些服务执行必要的寄存器设置，最终会导致硬件CAN控制器状态发生所需的更改。

同时外部事件（external event）也能触发状态改变，这里有两种可能的外部事件：

- Bus-off事件。
- 硬件唤醒事件。

以上的事件可以通过中断或者在`Can_MainFunction_BusOff`或 `Can_MainFunction_Wakeup`函数中轮询的状态位实现。

Can模块需要进行必要的寄存器设置，以实现所需的行为（如：在Bus-Off的情况下无硬件恢复）。然后Can模块通过调用相应的回调函数来通知CanIf模块，相关的软件状态也会在此回调函数中被更改。

如果开发错误被启用并且上层请求不允许转换，Can模块将产生开发错误**CAN_E_TRANSITION**。但Can模块在执行`Can_Write`或调用回调函数之前，则不会检查实际状态。

#### 8.3.1 CAN控制器状态描述（CAN Controller State Description）

##### CAN控制器未初始化状态（UNINIT）

在这种状态下，CAN控制器还未初始化。属于CAN模块的所有寄存器都处于复位状态，CAN中断被禁用，CAN控制器未参与CAN总线活动。

##### CAN控制器停止状态（STOPPED）

在这种状态下，CAN控制器被初始化但不参与总线活动。 此外也不会发送错误帧和确认帧。对于许多进入初始化模式（Entering an initialization-mode）的Can控制器，会导致CAN控制器进入停止状态。

##### CAN控制器启动状态（STARTED）

在这种状态下，CAN控制器已经处于具有完整功能的正常运行模式，也就是意味着它参与了网络。 对于许多离开初始化模式（Leaving the initialization-mode）的控制器，会导致CAN控制器进入启动状态。

##### CAN控制器睡眠状态（SLEEP）（分为硬件支持睡眠模式和不支持睡眠模式）

对于支持睡眠模式的CAN硬件，只有硬件设置与的STOPPED状态不同，即能通过CAN硬件直接支持的CAN总线唤醒。

**当CAN硬件支持睡眠模式**，并被触发转换为SLEEP状态时，Can模块需要将控制器设置为SLEEP状态，以便于可以通过硬件来唤醒CAN总线。

**当CAN硬件不支持睡眠模式**，并被触发转换为SLEEP状态时，Can模块应模拟逻辑的SLEEP状态，并且仅当CAN控制器被软件触发转换为STOPPED状态后才从函数返回。CAN硬件应保持在STOPPED状态，而逻辑的SLEEP状态处于活动状态。

#### 8.3.2 CAN控制器状态转换

状态转换由调用软件的`Can_SetControllerMode`函数触发，并将所需转换状态作为参数。 当触发状态成功转换后，回调函数`CanIf_ControllerModeIndication`会通知上层CanIf模块。 监控请求的状态是否完成，应该由上层CanIf模块负责，而不属于Can模块。

一些转换是由总线上的硬件事件触发的，这些转换通过回调函数进行通知。AUTOSAR标准未对无效转换的行为进行定义。 下图显示了所有有效的状态转换。

![](https://s2.loli.net/2022/10/08/RfMqFvHCIXW6da2.png)

<br/>

#### 8.3.3 Can_Init函数引起的转换

<br/>

1. 状态的变化：**UNINIT** -> **STOPPED**（适用于硬件单元中的所有控制器）
2. 由软件调用`Can_Init`函数触发
3. 对硬件单元内的所有CAN控制器进行配置，所有CAN控制器寄存器根据静态配置进行设置。

`Can_Init`函数应将所有CAN控制器设置为STOPPED状态。当进入`Can_Init`函数时，Can模块不在**CAN_UNINIT**状态或CAN控制器不在**UNINIT**状态时，将触发**CAN_E_TRANSITION**的错误。

#### 8.3.4 Can_SetBaudrate函数引起的状态转换

<br/>

1. 状态的变化：**STOPPED **-> **STOPPED**; **SLEEP **-> **SLEEP**; **STARTED **-> **STARTED**
2. 由软件调用`Can_SetBaudrate`函数触发
3. 更改CAN控制器配置，所有CAN控制器寄存器根据静态配置进行设置。

如果调用`Can_SetBaudrate`函数后，将导致CAN控制器重新初始化，并且CAN控制器未处于**STOPPED**状态，则函数应返回**E_NOT_OK**。

如果需要重新初始化，`Can_SetBaudrate`函数需要将CAN控制器保持在**STOPPED**状态；如果需要重新初始化， `Can_SetBaudrate`函数也需要确保不会设置任何会导致CAN控制器参与网络的设置。

#### 8.3.5 Can_SetControllerMode函数引起的状态转换

软件可以使用`Can_SetControllerMode`函数来触发CAN控制器状态转换。 依赖于CAN硬件，寄存器设置更改后，转换到新的CAN 控制器状态，可能会存在时间的延迟。 Can模块在状态转换成功后，通知上层模块（CanIf_ControllerModeIndication）新状态。 监控请求的状态是否完成，应该由上层CanIf模块负责，而不属于Can模块。

`Can_Mainfunction_Mode`函数将轮询CAN状态寄存器的标志，直到该标志发出更改生效的信号。然后通过调用 `CanIf_ControllerModeIndication`函数通知上层模块，由CanIf模块的`ControllerId`引用的相应CAN控制器状态转换成功。

`Can_SetControllerMode`函数需要使用系统服务`GetCounterValue`，来进行超时监控以避免函数阻塞。如果表明状态更改标志一直没有生效，并且`CanTimeoutDuration`参数定义的最大时间已过，则应退出`Can_SetControllerMode`函数，但`Can_Mainfunction_Mode`应继续轮询状态更改标志。

`Can_Mainfunction_Mode`函数需要调用`CanIf_ControllerModeIndication`函数通知上层模块，由CanIf模块的`ControllerId`引用的相应CAN控制器状态转换成功，以防状态转换由`Can_SetControllerMode`函数再次触发。

##### 8.3.5.1 Can_SetControllerMode(CAN_T_START)

<br/>

1. 状态的变化：**STOPPED **-> **STARTED**
2. 由软件触发

`Can_SetControllerMode(CAN_T_START)` 需要通过设置硬件寄存器，以使CAN控制器能参与网络。

`Can_SetControllerMode(CAN_T_START) `需要等待一段有限的时间，已确保CAN控制器已经完全工作。在CAN控制器运行之前发起的传输请求会丢失。CAN控制器可操作性的唯一指标是接收到TX确认或RX指示。发送实体可能会收到确认超时，并且需要能够应对这种情况。

当`Can_SetControllerMode(CAN_T_START)` 被调用时，如果CAN控制器未处于STOPPED状态时，这需要被检测为一个无效的状态转换。

##### 8.3.5.2 Can_SetControllerMode(CAN_T_STOPPED)

<br/>

1. 状态的变化：**STARTED **-> **STOPPED**; **SLEEP **-> **STOPPED**
2. 由软件触发

`Can_SetControllerMode(CAN_T_STOPPED)` 需要设置CAN硬件内部的配置，以使CAN控制器停止参与网络。

`Can_SetControllerMode(CAN_T_STOPPED)` 需要等待一段有限的时间，确保CAN控制器真正关闭，并且CAN控制器已经处于STOPPED状态。

如果CAN硬件不支持睡眠模式，则从SLEEP到STOPPED的转换，应从逻辑睡眠模式退出，但对CAN控制器状态其实没有影响，因为控制器在逻辑睡眠模式时，已经处于停止状态。

`Can_SetControllerMode(CAN_T_STOPPED) `应取消所有的pending的消息。

##### 8.3.5.3 Can_SetControllerMode(CAN_T_SLEEP)

<br/>

1. 状态的变化：**STOPPED **-> **SLEEP**
2. 由软件触发

`Can_SetControllerMode(CAN_T_SLEEP)` 应将控制器设置为睡眠模式。

如果CAN硬件支持睡眠模式，`Can_SetControllerMode(CAN_T_SLEEP) `将等待有限的时间，直到CAN控制器处于SLEEP状态，并确保CAN硬件能够被唤醒。如果CAN硬件不支持睡眠模式，Ca`n_SetControllerMode(CAN_T_SLEEP)` 应将CAN控制器设置为逻辑睡眠模式。仅当调用 `Can_SetControllerMode(CAN_T_WAKEUP)` 时，CAN控制器才需要离开此逻辑睡眠模式。

当`Can_SetControllerMode(CAN_CS_SLEEP) `被调用，但CAN控制器既不在STOPPED状态也不处于SLEEP状态时，它应被检测为无效的状态转换。

##### 8.3.5.4  Can_SetControllerMode(CAN_T_WAKEUP)

1. 状态变化：**SLEEP **-> **STOPPED**
2. 软件触发

如果 CAN 硬件不支持睡眠模式，函数 `CAN_SetControllerMode(CAN_T_WAKEUP)`将从逻辑睡眠模式返回，CAN 驱动程序规范但对 CAN 控制器状态没有影响(因为控制器已经处于停止状态)。

函数 `CAN_SetControllerMode(CAN_T_ WAKEUP)`应该等待一段有限的时间，直到 CAN 控制器处于停止状态。

当输入函数 `CAN_SetControllerMode(CAN_T_WAKEUP)`时，CAN 控制器既不处于状态 **SLEEP**，也不处于状态 **STOPPED**，它将检测到无效的状态转换

#### 8.3.6 硬件事件引起的状态转换（State transition caused by Hardware Events）

##### 8.3.6.1由硬件唤醒造成的状态转换（由CAN总线的唤醒事件触发）

- 状态变化：**SLEEP **-> **STOPPED**
- L-PDU 输入触发
- ECU 状态管理模块通过函数 `EcuM_CheckWakeup `得到通知

**==只有当硬件支持睡眠模式的时候才会触发这种转换==**

在硬件唤醒(由 CAN 总线的唤醒事件触发)时，CAN 控制器将转换到STOPPED状态

在硬件唤醒(由 CAN 总线的唤醒事件触发)时，CAN 模块将在中断上下文或 `CAN_Mainfunction_Wakeup `上下文中调用函数 `Can_MainFunction_Wakeup`

CAN模块不应该进一步处理导致唤醒的L-PDU

如果CAN总线唤醒发生在sleep转换过程中，`Can_SetControllerMode(CAN_T_WAKEUP)`函数应当返回**CAN_NOT_OK**

##### 8.3.6.2 Bus-Off（由CAN控制器的状态变化触发）

- 状态变化：**STARTED **-> **STOPPED**。
- 当CAN控制器达到Bus-Off状态，由硬件触发。
- 当转换为**STOPPED**状态后，通过`CanIf_ControllerBusOff`函数通知CanIf模块，通过CanIf模块的ControllerId引用的相应CAN控制器通知状态已发生变化。

检测到Bus-Off后，CAN控制器应转换为**STOPPED**状态，Can模块需要确保CAN控制器不再参与网络，同时取消仍待处理（pending）的消息。

Can模块禁用或抑制自动的Bus-Off恢复。

---

### 8.4 Can模块/控制器初始化

ECU状态管理器模块（EcuM）应在启动阶段通过调用Can_Init函数来初始化Can模块，之后Can模块的其他接口函数才被允许被调用。

**Can_Init函数应初始化：**

- 静态变量，包括所有标志。
- 完整CAN硬件单元的通用设置。
- 每个CAN控制器的特定设置。

但是`Can_Init`函数不允许更改任何CAN控制器未使用硬件资源的寄存器。

**Can模块需要遵守以下有关控制器寄存器初始化的规则：**

- 如果硬件只允许寄存器的一种用途，则实现该功能的Can模块负责初始化寄存器。
- 果寄存器可以影响多个硬件模块，并且如果它是一个I/O寄存器，它应该由PORT驱动程序初始化。
- 如果寄存器可以影响多个硬件模块，并且如果它不是一个I/O寄存器，则应由MCU驱动程序初始化。
- 一次性可写寄存器需要在复位后直接初始化，应由启动代码初始化。
- 所有其他寄存器都应由启动代码初始化。

如果`Can_SetBaudrate`确定目标配置需要重新初始化，并且CAN控制器也处于**STOPPED**状态，则`Can_SetBaudrate`函数需要重新初始化CAN控制器并进行控制器的特定设置。**如果重新初始化是必须，则CAN控制器必须切换到STOPPED状态，然后才能执行 Can_SetBaudrate来应用新的波特率配置。**

`Can_SetBaudrate`函数只影响包含单个CAN控制器的特定配置的寄存器区域。

Can模块配置定义全局CAN硬件单元设置和对默认CAN控制器配置集的引用。

可以通过参数Config来选择所需的CAN控制器配置

Config 是指向存储在 ROM 中的实现特定数据结构数组的指针。不同的控制器配置集位于 ROM 中的数据结构中

Config的可能值由配置说明提供

CAN 模块配置定义了全局 CAN 硬件单元设置，并引用了默认的 CAN 控制器配置集。

### 8.5 L-PDU的传输

在L-PDU传输时，Can模块将L-PDU内容，ID和数据长度转换为硬件特定格式（如果需要），然后触发传输。

CAN到存储器的数据映射定义为先发出的CAN数据字节为数组第0个元素，最后发出的CAN数据字节为数组第7个元素或者为CAN FD的数据第63个元素。

如果CAN硬件缓冲区内的表示与AUTOSAR定义不同，则Can模块必须为上层提供经过调整的 SDU-Buffer。

可以配置多个具有唯一HTH的TX硬件对象。CanIf模块提供HTH作为TX请求的参数。有关可能的配置，见下图

![](https://s2.loli.net/2022/10/10/MCNpWDSJoeFzrwQ.png)

`Can_Write`函数需要一直保存参数PduInfo中给出的swPduHandle，直到Can模块为此发送请求，调用了`CanIf_TxConfirmation`，其中swPduHandle作为参数给出。此特性用于CanIf模块实现中减少搜索的时间。

Can模块应调用`CanIf_TxConfirmation`以指示成功传输。在轮询模式的情况下，它也可以在相应硬件资源的TX中断服务程序中（TX-interrupt service routine）调用或在`Can_MainFunction_Write`函数内部调用。

#### 8.5.1 优先级反转

多路复用传输（Multiplexed transmission）可用于避免外部/内部优先级反转。同时Can模块允许多路复用传输功能在预编译时可静态配置使能或者禁止（ON | OFF）

多个传输硬件对象（由CanHwObjectCount定义）可以由一个HTH分配，并且向上层模块表现为一个传输实体。

**Can模块需要支持设备的多路传输机制：**

- 多个发送硬件对象被分组到一个发送实体，并且可以填充在同一个寄存器集，同时微控制器将L-PDU自动存储到一个空闲缓冲区中。
- 硬件提供寄存器或函数来识别传输实体内的自由传输硬件对象。

Can模块需要支持按L-PDU优先级顺序发送L-PDU的设备的多路传输。

注意：

按优先级排序L-PDU避免了分配给配置为多路传输的Basic-CAN的L-PDU的内部优先级反转问题。 避免内部优先级反转的另一种可能性是将所有HTH配置为Full-CAN，如果CAN 硬件能够使用CAN ID或相关优先级字段在传输时进行优先级传输。

应避免优先处理的软件仿真，因为额外的开销会使多路传输的优势无效。

![](https://s2.loli.net/2022/10/10/UD4MStWuoerHc1R.png)

#### 8.5.2 传输数据一致性

Can模块需要直接从上层模块缓冲区复制数据。上层模块负责保持缓冲区一致，直到`Can_Write`函数被调用后返回。

---

### 8.6 L-PDU的接收

当接收到L-PDU时，Can模块应调用RX指示回调函数`CanIf_RxIndication`，其中包含了 ID、HOH，Mailbox参数中包含抽象的CanIf的ControllerId，以及参数PduInfoPtr中的数据长度和指向L-SDU缓冲区的指针。

在扩展CAN帧的情况下，Can模块应将ID转换为标准化格式，因为上层CAN IF模块不知道接收到的CAN帧是标准CAN帧（Standard CAN frame）还是扩展CAN帧（**Extended CAN frame**）。 如果是扩展CAN帧，需要将接收到的CAN帧ID的MSB设为1，以将接收到的 CAN帧标记为扩展。

在相应硬件资源的RX中断服务程序（**RX-interrupt service routine**）或者轮询模式下的`Can_MainFunction_Read`函数需要调用回调函数`CanIf_RxIndication`。

CAN到内存的数据映射定义和数据传输一样，首先接收的CAN数据字节为数组第0个元素，最后接收的CAN数据字节为数组元素第7个或者CAN FD的第63个元素。如果CAN硬件缓冲区内的表示与AUTOSAR定义不同，则Can模块必须为上层模块提供经过调整的SDU-Buffer。

Can驱动需要指示接收到的消息是传统CAN帧（Conventional CAN frame）还是CAN FD帧（CAN FD frame），如Can_IdType中定义，通过使用两个最高有效位来指定帧类型：

- 00 带有标准CAN ID的传统CAN帧
- 01 带有标准CAN ID的CAN FD帧
- 10 带有扩展CAN ID的传统CAN帧
- 11 带有扩展CAN ID的CAN FD帧

#### 8.6.1 接收数据一致性

为了防止接收到消息的丢失，一些控制器支持为一组硬件对象（hardware object）构建一个先进先出队列（FIFO）。然而在其他一控制器上，可以配置另一个具有相同属性的硬件对象，在主对象出现忙碌的介入用作影子缓冲区（shadow buffer）。

CAN驱动程序需要支持实现了硬件FIFO的控制器。FIFO的大小通过CanHwObjectCount配置。

不支持硬件FIFO的控制器通常提供了实现影子缓冲区机制的能力。当主要硬件对象忙时，其他硬件对象会接管。硬件对象的数量也是通过CanHwObjectCount来进行配置。

![](https://s2.loli.net/2022/10/10/GSgcq4fJ8TMVvW9.png)

如果CAN硬件无法保护（锁定）RX缓冲区，以防止数据被新接收到的消息所覆盖。Can模块需要在接收后，将L-SDU复制到影子缓冲区中。

如果CAN硬件不可全局访问，Can模块也需要将L-SDU复制到影子缓冲区中。完整的RX处理（包括复制到目标层，例如：COM）需要在RX中断程序的上下文或者 Can_MainFunction_Read函数的上下文中完成。

Can模块应保证ISR和Can_MainFunction_Read函数都不能被自己中断。这样CAN硬件（或影子）缓冲区始终是一致的，因为它是在一个永远不会被自身中断的函数中按顺序写入和读取的。

如果CAN硬件无法配置为在接收后锁定RX硬件对象（硬件功能），则可能会发生硬件缓冲区被新到达的消息覆盖的情况。在这种情况下，如果硬件支持，CAN控制器会检测到覆盖（overwrite）事件。

如果CAN硬件可以配置为在接收后锁定RX硬件对象，则可能会发生新到达的消息无法存储到硬件缓冲区的情况。在这种情况下，如果硬件支持，CAN控制器会检测到溢出（overrun）事件。

如果检测到“覆盖”或“溢出”事件，Can模块需要引发运行时CAN_E_DATALOST错误。系统设计者应确保消息接收（中断驱动或轮询）的运行时间与系统中可能的快速接收相匹配。

---

### 8.7 唤醒概念（Wakeup concept）

Can模块处理可由Can控制器本身检测到的唤醒，而不是通过Can收发器（Can transceiver）检测到的唤醒。 有两种可能的情况：中断唤醒（wakeup by interrupt）和轮询唤醒（wakeup by polling）。对于中断唤醒，当硬件检测到唤醒时调用Can模块的ISR。

如果唤醒事件的ISR被调用，它应该依次调用`EcuM_CheckWakeup`。传递给`EcuM_CheckWakeup`的参数应该是`CanWakeupSourceRef`配置参数引用的唤醒源的ID。然后，ECU状态管理器将设置MCU，并通过Can接口（CanIf）模块再调用Can模块，从而调用 `Can_CheckWakeup`函数。

当通过轮询检测到唤醒事件时，ECU状态管理器会像以前一样通过Can接口（CanIf）模块周期性地调用`Can_CheckWakeup`函数。

在这两种情况下，`Can_CheckWakeup`都会检查Can控制器是否检测到唤醒并返回结果。然后，CAN驱动程序将通过`EcuM_SetWakeupEvent`通知ECU状态管理器（EcuM）收到唤醒事件（wakeup event）。

唤醒验证主要用于防止错误唤醒事件，它主要由ECU状态管理器（EcuM）和Can接口（CanIf）模块完成，无需Can模块的任何帮助。

有关唤醒机制和唤醒序列图的一般描述，请参阅ECU状态管理器规范[7]。

---

### 8.8 通知概念（Notification concept）

Can模块仅向上层CanIf模块提供事件触发通知接口。每个通知都由一个回调函数表示。

硬件事件可以通过中断或轮询硬件对象的状态标志来检测。有关轮询的配置可能性取决于硬件（即：哪些事件可以轮询，哪些事件需要轮询），并且不受AUTOSAR规范的限制。驱动程序同时也需要可以支持配置为完全不使用中断，即完全轮询。

轮不轮询的配置在Can驱动程序内部，在模块外部是不可见。轮询在CAN主函数（`Can_MainFunction_xxx`）内完成。 同时轮询事件也是通过适当的回调函数通知上层模块。 只不过回调函数地调用不是在ISR中，而是在CAN模块的主函数中。所有回调函数的实现都和在ISR中的实现相同。更多详细信息，可以参见CAN模块的主函数`Can_MainFunction_Read`、`Can_MainFunction_Write`、`Can_MainFunction_BusOff`和`Can_MainFunction_Wakeup`的说明。

---

### 8.9 重入问题（Reentrancy issues）

> **可重入函数**主要用于多任务环境中，一个可重入的函数简单来说就是可以被中断的函数，也就是说，可以在这个函数执行的任何时刻中断它，转入OS调度下去执行另外一段代码，而返回控制时不会出现什么错误；而不可重入的函数由于使用了一些系统资源，比如全局变量区，中断向量表等，所以它如果被中断的话，可能会出现问题，这类函数是不能运行在多任务环境下的。

**例程必须满足以下条件才能可重入：**

- 它以原子方式使用所有共享变量，除非每个变量都分配给函数的特定实例。
- 它不调用不可重入函数。
- 它不会以非原子方式使用硬件。

传输请求仅会在CanIf模块的`CanIf_Transmit`函数内进行转发。由于函数`CanIf_Transmit`是可重入的，所以`Can_Write`函数需要实现线程安全（例如：通过使用互斥锁）。

当写入无法重入时，进一步的（抢占式）调用将返回**CAN_BUSY**。（例如：允许写入不同的硬件TX句柄，不允许写入相同的TX句柄）。在**CAN_BUSY**的情况下，CanIf模块需要对写入请求进行队列管理。（行为与所有硬件对象都忙的情况相同）。

`Can_EnableCanInterrupts`和`Can_DisableCanInterrupts`可能会在可重入函数中被调用，所以这两个函数也是需要是符合可重入设计的。

所有其他服务不需要实现为可重入函数。CAN主函数（如：`Can_MainFunction_Read`）不允许被自己中断，所以这些CAN主函数都是不可重入的。

---

### 8.10 虚拟网络（Pretended Networking）

优化能源效率正变得越来越重要的所有汽车领域，因为能源消耗直接影响燃料消耗，二氧化碳排放量，和范围的混合动力或所有电动汽车。虚拟网络的概念在 ECU 层面上具有很大的节能潜力。

![](https://s2.loli.net/2022/10/10/cih4fZPolYdaRHn.png)

智能通信控制器( Intelligent Communication Controllers，ICOM)的使用支持这些特性(不强制要求具体的硬件实现)。如果暂时不需要 CAN 驱动器 ECU 的部分或全部功能，例如，根据车辆状态，ECU 可以进入“预先联网”模式。在这种模式下，单片机和/或外围设备切换到低功耗模式。只有 ICOM 和连接的收发器保持活跃。当 ECU 需要恢复操作时，ICOM 会生成一个唤醒事件，例如一个接收到的总线消息。

根据 ICOM 实现的不同，接收到的消息的消息 ID 和有效负载可以在硬件中完全进行评估和过滤，这需要软件回调机制。如上图所示，ECU 的实现可以分为三种可能的变体-方法(a)软件方法，没有特定的硬件来支持预装网络。方法(b)描述了通信控制器的功能性硬件扩展，方法(c)显示了具有用于唤醒处理的第二扩展通信控制器的硬件变体。所有的变体和 ICOM 实现都应该得到虚拟网络的支持。

根据硬件实现的不同，ICOM 也能够发送消息。通过使用 ICOM 继续发送，例如状态消息，依赖该消息的其他节点不会受到 ECU 预装联网模式的影响。

此外，预装网络旨在减少唤醒响应时间，即唤醒事件和 ECU 的有效行为之间的时间。通过使用 ICOM 在激活的预装联网模式下保存相关信息，应用程序可以在恢复操作后直接访问最后一个有效信号值。因此，ECU 可以在唤醒后立即响应用户请求，而不必等到再次接收到相应的消息。

#### 8.10.1 虚拟网络模式处理（Support Pretended Networking mode handling）

在 CAN 控制器初始化后，CAN 驱动程序应停用虚拟网络。

通过调用配置 ID 未设置为0的 `Can_SetIcomConfiguration()`函数来激活虚拟网络

CanDrv 负责对 CAN 控制器进行重新配置(包括,根据选定配置(CanIcomConfigId)的 CanIcomConfig 参数，可以使用 ICOM)

如果激活成功，那么 `CanIf_CurrentIcomConfiguration `将被调用，参数 Error 设置为 **ICOM_SWITCH_E_OK**，引用相应的 CAN 控制器，并使用抽象的 CanIf ControllerId。如果激活不成功，那么 `CanIf_CurrentIcomConfiguration `将被调用，参数 Error 设置为 **ICOM_SWITCH_E_FAILED**，引用相应的 CAN 控制器，并使用抽象的 CanIf ControllerId。

如果模拟网络被激活，CanDrv将调用`CanIf_RxIndication()`，当且仅当接收到的消息匹配CanIcomConfig的唤醒条件(参见CanIcomWakeupCauses)。

如果虚拟网络被激活，CanDrv将拒绝返回值为**CAN_BUSY**的`Can_Write()`请求

CAN驱动程序应该在通过`SetControllerMode(CAN_T_START)`启动CAN控制器之前去激活网络

##### 激活虚拟网络（Deactivation of Pretended Networking）

通过调用配置ID = 0的`Can_SetIcomConfiguration()`，可以去激活虚拟网络(即CanDrv的行为应该与在没有假装网络支持的情况下配置时一样)。

如果虚拟网络被禁用，CanDrv将按照正常配置中的配置正常处理消息。

CAN驱动程序应该通过调用`CanIf_CurrentIcomConfiguration`来通知CanIf配置开关，引用对应的带有抽象CanIf ControllerId的CAN控制器。如果去激活成功，则error参数设置为**ICOM_SWITCH_E_OK**，否则设置为**ICOM_SWITCH_E_FAILED**

CAN驱动程序应该在通过`SetControllerMode(CAN_T_STOP)`停止CAN控制器之前停用虚拟网络。

#### 8.10.2 支持消息的自主发送和接收（Support autonomous sending and receiving of messages）

只有在有额外的ICOM硬件可用的情况下，才支持以虚拟网络模式自主发送消息。配置参数定义是否有硬件支持(参见CanIcomVariant)。

如果ICOM是软件实现，则控制器不允许采用虚拟网络方式发送消息

CanDriver应将在虚拟网络模式中收到的所有消息转发给CanIf

### 8.11 时间戳

如果CAN控制器支持基于硬件的时间戳，Can模块可以使用硬件时间戳。这样可以提高CAN时间同步的精度。

如果支持基于硬件的时间戳，CAN驱动程序需要提供以下API：

- Can_GetCurrentTime
- Can_EnableEgressTimeStamp
- Can_GetEgressTimeStamp
- Can_GetIngressTimeStamp

这些API需要通过配置参数`CanGlobalTimeSupport`使能。

基于硬件的时间戳功能的CAN控制器，需要提供一个自由运行（free-running）的计数器，用于获取CAN消息接收和发送的时间戳。自由运行计数器需要在达到其指定最大值后，向上计数并溢出到零。 CiA603标准中规定自由运行计数器对时钟周期进行计数，分辨率支持至少需要为1μs，最多可以1ns。强烈建议提供32位时间戳寄存器和32位计数器。

当CAN帧被认为有效时，发送和接收的CAN消息的时间戳将被捕获。CiA603标准中给出了详细信息。

---

### 8.12 CAN FD的支持

出于性能原因，一些CAN控制器允许使用称为CAN FD的灵活数据速率功能。在仲裁阶段表明可以在有效负载和CRC期间切换到更高的波特率。必须通过使用`CanControllerFdBau​​drateConfig`参数对原`CanControllerBaudrateConfig`进行扩展，实现配置第二个波特率。如果具有CAN FD配置的波特率处于活动状态（请参阅：CanControllerFdBau​​drateConfig），则为该控制器启用CAN FD功能。

可以使用第二个波特率来支持比特率切换（bit rate switch）BRS功能，实现CAN FD帧的接收。但第二个波特率是否用于传输，取决于配置参数`CanControllerTxBitRateSwitch`。

但是，可能也存在需要以下的场景，在支持CAN-FD消息的网络中传输传统CAN 2.0消息。（例如：便于CAN的选择性唤醒。）在这些情况下，有必要支持CAN-FD消息和传统CAN消息交替传输。这问题可以通过在调用Can_Write函数的时候，利用CanId参数的两个最高有效位，以指定使用哪种类型的帧，从而在数据帧的级别上实现交替发送。

CAN FD还支持扩展的有效载荷（Payload），允许传输最多64个字节的数据。此功能也取决于CAN FD配置（请参阅：`CanControllerFdBau​​drateConfig`）。如果CAN控制器处于CAN FD模式（有效的`CanControllerFdBau​​drateConfig`）并且在传递给 `Can_Write`函数的CanId中设置了CAN FD标志，则Can驱动支持传输长度最大为64字节的PDU。如果有发送CAN FD帧的请求，且CAN控制器未处于CAN FD模式（无CanControllerFdBau​​drateConfig），则只要PDU长度<= 8字节，该帧就会作为传统CAN帧发送。

---

### 8.13 错误分类（Error classification）

Can模块应能够根据其配置(默认/生产)检测以下错误和异常

#### 8.13.1 开发中的错误（Development Errors）

|类型或错误|相关|相关错误码|数值[Hex]|
|:--:|:--:|:--:|:--:|
|API Service called with  wrong parameter<br/>使用错误的参数调用API服务|Development<br/>开发|CAN_E_PARAM_POINTER <br/>CAN_E_PARAM_HANDLE <br/>CAN_E_PARAM_DLC <br/>CAN_E_PARAM_CONTROLLER|0x01<br/>0x02<br/>0x03<br/>0x04|
|API Service used without  initialization<br/>API服务未初始化使用|Development<br/>开发|CAN_E_UNINIT|0x05|
|Invalid transition for the  current mode<br/>当前模式转换无效|Development <br/>开发|CAN_E_TRANSITION|0x06|
|Received CAN message  is lost<br/>接收到的CAN消息丢失|Development  <br/>开发|CAN_E_DATALOST|0x07|
|Parameter Baudrate has  an invalid value<br/>参数Baudrate的值无效|Development <br/>开发|CAN_E_PARAM_BAUDRATE|0x08|
|Invalid ICOM  Configuration Id<br/>ICOM配置ID无效|Development  <br/>开发|CAN_E_ICOM_CONFIG_INVALID|0x09|
|Invalid configuration set  selection<br/>配置集选择无效|Development <br/>开发|CAN_E_INIT_FAILED|0x0A|

Can模块应指明由Can模块API的错误使用引起的错误。这包括API参数检查和调用序列错误。

在DET返回后，Can模块产生默认错误的函数应立即返回。

当DET被打开并且Can模块提供返回值时，Can模块的环境应该只在Can模块的函数的返回值中显示默认错误。返回值为**CAN_NOT_OK**。
