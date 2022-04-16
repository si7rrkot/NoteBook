# QEMU



## 搭建环境

![img](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAAAXNSR0IArs4c6QAAAA1JREFUGFdjaGhoSAMABOsB5/dzA/0AAAAASUVORK5CYII=)

### 安装交叉编译工具链

  ```shell
  sudo  apt-get  install gcc-arm-linux-gnueabi  
  ```

查看编译工具链版本号

  ```shell
  dpkg  -l gcc-arm-linux-gnueabi  
  ```

结果如下

 ```
 Desired=Unknown/Install/Remove/Purge/Hold
 | Status=Not/Inst/Conf-files/Unpacked/halF-conf/Half-inst/trig-aWai>
 |/ Err?=(none)/Reinst-required (Status,Err: uppercase=bad)
 ||/ Name                  Version          Architecture Description
 +++-=====================-================-============-===========>
 ii  gcc-arm-linux-gnueabi 4:9.3.0-1ubuntu2 amd64        GNU C compi>
 ```



下载qemu模拟器源码

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

 

### 运行模拟

```shell
qemu-system-arm -M vexpress-a9 -m 512M -kernel ./zImage -dtb  vexpress-v2p-ca9.dtb -nographic -append "root=/dev/mmcblk0 rw console=ttyAMA0" -sd rootfs.ext4 
```

 

查看qemu支持的cpu型号

   ```shell
   qemu-system-arm -cpu help  
   ```



查看qemu支持的设备

  ```shell
  qemu-system-x86_64 -device help   
  ```