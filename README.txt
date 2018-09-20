# HC5661---1.4.11.21001s
极路由1s刷机OP，实现IPTV和网络复用一条网线的研究
极路由1s刷机记录并备份MAC

极路由1s先刷机


刷成OP

1、开启ssh
极路由进智能插件，下载开发者模式这个插件，ok

2、开启putty，进入极路由
192.168.199.1
1022
root
admin

3、备份固件

root@Hiwifi:~# cat /proc/mtd       //命令说明：查看原固件分区信息
dev:    size   erasesize  name
mtd0: 00030000 00010000 "u-boot"
mtd1: 00010000 00010000 "hw_panic"
mtd2: 00010000 00010000 "Factory"
mtd3: 00020000 00010000 "oem"
mtd4: 00010000 00010000 "bdinfo"
mtd5: 00010000 00010000 "backup"
mtd6: 00f70000 00010000 "firmware"
mtd7: 00120000 00010000 "kernel"
mtd8: 00e50000 00010000 "rootfs"
mtd9: 00400000 00010000 "rootfs_data"
root@Hiwifi:~#


root@Hiwifi:~# dd if=/dev/mtd0 of=/tmp/u-boot.bin //命令说明：备份打包mtd0为u-boot.bin文件到tmp目录下
1024+0 records in
1024+0 records out
root@Hiwifi:~# dd if=/dev/mtd2 of=/tmp/Factory.bin  //命令说明：备份打包mtd2为Factory.bin文件到tmp目录下
512+0 records in
512+0 records out
root@Hiwifi:~# dd if=/dev/mtd6 of=/tmp/firmware.bin  //命令说明：备份打包mtd6为firmware.bin文件到tmp目录下
65536+0 records in
65536+0 records out
root@Hiwifi:~#

其它mtd文件也可以全部备份到tmp目录下。



3、用winscp工具把备份的固件下载到本地电脑：
打开winscp
文件协议SCP
主机192.168.199.1
端口1022
用户名root
密码admin
点登陆

点更新，然后把这三个文件拖到电脑，小心保存好

其实这一步也可以用U盘拷贝，先插内存卡到路由上，然后cp命令复制过去。但是有点麻烦。

4、备份MAC地址：

MAC地址规则及示例：

WAN MAC：在LAN的基础上最后一位加1，具体以固件内看到的为准。示例：D4:EE:07:32:84:23
LAN MAC：机器背面的MAC地址即LAN地址，也可以进官方固件查看。示例：D4:EE:07:32:84:22
2.4G MAC：同LAN MAC，也可以用WirelessMon软件查看。示例：D4:EE:07:32:84:22
5G MAC：可以用WirelessMon软件查看，与2.4G MAC区别是第二位不同。示例：D0:EE:07:32:84:22

这个极路由1s没5G

WAN MAC:
LAN MAC:
2.4G MAC:
******************************以上备份完成***************************

5、刷入不死bread，我的已经刷过了，就跳过了。

以下是B70刷pb-boot的过程
以作参考
 ssh登录路由器输入以下红色部分命令。（注意：路由器要联网）
ssh 192.168.199.1 22
root@Hiwifi:/tmp# cd /tmp   //命令说明：进入tmp目录
root@Hiwifi:/tmp# wget http://files.80x86.io/router/rom ... /pb-boot-hc5962.bin //命令说明：用wget命令下载B70 pb-boot，后面网址为pb-boot下载地址，直接复制即可。
Connecting to files.80x86.io(172.104.85.105:80)
pb-boot-hc5962.bin   100%|**************************************************************|   157k 0:00:00 ETA
root@Hiwifi:/tmp# md5sum pb-boot-hc5962.bin   //命令说明：验证pb-boot MD5值，保证文件完整性。
0ebdb3f60b5c407fa82570855c703522  pb-boot-hc5962.bin
root@Hiwifi:/tmp# mtd write pb-boot-hc5962.bin u-boot  //命令说明：用mtd命令写入pb-boot
Unlocking u-boot ...

Writing from pb-boot-hc5962.bin to u-boot...
[e:0] [w0]
[e:1] [w1]
[e:1] [w1]                                                                                                  
root@Hiwifi:/tmp#mtd erase firmware_backup   //命令说明：擦除原厂备份固件。如果不擦backup,重启后会恢复回去。
Unlocking firmware_backup
Erasing firmware_backup


---------------------------网友的极路由1s的刷bread方案-------------------
https://www.3kjs.com/topic/10014.html

1、给极1s刷不死固件Breed
Breed官方主页为https://breed.hackpascal.net/，下载对应的极路由1S的是breed-mt7620-hiwifi-hc5761.bin。

用winSCP上传到tmp下，然后运行putty，运行下面代码：

cd /tmp

mtd write breed-mt7620-hiwifi-hc5761.bin u-boot

或者直接putty，运行：

cd /tmp

wget http://breed.hackpascal.net/breed-mt7620-hiwifi-hc5761.bin

mtd write breed-mt7620-hiwifi-hc5761.bin u-boot

等待大约1秒钟左右，写入完成。

2、下载对应的padavan固件
极壹S（HC5661）对应的固件是RT-N14U-GPIO-12-xiaodu-ji1S-128M

最新固件下载地址：http://opt.cn2qq.com/padavan/

3、进入breed后台备份原厂key
路由器通过网线连接电脑，断开路由器电源，按住路由器reset键并通电，直到跑马灯快速闪烁4下松开reset键，浏览器访问 http://192.168.1.1 进入breed后台。

极路由和其他路由不同，在Flash中保存了无线的天线修正数据，如果你在breed中恢复出厂设置，会导致你的无线信号变得超差。同时原厂固件使用key认证进入云插件，丢失key将不能再进入云插件。为了避免这种情况的发生，第一件事情就是在breed界面备份编程器固件，备份KEY，以备随时可以恢复。


路由器后台登陆地址是192.168.123.1，默认用户名admin，密码admin。
---------------------------------------------------------------------

6、下载想刷的固件，我想刷OP

http://www.openwrt.org.cn/

进下载中心http://downloads.openwrt.org.cn/


这个是原版
https://openwrt.org/

这是18.06.1
http://downloads.openwrt.org/releases/18.06.1/targets/

ramips/
http://downloads.openwrt.org/releases/18.06.1/targets/ramips/mt7620/
hc5661-squashfs-sysupgrade.bin

下载好了，开刷，断电状态下，按住reset按键，开机，然后10秒后松手，

电脑本地连接，设置静态ip
192.168.1.11
255.255.255.0

打开浏览器192.168.1.1

CPU	MediaTek MT7620A ver 2, eco 6
内存	128MB DDR2
Flash	Winbond W25Q128 @ 24MHz (16MB)
以太网	MediaTek MT7620A built-in 5-port 10/100M switch
时钟频率	CPU: 580MHz, Bus: 193MHz
编译日期	2018-03-23 [git-994feec]
版本	1.1 (r1211)

点固件更新，浏览选中新下载的固件，点确定，点更新。等待读条，这时把电脑本地连接的IP重新设置为动态获取。

第一次开机比较慢，耐心等待

输入192.168.1.1

进到LUCI界面，输入admin进入
先设置密码admin
ssh的interface 设置lan，点确定

英文看着太难受，果断改中文

http://192.168.1.1/cgi-bin/luci/admin/system/packages
点更新列表update lists

点L，往下拉，找到中文的那个包

luci-i18n-base-zh-cn

还有其他的zh-cn都是一些单独的应用的中文界面

比如openvpn你安装基础包后显示的还是英文，那么你安装luci-i18n-openvpn-zh-cn
就能看中文了

******************

刚才已经开启ssh了，直接用putty进去

竟然不需要输密码。。。。

login as: root


BusyBox v1.28.3 () built-in shell (ash)

  _______                     ________        __
 |       |.-----.-----.-----.|  |  |  |.----.|  |_
 |   -   ||  _  |  -__|     ||  |  |  ||   _||   _|
 |_______||   __|_____|__|__||________||__|  |____|
          |__| W I R E L E S S   F R E E D O M
 -----------------------------------------------------
 OpenWrt 18.06.1, r7258-5eb055306f
 -----------------------------------------------------
=== WARNING! =====================================
There is no root password defined on this device!
Use the "passwd" command to set up a new password
in order to prevent unauthorized SSH logins.
--------------------------------------------------
root@OpenWrt:~# cd /
root@OpenWrt:/# ls
bin      etc      mnt      proc     root     sys      usr      www
dev      lib      overlay  rom      sbin     tmp      var
root@OpenWrt:/# cd /etc
root@OpenWrt:/etc# cd /config
-ash: cd: can't cd to /config: No such file or directory
root@OpenWrt:/etc# cd config
root@OpenWrt:/etc/config# ls
adblock   dropbear  luci      qos       sqm       ucitrack  wireless
dhcp      firewall  network   rpcd      system    uhttpd
root@OpenWrt:/etc/config# cat network

config interface 'loopback'
        option ifname 'lo'
        option proto 'static'
        option ipaddr '127.0.0.1'
        option netmask '255.0.0.0'

config globals 'globals'
        option ula_prefix 'fd9f:243c:eee2::/48'

config interface 'lan'
        option type 'bridge'
        option ifname 'eth0.1'
        option proto 'static'
        option ipaddr '192.168.1.1'
        option netmask '255.255.255.0'
        option ip6assign '60'

config device 'lan_dev'
        option name 'eth0.1'
        option macaddr 'd4:ee:07:10:21:a8'

config interface 'wan'
        option ifname 'eth0.2'
        option proto 'dhcp'

config device 'wan_dev'
        option name 'eth0.2'
        option macaddr 'd4:ee:07:10:21:a9'

config interface 'wan6'
        option ifname 'eth0.2'
        option proto 'dhcpv6'

config switch
        option name 'switch0'
        option reset '1'
        option enable_vlan '1'

config switch_vlan
        option device 'switch0'
        option vlan '1'
        option ports '1 2 3 4 5 6t'

config switch_vlan
        option device 'switch0'
        option vlan '2'
        option ports '0 6t'

root@OpenWrt:/etc/config# ^C
root@OpenWrt:/etc/config#

**********************对应网络，里面的交换机选项得出如下结论，一一对应关系************************
VLAN ID	CPU (eth0)	LAN 1	LAN 2	LAN 3	LAN 4	LAN 5	WAN
         6              1        2       3        4      5      0

1        6              1        2       3        4      5

2        6                                                      0

*******************************通过luci界面改的*****************************


config switch_vlan
        option device 'switch0'
        option vlan '1'
        option vid '1'
        option ports '1 2 5 6t'

config switch_vlan
        option device 'switch0'
        option vlan '2'
        option ports '0 6t'
        option vid '2'

config switch_vlan
        option device 'switch0'
        option vlan '3'
        option vid '3'
        option ports '3 4 6t'

http://koolshare.cn/thread-120569-1-1.html
看完这种设置，不懂

http://www.right.com.cn/forum/forum.php?mod=viewthread&tid=267973&ordertype=1
这个时河北的用户，他通过腾达的AC9搞定了，但是他用的中兴光猫，可以把两个VLAN都绑定到LAN1口

-------------------------------------------------------------------

http://koolshare.cn/thread-118663-1-9.html
根据这个帖子，搞定了，。


config switch_vlan
        option device 'switch0'
        option vlan '1'
        option vid '1'
        option ports '1 2 5 6t'

config switch_vlan
        option device 'switch0'
        option vlan '2'
        option ports '0 6t'
        option vid '2'

config switch_vlan
        option device 'switch0'
        option vlan '3'
        option vid '3'
        option ports '0t 3 4'


原理就是，因为vlan99和vlan1024不能同时绑定LAN1口，所以只能用用户侧vlan来同时绑定LAN1口。


因为我的互联网采用的时路由模式，光猫网口1直接就是网，所以不用设置用户侧VLAN，只有用桥接模式时才需要用用户侧VLAN，

所以我只需要给vlan99建立一个用户侧vlan就行了，让iptv的信号也能从LAN1出去。

光猫上只需要在VLAN绑定里面做如下绑定就ok了。

端口   千兆口

用户侧vlan 3

wan侧vlan 3_OTHER_vlan99



而路由器里面，把vlan3设置为3口和4口，加上wan口，就ok了
vlan3：'0t 3 4'



ok

现在网能正常用，电视也能正常看，点播直播都没问题，4K也没问题。


但是一会儿我可能要设置qos，不知道电视会不会受影响

普通qos 设置下行90000Kbit/s
上行9000Kbit/s

不受影响，不过电视重启一次

*********************************************发现严重问题*****************************************

因为这个路由器时百兆端口，实际能走的带宽就90M，而我家带宽是200M，所以，一满速下载就会出问题，4K直接就卡住了

而且即使不满速下载电视在10分钟左右会重启一次，不是那种断电重启，是黑屏然后回到首页

直播没事1：22到1：45电视没问题




***************************************************************************************************

好消息是，全速下载不会对直播有影响


qos极限限速，上下行全部是9Kbit/s的话，对电视无影响

解决方案是对lan口限速就避免了限速wan口导致一到极速就会导致电视卡的现象。

********凑合的解决方案，因为每次4K片头1分钟的时候还是会卡6秒钟（最后知道那个是片源卡，我说怎么每次都是那个点卡）****************


//把vlan3里加进cpu 也就是6t

//然后再接口里面新建接口：dianshi
//协议为不配置
//编辑，物理端口--选择eth0.3---保存

//这样限速的时候选eth0.1就好了，因为这个接口里没有3，4口


//*********************************我笨死了，eth0.1里面本来就没有3，4口啊。。。****************************

//不用改vlan3了

目前是这样的，其中dianshi那个是多余的
root@OpenWrt:/etc/config# cat network

config interface 'loopback'
        option ifname 'lo'
        option proto 'static'
        option ipaddr '127.0.0.1'
        option netmask '255.0.0.0'

config globals 'globals'
        option ula_prefix 'fd9f:243c:eee2::/48'

config interface 'lan'
        option type 'bridge'
        option ifname 'eth0.1'
        option proto 'static'
        option ipaddr '192.168.1.1'
        option netmask '255.255.255.0'
        option ip6assign '60'

config device 'lan_dev'
        option name 'eth0.1'
        option macaddr 'd4:ee:07:10:21:a8'

config interface 'wan'
        option ifname 'eth0.2'
        option proto 'dhcp'

config device 'wan_dev'
        option name 'eth0.2'
        option macaddr 'd4:ee:07:10:21:a9'

config interface 'wan6'
        option ifname 'eth0.2'
        option proto 'dhcpv6'

config switch
        option name 'switch0'
        option reset '1'
        option enable_vlan '1'

config switch_vlan
        option device 'switch0'
        option vlan '1'
        option vid '1'
        option ports '1 2 5 6t'

config switch_vlan
        option device 'switch0'
        option vlan '2'
        option ports '0 6t'
        option vid '2'

config switch_vlan
        option device 'switch0'
        option vlan '3'
        option vid '3'
        option ports '0t 3 4 6t'//这个有3 4 6t就行，0t是为了加eth0.3接口来着

config interface 'dianshi'//这个是多余的
        option proto 'none'
        option ifname 'eth0.3'


sqm里面接口选lan，也就是eth0.1，设置下行20000，上行60000.其他不用动。
