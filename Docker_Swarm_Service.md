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

<aside class="notice">
Service를 제어하는 모든 docker command는 Manager Node에서만 사용 가능
</aside>

## Service Create
- `-d`가 사용 가능한 이미지만 사용
```shell
$ docker service create {Option} {Image:Tag}

# Host Port - Service Port
$ docker service create -p {Host_Port}:{Service_Port} {Image:Tag}
```
- `-p` : Swarm Cluster 자체에 포트를 개방
- 각 생성된 Container가 Host_Port에 연결 X
- 실제로 각 Node_Port로 들어온 요청을 클러스터 내에서 생성된 컨에이너 중 1개로 redirect
  - 각 Node_Port를 Service_Port로 매핑하여 요청이 클러스터 내에서 생성된 컨테이너 중 하나로 redirection
```shell
# Service container list
$ docker ps --filter is-task=true --format {{.Names}}
```
- Docker swarm모드에서 실행 중인 Service에서 생성된 Container만 확인 가능
- 명령어를 입력한 Node에서 실행 중인 Service의 Container 이름 출력

```shell
# service list
$ docker service ls

# service info
# 각 container가 실행 중인 Node 확인 가능
$ docker service ps {Service_Name}
# 출력되는 정보를 축소하지 않고 전체 정보를 출력하도록 지정하는 옵션
$ docker service ps {Service_Name} --no-trunc

# delete service
$ docker service rm {Service_Name}

# Container 개수 증가
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
- Manager Node의 Local Docker Daemon에 이미지가 없더라도 Private registry에서 이미지를 찾아서 Service create

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
```


## Service Recovery from Failure
- replicated mode로 설정된 Service의 Container 혹은 Node에 문제가 생기면 Manager는 새로운 Conatiner를 자동으로 생성
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

- `docker service ps`에서 NAME에 `\_`가 붙어 있으면 어떤 이유이든지 Down Container

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
- 문제의 Node를 복구해도 다시 생성된 Container이 원래의 Node로 돌아가지 X
  - Rebalance 작업 X  
- 새로운 Node를 추가하거나 Node를 복구한 경우 `docker service scale`로 컨테이너의 개수 조정 필요(감소 후 증가)
```shell
$ docker service scale {Service_Name}=1
$ docker service scale {Service_Name}=4
```
## Service Rolling Update
- 여러 개의 서버, 컨테이너로 구성된 클러스터를 변경하기 위해 하나씩 재시작
- 모든 Node나 Containeer를 한 번에 재시작하면 Service Down time이 발생
- 하나씩 재시작을 진행하는 경우 다른 Node나 Container은 작동 중이기에 지속적인 서비스 가능

```shell
# Sevice Image Update
$ docker service update --image {Image:Tag} {Servicd_Name}
```
```shell
# Sevice Image Update
$ docker service create \
> --replicas 4 \
> --name {Service_Name} \
> --update-delay 10s \
> --update-parallelism 2 \
> {Image:Tag}

$ docker service create --replicas 4 --name {Service_Name} --update-delay 10s --update-parallelism 2 {Image:Tag}
```

- `--replicas` : 생성할 컨테이너 개수를 지정
- `--update-delay` : Rolling Update를 진행할 때, 각 Container 간의 업데이트 딜레이를 지정
- `--update-parallelism` : Rolling Update를 진행할 때, 동시에 업데이트할 Container의 개수를 지정

```shell
# Service Info
$ docker service inspect --pretty {Service_Name}

...
UpdateConfig:
  Parallelism: 2
  Delay:       10s
  On failure:  pause
...
```
- `On failuce : pause` : 업데이트 중 오류가 발생하면 롤링 업데이트를 중지
- `docker create service`에서 `--update-failure-action continue`옵션을 추가하면 오류가 발생해도 업데이트 진행
```shell
$ docker create service --name {Service_Name} --replicas {Number} --update-failure-action continue {Image:Tag}
```
- `docker create service`에 기본적으로 update에 대한 내용이 service 자체에 설정
- `docker create service`의 옵션을 변경하는 것으로 update에 대한 설정 변경 가능
- Rollback도 가능
```shell
$ docker service rollback {Service_Name}
```

## Passing configuration information to service containers
- 외부에 어플리케이션 서비스를 위해 컨테이너 내부에 미리 설정값 포함
- 정적으로 설정 값을 이미지 내부에 포함해 배포 시 확장성과 유연성 감소
  - 대안으로 `-v`를 통해 호스트에 위치함 설정 파일이나 값을 볼륨으로 컨테이너에 공유 가능
  - 컨테이너 내부의 설정값을 유동적으로 설정하기 위해 `-e`옵션을 통한 환경변수 설정 가능
- 같은 클러스터 내에서 파일 공유를 위해 설정 파일을 호스트 마다 마련하는 것은 비효율적
  - secret : Password, SSH key, 인증서 key 같은 민감한 데이터 전송
  - config : 어플리케이션 컨테이너나 레지스트리 설정 파일과 같이 암호화가 필요없는 설정값에 대해 사용
- `docker run`에서는 사용이 불가능하며 Docker Swarm 모드에서만 사용 가능

### Secret
- 파일의 내용을 터미널에 출력해 secret로 가져올 수 있지만 echo도 사용 가능
```shell
# Secret create
$ echo {Password} | docker secret create {Secret_Name} -
dfalen2o3ql2

# Secret List
$ docker secret ls

# Secret Info
$ docker secret inspect {Secret_Name}
[
    {
        "ID": "dfalen2o3ql2",
        "Version": {
            "Index": 29550
        },
        "CreatedAt": "2023-xx-xxT00:06:05.625892093Z",
        "UpdatedAt": "2023-xx-xxT00:06:05.625892093Z",
        "Spec": {
            "Name": "{Secret_Name}",
            "Labels": {}
        }
    }
]

```
- Secret를 조회해도 실제 값은 확인 불가능
- secret값은 Manager Node간에 암호화된 상태로 저장
- File system에 저장되지 않고 Memory에 저장되기에 Service Container이 삭제되면 Secret도 같이 삭제(휘발성)
```shell
$ docker service create \
> --name {Service_Name} \
> --replicas {Number} \
> --secret source={Password}, target={Service_Container_Root_Password} \
> --secret source={Password}, target={Service_Container_User_Password} \
> -e {Service_Container_Root_Password_File}="/run/secrets/{Service_Container_Root_Password}" \
> -e {Service_Container_User_Password_File}="/run/secrets/{Service_Container_User_Password}" \
> -e {Service_Container_Database}="{Database_Info}" \
> {Image:Tag}
```
- `--secret source={Password}, target={Service_Container_Root_Password}`
  -  Service Container에서 사용할 Root 계정의 비밀번호를 지정
  -  `{Password}` : 비밀번호를 저장한 Secret의 이름
  -  `{Service_Container_Root_Password}` : 비밀번호를 사용할 Root 계정의 이름 지정

- `--secret source={Password}, target={Service_Container_User_Password}` 
  - Service Container에서 사용할 User 계정의 비밀번호를 지정
  - `{Password}` : 비밀번호를 저장한 Secret의 이름
  - `{Service_Container_User_Password}` : 비밀번호를 사용할 User 계정의 이름 지정

- `-e {Service_Container_Root_Password_File}="/run/secrets/{Service_Container_Root_Password}"`
  - Service Container에서 사용할 Root 계정의 비밀번호 파일 경로를 지정
  - `{Service_Container_Root_Password_File}` : 파일 경로를 지정하는 환경 변수의 이름
  - `{Service_Container_Root_Password}` : 비밀번호를 사용할 Root 계정의 이름 지정

- `-e {Service_Container_User_Password_File}="/run/secrets/{Service_Container_User_Password}"` 
  - Service Container에서 사용할 User 계정의 비밀번호 파일 경로를 지정 
  - `{Service_Container_User_Password_File}` : 파일 경로를 지정하는 환경 변수의 이름
  - `{Service_Container_User_Password}` : 비밀번호를 사용할 User 계정의 이름 지정

- `-e {Service_Container_Database}="{Database_Info}"`
  -  Service Container에서 사용할 데이터베이스 정보를 지정
  -  `{Service_Container_Database}` : 데이터베이스 정보를 저장하는 환경 변수의 이름 
  -  `{Database_Info}` : 데이터베이스 정보를 지정