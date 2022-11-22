# FEE

## 一、逻辑块和地址空间

Fee模块向上层提供32位虚拟地址空间（Virtual Linear Address Space），其中

- 前16位为Block Number，最多允许65536个逻辑块（Logical Block）
- 后16位为Block Offset，，即数据在Block中的偏移地址，每个Logical Block最大64K byte

此地址对齐的值可以从底层闪存驱动器和设备的值中得出。此虚拟分页应可通过参数FeeVirtualPageSize进行配置。

每个配置的逻辑块应占用配置的虚拟页面大小的整数倍

<br/>

## 二、FEE模块提供的API

![](https://s2.loli.net/2022/10/12/ndJWcXbz4oGwqkZ.png)

### 1.Fee_Init函数

用于初始化FEE模块

**声明：**

```c_cpp
void Fee_Init(const Fee_ConfigType* ConfigPtr)
```

**时序图：**

![](https://s2.loli.net/2022/10/12/SCsMeVjwILXA3gk.png)

**主要工作：**

- 由EcuM模块调用`Fee_Init`完成对Fee模块的初始化
- 如果初始化在`Fee_Init`内完成，一旦初始化成功完成，函数`Fee_Init`将把模块状态从**MEMIF_UNINIT**设置为**MEMIF_IDLE**。**Job Result**=**MEMIF_JOB_OK**
- `Fee_Init`应该在`Fls_Init`之后

---

### 2.Fee_MainFunction函数

处理请求的读/写/擦除作业和内部管理操作的服务。

函数由Basic Software Scheduler直接调用。函数应无返回值和参数。所有功能均不得重入。

**声明：**

```c_cpp
void Fee_MainFunction(void)
```

**时序图：**

![](https://s2.loli.net/2022/10/12/HCypMZxd96v3bBY.png)

**主要工作：**

- 函数Fee_MainFunction应异步处理上层和内部管理操作请求的读/写/擦除/无效作业。

- `Fee_MainFunction`在处理完一个job后，将调用`Job_End_Notification`，或者如果发生错误，将执行`Job_Error_Notification`

---

### 3.Fee_Read函数

用于启动读取作业的服务。函数Fee_Read应获取块起始地址和偏移量，并计算相应的内存读取地址

**声明：**

```c_cpp
Std_ReturnType Fee_Read(uint16 BlockNumber, uint16 BlockOffset, uint8* DataBufferPtr, uint16 Length)
```

**时序图：**

![](https://s2.loli.net/2022/10/12/FVrMkLvNGoUehaB.png)

**主要工作：**

- `Fee_Read`设置状态**FeeMainJob**=**FEE_MAIN_READ**
- `Fee_MainFunction`执行后检测到**FEE_MAIN_READ**，开始处理
- 将BlockNumber转为BlockId，通过**BlockOffset**、**Length**获取数据的虚拟地址
- 虚拟地址转换为Block Instance在Flash中的实际地址
- 调用`Fls_Read`，将实际地址上的数据存入**DataBufferPtr**指向的RAM区域

<br/>

- 异步Job，在`Fee_MainFunction`中执行
- 此Job只有在**Module Status**==**MEMIF_IDLE**才能请求

---

### 4.Fee_Write函数

用于启动写入作业的服务

函数Fee_Write应采用块起始地址并计算相应的存储器写入地址。块地址偏移应固定为零

**声明：**

```c_cpp
Std_ReturnType Fee_Write(uint16 BlockNumber, const uint8* DataBufferPtr)
```

**时序图：**

![](https://s2.loli.net/2022/10/12/nTUcE7rG2qvmMxS.png)

**主要工作：**

- `Fee_Write`设置状态**FeeMainJob**=**FEE_MAIN_WRITE**
- `Fee_MainFunction`执行后检测到**FEE_MAIN_WRITE**，开始处理
- 将**BlockNumber**转为**BlockId**，转换为Block Instance在Flash中的实际地址
- 调用`Fls_Write`

<br/>

- 异步Job，在Fls_17_Dmu_MainFunction中执行
- 此Job只有在Module Status==MEMIF_IDLE才能请求

---

### 5.Fee_Cancel函数

调用底层闪存驱动程序取消功能的服务。

<br/>

**声明：**

```c_cpp
void Fee_Cancel(void)
```

**时序图：**

![](https://s2.loli.net/2022/10/12/HPw2toXYklRyIWa.png)

**主要工作：**

- 取消当前Job
- 同步Job，立即执行

---

### 6.Fee_GetStatus函数

返回当前状态，当前FEE模块状态包括：

- MEMIF_UNINIT					：FEE模块未初始化
- MEMIF_IDLE						：FEE模块当前空闲
- MEMIF_BUSY						：FEE模块当前正在处理
- MEMIF_BUSY_INTERNAL	：FEE模块当前正在做内部管理

**声明：**

```c_cpp
MemIf_StatusType Fee_GetStatus(void)
```

**主要工作：**

- 返回当前的FEE模块状态
- 同步job，立即执行

---

### 7.Fee_GetJobResult函数

查询上层软件发出的上一次接受作业的结果的服务。

返回的JobResult状态包括：

- MEMIF_JOB_OK							：最后一个作业成功完成
- MEMIF_JOB_PENDING				：最后一个作业正在等待执行
- MEMIF_JOB_CANCELED				：最后一个作业已经被取消了（意味着他失败）
- MEMIF_JOB_FAILED					：最后一个作业没有成功
- MEMIF_BLOCK_INCONSISTENT	：请求的块不一致，他可能包含损坏的数据
- MEMIF_BLOCK_INVALID				：请求的块已经失效，无法执行请求的读取操作

**声明：**

```c_cpp
MemIf_JobResultType Fee_GetJobResult(void)
```

**主要工作：**

- 返回最近或当前处理的Job Result
- 同步job，立即执行

---

### 8.Fee_SetMode函数

用于切换底层flash Driver模式的函数

**声明：**

```c_cpp
void Fee_SetMode(MemIf_ModeType Mode)
```

**时序图：**

![](https://s2.loli.net/2022/10/12/eAx2ynbr9PQZCEN.png)

**主要工作：**

- 调用`Fls_SetMode`
- 同步job，立即执行
- 此job只有在**Module Status**==**MEMIF_IDLE**才能请求
- `Set Mode supported`决定是否启用API

---