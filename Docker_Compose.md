# Docker Compose
- 단일 노드에서 여러 개의 컨테이너로 구성된 어플리케이션을 하나의 컨테이너 묶음으로 관리
- 옵션과 환경을 정의한 파일(YAML)을 읽어 컨테이너를 순차적으로 생성
- `run`의 옵션을 그대로 사용 가능
    - 각 컨테이너의 의존성, 네트워크, 볼륨등을 함께 정의 가능
- Swarm의 서비스와 유사하게 디스커버리도 자동으로 수행

## Docker Compose install
```shell
$ curl -L "https://github.com/docker/compose/releases/download/v2.7.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
$ chmod +x /usr/local/bin/docker-compose
$ docker-compose --version
```

## User Docker Compose
- 설정이 정의된 `.yml`파일을 읽어 도커 엔진을 통해 컨테이너 생성
- 서로 다른 이미지를 정의해 각각 컨테이너에게 원하는 설정 가능
- A와 B 이미지를 정의한 Docker Compose인 경우 `--scale` 옵션 사용 가능
```shell
version: '3'
services:
  {Service_Name_A}:
    image: {Pravate_Regisrty_IP:PORT}/{Image_A:Tag}
    privileged: true
    init: true
    volumes:
      - /Dir:/Dir
    mem_limit: 2g
    ipc: host
    network_mode: host
  {Service_Name_B}:
    image: {Pravate_Regisrty_IP:PORT}/{Image_B:Tag}
    privileged: true
    init: true
    volumes:
      - /Dir:/Dir
    mem_limit: 2g
    ipc: host
    network_mode: host
```
- version: '3.0'은 Docker Compose 파일의 버전을 지정
  - Docker Compose가 사용할 문법과 기능을 결정
  - 3.0 버전은 Docker Compose 파일에서 사용할 수 있는 최신 버전
  - Docker Compose v1.17.0 이상에서 지원
  - 3.0version에서는 services, networks, volumes 등의 키워드를 사용하여 서비스, 네트워크, 볼륨 등을 정의가능
- YAML파일에서 들여쓰기는 Tab가 아닌 공백 2개(Space bar 2)로 구분
- `-f` 옵션으로 yml파일 지정
  - 옵션을 주지 않으면 docker-compose.yml파일을 읽어 컨테이너 생성
```shell
# Container Create
$ docekr-compose up -d
$ docker-compose -f {File_Name.yml} up -d

# add Project Name
$ docker-compose -p {Project_Name} -f {File_Name.yml} up -d
# scale option
$ $ docker-compose -p {Project_Name} -f {File_Name.yml} up -d --scale {Container_Name}=5

# delete docker-compose
$ docker-compose -f {YAML_FILE} down -v
```
- 프로젝트 이름을 명시하지 않으면 현재 디렉터리의 이름으로 시작하는 이름으로 컨테이너 생성
```shell
$ docker service create --name {Service_Name} \
> --replicas=5 \
> --publish=8080:80 \
> {Image:Tag}
```


```shell


$ docker service ls
$ docker service ps {Service_Name}

```


# Docker Stack ( Compose + Swarm )
- Compose와 Stack 는 YAML파일로 컨테이너 생성
- YAML작성 구조는 동일하나 각각 사용 가능한 옵션들이 상이
- Docker Compose 파일을 사용하여 여러 개의 서비스를 정의 가능
  - 여러 개의 이미지를 하나의 집합으로 생성하고 관리
- Docker Swarm 클러스터에서 배포하고 관리
  - 각 이미지마다 서비스 단위로 컨테이너 생성
  - Docker Compose 파일을 사용하여 여러 개의 서비스를 정의
  - Docker Swarm 클러스터에서 배포하고 관리
  - Docker Swarm을 기반으로 하며, Docker Compose 파일을 사용하여 서비스를 정의하고 배포

## Docker Stack
```shell
# Docker Compose 파일을 Docker Swarm 파일로 변환
$ docker-compose -f docker-compose.yml config > docker-stack.yml

# Docker Swarm으로 스택을 배포
$ docker stack deploy -c {YAML_FILE} {Stack_Name}
# Pravate Registry Image를 사용하는 경우
$ docker stack deploy -c {WAML_FILE} {Stack_Name} --with-registry-auth

# 레플리카 수를 늘립니다.
$ docker service scale <service-name>=<replica-count>

# docker stack delete
$ docker stack rm {Stack_Name}
```

```shell
#------------------------------------------------------------------------------- 
# Docker Compose file version
#   3.8 - max_replicas_per_nodea, init (O)
#   3.0 - max_replicas_per_nodea, init (X)
#-------------------------------------------------------------------------------
version: '3.8'
#version: '3.0'

#------------------------------------------------------------------------------- 
# Serivce [Container]          : acs[4], ivs[2], mfs[1]
# Ignoring unsupported options : ipc, network_mode, privileged, security_opt 
#-------------------------------------------------------------------------------
services:

# [ Container Name : A ] -------------------------------------------------------------#
  {A}:
    #--- [ Image ]
    image: {Pravate_Registry_IP:Port}/{Image:Tag}

    #--- [ Docker Network ]
    networks:
      - default

    #--- [ Deploy ]
    deploy:
      replicas: 4
      restart_policy:
        condition: on-failure
      placement:
        max_replicas_per_node: 1
        constraints:
         - node.role == manager
#        - node.hostname == Wroker_1
#        - node.hostname == Worker_2
#        - node.hostname == Worker_3
#        - node.hostname == Manager
      resources:
        limits:
          memory: 3g
        reservations:
          memory: 2g

    #--- [ Mount : Host 2 Container ]
    volumes:
      - /Dir:/Dir

    init: true
    tty: true
    stdin_open: true

# [ Container Name : B ] -------------------------------------------------------------#
  {B}:
    #--- [ Image ]
    image: {Pravate_Registry_IP:Port}/{Image:Tag}

    #--- [ Docker Network ]
    networks:
      - default

    #--- [ Deploy ]
    deploy:
      replicas: 2
      restart_policy:
        condition: on-failure
      placement:
        max_replicas_per_node: 1
        constraints:
         - node.role == worker
#        - node.hostname == Wroker_1
#        - node.hostname == Worker_2
#        - node.hostname == Worker_3   
      resources:
        limits:
          memory: 4g
        reservations:
          memory: 2g

    #--- [ Mount : Host 2 Container ]
    volumes:
      - /Dir:/Dir

    init: true
    tty: true
    stdin_open: true

```