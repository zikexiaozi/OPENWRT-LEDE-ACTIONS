争对xiaomi_mi-router-3-pro优化刷机体验，使用pb-boot直刷openwrt，免除设置Breed环境变量等

原理：

都知道小米R3P路由有两个内核分区kernel1及kernel2

pb-boot只能从kernel1启动

Breed环境变量默认从kernel1启动，但是不能进行后台WEB升级，升级只覆盖kernel2，设置xiaomi.r3g.bootfw=2可以从kernel2启动，但是测试发现Breed引导重启很多次才能成功，不知道什么原因，于是自行编译能让pb-boot只能从kernel1启动固件，进行分区编辑，

文件：

/openwrt/target/linux/ramips/dts/mt7621_xiaomi_mi-router-3-pro.dts

/openwrt/target/linux/ramips/image/mt7621.mk

仅保留kernel1分区启动

有能力的可以自行修改代码进行自定义编辑后编译，我只是按自己的思路进行测试，目前没有问题

https://github.com/zikexiaozi/OPENWRT-LEDE-ACTIONS/

刷机方法一：
1、刷机步骤（自行刷入PB-BOOT，小白误折腾）pb-boot-xiaomi_r3p-20190317-61b6d33.img
2、PB-BOOTIP地址192.168.1.1
3、刷入192.168.123.1-PB-BOOT底包openwrt-ramips-mt7621-xiaomi_mi-router-3-pro-initramfs-kernel.bin
   IP地址192.168.123.1
   后台用户名、密码：root、password(OPENWRT默认)
4、后台WEB升级openwrt-ramips-mt7621-xiaomi_mi-router-3-pro-squashfs-sysupgrade.bin
   IP地址192.168.123.1
   后台用户名、密码：root、password(OPENWRT默认)


刷机方法二：
1、刷机步骤（自行刷入PB-BOOT，小白误折腾）pb-boot-xiaomi_r3p-20190317-61b6d33.img
2、PB-BOOT，IP地址192.168.1.1
3、直接刷入PB-BOOT-factory.bin，无需再进后台进行squashfs-sysupgrade.bin更新
   openwrt-ramips-mt7621-xiaomi_mi-router-3-pro-squashfs-PB-BOOT-factory.bin
   IP地址192.168.123.1
   后台用户名、密码：root、password(OPENWRT默认)


欢迎来到Lean的Openwrt源码仓库！
=
如何下载原版代码编译自己需要的Lean的Openwrt 固件
-
注意：
-
1. **不**要用 **root** 用户进行编译！！！
2. 国内用户编译前最好准备好梯子
3. 默认登陆IP 192.168.1.1 密码 password

编译命令如下:
-
1. 首先装好 Ubuntu 64bit，推荐 Ubuntu 20.04 LTS x64

2. 命令行输入 `sudo apt-get update` ，然后输入
   `
   sudo apt-get -y install build-essential asciidoc binutils bzip2 gawk gettext git libncurses5-dev libz-dev patch python3 python2.7 unzip zlib1g-dev lib32gcc1 libc6-dev-i386 subversion flex uglifyjs git-core gcc-multilib p7zip p7zip-full msmtp libssl-dev texinfo libglib2.0-dev xmlto qemu-utils upx libelf-dev autoconf automake libtool autopoint device-tree-compiler g++-multilib antlr3 gperf wget curl swig rsync
   `

3. 使用 `git clone https://github.com/coolsnowwolf/lede` 命令下载好源代码，然后 `cd lede` 进入目录

4. ```bash
   ./scripts/feeds update -a
   ./scripts/feeds install -a
   make menuconfig
   ```
5. `make -j8 download V=s` 下载dl库（国内请尽量全局科学上网）

6. 输入 `make -j1 V=s` （-j1 后面是线程数。第一次编译推荐用单线程）即可开始编译你要的固件了。

本套代码保证肯定可以编译成功。里面包括了 R21 所有源代码，包括 IPK 的。

你可以自由使用，但源码编译二次发布请注明我的 GitHub 仓库链接。谢谢合作！
=

二次编译：
```bash
cd lede
git pull
./scripts/feeds update -a && ./scripts/feeds install -a
make defconfig
make -j8 download
make -j$(($(nproc) + 1)) V=s
```

如果需要重新配置：
```bash
rm -rf ./tmp && rm -rf .config
make menuconfig
make -j$(($(nproc) + 1)) V=s
```

编译完成后输出路径：bin/targets

不建议使用WSL或WSL2进行编译，容易各种出错。不提供方案。

特别提示：
------
源代码中绝不含任何后门和可以监控或者劫持你的 HTTPS 的闭源软件， SSL 安全是互联网最后的壁垒。安全干净才是固件应该做到的；
