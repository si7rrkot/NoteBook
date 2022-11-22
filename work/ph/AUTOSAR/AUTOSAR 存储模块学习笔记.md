# AUTOSAR存储模块学习笔记

## 存储基础知识

FEE（Flash EEPROM Emulation），即Flash模拟EEPROM。

### EEPROM和FLASH的区别

flash和EEPROM都属于是Non-volatile memory，非易失性存储器，就是断电后可以保存数据的存储器，但是EEPROM和FLASH的区别：

1. 擦写方式不同
   
   **Flash**：分为若干个Page，擦除的时候会整片擦除，写的时候必须该位为1才可以写进去，所以说，每次在Flash里修改数据的时候，要先擦除再写入。
   
   **EEPROM**：写之前可以不需要擦除直接修改，可以修改单字节，读写速度较慢一点。
2. 擦写次数
   
   FLASH比EEPROM少
3. 面向需求不同
   
   **Flash**：一般存储一些不容易改变的数据，如车辆的一些出厂数据（VIN码）等，因为这些数据不需要时常改变，所以存在Flash里就可以。
   
   **EEPROM**：一般存储一些容易改变的数据，如故障诊断中的DTC，如果将其存在Flash里面就会不停地去擦写Flash，并且擦除面积较大，不利于操作。
   
   **FEE**: 在MCU 中有一块非易失性存储器我们称之为Data Flash , 在AUTOSAR FEE/FLS 规范中，通过DATA FLASH 模拟EEP的形式将数据存储在Data Flash中。

### FLASH的基本概念

页（page）、扇区（sector）、块（block）

![](https://s2.loli.net/2022/10/19/L8qZnOxIirvBmd5.png)

**page：**由若干个字节所组成，是最小的读写单元，如果Page：8 bytes，也就意味着即使向DATA Flash写入一个字节，也会占用Page这么大的空间

**Logical  Sector：**逻辑扇区，擦除的最小单元，其由若干Page所构成，比如 Logical Section :2K 意味着擦除的最小单元式2K Bytes

**Physical Sectors：**物理扇区由多个逻辑扇区所构成 比如 Physical Section :20K，那么其由10个逻辑扇区所构成，一个Physical Sector包含多个Logical Sector。

**Block：**比扇区更高一个等级，一般1块包含多个扇区和扇区一个级别的SubSector，和块一个级别的Bank、Bulk等。
块（bank） > 扇区（sector） > 页（page）

<br/>

## AUTOSAR中的存储模块

AutoSAR中对NVM数据的存储主要包括片内存储和外部存储两种方式：
片内存储，是用芯片内部的DFLASH进行数据存储；
外部存储，是通过外部EEPROM进行存储，一般会调用SPI、IIC等通信方式外挂EEPROM芯片。

![](https://s2.loli.net/2022/10/19/lEQSY1RtIzeMujD.png)

AUTOSAR分层架构中的存储器堆栈由以下模块组成,它为上层应用层和基础软件提供基本的内存管理服务。

- 非易失性存储器管理器 - NVM（服务层）
- 存储器接口 - MemIf（ECU抽象层）
- Flash EEPROM模拟 - FEE（ECU抽象层）
- EEPROM抽象 - EA （ECU 抽象层）
- Flash 驱动程序 - Fls（MCAL 层）
- EEPROM 驱动程序 - Eep（MCAL 层）

**NVM**主要提供抽象数据存储，在上电读取下电存储数据，支持Immediately存储数据，将NV data在ROM和RAM之间建立关联；

**MemIf**实现存储数据Block在内部Flash或者外部EEP的分离操作；

**FEE**实现对Flash的数据Block的抽象和动态数据的Layout；

**Flash Drive**r提供操作Flash的接口；

**EA**定义外部EEPROM的存储抽象和数据的Layout；

**EEP**提供外部EEPROM的操作接口和驱动。

<br/>

### Fee模块

![](https://s2.loli.net/2022/10/19/cSsfPQyMaRnXAm9.png)

**模拟EEP写入方式**

Fee 通过 Fls 模块操作 DataFlash， DataFlash 具有如下物理特性：

按页写入，写数据需为物理页的整数倍， 否则需做字节补齐。

写入前需确保此段空间未被写入过数据， 否则需要擦除后再写入数据。最小擦除单位为整个物理扇区。

Fee 的写入单元是Block,Fee Block 是继承自NvM Block，在NvM Block 数据的基础上加上Fee Block Header数据，形成Fee Block。

Fee 写入Block 按照先写入，地址就靠前的原则，下次写入Block 就依次往后排。这样确保每次写入都不需要擦除