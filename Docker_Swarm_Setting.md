# Docker Swarm Setting
- docker engin에 내장
- 서버 클러스터 구성시 반드시 각 서버의 시간 동기화 필요(NTP)
- Manager Node + Worker Node
  - Manager Node는 Manager 이면서 Worker
  - 모든 노드를 Manager로 구성 가능
- 운영 환경에서 스웜 모드로 클러스터 구성시 Manager Node 다중화 권장
  - Manager Node의 부하를 분산
  - 특정 Manager Node가 다운되어도 정상적으로 클러스터 유지 가능
- Manager Node의 절반 이상이 장애가 생기면 복구될 때까지 클러스터 운영 중단
- Manager Node를 홀 수개로 생성해 과반 이상이 유지 필요

## Docker Swarm Cluster
```shell
Swarm-Maneger
Swarm-worker1
Swarm-worker2
Swarm-worker3
```

- Port Open
```shell
# Swarm Manager Node use this port
$ firewall-cmd --add-port=2377/tcp --permanent

# All Node network
$ firewall-cmd --add-port=7946/tcp --permanent
$ firewall-cmd --add-port=7946/tcp --permanent

# ingress overlay network use this port 
$ firewall-cmd --add-port=4789/tcp --permanent
$ firewall-cmd --add-port=4789/udp --permanent
```

- Manager Node에서 Swarm Cluster 시작
- Worker Node가 Manager Node에 접근하기 위한 IP 입력
```shell
$ docker swarm init --advertise-addr {Manager_Node_IP}

...
    docker swarm join \
    --token SWMTKN-x-xxxxxxxxxxxxxxxxxxxxx ... \
    xxx.xxx.xxx.xxx:xxxx
...
```
- 제공된 토근을 각 Worker Node에 입력해 Manager Node에 Worker Node 추가
- Docker Node 확인
```shell
$ docker node ls

ID                            HOSTNAME     STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
nkrkjn231009391rfrs91ky29     Worker_1     Ready     Active                          24.0.2
kpc3jdcvnq7fmp4vy794lku8q     Worker_2     Ready     Active                          24.0.2
21318rrsad0123ngrb4dc4ie8     Werker_3     Ready     Active                          24.0.2
vpzn9jv2no5szadfsdcvaadq01 *  Manager_4    Ready     Active         Leader           24.0.2

```
- (*)가 붙은 Node 현재 위치한 Node
- Manager node = Nomal Manager Node vs Leader Node
  - Leader Node : 모든 Manager Node에 해한 데이터 동기화화 관리 담당, 항상 작동 필요
  - 장애가 생기면 새로운 Leader Node 선출
- 새로운 Manager Node를 생성할려면 Manager token 생성 및 입력
```shell
# Worker Node add
$ docker swarm join-tocken worker

# Mananger Node add
$ docker swarm join-token manager

# 보안을 위해 주기적으로 토큰 갱신 필요
$ docker swarm join-token --rotate manager
```
- 토큰을 공개하면 누구든지 클러스터에 노드를 추가해 보안에 취약
- 토큰 갱신은 Manager Node에서만 가능

```shell
# docker swarm cluster info
$ docker info
```
## Delete Worker Node
- 클러스터에서 제외하고 싶은 노드에서 명령어 입력
```shell
$ docker swarm leave
```
- leave는 해당 노드의 상태를 Down으로만 인지(노드 삭제 X)
- Node를 삭제하고 싶으면 Manager Node에서 명령어 입력
```shell
# Woker Node delete in Manager Node
$ docker node rm {Worker_Node_Name}

# Manager Node delete in Manager Node
$ docker swarm leave --force
```
- Manager Node를 삭제하면 Manager Node에 저장되어 있는 클러스터의 정보도 삭제
- Manager Node를 삭제하면 클러스터 이용 불가

## Worker 2 Manager / Manager 2 Worker
```shell
# Worker 2 Manager
$ docker node promote {Woker_Node_Name}

# Manager 2 Worker
$ docker node demote {Manager_Node_Name}
```
- Manager Node가 1개만 존재하면 Manager 2 Worker 불가능
- Manager Leader Node에서 Manager 2 Worker하면 다른 Manager Ndoe 중 New Leadr 선출
