# 小米路由器 R3

* 系统：X-Wrt
* 版本：8.0-b202008040804

### 1.开启隐藏菜单

```bash
sed -i 's/-hidden//g' /usr/share/luci/menu.d/*.json
```

### 2.改为国内源

```
# /etc/opkg/distfeeds.conf
src/gz x-wrt_core https://mirrors.tuna.tsinghua.edu.cn/openwrt/snapshots/targets/ramips/mt7620/packages
src/gz x-wrt_base https://mirrors.tuna.tsinghua.edu.cn/openwrt/snapshots/packages/mipsel_24kc/base
src/gz x-wrt_luci https://mirrors.tuna.tsinghua.edu.cn/openwrt/snapshots/packages/mipsel_24kc/luci
src/gz x-wrt_packages https://mirrors.tuna.tsinghua.edu.cn/openwrt/snapshots/packages/mipsel_24kc/packages
src/gz x-wrt_routing https://mirrors.tuna.tsinghua.edu.cn/openwrt/snapshots/packages/mipsel_24kc/routing
src/gz x-wrt_telephony https://mirrors.tuna.tsinghua.edu.cn/openwrt/snapshots/packages/mipsel_24kc/telephony
```

### 3.安装证书

```bash
opkg --no-check-certificate update
opkg install ca-certificates --no-check-certificate
```

### 4.安装主题

```bash
opkg install luci-theme-openwrt
opkg install luci-theme-material

wget --no-check-certificate https://github.com/jerrykuku/luci-theme-argon/releases/download/v2.2.1/luci-theme-argon_2.2.1-20200725_all.ipk
opkg install luci-theme-argon_2.2.1-20200725_all.ipk
```

### 5.安装 SFTP

```
opkg install openssh-sftp-server
```

### 6.安装 Clash

版本为 [`clash-linux-mipsle-softfloat-v1.0.0`](https://github.com/Dreamacro/clash/releases/download/v1.0.0/clash-linux-mipsle-softfloat-v1.0.0.gz)

```yaml
# config.yaml
port: 7890
socks-port: 7891
redir-port: 7892
external-controller: 0.0.0.0:9090
allow-lan: true

dns:
  enable: true
  listen: 0.0.0.0:5353
  ...
  fallback-filter:
    geoip: true
```

```bash
scp -r ./root/clash root@192.168.15.1:~
scp -r ./etc root@192.168.15.1:/

chmod +x /root/clash
chmod +x /etc/init.d/clash

/etc/init.d/clash enable
/etc/init.d/clash start
```

设置 DNS 转发地址为 `127.0.0.1#5353`

设置 LAN 口 DNS地址为 `127.0.0.1`

可以使用 [yacd](http://yacd.haishan.me/) 查看 Clash 状态。


