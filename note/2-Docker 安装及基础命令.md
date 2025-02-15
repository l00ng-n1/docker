[Docker: Accelerated Container Application Development](https://www.docker.com/)

# docker 安装

## 包安装

[moby/moby: The Moby Project - a collaborative project for the container ecosystem to assemble container-based systems](https://github.com/moby/moby)

CE（Docker Community Edition，社区版本）和 EE（Docker Enterprise Edition，企业收费版），CE 社区版本和EE企业版本都是每个季度发布一个新版本，但是EE版本提供后期安全维护1年，而CE版本是4 个月

[Install | Docker Docs](https://docs.docker.com/engine/install/)

阿里云安装文档：[docker-ce镜像_docker-ce下载地址_docker-ce安装教程-阿里巴巴开源镜像站](https://developer.aliyun.com/mirror/docker-ce?spm=a2c6h.13651102.0.0.3e221b11guHCWE)

apt 仓库

```shell
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

安装

```shell
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 指定版本
apt install docker-ce=5:20.10.10~3-0~ubuntu-focal docker-ce
cli=5:20.10.10~3-0~ubuntu-focal
```

验证

```shell
sudo docker run hello-world
```

删除 docker

```shell
apt purge docker-ce
rm -rf /var/lib/docker

for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

 内置仓库安装docker

```shell
apt -y install docker.io
```

## 二进制离线安装

[Binaries | Docker Docs](https://docs.docker.com/engine/install/binaries/)

二进制安装下载路径

[Index of linux/static/stable/](https://download.docker.com/linux/static/stable/?_gl=1*4kfoct*_gcl_au*OTE5MDUzODY2LjE3Mzk0OTU0MjI.*_ga*MTU0NTY3NzAzMi4xNzM5NDk1NDIy*_ga_XJWPQMJYHQ*MTczOTUzNDExMC4zLjEuMTczOTUzNTU0Mi41Ny4wLjA.)

[docker-ce-linux-static-stable-x86_64安装包下载_开源镜像站-阿里云](https://mirrors.aliyun.com/docker-ce/linux/static/stable/x86_64/)

```shell
wget https://download.docker.com/linux/static/stable/x86_64/docker-27.5.1.tgz

tar xvf docker-27.5.1.tgz

cp docker/* /usr/local/bin/
```

启动

```shell
dockerd &>/dev/null &

docker version

docker run hello-world
```

服务文件

```shell
cat > /lib/systemd/system/docker.service <<-EOF
[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target firewalld.service
Wants=network-online.target

[Service]
Type=notify
# the default is not to use systemd for cgroups because the delegate issues still
# exists and systemd currently does not support the cgroup feature set required
# for containers run by docker
ExecStart=/usr/local/bin/dockerd -H unix://var/run/docker.sock
ExecReload=/bin/kill -s HUP \$MAINPID
# Having non-zero Limit*s causes performance problems due to accounting overhead
# in the kernel. We recommend using cgroups to do container-local accounting.
LimitNOFILE=infinity
LimitNPROC=infinity
LimitCORE=infinity
# Uncomment TasksMax if your systemd version supports it.
# Only systemd 226 and above support this version.
#TasksMax=infinity
TimeoutStartSec=0
# set delegate yes so that systemd does not reset the cgroups of docker containers
Delegate=yes
# kill only the docker process, not all processes in the cgroup
KillMode=process
# restart the docker process if it exits prematurely
Restart=on-failure
StartLimitBurst=3
StartLimitInterval=60s

[Install]
WantedBy=multi-user.target

root@loong:~# systemctl daemon-reload 
root@loong:~# systemctl start docker.service
```

docker命令补全

```
wget -P /etc/bash_completion.d https://github.com/l00ng-n1/-shell-/blob/main/docker%E5%91%BD%E4%BB%A4%E8%A1%A5%E5%85%A8/docker_completion
```

