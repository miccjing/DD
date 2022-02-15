## DD Debian10

```
wget https://raw.githubusercontent.com/miccjing/DD/main/InstallNET.sh && \

bash InstallNET.sh -d 11 -v 64 -a
```

密码
```
MoeClub.org
```

安装常用工具添加 backports 源
```
apt update && \
apt install screen curl vim wget sudo unzip lsb-release -y && \
echo "deb http://deb.debian.org/debian $(lsb_release -sc)-backports main" | sudo tee /etc/apt/sources.list.d/backports.list && \
apt update
```

修改时区
```
timedatectl set-timezone Asia/Singapore
```

更新内核
```
apt -t $(lsb_release -sc)-backports install linux-image-$(dpkg --print-architecture) linux-headers-$(dpkg --print-architecture) --install-recommends -y
```

启用密钥认证
```
sed -i 's/#PubkeyAuthentication yes/PubkeyAuthentication yes/g' /etc/ssh/sshd_config && \
sed -i 's/Port 22/Port 2222/g' /etc/ssh/sshd_config && \
mkdir -p ~/.ssh && \
wget -P ~/.ssh https://cdn.jsdelivr.net/gh/miccjing/DD/authorized_keys && \
systemctl restart sshd.service
```

关闭密码登录
```
sed -i 's/PasswordAuthentication yes/PasswordAuthentication no/g' /etc/ssh/sshd_config && \
systemctl restart sshd.service
```

安装xray
```
bash -c "$(curl -L https://github.com/XTLS/Xray-install/raw/main/install-release.sh)" @ install -u root && \

systemctl enable xray
```

配置xray
```
vim /usr/local/etc/xray/config.json
```

```
reboot
```

安装wireguard
```
apt install net-tools iproute2 openresolv dnsutils -y && \
apt install wireguard-tools --no-install-recommends
```

wireguard 绕过IP
```
PostUp = ip rule add from ipv4 lookup main
PostDown = ip rule delete from ipv4 lookup main
PostUp = ip -6 rule add from ipv6 lookup main
PostDown = ip -6 rule delete from ipv6 lookup main
```

配置wireguard
```
curl -fsSL git.io/wgcf.sh | sudo bash
wgcf register
WGCF_LICENSE_KEY="" wgcf update
wgcf generate
sed -i 's/DNS = 1.1.1.1/DNS = 8.8.8.8,2001:4860:4860::8888/g' wgcf-profile.conf
vim wgcf-profile.conf
cp wgcf-profile.conf /etc/wireguard/wgcf.conf
wg-quick up wgcf
curl -6 ip.p3terx.com
curl -4 ip.p3terx.com
wg-quick down wgcf
systemctl start wg-quick@wgcf
systemctl enable wg-quick@wgcf
curl ip.p3terx.com
```

开启bbr fq pie
```
echo "net.core.default_qdisc=fq_pie" >> /etc/sysctl.d/99-sysctl.conf && \
echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.d/99-sysctl.conf && \
sysctl --system
```

```
reboot
```
