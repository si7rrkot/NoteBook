# ADC

## 一、ADC类型

ADC按照工作原理可以分为直接型和间接型，直接型分为**并联比较型**和**逐次逼近型**，间接型则有**双积分型**。

![](https://s2.loli.net/2022/10/26/qyNnuoKx7r5DHWM.png)

## 二、ADC转换原理与步骤

A/D转换的作用是将时间、幅值**连续**的模拟信号转换为时间、幅值**离散**的数字信号。所以A\D转换一般要经过**采样**、**保持**、**量化**和**编码**四个过程。

### 1.采样和保持（Sampling and Holding）

采样是指在时间上将模拟信号离散化，即是将时间上连续的信号转为一系列等时间间隔的信号离散序列。其中离散信号脉冲的幅度取决于输入模拟量。

> 采样需要满足采样原理: 采样频率大于模拟信号中最高频率成分的两倍时，采样值才能不失真的反映原来模拟信号。

![](https://s2.loli.net/2022/10/26/sH7ztX9wUZVM3uK.png)

### 2.量化和编码(Quantizing and Encoding)

量化是用有限个幅度值近似原来连续变化的幅度值，把模拟信号的连续幅度变为有限数量的有一定间隔的离散值。而编码则是按照一定的规律，把量化后的值用二进制数字表示。

> n:分辨率，用于对输入进行量化的位数
FSR: Full-Scale Range，满量程
LSB: Least Significant Bit，最低有效位
MSB: Most Significant Bit，最高有效位

<br/>

用ADI的高速ADC LTC2380的datasheet部分参数来进行举例。

### 3.分辨率（Resolution）

<br/>

ADC的分辨率是指模数转换器所能表示的最大位数是多少，即ADC的位数。

分辨率是ADC最基本的参数，可以用表示每个模拟信号值的位数（二进制）来表示。一个4位ADC能表示16个不同的模拟信号值，因为2的4次方是16。位数越多，转换的精度越高，分辨率也就越大。注意，ADC的精度并不仅仅取决于分辨率。

![](https://s2.loli.net/2022/10/27/cwvrmOk7C1uPyMo.png)

### 4.采样速率（Sampling Time）

这里说的采样速率就是我们最高的ADC采样频率，也就是数据手册中的Maximum Sampling Frequency。**ADC的采样速率必须小于转换速率**，常用单位是 ksps 和 Msps，表示每秒 采样千/百万次（kilo / Million Samples per Second）。

![](https://s2.loli.net/2022/10/27/7TPC9gWEp5JaDc2.png)

### 5.转换时间（Conversion Time）

转换时间的导数就是转换速率。因为将一个模拟信号值转换成一个数字量不能瞬间完成，这个过程需要一定的时间。下图说明了转化时间的基本概念，在t0时刻进行模拟电压值的转换，但是直到t1时刻才完成转换。

![](https://s2.loli.net/2022/10/27/iNXpPW821GQzs9l.png)

积分型 AD 的转换时间是毫秒级属低速 AD，逐次比较型 AD 是微秒级属中速 AD，全并行/ 串并行型 AD 可达到纳秒级。转换时间是衡量一个ADC是不是高速的主要指标，高速ADC转换时间小于1us，低俗ADC转换时间大于300us。

![](https://s2.loli.net/2022/10/27/uZgnsvmtbEwLD3F.png)

### 6.量程（full-scale range, FSR）

作为测量元件，量程一定是需要关注的，指的是允许输入的模拟信号范围。

![](https://s2.loli.net/2022/10/27/IBhW4g9SyAZFYCf.png)

### 7.最低有效位（One least significant bit, LSB）

又称最小分辨率，要记住这个参数的英文LSB，**满量程值除以ADC的分辨率就是LSB**，很好理解，就比如说一个4位的ADC，数字量最高可以表示2的4次方也就是16，满量程5V，那么最小的分辨率就是5/16=0.31V，也就是说这个ADC最小辨认的电压是0.31V，可以用数字量0001表示0.31V这个模拟量。LSB越小表明ADC的精度越高。下图纵坐标是数字量编码，横坐标每一个台阶就代表LSB。

![](https://s2.loli.net/2022/10/27/NWIrbAyhj5dlMk2.png)

### 8.香农采样定律

采样定理的提出者不是Nyquist而是Shannon, Nyquist定理/频率是用来描述给定带宽的最高传输速率。因为结果相似，所以大家把Nyquist的名字加在采样定理之前, 作为一种荣誉。

它的标准名字应该是Nyquist-Shannon 采样定理。

![](https://s2.loli.net/2022/10/27/qBxa12df8SgDUPk.png)

我们可以用一个旋转轮来形象理解这个定理，

这是一个各个轴之间间隔45度的轮子，每个轮子都被标上了标识。

假设这个轮子以每秒45度来转动，那么每个轴返回原位需要8秒（采样周期）。

那么如果我们每8,16,24秒来用相机拍照，是不是每次都可以拍摄到原图像静止不动？

这是因为在采样周期内，车轮旋转的整数周期都会回到原位，不论旋转方向如何。那么就有了一个非常重要的结论：

**采样周期的整数倍不能检测到相位（状态）变化。**

我们来减少一点拍摄周期，如果以每4秒的速度拍摄呢？

每4秒拍照一次，轮子只能转一半，那么我们可以在照片中检测到轮子正在旋转，虽然依然不能区分它的旋转方向，但是轮子的状态（相位）已经可以区分了。

那么再减少一点拍摄周期，以每3秒的速度拍摄呢？

无论顺时针还是逆时针，都可以看到轮轴的错位（相位的变化）。

这就是Nyquist-Shannon采样定理，**我们希望同时看到轮子的旋转和相位变化，采样周期要小于整数周期的1/2，采样频率应该大于原始频率的2倍。同理，对于模拟信号，我们希望同时看到信号的各种特性，采样频率应该大于原始模拟信号的最大频率的两倍，否则将发生混叠（相位/频率模糊）。**

## 三、一般行为

ADC模块可以允许将一个或多个ADC通道（ADC channel）分组为所谓的ADC通道组（ADC Channel group）。也即是说ADC模块的配置应使得一个ADC通道组包含至少一个ADC通道。

如果启用了全局限制检查功能并且为ADC通道启用了特定于通道的限制检查，则 ADC模块的配置应使ADC通道组仅包含一个ADC通道。

ADC模块应允许将一个ADC通道分配给多个ADC通道组。

ADC模块的配置应使得包含在一个ADC通道组中的所有通道应属于同一个ADC硬件单元。

### 1.初始化

![](https://s2.loli.net/2022/10/27/vC1TsL8zdPuY3g2.png)

这里EcuM调用`Adc_Init`函数初始化ADC模块，初始化完成之后返回EcuM模块。

### 2.去初始化

![](https://s2.loli.net/2022/10/27/HsG14OXzWmqkDty.png)

EcuM调用`Adc_DeInit`函数取消ADC模块的初始化，所有的notifications都被禁用，所有的中断也被禁用。

### 3.转换模式

ADC模块需要支持以下转换模式：

ADC模块需要为所有的ADC通道组，提供one-shot conversion模式的支持。one-shot conversion意味着为正在转换的组配置每一个通道都执行一次转换

![](https://s2.loli.net/2022/10/27/aBXZqYgONtmAj91.png)

ADC模块需为所有带有触发源软件的ADC通道组，提供连续转换模式（Continuous Conversion）的支持。连续转换是指一次ADC转换完成后，一直重复整个ADC通道组的转换。组内各个ADC通道的转换以及整个ADC通道组的重复不需要执行任何额外的触发事件。根据硬件和软件功能，可以顺序或并行转换ADC通道组内的各个通道。

![](https://s2.loli.net/2022/10/27/PiOw3EaI6zBF2Cr.png)

### 4.触发源

ADC模块需支持以下启动条件或触发源：

- ADC模块需支持所有转换模式的启动条件的软件API调用（Software API Call）。触发源的软件API调用是指ADC通道组的转换由ADC模块提供的服务来启动或者停止。
- ADC模块需支持在One-Shot转换模式中配置的ADC通道组的启动条件的硬件事件（Hardware Event）。触发源的硬件事件意味着ADC通道组的转换可以由硬件事件启动。（例如：定时器的超时事件，或者输入线上检测到一个边沿跳变）

![](https://s2.loli.net/2022/10/27/Vile41yIHKdnTxB.png)

ADC模块应允许为每个ADC通道组配置一个触发源。

### 5.数据访问模式

ADC模块需支持以下结果访问模式：

- ADC模块需支持使用API函数Adc_GetStreamLastPointer来访问结果。调用 Adc_GetStreamLastPointer会告知用户最新一轮的ADC通道组转换结果在结果缓冲区中的位置以及结果缓冲区中有效转换结果的数量。结果缓冲区是应用程序提供的外部缓冲区。此函数可用于ADC通道组的两种类型，通过配置Streaming Access Mode和Single Access Mode进行配置。同时Single Access Mode的处理等于Streaming Access Mode的Streaming Counter等于1的情况。
- 如果API函数的生成是静态配置的，ADC模块需支持使用API函数 Adc_ReadGroup来访问结果。调用Adc_ReadGroup将最近一轮转换的ADC通道组转换结果复制到应用程序缓冲区，其起始地址指定为Adc_ReadGroup的API参数。此函数可用于ADC通道组的两种类型，通过配置Streaming Access Mode和Single Access Mode进行配置。

![截图](1eeac824a481f510fd5771733c9a9d08.png)

ADC模块需保证每次完成转换的返回结果值的一致性。

**注意：**

ADC通道组结果的一致性在应用端可以通过以下方法获得：

- 使用ADC通道组通知机制（group notification mechanism）
- 通过API函数Adc_GetGroupStatus轮询获取。

在任何情况下，新的结果数据都必须在被覆盖之前从结果缓冲区中读出（例如：通过 `Adc_ReadGroup`）。如果函数`Adc_GetGroupStatus`报告状态**ADC_STREAM_COMPLETED**, 并且同一ADC通道组的转换仍在进行中（因连续转换或硬件触发转换激活），则上层用户需负责在ADC驱动程序覆盖结果缓冲区之前，读取ADC通道组结果缓冲区中的结果。

ADC模块的环境应确保在请求转换结果之前，以及完成被请求的ADC通道组的转换。如果被请求的ADC通道组没有完成转换（例如：因为用户停止了ADC通道组的转换），ADC模块返回的值将是任意的。（如：`Adc_GetStreamLastPointer`将返回0并读取值为**NULL_PTR**，而`Adc_ReadGroup`将返回**E_NOT_OK**）。

### 6.优先级

ADC模块需允许为每个ADC通道组配置优先级。这意味着优先级机制，在软件中实施，或者在可用的情况下由硬件支持。具有触发源硬件的ADC通道组，始终使用硬件优先级机制进行优先级排序。

ADC模块的优先级机制需允许中止和重新启动ADC通道组转换。ADC模块的优先级机制同时也应允许暂停和恢复ADC通道组转换。

ADC模块需允许ADC通道组特定配置，无论中止/重新启动或挂起/恢复机制是否用于中断的ADC通道组。与ADC通道组（ADC channel group）级别的软件控制的中止/重启或挂起/恢复机制相比，ADC硬件可以支持ADC通道（ADC channel）级别的中止/重启和挂起/恢复机制。这两种机制中的哪一个在通道级别上实现，取决于最终的代码实现。

ADC模块的优先级机制需允许对不同ADC通道组的请求进行排队。较高优先级的ADC通道组可以中止或挂起较低优先级的ADC通道组。在这种情况下，优先级处理程序应将中断的ADC通道组转换放入队列中，并且此ADC通道组的转换将在稍后重新启动或恢复。具体实现对用户来说是透明。

ADC模块的优先级机制需允许配置256个优先级（0…255）。最低优先级为0。

ADC模块需支持静态配置选项以禁用优先级机制。同时ADC模块需支持静态配置选项以启用ADC_PRIORITY_HW_SW优先级机制，即同时使用硬件和软件优先级机制。如果硬件不提供硬件优先级机制，则纯软件的优先级机制会被实施。

![](https://s2.loli.net/2022/10/27/SR94byn5HzUpD6Z.png)

如果硬件支持优先级机制，ADC模块应支持静态配置选项**ADC_PRIORITY_HW**启用仅使用硬件优先级机制的优先级机制。如果选择了硬件优先机制，则具有软件触发源的ADC通道组也会使用硬件优先级机制中获得优先权配置。

![](https://s2.loli.net/2022/10/27/GlTq2XQCvW6Fang.png)

如果支持并选择了硬件优先级机制，ADC模块应允许将配置的优先级（0-255）映射到可用的硬件优先级。ADC模块的具体实现有关可用硬件优先级的限制以及可用硬件优先级到ADC通道组优先级的可能映射。

如果优先机制处于活动状态，ADC模块应支持转换请求的队列的功能：

1. 当较低优先级的ADC通道组转换正在进行时，收到更高优先级的ADC通道组转换请求，较低优先级的ADC通道组需放入队列中等待。
2. 因为更高优先级的ADC通道组转换正在进行中，较低优先级的ADC通道组转换请求将不能被立即处理时，较低优先级的ADC通道组转换请求也需要放入队列中等待。

如果优先级机制处于活动状态，则ADC模块会按照先到先服务（first come first served）的顺序，处理具有相同优先级的ADC通道组转换请求。

如果优先级机制未激活，并且静态配置参数**AdcEnableQueuing**设置为**ON**，则 ADC模块会支持转换请求的队列功能，并应以**先到先服务**（first come first served）的顺序为软件组提供服务。软件转换请求存储的队列中需由软件实现或由硬件支持。

![](https://s2.loli.net/2022/10/27/H8K5xh6flPAEkBO.png)

如果队列机制激活（优先机制激活或队列显式激活），ADC模块需为每个ADC通道组的每个软件转换请求，最多在软件队列中存储一次。ADC模块应仅存储每个ADC通道组的一个转换请求，而不是多个请求。原因就是避免高优先级长期转换阻塞硬件的情况。

![截图](c643be2eac5fb70e7db009c2028165b5.png)

在启用硬件触发请求情况下，通过API函数`Adc_EnableHardwareTrigger`生成的请求不应存储在任何队列中。

在硬件触发转换请求的情况下，硬件优先级机制需被使用。

![](https://s2.loli.net/2022/10/27/xGrbRV3s7qe8H9z.png)

如果ADC通道组可以被隐式停止，当ADC通道组状态为ADC_IDLE或ADC_STREAM_COMPLETED，这时ADC模块则被允许存储同一ADC通道组的额外软件转换请求。

### 7.通知

如果通知功能被配置并启用，当被请求ADC通道组的所有通道的转换完成并且，ADC模块会调用此ADC通道组通知（group notification）函数。

### 8.重入和完整性

对于不同的ADC通道组，ADC模块函数是可以支持可重入的。此要求应适用于除了以下的所有API函数：

- `Adc_Init`
- `Adc_DeInit`
- `Adc_GetVersionInfo`
- `Adc_SetPowerState`
- `Adc_GetTargetPowerState`
- `Adc_GetCurrentPowerState`
- `Adc_PreparePowerState`

API函数的可重入性，必须由调用者来关注是否同时使用了同一个ADC通道组的情况。

简单的读取调用，如`Adc_ReadGroup`和`Adc_GetGroupStatus`的实现，即使这些函数是为相同的ADC通道组调用，也需支持是可重入的。是否使用适当的保护机制（例如：禁用和启用中断）取决于代码实现。

**注意：** 调用`Adc_ReadGroup`可以隐式更改组状态。

在运行时，如果在不同的**Task**或者**ISR**中，对于同一个ADC通道组的多次调用，则ADC模块无需检查数据的完整性，但ADC模块的用户需确保数据的完整性。

### 9.限制检查

ADC模块需允许为ADC通道配置限制检查。

如果ADC通道的限制检查（limit checking）处于被活动状态，则只有在配置范围内的ADC转换结果，才会被考虑用于更新用户指定的ADC结果缓冲区，并用于触发ADC通道组状态转换（state transitions）。

如果选择了带软件触发源的一次性（one-shot）转换模式，并且对ADC的通道启用 了限制检查，则 ADC驱动模块需忽略不在配置范围内的ADC转换结果，并且包含此ADC通道的ADC通道组，需仍旧保持**ADC_BUSY**的状态。

在重新发出新的一次性（one-shot）转换软件触发之前，必须使用`Adc_StopGroupConversion()`将ADC通道组状态设置为**ADC_IDLE**。

如果选择了带硬件触发源的一次性（one-shot）转换模式，并且对ADC的通道启用了限制检查，则ADC驱动模块需忽略不在配置范围内的ADC转换结果，并等待硬件触发源再次发出下一次的转换。

## 四、转换处理和交互

ADC模块一次只能转换每个ADC硬件单元（**ADC HW Unit**）的一个ADC通道组。ADC模块不支持在同一个ADC硬件单元上，不同的ADC通道组的同时转换。

**注意：**

根据硬件的能力，不同ADC硬件单元上的ADC通道组的并发转换是可能的。如果硬件支持，也可以同时转换一个通道组内的个别通道。

如果一个通道需要在不同的转换模式下使用（例如：正常操作期间的连续转换模式和在特定时间点进行特殊转换的一次性转换模式），则该通道应分配到具有各自的不同转换模式配置的ADC通道组上。

为了请求转换两个组之间共享的通道，ADC用户必须停止包含指定通道的第一组的转换，然后才能开始包含指定通道的第二组的转换。

![](https://s2.loli.net/2022/10/27/lMCu3iNDwsqPhTz.png)

1. 多通道/连续转换
   
   ADC通道组包含通道（CH0、CH1、CH2、CH3 和 CH4），并配置为连续转换模式。当完成每次扫描后，会调用通知（如果启用）。 然后自动开始新一轮扫描。
2. 多通道/one-shot
   
   ADC通道组包含通道（CH0、CH1、CH2、CH3 和 CH4），并配置为One-Shot转换模式。当完成扫描后，会调用通知（如果启用）。
3. 单通道/连续转换
   
   ADC通道组包含通道（CH3）并配置为连续转换模式。当完成每次扫描后，将调用通知（如果启用）。然后自动开始新一轮的扫描。
4. 单通道/one-shot
   
   ADC通道组包含通道（CH4）并配置为One-Shot转换模式。当完成扫描后，会调用通知（如果启用）。

## 五、ADC缓冲区访问模式

### 1.配置

![](https://s2.loli.net/2022/10/27/DQ7TyLwYgMqZtCS.png)

通道组G1有两个通道 **CH0**，**CH1** 结果缓冲区指针 **G1_ResultPtr** 数据访问模式是 **ADC_ACESS_MODE_STREAMING** ADC流采样配置为 3

通道组G2有一个通道 **CH2** 结果缓冲区指针 **G2_RESULT_POINTER** 数据访问模式是 **ADC_ACESS_MODE_STREAMING** ADC流采样配置为 2

通道组G3有一个通道 **CH3** 结果缓冲区指针 **G3_RESULT_POINTER** 数据访问模式是 **ADC_ACESS_MODE_SINGLE** ADC流采样配置为 2

### 2.初始化

![](https://s2.loli.net/2022/10/27/eUw5PmHKSBnfG7b.png)

用户必须为ADC通道组结果提供应用程序结果缓冲区。每组都需要一个缓冲区。如果选择流模式（**ADC_ACCESS_MODE_STREAMING**）的访问模式，缓冲区大小取决于组通道的数量、组访问模式以及流模式采样的数量。在开始ADC通道组转换之前，用户必须使用API函数 `Adc_SetupResultBuffer`初始化ADC通道组结果指针，该函数将ADC通道组结果指针初始化为指向特定的应用程序结果缓冲区。

### 3.Adc_GetStreamLastPointer用法

![](https://s2.loli.net/2022/10/27/PnAqMChI5N2UV4d.png)

ADC驱动程序将ADC通道组G1、G2和G3的转换结果存储在相应的结果缓冲区数组**G1_ResultBuffer**、**G2_ResultBuffer**和**G3_ResultBuffer**中。ADC驱动程序并不支持通过ADC的API函数直接访问ADC硬件结果寄存器。

ADC用户定义了三个指针**G1_SamplePtr**、**G2_SamplePtr**和**G3_SamplePtr**。在调用`Adc_GetStreamLastPointer`后，这些指针分别指向 ADC应用程序结果缓冲区。

准确地说，指针**G1_SamplePtr**在调用`Adc_GetStreamLastPointer`后，指向最新完成的一轮AD转换后的最新的G1_CH0的结果（其中G1_CH0是G1组定义中的第一个通道）。应用程序结果缓冲区布局如上图所示。组1的应用程序结果缓冲区中保存了3份G1_CH0的流结果（streaming result），也保存了3份G1_CH1 的流结果。了解应用程序结果缓冲区布局后，用户可以访问最新一轮转换的所有ADC通道组的组内通道的结果。

**G2_SamplePtr**和**G3_SamplePtr**也对齐，在调用`Adc_GetStreamLastPointer`后，指向对应ADC通道组的的第一个组通道的最新结果。因为这两个ADC通道组只有一个频道。**G2_SamplePtr**指向G2_CH2结果之一（即：最新结果）。ADC通道组，因为配置为单一访问模式，所以G3_SamplePtr始终指向G3_CH3。

`Adc_GetStreamLastPointer`会返回存储在应用程序结果缓冲区中，每个通道的有效采样数（即：完整的组转换次数）。如果返回值等于配置参数流采样个数（number of streaming samples），则流缓冲区中的所有转换结果都有效。如果返回值为 0，则流缓冲区中没有可用的转换结果（采样数据的指针将对齐为NULL）。

为了使`Adc_GetStreamLastPointer`将采样数据指针（**G1_SamplePtr**、**G2_SamplePtr**和**G3_SamplePtr**）指向最新的通道结果，API被定义为将指针传递给结果指针而不是结果指针本身。