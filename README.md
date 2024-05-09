本分支 forked from [tomxi1997/LXC_KernelSU_Action](https://github.com/tomxi1997/LXC_KernelSU_Action)  ，tomxi1997二开优化： [xiaoleGun/KernelSU_Action](https://github.com/xiaoleGun/KernelSU_Action)

### 工具链版本运行状态：https://clangbuiltlinux.github.io/

# LXC-Docker-KernelSU_Action

用于 Non-GKI Kernel 的 Action，具有一定的普遍性，需要了解内核及 Android 的相关知识得以运用。

## 警告:warning: :warning: :warning:

如果你不是内核作者，使用他人的劳动成果构建 KernelSU，请仅供自己使用，不要分享给别人，这是对原作者的劳动成果的尊重。

## 支持内核

- `5.4`
- `4.19`
- `4.14`
- `4.9`

# 使用方法：

## 1、修改config.env 文件

###（一般如果只需要lxc,修改第12～30行内容即可）提交，run。

> 所有 config.env 内的变量均只判断`true`

> 编译成功后，会在`Action`上传 AnyKernel3，已经关闭设备检查，请在 Twrp 刷入。

Fork 本仓库到你的储存库然后按照以下内容编辑 config.env，之后点击`Star`或`Action`，在左侧可看见`Build Kernel`选项，点击选项会看见右边的大对话框的上面会有`Run workflows`点击它会启动构建。

### Kernel Source

修改为你的内核仓库地址

例如: https://github.com/Diva-Room/Miku_kernel_xiaomi_wayne

### Kernel Source Branch

修改为你的内核分支

例如: TDA

### Kernel Config

修改为你的内核配置文件名

例如: vendor/wayne_defconfig

### Arch

例如: arm64

### Kernel Image Name

修改为需要刷写的 kernel binary，一般与你的 aosp-device tree 里的 BOARD_KERNEL_IMAGE_NAME 是一致的

例如: Image.gz-dtb

常见还有 Image、Image.gz

### Clang

#### Use custom clang

可以使用除 google 官方的 clang，如[proton-clang](https://github.com/kdrag0n/proton-clang)

#### Custom Clang Source

> 如果是 git 仓库，请填写包含`.git`的链接

支持 git 仓库或者 zip 压缩包的直链

#### Custom cmds

都用自定义 clang 了，自己改改这些配置应该都会吧 :)

#### Clang Branch

由于 [#23](https://github.com/xiaoleGun/KernelSU_Action/issues/23) 的需要，我们提供可自定义 Google 上游分支的选项，主要的有分支有
| Clang 分支 |
| ---------- |
| master |
| master-kernel-build-2021 |
| master-kernel-build-2022 |

或者其它分支，请根据自己的需求在 https://android.googlesource.com/platform/prebuilts/clang/host/linux-x86 中寻找

#### Clang version

填写需要使用的 Clang 版本
| Clang 版本 | 对应 Android 版本 | AOSP-Clang 版本 |
| ---------- | ----------------- | --------------- |
| 12.0.5 | Android S | r416183b |
| 14.0.6 | Android T | r450784d |
| 14.0.7 | | r450784e |
| 15.0.1 | | r458507 |

一般 Clang12 就能通过大部分 4.14 及以上的内核的编译
我自己的 MI 6X 4.19 使用的是 r450784d

### GCC

#### Enable GCC 64

启用 GCC 64 交叉编译

#### Enable GCC 32

启用 GCC 32 交叉编译

### Extra cmds

有的内核需要加入一些其它编译命令，才能正常编译，一般不需要其它的命令，请自行搜索自己内核的资料
请在命令与命令之间用空格隔开

例如: LLVM=1 LLVM_IAS=1

### Enable KernelSU

启用 KernelSU，用于排查内核故障或单独编译内核

#### KernelSU Branch or Tag

选择 KernelSU 的分支或 tag:

- main 分支(开发版): `KERNELSU_TAG=main`
- 最新 TAG(稳定版): `KERNELSU_TAG=`
- 指定 TAG(如`v0.5.2`): `KERNELSU_TAG=v0.5.2`

#### KernelSU Manager signature size and hash

自定义KernelSU管理器签名的size值和hash值，如果不需要自定义管理器则请留空或填入官方默认值：

`KSU_EXPECTED_SIZE=0x033b`

`KSU_EXPECTED_HASH=0xb0b91415`

可键入`ksud debug get-sign <apk_path>`获取apk签名的size值和hash值

### Disable LTO

LTO 用于优化内核，但有些时候会导致错误

### Disable CC_WERROR

用于修复某些不支持或关闭了Kprobes的内核，修复KernelSU未检测到开启Kprobes的变量抛出警告导致错误

### Add Kprobes Config

自动在 defconfig 注入参数

### Add overlayfs Config

此参数为 KernelSU 模块和 system 分区读写提供支持
自动在 defconfig 注入参数

### Enable ccache

启用缓存，让第二次编译内核更快，最少可以减少 2/5 的时间

### Need DTBO

上传 DTBO
部分设备需要

### Build Boot IMG

> 从之前的 Workflows 合并进来的，可以查看历史提交

编译 boot.img，需要你提供`Source boot image`

### Source Boot Image

故名思义，提供一个源系统可以正常开机的 boot 镜像，需要直链，最好是同一套内核源码以及与你当前系统同一套设备树从 aosp 构建出来的。ramdisk 里面包含分区表以及 init，没有的话构建出来的镜像会无法正常引导。

例如: https://raw.githubusercontent.com/xiaoleGun/KernelSU_action/main/boot/boot-wayne-from-Miku-UI-latest.img


## 2、打开Actions开始编译

一般来说只需执行以下3个工作流就够了

Build kernel use Google-git

Build kernel by clang-r416183b1

Build kernel by clang-r383902b

Build kernel by google-clang10 可选

本二改项目，包含18个工作流可同时工作可最大化提高编译内核的成功率，助力LXC,KernelSU 。

工具链集包含AOSP clang ,LLVM, ARM gcc ,SD-clang(高通的llvm工具链）,Proton-clang, Slim-LLVM, ZyC clang,Mandi-Sa-clang,linaro gcc ,Google gcc_4.9 ,Android NDK ,arter97_gcc等

参考视频：

1、利用KernelSU action构建属于自己的KernelSU 内核（非gki机型），无需电脑只需一部手机即可（将就看下）_哔哩哔哩_https://www.bilibili.com/video/BV17X4y177Up

2、LXC_KernelSU_Action的项目演示，最大化提高编译内核的成功率，助力LXC,KernelSU 。_哔哩哔哩_https://www.bilibili.com/video/BV1Ph4y1v7Bq

3、[编译]构建属于你自己的内核镜像_哔哩哔哩_https://www.bilibili.com/video/BV1eH4y1q7pt

4、使用KernelSU_Action编译自己的KernelSU内核_哔哩哔哩_https://www.bilibili.com/video/BV1Jo4y1N7eC/

## 手机型号代号及CPU型号代号参考

| 手机型号 | 手机代号 | 处理器型号 | 处理器代号 | 
| :------------: | :------------: | :------------: | :------------: |
| k20pro | xiaomi_raphael | 骁龙sdm855| xiaomi_sm8150 |
| 小米9透明探索版  | xiaomi_cepheus  | 骁龙sdm855  | xiaomi_sm8150 | 
|  小米6X | xiaomi_wayne  | 骁龙sdm660  | xiaomi_msm8976 Plus | 
| 小米MIX | xiaomi_lithium | 骁龙sdm821 | xiaomi_msm8996 Pro | 
| Redmi 9 | redmi_lancelot | MTK Helio G80 | redmi_MT6769V/CU |
| Redmi 9A | redmi_dandelion |MTK Helio G25 | redmi_MT6762G |
| RedMi Note8 | redmi_ginkgo | 骁龙sdm665 | redmi_sm6125 | 
| Redmi 3S/X | redmi_land | 骁龙sdm665 | redmi_msm8937 | 

更多：移动端常见芯片名及其对应代号_天玑9200+代号-CSDN博客

https://blog.csdn.net/aaa111/article/details/121532695

### 如何查看自己的手机型号？

MT文件管理器-进入System文件夹-找到build.prop这个文件打开

ro.product.name=lmi

ro.product.device=lmi

ro.rom.jxdh=lmi

ro.product.mod_device=lmi

这几行代码的末尾，就是机型代号

### miui官方源码地址          列表向左拉动-查看对应地址（被挡住了）

https://github.com/MiCode/Xiaomi_Kernel_OpenSource

或查看：https://github.com/hubhike/LXC-Docker-KernelSU_Action/blob/main/README.md

| Branch | Device | Android version | Base tag | Link |
| :-: | :-: | :-: | :-: | :-: |
| air-t-oss | Redmi 13R 5G, Redmi 13C 5G | Android T | alps-mp-t0.mp1.tc8sp2-V1.32 | [air-t-oss](https://github.com/MiCode/MTK_kernel_modules/tree/air-t-oss) |
| alioth-r-oss | Mi 10S, Redmi K40 | Android R | LA.UM.9.12.r1-08000-SMxx50.0 | [alioth-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/alioth-r-oss) |   
| andromeda-p-oss | Mi MIX 3 5G | Android P | LA.UM.7.1.r1-13000-sm8150.0 | [andromeda-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/andromeda-p-oss) |
| ares-r-oss | Redmi K40 Gaming | Android R | MTK | [ares-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/ares-r-oss) |    
| armani-jb-oss | Redmi 1S | Android JB | LNX.LA.3.2-09720-8x26.0 | [armani-jb-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/armani-jb-oss) |
| armani-kk-oss | Redmi 1S, Redmi Note Single SIM | Android KK | LNX.LA.3.5.2.2.2-00510-8x26.0 | [armani-kk-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/armani-kk-oss) |
| apollo-q-oss | Redmi K30S Ultra | Android Q | LA.UM.8.12.r1-10600-sm8250.0 | [apollo-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/apollo-q-oss) |
| begonia-p-oss | Redmi Note 8 Pro | Android P | MTK | [begonia-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/begonia-p-oss) |
| begonia-q-oss | Redmi Note 8 Pro | Android Q | MTK | [begonia-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/begonia-q-oss) |
| begonia-r-oss | Redmi Note 8 Pro | Android R | MTK | [begonia-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/begonia-r-oss) |
| biloba-r-oss | Redmi Note 8 | Android R | MTK | [biloba-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/biloba-r-oss) |
| bomb-q-oss | Redmi 10X, Redmi 10X Pro | Android Q | MTK | [bomb-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/bomb-q-oss) |
| cactus-o-oss | Redmi 6A, Redmi 6 | Android O | MTK | [cactus-o-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/cactus-o-oss) |
| cactus-p-oss | Redmi 6A, Redmi 6 | Android P | MTK | [cactus-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/cactus-p-oss) |
| cancro-kk-oss | Mi 3, Mi 4, Mi Note | Android KK | LNX.LA.3.5.2.2.1-04310-8x74.0 | [cancro-kk-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/cancro-kk-oss) |
| cannon-r-oss | Redmi Note 9 | Android R | MTK | [cannon-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/cannon-r-oss) |   
| cancro-m-oss | Mi 3, Mi 4, Mi Note | Android M | LA.BF.1.1.3-01310-8x74.0 | [cancro-m-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/cancro-m-oss) |
| cappu-n-oss | Mi Pad 3 | Android N | MTK | [cappu-n-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/cappu-n-oss) |
| cas-q-oss | Mi 10 Ultra, Mi 10 Pro, Mi 10, Redmi K30 Pro | Android Q | LA.UM.8.12.r1-10600-sm8250.0 | [cas-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/cas-q-oss) |
| cannon-q-oss | Redmi Note 9 | Android Q | MTK | [cannon-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/cannon-q-oss) |
| camellia-r-oss | Redmi Note 10 | Android R | MTK | [camellia-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/camellia-r-oss) |  
| cepheus-p-oss | Mi 9 | Android P | LA.UM.7.1.r1-07600-sm8150.0 | [cepheus-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/cepheus-p-oss) |
| cepheus-q-oss | Mi 9, Redmi K20 Pro | Android Q | LA.UM.8.1.r1-08700-sm8150.0 | [cepheus-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/cepheus-q-oss) |
| cezanne-q-oss | Redmi K30 Ultra | Android Q | MTK | [cezanne-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/cezanne-q-oss) |
| cezanne-r-oss | Redmi 10X, Redmi 10X Pro, Redmi K30 Ultra | Android R | MTK | [cezanne-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/cezanne-r-oss) |   
| chopin-r-oss | Redmi Note 10 Pro | Android R | MTK | [chopin-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/chopin-r-oss) |  
| clover-o-oss | Mi Pad 4 | Android O | LA.UM.6.2.r1-08100-sdm660.0 | [clover-o-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/clover-o-oss) |
| cmi-r-oss | Redmi K30S Ultra, Mi 10 Ultra, Mi 10 Pro, Redmi K30 Pro, Mi 10 | Android R | LA.UM.9.12.r1-08000-SMxx50.0 | [cmi-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/cmi-r-oss) | 
| courbet-r-oss | Mi 11 Lite | Android R | LA.UM.9.1.r1-06700-SMxxx0.0 | [courbet-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/courbet-r-oss) |   
| crux-p-oss | Mi9 Pro 5G | Android P | LA.UM.7.1.r1-15600-sm8150.0 | [crux-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/crux-p-oss) |
| curtana-q-oss | Redmi Note 9 Pro,Redmi Note 9 Pro Max | Android Q | LA.UM.8.9.r1-07100-SM6xx.0 | [curtana-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/curtana-q-oss) |
| dagu-s-oss | Xiaomi Pad 5 pro 12.4 | Android S | LA.UM.9.12.r1-13300-SMxx50.QSSI12.0-1 | [dagu-s-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/dagu-s-oss) |
| daisy-o-oss | Mi A2 lite, Redmi 6pro | Android O | LA.UM.6.6.r1-07400-89xx.0-1 | [daisy-o-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/daisy-o-oss) |
| daisy-p-oss | Mi A2 lite | Android P | LA.UM.7.6.r1-02800-89xx.0 | [daisy-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/daisy-p-oss) |
| daisy-q-oss | Mi A2 Lite| Android Q | LA.UM.8.6.r1-02600-89xx.0 | [daisy-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/daisy-q-oss) |
| dandelion-q-oss | Redmi 9C, Redmi POCO C3, Redmi 9A | Android Q | MTK | [dandelion-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/dandelion-q-oss) |
| davinci-p-oss | Redmi K20 | Android P | LA.UM.7.9.r1-03300-sm6150.0 | [davinci-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/davinci-p-oss) |
| dior-kk-oss | Redmi Note Single SIM | Android KK | LNX.LA.3.5.2.2.2-00510-8x26.0 | [dior-kk-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/dior-kk-oss) |
| dipper-o-oss | Mi 8/MI 8 Explorer Edition/POCOPHONE F1 | Android 8.1 | LA.UM.6.3.r4-03000-sdm845.0 | [dipper-o-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/dipper-o-oss) |
| dipper-p-oss | MIX2S, MIX3, MI 8, POCOPHONE F1, MI 8UD, MI 8 Explorer Edition | Android P | LA.UM.7.3.r1-04500-sdm845.0 | [dipper-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/dipper-p-oss) |
| dipper-q-oss | MI 8, MIX2S, POCOPHONE F1, MI 8UD, MIX 3, MI 8 Explorer Edition | Android Q | LA.UM.8.3.r1-05800-sdm845.0 | [dipper-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/dipper-q-oss) |
| earth-s-oss | Redmi 12C, POCO C55 | Android S | alps-mp-s0.mp1.tc8sp2-cs1-V1.3 | [earth-s-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/earth-s-oss) |
| elish-r-oss | Xiaomi Pad 5 Pro, Xiaomi Pad 5 Pro 5G | Android R | LA.UM.9.12.r1-08000-SMxx50.0 | [elish-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/elish-r-oss) |
| ferrari-l-oss | Mi 4i | Android L | LA.BR.1.1.2-01120-8x16.0 | [ferrari-l-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/ferrari-l-oss) |
| fire-t-oss | Redmi 12 | Android T | alps-mp-s0.mp1.tc8sp2-cs1-xm.V1.0.13_P53 | [fire-t-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/fire-t-oss) |
| fleur-s-oss | Redmi Note 11S, POCO M4 Pro | Android S | alps-mp-r0.mp8.tc8sp3-mt6781-V1.5.1 | [fleur-s-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/fleur-s-oss) |
| gram-q-oss | MI POCO M2 Pro,Redmi Note 9 Pro,Redmi Note 9 Pro Max | Android Q | LA.UM.8.9.r1-07100-SM6xx.0 | [gram-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/gram-q-oss) |
| gemini-m-oss | Mi 5 | Android M | LA.HB.1.1.1.c2_rb1035 | [gemini-m-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/gemini-m-oss) |
| ginkgo-p-oss | Redmi Note 8 | Android P | LA.UM.7.11.r1-02700-NICOBAR.0 | [ginkgo-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/ginkgo-p-oss) |
| ginkgo-q-oss | Redmi Note 8, Redmi Note 8T | Android Q | LA.UM.8.11.r1-02400-NICOBAR.0 | [ginkgo-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/ginkgo-q-oss) |
| grus-p-oss | Mi 8SE, Mi 9SE | Android P | LA.UM.7.8.r1-04400-SDM710.0 | [grus-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/grus-p-oss) |
| gucci-kk-oss | Redmi Note Dual SIM | Android KK | LNX.LA.3.7.2.1.c6-01400-8x16.0 | [gucci-kk-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/gucci-kk-oss) |
| gauguin-q-oss | Redmi Note 9 Pro | Android Q | LA.UM.8.13.r1-09200-SAIPAN.0 | [gauguin-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/gauguin-q-oss) |
| gauguin-r-oss | Redmi Note 9 Pro | Android R | LA.UM.9.12.r1-08000-SMxx50.0 | [gauguin-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/gauguin-r-oss) |
| hennessy-l-oss | Redmi Note3,Redmi Note2,Redmi Note2 Pro | Android L | MTK | [hennessy-l-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/hennessy-l-oss) |
| haydn-r-oss | Redmi K40 Pro | Android R | LA.UM.9.14.r1-11500-LAHAINA.0 | [haydn-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/haydn-r-oss) |
| hydrogen-m-oss | Mi Max, Mi Max Pro | Android M | LA.BR.1.3.4-05310-8976.0 | [hydrogen-m-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/hydrogen-m-oss) |
| ido-l-oss | Redmi 2, Redmi 3 | Android L | LA.BR.1.2.4-04410-8x16.0 | [ido-l-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/ido-l-oss) |
| jasmine-o-oss| Mi A2 | Android O | LA.UM.6.2.r1-06100-sdm660.0-1.151426.0.156675.1 | [jasmine-o-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/jasmine-o-oss) |
| jasmine-p-oss| Mi A2 | Android P | LA.UM.7.2.r1-04900-sdm660.0 | [jasmine-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/jasmine-p-oss) |
| jasmine-q-oss| Mi A2 | Android Q | LA.UM.8.2.r1-04300-sdm660.0 | [jasmine-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/jasmine-q-oss) |
| jason-n-oss | Mi Note 3 | Android N | LA.UM.6.1.r1-08100-sdm660.0 | [jason-n-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/jason-n-oss) |
| jason-p-oss | Mi Note 3 | Android P | LA.UM.7.2.r1-05200-sdm660.0 | [jason-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/jason-p-oss) |
| kenzo-l-oss | Redmi Note 3 Full Netcom | Android L | LA.BR.1.3.2-04330-8976.0 | [kenzo-l-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/kenzo-l-oss) |
| land-m-oss | Redmi 3S, Redmi 3X | Android M | LA.UM.5.1-03810-8x37.0 | [land-m-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/land-m-oss) |
| latte-l-oss | Mi Pad 2 | Android L | imin.cht_rvp.a51.20150519-2015_ww20_b | [latte-l-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/latte-l-oss) |
| laurel-q-oss| Mi A3 | Android Q | LA.UM.8.11.r1-01200-NICOBAR.0 | [laurel-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/laurel-q-oss) |
| laurus-p-oss| MI CC 9e, Mi A3 | Android P | LA.UM.7.11.r1-02100-NICOBAR.0 | [laurus-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/laurus-p-oss) |
| laurus-q-oss | MI CC 9e | Android Q | LA.UM.8.11.r1-01200-NICOBAR.0-2 | [laurus-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/laurus-q-oss) |
| lavender-p-oss| Redmi Note 7 | Android P | LA.UM.7.2.r1-04900-sdm660.0 | [lavender-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/lavender-p-oss) |
| lavender-q-oss | Redmi Note 7 | Android Q | LA.UM.8.2.r1-04800-sdm660.0-1 | [lavender-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/lavender-q-oss) |
| lancelot-q-oss | Redmi 10X 4G,Redmi 9,Redmi POCO M2 | Android Q | MTK | [lancelot-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/lancelot-q-oss) |
| laurel-r-oss | Mi A3 | Android R | LA.UM.9.11.r1-02100-NICOBAR.0 | [laurel-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/laurel-r-oss) | 
| libra-l-oss | Mi 4S, Mi 4C, Mi Note Pro | Android L | LA.BF64.1.2.1.c1-05310-8x92.0 | [libra-l-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/libra-l-oss) |
| libra-n-oss | Mi 4S, Mi 4C, Mi Note Pro | Android N | LA.BF64.1.2.3-01110-8x94.0 | [libra-n-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/libra-n-oss) |
| liuqin-t-oss | Xiaomi Pad 6 Pro | Android T | LA.VENDOR.1.0.r1-13100-WAIPIO.0-1 | [liuqin-t-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/liuqin-t-oss) |
| lisa-r-oss | Mi 11 LE | Android R | LA.UM.9.14.r1-16700-LAHAINA.0 | [lisa-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/lisa-r-oss) |
| lmi-q-oss | Redmi K30 Pro| Android Q | LA.UM.8.12.r1-06000-sm8250.0 | [lmi-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/lmi-q-oss) |
| lotus-o-oss | Mi Play | Android O | MTK | [lotus-o-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/lotus-o-oss) |
| lime-q-oss | Redmi Note 9 4G,Redmi POCO M3 | Android Q | LA.UM.8.15.r1-06600-KAMORTA.0 | [lime-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/lime-q-oss) |
| markw-m-oss | Redmi 4 Premium | Android M | LA.UM.5.3-03710-89xx.0 | [markw-m-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/markw-m-oss) |
| mi1_kernel | Mi 1 | Android ICS | M8260AAABQNLZA313065 | [mi1_kernel](https://github.com/MiCode/mi1_kernel) |
| mi2_kernel | Mi 2, Mi 2A, Mi 2S | Android KK | A8064AAAAANLYA103051 | [mi2_kernel](https://github.com/MiCode/mi2_kernel) |
| mido-m-oss | Redmi Note 4X Standard | Android M | LA.UM.5.3-06310-89xx.0 | [mido-m-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/mido-m-oss) |
| mido-n-oss | Redmi Note 4X Standard | Android N | LA.UM.5.6.r1-02100-89xx.0 | [mido-n-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/mido-n-oss) |
| mocha-kk-oss | Mi Pad | Android KK | tegra-19r15.1-android-4.4 | [mocha-kk-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/mocha-kk-oss) |
| mojito-r-oss | Redmi Note 10 | Android R | LA.UM.9.1.r1-07700-SMxxx0.0 | [mojito-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/mojito-r-oss) |
| merlin-r-oss | Redmi 10X 4G, Redmi 9 and POCO M2 | Android R | MTK | [merlin-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/merlin-r-oss) | 
| nabu-r-oss | Xiaomi Pad 5 | Android R | LA.UM.9.1.r1-07000-SMxxx0.0 | [nabu-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/nabu-r-oss) |
| nitrogen-o-oss | MI MAX3,Mi Note 3 | Android O | LA.UM.6.2.r1-07400-sdm660.0 | [nitrogen-o-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/nitrogen-o-oss) |
| nitrogen-p-oss | MI MAX3,MI 8Lite | Android P | LA.UM.7.2.r1-05200-sdm660.0 | [nitrogen-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/nitrogen-p-oss) |
| nitrogen-q-oss | MI MAX3,MI 8Lite| Android Q | LLA.UM.8.2.r1-04300-sdm660.0 | [nitrogen-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/nitrogen-q-oss) |
| mona-r-oss | Xiaomi Civi | Android R | LA.UM.9.14.r1-11500-LAHAINA.0 | [mona-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/mona-r-oss) |
| odin-r-oss | MIX 4 | Android R | LA.UM.9.14.r1-16700-LAHAINA.0 | [odin-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/odin-r-oss) |  
| olive-p-oss | Redmi 8, Redmi 8A | Android P | LA.UM.7.6.2.r1-08100-89xx.0 | [olive-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/olive-p-oss) |
| olive-q-oss | Redmi 7A, Redmi 8, Redmi 8A, Redmi 8A | Android Q | LA.UM.8.6.2.r1-05300-89xx.0 | [olive-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/olive-q-oss) |
| olivewood-p-oss  | Redmi 8A Dual | Android P | LA.UM.7.6.2.r1-08100-89xx.0 | [olivewood-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/olivewood-p-oss) |
| onc-p-oss | Redmi 7, Redmi Y3 | Android P | LA.UM.7.6.2.r1-04200-89xx.0 | [onc-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/onc-p-oss) |
| onc-q-oss | Redmi 7, Redmi Y3 | Android Q | LA.UM.8.6.2.r1-04900-89xx.0 | [onc-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/onc-q-oss) |
| oxygen-n-oss | Mi Max 2 | Android N | LA.UM.5.6.r1-01900-89xx.0 | [oxygen-n-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/oxygen-n-oss) |
| perseus-p-oss | MIX 3 | Android P | LA.UM.7.3.r1-04500-sdm845.0 | [perseus-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/perseus-p-oss) |
| phoenix-q-oss | Redmi K30, Redmi K20 | Android Q | LA.UM.8.9.r1-03800-sm6150.0 | [phoenix-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/phoenix-q-oss) |
| phoenix-r-oss | Redmi K20, Redmi K30, MI Note 10 Lite, MI CC9 Pro | Android R | LA.UM.9.1.r1-06700-SMxxx0.0-1 | [phoenix-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/phoenix-r-oss) |
| picasso-q-oss | Redmi K30 5G | Android Q | LA.UM.8.13.r1-03300-SAIPAN.0 | [picasso-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/picasso-q-oss) |
| picasso-r-oss | Mi 10 Lite 5G, Redmi K30 5G, Mi 10 Lite Zoom | Android R | LA.UM.9.12.r1-08000-SMxx50.0 | [picasso-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/picasso-r-oss) |
| pissarro-r-oss | Redmi Note 11 Pro, Redmi Note 11 Pro+ | Android R | MTK | [pissarro-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/pissarro-r-oss) |
| pine-p-oss | Redmi 7A | Android P | LA.UM.7.6.2.r1-08100-89xx.0 | [pine-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/pine-p-oss) |
| pipa-t-oss | Xiaomi Pad 6 | Android T | LA.UM.9.12.r1-13300-SMxx50.QSSI12.0-1 | [pipa-t-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/pipa-t-oss) |
| pisces-kk-oss | Mi 3 China Mobile | Android KK | tegra-17r18-android-4.2 | [pisces-kk-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/pisces-kk-oss) |
| platina-o-oss | MI 8 Lite | Android O | LA.UM.6.2.r1-07400-sdm660.0 | [platina-o-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/platina-o-oss) |
| polaris-o-oss | Mi MIX 2S | Android O | LA.UM.6.3.r1_r00455 | [polaris-o-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/polaris-o-oss) |
| psyche-r-oss | Mi 12X | Android R | LA.UM.9.12.r1-08000-SMxx50.0 | [psyche-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/psyche-r-oss) |
| pyxis-p-oss | MI CC 9, MI CC 9 Meitu Edition | Android P | LA.UM.7.8.r1-04400-SDM710.0 | [pyxis-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/pyxis-p-oss) |
| raphael-p-oss | Redmi K20 Pro | Android P | LA.UM.7.1.r1-12100-sm8150.0 | [raphael-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/raphael-p-oss) |
| redwood-s-oss | Redmi Note 12 Pro Speed, POCO X5 Pro 5G | Android S | LA.UM.9.14.r1-18200-LAHAINA.QSSI12.0 | [redwood-s-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/redwood-s-oss) |
| riva-n-oss | Redmi 4A, Redmi 5, Redmi 5A | Android N | LA.UM.5.6.r1-05900-89xx.0 | [riva-n-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/riva-n-oss) |
| riva-o-oss | Redmi 5, Redmi 5A | Android O | LA.UM.6.6.r1-09900-89xx.0 | [riva-o-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/riva-o-oss) |
| rosemary-r-oss | Redmi Note 10S | Android R | MTK | [rosemary-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/rosemary-r-oss) |   
| sagit-n-oss | Mi 6, Mi MIX 2 | Android N | MSM8998.LA.1.1.r1-00232-STD.PROD-3 | [sagit-n-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/sagit-n-oss) |
| sagit-o-oss | Mi 6, Mi MIX 2 | Android O | LA.UM.6.4.r1-04900-8x98.0 | [sagit-o-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/sagit-o-oss) |
| sagit-p-oss | Mi 6, Mi MIX 2 | Android P | LA.UM.7.4.r1-04700-8x98.0 | [sagit-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/sagit-p-oss) |
| sakura-o-oss | Redmi 6Pro, Redmi 5Plus,Mi 5X | Android O | LA.UM.6.6.r1-07400-89xx.0 | [sakura-o-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/sakura-o-oss) |
| sakura-p-oss | Redmi 6Pro | Android P | LA.UM.7.6.r1-02800-89xx.0 | [sakura-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/sakura-p-oss) |
| santoni-n-oss | Redmi 4X | Android N | LA.UM.5.6.r1-04600-89xx.0 | [santoni-n-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/santoni-n-oss) |
| scorpio-m-oss | Mi 5, Mi 5s, Mi 5s Plus, Mi MIX, Mi Note 2 | Android M | LA.HB.1.3.1_101rb1275 | [scorpio-m-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/scorpio-m-oss) |
| scorpio-n-oss | Mi 5, Mi 5s, Mi 5s Plus, Mi MIX, Mi Note 2 | Android N | LA.UM.5.5.r1-01800-8x96.0 | [scorpio-n-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/scorpio-n-oss) |
| scorpio-o-oss | Mi 5, Mi 5s, Mi 5s Plus, Mi MIX, Mi Note 2 | Android O | LA.UM.6.5.r1-04300-8x96.0 | [scorpio-o-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/scorpio-o-oss) |
| selene-r-oss | Redmi 10 Prime | Android R | MTK | [selene-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/selene-r-oss) |
| sea-t-oss | Redmi Note 12S | Android T | bsp-t-alps-release-s0.mp1.tc8sp2-cs1-xm-V1.0.13 | [sea-t-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/sea-t-oss) |
| sirius-o-oss | Mi 8 SE | Android O | LA.UM.6.8.r2-00700-SDM710.0 | [sirius-o-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/sirius-o-oss) |
| sirius-q-oss | MI 8SE, MI 9SE, MI CC 9, MI CC 9 Meitu Edition | Android Q | LA.UM.8.8.r1-05100-SDM710.0 | [sirius-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/sirius-q-oss) |
| sky-t-oss | Redmi Note 12R | Android T | LA.VENDOR.1.0.r1-21200-WAIPIO.QSSI13.0-1 | [sky-t-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/sky-t-oss) |
| star-r-oss | Mi 11 Lite 5G, Mi 11 Pro, Mi 11 Ultra, MIX FOLD | Android R | LA.UM.9.14.r1-11500-LAHAINA.0 | [star-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/star-r-oss) |  
| surya-q-oss | Redmi POCO X3 | Android Q | LA.UM.8.9.r1-09300-SM6xx.0 | [surya-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/surya-q-oss) |
| sweet-r-oss | Redmi Note 10 Pro | Android R | LA.UM.9.1.r1-06700-SMxxx0.0-1 | [sweet-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/sweet-r-oss) |
| sweet_k6a-r-oss | Redmi Note 12 Pro | Android R | LA.UM.9.1.r1-06700-SMxxx0.0-1.436288.2.444481.1 |[sweet_k6a-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/sweet_k6a-r-oss) |
| taoyao-s-oss | Xiaomi 12 Lite | Android S | LA.UM.9.14.r1-18300.05-LAHAINA.QSSI12.0-1 | [taoyao-s-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/taoyao-s-oss) |
| thomas-kk-oss | Redmi 2 Standard | Android KK | LNX.LA.3.7.2.1.c6-02400-8x16.0 | [thomas-kk-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/thomas-kk-oss) |
| tiare-o-oss | Redmi Go | Android O | LA.UM.6.6.r1-09900-89xx.0 | [tiare-o-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/tiare-o-oss) |
| tiffany-n-oss | Mi 5X, Redmi 5 Plus | Android N | LA.UM.5.3-06310-89xx.0 | [tiffany-n-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/tiffany-n-oss) |
| tissot-n-oss | Mi A1 | Android N | LA.UM.5.3-06310-89xx.0 | [tissot-n-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/tissot-n-oss) |
| tissot-o-oss | Mi A1 | Android 8.0 | LA.UM.6.6.r1-04000-89xx.0 | [tissot-o-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/tissot-o-oss) |
| tissot-o-oss-8.1 | Mi A1 | Android 8.1 | LA.UM.6.6.r1-08600-89xx.0 | [tissot-o-oss-8.1](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/tissot-o-oss-8.1) |
| tissot-p-oss | Mi A1 | Android P | LA.UM.7.6.r1-02800-89xx.0 | [tissot-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/tissot-p-oss) |
| tulip-p-oss | Redmi Note 5, Redmi Note6 Pro, Mi 6X | Android P | LA.UM.7.2.r1-04900-sdm660.0 | [tulip-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/tulip-p-oss) |
| tucana-p-oss | Mi CC9 Pro | Android P | LA.UM.7.9.r1-05700-sm6150.0 | [tucana-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/tucana-p-oss) |
| tucana-q-oss | MI CC9 Pro | Android Q | LA.UM.8.9.r1-03800-sm6150.0-1 | [tucana-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/tucana-q-oss) |
| toco-q-oss | MI Note 10 | Android Q | LA.UM.8.9.r1-03800-sm6150.0-1 | [toco-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/toco-q-oss) |
| ulysse-n-oss | Redmi Note 5A | Android N | LA.UM.5.6.r1-04600-89xx.0 | [ulysse-n-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/ulysse-n-oss) |
| umi-q-oss | Mi 10 Pro, Mi 10 | Android Q | LA.UM.8.12.r1-06000-sm8250.0 | [umi-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/umi-q-oss) |
| vangogh-q-oss | Mi 10 Lite 5G, Mi 10 Lite Zoom | Android Q | LA.UM.8.13.r1-04700-SAIPAN.0 | [vangogh-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/vangogh-q-oss) |
| vayu-r-oss | Redmi POCO X3 Pro | Android R | LA.UM.9.1.r1-07000-SMxxx0.0 | [vayu-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/vayu-r-oss) |  
| venus-r-oss | Mi 11 | Android R | LA.UM.9.14.r1-10000-LAHAINA.0 | [venus-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/venus-r-oss) |
| veux-r-oss | Redmi Note 11E Pro, Redmi Note 11 Pro 5G, Redmi Note 11 Pro+ 5G | Android R | LA.UM.9.16.r1-07900-MANNAR.0-1 | [veux-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/veux-r-oss) |
| vili-r-oss | Xiaomi 11T Pro | Android R | LA.UM.9.14.r1-16700-LAHAINA.0 | [vili-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/vili-r-oss) |
| violet-p-oss | Redmi Note 7Pro | Android P | LA.UM.7.9.r1-03500-sm6150.0 | [violet-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/violet-p-oss) |
| violet-q-oss | Redmi Note 7 Pro | Android Q | LA.UM.8.9.r1-04400-SM6xx.0-1 | [violet-q-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/violet-q-oss) |
| whyred-n-oss | Redmi Note 5 | Android N | LA.UM.6.1.r1-11000-sdm660.0 | [whyred-n-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/whyred-n-oss) |
| whyred-o-oss | Redmi Note 5, Redmi Note6 Pro, Mi 6X | Android O | LA.UM.6.2.r1-06100-sdm660.0 | [whyred-o-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/whyred-o-oss) |
| willow-p-oss | Redmi Note 8T | Android P | LA.UM.7.11.r1-02700-NICOBAR.0 | [willow-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/willow-p-oss) |
| yudi-t-oss | Xiaomi Pad 6 Max 14 | Android T | qcom-S-LA.VENDOR.1.0.r1-21800-r2.0.r1_00047.0 | [yudi-t-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/yudi-t-oss) |
| ysl-o-oss | Redmi S2 | Android O | LA.UM.6.6.r1-06200-89xx.0 | [ysl-o-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/ysl-o-oss) |
| ysl-p-oss | Redmi S2 | Android P | LA.UM.7.6.r1-03900-89xx.0 | [ysl-p-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/ysl-p-oss) |
| yuechu-t-oss | Xiaomi Civi3 | Android T | bsp-t-alps-release-s0.mp1.tc8sp2-mt6983-V1.0.1_W53 | [yuechu-t-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/yuechu-t-oss) |
| zeus-s-oss | Mi 12, Mi 12 Pro | Android S | LA.VENDOR.1.0.r1-09100-r1.0.r1 | [zeus-s-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/zeus-s-oss) |
| zijin-s-oss | Xiaomi Civi 1S | Android S | LA.UM.9.14.r1-18300.05-LAHAINA.QSSI12.0-1 | [zijin-s-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/zijin-s-oss) |
| ziyi-s-oss | Xiaomi civi2 | Android S | LA.VENDOR.1.0.r1-13100-r1.0.r1_00037.0 | [ziyi-s-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/ziyi-s-oss) |
| munch-s-oss | Redmi K40S | Android S | LA.UM.9.12.r1-13300-SMxx50.QSSI12.0-1 | [munch-s-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/munch-s-oss) |
| ice-s-oss | Redmi POCO C50 | Android S | alps-mp-s0.mp1.tc8sp3-cs1-V1 | [ice-s-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/ice-s-oss) |
| rubens-s-oss | Redmi K50 | Android S | alps-release-s0.mp1.tc8sp2-mt6983 | [rubens-s-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/rubens-s-oss) |
| socrates-t-oss | Redmi K60 Pro | Android T | LA.VENDOR.13.2.0.r1-07300-r1.0.r1_00020.0 |[socrates-t-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/socrates-t-oss) |
| marble-s-oss | Redmi Note 12 Turbo | Android S | LA.VENDOR.1.0.r1-19000-r2.0.r1_00029.0 |[marble-s-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/marble-s-oss) |
| ruby-s-oss | Redmi Note12 Pro | Android S | alps-mp-s0.mp1.tc8sp-cs1-V1 |[ruby-s-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/ruby-s-oss) |
| mayfly-s-oss | Xiaomi 12S | Android S | LA.VENDOR.1.0.r1-13100-r1.0.r1_00037.0 |[mayfly-s-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/mayfly-s-oss) |
| mondrian-s-oss | Redmi K60 | Android S | LA.VENDOR.1.0.r1-11900-r1.0.r1_00029.0 |[mondrian-s-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/mondrian-s-oss) |
| daumier-s-oss | Xiaomi 12 Pro | Android S | alps-release-s0.mp1.tc8sp2-mt6983-V1.0.1_W53 |[daumier-s-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/daumier-s-oss) |
| spes-r-oss | Redmi note 11 | Android R | Snapdragon_Mid_2020.SPF.1.0.1R_r00024.0 |[spes-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/spes-r-oss) |
| moonstone-s-oss | Redmi POCO X5 5G | Android S | Snapdragon_Mid_2021.SPF.2.0_r2.0_00023.0 |[moonstone-s-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/moonstone-s-oss) |
|  rembrandt-s-oss | Redmi K60E  | Android S | t-alps-release-s0.mp1.tc8sp2-mt6983-V1.0.1 |[ rembrandt-s-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/rembrandt-s-oss) |
| xaga-s-oss | Redmi Note 11T Pro | Android S | alps-release-s0.mp1.tc8sp2-mt6983 |[xaga-s-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/xaga-s-oss) |
| bsp-plato-s-oss | Xiaomi 12T | Android S | t-alps-release-s0.mp1.tc8sp2-mt6983-V1.0.1 |[bsp-plato-s-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/bsp-plato-s-oss) |
| ishtar-t-oss | Xiaomi 13 Ultra | Android T | LA.VENDOR.13.2.0.r1-07300-KAILUA.0 |[ishtar-t-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/ishtar-t-oss) |
| fuxi-t-oss | Xiaomi 13, Xiaomi 13 Pro | Android T | LA.VENDOR.13.2.0.r1-07300-KAILUA.0 |[fuxi-t-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/fuxi-t-oss) |
| yunluo-s-oss | Redmi Pad | Android S | t-alps-release-s0.mp1.tc8sp2-cs1-xm-V1 |[yunluo-s-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/yunluo-s-oss) |
| topaz-t-oss | Redmi Note 12 | Android T | AU_LINUX_ANDROID_LA.VENDOR.13.2.1.R1.11.00.00.1008.048 |[topaz-t-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/topaz-t-oss) |
| selene-r-oss-update | Redmi 10 Prime | Android R | alps-mp-r0.mp1.tc8sp2-V2.66.1 |[selene-r-oss-update](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/selene-r-oss-update) |
| viva-r-oss | Redmi Note 11 Pro | Android R | alps-mp-r0.mp8.tc8sp1-mt6781-V1.5.1_wingtech.xm.r0mp8.k6781v1.64_P60 |[viva-r-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/viva-r-oss) |
| pearl-s-oss | Redmi Note 12T Pro | Android S | alps-mp-s0.mp1.tc8sp2-mt6983-V1.0.13_P189 |[pearl-s-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/pearl-s-oss) |
| diting-s-oss | Redmi K50 Ultra | Android S | qcom-S-LA.VENDOR.1.0.r1-13100-r1.0.r1_00037.0 |[diting-s-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/bsp-diting-s-oss) |
| pissarro-s-oss | Redmi Note 11 Pro | Android S | bsp-s-t-alps-release-s0.bsp-pre10.2 | [pissarro-s-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/pissarro-s-oss) |
| corot-s-oss | Redmi K60 Ultra | Android T | t-alps-release-t0.mp1.tc8sp2-V1.14 | [corot-s-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/corot-t-oss) |
| bsp-aristotle-s-oss | Xiaomi 13T | Android T | alps-mp-s0.mp1.tc8sp2-mt6983-V1.0.1 | [bsp-aristotle-s-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/bsp-aristotle-s-oss) |
| garnet-t-oss | Redmi Note 13 Pro | Android T | KERNEL.PLATFORM.1.0.r1-15100-kernel.0-1* | [garnet-t-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/garnet-t-oss) |
| xun-t-oss | Redmi Pad SE | Android T | AU_LINUX_ANDROID_LA.VENDOR.13.2.1.R1.11.00.00.587.064 | [xun-t-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/xun-t-oss) |
| bsp-vermeer-t-oss | Redmi K70 | Android U | LA.VENDOR.13.2.0.r1-14800-r1.0.r1_00043.0 | [bsp-vermeer-t-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/bsp-vermeer-t-oss) |
| bsp-duchamp-u-oss | Redmi K70E | Android U | t-alps-release-u0.mp1.tc8sp1-V1 | [bsp-duchamp-u-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/bsp-duchamp-u-oss) |
| zircon-t-oss  | Redmi Note 13 Pro+ | Android T | t-alps-release-t0.mp1.tc8sp2-V1.14 | [zircon-t-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/zircon-t-oss) |
| gold-s-oss  | Redmi Note 13 5G | Android T | t-alps-release-s0.mp1.tc8sp-cs2-V1 | [gold-s-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/gold-s-oss) |
| shennong-u-oss  | Xiaomi 14, Xiaomi 14 Pro | Android U | KERNEL.PLATFORM.3.0.r1-03200-kernel.0-1 | [shennong-u-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/shennong-u-oss) |
| bsp-manet-u-oss  | Redmi K70Pro | Android U | LA.VENDOR.14.3.0.r1-04800-r1.0.r1_00016.0 | [bsp-manet-u-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/bsp-manet-u-oss) |
| aurora-u-oss | Xiaomi 14 Ultra | Android U | LA.VENDOR.14.3.0.r1-06800-lanai.0-1 | [aurora-u-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/aurora-u-oss) |
| sheng-u-oss | Xiaomi Pad 6S Pro 12.4 | Android U | LA.VENDOR.13.2.0.r1-14800-KAILUA.0-1.36233.3 | [sheng-u-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/sheng-u-oss) |
| blue-u-oss | POCO C61 / Redmi A3 | Android U | alps-mp-s0.mp1.tc8sp-cs3-V1_xiaomi.s0mp1.k65v1.64.bsp.k510_P18 | [blue-u-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/blue-u-oss) |
| peridot-u-oss | Redmi Turbo 3 | Android U | AU_LINUX_ANDROID_LA.VENDOR.14.3.0.R1.00.00.00.000.092 | [peridot-u-oss](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/peridot-u-oss) |



## 感谢

- [AnyKernel3](https://github.com/osm0sis/AnyKernel3)
- [AOSP](https://android.googlesource.com)
- [KernelSU](https://github.com/tiann/KernelSU)
- [xiaoxindada](https://github.com/xiaoxindada)
- [xiaoleGun](https://github.com/xiaoleGun/KernelSU_Action)
- [tomxi1997/LXC_KernelSU_Action](https://github.com/tomxi1997/LXC_KernelSU_Action)
- [qiuqiu](https://blog.qiuqiu233.top/)
- [grilix](https://github.com/grilix/kernel-docker-support)
- [wu17481748](https://github.com/wu17481748/LXC-DOCKER-KernelSU_Action)
- [kdrag0n](https://github.com/kdrag0n/proton-clang)
- [JonasCardoso](https://github.com/JonasCardoso/Toolchain)
- [Neebe3289](https://gitlab.com/Neebe3289/android_prebuilts_clang_host_linux-x86)
- [ZyCromerZ](https://github.com/ZyCromerZ/Clang)
- [ARM](https://developer.arm.com/-/media/Files/downloads/gnu)
- [LLVM](https://github.com/llvm/llvm-project/)
- [mvaisakh](https://github.com/mvaisakh/gcc-build)
- [HDTC](https://gitlab.com/HDTC/sdclang)
- [Neutron-Toolchains](https://github.com/Neutron-Toolchains)
- [kali](https://kali.download/nethunter-images/toolchains/)
- [Mandi-Sa](https://github.com/Mandi-Sa/clang/)
