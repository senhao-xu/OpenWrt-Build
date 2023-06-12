# OpenWrt-Build

My OpenWrt-Build Config

## 编译

###  要求

1. 基于Ubuntu  LTS
2. 不使用root进行编译
3. 硬盘80G
4. cpu核数越多编译越快
5. 全程需科学网络
6. 默认登陆IP 192.168.1.1 密码 password

### 编译

1. 安装编译依赖

   ```bash
   # 更新本地软件
   sudo apt update -y
   sudo apt full-upgrade -y
   # 安装依赖
   sudo apt install -y ack antlr3 aria2 asciidoc autoconf automake autopoint binutils bison build-essential \
   bzip2 ccache cmake cpio curl device-tree-compiler fastjar flex gawk gettext gcc-multilib g++-multilib \
   git gperf haveged help2man intltool libc6-dev-i386 libelf-dev libglib2.0-dev libgmp3-dev libltdl-dev \
   libmpc-dev libmpfr-dev libncurses5-dev libncursesw5-dev libreadline-dev libssl-dev libtool lrzsz \
   mkisofs msmtp nano ninja-build p7zip p7zip-full patch pkgconf python2.7 python3 python3-pip libpython3-dev qemu-utils \
   rsync scons squashfs-tools subversion swig texinfo uglifyjs upx-ucl unzip vim wget xmlto xxd zlib1g-dev
   ```

2. 下载源代码，更新 feeds 并选择配置

   ```bash
   # 下载源码
   git clone https://github.com/coolsnowwolf/lede
   # 进入lede
   cd lede
   # 添加自定义软件源
   cat >> feeds.conf.default <<EOF
   src-git kenzo https://github.com/kenzok8/openwrt-packages
   src-git small https://github.com/kenzok8/small
   EOF
   # 更新 feeds 并选择配置
   ./scripts/feeds update -a && ./scripts/feeds install -a
   ```

3. 定制配置（根据需求指定）

   ```bash
   # 更换默认IP
   sed -i 's/192.168.1.1/10.0.0.1/g' package/base-files/files/bin/config_generate
   # 交换 LAN/WAN 口 (一般用于R2S)
   sed -i 's,"eth1" "eth0","eth0" "eth1",g' target/linux/rockchip/armv8/base-files/etc/board.d/02_network
   sed -i "s,'eth1' 'eth0','eth0' 'eth1',g" target/linux/rockchip/armv8/base-files/etc/board.d/02_network
   #取消登录密码
   sed -i 's/$1$V4UetPzk$CYXluq4wUazHjmCDBCqXF.//g' openwrt/package/lean/default-settings/files/zzz-default-settings
   ```

4. 插件选择

   ```bash
   # 进行插件选择 (可使用config下文件)
   make menuconfig
   # 下载编译所需软件
   make download -j8 V=s
   ```

5. 编译 （二选一）

   ```bash
   # 单线程编译
   make -j1 V=s
   # 多线程编译
   make -j$(($(nproc) + 1)) V=s
   ```

编译完成后固件地址相对路径：`/bin/targets/`

### 其他

安装完成后显示CPUMark分数

```bash
/etc/coremark.sh && cat /etc/bench.log
```

### 二次编译
```bash
rm -rf ./tmp && rm -rf .config
```

