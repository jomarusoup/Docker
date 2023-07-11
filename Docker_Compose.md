# Docker Compose
- 여러 개의 컨테이너로 구성된 어플리케이션을 하나의 컨테이너 묶음으로 관리
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

# Docker Stack ( Compose & Swarm )
- Docker Compose 파일을 사용하여 여러 개의 서비스를 정의 가능
- Docker Swarm 클러스터에서 배포하고 관리
- Docker Stack은 Docker Compose 파일을 사용하여 여러 개의 서비스를 정의하고, Docker Swarm 클러스터에서 배포하고 관리
  -  Docker Swarm을 기반으로 하며, Docker Compose 파일을 사용하여 서비스를 정의하고 배포
-  Docker CLI를 사용하여 스택을 배포하고 관리

## Docker Compose 2 Stack
```shell
# Docker Compose 파일을 Docker Swarm 파일로 변환
$ docker-compose -f docker-compose.yml config > docker-stack.yml

# Docker Swarm으로 스택을 배포
$ docker stack deploy -c docker-stack.yml <stack-name>

# 레플리카 수를 늘립니다.
$ docker service scale <service-name>=<replica-count>
```