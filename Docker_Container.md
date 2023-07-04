# 1. docker basic commandline

| Command                                         | Explain                                             |
| :---------------------------------------------- | :-------------------------------------------------- |
| `docker search {Image_Name}`                  | 이미지 이름으로 docker hub에서 검색                 |
| `docker pull {Image_Name:TAG}`                | 저장소에서 이미지 다운로드                          |
| `docker ps`                                   | 실행중인 컨테이너 조회                              |
| `docker ps -a`                                | 생성된 컨테이너 전체 조회                           |
| `docker images`                               | 저장한 이미지 목록 조회                             |
| `docker create {Option} {Image_Name:Tag}`     | 컨테이너 생성                                       |
| `docker run {Option} {Image_Name:Tag}`        | 컨테이너 생성 및 실행                               |
| `docker start {Image_Name:Tag}`               | 컨테이너 실행                                       |
| `docker attach {Option} {Image_Name:Tag}`     | 컨테이너 내부 진입                                  |
| `docker exec {Option} {Image_Name:Tag}`       | 컨테이너 내부에서 명령어 실행 후 결과값 반환        |
| `Ctrl + P, Q`                                 | 컨테이너 탈출                                       |
| `exit`                                        | 컨테이너 프로세스 종료 및 탈출                      |
| `docker inspect {Image_Name:Tag}`             | 컨테이너ID생성된 컨테이너의 정보를 확인가능         |
| `docker update {Option} {Container_Name}`     | 컨테이너의 자원 제한 업데이트                       |
| `docker rm {Container_ID or Container_Name} ` | 생성한 컨테이너 삭제 (단 컨테이너 정시 후 삭제가능) |
| `docker rmi {Image NAME:TAG}`                 | 생성한 이미지를 삭제                                |

# 2. Docker Image

- 저장소 이름 / 이미지 이름(역할) : 태그(리비전) {Repository / Images_Name : Tag}
- 모든 컨테이너는 이미지를 기반으로 생성
- 도커 허브(중앙 이미지 저장소)에서 이미지를 pull
  - 도커가 공식적으로 제공하는 이미지 저장소

# 3. Docker Container

- 컨테이너에서 무엇을 하든 원래 이미지는 영향을 받지 않는다
- 생성된 각 컨테이너는 각기 독립된 파일 시스템을 제공
- 호스트와 분리되어 특정 컨테이너에서 어떤 변화가 있든 호스트와 컨테이너는 변화 X

## 3.1. Container Create

- 컨테이너에서 기본 사용자는 root
- 컨테이너와 호스트의 파일 시스템은 서로 독립적

### 3.1.1. 컨테이너 생성 명령어

- `docker run` : pull + create + start를 일괄적으로 실행하고 attach가 가능하면 attach
- `docker create` : pull + create만 실행하고 start, attach는 실행하지 X

```shell
# 컨테이너 생성 및 실행
$ docker run {option} {Image:Tag}

# 컨테이너 생성
$ docker create {option} {Image:Tag}
# docker create로 생성된 혹은 실행 중인 컨테이너 접근
$ docker attach {Container_Name}
```

```shell
$ docker run -itd --init --privileged --name {container_name} -v {Host_Dir}:{Container_Dir} --memory=2g --ipc=host --net host {Image_Name:Tag}
```

### 3.1.2. run/create 옵션

```shell
--privileged
```

- 컨테이너 안에서 호스트의 리눅스 커널 기능(Capability)을 모두 사용
- 호스트의 주요 자원에 접근 가능

```shell
-i, --interactive
```

- 표준 입력(stdin)을 활성화하며, 컨테이너와 연결(attach)되어 있지 않더라도 표준 입력을 유지
- 보통 이 옵션을 사용하여 Bash 에 명령을 입력합니다.

```shell
-t, --tty
```

- TTY 모드(pseudo-TTY)를 사용
- Bash를 사용하려면 `-t`옵션 설정
- `-t` 옵션을 설정하지 않으면 명령을 입력할 수는 있지만, 셸이 표시되지 않습니다.

```shell
-d, --detach
```

- Detached 모드
- 보통 데몬 모드라고 부르며, 컨테이너를 백그라운드로 실행

```shell
--name
```

- 컨테이너 이름을 설정

```shell
-v, --volume
```

- 데이터 볼륨을 설정
- 호스트와 컨테이너의 디렉토리를 연결하여, 파일을 컨테이너에 저장하지 않고 호스트에 바로 저장(마운트)

```shell
-p, --publish
```

- 호스트와 컨테이너의 포트를 연결합니다. (포트포워딩)
- <호스트 포트>:<컨테이너 포트>
- `-p 80:80`

```shell
--rm
```

- 프로세스 종료시 컨테이너 자동 제거

```shell
--restart
```

- 컨테이너 종료 시, 재시작 정책을 설정
- `--restart="always"`

```shell
-u, --user
```

- 컨테이너가 실행될 리눅스 사용자 계정 이름 또는 UID를 설정
- `--user root`

```shell
-e, --env
```

- 컨테이너 내에서 사용할 환경 변수를 설정
- 컨테이너화 된 어플리케이션은 환경변수에서 값을 가져와 사용히기에 자주 사용하는 옵션
- root의 비밀번호와 같은 것들을 설정 가능
- 컨테이너 내부에서 echo {환경변수}를 입력하면 docker run -e로 설정된 환경 변수 확인 가능

```shell
--link
```

- 컨테이너끼리 연결
- {Container_Name:alias}

```shell
-h, --hostname
```

- 컨테이너의 호스트 이름을 설정합니다.

```shell
-w, --workdir
```

- 컨테이너 안의 프로세스가 실행될 디렉터리를 설정합니다.

```shell
-a, --attach
```

- 컨테이너에 표준 입력(stdin), 표준 출력(stdout), 표준 에러(stderr) 를 연결합니다.

### 3.1.3. run/create 자원 제한 옵션

- 자원 제한 옵션을 사용하지 않으면 호스트의 자원을 제한 없이 사용
- `docker inspect`를 사용해 컨테이너의 자원 제한 확인 가능
- `docker update {Option} {Container_Name}`로 자원 제한 변경

```shell
-c, --cpu-shares
```

- CPU 자원 제한 설정
- 기본 값은 1024이며, 각 값은 상대적으로 적용
  - CPU 할당에서 1의 비중을 의미

```shell
--cpuset-cpus
```

- Host에 여러개의 CPU가 조재할 경우 컨테이너가 특정 CPU만 사용하도록 제한
- `--cpuset-cups=2` : 2번 CPU만 사용용

```shell
-m, --memory
```

- 메모리 제한 설정
- <숫자><단위> 형식이며 단위는 b, k, m, g 를 사용

```shell
--security-opt
```

- SELinux, AppArmor 옵션을 설정합니다.
- `--security-opt=”label:level:TopSecret”`

## 3.2. 컨테이너 탈출

1. 컨테이너를 정지하고 탈출

```shell
# 컨테이너 내부에서 입력
$ exit
```

```shell
# 컨테이너 내부에서
Ctrl + D
```

2. 컨테이너를 정지하지 않고 탈출

```shell
# 컨테이너 내부에서 입력
$ Ctrl + P, Q (동시에 입력)
```

## EXEC 명령어

- exec 명령어를 사용해 컨테이너 내부의 shell 사용 가능

```shell
$ docker exec -it {Container_Name} /bin/bash
```

## Container list

```shell
# 실행 중인 컨테이너 목록
$ docker ps

# 모든 컨테이너 목록
$ docker ps -a

# 컨테이너 정보 확인
$ docker inspect {Container_Name}
```

- COMMAND : 컨테이너 시작시 실행될 명령어
  - 커맨드의 경우 대부분의 이미지에 미리 내장
  - 일반적인 OS이미지의 경우 내장되어 컨테이너 생성시 별도의 커맨드 생성 X
  - 내장된 커맨드는 `docker run ` or `docker create`시 덮어 쓰기 가능
    ex) `docker run -it {Image:Tag} echo hello`
    - 컨테이너 실행 시 마다 `echo hello`를 실행
- CREATED : 컨테이너가 생성되고 난 뒤 흐른 시간
- PORTS : 컨테이너가 개방한 포트화 호스트와 연결된 포트
  - 외부에 노출될 포트를 설정하지 않으면 `<none>`
- NAMES : `--name`으로 이름을 설정하지 않으면 무작위로 이름이 설정

```shell
$ docker ps --format "table {{.Names}}\t{{.Image}}"
```

- Go 템플릿을 사용해 컨테이너의 원하느 정보만 출력 가능
- `\t` 사용 가능

## Container delete

```shell
# 컨테이너가 정지된 상태에서만 삭제 가능
$ docker rm {Container_Name}
```

- 실행 중인 컨테이너는 삭제 불가능
- 컨테이너를 정지하고 삭제하던가 강제로 삭제할 수 있는 옵션 필요

```shell
# 실행 중인 컨테이너 삭제
$ docker rm -f {Container_Name}
```

```shell
# 모든 컨테이너 삭제
$ docker container prune
WARNING! This wil remove all stopped contaiers
Are you sure you want to continue? [Y/N] y
```

```shell
# 실행 상태와 관계없이 모든 컨테이너를 정지후 삭제
$ docker stop $(docker ps -a -q)
$ docker rm $(docker ps -a -q)
```

- `docker ps - a -q`
  - `-a` : 컨테이너의 상태에 상관 없이 모든 컨테이너
  - `-q` : 컨테이너의 ID 출력

## Exposing a container to the outside

- 컨테이너는 가상 IP 주소를 할당 받는다
- 172.17.0.0의 IP를 순차적으로 할당
- 아무런 설정을 하지 않으면 컨테이너는 외부에 접근 불가
  - 도커가 설치된 호스트만 접근 가능
- 외부에 컨테이너를 노출하기 위해 컨테이너와 호스트의 포트 바인딩 필요

```shell
# Container의 포트를 Host의 포트에 바인딩
$ docker run -it --name {Container_Name} -p {Host_Port:Cotainer_Port} {Image:Tag}

# 호스트의 특정 IP와 포트이용
$ docker run -it --name {Container_Name} -p {Host_IP:Host_Port:Cotainer_Port} {Image:Tag}

# 여러개의 포트 등록
$ docker run -it --name {Container_Name} -p {Host_Port:Cotainer_Port} -p {Host_Port:Cotainer_Port} {Image:Tag}
```

- 컨테이너 내부에서 생성된 어플리케이션의 경우 호스트에 영향이 없다
- 호스트와 컨테이너를 포트로 연결함으로써 외부에서 호스트를 통해 컨테이너 내의 어플리케이션에 접근 가능

## Container Application

- All-in-one Container : 서버와 데이터베이스를 하나의 컨테이너 안에 구출
- Isolated Container : 서버와 데이터베이스를 분리
  - 도커의 이미지 관리와 컴포넌트의 독립성 유지에 유리
  - 도커에서 공식으로 권장하는 구조




## docker commit

- Docker Conatiner에서 변경된 내용을 새로운 이미지로 만드는 명령어
- 컨테이너에셔 변경된 파일, 설정 등을 새로운 이미지로 만들어 로컬 머신에 저장

```shell
$ docker commit {Container_Name} {New_Image_Name:Tag}
```
