# docker setting

쿠버네티스 클러스트를 구성할 모든 노드에 도커 설치 및 설정 필요

## docker install
- yum dnf 
```shell
$ dnf install -y dnf-utils

# add repo
$ dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

# system repo update
$ dnf update -y

# New repo list
$ dnf repolist -v

# install docker
# try to add if you
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
- yum install 
```
# yum install -y yum-utils
# yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
# vi /etc/yum.repos.d/docker-ce.repo
```
docker-ce.repo 파일에 아래 내용 추가 후 저장 
```YAML
[centos-extras]
name=Centos extras - $basearch
baseurl=http://mirror.centos.org/centos/7/extras/x86_64
enabled=1
gpgcheck=1
gpgkey=http://centos.org/keys/RPM-GPG-KEY-CentOS-7
```
```
# yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

## docker deamon

- 도커 위치 : /usr/bin/docker
  - 컨테이너 이미지를 다루는 명령어를 실행하는 도커 
  - 도커 명령어가 실제 도커 엔진이 아닌 클라이언트로서의 도구
- 실제 도커 엔진 프로세스 실행 파일 위치 : /usr/bin/dockerd
  - 도커 엔진 프로세스

### Client Docker vs Server Docker
- Client Docker : API를 사용 가능하도록 CLI를 제공
  - Docker deamon은 API를 받아 Docker Engin의 기능 수행
- Server Docker
  - 컨테이너를 생성 및 실행 / 이미지 관리 
  - dockerd processfh ehdwkr
  - Docker engin은 외부에서 API입력을 받아 도커 엔지의 기능 수행
  - Docker process가 실행되어 서버로서 입력을 받을 준비가 된 상태가 docker deamon
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

