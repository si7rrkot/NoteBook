# ADC

[SWS_Adc_00090] ADC模块应允许将一个或多个ADC通道分组为ADC通道组。

[SWS_Adc_00091] ADC模块的配置应确保ADC信道组至少包含一个ADC信道。

[SWS_Adc_00451] ADC模块的配置应确保，如果启用了全局限值检查功能，并且ADC通道启用了通道特定限值检查，则ADC通道组正好包含一个ADC通道。

[SWS_Adc_00092] ADC模块应允许将一个ADC通道分配给多个组。

[SWS_Adc_00277] ADC模块的配置应确保一个ADC信道组中包含的所有信道应属于同一ADC硬件单元。

[SWS_Adc_00380] ADC模块应支持所有ADC信道组的转换模式“One-shot Conversion”。一次转换意味着为要转换的组配置的每个通道只执行一次转换。

[SWS_Adc_00381] ADC模块应支持所有带触发源软件的ADC通道组的转换模式“Continuous Conversion”。“Continuous Conversion”是指转换完成后，重复整个组的转换。组内单个ADC通道的转换以及整个组的重复不需要执行任何额外的触发事件。根据硬件和/或软件功能，可以顺序或并行转换组中的各个通道。

[SWS_Adc_00356] ADC模块应支持所有转换模式的启动条件“Software API Call”。触发源“Software API Call”表示通过ADC模块提供的服务启动/停止ADC通道组的转换。

[SWS_Adc_00357] ADC模块应支持以一次性转换模式配置的组的启动条件“Hardware Event”。触发源“硬件事件”是指ADC通道组的转换可以由硬件事件启动，例如在输入线上检测到的过期计时器或边缘。

[SWS_Adc_00279] ADC模块应允许为每个ADC信道组准确配置一个触发源。

[SWS_Adc_00382] ADC模块应支持使用API函数ADC_GetStreamLastPointer访问结果。调用Adc_GetStreamLastPointer将通知用户最近一轮转换的组转换结果在结果缓冲区中的位置，以及结果缓冲区内有效转换结果的数量。结果缓冲区是应用程序提供的外部缓冲区。

[SWS_Adc_00383] 如果此API函数的生成是静态配置的，则ADC模块应支持使用API函数ADC_ReadGroup进行结果访问。调用Adc_ReadGroup会将最近一轮转换的组转换结果复制到应用程序缓冲区，该缓冲区的起始地址指定为Adc_ReardGroup的API参数。

[SWS_Adc_00140] ADC模块应保证每个完成转换的返回结果值的一致性。

[SWS_Adc_00384] ADC模块的环境应确保在请求转换结果之前，已为请求的组完成转换。

[SWS_Adc_00288] ADC模块应允许为每个信道组配置优先级。

[SWS_Adc_00310] ADC模块的优先级机制应允许中止和重新启动信道组转换。

[SWS_Adc_00345] ADC模块的优先级机制应允许暂停和恢复信道组转换。

[SWS_Adc_00430] ADC模块应允许组特定配置，无论中断信道组是否使用中止/重启或暂停/恢复机制。

[SWS_Adc_00311] ADC模块的优先级机制应允许不同组的请求排队。

[SWS_Adc_00312] 在ADC模块的优先级机制中，最低优先级为0。

[SWS_Adc_00289] ADC模块的优先级机制应允许配置256个优先级（0…255）。

[SWS_Adc_00315] ADC模块应支持静态配置选项，以禁用优先级机制。

[SWS_Adc_00340] ADC模块应支持静态配置选项，以使用硬件和软件优先级机制启用优先级机制ADC_priority_HW_SW。如果硬件不提供硬件优先级机制，则应实施纯软件优先级机制。

[SWS_Adc_00341] 如果硬件支持优先级机制：ADC模块应支持静态配置选项ADC_PRIORITY_HW，以仅使用硬件优先级机制启用优先级机制。

[SWS_Adc_00339] 如果支持并选择硬件优先级机制：ADC模块应允许将配置的优先级（0-255）映射到可用的硬件优先级。

[SWS_Adc_00332] 如果优先级机制处于活动状态，ADC模块应支持转换请求队列。如果在低优先级信道组转换正在进行时，请求更高优先级的信道组进行转换（此处低优先级组应排队），或者由于高优先级信道组正在进行转换，无法立即处理信道组转换请求，则转换请求应排队。

[SWS_Adc_00417] 如果优先级机制处于活动状态，ADC模块应按照“先到先得”的顺序处理具有相同优先级的组的信道组转换请求。

[SWS_Adc_00333] 如果优先级机制未激活，且静态配置参数AdcEnableQueuing设置为ON，则ADC模块应支持转换请求队列，并应按照“先到先得”的顺序为软件组提供服务。

[SWS_Adc_00335] 如果队列机制激活（优先级机制激活或队列明确激活），ADC模块应在软件队列中最多一次存储每个信道组的每个软件转换请求。

[SWS_Adc_00336] 使用API函数Adc_EnableHardwareTrigger生成的“启用硬件触发器请求”不应存储在任何队列中。

[SWS_Adc_00337] 在硬件触发转换请求的情况下，应使用硬件优先级机制。

[SWS_Adc_00338] ADC模块不得为组状态不等于ADC_IDLE的同一组存储额外的软件转换请求。

[SWS_Adc_00060] 每当完成请求组的所有通道的转换，以及配置并启用通知时，ADC模块应调用组通知功能。

[SWS_Adc_00413] 如果功能是针对不同信道组调用的，则ADC模块功能应是可重入的。此要求适用于除Adc_Init、Adc_DeInit、Adc_GetVersionInfo、Adc_SetPowerState、Adc_GetTargetPowerState和Adc_PreparePowerState之外的所有API函数。

[SWS_Adc_00503] Adc_ReadGroup和Adc_GetGroupStatus中实现的简单读取调用应始终是可重入的，即使函数是为相同的通道组调用的。应由实施人员使用适当的保护机制（例如禁用/启用中断）。

[SWS_Adc_00414] 如果运行时在不同任务或ISR中使用了同一ADC组的多个调用，ADC模块的环境应检查完整性（见注释SWS_ADC_00413）。

[SWS_Adc_00415] 如果运行时在不同任务或ISR中使用了对同一ADC组的多个调用，ADC模块不应检查完整性（见注释SWS_ADC_00413）。

[SWS_Adc_00445] ADC模块应允许对ADC通道进行配置限制检查。

[SWS_Adc_00446] 如果ADC通道的限制检查处于活动状态，则仅考虑配置范围内的ADC转换结果，以更新用户指定的ADC结果缓冲区。

[SWS_Adc_00447] 如果ADC通道的限制检查处于活动状态，则仅考虑配置范围内的ADC转换结果，以触发ADC组状态的状态转换。

[SWS_Adc_00448] 如果选择了带SW触发源的连续转换模式：如果ADC通道的限制检查处于活动状态，则ADC驱动程序将忽略不在配置范围内的ADC转换结果，并重新进行转换。

[SWS_Adc_00449] 如果选择了带SW触发源的单发转换模式：如果ADC通道的限制检查处于活动状态，则ADC驱动程序将忽略不在配置范围内的ADC转换结果，并且包含ADC通道在内的ADC组将保持状态ADC_BUSY。

[SWS_Adc_00450] 如果选择了带硬件触发源的one-shot模式：如果ADC通道的限制检查处于活动状态，则ADC驱动程序将忽略不在配置范围内的ADC转换结果，并重新发出转换，由下一个硬件触发触发。

[SWS_Adc_00280] ADC模块一次只能转换每个ADC硬件单元的一个ADC信道组。ADC模块不应支持同一ADC硬件单元上不同（甚至是专用）ADC信道组的并行转换。

[SWS_Adc_00462] 当相应的配置参数AdcLowPowerStatesSupport设置为TRUE时，ADCD驱动程序应支持电源状态更改及其API。

[SWS_Adc_00463] 如果启用了参数AdcLowPowerStatesSupport，则应生成API Adc_PreparePowerState、Adc_SetPowerState和Adc_GetCurrentPowerState，并用于管理和获取电源状态转换信息。

[SWS_Adc_00464] API Adc_GetTargetPowerState和Adc_GetCurrentPowerState应分别用于收集有关请求和目标Adc电源状态的信息。

[SWS_Adc_00465] API Adc_PreparePowerState应用于启动电源状态转换。

[SWS_Adc_00466] 通过API Adc_PreparePowertState实现电源状态准备后，应使用API Adc_SetPowerState实现Adc模块的请求电源状态。为了避免不一致的电源状态条件，必须按给定顺序调用某些API（Adc_SetPowerState、Adc_PreparePowerState），否则将存储错误（如果启用了DET跟踪），并中断操作。ADC驱动程序跟踪调用序列。

[SWS_Adc_00467] ADC驱动程序应跟踪API ADC_SetPowerState和ADC_PreparePowerState的调用顺序。如果在调用第二个之前调用了第一个，则应存储DET条目，并且不应执行该操作。

[SWS_Adc_00469] 如果参数AdcLowPowerStatesSupport设置为TRUE，则Adc模块应跟踪当前和目标电源状态。

[SWS_Adc_00470] 初始化后，如果AdcLowPowerStatesSupport设置为TRUE，则模块的电源状态应始终为全功率。

[SWS_Adc_00471] ADC驱动程序应支持同步和异步电源状态转换，具体取决于配置参数AdcPowerStateAsynchTransitionMode的值。

[SWS_Adc_00472] 如果配置参数AdcPowerStateAsynchTransitionMode设置为FALSE，则应考虑在相应API返回后立即结束准备过程和设置过程。

[SWS_Adc_00473] 如果配置参数AdcPowerStateAsynchTransitionMode设置为TRUE，则在相关API返回后，准备过程应在后台继续，并通过配置的回调通知其完成。

[SWS_Adc_00318] 在单值访问模式下，结果缓冲区应具有与属于该组的通道一样多的元素。这样，每个缓冲元素都对应于一个通道，按照通道在组中定义的顺序。

[SWS_Adc_00319] 在流式访问模式下，结果缓冲区应具有m*n个元素，其中n是属于该组的通道数，m是每个通道采集的样本数。以这种方式，第一个m个元素属于组中的第一个通道，第二个m个元件属于第二个通道，依此类推。

[SWS_Adc_00320] 每个缓冲元素（类型为整数）的尺寸（位数）应统一，并根据属于任何组的最大（位数）信道进行调整。

<br/>

## Adc_Init

[SWS_Adc_00365]

|函数名|Adc_Init|
|:--|--|
|声明|void Adc_Init( const Adc_ConfigType* ConfigPtr )|
|同步/异步|同步|
|可重入性|不可重入|
|参数（in）|ConfigPtr：指向变体PB中设置的配置的指针（变体PC需要NULL_PTR）。|
|参数（inout）|None|
|参数（out）|None|
|返回值|None|
|描述|初始化ADC硬件单元和驱动程序。|

[SWS_Adc_00054] 对于变体PB：函数Adc_Init应根据ConfigPtr引用的配置集初始化Adc硬件单元和驱动程序。

[SWS_Adc_00056] 函数Adc_ Init将仅初始化配置的资源。不得触摸配置文件中未包含的资源。

以下有关控制器寄存器初始化的规则适用于此驱动程序实现：

```
[SWS_Adc_00246] 如果硬件仅允许寄存器的一次使用，则实现该功能的驱动程序模块负责初始化寄存器。

[SWS_Adc_00247] 如果寄存器可以影响多个硬件模块，如果它是一个I/O寄存器，则应由PORT驱动程序初始化。

[SWS_Adc_00248] 如果寄存器可能影响多个硬件模块，并且不是I/O寄存器，则应由MCU驱动程序初始化。

[SWS_Adc_00249] 复位后需要直接初始化的一次性可写寄存器应通过启动代码进行初始化。

[SWS_Adc_00250] 所有其他寄存器应通过启动代码进行初始化。
```

[SWS_Adc_00077] 函数Adc_Init应禁用通知和硬件触发功能（如果静态配置为活动）。

[SWS_Adc_00307] 函数Adc_Init应将所有组设置为ADC_IDLE状态。

[SWS_Adc_00107] 如果启用了ADC模块的开发错误检测：如果在ADC驱动程序和硬件已初始化时调用，函数Adc_Init将引发开发错误ADC_E_ALREADY_INITIALIZED，并返回而不执行任何操作。

## Adc_SetupResultBuffer

[SWS_Adc_00419]

|函数名|Adc_SetupResultBuffer|
|--|--|
|声明|Std_ReturnType Adc_SetupResultBuffer( Adc_GroupType Group, const Adc_ValueGroupType* DataBufferPtr )|
|同步/异步|同步|
|可重入性|不可重入|
|参数（in）|Group：请求的ADC通道组的数字ID。</br>DataBufferPtr：指向结果数据缓冲区的指针|
|参数（inout）|None|
|参数（out）|None|
|返回值|Std_ReturnType：</br>E_OK：结果缓冲区指针已正确初始化 。</br>E_NOT_OK：操作失败或发生开发错误。|
|描述|使用将存储转换结果的组特定结果缓冲区起始地址初始化ADC驱动程序。应用程序必须确保DataBufferPtr所指向的应用程序缓冲区可以保存指定组的所有转换结果。重置后，需要使用Adc_SetupResultBuffer进行初始化，然后才能开始组转换。|

[SWS_Adc_00420] 函数Adc_SetupResultBuffer应使用作为参数传递的地址值初始化所选组的结果缓冲区指针。

[SWS_Adc_00421] ADC模块的环境应确保在未事先初始化相应结果缓冲区指针以指向有效结果缓冲区的情况下，不会启动组转换。

[SWS_Adc_00422] ADC模块的环境应确保应用程序缓冲区（地址作为Adc_SetupResultBuffer中的参数传递）具有相应的大小来保存所有组通道转换结果，如果选择了流访问，则根据流采样参数的规定多次保存这些结果。

[SWS_Adc_00423] 如果启用了ADC模块的开发错误检测：如果通道组ID不存在，函数Adc_SetupResultBuffer将引发开发错误ADC_E_PARAM_GROUP，并返回而不执行任何操作。

[SWS_Adc_00433] 如果在组不处于状态ADC_IDLE时调用，函数Adc_SetupResultBuffer将报告运行时错误ADC_E_BUSY。

[SWS_Adc_00434] 如果启用了ADC模块的开发错误检测：在初始化驱动程序之前调用时，函数Adc_SetupResultBuffer将引发开发错误ADC_E_UNINIT。

[SWS_Adc_00457] 如果启用了ADC模块的开发错误检测：当使用NULL_PTR作为DataBufferPtr调用时，函数Adc_SetupResultBuffer将引发开发错误ADC_E_PARAM_POINTER。

## Adc_DeInit

[SWS_Adc_00366] 

|函数名|Adc_DeInit|
|--|--|
|声明|void Adc_DeInit( void )|
|同步/异步|同步|
|可重入性|不可重入|
|参数（in）|None|
|参数（inout）|None|
|参数（out）|None|
|返回值|None|
|描述|将所有ADC硬件单元返回到与其上电复位状态相当的状态。|

[SWS_Adc_00110] 功能Adc_DeInit应将所有Adc硬件单元返回到与其上电复位状态相当的状态。不可写入的寄存器值被排除。硬件设计的责任是该状态不会导致μC中未定义的活动。

[SWS_Adc_00111] 函数Adc_DeInit应禁用所有使用的中断和通知。

[SWS_Adc_00358] 当任何组未处于ADC_IDLE状态时，ADC模块的环境不得调用函数Adc_DeInit。

[SWS_Adc_00228] 函数Adc_DeInit应可通过配置参数AdcDeInitApi在编译前配置开/关。

[SWS_Adc_00112] 如果在不是所有组都处于状态ADC_IDLE或状态ADC_STREAM_COMPLETED时调用，而没有转换正在进行（隐含停止的ADC组），则函数Adc_DeInit将报告运行时错误。

[SWS_Adc_00154] 如果启用了ADC模块的开发错误检测：如果在模块初始化之前调用，函数ADC_DeInit将引发开发错误ADC_E_UNINIT并返回而不执行任何操作。

<br/>

## Adc_StartGroupConversion

[SWS_Adc_00367]

|函数名|Adc_StartGroupConversion|
|--|--|
|声明|void Adc_StartGroupConversion( Adc_GroupType Group )|
|同步/异步|异步|
|可重入性|可重入|
|参数（in）|Group：请求的ADC通道组的数字ID。|
|参数（inout）|None|
|参数（out）|None|
|返回值|None|
|描述|启动所请求ADC通道组的所有通道的转换。|

[SWS_Adc_00061] 函数Adc_StartGroupConversion应启动所请求Adc通道组的所有通道的转换。根据组配置，启动一次或连续转换。

[SWS_Adc_00431] 函数Adc_StartGroupConversion应重置内部结果缓冲区指针，即在调用Adc_StartGroupConvertion后，转换结果存储始终在使用Adc_SetupResultBuffer配置的结果缓冲区基址处启动。

[SWS_Adc_00156] 函数Adc_StartGroupConversion不应自动启用该组的通知机制（这必须通过单独的API调用完成）。

[SWS_Adc_00146] ADC模块的环境只能为配置有软件触发源的组调用ADC_StartGroupConversion。

[SWS_Adc_00259] 函数Adc_StartGroupConversion应可通过配置参数AdcEnableStartStopGroupApi在编译前配置On/Off。

[SWS_Adc_00125] 如果启用了ADC模块的开发错误检测：当使用不存在的通道组ID调用时，函数ADC_StartGroupConversion将引发开发错误ADC_E_PARAM_group，并返回而不执行任何操作。

[SWS_Adc_00133] 如果启用了ADC模块的开发错误检测：在触发源配置为硬件的组上调用时，函数ADC_StartGroupConversion将引发开发错误ADC_E_WRONG_TRIG_SRC，并返回而不执行任何操作。

[SWS_Adc_00346] 如果优先级机制被禁用，队列被禁用：当任何组（不能隐式停止）未处于ADC_IDLE状态时调用时，函数ADC_StartGroupConversion将报告运行时错误ADC_E_BUSY。

[SWS_Adc_00426] 如果优先级机制被禁用，队列被禁用：当调用时，任何可以隐式停止的组都不处于状态ADC_IDLE，也不处于ADC_STREAM_COMPLETED，函数ADC_StartGroupConversion将报告运行时错误ADC_E_BUSY。

[SWS_Adc_00348] 如果启用了优先级机制：当在无法隐式停止的组未处于ADC_IDLE状态时调用时，函数ADC_StartGroupConversion将报告运行时错误ADC_E_BUSY。

[SWS_Adc_00427] 如果启用了优先级机制：当组（可以隐式停止）未处于状态ADC_IDLE且未处于状态ADC_STREAM_COMPLETED时调用时，函数ADC_StartGroupConversion将报告运行时错误ADC_E_BUSY。

[SWS_Adc_00351] 如果禁用了优先级机制并且启用了排队：当在无法隐式停止的组未处于ADC_IDLE状态时调用时，函数Adc_StartGroupConversion将报告运行时错误ADC_E_BUSY。

[SWS_Adc_00428] 如果禁用了优先级机制，并且启用了排队：当组（可以隐式停止）未处于状态ADC_IDLE且未处于ADC_STREAM_COMPLETED时调用时，函数Adc_StartGroupConversion将报告运行时错误ADC_E_BUSY。

[SWS_Adc_00294] 如果启用了ADC模块的开发错误检测：在初始化驱动程序之前调用时，函数Adc_StartGroupConversion将引发开发错误ADC_E_UNINIT。

[SWS_Adc_00424] 如果启用了ADC模块的开发错误检测：当在使用函数Adc_SetupResultBuffer初始化结果缓冲区指针之前调用时，函数AdcStartGroupConversion将引发开发错误ADC_E_BUFFER_UNINIT。

## Adc_StopGroupConversion

[SWS_Adc_00368] 

|函数名|Adc_StopGroupConversion|
|--|--|
|声明|void Adc_StopGroupConversion( Adc_GroupType Group )|
|同步/异步|同步|
|可重入性|可重入|
|参数（in）|Group：请求的ADC通道组的数字ID。|
|参数（inout）|None|
|参数（out）|None|
|返回值|None|
|描述|停止所请求ADC通道组的转换。|

[SWS_Adc_00385] 当ADC通道组处于单触发和软件触发模式时，函数ADC_StopGroupConversion将停止组的正在进行的转换。

[SWS_Adc_00437] 当ADC通道组处于单触发和软件触发模式时，如果启用了排队并且在队列中存储了启动/重新启动请求，则函数Adc_StopGroupConversion将从队列中删除该组的启动/重新开始请求。

[SWS_Adc_00386] 当ADC通道组处于连续转换和软件触发模式时，函数ADC_StopGroupConversion将停止该组的正在进行的转换。

[SWS_Adc_00438] 当ADC通道组处于连续转换和软件触发模式时，如果启用了排队并且启动/重新启动请求存储在队列中，则函数Adc_StopGroupConversion将从队列中删除该组的启动/重新开始请求。

[SWS_Adc_00155] 函数Adc_StopGroupConversion将自动禁用请求组的组通知。

[SWS_Adc_00360] 函数Adc_StopGroupConversion应将组状态设置为状态Adc_IDLE。

[SWS_Adc_00283] ADC模块的环境只能为配置有触发源软件的组调用函数Adc_StopGroupConversion。

[SWS_Adc_00260] 函数Adc_StopGroupConversion应可通过配置参数AdcEnableStartStopGroupApi在编译前配置On/Off。

[SWS_Adc_00126] 如果启用了ADC模块的开发错误检测：如果组ID不存在，函数Adc_StopGroupConversion将引发开发错误ADC_E_PARAM_GROUP，并返回而不执行任何操作。

[SWS_Adc_00164] 如果启用ADC模块的开发错误检测：如果组具有配置为硬件的触发源，则函数Adc_StopGroupConversion将引发开发错误ADC_E_WRONG_TRIG_SRC，并返回而不执行任何操作。

[SWS_Adc_00241] 当组处于状态ADC_IDLE时调用时，函数Adc_StopGroupConversion将报告运行时错误ADC_E_IDLE。

[SWS_Adc_00295] 如果启用了ADC模块的开发错误检测：如果在初始化模块之前调用，函数Adc_StopGroupConversion将引发开发错误ADC_E_UNINIT并返回而不执行任何操作。

## Adc_ReadGroup

[SWS_Adc_00369]

|函数名|Adc_ReadGroup|
|--|--|
|声明|Std_ReturnType Adc_ReadGroup( Adc_GroupType Group, Adc_ValueGroupType* DataBufferPtr )|
|同步/异步|同步|
|可重入性|可重入|
|参数（in）|Group：请求的ADC通道组的数字ID。|
|参数（inout）|None|
|参数（out）|DataBufferPtr：所选组的所有通道的ADC结果存储在用指针寻址的数据缓冲器中。|
|返回值|Std_ReturnType：E_OK：结果缓冲区指针已正确初始化 。E_NOT_OK：操作失败或发生开发错误。|
|描述|读取所请求组的最后一次完成转换的组转换结果，并存储从DataBufferPtr地址开始的通道值。组通道值按通道编号升序存储（与配置流访问时的结果缓冲区的存储布局相反）。|

[SWS_Adc_00075] 函数Adc_ReadGroup应读取请求组的最新可用转换结果。

[SWS_Adc_00113] 函数Adc_ReadGroup应读取原始转换值，无需进一步缩放。读取值应根据ADC_RESULT_ALIGNMENT的配置参数设置对齐。

[SWS_Adc_00122] 如果适用，函数Adc_ReadGroup应屏蔽转换提供的所有信息或诊断位，但不属于转换结果本身。

[SWS_Adc_00329] 当组状态为Adc_STREAM_COMPLETED时，调用函数Adc_ReadGroup将触发连续转换模式（单访问模式或循环流缓冲模式）和单访问模式和循环流访问模式下的硬件触发组的状态转换到ADC_BUSY。

[SWS_Adc_00330] 当组状态为ADC_STREAM_COMPLETED时，调用函数Adc_ReadGroup应触发状态转换到ADC_IDLE，用于自动停止转换的软件触发转换模式（具有线性访问模式的流缓冲区或具有单次访问的单触发转换模式），以及用于与线性流访问模式组合的硬件触发转换模式。

[SWS_Adc_00331] 当组状态为ADC_COMPLETED时，调用函数Adc_ReadGroup将触发到ADC_BUSY的状态转换。

[SWS_Adc_00359] 函数Adc_ReadGroup应通过配置参数AdcReadGroupApi预编译可配置On/Off。

[SWS_Adc_00388] 当在组状态为ADC_IDLE且组转换未启动时（以前的转换没有可用结果）调用时，函数ADC_ReadGroup应报告运行时错误ADC_E_IDLE。

[SWS_Adc_00152] 如果启用了ADC模块的开发错误检测：如果组ID不存在，函数Adc_ReadGroup将引发开发错误ADC_E_PARAM_GROUP并返回E_NOT_OK。

[SWS_Adc_00296] 如果启用了ADC模块的开发错误检测：在初始化驱动程序之前调用时，函数Adc_ReadGroup将引发开发错误ADC_E_UNINIT并返回E_NOT_OK。

## Adc_EnableHardwareTrigger

[SWS_Adc_00370]

|函数名|Adc_EnableHardwareTrigger|
|--|--|
|声明|void Adc_EnableHardwareTrigger( Adc_GroupType Group )|
|同步/异步|同步|
|可重入性|可重入|
|参数（in）|Group：请求的ADC通道组的数字ID。|
|参数（inout）|None|
|参数（out）|None|
|返回值|None|
|描述|为请求的ADC通道组启用硬件触发器。|

[SWS_Adc_00114] 函数Adc_EnableHardwareTrigger应为请求的Adc通道组启用硬件触发器。

[SWS_Adc_00144] 具有触发源硬件的组（其触发由Adc_EnableHardwareTrigger启用）应在触发事件发生时执行组通道转换。

[SWS_Adc_00432] 函数Adc_EnableHardwareTrigger应重置内部组结果缓冲区指针，即在调用Adc_EnableHardwareTrigger后，转换结果存储始终在使用Adc_SetupResultBuffer配置的结果缓冲区基址处启动。

[SWS_Adc_00273] ADC模块的环境应确保在同一硬件单元上不会发生并发转换（同时发生不同硬件触发）。

[SWS_Adc_00120] ADC模块的环境应仅为在硬件触发模式下配置的组调用函数ADC_EnableHardwareTrigger（请参阅AdcGroupTriggSrc）。

[SWS_Adc_00265] 函数Adc_EnableHardwareTrigger应通过配置参数AdcHwTriggerApi在编译时进行预配置。

[SWS_Adc_00321] 如果优先级机制被禁用且队列被禁用：当具有触发源SW的任何组不处于状态ADC_IDLE时调用时，函数ADC_EnableHardwareTrigger将报告运行时错误ADC_E_BUSY。

[SWS_Adc_00349] 如果组的HW触发器已启用，则函数Adc_EnableHardwareTrigger应报告运行时错误ADC_E_BUSY。

[SWS_Adc_00353] 如果已启用可用硬件触发器的最大数量（特定于设备和实现），则函数Adc_EnableHardwareTrigger应报告运行时错误ADC_E_BUSY。

[SWS_Adc_00128] 如果启用了ADC模块的开发错误检测：如果通道组ID无效，函数Adc_EnableHardwareTrigger将引发开发错误ADC_E_PARAM_GROUP，并返回而不执行任何操作。

[SWS_Adc_00136] 如果启用ADC模块的开发错误检测：如果组配置为软件API触发模式，则函数Adc_EnableHardwareTrigger将引发开发错误ADC_E_WRONG_TRIG_SRC，并返回而不执行任何操作。

[SWS_Adc_00281] 如果启用了ADC模块的开发错误检测：如果硬件组被错误配置为连续转换模式，则函数ADC_EnableHardwareTrigger将引发开发错误ADC_E_WRONG_CONV_mode，并返回而不执行任何操作。

[SWS_Adc_00297] 如果启用了ADC模块的开发错误检测：如果在初始化驱动程序之前调用，函数Adc_EnableHardwareTrigger将引发开发错误ADC_E_UNINIT并返回而不执行任何操作。

[SWS_Adc_00425] 如果启用了ADC模块的开发错误检测：当在使用函数Adc_SetupResultBuffer初始化结果缓冲区指针之前调用时，函数ADC_EnableHardwareTrigger将引发开发错误ADC_E_BUFFER_UNINIT。

## Adc_DisableHardwareTrigger

[SWS_Adc_00371]

|函数名|Adc_DisableHardwareTrigger|
|--|--|
|声明|void Adc_DisableHardwareTrigger( Adc_GroupType Group )|
|同步/异步|同步|
|可重入性|可重入|
|参数（in）|Group：请求的ADC通道组的数字ID。|
|参数（inout）|None|
|参数（out）|None|
|返回值|None|
|描述|禁用请求的ADC通道组的硬件触发器。|

[SWS_Adc_00116] 函数Adc_DisableHardwareTrigger应禁用所请求Adc通道组的硬件触发器。

[SWS_Adc_00429] 如果启用了排队，函数Adc_DisableHardwareTrigger将删除所请求Adc通道组的任何排队启动/重新启动请求。

[SWS_Adc_00145] 如果适用（硬件支持），函数Adc_DisableHardwareTrigger应中止正在进行的转换。

[SWS_Adc_00157] 如果启用，函数Adc_DisableHardwareTrigger将禁用请求组的通知机制。

[SWS_Adc_00361] 功能Adc_DisableHardwareTrigger应将组状态设置为状态ADC_IDLE。

[SWS_Adc_00121] ADC模块的环境只能为硬件触发模式下配置的组调用函数Adc_DisableHardwareTrigger（请参阅AdcGroupTriggSrc）。

[SWS_Adc_00266] 函数Adc_DisableHardwareTrigger应通过配置参数AdcHwTriggerApi预编译时可配置On/Off。

[SWS_Adc_00129] 如果启用了ADC模块的开发错误检测：如果通道组ID不存在，函数ADC_DisableHardwareTrigger将引发开发错误ADC_E_PARAM_group，并返回而不执行任何操作。

[SWS_Adc_00137] 如果启用ADC模块的开发错误检测：如果组配置为软件API触发模式，则函数ADC_DisableHardwareTrigger将引发开发错误ADC_E_WRONG_TRIG_SRC，并返回而不执行任何操作。

[SWS_Adc_00282] 如果启用了ADC模块的开发错误检测：如果硬件组被错误配置为连续转换模式，则函数ADC_DisableHardwareTrigger将引发开发错误ADC_E_WRONG_CONV_MODE，并返回而不执行任何操作。

[SWS_Adc_00304] 如果该Group未启用（之前调用了Adc_EnableHardwareTrigger），函数Adc_DisableHardwareTrigger将报告运行时错误ADC_E_IDLE。

[SWS_Adc_00298] 如果启用了ADC模块的开发错误检测：如果在初始化ADC模块之前调用，ADC_DisableHardwareTrigger将引发开发错误ADC_E_UNINIT，并返回而不执行任何操作。

## Adc_EnableGroupNotification

[SWS_Adc_00372]

|函数名|Adc_EnableGroupNotification|
|--|--|
|声明|void Adc_EnableGroupNotification( Adc_GroupType Group )|
|同步/异步|同步|
|可重入性|可重入|
|参数（in）|Group：请求的ADC通道组的数字ID。|
|参数（inout）|None|
|参数（out）|None|
|返回值|None|
|描述|为请求的ADC通道组启用通知机制。|

[SWS_Adc_00057] 函数Adc_EnableGroupNotification应为请求的Adc通道组启用通知机制。

[SWS_Adc_00100] 函数Adc_EnableGroupNotification应通过配置参数AdcGrpNotifCapability在编译时进行预配置。

[SWS_Adc_00130] 如果启用了ADC模块的开发错误检测：如果通道组ID不存在，函数ADC_EnableGroupNotification将引发开发错误ADC_E_PARAM_group，并返回而不执行任何操作。

[SWS_Adc_00165] 如果启用了ADC模块的开发错误检测：如果组通知函数指针为NULL，则函数ADC_EnableGroupNotification将引发开发错误ADC_E_NOTIF_CAPABILITY并返回而不执行任何操作。

[SWS_Adc_00299] 如果启用了ADC模块的开发错误检测：如果在初始化ADC模块之前调用，ADC_EnableGroupNotification将引发开发错误ADC_E_UNINIT，并返回而不执行任何操作。

## Adc_DisableGroupNotification

[SWS_Adc_00373]

|函数名|Adc_DisableGroupNotification|
|--|--|
|声明|void Adc_DisableGroupNotification( Adc_GroupType Group )|
|同步/异步|同步|
|可重入性|可重入|
|参数（in）|Group：请求的ADC通道组的数字ID。|
|参数（inout）|None|
|参数（our）|None|
|返回值|None|
|描述|禁用请求的ADC通道组的通知机制。|

[SWS_Adc_00058] 函数Adc_DisableGroupNotification将禁用所请求Adc通道组的通知机制。

[SWS_Adc_00101] 函数Adc_DisableGroupNotification应在编译前通过配置参数AdcGrpNotifCapability进行off/on配置

[SWS_Adc_00131] 如果启用了ADC模块的开发错误检测：如果通道组ID不存在，函数Adc_DisableGroupNotification将引发开发错误ADC_E_PARAM_GROUP，并返回而不执行任何操作。

[SWS_Adc_00166] 如果启用了ADC模块的开发错误检测：如果组通知函数指针为NULL，则函数Adc_DisableGroupNotification将引发开发错误ADC_E_NOTIF_CAPABILITY并返回而不执行任何操作。

[SWS_Adc_00300] 如果启用了ADC模块的开发错误检测：如果在初始化ADC模块之前调用，ADC_DisableGroupNotification将引发开发错误ADC_E_UNINIT，并返回而不执行任何操作。

## Adc_GetGroupStatus

[SWS_Adc_00374]

|函数名|Adc_GetGroupStatus|
|--|--|
|声明|Adc_StatusType Adc_GetGroupStatus( Adc_GroupType Group )|
|同步/异步|同步|
|可重入性|可重入|
|参数（in）|Group：请求的ADC通道组的数字ID。|
|参数（inout）|None|
|参数（out）|None|
|返回值|Adc_StatusType：请求的ADC通道组的转换状态|
|描述|返回请求的ADC通道组的转换状态。|

[SWS_Adc_00220] 函数Adc_GetGroupStatus将返回请求的Adc通道组的转换状态。

[SWS_Adc_00221] 函数Adc_GetGroupStatus应返回ADC_IDLE：

- 如果Adc_GetGroupStatus在请求的组转换开始之前被调用
- 对于具有触发源软件的组：如果在使用Adc_StopGroupConversion停止转换后调用Adc_GetGroupStatus
- 在具有线性流访问模式的连续组转换模式中：如果在调用Adc_GetStreamLastPointer后调用AdcGetGroupStatus（在调用Adc_GetStreamLastPoint时，组处于状态ADC_STREAM_COMPLETED）。
- 在具有线性流访问模式的连续组转换模式中：如果调用Adc_ReadGroup后调用Adc_GetGroupStatus（在调用Adc_LeadGroup时，组处于状态ADC_STREAM_COMPLETED）。
- 在一次性SW转换模式中：如果在调用Adc_GetStreamLastPointer之后调用AdcGetGroupStatus。
- 在单次SW转换模式中：如果在调用Adc_ReadGroup之后调用Adc_GetGroupStatus。
- 对于具有触发器源硬件的组：如果在调用Adc_DisableHardwareTrigger之后调用Adc_GetGroupStatus
- 对于具有触发源硬件和线性流访问模式的组：如果在调用Adc_GetStreamLastPointer之后调用了Adc_GetGroupStatus（组在调用Adc_GetStream LastPoint时处于状态Adc_STREAM_COMPLETED）。
- 对于具有触发源硬件和线性流访问模式的组：如果在调用Adc_ReadGroup后调用Adc_GetGroupStatus（组在调用Adc_ReadGroup时处于状态ADC_STREAM_COMPLETED）。

[SWS_Adc_00222] 函数Adc_GetGroupStatus应返回ADC_BUSY：

- 如果在请求组的第一轮转换仍在进行时调用它（连续转换模式）。
- 一旦为具有HW触发源的组启用了触发器。
- 一旦为具有SW触发源的组调用了Adc_StartGroupConversion。
- 在具有单访问模式的连续组转换模式中：如果在调用Adc_GetStreamLastPointer之后调用AdcGetGroupStatus
- 在具有单访问模式的连续组转换模式中：如果在调用Adc_ReadGroup之后调用Adc_GetGroupStatus。
- 在具有循环流访问模式的连续组转换模式中：如果在调用Adc_GetStreamLastPointer之后调用AdcGetGroupStatus。
- 在具有循环流访问模式的连续组转换模式下，如果在调用Adc_ReadGroup后调用Adc_GetGroupStatus。
- 在具有线性流访问模式的连续组转换模式中：如果在调用Adc_GetStreamLastPointer后调用AdcGetGroupStatus（在调用Adc_GetStreamLastPoint时，组处于状态ADC_COMPLETED）。
- 在线性流访问模式下的连续组转换模式中：如果调用Adc_ReadGroup后调用Adc_GetGroupStatus（在调用Adc_LeadGroup时组处于状态ADC_COMPLETED）。
- 在单次硬件转换模式和单次访问模式中：如果在调用Adc_GetStreamLastPointer之后调用AdcGetGroupStatus。
- 在单次硬件转换模式和单次访问模式下：如果在调用Adc_ReadGroup之后调用Adc_GetGroupStatus。
- 在一次性HW转换模式和循环流访问模式中：如果在调用Adc_GetStreamLastPointer之后调用AdcGetGroupStatus。
- 在一次硬件转换模式和循环流访问模式中：如果在调用Adc_ReadGroup之后调用Adc_GetGroupStatus。
- 在 one-shot HW转换模式和线性流访问模式中：如果调用Adc_GetStreamLastPointer后调用了Adc_GetGroupStatus（组在调用AdcGetStreamlastPointer时处于状态ADC_COMPLETED）。
- 在one-shot HW转换模式和线性流访问模式中：如果调用Adc_ReadGroup后调用Adc_GetGroupStatus（在调用Adc_LeadGroup时，组处于状态ADC_COMPLETED）。

[SWS_Adc_00224] 函数Adc_GetGroupStatus应返回ADC_COMPLETED：

- 如果是在请求组的转换轮次（而不是最后一轮）完成后调用的。

[SWS_Adc_00325] 函数Adc_GetGroupStatus应返回ADC_STREAM_COMPLETED：

- 如果在完成一轮转换后以单次访问模式调用。
- 如果在请求的组的转换轮数结束后，在流访问模式下调用，则将流缓冲区完全填满。

[SWS_Adc_00226] 函数Adc_GetGroupStatus应通过使用原子指令提供对状态数据的原子访问。

[SWS_Adc_00305] 为保证返回值的一致性，假设在状态轮询开始之前，软件总是成功启动ADC组转换（或在硬件组的情况下启用）。

[SWS_Adc_00225] 如果启用了ADC模块的开发错误检测：如果通道组ID不存在，函数ADC_GetGroupStatus将引发开发错误ADC_E_PARAM_GROUP，并返回ADC_IDLE而不执行任何操作。

[SWS_Adc_00301] 如果启用了ADC模块的开发错误检测：如果在初始化ADC模块之前调用，ADC_GetGroupStatus将引发开发错误ADC_E_UNINIT，并返回ADC_IDLE而不执行任何操作。

[SWS_Adc_00436] 对于中止/挂起的组，排队组的状态与该组中止/挂断之前的状态相同。

## Adc_GetStreamLastPointer

[SWS_Adc_00375]

|函数名|Adc_GetStreamLastPointer|
|--|--|
|声明|Adc_StreamNumSampleType Adc_GetStreamLastPointer( Adc_GroupType Group, Adc_ValueGroupType** PtrToSamplePtr )|
|同步/异步|同步|
|可重入性|可重入|
|参数（in）|Group：请求的ADC通道组的数字ID。|
|参数（inout）|None|
|参数（out）|PtrToSamplePtr：指向结果缓冲区指针的指针。|
|返回值|Adc_StreamNumSampleType：每个通道的有效样本数。|
|描述|返回存储在结果缓冲区中的每个通道的有效样本数。读取指向组结果缓冲区中某个位置的指针。使用指针位置，可以访问最后一次完成的转换回合的所有组通道的结果。 使用指针和返回值，可以访问所有有效的组转换结果（用户必须考虑结果缓冲区的布局）。|

[SWS_Adc_00214] 函数Adc_GetStreamLastPointer应设置作为参数（PtrToSamplePtr）传递的指针，以在Adc结果缓冲区中指向最后一次完成转换循环的第一组通道的最新结果。

[SWS_Adc_00418] ADC驱动程序存储在ADC结果缓冲区中的所有值都不需要进一步缩放，应根据ADC_RESULT_ALIGNMENT的配置参数设置进行对齐。

[SWS_Adc_00387] 函数Adc_GetStreamLastPointer应返回存储在Adc结果缓冲区中的每个通道的有效样本数。

[SWS_Adc_00216] 当在组状态为ADC_BUSY（组的转换正在进行中）时调用时，函数ADC_GetStreamLastPointer应将作为参数（PtrToSamplePtr）传递的指针设置为NULL并返回0。

[SWS_Adc_00219] ADC模块环境应通过检查Adc_GetGroupStatus的返回值来保证已读取数据的一致性。

[SWS_Adc_00326] 当组状态为ADC_STREAM_COMPLETED时调用函数Adc_GetStreamLastPointer应触发状态转换到ADC_BUSY，用于连续转换模式（单访问模式或循环流缓冲模式）和硬件触发的组以单访问模式或循环流访问模式。

[SWS_Adc_00327] 当组状态为ADC_STREAM_COMPLETED时，调用函数Adc_GetStreamLastPointer将触发对自动停止转换的软件转换模式（线性访问模式的流缓冲区或单次访问的单触发转换模式）以及与线性流访问模式结合的硬件触发转换模式的状态转换到ADC_IDLE。

[SWS_Adc_00328] 当组状态为ADC_COMPLETED时调用函数Adc_GetStreamLastPointer将触发到ADC_BUSY的状态转换。

[SWS_Adc_00215] 当组状态为ADC_IDLE且未启动组转换（以前的转换没有结果可用）时调用时，函数Adc_GetStreamLastPointer应报告运行时错误ADC_E_IDLE。

[SWS_Adc_00218] 如果启用了ADC模块的开发错误检测：如果组ID不存在，函数ADC_GetStreamLastPointer将引发开发错误ADC_E_PARAM_group，将作为参数（PtrToSamplePtr）传递的指针设置为NULL，并返回0，无需任何进一步操作。

[SWS_Adc_00302] 如果启用了ADC模块的开发错误检测：如果在初始化驱动程序之前调用，函数ADC_GetStreamLastPointer将引发开发错误ADC_E_UNINIT，将作为参数（PtrToSamplePtr）传递的指针设置为NULL，并返回0，无需任何进一步操作。

## Adc_GetVersionInfo

[SWS_Adc_00376]

|函数名|Adc_GetVersionInfo|
|--|--|
|声明|void Adc_GetVersionInfo( Std_VersionInfoType* versioninfo )|
|同步/异步|同步|
|可重入性|可重入|
|参数（in）|None|
|参数（inout）|None|
|参数（out）|versioninfo：指向存储此模块版本信息的位置的指针。|
|返回值|None|
|描述|返回此模块的版本信息|

[SWS_Adc_00458] 如果启用了ADC模块的开发错误检测：函数ADC_GetVersionInfo应检查参数versioninfo是否为NULL，如果检查失败，则应引发开发错误ADC_E_PARAM_POINTER。

## Adc_SetPowerState

[SWS_Adc_00475]

|函数名|Adc_SetPowerState|
|--|--|
|声明|Std_ReturnType Adc_SetPowerState(Adc_PowerStateRequestResultType* Result )|
|同步/异步|同步|
|可重入性|不可重入|
|参数（in）|None|
|参数（inout）|None|
|参数（out）|Result：如果API返回E_OK:ADC_SERVICE_ACCEPTED:电源状态更改已执行。</br>如果API返回E_NOT_OK：ADC_NOT_INIT：ADC模块未初始化。ADC_SEQUENCE_ERROR:错误的API调用序列。ADC_HW_FAILURE：硬件模块出现故障，无法进入所需电源状态。|
|返回值|Std_ReturnType：</br>E_OK：模式已转换。</br>E_NOT_OK：请求被拒绝。|
|描述|此API配置Adc模块，使其进入已准备好的电源状态，在预定义的一组配置状态之间进行选择。|

[SWS_Adc_00481] API配置硬件以进入先前准备的电源状态。负责的SWC（例如IoHwAbs）必须已经采取了启用此转换的所有初步措施（例如，将所有通道设置为IDLE状态，取消所有通知的注册等）。

[SWS_Adc_00482] 如果目标电源状态与当前电源状态相同，则不会执行任何操作，API将立即返回E_OK结果。

[SWS_Adc_00483] 如果请求正常电源状态，API应参考Adc_Init使用的相同容器中包含的必要参数。

[SWS_Adc_00484] 对于其他电源状态，只能在该API的上下文中执行电源状态转换特定的重新配置（即，该API不能用于将全新配置应用于Adc模块）。不得进行与电源状态转换无关的任何其他重新配置。

[SWS_Adc_00485] API应参考与所需电源状态相关的配置容器，以便导出该状态的某些特定特征（例如，对电源状态的支持）。

[SWS_Adc_00486] 如果激活了开发错误报告：如果在初始化硬件单元之前调用此API，则API应报告DET错误ADC_E_UNINIT

[SWS_Adc_00487] 如果一个或多个硬件通道（如适用）处于不同于IDLE（或类似的非操作状态）的状态和/或仍有为硬件模块通道注册的通知，则API应报告运行时错误ADC_E_NOT_DISENGAGED。

[SWS_Adc_00488] 如果激活了开发错误报告：如果使用不支持的电源状态调用此API或外设根本不支持低电源状态，则API应报告DET错误ADC_E_POWER_STATE_NOT_SUPPORTED。

[SWS_Adc_00489] 如果当前电源状态无法直接达到请求的电源状态，API应报告运行时错误ADC_E_TRANSITION_NOT_POSSIBLE。

[SWS_Adc_00490] 如果开发错误报告被激活：如果硬件单元之前未使用API ADC_PreparePowerState为目标电源状态做好准备，则API应报告DET错误ADC_E_PERIPHERAL_NOT_PREPARED。

## Adc_GetCurrentPowerState

[SWS_Adc_00476]

|函数名|Adc_GetCurrentPowerState|
|--|--|
|声明|Std_ReturnType Adc_GetCurrentPowerState( Adc_PowerStateType* CurrentPowerState, Adc_PowerStateRequestResultType* Result )|
|同步/异步|同步|
|可重入性|不可重入|
|参数（in）|None|
|参数（inout）|None|
|参数（out）|CurrentPowerState：ADC HW单元的当前电源模式在此参数中返回。</br>Result：</br>如果返回E_OK：ADC_SERVICE_ACCEPTED:返回当前电源模式。</br>如果返回E_NOT_OK：ADC_NOT_INIT:ADC模块未初始化。|
|返回值|Std_ReturnType：E_OK：模式能被读取。</br>E_NOT_OK：服务被拒绝。|
|描述|此API返回ADC HW单元的当前电源状态。|

[SWS_Adc_00491] 如果激活了开发错误报告：如果在初始化硬件单元之前调用此API，则API应报告DET错误ADC_E_UNINIT。

## Adc_GetTargetPowerState

[SWS_Adc_00477]

|函数名|Adc_GetTargetPowerState|
|--|--|
|声明|Std_ReturnType Adc_GetTargetPowerState( Adc_PowerStateType\*  TargetPowerState, Adc_PowerStateRequestResultType* Result )|
|同步/异步|同步|
|可重入性|不可重入|
|参数（in）|None|
|参数（inout）|None|
|参数（out）|TargetPowerState：ADC硬件单元的目标电源模式在此参数中返回。</br>Result：</br>如果返回E_OK：ADC_SERVICE_ACCEPTED:已返回目标电源模式。</br>如果返回E_NOT_OK：ADC_NOT_INIT:ADC模块未初始化。|
|返回值|Std_ReturnType：E_OK：模式能被读取。</br>E_NOT_OK：请求被拒绝。|
|描述|此API返回ADC HW单元的目标电源状态。|

[SWS_Adc_00492] API返回HW单元的请求电源状态。如果没有转换正在进行，则这应与当前电源状态一致。API被认为总是成功的，除非硬件出现故障。

[SWS_Adc_00493] 如果激活了开发错误报告：如果在初始化硬件单元之前调用此API，则API应报告DET错误ADC_E_UNINIT。

## Adc_PreparePowerState

[SWS_Adc_00478]

|函数名|Adc_PreparePowerState|
|--|--|
|声明|Std_ReturnType Adc_PreparePowerState( Adc_PowerStateType PowerState, Adc_PowerStateRequestResultType* Result )|
|同步/异步|同步|
|可重入性|不可重入|
|参数（in）|PowerState：预期达到的目标功率状态|
|参数（inout）|None|
|参数（out）|Result：如果返回E_OK：ADC_SERVICE_ACCEPTED:ADC模块电源状态准备已启动。</br>如果返回E_NOT_OK：ADC_NOT_INIT:ADC模块未初始化。ADC_SEQUENCE_ERROR:错误的API调用序列（当前电源状态=目标电源状态）。ADC_POWER_STATE_NOT_SUPP:ADC模块不支持请求的电源状态。ADC_TRANS_NOT_POSSIBLE:ADC模块无法直接从当前电源状态转换到请求的电源状态，或者HW外围设备仍然繁忙。|
|返回值|Std_ReturnType：E_OK：准备进程已开始</br>E_NOT_OK：请求被拒绝。|
|描述|此API启动所需的过程，以允许ADC HW模块进入请求的电源状态。|

[SWS_Adc_00494] 此API启动使硬件模块进入目标电源状态所需的所有操作。       操作外围设备的可能性取决于电源状态和硬件特性。积分器应该知道这些属性，是否使用外围设备由积分器负责。

[SWS_Adc_00495] 如果目标电源状态与当前电源状态相同，则不会执行任何操作，API将立即返回E_OK结果。		先决条件的责任留给环境。

[SWS_Adc_00496] 如果激活了开发错误报告：如果在初始化硬件单元之前调用此API，则API应报告DET错误ADC_E_UNINIT。

[SWS_Adc_00497] 如果激活了开发错误报告：如果调用此API时请求了不支持的电源状态或外设根本不支持低电源状态，则API应报告DET错误ADC_E_POWER_STATE_NOT_SUPPORTED。

[SWS_Adc_00498] 如果当前电源状态无法直接达到请求的电源状态，API应报告运行时错误ADC_E_TRANSITION_NOT_POSSIBLE。达到目标电源状态所需的所有异步操作都可以在Adc_Main_PowerTransitionManager上下文中的后台执行。

## Adc_Main_PowerTransitionManager

[SWS_Adc_00479]

|函数名|Adc_Main_PowerTransitionManager|
|--|--|
|声明|void Adc_Main_PowerTransitionManager( void )|
|描述|此API被循环调用并监督电源状态转换，检查模块的就绪状态并发出回调IoHwAb_Adc_NotifyReadyForPowerState＜Mode＞|

[SWS_Adc_00499] 此API执行完成Adc_PreparePowerState()请求的电源状态转换所需的任何非立即操作。

[SWS_Adc_00500] 调度速率应由Adc MainSchedulePeriod定义，并且应该是可变的，因为只有在请求转换时才需要调用该函数。

[SWS_Adc_00501] 该API还应根据配置向最终注册用户（IoHwAbs）发出回调通知，仅在选择异步模式的情况下。

[SWS_Adc_00502] 如果ADC模块未初始化，则此函数应简单返回，无需进一步详细说明。这是为了避免详述未初始化的变量。不应输入DET错误，因为在启动阶段（任务在初始化完成之前启动）可以很容易地验证此条件。         理由：在启动阶段，操作系统可能已经安排了调用主要功能的任务，而一些模块尚未初始化。这不是真正的错误情况，尽管需要处理，即返回而不执行。    虽然过渡状态监视功能是强制性的，但此API的实现是可选的，这意味着如果硬件允许其他方式传递通知和监视过渡状态，则可以跳过此功能的实现。

<br/>

<br/>

<br/>

【SWS_Adc_00078】 ADC模块的ISR提供“转换完成事件（conversion completed events）”，应负责重置中断标志（如果硬件需要）并调用相关通知功能。

## IoHwAb_Adc_Notification<#groupID>

[SWS_Adc_00082]

|函数名|IoHwAb_Adc_Notification<#groupID>|
|--|--|
|声明|void IoHwAb_AdcNotification<#groupID>( void )|
|同步/异步|同步|
|可重入性|不可重入|
|参数（in）|None|
|参数（inout）|None|
|参数（out）|None|
|返回值|None|
|描述|当组＜#groupID＞的组转换完成时，ADC驱动程序将调用。|

[SWS_Adc_00104] ADC驱动程序应支持每个ADC通道组（如果配置了功能）的单独通知，每当该组所有通道的转换完成时，都会调用该通知。

[SWS_Adc_00083] 当禁用通知机制时，ADC模块不应发送通知。

[SWS_Adc_00416] 当重新启用通知时，ADC模块不应发送通知，通知已禁用时发生的事件。

[SWS_Adc_00084] 对于每个组，必须配置特定的通知回调。这可以是函数指针或NULL指针。

[SWS_Adc_00080] 如果为通知回调配置了NULL指针，则不应执行回调。

[SWS_Adc_00085] 回调通知应可配置为指向配置结构内用户定义功能的指针。对于所有可用通道组，必须在模块的配置阶段声明回调函数。

## oHwAb_Adc_NotifyReadyForPowerState<#Mode>

[SWS_Adc_00480] 

|函数名|oHwAb_Adc_NotifyReadyForPowerState<#Mode>|
|--|--|
|声明|void IoHwAb_Adc_NotifyReadyForPowerState<#Mode>(void)|
|同步/异步|同步|
|可重入性|不可重入|
|参数（in）|None|
|参数（inout）|None|
|参数（out）|None|
|返回值|None|
|描述|当模式＜#mode＞的请求电源状态准备完成时，ADC驱动程序应调用API。|