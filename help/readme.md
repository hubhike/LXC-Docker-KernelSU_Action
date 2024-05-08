# 怎么找到设备内核的config文件

## 怎么确定KERNEL_IMAGE_NAME的取值

一般来说，内核配置文件（defconfig）内核名（kernel- name如：Image、Image.gz等）都可以在内核所对应的系统项目的源码中找到，即：

KERNEL_IMAGE_NAME可能的取值有Image.gz-dtb,Image.gz,Image等

> KERNEL_IMAGE_NAME=Image.gz-dtb

### 在GitHub搜索 device_xiaomi_wayne    （即 device_品牌_代号）

如：device_xiaomi_wayne

![](https://github.com/hubhike/LXC-Docker-KernelSU_Action/raw/main/help/01.jpg)

### 它的文件为：https://github.com/LineageOS-wayne/android_device_xiaomi_wayne/blob/lineage-20.0/BoardConfig.mk

在Board config文件中可找到kernel name

![](https://github.com/hubhike/LXC-Docker-KernelSU_Action/raw/main/help/pic/02.jpg)


## 对应的，deconfig亦可，只要找仔细点就行

实在找不到的，也可以去Android.mk及源码中的其他文件耐心寻找

不要问我xx系统没源码怎么办，那我也帮不了你。

当然，如果你已root，查看系统的/proc下的config.gz文件，在哪里你还可以查看到工具链（toolchain）的选择（工具链错选，这也是很多内核编译不成功的原因）



怎么确定内核config文件 - 哔哩哔哩

https://www.bilibili.com/read/cv32443301/?jump_opus=1

使用kernelSU action-小白纯经验之谈 - 哔哩哔哩

https://www.bilibili.com/read/cv29444447/?jump_opus=1
