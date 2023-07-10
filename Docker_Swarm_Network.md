# Docker Swarm Network
- 여러 개의 도커엔진에 같은 컨테이너를 분산해서 할당
- 각 도커 데몬의 네트워크가 하나로 묶인, 네트워크 풀 필요
- 서비스를 외부로 노출 시 어느 노드로 접근하더라도 해당 서비스의 컨테이너로 접근 가능하게 라우팅 기능 필요
- 분산, 네트워크 풀, 라우팅 기능은 Docker Swarm에서 자체적으로 지원하는 네트워크 드라이버에서 사용 가능

## Docker Network List
```shell
$docker network ls
NETWORK ID     NAME              DRIVER    SCOPE
8401dd22deae   bridge            bridge    local
230b85abd851   docker_gwbridge   bridge    local
bcfd8e951d46   host              host      local
6b35m297a6m9   ingress           overlay   swarm
d7fb9ee70ec7   none              null      local
```
- bridge: Docker의 기본 네트워크 드라이버
  - 컨테이너가 이 네트워크에 연결되면, 컨테이너는 호스트와 동일한 네트워크에 속한 것처럼 작동
- docker_gwbridge: Docker가 호스트와 통신할 때 사용하는 네트워크
  - 이 네트워크는 호스트와 동일한 네트워크에 속하지만, 호스트와는 별도의 IP 주소 범위를 사용
  - Docker Swarm 에서 Overlay 네트워크를 사용할 때 사용
- host: 컨테이너가 호스트의 네트워크 스택을 공유
  - 이 경우, 컨테이너는 호스트와 동일한 IP 주소를 가지며, 호스트의 포트도 사용 가능
- ingress: Docker Swarm 모드에서 사용되는 내부 네트워크
  - 이 네트워크는 서비스 간 통신을 위해 사용
  - 로드 밸런싱과 Routing Mesh에 사용
- none: 컨테이너가 네트워크에 연결되지 않도록 설정

## Ingress NetWork
- Swarm Cluster 구성시 자동으로 등록되는 네트워크
- Swarm Mode에서만 유효
- Swarm에 등록된 모든 Manger Node, Worker Node는 전부 ingress network가 생성
- 어떤 Swarm Node에 접근 하더라도 서비스 내의 컨테이너에 접근 가능하도록 라우팅 메시를 구성
- 서비스 내의 컨테이너에 대한 접근을 라운드 로빈 방식으로 분산하는 로드 밸런싱 역할 수행
  - 컨테이너를 여러 개 생성하고 접근시 어느 노드에 있든 개수와 상관없이 접근이 가능