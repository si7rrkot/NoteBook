在嵌入式领域当中，在配置内核的时候会有很多配置方法，比如说make config（基于文本的为传统的配置界面）、make oldconfig（如何只想在原来内核配置的基础上修改一部分，则会省去很多麻烦）、make xconfig（基于图形窗口模式的配置界面，Xwindow下推荐使用）、make menuconfig（基于文本选择的配置界面，字符终端下推荐使用），以上4种配置方式的目的都是在源码的顶层目录下面生成一个.config文件，那么在我们嵌入式开发当中，经常使用的和推荐使用的是make menuconfig这种配置方法。

| 字段 | 说明 |
| --- | --- |
| config | 此关键字定义了一新的配置选项 |
| menuconfig | 此关键字和前面的关键字很相似，但它在前面的基础上要求所有的子选项作为独立的行显示 |
| choice/endchoice | 该关键字定义了一组选择项 |
| comment | 这里定义了在配置过程中显示给用户的注释，该注释还将写进输出文件中。格式说明： comment "eg: description content" |
| menu / endmenu | 这里定义了一个菜单，所有依赖于此菜单的选项都是它的子选项 |
| if/endif | 定义了if结构 |
| source | 读取其他具体的配置文件，其他配置文件会被解析 |

```ruby
config MQTT_COMM_SUPPORT
          bool "CONFIG_MQTT_COMM_SUPPORT"
          default n
          help
               mqtt communication with star_link server.

menu "SL_CONFIG_MQTT "
         depends on MQTT_COMM_SUPPORT

         config MQTT_SDK_IMPL
                bool "CONFIG_MQTT_SDK_IMPL_SUPPORT"
                default y
                help
                     mqtt impl sdk/device

endmenu

config :定义了配置项名字

menu ：定义了菜单名字，必须以endmenu结束，menu后跟的名字会显示在菜单中。

             在其控制范围内再添加config 或者menu

bool ：描述了配置项显示在菜单中的名字

default ：y 选中，菜单中有* 表示，n 未选中，空白表示

help： 帮助，描述文档

depends on：依赖，如上菜单SL_CONFIG_MQTT  依赖于config MQTT_COMM_SUPPORT，选中MQTT_COMM_SUPPORT才会出现SL_CONFIG_MQTT菜单
```
## 简介

- make menuconfig ：生成mconf 应用，产生图形界面，并且保存.config
- make xxx_defconf：生成conf 应用，解析xxx_defconf，并保存成.config

**本质上.config 和 xxx_defconfig 是同一文件**。
**xxx_defconfig**就是保存的**.config**文件，我们可以通过`make** **xxx_defconf`来快速生成我们需要的配置，减少繁琐的选项配置
在编译内核的过程中会将 .config 转换为相应的宏，包含在头文件`autoconf.h `中，这样就可以供内核使用了。
## 常用语法
在Kconfig文件中，假设配置项的名字是XXX，在.config文件中

- 默认情况下，它对应的变量名为CONFIG_XXX
- 如果设置了环境变量CONFIG_=ABC，则对应的变量名为ABC_XXX
```ruby
config xxx
	bool "xxx"
```
则在`.config`中表示为`CONFIG_XXX`
### 单个配置选项 config
如图所示，下面是一个配置MQTT的config
![image.png](/figure/image.png)
#### 🪭语法
config 配置基础语法如下：
```ruby
config option
    type "xxx"         //简单描述
    depends on xxx 	   //依赖选项，可选
    default xxx        //初始值
    help               //帮助信息
        xxxxxxxxxxxxxxx
```

- config option 是一个配置的开始，紧跟着的是配置选项的名称。
- config 下定义了以下属性：
   - 类型、输入提示、依赖关系、默认值、帮助信息。
   - 每个表示配置的菜单都有类型。变量共有五种类型。
      - bool：布尔类型
      - tristate：三态类型
      - string：字符串
      - hex：十六进制
      - int：整型
#### 🎶示例
```ruby
config TEST_A
    bool "config test A"
    default y
    help
        test config test A, yes/no
 
 config TEST_B
    bool "config test B"
    depends on TEST_A
    default y
    help
        test config test B, yes/no
```


TEST_B 依赖 TEST_A，只有A选中的时候才会出现TEST_B的选项配置
### 菜单 menu


menu 语法以 menu 开始，endmenu 结束。中间包含若干项config配置, 当然也可以包含 其他语法。
#### 🪭语法
```ruby
menu "menu"
config AAA
        bool "AAA"

config BBB
        bool "BBB" 
endmenu
```
菜单以`menu`开始，以`endmenu`结束，中间可以包含`config`和`choice`，当然也能包含二级菜单
#### 🎶示例
```ruby
menu "test menu"
config TEST_MENU_A
  bool "TEST_MENU_A"

config TEST_MENU_B
	bool "menu test B"
	default n                           
endmenu
```
**二级菜单示例**
```ruby
menu "1"			# 一级菜单

config AAA
	bool "AAA"
	default n
config BBB
	bool "BBB"
	default y

menu "2"			# 二级菜单
config CCC
	bool "CCC"
	default n
config DDD
	bool "DDD"
	default y
endmenu

endmenu
```
### **单选 choice/endchoice**
从多个选项中选择其中一个
#### 🪭语法
```ruby
choice
config USB_G_HID
        tristate "HID Gadget"
        select USB_LIBCOMPOSITE
        help
        ¦ The HID gadget driver provides generic emulation of USB
        ¦ Human Inter
# put drivers that need isochronous transfer support (for audio
# or video class gadget drivers), or specific hardware, here.
......
config USB_G_WEBCAM
        tristate "USB Webcam Gadget"
        depends on VIDEO_DEV
        select USB_LIBCOMPOSITE
        select VIDEOBUF2_VMALLOC
        help
        ¦ The Webcam Gadget acts as a composite USB Audio and Video Class
        ¦ device. It provides a userspace API to process UVC control requests
        ¦ and stream video data to the host.

        ¦ Say "y" to link the driver statically, or "m" to build a
        ¦ dynamically linked module called "g_webcam".
endchoice
```

 

- choice 开始，endchoice 结束。
- choice 中间可以加入其他配置，以及 choice 嵌套
- 他们之间只能有一个被设置为 y：表示编译进内核
#### 🎶示例
```ruby
choice
    prompt "The maximal size of fifo"
    default USB_FIFO_512
    
    config USB_FIFO_512
        bool "512"
        
    config USB_FIFO_1024
        bool "1024"
        
    config USB_FIFO_3072
        bool "3072"

endchoice
```
### 带菜单的配置项 **menuconfig**
menuconfig XXX和config XXX类似， 唯一不同的是该选项除了能设置y/m/n外，还可以实现菜单效果(能回车进入该项内部)。
#### 🪭语法
menuconfig常用格式有2种：
```ruby
  menuconfig M
  if M
      config C1
      config C2
  endif
    
```
或者
```ruby
  menuconfig M
  config C1
      depends on M
  config C2
      depends on M
```
第1项menuconfig M跟config M语法是一样的， 不同之处在于menuocnfig M后面可以跟着好几个依赖于M的config C1、config C2等子配置项.
#### 🎶示例
```ruby
 menuconfig M
  config C1
      depends on M
  config C2
      depends on M
```
### 条件语句 **if/endif**
用于条件判断
#### 🪭语法
```ruby
if xxx
endif
```
#### 🎶示例
```ruby
if USB_GADGET

config USB_GADGET_DEBUG
        boolean "Debugging messages (DEVELOPMENT)"
        depends on DEBUG_KERNEL
        help 
        ¦  Many controller and gadget drivers will print some debugging
        ¦  messages if you use this option to ask for those messages.

        ¦  A
 ......
 endif
```
只有定义了 USB_GADGET ，以下部分才会显示出来。
### 文件导入 **source**
#### 🪭语法
source 语句用于读取另一个文件中的 Kconfig 文件， 比如driver/usb/Kconfig 中就包含了其他Kconfig：
```ruby
source "drivers/usb/gadget/Kconfig"
```
### **注释 comment**
#### 🪭语法
comment 语句出现在界面的第一行，用于定义一些提示信息。
这里定义了在配置过程中显示给用户的注释，该注释还将写进输出文件中。格式说明：
```ruby
 comment "eg: description content"
```
## 完整的实例
以下是正点原子STM32F429阿波罗板对应的board目录下kconfig文件，可以参考学习！
```ruby
menu "Hardware Drivers Config"      #定义1级菜单

config SOC_STM32F429IG              #选择MCU
    bool                            #bool数据类型后面为空，表示配置界面不会显示
    select SOC_SERIES_STM32F4
    default y

menu "Onboard Peripheral Drivers"   #定义2级菜单

    config BSP_USING_USB_TO_USART   #2级菜单中的一行新的配置项，BSP_USING_USB_TO_USART只是标识
        bool "Enable USB TO USART (uart1)"   #配置项显示的内容，配置类型为bool
        select BSP_USING_UART       #
        select BSP_USING_UART1      #如果使能该配置项，select表示会在rtconfig.h文件增加宏定义
        default y                   #使能配置项，决定上面宏定义是否增加

    config BSP_USING_COM2
        bool "Enable COM2 (uart2 pin conflict with Ethernet and PWM)"
        select BSP_USING_UART
        select BSP_USING_UART2
        default n

    config BSP_USING_COM3
        bool "Enable COM3 (uart3 pin conflict with Ethernet)"
        select BSP_USING_UART3
        default n

    config BSP_USING_SDRAM
        bool "Enable SDRAM"
        select BSP_USING_FMC
        default n

    config BSP_USING_SPI_FLASH
        bool "Enable SPI FLASH (W25Q256 spi5)"
        select BSP_USING_SPI
        select BSP_USING_SPI5
        select RT_USING_SFUD
        select RT_SFUD_USING_SFDP
        default n

    config BSP_USING_MPU9250
        bool "Enable MPU 9250 (i2c1)"
        select BSP_USING_I2C1
        select PKG_USING_MPU6XXX
        default n

    config PHY_USING_LAN8720A
        bool

    config BSP_USING_ETH
        bool "Enable Ethernet"
        select BSP_USING_I2C1
        select PKG_USING_PCF8574
        select RT_USING_LWIP
        select PHY_USING_LAN8720A
        default n

    config BSP_USING_SDCARD
        bool "Enable SDCARD (sdio)"
        select BSP_USING_SDIO
        select RT_USING_DFS
        select RT_USING_DFS_ELMFAT
        default n

endmenu                             # 2级菜单的结束

menu "On-chip Peripheral Drivers"   # 新的2级菜单

    config BSP_USING_GPIO
        bool "Enable GPIO"
        select RT_USING_PIN
        default y

    menuconfig BSP_USING_UART       # 带菜单的配置项，配置后可以进入该菜单继续配置
        bool "Enable UART"
        default y                   # 没有select 选项，说明该菜单配置项强制选中
        select RT_USING_SERIAL
        if BSP_USING_UART           # 带菜单的配置项如果被配置，则if生效
            config BSP_USING_UART1
                bool "Enable UART1"
                default y

            config BSP_UART1_RX_USING_DMA
                bool "Enable UART1 RX DMA"
                depends on BSP_USING_UART1 && RT_SERIAL_USING_DMA  #依赖两个宏定义
                default n

            config BSP_USING_UART2
                bool "Enable UART2"
                default n

            config BSP_UART2_RX_USING_DMA
                bool "Enable UART2 RX DMA"
                depends on BSP_USING_UART2 && RT_SERIAL_USING_DMA
                default n
                
            config BSP_USING_UART3
                bool "Enable UART3"
                default n
   
            config BSP_UART3_RX_USING_DMA
                bool "Enable UART3 RX DMA"
                depends on BSP_USING_UART3 && RT_SERIAL_USING_DMA
                default n  
        endif

    config BSP_USING_ON_CHIP_FLASH
        bool "Enable on-chip FLASH"
        default n

    menuconfig BSP_USING_SPI
        bool "Enable SPI BUS"
        default n
        select RT_USING_SPI
        if BSP_USING_SPI
            config BSP_USING_SPI1
                bool "Enable SPI1 BUS"
                default n

            config BSP_SPI1_TX_USING_DMA
                bool "Enable SPI1 TX DMA"
                depends on BSP_USING_SPI1
                default n
                
            config BSP_SPI1_RX_USING_DMA
                bool "Enable SPI1 RX DMA"
                depends on BSP_USING_SPI1
                select BSP_SPI1_TX_USING_DMA
                default n

            config BSP_USING_SPI2
                bool "Enable SPI2 BUS"
                default n  
                
            config BSP_SPI2_TX_USING_DMA
                bool "Enable SPI2 TX DMA"
                depends on BSP_USING_SPI2
                default n
                
            config BSP_SPI2_RX_USING_DMA
                bool "Enable SPI2 RX DMA"
                depends on BSP_USING_SPI2
                select BSP_SPI2_TX_USING_DMA
                default n
                
            config BSP_USING_SPI5
                bool "Enable SPI5 BUS"
                default n        

            config BSP_SPI5_TX_USING_DMA
                bool "Enable SPI5 TX DMA"
                depends on BSP_USING_SPI5
                default n
                
            config BSP_SPI5_RX_USING_DMA
                bool "Enable SPI5 RX DMA"
                depends on BSP_USING_SPI5
                select BSP_SPI5_TX_USING_DMA
                default n  
        endif

    menuconfig BSP_USING_I2C1
        bool "Enable I2C1 BUS (software simulation)"
        default n
        select RT_USING_I2C
        select RT_USING_I2C_BITOPS
        select RT_USING_PIN
        if BSP_USING_I2C1
            comment "Notice: PH4 --> 116; PH5 --> 117"   #配置项的注释说明
            config BSP_I2C1_SCL_PIN
                int "I2C1 scl pin number"                #配置项类型为整型
                range 1 176                              #配置项的值取值范围设定
                default 116
            config BSP_I2C1_SDA_PIN
                int "I2C1 sda pin number"
                range 1 176
                default 117
        endif

    menuconfig BSP_USING_TIM
        bool "Enable timer"
        default n
        select RT_USING_HWTIMER
        if BSP_USING_TIM
            config BSP_USING_TIM11
                bool "Enable TIM11"
                default n

            config BSP_USING_TIM13
                bool "Enable TIM13"
                default n

            config BSP_USING_TIM14
                bool "Enable TIM14"
                default n
        endif

    menuconfig BSP_USING_PWM
        bool "Enable pwm"
        default n
        select RT_USING_PWM
        if BSP_USING_PWM
        menuconfig BSP_USING_PWM2
            bool "Enable timer2 output pwm"
            default n
            if BSP_USING_PWM2
                config BSP_USING_PWM2_CH4
                    bool "Enable PWM2 channel4"
                    default n
            endif
        endif

    menuconfig BSP_USING_ADC
        bool "Enable ADC"
        default n
        select RT_USING_ADC
        if BSP_USING_ADC
            config BSP_USING_ADC1
                bool "Enable ADC1"
                default n
        endif

    menuconfig BSP_USING_ONCHIP_RTC
        bool "Enable RTC"
        select RT_USING_RTC
        select RT_USING_LIBC
        default n
        if BSP_USING_ONCHIP_RTC               #条件判断
            choice                            #选择配置项，其值可选，类似单选框功能
                prompt "Select clock source"  #配置项的显示名称
                default BSP_RTC_USING_LSE     #默认选择的配置

                config BSP_RTC_USING_LSE      #配置选项
                    bool "RTC USING LSE"

                config BSP_RTC_USING_LSI
                    bool "RTC USING LSI"
            endchoice                         #结束选择配置项
        endif

    config BSP_USING_WDT
        bool "Enable Watchdog Timer"
        select RT_USING_WDT
        default n

    config BSP_USING_SDIO
        bool "Enable SDIO"
        select RT_USING_SDIO
        select RT_USING_DFS
        default n

    config BSP_USING_FMC
        bool
        default n
endmenu

menu "Board extended module Drivers"

endmenu

endmenu
```
