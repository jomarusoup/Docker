# Docker Swarm Service
- Service : Docker Swarm 제어 단위
- Service는 컨테이너의 집합 단위
- Service 제어 시 Service에 존재하는 모든 Container에 적용
- Service 내부에 1개 이상의 컨태이너 존재 가능
- 각 Container들은 Manager & Worker Node에 할당
- 할당 된 Container(Services 내부의 Container) = Task

- Swarm Scheduler : Service의 정의에 따라 컨테이너를 Node에 할당
  - 반드시 각 Node에 하나씩 할당 되지는 않음
- Container replica : Service의 정의에 따라 동시에 생성된 Contianer 개수
  - 상황에 따라 특정 Node에 Container가 정지되면 생성 가능한 노드에 Conatainer를 생성해 replica 수 유지
- Node가 다운되면 Manager는 사용 가능한 다른 노드에 같은 컨테이너를 생성
- Rolling Update : Service 내부의 Container의 이미지를 일괄적으로 업데이트 가능
  - 이미지를 순서대로 변경해 서비스가 전체가 정지되지 않고 컨테이너의 업데이트 진행 가능

- Service를 제어하는 모든 docker command는 Manager Node에서만 사용 가능

## Service Create
- `-d`가 사용 가능한 이미지만 사용
```shell
$ docker service create {Image:Tag}

# service list
$ docker service ls

# service info
$ docker service ps {Service_Name}

# delete service
$ docker service rm {Service_Name}

# the number of replicas
$ docker service scale {Service_Name}={Number}

# service ps
$ docker service ps {Service_Name}
```

```shell
# Private registry
$ docker service create --with-registry-auth \
> --name {Service_Name} \
> --replicass {Number} \
> {Image:Tag}
```
- Private registry에서 이미지를 받아오는 경우 `--with-registry-auth` 옵션 추가
- Worker Node에서 별도로 로그인을 하지 않아도 이미지를 받기 가능
- Manager Node의 Local Docker engin에 이미지가 없더라도 Private registry에서 이미지를 찾아서 Service create

## Service mode
- Replicated mode : 복제 모드, 실제 서비스를 제공하기 위한 일반 모드
  - 레플리카셋의 개수를 정의해 개수 만큼의 같은 컨테이너를 생성하는 모드
- Global mode : 클러스터 내에서 사용 가능한 모든 노드에 컨테이너를 반드시 하나씩 생성하는 모드
  - 레플리카셋을 별도로 지정 X
  - Docker swarm을 모니터리하기 위한 에이전트 컨테이너등을 생성해야 할때 유용
  - `--mode` 옵션을 주지 않으면 replicated mode를 사용
```shell
# Global mode
$ docker service create --mode global --name {Service_Name} {Image:Tag}

# Service container list
$ docker ps --filter is-task=true --format {{.Names}}
```

## Service Recovery from Failure
- replicated mode로 설정된 service의 컨테이너 혹은 Node의 문제가 생기면 Manager는 새로운 Conatiner를 자동으로 생성
```shell
# Service Task list
$ docker service ps {Service_Name}
ID              NAME          IMAGE                                  NODE        DESIRED STATE   CURRENT STATE            ERROR     PORTS
vdifjwicnf9tp   conatiner.1   {Registry_Host_IP}:5000/hello:latest   Manager     Running         Running 13 minutes ago             
iw39tkdvkd2v3   container.2   {Registry_Host_IP}:5000/hello:latest   Worker_2    Running         Running 12 minutes ago             
4103jfm2kldc0   container.3   {Registry_Host_IP}:5000/hello:latest   Worker_1    Running         Running 12 minutes ago             
```

```shell
# Manager Node Container list
$ docker ps
CONTAINER ID   IMAGE                                  COMMAND                   CREATED          STATUS          PORTS                                       NAMES
f2783a268e76   192.168.0.104:5000/hello_test:latest   "/bin/sh -c 'while t??   14 minutes ago   Up 14 minutes                                               container.1.vdifjwicnf9tp
853aidh22094   registry                               "/entrypoint.sh /etc??   6 hours ago      Up 39 minutes   0.0.0.0:5000->5000/tcp, :::5000->5000/tcp   registry
```

```shell
# Manager Node Task delete
$ docker rm -f container.1.vdifjwicnf9tp
container.1.vdifjwicnf9tp
```

```shell
# Service Recovery
$ docker service ps blissful_kirch
ID             NAME              IMAGE                                  NODE        DESIRED STATE   CURRENT STATE            ERROR                         PORTS
38xdze58e2ws   container.1       {Registry_Host_IP}:5000/hello:latest   Worker_3    Running         Running 3 seconds ago                                  
vdifjwicnf9tp   \_ container.1   {Registry_Host_IP}:5000/hello:latest   Manager     Shutdown        Failed 8 seconds ago     "task: non-zero exit (137)"   
iw39tkdvkd2v3  container.2       {Registry_Host_IP}:5000/hello:latest   Worker_2    Running         Running 13 minutes ago                                 
4103jfm2kldc0  container.3       {Registry_Host_IP}:5000/hello:latest   Worker_1    Running         Running 13 minutes ago                                 
```

```shell
# Random Node Down
$ systmectl stop docker

# docker node list
$ docker node ls
ID                  HOSTNAME      STATUS     AVAILABILITY    MANAGER           STATUS
29ehkjo83bh3o8ab    Worker_1      Ready      Actice
abvjk391fnkj391m    Worker_2      Down       Actice
mdn093bgasdfhi29    Worker_3      Ready      Actice
02ujhdfnj239ddf2    Manager       Ready      Actice          Leader
```

## Service Rolling Update