https://www.right.com.cn/forum/forum.php?mod=viewthread&tid=7085123 

## 关于本固件
glinet背靠openwrt刷机没设门槛，看着mt1300挺迷你还可以c口供电，眼缘买之。不过似乎论坛没什么人玩，那就分享一下自用的固件出来给有折腾需求的朋友好了^o^
这个固件修改自Openwrt官方仓库最新的稳定版21.02，仅添加了改善网络质量的必要第三方插件和与之搭配的patch，涵盖了差旅路由、家庭组网的基本需求，非常适合原生强迫症用户使用。  
我在原版基础上做出的任何修改，以及编译的全过程都可以在github 看到。无论是监督检查代码安全性，还是单纯不喜欢我的搭配思路想照着自己改一个原生的出来都非常欢迎！

## 对产品定位和用途的一己之见
作为一款21年的mt7621，本来就不强的性能加上新内核上不再支持模拟FPU，低端mips失去了docker之类的可玩性。此外射频芯片就一颗7615当爹又当妈地包办了2.4GHz和5GHz，让这台路由器彻底沦为玩具。
对于已经转移到华硕梅林阵营的我来说，只希望带着它出差的时候能够让我的NS能顺畅联机，同时充当一台轻度NAS在手机电脑之间倒倒数据就足够了。这也是本固件的功能集成偏好

## 需自行启动 Action 构建
### master 分支，mt76 开源驱动
在20211126版的基础上：  
- mt76开源无线驱动，集成natflow内核模块，使无线转发效率接近硬件NAT的水平
- 集成Easymesh图形界面，支持有线回程mesh组网
- 去掉USB接口读取U盘的功能（与natflow冲突），追加外接4G/LTE模块功能
- SQM QOS流控，对大型局域网的流量管理十分必要
- 支持全锥形NAT（Fullcone）、BBR拥塞算法、MWAN多播、有线硬件转发，可提高在线游戏体验
- 使用Nginx代替httpd作为luci图形界面的web服务。对普通用户体验无影响，方便内网反代的进阶玩家配置（可配合DDNS使用）
- 对支持远程启动的设备，提供Wake on LAN功能

注：  
2. 默认的登录账号为root，密码为toor
3. 为避免不必要的错误，请通过uboot或breed全新安装固件而非通过升级口径安装

## 常见问题
1. wifi驱动没有hwnat  
即便是闭源驱动，在5.x内核上都是目前整个社区都在尝试解决的问题。这个固件通过移植了x-wrt上的natflow在开源驱动下提供了接近的性能。
2. mt76配置无线出现错误
目前该驱动对DBDC的支持还不完善，部分参数需要用户通过ssh手动编辑/etc/config/wireless配置文件来实现
3. 何时更新
制作方针是“关键功能集成的前提下尽可能小巧”，因此只有当非常需要预装带来新特性的内容时才会更新。路由器是不能掉链子的网络设备，折腾要讲究一步到位

# match_complier
>Custom OpenWrt complier for GL-MT1300  

## Why this variant
This repo using github actions to complile firmware for my GL.iNet Beryl(GL-MT1300) , which is minimum-obtrusive to the latest stable version of OpenWrt but only optimize for console game accelerator use. It's a reasonable product positioning for a low-end(mt7621) router.  
With that said it's fine to use it complile for other routers in [support list](https://openwrt.org/toh/start).  

## Main features
* Integrated with wlan offload optimization and fullcone-NAT support to improve connection quality
* Multiple WAN function allows you to increase the net speed exponentially  
* Build your own global transit between the router and private server. Xray is recommended due to its [ low CPU cost](https://github.com/v2ray/v2ray-core/issues/2636) and [UDP support](https://github.com/XTLS/Xray-core/discussions/252) as claimed  
* Fully support LTE modules on USB port  
* Using nginx to provide luci and other web page over https. Remote access also available  
* HomeAssistant [intergration](https://www.home-assistant.io/integrations/luci/) compatible

## Usage
1. Generate `.config` files with [stock repository](https://github.com/openwrt/openwrt/tree/openwrt-21.02) source code and overwrite it if you don't buy our default package set. You can also change the source code through environment variables in the workflow file  
2. Add 3rd-part packages to [this branch](https://github.com/Vector-Digi/match_complier/tree/packages) whatever you want  
3. Modify `prefix.sh` and `postfix.sh` to apply custom settings  
4. Select `Build OpenWrt` on the Actions page, then lick the `Run workflow` button  
5. When the build is complete, click the `Artifacts` button in the upper right corner of the Actions page to download the binaries  
6. [Flash](https://git.openwrt.org/?p=openwrt/openwrt.git;a=commit;h=d89a7f0120db42d4fae1656e1860ef49523acabb) and enjoy!  
p.s. Use **toor** as the default password  
