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
 
