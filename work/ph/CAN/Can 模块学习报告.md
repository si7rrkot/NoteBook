# Can 模块学习报告

[toc]

## 一、相关硬件概念

### 1.Can的硬件单元

![](https://s2.loli.net/2022/10/08/meXIc6pH8SZKgfO.png)

- 一个**CAN 硬件单元（CAN HardwareUnit）**就相当于某个ECU的CAN硬件部分，它可以包括一个或多个**CAN控制器（Controller）**。
- 一个**CAN  Controller**就是一个CAN节点或一条CAN，即对应连接一条物理通道。它有很多个**报文邮箱（message object）**。
- 一个报文邮箱又有多个寄存器，用来存储报文信息，如下图所示

![](https://s2.loli.net/2022/10/14/frh4PsL1CUdyDei.png)

### 2.对其他模块的依赖

#### 1.如果CAN Controller是片上的（on-chip），CAN模块不能使用其他驱动的任何服务。

**在CAN模块初始化调用`Can_Init`函数时，Can Controller所使用的硬件资源应该在这时被初始化，除了CAN使用的引脚（在Port初始化的时候被初始化）。**

#### 2.如果使用芯片外Can Controller，CAN模块可以使用其他MCAL驱动程序

**但是在使用其他模块的服务之前，必须确保这些服务的驱动已经被正确的初始化，并且已经正常运行。**

---

## 二、状态机

Can 模块有2个状态机，一个是模块整体的一个状态机，另一个是Can Controller的状态机。

### 1.Can 模块状态机

CAN模块有一个含两个状态的状态机，在上电或重置后，CAN模块进入**CAN_UNINIT**状态(未初始化状态)，随后初始化硬件单元的所有控制器后，CAN模块进入**CAN_READY**状态。这时就意味着CAN模块初始化完成了，可以执行一些与读，写，总线关闭，唤醒，设置控制器状态相关的功能。

![](https://s2.loli.net/2022/10/08/JZXAlCv7ITWj6zr.png)

### 2.Can Controller状态机

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
支持-- the CAN hardware is triggered to transition into SLEEP state, the Can module shall set the controller to the SLEEP state from which the hardware can be woken over CAN Bus.
不支持-- the CAN hardware is triggered to transition into SLEEP state, the Can module shall emulate a logical SLEEP state from which it returns only, when it is triggered by software to transition into STOPPED state.

---

## 三、Can 模块的工作模式

这里说明下CAN发送处理方式，有3种：

### （1）轮询模式（polling mode）

轮询模式下Can模块的发送确认被调度模块（BSW Scheduler）触发，执行随后的处理。比如设定CAN发送为轮询模式，那么Can_MainFunction_Write就会在预定义的时间间隔被周期性地调用（比如每几ms调用一次），然后Can_MainFunction_Write中再去调用上层的CAN Interface模块的CanIf_TxConfirmation模块，向上确认。

### （2）中断模式（interrupt mode）

中断模式下Can模块的发送确认由CAN控制器的中断触发，执行随后的处理，比如设定CAN发送为中断模式，那么中断触发后，调用Can模块的发送确认处理函数，在该函数中去调用Can Interface模块的CanIf_TxConfirmation函数，向上确认。

### （3）混合模式（mixed mode）

即（1）（2）的混合使用，看具体设定，是使用轮询模式还是使用中断模式的处理方式。

所以这里我选择的是轮询方式进行介绍。根据CanIf_TxConfirmation函数的输入参数可知，向上确认的是哪个CAN ID发送成功。

---

## 四、数据发送与接收

### 1.简介

介绍下**PDU**的概念，**PDU**包括协议控制信息（Protocol Control Information，PCI）和服务数据单元（Service Data Unit, **SDU**）。

其中**SDU**是指从上层模块传过来，要求发送的数据；或下层模块接收的，已提取好的数据，需要传给上层。

**PCI**是指需要将**SDU**从特定协议层的一个实例传递到另一实例。 例如。 它包含源和目标信息。**PCI**在发送方的协议层添加，再在接收方被删除。

通俗地理解就是：PDU不仅仅是数据（SDU），还携带了来自哪要去哪的信息（PCI）；每到哪都会删掉之前的信息，添加这是哪要去哪的信息（不同层的PCI更新），如下图所示。

![](https://s2.loli.net/2022/10/14/O8oMcas6iAETZBl.png)

### 2.发送操作

对于CanDrive来说，发送信号的要做两件事：一是先访问硬件，将数据写入寄存器；二是发送成功后，向上层CAN Interface模块确认。

针对写数据操作，CAN Interface模块调用Can_Write函数，其定义如下：

```c_cpp
Std_ReturnType Can_Write(Can_HwHandleType Hth, const Can_PduType* PduInfo)
```

输入参数1 Hth对应着使用哪个MO或发送邮箱进行发送，
输入参数2 PduInfoPtr是要发送的内容，包含的参数如代码中的结构体所示，有CAN ID、数据、长度以及PDU ID。

在发送该帧报文的时候会把**PDU ID**和**Hrh**的对应关系记录下来，在`Can_MainFunction_Write`函数中进行发送成功确认的时候，要使用相同的对应关系。

其中结构体`Can_PduType`定义如下：

```c_cpp
typedef struct
{
  PduIdType swPduHandle;
  uint8 length;
  Can_IdType id;
  uint8* sdu;
} Can_PduType;
```

`Can_Write`函数是供**CANIF**层发送报文调用的接口，在`CanIf_Transmit`函数中被调用。

每一个硬件对象都由ID，DLC和 SDU三部分组成。用来存储写入数据的硬件对象就叫Hth，用来存储接收数据的硬件对象就叫Hrh

![](https://s2.loli.net/2022/10/10/MCNpWDSJoeFzrwQ.png)

所以不难理解，Can_Write的输入参数**Hth**其实就是**硬件对象的编号（id）**,而且实际上是已经定义好了硬件对象的编号与CAN ID的映射关系，比如下图所示，如果想发送CAN ID 为0x001的数据，那么调用Can_Write函数的输入参数Hth应该为4.

![](https://s2.loli.net/2022/10/14/mATtkviHU7ylM4e.png)

**BSW调度器**会周期性调用`Can_MainFunction_Write`函数，

### 3.接收操作

从上面可知，CAN接收时，对于CAN模块同样有两个操作：一是先访问硬件，从寄存器提取数据；二是通知上层CAN Interface模块，传递数据。

针对读数据操作，首先BSW调度器周期性调用Can模块的`Can_MainFunction_Read`函数，其定义如下。

```c_cpp
void Can_MainFunction_Read(void)
```

同样地CAN接收处理方式有轮询，中断和混合模式 。这里选择的是轮询模式。

最后，数据成功提取后，`Can_MainFunction_Read`函数将通过调用`CanIf_RxIndication`函数通知**CAN Interface模块**，向上传递数据。

---

## 五、CAN Driver接口函数    

### 1、单控制器服务接口函数

#### 1）Can_Init函数

**描述：**

 `Can_Init`用于初始化Can模块。

**函数声明：**

```c_cpp
void Can_Init(const Can_ConfigType* Config)
```

**主要工作：**

初始化函数，输入为CAN模块配置结构体指针，在MCU启动时调用，初始化CAN模块的**时钟**、**波特率**、**引脚**、**接收发送邮箱**等一系列寄存器。完毕后将CAN控制器的状态由**CAN_CS_UNINIT**转换为**CAN_CS_STOPPED**。

---

#### 2）Can_SetBaudrate函数

**描述：**

此函数应设置Can Controller的波特率。根据必要的波特率修改，控制器可能需要重置。

**函数声明：**

```c_cpp
Std_ReturnType Can_SetBaudrate(uint8 Controller,uint16 BaudRateConfigID)
```

**主要工作：**

设置CAN模块某个Controller的波特率，输入为控制器ID和波特率ID，所有波特率要被定义为一个结构体，并分配ID，供波特率切换时调用。

---

#### 3）Can_SetControllerMode函数

**描述：**

函数设置Can Controller状态机的软件触发状态转换。

**函数声明：**

```c_cpp
Std_ReturnType Can_SetControllerMode(uint8 Controller,Can_ControllerStateType Transition)
```

**主要工作：**

转换某个CAN控制器的状态。CAN控制器在初始化完毕后处于**STOPPED**状态，要调用这个函数转换为**STARTED**状态后才能参与网络通信。

---

### 2、轮询函数

**轮询函数要在应用程序中被周期性调用。**

#### 1）Can_MainFunction_Write函数

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

#### 2）Can_MainFunction_Read函数

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

#### 3）Can_MainFunction_BusOff函数

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

### 3、主要功能函数

#### 1）Can_Write函数

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
