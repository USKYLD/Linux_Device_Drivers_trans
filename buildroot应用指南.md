## 简介
buildroot可以简单理解为一个构建一个嵌入式设备从上到下运行环境的一个包
如bootloader,uboot,kernel都包含在里面
## 下载链接
[下载链接](https://buildroot.org/downloads/)  选择一个版本进行下载

## 目录结构
**arch/** 目录存放CPU架构相关的配置脚本，如arm/mips/x86 ，这些CPU相关的配置，在制作工具链，编译boot和内核时很关键。

**board/** 目录存放各种board 特有的一些配置脚本，在构建系统时，board默认的boot和Linux kernel配置文件，以及一些板相关特殊构建流程的脚本，都在该目录下，等待自动构建时调用。

**boot/** 目录存在各种boot软件的自动构建脚本，不只是u-boot，还有grub等，也可以通过Buildroot来构建。

**configs/** 目录存放这每种方案上层的全局配置文件，之前的make mini2440_defconfig实际上就是调用了该目录下的mini2440方案的配置。该目录下的配置文件记录着该机器平台或者方案使用的工具栏，boot， kernel，各种应用软件包的配置和是否编译选择的状态，之前所说的某个特殊开发板整个系统的配置文件，就在configs/目录下。

**dl/** 目录存在从官网上下载的开源软件包，第一次下载后，下次就不会再去从官网下载了，而是从dl/目录下拿开源包，以节约时间。

**docs/** 存放相关的参考文档。

**fs/** 存放着各种文件系统的自动构建脚本。

**linux/** 存放着Linux kernel的自动构建脚本。

**output/** 是编译出来的输出文件夹，里面的build/目录存放着解压后的各种软件包编译完后的现场。host/目录放着制作好的编译工具链，target/ 目录是用来制作rootfs的，里面放着Linux系统基本的目录结构，以及各种编译好的应用库和bin可执行文件。Images/目录下就是最终生成的可烧写到板子上的各种image。

**package/** 目录存放着各种第三方开源应用软件包的自动编译构建脚本，这些构建脚本一般都是经过测试，能够构建出相应的软件包的。

**support/** 目录存放着一些固定的流程脚本，以备构建时调用执行。

**system/** 目录存放着文件系统目录的和设备节点的模板，这些模板会被拷贝到 output/ 目录下，用于制作根文件系统rootfs。

**toolchain/** 目录中存放着各种制作工具链的脚本，buildroot可以选择从0开始，用gcc和linux 内核，glibc、uclibc库等原材料制作一个自己工具链，也可以下载第三方制作好的开源工具。
## 安装过程
`makefile`最重要，`Config.in`文件是一个makefile文件的配置输入，该文件由makefile读取解析，`README`文件中描述了buildroot的使用方法

```shell
cd /root/buildroot-2023.11/
#修改.config:
BR2_BACKUP_SITE="http://sources.buildroot.net"
BR2_KERNEL_MIRROR="https://mirrors.aliyun.com/linux-kernel"
BR2_GNU_MIRROR="https://mirrors.aliyun.com/gnu"
BR2_LUAROCKS_MIRROR="https://luarocks.cn"
BR2_CPAN_MIRROR="https://mirrors.aliyun.com/CPAN" 
apt-get install cpio bc rsync libncurses5-dev#根据需要安装,可以先make看一下缺少什么再安装什么
make qemu_riscv64_virt_defconfig#将配置文件写入,这里选择需要的
make -j20#根据自己的需要选择

```
## 安装后介绍
在output目录下
build目录是依赖文件
host目录是本地编译器,放入文件系统后课直接编译而不是交叉编译
target是根文件系统
images:
qemu-system-riscv64需要把之前生成的这个文件拷贝过来
rootfs.ext2是虚拟的磁盘文件,里面装的根文件系统
## 文件挂载
```shell
mkdir mnt
mount rootfs.ext2 mnt#挂载
cd mnt
ls
umount mnt#卸载
```
就可以看到这个磁盘里的东西
rootfs.tar是这个磁盘文件的压缩包
## qemu运行脚本
start_qemu.sh运行脚本
```shell
cp ./output/build/linux-6.1.44/vmlinux ./output/images/

```

启动:
```shell
./start_qemu.sh
```

[【搞linux的旺仔】 buildroot怎么使用？利用buildroot编译qemu的运行软件环境，qemu启动linux + 根文件系统，欢迎点赞关注!_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1Gh411F7DN/?spm_id_from=..search-card.all.click&vd_source=d378838d46c7eef0b16f97a12c4502b7)
