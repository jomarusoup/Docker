# docker seting

쿠버네티스 클러스트를 구성할 모든 노드에 도커 설치 및 설정 필요

## docker install

```shell
$ yum install -y yum-utils

# add repo
$ yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo

# install docker
$ yum install docker-ce docker-ce-cli containerd.io

# try to add 
# '--allowerasing' to command line to replace conflicting packages 
# '--skip-broken' to skip uninstallable packages 
# '--nobest' to use not only best candidate packages

# docker start & reboot auto start
$ systemctl start docker
$ systmectl enable docker

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
  "insecure-registries": ["Registry_Host_IP:Port", "localhost:5000"]
}

# deamon reload
$ sudo systemctl daemon-reload

# docker restart
$ sudo systemctl restart docker
```

## 일반 사용자 계정으로 Docker 실행 설정

docker 그룹을 생성하고 사용자 계정을 추가. 그룹 설정에 sudo 권한이 필요

```shell
$ sudo usermod -aG docker $USER
$ sudo su - $USER
$ groups $USER
```

## `dockerfile` 작성

```dockerfile
# BASE IMAGE : FROM
FROM scratch

# IMAGE INFO : LABEL, MAINTAINER
LABEL "파일명" = "Filename"
LABEL "설명"   = "explain"
LABEL "작성자" = "Name"

# IMAGE BUILD : WORKDIR, ENV, COPY, ADD, RUN, EXPOSE, VOLUME, ARG, USER, ...
#--- [작업경로]
WORKDIR 

#--- [환경설정]
ENV 

#--- [HOST to CONTAINER]
COPY 

# CONTAINER RUN : ENTRYPOINT, CMD
ENTRYPOINT ["/bin/bash", "-c", "tail -f /dev/null"]

# 선택
# 컨테이너가 생성된 후 실행될 명령어 (단 ENTRYPOINT와 다르게 docker run에서 명령어를 변견할 수 있다.)
CMD ["-c", "tail -f /dev/null"]

```

### docker run 후 바로 exit되는 경우 Dockerfile에 추가

```shell
tail -f /dev/null
```

- null device(/dev/null) 라고 불리는 리눅스 장치 파일을 계속 읽음으로써 컨테이너 작업을 유지시키는 방식

## 이미지 빌드

이미지 빌드시 /home/xt_kb_linux에서 명령어 실행

```shell
$ docker build -t {Images_Name:Tag} -f {Dockerfile_Dir} .
```

- `-t`: 이미지의 이름:TAG 설정, TAG를 주지 않으면 latest로 생성
- `-f`: Dockerfile의 절대경로

## #해당 컨테이너 bash로 접속

## 0-6. 컨테이너의 내용을 이미지로 옮기는 명령어

|    명령어    | Container ID | Image Name:TAG |
| :-----------: | :----------: | :------------: |
| docker commit | 64d60d3dd306 |   ubuntu:git   |

```shell
$ docker commit 64d60d3dd306 ubuntu:git
```
