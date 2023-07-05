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

# Private registry
$ docker service create --with-registry-auth \
> --name {Service_Name} \
> --replicass {Number} \
> {Image:Tag}

# the number of replicas
$ docker service scale {Service_Name}={Number}

# service ps
$ docker service ps {Service_Name}
```

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

## Service Rolling Update