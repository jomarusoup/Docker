# docker setting

쿠버네티스 클러스트를 구성할 모든 노드에 도커 설치 및 설정 필요

## docker install

```shell
$ dnf install -y dnf-utils

# add repo
$ dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

# system repo update
$ dnf update -y

# New repo list
$ dnf repolist -v

# install docker
# try to add 
# '--allowerasing' to command line to replace conflicting packages 
# '--skip-broken' to skip uninstallable packages 
# '--nobest' to use not only best candidate packages
$ dnf install docker-ce docker-ce-cli containerd.io

# docker start & reboot auto start
$ systemctl start docker
$ systemctl enable docker

# docker version check
$ docker version
```

## docker deamon

```shell
$ vi /etc/docker/daemon.json

{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "99m"
  },
  "storage-driver": "overlay1",
  # 이미지를 저장할 레지스트리 컨테이너가 위치한 IP
  "insecure-registries": ["Registry_Host_IP:Port"]
}

# deamon reload
$ sudo systemctl daemon-reload

# docker restart
$ sudo systemctl restart docker
```

## 일반 사용자 계정으로 Docker 실행 설정

docker 그룹을 생성하고 사용자 계정을 추가. 그룹 설정에 sudo 권한이 필요

```shell
$ usermod -aG docker $USER
$ su - $USER
$ groups $USER
```

