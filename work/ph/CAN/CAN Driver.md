## 术语缩写

<br/>

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

<br/>

## 基础理解

<br/>

Can driver是最低层的一部分，执行硬件访问，并向上层提供一个硬件独立的API。唯一可以访问Can模块的上层是**Can If**模块。Can模块提供启动传输的服务，并调用CanIf模块的回调函数，以独立于硬件通知事件。此外，它还提供服务来控制属于同一CAN硬件单元的CAN控制器的行为和状态。几个Can Controller可以由一个CAN模块来控制，只要它们属于同一CAN硬件单元。

<br/>

### 一、对其他模块的依赖

#### 1.如果CAN控制器是片载的（on-chip），CAN模块不能使用其他驱动的任何服务。

**在CAN模块初始化调用`Can_Init`函数时，Can Controller所使用的硬件资源应该在这时被初始化，除了CAN使用的引脚（在Port初始化的时候被初始化）。**

#### 2.如果使用芯片外Can Controller，CAN模块可以使用其他MCAL驱动程序

**但是在使用其他模块的服务之前，必须确保这些服务的驱动已经被正确的初始化，并且已经正常运行。**

## 二、其他模块对Can模块的使用

**Can模块需与其他模块进行直接的交互。如：默认错误跟踪（DET），Ecu状态管理器（EcuM）和CanIf模块。驱动程序只将CanIf模块视为起点和目的地。**

<br/>

## CAN Driver接口函数

### 一、单控制器服务接口函数

#### 1.Can_Init函数

**描述：**

 `Can_Init`用于初始化Can模块。

**函数声明：**

```c_cpp
void Can_Init(const Can_ConfigType* Config)
```

**主要工作：**

初始化函数，输入为CAN模块配置结构体指针，在MCU启动时调用，初始化CAN模块的**时钟**、**波特率**、**引脚**、**接收发送邮箱**等一系列寄存器。完毕后将CAN控制器的状态由**CAN_CS_UNINIT**转换为**CAN_CS_STOPPED**。

---

#### 2.Can_SetBaudrate函数

**描述：**

此函数应设置Can Controller的波特率。根据必要的波特率修改，控制器可能需要重置。

**函数声明：**

```c_cpp
Std_ReturnType Can_SetBaudrate(uint8 Controller,uint16 BaudRateConfigID)
```

**主要工作：**

设置CAN模块某个Controller的波特率，输入为控制器ID和波特率ID，所有波特率要被定义为一个结构体，并分配ID，供波特率切换时调用。

---

#### 3.Can_SetControllerMode函数

**描述：**

函数设置Can Controller状态机的软件触发状态转换。

**函数声明：**

```c_cpp
Std_ReturnType Can_SetControllerMode(uint8 Controller,Can_ControllerStateType Transition)
```

**主要工作：**

转换某个CAN控制器的状态。CAN控制器在初始化完毕后处于**STOPPED**状态，要调用这个函数转换为**STARTED**状态后才能参与网络通信。

---

### 二、轮询函数

**轮询函数要在应用程序中被周期性调用。**

#### 1.Can_MainFunction_Write函数

**描述：**

当CAN_TX_PROCESSING设置为POLLING时，该函数执行发送确认轮询。

**函数声明：**

```c_cpp
void Can_MainFunction_Write(void)
```

**主要工作：**

**当发送成功确认没有被配置为中断模式时，该函数中会逐个查询当前是否有Hth的报文发送成功**，如果有，就会调用下面这个函数一层层向上层报告发送成功：

```c_cpp
CanIf_TxConfirmation(PduIdType CanTxPduId)
```

Hth通常对应一个MO或报文发送邮箱。CanTxPduId、Hth ID和MO ID是一一对应的。

---

#### 2.Can_MainFunction_Read函数

**描述：**

当CAN_TX_PROCESSING设置为POLLING时，该函数执行接收确认轮询。

**函数声明：**

```c_cpp
void Can_MainFunction_Read(void)
```

**主要工作：**

**当报文接收没有被配置为中断模式时**，该函数会查询当前所有MO或接收报文邮箱是否有报文被成功接收，如果有，就会下面这个函数来向上层报告有报文被接收：

```c_cpp
CanIf_RxIndication(const Can_HwHandleType Hrh, const Can_IdType CanId, const PduInfoType* PduInfoPtr)
```

Hrh ID、MO ID或接收报文邮箱ID是一一对应的。

---

#### 3.Can_MainFunction_BusOff函数

**描述：**

此函数执行对静态配置为“to be polled”的Bus-Off事件的轮询。

**函数声明：**

```c_cpp
void Can_MainFunction_BusOff(void)
```

**主要工作：**

当Busoff处理方式没有被配置为中断模式时，该函数会检查CAN Controller是否处于Busoff状态，如果是，则会报相应的故障，并将CAN控制器状态转换为CAN_CS_STOPPED，并调用下面的函数向上层报告CAN Busoff：

```c_cpp
CanIf_ControllerBusOff(uint8 ControllerId)
```

---

### 三、影响硬件的函数

#### 1.Can_Write函数

**描述：**

CanIf调用此函数将CAN消息传递给CanDrv进行传输。

**函数声明：**

```c_cpp
typedef struct
{
  PduIdType swPduHandle;
  uint8 length;
  Can_IdType id;
  uint8* sdu;
} Can_PduType;

Std_ReturnType Can_Write(Can_HwHandleType Hth, const Can_PduType* PduInfo)
```

**主要工作：**

Can_Write函数是供CANIF层发送报文调用的接口，在下面的函数中被调用：

```c_cpp
Std_ReturnType CanIf_Transmit(PduIdType TxPduId, const PduInfoType* PduInfoPtr)
```

输入参数1 Hth对应着使用哪个MO或发送邮箱进行发送，输入参数2 PduInfoPtr是要发送的内容，包含的参数如代码中的结构体所示，有CAN ID、数据、长度以及PDU ID。在发送该帧报文的时候会把PDU ID和Hrh的对应关系记录下来，在Can_MainFunction_Write函数中进行发送成功确认的时候，要使用相同的对应关系。

## 状态转换

### 一、Can模块状态机

CAN模块有一个含两个状态的状态机，在上电或重置后，CAN模块进入**CAN_UNINIT**状态(未初始化状态)，随后初始化硬件单元的所有控制器后，CAN模块进入**CAN_READY**状态。这时就意味着CAN模块初始化完成了，可以执行一些与读，写，总线关闭，唤醒，设置控制器状态相关的功能。

![](https://s2.loli.net/2022/10/08/JZXAlCv7ITWj6zr.png)

---

### 二、Can Controller状态机

在硬件层面，CAN控制器会执行很复杂的状态机，去实现不同的硬件行为。有时是需要在相应的状态去实现某些功能，比如初始化，波特率设置等；有时是外部事件会触发状态变化，比如总线关闭，唤醒事件。出于简化目的，这里将CAN控制器的状态分为4个。

未初始化**（UNINIT）**、**停止（STOPPED）**、**启动（STARTED）**和**睡眠（SLEEP）**。

Can Controller状态转换图，如下所示：

![](https://s2.loli.net/2022/10/08/RfMqFvHCIXW6da2.png)

**CAN控制器未初始化- UNINIT**
属于CAN模块的所有寄存器都处于复位状态，不使能CAN中断。 CAN控制器不参与CAN总线。

**CAN控制器状态已停止- STOPPED**
在这种状态下，CAN控制器已初始化，但不参与总线。另外也不得发送错误帧和确认。

**CAN控制器状态已启动- STARTED**
控制器处于正常运行模式，具有完整的功能，这意味着它已加入网络。
对于许多控制器而言，离开“初始化”模式会使控制器启动。

**CAN控制器状态为睡眠- SLEEP**
与STOPPED仅有的不同是：CAN硬件的硬件设置支持睡眠模式（由CAN硬件支持唤醒的CAN总线直接唤醒）
关于CAN硬件支不支持SLEEP，其对应的处理方法有：
支持-- the CAN hardware is triggered to transition into SLEEP state, the Can module shall 
set the controller to the SLEEP state from which the hardware can be woken over CAN Bus.
不支持-- the CAN hardware is triggered to transition into SLEEP state, the Can module shall 
emulate a logical SLEEP state from which it returns only, when it is triggered by software 
to transition into STOPPED state.

## Can模块发送处理方式

这里说明下CAN发送处理方式，有3种：

### （1）轮询模式（polling mode）

轮询模式下Can模块的发送确认被调度模块（BSW Scheduler）触发，执行随后的处理。比如设定CAN发送为轮询模式，那么Can_MainFunction_Write就会在预定义的时间间隔被周期性地调用（比如每几ms调用一次），然后Can_MainFunction_Write中再去调用上层的CAN Interface模块的CanIf_TxConfirmation模块，向上确认。

### （2）中断模式（interrupt mode）

中断模式下Can模块的发送确认由CAN控制器的中断触发，执行随后的处理，比如设定CAN发送为中断模式，那么中断触发后，调用Can模块的发送确认处理函数，在该函数中去调用Can Interface模块的CanIf_TxConfirmation函数，向上确认。

### （3）混合模式（mixed mode）

即（1）（2）的混合使用，看具体设定，是使用轮询模式还是使用中断模式的处理方式。

所以这里我选择的是轮询方式进行介绍。根据CanIf_TxConfirmation函数的输入参数可知，向上确认的是哪个CAN ID发送成功。
