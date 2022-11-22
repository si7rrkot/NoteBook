# TestCmd message transmission

## 1.简介

本文仿照AUTOSAR中的Testability Protocol and Service Primitives来规定一套用于测试的数据交互的协议。

![](https://s2.loli.net/2022/11/02/Ntw1OzxEg6qRyvi.png)

通过串口收发数据，来传输测试命令以及返回测试执行结果。

<br/>

## 2.消息格式和协议字段

消息格式和序列化格式源自SOME/IP标准。我们根据SOME/IP标准进行修改而来的数据帧格式。

![](https://s2.loli.net/2022/11/10/nqAumeLV4Uh693v.png)

我们在其中只用关心GID、PID、TID和RID即可	

其中

|字段|名称|描述|
|--|--|--|
|GID|Service Group ID|就是我们测试的模块的ID（例如Can、Lin、Adc等）。|
|PID|Service Primitive ID|即是我们对应的测试案例的标识符，每个测试案例都有唯一的标识符。|
|TID|Message Type ID|选择是请求（request）、回应（response）或者是事件（event）。|
|RID|Result ID|发出请求结果的信号。|

### 1.Service Group ID

Server Group ID是测试的MCAL的模块，编号参考`MCAL适配通用测试用例时间计划.xlsx`。

|ID|名称|
|--|--|
|01|Port|
|02|Dio|
|03|Gpt|
|04|Can|
|05|Mcu|
|06|Icu|
|07|Spi|
|08|Fee|
|09|Fls|
|10|Eep|
|11|Lin|
|12|Pwm|
|13|Adc|
|14|Wdg|
|15|Eth|
|16|Ocu|
|17|Crypto|
|18|CoreTest|
|19|Fr|
|20|TTCan|
|21|WEth|
|22|Dma|
|23|Crc|
|24|I2c|

### 2.Service Primitive ID

这里把对应的测试用例编号对应到这里，每个模块的测试案例不同。

#### 1)Port Primitive

<br/>

#### 2)Dio Primitive

<br/>

#### 3)Gpt Primitive

<br/>

#### 4)Can Primitive

|测试标识符|ID|描述|
|--|--|--|
|Can_001|0x01|测试Can_Init是否能正常初始化|
|Can_002|0x02|测试Can_SetBaudrate()是否能设置波特率，并且接收返回值。|
|Can_003|0x03|测试Can_SetControllerMode修改Controller的状态。|
|...|||

#### 5)Mcu Primitive

<br/>

#### 6)Icu Primitive

<br/>

#### 7)Spi Primitive

<br/>

#### 8)Fee Primitive

<br/>

#### 9)Fls Primitive

<br/>

#### 10)Eep Primitive

<br/>

#### 11)Lin Primitive

<br/>

#### 12)Pwm Primitive

<br/>

#### 13)Adc Primitive

<br/>

#### 14)Wdg Primitive

<br/>

#### 15)Eth Primitive

<br/>

#### 16)Ocu Primitive

<br/>

#### 17)Crypto Primitive

<br/>

#### 18)CortTest Primitive

<br/>

#### 19)Fr Primitive

<br/>

#### 20)TTCan Primitive

<br/>

#### 21)WEth Primitive

<br/>

#### 22)Dma Primitive

<br/>

#### 23)Crc Primitive

<br/>

#### 24)I2C Primitive

### 3.Message Type ID

Message Type ID是指当前传输的数据的类型，是一条请求，或者是一条回应，亦或是事件。

|信息|TID|描述|
|--|--|--|
|Request|0x00|对应于非阻塞函数调用|
|Response|0x80|对应于请求后始终跟随的非阻塞函数返回|
|Event|0x02|对应于回调函数调用（EVB设置为1）|

### 4.Result ID

Result ID，一般来说通用的RID只有2个，包括**E_OK**和**E_NOT_OK**。剩下的根据各个测试模块的不同需要参考各个模块中SWS文档中错误类型定义。

|Result|ID|描述|
|--|--|--|
|E_OK|0x00|即当前测试成功执行。|
|E_NOT_OK|0x01|一般错误，当前测试执行失败。|
|0x02-0x7F||各个模块可根据AUTOSAR描述文档中指定对应的错误类型。|

#### 1)Port Result ID

<br/>

#### 2)Dio Result ID

<br/>

#### 3)Gpt Result ID

<br/>

#### 4)Can Result ID

|Result|ID|描述|
|--|--|--|
|CAN_E_PARAM_POINTER|0x02|参数检测指针错误。|
|CAN_E_PARAM_HANDLE|0x03|参数检测句柄错误。|
|CAN_E_PARAM_DATA_LENGTH|0x04|参数检测长度错误。|
|CAN_E_PARAM_CONTROLLER|0x05|参数检测控制器错误。|
|CAN_E_UNINIT|0x06|函数在Can模块未初始化时调用。|
|CAN_E_TRANSITION|0x07|当前模式转换无效。|
|CAN_E_PARAM_BAUDRATE|0x08|参数波特率的值无效。|
|CAN_E_ICOM_CONFIG_INVALID|0x09|ICOM配置Id无效。|
|CAN_E_INIT_FAILED|0x0A|配置集选择无效。|
|CAN_E_DATALOST|0x0B|接收到的CAN消息丢失。|

#### 5)Mcu Result ID

<br/>

#### 6)Icu Result ID

<br/>

#### 7)Spi Result ID

<br/>

#### 8)Fee Result ID

<br/>

#### 9)Fls Result ID

<br/>

#### 10)Eep Result ID

<br/>

#### 11)Lin Result ID

<br/>

#### 12)Pwm Result ID

<br/>

#### 13)Adc Result ID

<br/>

#### 14)Wdg Result ID

<br/>

#### 15)Eth Result ID

<br/>

#### 16)Ocu Result ID

<br/>

#### 17)Crypto Result ID

<br/>

#### 18)CortTest Result ID

<br/>

#### 19)Fr Result ID

<br/>

#### 20)TTCan Result ID

<br/>

#### 21)WEth Result ID

<br/>

#### 22)Dma Result ID

<br/>

#### 23)Crc Result ID

<br/>

#### 24)I2C Result ID
