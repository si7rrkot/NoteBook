# FLASH

## 需求描述

[SWS_Fls_00001] FLS模块应为闪存操作（读取/擦除/写入）提供异步服务

[SWS_Fls_00002] FLS模块不应缓冲数据。FLS模块应使用通过API传递的指针引用的应用程序数据缓冲区。

[SWS_Fls_00003] FLS模块不应确保给定的应用程序缓冲区的数据一致性

[SWS_Fls_00205] FLS模块应静态检查静态配置参数（最迟在编译期间）的正确性

[SWS_Fls_00206] FLS模块应验证FLS模块头文件和源文件中的版本信息是否一致（例如，通过将模块头文件与源文件的版本信息与预处理器宏进行比较）。

[SWS_Fls_00208] FLS模块应将所有可用闪存区域组合成一个线性地址空间（由参数FlsBaseAddress和FlsTotalSize表示）。

[SWS_Fls_00209] FLS模块应根据闪存区的物理结构，将读、写、擦除和比较功能的地址和长度参数作为“虚拟”地址映射到物理地址

[SWS_Fls_00389] FLS模块应在内部处理数据缓冲区对齐。它不会对RAM缓冲区的对齐施加任何要求（因为它们是uint8*），而是将传递的指针作为字节对齐来处理。

[SWS_Fls_00390] 如果一个ECU中使用了多个闪存驱动程序实例，则必须为各个实例指定一个唯一的实例ID。该实例ID应配置为参数FlsDriverIndex。如果ECU中只使用一个闪存驱动器实例，则该实例的参数FlsDriverIndex应配置为0

[SWS_Fls_00310] 当使用错误参数调用API服务时，应报告以下开发错误代码：FLS_E_PARAM_CONFIG、FLS_E_PERAM_ADDRESS、FLS_E_PARAM_LENGTH、FLS_E_PARAM_DATA。

[SWS_Fls_00311] 在模块初始化之前调用API服务时，应报告开发错误代码FLS_E_UNINIT。函数Fls_Init和Fls_GetVersionInfo除外。

[SWS_Fls_00312] 当模块仍然繁忙时调用API服务时，应报告开发错误代码FLS_E_BUSY。

[SWS_Fls_00313] 当启用擦除验证功能（通过编译开关FlsEraseVerificationEnabled）和擦除验证函数（blankcheck）时，应报告运行时错误代码FLS_E_VERIFY_ERASE_FAILED

[SWS_Fls_00314] 当写入验证功能启用（通过编译开关FlsWriteVerificationEnabled）且写入验证功能（compare）失败时，应报告运行时错误代码FLS_E_VERIFY_WRITE。

[SWS_Fls_00361] 当启用超时监控功能（通过编译开关FlsTimeoutSupervisionEnabled），并且读取、写入、擦除或比较作业（在硬件中）的超时监控失败时，应报告运行时错误代码FLS_E_TIMEOUT。

[SWS_Fls_00315] 当闪存擦除功能失败（硬件中）时，应报告瞬态故障代码FLS_E_ERASE_FAILED

[SWS_Fls_00316] 当闪存写入功能失败（在硬件中）时，应报告瞬态故障代码FLS_E_WRITE_FAILED。

[SWS_Fls_00317] 当闪存读取功能失败（硬件中）时，应报告瞬态故障代码FLS_E_READ_FAILED

[SWS_Fls_00318] 当闪存比较功能失败（硬件中）时，应报告瞬态故障代码FLS_E_COMPARE_FAILED

[SWS_Fls_00319] 当预期闪存ID不匹配时，应报告瞬态故障代码FLS_E_UNEXPECTED_FLASH_ID

[SWS_Fls_00144] 在外部闪存驱动器初始化期间，FLS模块应根据相应的发布参数检查外部闪存设备的硬件ID。如果发生硬件ID不匹配，FLS模块应向默认错误跟踪程序（DET）报告错误代码FLS_E_UNEXPECTED_FLASH_ID，将FLS模块状态设置为FLS_E_UNINIT，并且不应自行初始化。

[SWS_Fls_00137] FLS模块的实施者应将闪存访问例程的代码放入单独的C模块FLS_ac.C中

[SWS_Fls_00215] FLS模块的闪存访问例程只能禁用中断，并在必要时等待擦除/写入命令完成（即必须确保在此期间没有执行其他代码）。

[SWS_Fls_00211] FLS模块的实施者应尽可能缩短闪存访问代码的执行时间

[SWS_Fls_00140] 如果FLS模块配置为在作业启动时将闪存访问代码加载到RAM，则FLS模块的擦除程序应加载闪存访问代码，以将闪存擦除到RAM中闪存驱动器配置集中包含的擦除功能指针所指向的位置

[SWS_Fls_00141] 如果FLS模块配置为在作业启动时将闪存访问代码加载到RAM，则FLS模块的写入例程应加载闪存访问代码，以将闪存写入RAM中闪存驱动器配置集中包含的写入功能指针指向的位置。

[SWS_Fls_00212] FLS模块的主处理程序应执行闪存访问代码程序

[SWS_Fls_00213] FLS模块的主处理程序应通过FLS模块配置集（编译后参数）中包含的相应功能指针访问闪存访问代码程序，无论闪存访问代码例程是否已加载到RAM中，或是否可以直接从（闪存）ROM执行

[SWS_Fls_00143] 擦除或写入作业完成或取消后，如果闪存驱动器已将闪存访问代码（内部擦除/写入例程）加载到RAM，则FLS模块的主处理例程应从RAM中卸载（即覆盖）闪存访问代码。

[SWS_Fls_00214] 如果无法从闪存ROM执行访问代码，FLS模块只能将访问代码加载到RAM

---

### Fls_Init

[SWS_Fls_00014] 功能Fls_Init应使用给定配置集中提供的参数初始化Fls模块（软件）和所有闪存相关寄存器（硬件）。

[SWS_Fls_00191] 函数Fls_Init应将指向给定配置集的指针存储在变量中，以允许Fls模块在运行时访问配置集内容

[SWS_Fls_00086] 功能Fls_Init应初始化所有Fls模块全局变量以及控制闪存设备所需的控制器寄存器，这些寄存器不影响或依赖于其他（硬件）模块。可以影响或依赖于其他模块的寄存器应通过通用系统模块进行初始化。

[SWS_Fls_00015] 如果模块Fls的开发错误检测已启用：功能Fls_Init应检查给定配置集的（硬件特定）内容是否在允许范围内。如果不是这样，则会引发开发错误FLS_E_PARAM_CONFIG

[SWS_Fls_00323] 完成Fls模块初始化后，功能Fls_Init应将Fls模块状态设置为MEMIF_IDLE。

[SWS_Fls_00324]完成Fls模块初始化后，功能Fls_Init应将闪存作业结果设置为MEMIF_JOB_OK 

[SWS_Fls_00268] 如果模块Fls的开发错误检测被启用：功能Fls_Init应检查Fls模块当前不忙碌（Fls模块状态不是MEMIF_BUSY）。如果此检查失败，函数Fls_Init将引发开发错误FLS_E_BUSY。

[SWS_Fls_00048] 如果硬件支持，功能Fls_Init应按照配置集的规定设置闪存擦除/写入保护

---

### Fls_Erase

[SWS_Fls_00218] Fls_Erase的作业应擦除一个或多个完整的闪存扇区。

[SWS_Fls_00327] 函数Fls_Erase应将给定参数复制到Fls模块内部变量，并启动擦除作业

[SWS_Fls_00328] 启动擦除作业后，功能Fls_erase应将Fls模块状态设置为MEMIF_BUSY

[SWS_Fls_00329] 启动擦除作业后，函数Fls_erase应将作业结果设置为MEMIF_JOB_PENDING

[SWS_Fls_00330] 启动擦除作业后，函数Fls_erase将返回E_OK

[SWS_Fls_00220] FLS模块应在FLS模块的主功能内异步执行功能FLS_Erase的作业

[SWS_Fls_00221] 函数Fls_Erase的作业应擦除从长度为的FlsBaseAddress+TargetAddress开始的闪存块。

[SWS_Fls_00020] 如果模块Fls的开发错误检测已启用：函数Fls_Erase应检查擦除起始地址（FlsBaseAddress+TargetAddress）是否与闪存扇区边界对齐，并且是否位于指定的闪存上下边界内。如果该检查失败，函数Fls_Erase应拒绝擦除请求，提出开发错误Fls_E_PARAM_ADDRESS，并返回E_NOT_OK

[SWS_Fls_00021] 如果模块Fls的开发错误检测已启用：功能Fls_Erase应检查擦除长度是否大于0，擦除结束地址（擦除开始地址+长度）是否与闪存扇区边界对齐，并且是否位于指定的闪存地址上限边界内。如果此检查失败，函数Fls_Erase将拒绝擦除请求，引发开发错误Fls_E_PARAM_LENGTH，并返回E_NOT_OK

[SWS_Fls_00065] 如果模块Fls的开发错误检测已启用：功能Fls_Erase应检查Fls模块是否已初始化。如果此检查失败，函数Fls_Erase将拒绝擦除请求，引发开发错误Fls_E_UNINIT并返回E_NOT_OK

[SWS_Fls_00023] 如果模块Fls的开发错误检测已启用：功能Fls_Erase应检查Fls模块当前是否繁忙。如果此检查失败，函数Fls_Erase应拒绝擦除请求，提出开发错误Fls_E_BUSY并返回E_NOT_OK

[SWS_Fls_00145] 如果可能，例如在中断控制实现中，FLS模块应直接在函数FLS_erase内启动第一轮擦除作业，以减少整体运行时间

---

### Fls_Write

[SWS_Fls_00223] 函数Fls_Write的工作应向闪存设备写入一个或多个完整的闪存页面。

[SWS_Fls_00331] 函数Fls_Write应将给定参数复制到Fls模块内部变量，并启动写入作业

[SWS_Fls_00332] 启动写入作业后，功能Fls_write应将Fls模块状态设置为MEMIF_BUSY

[SWS_Fls_00333] 启动写入作业后，函数Fls_write应将作业结果设置为MEMIF_JOB_PENDING

[SWS_Fls_00334] 启动写入作业后，函数Fls_write将返回E_OK。

[SWS_Fls_00225] FLS模块应在FLS模块的主功能内异步执行功能FLS_write的写入作业

[SWS_Fls_00226] 函数Fls_Write的工作应使用通过SourceAddressPtr提供的数据对闪存块进行编程，数据从大小为Length的FlsBaseAddress+TargetAddress开始

[SWS_Fls_00026] 如果模块Fls的开发错误检测已启用：功能Fls_Write应检查写入起始地址（FlsBaseAddress+TargetAddress）是否与闪存页边界对齐，并且是否位于指定的闪存地址下限和上限边界内。如果此检查失败，函数Fls_Write将拒绝写入请求，引发开发错误Fls_E_PARAM_ADDRESS，并返回E_NOT_OK。

[SWS_Fls_00027] 如果模块Fls的开发错误检测已启用：功能Fls_Write应检查写入长度是否大于0，写入结束地址（写入起始地址+长度）是否与闪存页边界对齐，并且是否位于指定的闪存地址上限内。如果此检查失败，函数Fls_Write将拒绝写入请求，引发开发错误Fls_E_PARAM_LENGTH，并返回E_NOT_OK。

[SWS_Fls_00066] 如果模块Fls的开发错误检测已启用：功能Fls_Write应检查Fls模块是否已初始化。如果此检查失败，函数Fls_Write将拒绝写入请求，引发开发错误FLS_E_UNINIT并返回E_NOT_OK

[SWS_Fls_00030] 如果模块Fls的开发错误检测已启用：功能Fls_Write应检查Fls模块当前是否繁忙。如果此检查失败，函数Fls_Write将拒绝写入请求，引发开发错误FLS_E_BUSY 并返回E_NOT_OK

[SWS_Fls_00157] 如果模块Fls的开发错误检测已启用：函数Fls_Write应检查给定的数据缓冲区指针是否不是空指针。如果数据缓冲区指针为空指针，函数Fls_Write将拒绝写入请求，引发开发错误Fls_E_PARAM_data并返回E_NOT_OK。

[SWS_Fls_00146] 如果可能，例如在中断控制实现中，FLS模块应直接在函数Fls_write内启动第一轮写入作业，以减少整体运行时间

---

### Fls_Cancel

[SWS_Fls_00229] 功能Fls_Cancel应取消正在进行的闪存读取、写入、擦除或比较作业

[SWS_Fls_00230] 函数Fls_Cancel应同步中止正在运行的作业，以便从该函数返回后可以直接启动新作业。

[SWS_Fls_00335] 函数Fls_Cancel应重置Fls模块的内部作业处理变量（如地址、长度和数据指针）。

[SWS_Fls_00336] 功能Fls_Cancel应将Fls模块状态设置为MEMIF_IDLE。

[SWS_Fls_00033] 如果作业结果当前的值为MEMIF_JOB_PENDING，则函数Fls_Cancel应将作业结果设置为MEMIF MEMIF_JOB_CANCELED。否则，函数Fls_Cancel将保持作业结果不变。

[SWS_Fls_00147] 如果已配置，函数Fls_Cancel应调用错误通知函数，以通知调用者取消作业

[SWS_Fls_00183] 功能Fls_Cancel应可通过配置参数FlsCancelApi在编译前配置开/关

[SWS_Fls_00356] 如果模块Fls的开发错误检测已启用：功能Fls_Cancel应检查Fls模块是否已初始化。如果此检查失败，函数Fls_Cancel将引发开发错误Fls_E_UNINIT并返回

---

### Fls_GetStatus

[SWS_Fls_00034] 函数Fls_GetStatus应同步返回Fls模块状态

[SWS_Fls_00184] 通过配置参数FlsGetStatusApi，函数Fls_GetStatus应为预编译时可配置的开/关

---

### Fls_GetJobResult

[SWS_Fls_00035] 函数Fls_GetJobResult应同步返回上一个作业的结果

[SWS_Fls_00036] 擦除、写入、读取和比较功能应共享相同的作业结果，即只能查询最后一个作业的结果。如果FLS模块接受了新作业，则FLS模块应使用MEMIF_job_PENDING覆盖作业结果。

[SWS_Fls_00185] 函数Fls_GetJobResult应可通过配置参数FlsGetJobResultApi在编译前配置On/Off

[SWS_Fls_00358] 如果模块Fls的开发错误检测已启用：函数Fls_GetJobResult应检查Fls模块是否已初始化。如果此检查失败，函数Fls_GetJobResult将引发开发错误FLS_E_UNINIT，并返回MEMIF_JOB_FAILED

---

### Fls_Read

[SWS_Fls_00236] 函数Fls_Read应从闪存中读取

[SWS_Fls_00337] 函数Fls_Read将把给定的参数复制到Fls模块内部变量，并启动读取作业

[SWS_Fls_00338] 启动读取作业后，功能Fls_read应将Fls模块状态设置为MEMIF_BUSY

[SWS_Fls_00339] 启动读取作业后，函数Fls_read应将Fls模块作业结果设置为MEMIF_JOB_PENDING。

[SWS_Fls_00340] 启动读取作业后，函数Fls_read应返回E_OK

[SWS_Fls_00238] Fls模块应在Fls模块的主功能内异步执行功能Fls_Read的读取作业

[SWS_Fls_00239] 函数Fls_Read的读取作业应将一个从长度为的FlsBaseAddress+SourceAddress开始的连续闪存块复制到TargetAddressPtr指向的缓冲区。

[SWS_Fls_00097] 如果模块Fls的开发错误检测已启用：函数Fls_Read应检查读取起始地址（FlsBaseAddress+SourceAddress）是否位于指定的闪存地址下限和上限边界内。如果此检查失败，函数Fls_Read将拒绝读取作业，引发开发错误Fls_E_PARAM_ADDRESS，并返回E_NOT_OK。

[SWS_Fls_00098] 如果模块Fls的开发错误检测已启用：功能Fls_Read应检查读取长度是否大于0，以及读取结束地址（读取起始地址+长度）是否位于指定的闪存地址上限内。如果此检查失败，函数Fls_Read将拒绝读取作业，引发开发错误FLS_E_PARAM_LENGTH，并返回E_NOT_OK

[SWS_Fls_00099] 如果模块Fls的开发错误检测已启用：函数Fls_Read应检查驱动程序是否已初始化。如果此检查失败，函数Fls_Read将拒绝读取请求，引发开发错误FLS_E_UNINIT 并返回E_NOT_OK

[SWS_Fls_00100] 如果模块Fls的开发错误检测已启用：功能Fls_Read应检查驱动程序当前是否繁忙。如果此检查失败，函数Fls_Read将拒绝读取请求，引发开发错误FLS_E_BUSY并返回E_NOT_OK。

[SWS_Fls_00158] 如果模块Fls的开发错误检测已启用：函数Fls_Read应检查给定的数据缓冲区指针是否不是空指针。如果数据缓冲区指针为空指针，函数Fls_Read应拒绝读取请求，引发开发错误Fls_E_PARAM_data并返回E_NOT_OK。

[SWS_Fls_00240] Fls模块的环境只能在Fls模块初始化后调用函数Fls_Read

---

### Fls_Compare

[SWS_Fls_00241] 函数Fls_Compare应将闪存区的内容与应用程序数据缓冲区的内容进行比较。

[SWS_Fls_00341] 函数Fls_Compare应将给定参数复制到Fls模块内部变量，并启动比较作业

[SWS_Fls_00342] 启动比较作业后，功能Fls_Compare应将状态设置为MEMIF_BUSY

[SWS_Fls_00343] 启动比较作业后，函数Fls_Compare应将作业结果设置为MEMIF_JOB_PENDING

[SWS_Fls_00344] 启动比较作业后，函数Fls_Compare应返回E_OK

[SWS_Fls_00243] FLS模块应在Fls模块的主功能内异步执行功能Fls_Compare的作业

[SWS_Fls_00244] 函数Fls_Compare的作业应将从FlsBaseAddress+SourceAddressof size Length开始的连续闪存块与TargetAddressPtr指向的缓冲区进行比较

[SWS_Fls_00150] 如果模块Fls的开发错误检测已启用：函数Fls_Compare应检查比较起始地址（FlsBaseAddress+SourceAddress）是否位于指定的闪存地址下限和上限边界内。如果此检查失败，函数Fls_Compare将拒绝比较作业，引发开发错误Fls_E_PARAM_ADDRESS，并返回E_NOT_OK。

[SWS_Fls_00151] 如果模块Fls的开发错误检测已启用：功能Fls_Compare应检查给定长度是否大于0，以及比较结束地址（比较起始地址+长度）是否位于指定的闪存地址上限内。如果此检查失败，函数Fls_Compare将拒绝比较作业，引发开发错误FLS_E_PARAM_LENGTH，并返回E_NOT_OK

[SWS_Fls_00152] 如果模块Fls的开发错误检测已启用：函数Fls_Compare应检查驱动程序是否已初始化。如果此检查失败，函数Fls_Compare将拒绝比较作业，引发开发错误FLS_E_UNINIT并返回E_NOT_OK

[SWS_Fls_00153] 如果模块Fls的开发错误检测已启用：功能Fls_Compare应检查驱动程序当前是否繁忙。如果此检查失败，函数Fls_Compare将拒绝比较作业，引发开发错误FLS_E_BUSY并返回E_NOT_OK

[SWS_Fls_00273] 如果模块Fls的开发错误检测已启用：函数Fls_Compare应检查给定的数据缓冲区指针是否不是空指针。如果数据缓冲区指针为空指针，函数Fls_Compare应拒绝请求，引发开发错误FLS_E_PARAM_DATA并返回E_NOT_OK

[SWS_Fls_00186] 通过配置参数FlsCompareApi，函数Fls_Compare应为预编译时可配置的开/关

---

### Fls_SetMode

[SWS_Fls_00155] 功能Fls_SetMode应将Fls模块的操作模式设置为给定的“mode”参数。

[SWS_Fls_00156] 如果模块Fls的开发错误检测已启用：功能Fls_SetMode应检查Fls模块当前是否繁忙。如果该检查失败，功能Fls_SetMode应拒绝设置模式请求，并引发开发错误代码Fls_E_BUSY。

[SWS_Fls_00187] 功能Fls_SetMode应可通过配置参数FlsSetModeApi在编译前配置开/关

---

### Fls_GetVersionInfo

[SWS_Fls_00363] 如果模块Fls的开发错误检测已启用：如果参数为NULL指针，函数Fls_GetVersionInfo将引发开发错误Fls_E_PARAM_POINTER，并返回而不执行任何操作。

---

### Fls_BlankCheck

[SWS_Fls_00373] 函数Fls_BlankCheck应验证给定的存储区域是否已被擦除但（尚未）重新编程

[SWS_Fls_00374] 函数Fls_BlankCheck应将给定参数复制到Fls模块内部变量，并启动验证工作

[SWS_Fls_00375] 启动验证工作后，功能Fls_BlankCheck应将Fls模块状态设置为MEMIF_BUSY

[SWS_Fls_00376] 启动验证作业后，功能Fls_BlankCheck应将Fls模块作业结果设置为MEMIF_JOB_PENDING

[SWS_Fls_00377] 启动验证作业后，函数Fls_BlankCheck应返回E_OK

[SWS_Fls_00378] Fls模块应在Fls模块的主功能内异步执行功能Fls_BlankCheck的验证工作。

[SWS_Fls_00379] 函数Fls_BlankCheck的验证工作应检查从长度为FlsBaseAddress+TargetAddress的连续闪存区域是否被擦除

[SWS_Fls_00380] 如果模块Fls的开发错误检测已启用；函数Fls_BlankCheck应检查验证起始地址（FlsBaseAddress+TargetAddress）是否位于指定的闪存地址下限和上限边界内。如果此检查失败，函数Fls_BlankCheck将拒绝验证作业，引发开发错误FLS_E_PARAM_ADDRESS，并返回E_NOT_OK

[SWS_Fls_00381] 如果模块Fls的开发错误检测已启用：功能Fls_BlankCheck应检查给定长度是否大于0，验证结束地址（验证开始地址+长度）是否位于指定的闪存地址上限内。如果此检查失败，函数Fls_BlankCheck将拒绝验证作业，引发开发错误FLS_E_PARAM_LENGTH，并返回E_NOT_OK

[SWS_Fls_00382] 如果模块Fls的开发错误检测已启用：功能Fls_BlankCheck应检查驱动程序是否已初始化。如果此检查失败，函数Fls_BlankCheck将拒绝验证请求，提出开发错误FLS_E_UNINIT并返回E_NOT_OK。

[SWS_Fls_00383] 如果模块Fls的开发错误检测已启用：功能Fls_BlankCheck应检查驱动程序当前是否繁忙。如果此检查失败，函数Fls_BlankCheck将拒绝验证请求，提出开发错误FLS_E_BUSY并返回E_NOT_OK。

[SWS_Fls_00193] 根据实现，Fls模块提供和/或调用的回调例程可以在中断级别调用。因此，提供这些例程的模块必须确保其运行时间相当短，即因为回调可能通过几个软件层向上传播。

[SWS_Fls_00269] Fls模块应仅提供一个预定功能。从闪存读取/写入闪存通常不能同时完成，并且同步两个预定功能的开销将超过好处。

[SWS_Fls_00037] 函数Fls_MainFunction应执行闪存读取、写入、擦除和比较作业的处理

[SWS_Fls_00038] 当作业启动后，Fls模块的环境将循环调用函数Fls_MainFunction，直到作业完成。

[SWS_Fls_00039] 如果没有挂起的作业，则函数Fls_MainFunction将不执行任何操作而返回。

[SWS_Fls_00040] 函数Fls_MainFunction在一个调用周期内只能处理与为当前作业类型(读取、写入或比较)和当前Fls模块的操作模式(正常、快速)静态配置的数据一样多的数据。

[SWS_Fls_00104] 如果闪存擦除作业因硬件错误而失败，则函数Fls_MainFunction应将作业结果设置为MEMIF_JOB_FAILED，并向DET报告错误代码FLS_E_ERASE_FAILED

[SWS_Fls_00105] 如果闪存写入作业因硬件错误而失败，则函数Fls_MainFunction应将作业结果设置为MEMIF_JOB_FAILED，并向DET报告错误代码FLS_E_WRITE_FAILED

[SWS_Fls_00106] 如果闪存读取作业因硬件错误而失败，则函数Fls_MainFunction应将作业结果设置为MEMIF_JOB_FAILED，并向DET报告错误代码FLS_E_READ_FAILED

[SWS_Fls_00154] 如果闪存比较作业因硬件错误而失败，则函数Fls_MainFunction应将作业结果设置为MEMIF_JOB_FAILED，并向DET报告错误代码FLS_E_COMPARE_FAILED。

[SWS_Fls_00385] 如果底层闪存技术需要读取地址或长度信息的某种对齐，并且如果读取或比较作业的地址和/或长度参数未正确对齐，则函数Fls_MainFunction应在内部补偿这种缺失的对齐，即函数Fls_MainFunctional应提供对闪存的逐字节读取访问，不考虑硬件施加的任何对齐限制

[SWS_Fls_00200] 如果来自闪存比较作业的比较数据不相等，函数Fls_MainFunction应将作业结果设置为MEMIF_BLOCK_INCONSISTENT。

[SWS_Fls_00022] 如果启用擦除验证（编译开关FlsEraseVerificationEnabled设置为TRUE）：闪存块被擦除后，函数Fls_MainFunction应将寻址内存区的内容与被擦除闪存单元的值进行比较，以检查该块是否已被完全擦除。如果此检查失败，函数Fls_MainFunction应将Fls模块的作业结果设置为MEMIF_JOB_FAILED ，并引发运行时错误FLS_E_VERIFY_ERASE_FAILED。

[SWS_Fls_00055] 如果启用擦除验证（编译开关FlsEraseVerificationEnabled设置为TRUE）：在写入闪存块之前，函数Fls_MainFunction应将寻址内存区的内容与已擦除闪存单元的值进行比较，以检查该块是否已完全擦除。如果此检查失败，函数Fls_MainFunction应将Fls模块的作业结果设置为MEMIF_JOB_FAILED，并引发运行时错误FLS_E_VERIFY_ERASE_FAILED。

[SWS_Fls_00056] 如果启用了写入验证（编译开关FlsWriteVerificationEnabled设置为TRUE）：写入闪存块后，函数Fls_MainFunction应将重新编程的内存区域的内容与提供的应用程序缓冲区的内容进行比较，以检查块是否已完全重新编程。如果此检查失败，函数Fls_MainFunction应将Fls模块的作业结果设置为MEMIF_JOB_FAILED，并引发运行时错误FLS_E_VERIFY_WRITE_FAILED

[SWS_Fls_00345] 读取、擦除、写入或比较作业完成后，如果Fls模块当前处于MEMIF_JOB_PENDING状态，则函数Fls_MainFunction应将Fls模块的作业结果设置为MEMIF_JOB_OK。否则，应保持结果不变。

[SWS_Fls_00346] 读取、擦除、写入或比较作业完成后，功能Fls_MainFunction应将Fls模块的状态设置为MEMIF_IDLE，并调用作业结束通知功能（如果已配置）

[SWS_Fls_00232] 如果闪存硬件支持，配置参数FlsUseInterrupts应在中断和轮询控制作业处理之间切换。

[SWS_Fls_00233] FLS模块的实施者应在FLS_Irq.c中定位中断服务例程

[SWS_Fls_00234] 如果支持中断控制作业处理并使用配置参数FlsUseInterrupts启用，则中断服务例程应重置中断标志，检查底层硬件报告的错误，为下一轮挂起作业重新加载硬件有限状态机，或在作业完成或中止时调用适当的通知例程。

[SWS_Fls_00235] 函数Fls_MainFunction应在不支持硬件中断的情况下处理作业（例如读取作业）

[SWS_Fls_00272] 如果启用超时监控（编译开关FlsTimeoutSupervisionEnabled设置为TRUE）：函数Fls_MainFunction应为当前运行的作业提供超时监控，即应监控读/比较/擦除或写入作业的截止时间

[SWS_Fls_00359] 如果启用了超时监督（编译开关FlsTimeoutSupervisionEnabled设置为TRUE）：函数Fls_MainFunction应检查是否已超过配置的最大擦除时间。如果是这种情况，函数Fls_MainFunction将引发运行时错误FLS_E_TIMEOUT。

[SWS_Fls_00360] 如果启用了超时监控（编译开关FlsTimeoutSupervisionEnabled设置为TRUE）：函数Fls_MainFunction应检查是否超过了预期的最大写入时间。如果是这种情况，函数Fls_MainFunction将引发运行时错误Fls_E_TIMEOUT。

[SWS_Fls_00362] 如果启用了超时监控（编译开关FlsTimeoutSupervisionEnabled设置为TRUE）：函数Fls_MainFunction应检查是否超过了预期的最大读取/比较时间。如果是这种情况，函数Fls_MainFunction将引发运行时错误FLS_E_TIMEOUT。

[SWS_Fls_00117] 如果模块Fls的开发错误检测已启用：功能Fls_MainFunction应检查Fls模块是否已初始化。如果此检查失败，函数Fls_MainFunction将引发开发错误Fls_E_UNINIT。

[SWS_Fls_00196] 功能Fls_MainFunction在每个循环中最多应发出一个扇区擦除命令（对硬件）。

---

[SWS_Fls_00109] 作业处理回调通知应可配置为初始化数据结构（Fls_ConfigType）内的函数指针

[SWS_Fls_00110] 回调通知应没有参数和返回值

### Fee_JobEndNotification

[SWS_Fls_00167] 当FLS模块完成作业并获得OK结果时，FLS模块应调用回调函数Fee_JobEndNotification

---

### Fee_JobErrorNotification

[SWS_Fls_00347] 当FLS模块完成一个结果为NOT_OK的作业时，FLS模块应调用回调函数Fee_JobErrorNotification

[SWS_Fls_00348] 当模块取消正在进行的作业时，FLS模块应调用回调函数Fee_JobErrorNotification

[SWS_Fls_00349] 当模块完成比较作业且内存块不同时，FLS模块应调用回调函数Fee_JobErrorNotification