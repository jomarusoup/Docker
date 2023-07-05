# Docker Swarm Service
- Docker Swarm 제어 단위가 Service
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