# ax7z100_pynq

## 概述

该仓库用于编译alinx 7100开发板PYNQ2.4 SD卡启动镜像的image.ub文件<br>
其中，BOOT.bin文件需要使用Alinx官方提供的ZYNQ镜像的BOOT.bin文件，位于zynq_boot文件夹下<br>
Alinx官方提供的ZYNQ镜像下载路径位于：SD卡恢复出厂状态.pdf<br>
文件系统使用pynq官方2.4系统，下载PYNQ_Z1_pynq_2.4.img，相关链接如下https://github.com/Xilinx/PYNQ/releases <br>
先将PYNQ_Z1_pynq_2.4.img烧录到SD卡，然后分别替换BOOT.bin文件和image.ub文件即可

## 文件介绍

project_1中是编译PYNQ所需的7100工程，用于制作base.bit和system.hdf，除芯片选择xc7z100ffg900-2外，制作配置过程同：启明星Zynq7010制作PYNQ V2.4镜像过程记录_正点原子 pynq镜像制作-CSDN博客.pdf

PYNQ-2.4.tar.gz是官方的pynq2.4仓库

如需自己编译，环境配置参考：制作ZCU102的PYNQ镜像 - 知乎.pdf。注意，该文章编译PYNQ3.0，2.4所需的软件版本均为2018.3

参考最后output章节，按需修改pynq配置

之后，将7100文件夹复制到boards文件夹下，在sdbulid文件夹下输入make boot_files BOARDS=7100即可开始编译

## output

output中为<br>
CONFIG_CMA_SIZE_MBYTES=768<br>
CONFIG_VMSPLIT_1G=y<br>
的编译结果<br>

配置文件位于PYNQ-2.4/sdbuild/boot/meta-pynq/recipes-kernel/linux/linux-xlnx/pynq.cfg
其中，官方默认配置为CONFIG_CMA_SIZE_MBYTES=128并且没有CONFIG_VMSPLIT_1G。

以下是不同配置下，编译得出的image.ub在实际开发板上的测试情况

CONFIG_CMA_SIZE_MBYTES=512：无法连接到jupter，怀疑是CMA_SIZE过大导致网卡驱动被覆盖

CONFIG_CMA_SIZE_MBYTES=1024，CONFIG_VMSPLIT_1G=y：xlnk size为0，jupter正常，怀疑是CMA_SIZE超出地址空间

CONFIG_CMA_SIZE_MBYTES=768，CONFIG_VMSPLIT_1G=y：xlnk size为196540，jupter正常，猜测成功原因：在适当增加CMA_SIZE的基础上，修改内核/用户为3G/1G，从而保证网卡驱动正常工作
