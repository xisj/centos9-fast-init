# centos9-fast-init
最小化安装centos9 之后， 快速初始化环境， 使新机器最短时间可以上线作为开发机/生产机使用

## 一键安装

```
dnf install -y epel-release  dnf-plugins-core
dnf install -y wget git vim lrzsz screen net-tools telnet iftop bind-utils tar psmisc  &&\
dnf install -y yum-utils  device-mapper-persistent-data  lvm2 &&\
dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo &&\
dnf install -y docker-ce docker-ce-cli &&\

wget "https://raw.githubusercontent.com/xisj/centos9-fast-init/master/vps/fast.sh" &&\
screen sh fast.sh 

```

## 一键安装（中国大陆环境）

## 一键安装

```
dnf install -y epel-release  dnf-plugins-core
dnf install -y wget git vim lrzsz screen net-tools telnet iftop bind-utils tar psmisc  &&\
dnf install -y yum-utils  device-mapper-persistent-data  lvm2 &&\
dnf config-manager --add-repo=https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo &&\
dnf install -y docker-ce docker-ce-cli &&\

wget "https://raw.githubusercontent.com/xisj/centos9-fast-init/master/vps/fast.sh" &&\
screen sh fast.sh 

```

### 缓解sshd长时间连接后自动断开的问题
```
ClientAliveInterval 60
ClientAliveCountMax 60
```
```
vim /etc/ssh/sshd_config
systemctl restart sshd
```

### 更新时区
```
timedatectl set-timezone "Asia/Shanghai"

```

### docker内部更新时区
```
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime 
```


### 手动给ssh开放更多端口
```
dnf install -y policycoreutils-python-utils
semanage port -m -t ssh_port_t -p tcp 110
```


### 打开防火墙端口
```
firewall-cmd --zone=public --add-port=80/tcp --permanent  
firewall-cmd --zone=public --add-port=80/udp --permanent

firewall-cmd --zone=public --add-port=443/tcp --permanent  
firewall-cmd --zone=public --add-port=443/udp --permanent  

firewall-cmd --zone=public --add-port=9999/tcp --permanent  
firewall-cmd --zone=public --add-port=9999/udp --permanent  

firewall-cmd --zone=public --add-port=500/udp --permanent  
firewall-cmd --zone=public --add-port=4500/udp --permanent  

firewall-cmd --reload
firewall-cmd --zone=public --list-ports
```

### 启用bbr
> vi /etc/sysctl.conf
```
net.core.default_qdisc=fq
net.ipv4.tcp_congestion_control=bbr
```

```
sysctl -p
reboot
sysctl net.ipv4.tcp_congestion_control
```

### 安装fail2ban
```
yum install epel-release
yum install fail2ban
cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```
> vim  /etc/fail2ban/jail.local  ,修改如下内容：
```
[sshd]
enabled = true
port = ssh
filter = sshd
maxretry = 5
```
> 启动并检查状态
```
systemctl start fail2ban
systemctl enable fail2ban
fail2ban-client status
```
> fail2ban-client status执行后可以看到被ban了多少ip之类的信息


### 申请https 证书
```
yum install certbot
```
> 第一次申请证书
```
certbot certonly --config-dir /data1/privkey/config --work-dir /data1/privkey/work --logs-dir /data1/privkey/logs
```
> 每2个月自动更新证书
> 
> crontab -e
```
0 0 1 */2 * certbot renew --quiet
```

> 对已有nginx配置进行修改
> 
>  cat www.zhuikan.com.conf 
```

server {
    listen 80;
    server_name www.zhuikan.com zhuikan.com;
    return 301 https://$host$request_uri;
}


server
    {

         listen 443 ssl;
    server_name www.zhuikan.com zhuikan.com;

    ssl_certificate /privkey/config/live/zhuikan.com/fullchain.pem;
    ssl_certificate_key /privkey/config/live/zhuikan.com/privkey.pem;


        index index.html index.htm index.php default.html default.htm default.php;
        root  /htdocs/www.zhuikan.com;

        include rewrite/none.conf;
        #error_page   404   /404.html;

        # Deny access to PHP files in specific directory
        #location ~ /(wp-content|uploads|wp-includes|images)/.*\.php$ { deny all; }

        include enable-php.conf;


        location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$
        {
            expires      30d;
        }

        location ~ .*\.(js|css)?$
        {
            expires      12h;
        }

        location ~ /.well-known {
            allow all;
        }

        location ~ /\.
        {
            deny all;
        }

        access_log  /logs/www.zhuikan.com.log;
    }
```





 
