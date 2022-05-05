#QEMU

## **搭建环境**

###安装交叉编译工具链

  ```shell
  sudo  apt-get  install gcc-arm-linux-gnueabi  
  ```



查看编译工具链版本号

  ```shell
  dpkg  -l gcc-arm-linux-gnueabi  
  ```

结果如下

  ```shell
  Desired=Unknown/Install/Remove/Purge/Hold
  | Status=Not/Inst/Conf-files/Unpacked/halF-conf/Half-inst/trig-aWai>
  |/ Err?=(none)/Reinst-required (Status,Err: uppercase=bad)
  ||/ Name                  Version          Architecture Description
  +++-=====================-================-============-===========>
  ii  gcc-arm-linux-gnueabi 4:9.3.0-1ubuntu2 amd64        GNU C compi>
  ```

 

###下载qemu模拟器源码

  ```shell
  wget https://download.qemu.org/qemu-7.0.0-rc2.tar.xz  
  ```

解压

  ```shell
  tar  xvJf qemu-7.0.0-rc2.tar.xz 
  ```

遇到问题,报

 ```shell
  Cannot find Ninja   #执行 apt-get install ninja-build  
 ```



 ```shell
  Dependency  “pixman-1”  not found, tried pkgconfig  #执行 apt-get install libpixman-1-dev
 ```

  

###执行命令进行qemu模拟

    qemu-system-arm -M vexpress-a9 -m 512M -kernel ./zImage -dtb  vexpress-v2p-ca9.dtb -nographic -append "root=/dev/mmcblk0 rw console=ttyAMA0" -sd rootfs.ext4  

```shell
qemu-system-arm -M vexpress-a9 -kernel u-boot -m 512M -nographic 
```



查看qemu支持的cpu型号

   ```shell
   qemu-system-arm -cpu help  
   ```

查看qemu支持的设备

  ```shell
  qemu-system-arm -device help
  ```

查看qemu支持的板子

```shell
qemu-system-arm -M help
```

### 在qemu环境下启动vexpress-a9的rt-thread工程

获取rt-thread源码

```shell
git clone https://gitee.com/rtthread/rt-thread.git
```

打开bsp文件夹下面的`vexpress-a9`

安装`scons`

```shell
sudo apt install scons
```

编译,在`vexpress-a9`文件夹下执行

```shell
scons
```

编译完成生成`rtthread.elf`,然后再在`qemu-vexpress-a9`文件夹下执行

```shell
./qemu-nographic.sh
```

驱动起来的设备

```shell
msh />list_device
device           type         ref count
-------- -------------------- ----------
e0       Network Interface    0       
sd0      Block Device         1       
rtc      RTC                  0       
uart1    Character Device     0       
uart0    Character Device     2  
```

