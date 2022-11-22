# Eth

## 7.功能规范

[SWS_Eth_00003] 以太网驱动程序使用基于零的索引来抽象上层软件层的访问。配置中的参数Eth_CtrlIdx对应于API中使用的参数CtrlIdx。

[SWS_Eth_00004] 缓冲区索引（BufIdx）标识由以太网驱动程序API函数处理的以太网缓冲区。每个控制器的缓冲器由缓冲器索引0至（n-1）标识，其中n是由相应控制器处理的缓冲器的数目。缓冲区索引仅在元组＜CtrlIdx，BufIdx＞中有效。BufIdx唯一标识用于以太网驱动程序的缓冲区。

[SWS_Eth_00005] 以太网驱动程序模块应支持预编译时间、链接时间和构建后配置。

[SWS_Eth_00006] Eth.h头文件应包括软件和规范版本号。

[SWS_Eth_00007] 以太网驱动程序模块应基于对相关代码文件和头文件版本号的预处理检查，对代码文件和头部文件进行一致性检查。

[SWS_Eth_00008] 如果Ethernet Driver模块启用了开发错误检测：Ethernet驱动模块应检查API参数的有效性，并向DET报告检测到的错误。

[SWS_Eth_00011] 任何以太网驱动程序模块头文件都不应定义全局变量。

[SWS_Eth_00218] 以太网驱动器应确保所有接收和传输缓冲器的基址满足各自平台所有AUTOSAR数据类型的存储器对齐要求。

[SWS_Eth_00216] 对于传输，以太网控制器应启用硬件功能，以便根据以下列表计算协议校验和（卸载）：

- 对于IPv4帧，如果EthCtrlEnableOffloadChecksumIPv4设置为TRUE
- 对于ICMP帧，如果EthCtrlEnableOffloadChecksumICMP设置为TRUE
- 对于TCP帧，如果EthCtrlEnableOffloadChecksumTCP设置为TRUE
- 对于UDP帧，如果EthCtrlEnableOffloadChecksumUDP设置为TRUE。

[SWS_Eth_00217] 对于接收，以太网控制器应根据以下列表启用硬件能力，以丢弃协议校验和不匹配的帧（卸载）：

- 对于IPv4帧，如果EthCtrlEnableOffloadChecksumIPv4设置为TRUE
- 对于ICMP帧，如果EthCtrlEnableOffloadChecksumICMP设置为TRUE
- 对于TCP帧，如果EthCtrlEnableOffloadChecksumTCP设置为TRUE
- 对于UDP帧，如果EthCtrlEnableOffloadChecksumUDP设置为TRUE。

[SWS_Eth_00176] 全局时间接口应用于访问时间同步功能（参见文件[23]）。

[SWS_Eth_00243] 以太网软件驱动程序应调用EthIf_TxConfirmation，结果设置为E_OK，以指示传输成功；中断例程（在中断模式下）或轮询模式下的Eth_TxConfirmation例程（如果通知已启用）。

[SWS_Eth_00256] 如果传输失败，以太网软件驱动程序应调用EthIf_TxConfirmation，结果设置为E_NOT_OK。

[SWS_Eth_00244] 以太网软件驱动程序应调用EthIf_RxIndication，以指示从中断例程（在中断模式下）或从轮询模式下的Eth_Receive例程成功接收（请参阅SWC_Eth_0096）

[SWS_Eth_00247] 交换机驱动程序管理API：

- EthSwt_EthRxProcessFrame(),
- EthSwt_EthRxFinishedIndication(),
- EthSwt_EthTxPrepareFrame(),
- EthSwt_EthTxAdaptBufferLength(),
- EthSwt_EthTxProcessFrame() and
- EthSwt_EthTxFinishedIndication()

应用于通知switch 驱动开关管理所需的特殊处理。

[SWS_Eth_00012] 以太网驱动模块应提供包含软件标识(应包含供应商标识、模块ID和软件版本信息)、配置和集成过程所需数据的XML文件。
该文件应描述供应商特定的配置参数，并应包含建议的配置参数值。

[SWS_Eth_00125] MCG应读取以太网驱动器模块的ECU配置说明。以太网驱动程序相关配置数据包含在以太网驱动程序模块配置描述中。

[SWS_Eth_00126] MCG应确保生成的配置数据的一致性。

[SWS_Eth_00013] 以太网驱动模块的配置应在ECU配置时计算。任何通信参数都不应在运行时计算。

[SWS_Eth_00014] 构建后配置数据的起始地址应在模块初始化期间传递（见第8.3.1章）。

[SWS_Eth_00148] Eth.h应包括Eth_GeneralTypes.h，以包括SWS_Eth_00026中列出的一般Eth类型声明。

## 8.API 

### 1.Eth_Init

[SWS_Eth_00027]

|函数|Eth_Init|
|--|--|
|声明|void Eth_Init( const Eth_ConfigType* CfgPtr )|
|同步/异步|同步|
|可重入性|不可重入|
|参数（in）|CfgPtr：指向具体实现结构|
|参数（inout）|None|
|参数（out）|None|
|返回值|None|
|描述|初始化以太网驱动程序|

[SWS_Eth_00028] 函数Eth_Init应存储对配置结构的访问，以便后续API调用。

[SWS_Eth_00034] 函数Eth_Init应适用于当前EthConfigSet中所有已配置的以太网控制器：

- 禁用所有控制器
- 清除挂起的所有中断
- 配置所有控制器配置参数（例如中断、帧长度、帧过滤器等）
- 配置所有发送/接收资源（例如缓冲区初始化）
- 删除所有挂起的发送和接收请求

[SWS_Eth_00029] 函数Eth_Init应将组件的状态从ETH_STATE_UNIIT更改为ETH_STATE_INIT。

[SWS_Eth_00039] 该功能应检查对以太网控制器的访问。如果检查失败，该功能将引发生产错误ETH_E_ACCESS。

[SWS_Eth_00031] 注意：在初始化过程中必须调用Eth_Init。

<br/>

### 2.Eth_SetControllerMode

[SWS_Eth_00041]

|函数名|Eth_SetControllerMode|
|--|--|
|声明|Std_ReturnType Eth_SetControllerMode( uint8 CtrlIdx, Eth_ModeType CtrlMode )|
|同步/异步|异步|
|可重入性|不可重入|
|参数（in）|CtrlIdx：以太网驱动程序上下文中控制器的索引。</br>CtrlMode：</br>ETH_MODE_DOWN: 禁用controller </br>ETH_MODE_ACTIVE: 启用controller|
|参数（inout）|None|
|参数（out）|None|
|返回值|Std_ReturnType：E_OK: success </br>E_NOT_OK: controller 模式不能被更改|
|描述|使能/失能 CtrlIdx的控制器|

[SWS_Eth_00042] 函数Eth_SetControllerMode应该：

- 将控制器置于参数“CtrlMode”中给定的指定模式。
  - 在模式ETH_MODE_DOWN时，驱动器应：
    - 禁用以太网控制器
    - 复位所有发送和接收缓冲器（即忽略所有待处理的发送和接收请求）
  - 在模式ETH_MODE_ACTIVE时：
    - 使能所有发送和接收缓冲区
    - 启用以太网控制器

[SWS_Eth_00043] 如果启用了开发错误检测：该函数应检查Eth_Init是否先前被调用。如果检查失败，函数将引发开发错误ETH_E_UNIIT ，否则（如果DET被禁用）返回E_NOT_OK。

[SWS_Eth_00044] 如果启用了开发错误检测：函数应检查参数CtrlIdx是否有效。如果检查失败，函数将引发开发错误ETH_E_INV_CTRL_IDX ，否则（如果DET被禁用）返回E_NOT_OK。

[SWS_Eth_00168] 该功能应检查对以太网控制器的访问。如果检查失败，函数将引发生产错误ETH_E_ACCESS并返回E_NOT_OK。

[SWS_Eth_00045] 注意：该函数需要之前的控制器初始化（Eth_Init）。

<br/>

### 3.Eth_GetControllerMode

[]

|函数|Eth_GetControllerMode|
|--|--|
|声明|Std_ReturnType Eth_GetControllerMode( uint8 CtrlIdx, Eth_ModeType* CtrlModePtr )|
|同步/异步|同步|
|可重入性|不可重入|
|参数（in）|CtrlIdx：以太网驱动程序上下文中控制器的索引|
|参数（inout）|None|
|参数（out）|CtrlModePtr：</br>ETH_MODE_DOWN: 控制器被禁用 </br>ETH_MODE_ACTIVE: 控制器被启用|
|返回值|Std_ReturnType：</br>E_OK: success</br>E_NOT_OK: 无法获取控制器模式|
|描述|获取索引控制器的状态|

[SWS_Eth_00047] 函数Eth_GetControllerMode应读取当前控制器模式。

[SWS_Eth_00048] 如果启用了开发错误检测：该函数应检查Eth_Init是否先前被调用。如果检查失败，函数将引发开发错误ETH_E_UNIIT ，否则（如果DET被禁用）返回E_NOT_OK。

[SWS_Eth_00049] 如果启用了开发错误检测：函数应检查参数CtrlIdx是否有效。如果检查失败，函数将引发开发错误ETH_E_INV_CTRL_IDX ，否则（如果DET被禁用）返回E_NOT_OK。

[SWS_Eth_00050] 如果启用了开发错误检测：函数应检查参数CtrlModePtr是否有效。如果检查失败，函数应引发开发错误ETH_E_PARAM_POINTER，否则（如果DET被禁用）返回E_NOT_OK。

[SWS_Eth_00051] 注意：该函数需要之前的控制器初始化（Eth_Init）。

### 4.Eth_GetPhysAddr

[]

|函数|Eth_GetPhysAddr|
|--|--|
|声明|void Eth_GetPhysAddr( uint8 CtrlIdx, uint8* PhysAddrPtr )|
|同步/异步|同步|
|可重入性|不可重入|
|参数（in）|CtrlIdx：以太网驱动程序上下文中控制器的索引|
|参数（inout）|None|
|参数（out）|PhysAddrPtr：物理源地址（MAC） ，按网络字节顺序排列|
|返回值|None|
|描述|获取索引控制器使用的物理源地址|

[SWS_Eth_00053] 该函数应读取索引控制器使用的源地址。

[SWS_Eth_00054] 如果启用了开发错误检测：该函数应检查Eth_Init是否先前被调用。如果检查失败，函数将引发开发错误ETH_E_UNIIT。

[SWS_Eth_00055] 如果启用了开发错误检测：函数应检查参数CtrlIdx是否有效。如果检查失败，函数将引发开发错误ETH_E_INV_CTRL_IDX。

[SWS_Eth_00056] 如果启用了开发错误检测：函数应检查参数PhysAddrPtr是否有效。如果检查失败，函数将引发开发错误ETH_E_PARAM_POINTER。

[SWS_Eth_00057] 注意：该函数需要之前的控制器初始化（Eth_Init）。

<br/>

### 5.Eth_SetPhysAddr

[SWS_Eth_00151]

|函数|Eth_SetPhysAddr|
|--|--|
|声明|void Eth_SetPhysAddr( uint8 CtrlIdx, const uint8* PhysAddrPtr )|
|同步/异步|同步|
|可重入性|对于相同的CtrlIdx不可重入，对于不同的CtrlIdx可重入|
|参数（in）|CtrlIdx：EthernetDriver上下文中以太网控制器的索引。</br>PhysAddrPtr：指向按网络字节顺序包含物理源地址（MACaddress）的内存指针|
|参数（inout）|None|
|参数（out）|None|
|返回值|None|
|描述|设置索引控制器使用的物理地址|

[SWS_Eth_00139] 函数PhysAddrPtr应更新索引控制器使用的源地址。

[SWS_Eth_00140] 如果启用了开发错误检测：该函数应检查Eth_Init是否先前被调用。如果检查失败，函数将引发开发错误ETH_E_UNIIT。

[SWS_Eth_00141] 如果启用了开发错误检测：函数应检查参数CtrlIdx是否有效。如果检查失败，函数将引发开发错误ETH_E_INV_CTRL_IDX。

[SWS_Eth_00142] 如果启用了开发错误检测：函数应检查参数PhysAddrPtr是否有效。如果检查失败，函数将引发开发错误ETH_E_PARAM_POINTER。

[SWS_Eth_00143] 注意：该函数需要之前的控制器初始化（Eth_Init）。

### 6.Eth_UpdatePhysAddrFilter

[SWS_Eth_00152]

|函数|Eth_UpdatePhysAddrFilter|
|--|--|
|声明|Std_ReturnType Eth_UpdatePhysAddrFilter( uint8 CtrlIdx, const uint8* PhysAddrPtr, Eth_FilterActionType Action )|
|同步/异步|同步|
|可重入性|对于相同的CtrlIdx不可重入，对于不同的CtrlIdx可重入|
|参数（in）|CtrlIdx：EthernetDriver上下文中以太网控制器的索引。</br>PhysAddrPtr：指向内存的指针，包含按网络字节顺序排列的物理目的地址（MAC地址）。这是第2层以太网数据包的组播目的地址。</br>Action：从以太网控制器过滤器中添加或删除地址。|
|参数（inout）|None|
|参数（out）|None|
|返回值|Std_ReturnType：</br>E_OK: filter was successfully changed </br>E_NOT_OK: filter could not be changed|
|描述|将物理源地址更新到索引控制器筛选器/从索引控制器筛选器更新。如果以太网控制器无法执行过滤，则软件必须执行此操作。|

[SWS_Eth_00150] 该功能应更新索引控制器的物理地址接收过滤器。

[SWS_Eth_00245] 当目标地址与此处传递的PhyAddrPtr匹配时，以太网驱动程序模块将接收帧。（例如，匹配可以通过哈希表或简单模式匹配完成）

[SWS_Eth_00246] 如果匹配为正，则调用RxIndication()回调通知上层，如果匹配为负，则丢弃帧。

[SWS_Eth_00164] 如果启用了开发错误检测：该函数应检查Eth_Init是否先前被调用。如果检查失败，函数将引发开发错误ETH_E_UNIIT。

[SWS_Eth_00165] 如果启用了开发错误检测：函数应检查参数CtrlIdx是否有效。如果检查失败，函数将引发开发错误ETH_E_INV_CTRL_IDX。

[SWS_Eth_00166] 如果启用了开发错误检测，则该函数应检查参数PhysAddrPtr是否有效。如果检查失败，函数将引发开发错误ETH_E_PARAM_POINTER。

[SWS_Eth_00167] 注意：该函数需要之前的控制器初始化（Eth_Init）。

[SWS_Eth_00144] 如果物理源地址（ MAC地址）配置为FF:FF:FF : FF:FF:FF，这将完全打开过滤器。

[SWS_Eth_00146] 如果使用该接口，且硬件不支持过滤，则初始化时需要启用混杂模式。

[SWS_Eth_00147] 如果物理源地址（ MAC地址）配置为00:00:00 : 00:00:00，这将使过滤器减少到控制器唯一的单播MAC地址，并在打开时结束混杂模式。

### 7.Eth_WriteMii

[SWS_Eth_00058]

|函数|Eth_WriteMii|
|--|--|
|声明|Std_ReturnType Eth_WriteMii( uint8 CtrlIdx, uint8 TrcvIdx, uint8 RegIdx, uint16 RegVal )|
|同步/异步|异步|
|可重入性|不可重入|
|参数（in）|CtrlIdx：以太网驱动程序上下文中控制器的索引</br>TrcvIdx：MII上收发器的索引（详见【21】 ）</br>RegIdx：MII收发器寄存器索引（详见【21】 ）</br>RegVal：要写入索引寄存器的值（有关详细信息，请参见【21】 ）|
|参数（inout）|None|
|参数（out）|None|
|返回值|Std_ReturnType：<br/>E_OK: Service accepted <br/>E_NOT_OK: Service denied|
|描述|配置收发器寄存器或触发接收器提供的功能|

[SWS_Eth_00059] 该函数应通过索引控制器的MII写入指定的收发器寄存器。

[SWS_Eth_00241] 当MII访问完成时，函数应调用EthTrcv_WriteMiiIndication。

[SWS_Eth_00060] 如果启用了开发错误检测：该函数应检查Eth_Init是否先前被调用。如果检查失败，函数将引发开发错误ETH_E_UNIIT。

[SWS_Eth_00061] 如果启用了开发错误检测：函数应检查参数CtrlIdx是否有效。如果检查失败，函数将引发开发错误ETH_E_INV_CTRL_IDX。

[SWS_Eth_00062] 该函数应为预编译时间，可通过配置参数配置开/关： EthCtrl启用Mii。

[SWS_Eth_00063] 注意：该函数需要之前的控制器初始化（Eth_Init）。

<br/>

### 8.Eth_ReadMii

[SWS_Eth_00064]

|函数|Eth_ReadMii|
|--|--|
|声明|Std_ReturnType Eth_ReadMii( uint8 CtrlIdx, uint8 TrcvIdx, uint8 RegIdx, uint16* RegValPtr)|
|同步/异步|异步|
|可重入性|不可重入|
|参数（in）|CtrlIdx：以太网驱动程序上下文中控制器的索引</br>TrcvIdx：MII上收发器的索引（详见【21】 ）</br>RegIdx：MII收发器寄存器索引（详见【21】 ）|
|参数（inout）|None|
|参数（out）|RegValPtr：填充索引寄存器的寄存器内容(详情见[21])|
|返回值|Std_ReturnType：<br/>E_OK: Service accepted <br/>E_NOT_OK: Service denied|
|描述|读取收发器寄存器|

[SWS_Eth_00065] 该函数应通过索引控制器的MII读取指定的收发器寄存器。

[SWS_Eth_00242] 当MII访问完成时，函数应调用EthTrcv_ReadMiiIndication。

[SWS_Eth_00066] 如果启用了开发错误检测：该函数应检查Eth_Init是否先前被调用。如果检查失败，函数将引发开发错误ETH_E_UNIIT。

[SWS_Eth_00067] 如果启用了开发错误检测：函数应检查参数CtrlIdx是否有效。如果检查失败，函数将引发开发错误ETH_E_INV_CTRL_IDX。

[SWS_Eth_00068] 如果启用了开发错误检测：函数应检查参数RegValPtr是否有效。如果检查失败，函数将引发开发错误ETH_E_PARAM_POINTER。

[SWS_Eth_00069] 该函数应为预编译时间，可通过配置参数配置开/关： EthCtrl启用Mii。

[SWS_Eth_00070] 注意：该函数需要之前的控制器初始化（Eth_Init）。

### 9.Eth_GetCounterValues

[SWS_Eth_00226]

|函数|Eth_GetCounterValues|
|--|--|
|声明|Std_ReturnType Eth_GetCounterValues( uint8 CtrlIdx, Eth_CounterType* CounterPtr )|
|同步/异步|同步|
|可重入性|不可重入|
|参数（in）|CtrlIdx：EthernetDriver上下文中控制器的索引|
|参数（inout）|None|
|参数（out）|CounterPtr：根据IETF RFC 1757、RFC 1643和RFC 2233的计数器值。|
|返回值|Std_ReturnType：E_OK: success E_NOT_OK: counter values read failure|
|描述|读取包含相应控制器丢弃计数器值的列表。这些值的含义在Eth_CounterType中描述。|

[SWS_Eth_00227] 该函数应从索引控制器读取值列表。

[SWS_Eth_00228] 如果启用了开发错误检测：该函数应检查Eth_Init是否先前被调用。如果检查失败，函数将引发开发错误ETH_E_UNIIT ，否则（如果DET被禁用）返回E_NOT_OK。

[SWS_Eth_00229] 如果启用了开发开发私奔错误检测：函数应检查参数CtrlIdx是否有效。如果检查失败，函数将引发开发错误ETH_E_INV_CTRL_IDX ，否则（如果DET被禁用） 返回E_NOT_OK。

[SWS_Eth_00230] 如果启用了开发错误检测：函数应检查参数CounterPtr是否有效。如果检查失败，函数应引发开发错误ETH_E_PARAM_POINTER ，否则（如果DET被禁用）返回E_NOT_OK。

[SWS_Eth_00231] 函数Eth_GetCounterValues应为预编译时间，可通过配置参数配置On/Off : EthGetCounterValuesApi。

[SWS_Eth_00232] 注意：该函数需要之前的控制器初始化（Eth_Init）。

### 10.Eth_GetRxStats

[SWS_Eth_00233]

|函数|Eth_GetRxStats|
|--|--|
|声明|Std_ReturnType Eth_GetRxStats( uint8 CtrlIdx, Eth_RxStatsType* RxStats )|
|同步/异步|同步|
|可重入性|不可重入|
|参数（in）|CtrlIdx：以太网驱动程序上下文中控制器的索引|
|参数（inout）|None|
|参数（out）|RxStats：根据IETF RFC 2819 （远程NetworkMonitoring管理信息库）的值列表|
|返回值|Std_ReturnType：<br/>E_OK: success <br/>E_NOT_OK: drop counter could not be obtained|
|描述|根据IETF RFC2819返回以下列表，其中最大可能值应表示无效值，例如，如果此计数器不可用：<br/>1. etherStatsDropEvents <br/>2. etherStatsOctets <br/>3. etherStatsPkts <br/>4. etherStatsBroadcastPkts <br/>5. etherStatsMulticastPkts <br/>6. etherStatsCrcAlignErrors <br/>7. etherStatsUndersizePkts <br/>8. etherStatsOversizePkts <br/>9. etherStatsFragments <br/>10. etherStatsJabbers <br/>11. etherStatsCollisions <br/>12. etherStatsPkts64Octets <br/>13. etherStatsPkts65to127Octets <br/>14. etherStatsPkts128to255Octets <br/>15. etherStatsPkts256to511Octets <br/>16. etherStatsPkts512to1023Octets <br/>17. etherStatsPkts1024to1518Octets|

[SWS_Eth_00234] 函数Eth_GetRxStats应根据【22】从索引控制器读取值列表。

[SWS_Eth_00235] 如果启用了开发错误检测：该函数应检查Eth_Init是否先前被调用。如果检查失败，函数将引发开发错误ETH_E_UNIIT ，否则（如果DET被禁用）返回E_NOT_OK。

[SWS_Eth_00236] 如果启用了开发错误检测：函数应检查参数CtrlIdx是否有效。如果检查失败，函数将引发开发错误ETH_E_INV_CTRL_IDX ，否则（如果DET被禁用）返回E_NOT_OK。

[SWS_Eth_00237] 如果启用了开发错误检测：函数应检查参数RxStats是否有效。如果检查失败，函数应引发开发错误ETH_E_PARAM_POINTER ，否则（如果DET被禁用）返回E_NOT_OK。

[SWS_Eth_00238] 函数Eth_GetRxStats应为预编译时间，可通过配置参数配置On/Off: EthGetRxStatsApi。

### 11.Eth_GetTxStats

[SWS_Eth_91005]

|函数|Eth_GetTxStats|
|--|--|
|声明|Std_ReturnType Eth_GetTxStats( uint8 CtrlIdx, Eth_TxStatsType* TxStats )|
|同步/异步|同步|
|可重入性|不可重入|
|参数（in）|CtrlIdx：EthernetDriver上下文中控制器的索引|
|参数（inout）|None|
|参数（out）|TxStats：要读取用于传输的统计值的值列表。|
|返回值|Std_ReturnType：<br/>E_OK: success<br/>E_NOTOK: Tx-statistics could not be obtained|
|描述|返回用Eth_TxStatsType定义的IETF RFC1213中的传输统计信息列表，其中最大可能值应表示无效值，例如，此计数器不可用。|

[SWS_Eth_00248] 如果启用了开发错误检测：该函数应检查Eth_Init是否先前被调用。如果检查失败，函数将引发开发错误ETH_E_UNIIT ，否则（如果DET被禁用）返回E_NOT_OK。

[SWS_Eth_00249] 如果启用了开发错误检测：函数应检查参数CtrlIdx是否有效。如果检查失败，函数将引发开发错误ETH_E_INV_CTRL_IDX ，否则（如果DET被禁用）返回E_NOT_OK。

[SWS_Eth_00250] 如果启用了开发错误检测：函数应检查参数TxStats是否有效。如果检查失败，函数应引发开发错误ETH_E_PARAM_POINTER ，否则（如果DET被禁用）返回E_NOT_OK。

[SWS_Eth_00251] 函数Eth_GetTxStats应为预编译时间，可通过配置参数配置On/Off: EthGetTxStatsApi。

### 12.Eth_GetTxErrorCounterValues

[SWS_Eth_91006]

|函数|Eth_GetTxErrorCounterValues|
|--|--|
|声明|Std_ReturnType Eth_GetTxErrorCounterValues(uint8 CtrlIdx, Eth_TxErrorCounterValuesType* TxErrorCounterValues )|
|同步/异步|同步|
|可重入性|不可重入|
|参数（in）|CtrlIdx：以太网驱动程序上下文中控制器的索引|
|参数（inout）|None|
|参数（out）|TxErrorCounterValues：要读取用于传输的统计错误计数器值的值列表。|
|返回值|Std_ReturnType：<br/>E_OK: success,<br/>E_NOTOK: Tx-statistics could not be obtained|
|描述|返回使用Eth_TxErrorCounterValuesType定义的IETF RFC1213和RFC1643中的传输错误计数器列表，其中最大可能值应表示无效值，例如，此计数器不可用。|

[SWS_Eth_00252] 如果启用了开发错误检测：该函数应检查Eth_Init是否先前被调用。如果检查失败，函数将引发开发错误ETH_E_UNIIT ，否则（如果DET被禁用）返回E_NOT_OK。

[SWS_Eth_00253] 如果启用了开发错误检测：函数应检查参数CtrlIdx是否有效。如果检查失败，函数将引发开发错误ETH_E_INV_CTRL_IDX ，否则（如果DET被禁用）返回E_NOT_OK。

[SWS_Eth_00254] 如果启用了开发错误检测：函数应检查参数TxStats是否有效。如果检查失败，函数应引发开发错误ETH_E_PARAM_POINTER ，否则（如果DET被禁用）返回E_NOT_OK。

[SWS_Eth_00255] 函数Eth_GetTxErrorCounterValues应为预编译时间，可通过配置参数配置On/Off : EthGetTx错误计数器值Api。

### 13.Eth_GetCurrentTime

[SWS_Eth_00181]

|函数|Eth_GetCurrentTime|
|--|--|
|声明|Std_ReturnType Eth_GetCurrentTime( uint8 CtrlIdx, Eth_TimeStampQualType* timeQualPtr, Eth_TimeStampType* timeStampPtr)|
|同步/异步|同步|
|可重入性|不可重入|
|参数（in）|CtrlIdx：ETH控制器地址索引。|
|参数（inout）|None|
|参数（out）|timeQualPtr：硬件时间戳的质量，例如基于电流漂移<br/>timeStampPtr：当前时间戳|
|返回值|Std_ReturnType：<br/>E_OK: successful <br/>E_NOT_OK: failed|
|描述|根据HW的能力从HW寄存器中返回一个时间值。HW分辨率是否低于Eth_TimeStampType分辨率resp。范围，比a剩余位将填充0。|

[SWS_Eth_00182] 如果启用了开发错误检测：该函数应检查Eth_Init是否先前被调用。如果检查失败，函数将引发开发错误ETH_E_UNIIT。

[SWS_Eth_00183] 如果启用了开发错误检测：函数应检查参数CtrlIdx是否有效。如果检查失败，函数将引发开发错误ETH_E_INV_CTRL_IDX。

[SWS_Eth_00184] 如果启用了开发错误检测：函数应检查参数timeQualPtr和timeStampPtr是否有效。如果检查不通过，函数将引发开发错误ETH_E_PARAM_POINTER。

[SWS_Eth_00210] 该函数应为预编译时间，可通过配置参数配置开/关： Eth全局时间支持。

[SWS_Eth_00185] 注意：该函数需要之前的控制器初始化（Eth_Init）。

### 14.Eth_EnableEgressTimeStamp

[SWS_Eth_00186]

|函数|Eth_EnableEgressTimeStamp|
|--|--|
|声明|void Eth_EnableEgressTimeStamp( uint8 CtrlIdx, Eth_BufIdxType BufIdx )|
|同步/异步|同步|
|可重入性|不可重入|
|参数（in）|CtrlIdx：ETH控制器地址索引。<br/>BufIdx：消息缓冲区的索引，应用程序希望在其中设置出口时间戳|
|参数（inout）|None|
|参数（out）|None|
|返回值|None|
|描述|激活专用消息对象上的出口时间戳。某些硬件确实存储了一次出口时间戳标记，而某些硬件在传输之前总是需要它。由于消息类型始终被网络设计“时间戳”，因此不会有“禁用”功能。|

[SWS_Eth_00187] 如果启用了开发错误检测：该函数应检查Eth_Init是否先前被调用。如果检查失败，函数将引发开发错误ETH_E_UNIIT。

[SWS_Eth_00188] 如果启用了开发错误检测：函数应检查参数CtrlIdx是否有效。如果检查失败，函数将引发开发错误ETH_E_INV_CTRL_IDX。

[SWS_Eth_00211] 该函数应为预编译时间，可通过配置参数配置开/关： Eth全局时间支持。

[SWS_Eth_00189] 注意：该函数需要之前的控制器初始化（Eth_Init）。

### 15.Eth_GetEgressTimeStamp

[SWS_Eth_00190]

|函数|Eth_GetEgressTimeStamp|
|--|--|
|声明|void Eth_GetEgressTimeStamp( uint8 CtrlIdx, Eth_BufIdxType BufIdx, Eth_TimeStampQualType\* timeQualPtr, Eth_TimeStampType* timeStampPtr )|
|同步/异步|同步|
|可重入性|不可重入|
|参数（in）|CtrlIdx：ETH控制器地址索引。<br/>BufIdx：消息缓冲区的索引，应用程序希望在其中设置出口时间戳|
|参数（inout）|None|
|参数（out）|timeQualPtr：硬件时间戳的质量，例如基于电流漂移<br/>timeStampPtr：当前时间戳|
|返回值|None|
|描述|回读专用消息对象上的出口时间戳。它必须在TxCon Confirmation()函数中调用。|

[SWS_Eth_00191] 如果启用了开发错误检测：该函数应检查Eth_Init是否先前被调用。如果检查失败，函数将引发开发错误ETH_E_UNIIT。

[SWS_Eth_00192] 如果启用了开发错误检测：函数应检查参数CtrlIdx是否有效。如果检查失败，函数将引发开发错误ETH_E_INV_CTRL_IDX。

[SWS_Eth_00193] 如果启用了开发错误检测：函数应检查参数timeQualPtr和timeStampPtr是否有效。如果检查不通过，函数将引发开发错误ETH_E_PARAM_POINTER。

[SWS_Eth_00212] 该函数应为预编译时间，可通过配置参数配置开/关： Eth全局时间支持。

[SWS_Eth_00194] 注意：该函数需要之前的控制器初始化（Eth_Init）。

### 	16.Eth_GetIngressTimeStamp

[SWS_Eth_00195]

|函数|Eth_GetIngressTimeStamp|
|--|--|
|声明|void Eth_GetIngressTimeStamp( uint8 CtrlIdx, const Eth_DataType\* DataPtr, Eth_TimeStampQualType\* timeQualPtr, Eth_TimeStampType* timeStampPtr )|
|同步/异步|同步|
|可重入性|不可重入|
|参数（in）|CtrlIdx：ETH控制器地址索引。<br/>DataPtr：指向消息缓冲区的指针，应用程序希望在其中设置Ingresstime戳记|
|参数（inout）|None|
|参数（out）|timeQualPtr：硬件时间戳的质量，例如基于电流漂移<br/>timeStampPtr：当前时间戳|
|返回值|None|
|描述|回读专用消息对象上的入口时间戳。它必须在RxIndication()函数中调用。|

[SWS_Eth_00196] 如果启用了开发错误检测：该函数应检查Eth_Init是否先前被调用。如果检查失败，函数将引发开发错误ETH_E_UNIIT。

[SWS_Eth_00197] 如果启用了开发错误检测：函数应检查参数CtrlIdx是否有效。如果检查失败，函数将引发开发错误ETH_E_INV_CTRL_IDX。

[SWS_Eth_00198] 如果启用了开发错误检测：函数应检查参数DataPtr、timeQualPtr和timeStampPtr是否有效。如果检查失败，函数将引发开发错误ETH_E_PARAM_POINTER。

[SWS_Eth_00213] 该函数应为预编译时间，可通过配置参数配置开/关： Eth全局时间支持。

[SWS_Eth_00199] 注意：该函数需要之前的控制器初始化（Eth_Init）。

### 17.Eth_ProvideTxBuffer

[SWS_Eth_00077]

|函数|Eth_ProvideTxBuffer|
|--|--|
|声明|BufReq_ReturnType Eth_ProvideTxBuffer( uint8 CtrlIdx, uint8 Priority, Eth_BufIdxType\* BufIdxPtr, uint8\*\* BufPtr, uint16* LenBytePtr )|
|同步/异步|同步|
|可重入性|可重入|
|参数（in）|CtrlIdx：EthernetDriver上下文中控制器的索引<br/>Priority：发送缓冲区FIFO选择帧优先级|
|参数（inout）|LenBytePtr：输入：所需长度（以字节为单位），输出：授予的长度（以字节为单位）|
|参数（out）|BufIdxPtr：授予的缓冲区资源的索引。用于后续请求<br/>BufPtr：指向授予缓冲区的指针|
|返回值|BufReq_ReturnType：<br/>BUFREQ_OK：成功<br/>BUFREQ_E_NOT_OK ：检测到开发错误<br/>BUFREQ_E_BUSY：所有正在使用的缓冲区<br/>BUFREQ_E_OVFL ：请求的缓冲区太大|
|描述|提供对与指定优先级相关的FIFO的发送缓冲器的访问|

[SWS_Eth_00078] 该功能应提供发送缓冲区资源。以太网驱动程序应锁定缓冲区，直到它收到后续的Eth_Transmit服务调用，并在BufIdxPtr参数中返回bufferindex。

[SWS_Eth_00137] 如果控制器通过Eth_SetControllerMode禁用，则应释放所有锁定的发送缓冲器。

[SWS_Eth_00079] 如果使用Eth_ProvideTxBuffer请求的缓冲区大于可用缓冲区长度，则不应锁定缓冲区，而是返回可用长度和BFREQ_E_OVFL。

[SWS_Eth_00080] 如果所有可用缓冲器都在使用中，则组件应返回BUFREQ_E_BUSY。

[SWS_Eth_00081] 如果启用了开发错误检测：该函数应检查Eth_Init是否先前被调用。如果检查失败，函数将引发开发错误ETH_E_UNIIT并返回BUFREQ_E_NOT_OK。

[SWS_Eth_00082] 如果启用了开发错误检测：函数应检查参数CtrlIdx是否有效。如果检查失败，函数将引发开发错误ETH_E_INV_CTRL_IDX并返回BUFREQ_E_NOT_OK。

[SWS_Eth_00083] 如果启用了开发错误检测：函数应检查参数BufIdxPtr是否有效。如果检查失败，函数应引发开发错误ETH_E_PARAM_POINTER并返回BUFREQ_E_NOT_OK。

[SWS_Eth_00084] 如果启用了开发错误检测：函数应检查参数BufPtr是否有效。如果检查失败，函数将引发开发错误ETH_E_PARAM_PointER并返回BUFREQ_E_NOT_OK。

[SWS_Eth_00085] 如果启用了开发错误检测：函数应检查参数LenBytePtr是否有效。如果检查失败，函数应引发开发错误ETH_E_PARAM_POINTER并返回BUFREQ_E_NOT_OK。

[SWS_Eth_00086] 注意：该函数需要之前的控制器初始化（Eth_Init）。

### 18.Eth_Transmit

[SWS_Eth_00087]

|函数|Eth_Transmit|
|--|--|
|声明|Std_ReturnType Eth_Transmit( uint8 CtrlIdx, Eth_BufIdxType BufIdx, Eth_FrameType FrameType, boolean TxConfirmation, uint16 LenByte, const uint8* PhysAddrPtr )|
|同步/异步|同步|
|可重入性|不同缓冲区索引和Ctrl索引的可重入|
|参数（in）|CtrlIdx：以太网驱动程序上下文中控制器的索引<br/>BufIdx：缓冲区资源索引<br/>FrameType：以太网帧类型<br/>TxConfirmation：激活传输确认<br/>LenByte：以字节为单位的数据长度<br/>PhysAddrPtr：网络字节顺序的物理目标地址（MAC地址）|
|参数（inout）|None|
|参数（out）|None|
|返回值|Std_ReturnType：<br/>E_OK: success <br/>E_NOT_OK: transmission failed|
|描述|触发先前填充的发送缓冲器的传输|

[SWS_Eth_00088]函数Eth_Transmit应使用给定的物理目标地址（MAC地址）构建以太网报头，并触发先前填充的发送缓冲器的传输。

[SWS_Eth_00089] 如果TxConfirmation为false ，则函数应释放缓冲区资源。

[SWS_Eth_00138] 如果控制器通过Eth_SetControllerMode禁用，则应释放所有待处理的发送缓冲器。

[SWS_Eth_00090] 如果启用了开发错误检测：该函数应检查Eth_Init是否先前被调用。如果检查失败，函数将引发开发错误ETH_E_UNIIT ，否则（如果DET被禁用）返回E_NOT_OK。

[SWS_Eth_00091] 如果启用了开发错误检测：函数应检查参数CtrlIdx是否有效。如果检查失败，函数将引发开发错误ETH_E_INV_CTRL_IDX ，否则（如果DET被禁用）返回E_NOT_OK。

[SWS_Eth_00092] 如果启用了开发错误检测：函数应检查参数BufIdx是否有效。如果检查失败，函数将引发开发错误ETH_E_INV_PARAM ，否则（如果DET被禁用）返回E_NOT_OK。

[SWS_Eth_00093] 如果启用了开发错误检测：函数应检查参数PhysAddrPtr是否有效。如果检查失败，函数将引发开发错误ETH_E_PARAM_PointER ，否则（如果DET被禁用） 返回E_NOT_OK。

[SWS_Eth_00129] 如果启用了开发错误检测：该函数应检查控制器模式是否处于活动状态（ETH_MODE_Active）。如果检查失败，该函数应引发开发错误ETH_E_INV_MODE ，否则（如果DET被禁用）返回E_NOT_OK。

[SWS_Eth_00094] 注意：该函数需要之前的缓冲区请求（Eth_ProvideTxBuffer）。

### 19.Eth_Receive

[SWS_Eth_00095]

|函数|Eth_Receive|
|--|--|
|声明|void Eth_Receive( uint8 CtrlIdx, uint8 FifoIdx, Eth_RxStatusType* RxStatusPtr )|
|同步/异步|同步|
|可重入性|不同FIFO的可重入。同一FIFO的不可重入。|
|参数（in）|CtrlIdx：以太网驱动程序上下文中控制器的索引<br/>FifoIdx：指定相关的fifo|
|参数（inout）|None|
|参数（out）|RxStatusPtr：指示是否已接收帧，如果已接收，则是否有更多帧可用于相关FIFO。|
|返回值|None|
|描述|从相关fifo接收帧。|

[SWS_Eth_00096] 该函数应从接收缓冲器读取下一帧。该函数使用回调函数EthIf_RxIndication将接收到的帧传递到以太网接口，并指示接收缓冲区中是否有更多帧。

[SWS_Eth_00097] 如果启用了开发错误检测：该函数应检查Eth_Init是否先前被调用。如果检查失败，函数将引发开发错误ETH_E_UNIIT。

[SWS_Eth_00098] 如果启用了开发错误检测：函数应检查参数CtrlIdx是否有效。如果检查失败，函数将引发开发错误ETH_E_INV_CTRL_IDX。

[SWS_Eth_00132] 如果启用了开发错误检测：该函数应检查控制器模式是否处于活动状态（ETH_MODE_Active）。如果检查失败，该函数将引发开发错误ETH_E_INV_MODE。

[SWS_Eth_00153] 当调用回调函数EthIf_RxIndication时，广播帧应指示到以太网接口（参见【6】 ）。

[SWS_Eth_00099] 注意：该函数需要之前的控制器初始化（Eth_Init）。

### 20.Eth_TxConfirmation

///

[SWS_Eth_00101] Eth_TxConfirmation应检查所有已填充的传输缓冲器是否成功传输。如果Eth_Transmitservice的上一个调用请求，该函数使用回调函数EthIf_TxConceration为每个传输帧发出发送确认。

[SWS_Eth_00102] 如果之前调用Eth_Transmit函数启用了传输确认，则该函数应释放缓冲区资源。

[SWS_Eth_00103] 如果启用了开发错误检测：该函数应检查Eth_Init是否先前被调用。如果检查失败，函数将引发开发错误ETH_E_UNIIT。

[SWS_Eth_00104] 如果启用了开发错误检测：函数应检查参数CtrlIdx是否有效。如果检查失败，函数将引发开发错误ETH_E_INV_CTRL_IDX。

[SWS_Eth_00134] 如果启用了开发错误检测：该函数应检查控制器模式是否处于活动状态（ETH_MODE_Active）。如果检查失败，该函数将引发开发错误ETH_E_INV_MODE。

[SWS_Eth_00105] 注意：该函数需要之前的初始化（Eth_Init）。

### 21.Eth_GetVersionInfo

///

[SWS_Eth_00136] 如果启用了开发错误检测：函数应检查参数VersionInfoPtr是否有效。如果检查失败，函数将引发开发错误ETH_E_PARAM_POINTER。

### 22.Eth_MainFunction

///

[SWS_Eth_00169] 该功能应检查丢失的帧。如果检查失败，函数应引发扩展生产错误事件ETH_E_RX_FRAMES_LOST。

[SWS_Eth_00172] 该功能应检查控制器错误（例如CRC错误）。如果检查失败，该功能应引发第7.2.2节扩展生产错误（例如ETH_E_CRC ）中定义的扩展生产错误事件。

[SWS_Eth_00240] 用于轮询状态更改。当控制器模式更改时，调用EthIf_CtrlModeIndication。