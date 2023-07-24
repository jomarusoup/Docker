# Docker Images
- 모든 컨테이너는 이미지를 기반으로 생성
- Docker hub(중앙 이미지 저장소)에서 이미지 pull/push
## docker image create

- 특정 어플리케이션을 위한 특정 개발 환경 구축 후 사용자만의 이미지 직접 생성
- 기본 base image에 컨테이너 변경사항이 포함된 것을 이미지로 만들면 

```shell
$ docker run -it --name {Container_Name} {Image:Tag}
root@{Container_ID}# (변경 사항 진행)
```

- 컨테이너 내의 변경 사항 존재
- host로 나와 docker commit로 컨테이너를 이미지로 변환

```shell
$ docker commit -a "author" -m "Commit_Message" {Option} {Container_Name} {Image:Tag}
```

- `-a`으로 이미지 작성자 와 `-m`으로 커밋 메시지를 추가해 메타 데이터를 이미지에 포함

## Dockerfile
- docker build로 컨테이너를 직접 생성 후 이미지로 docker commit 할 필요 X
- 도구를 이용해 빌드 및 배포 자동화 가능
- 도커 허브에 올라와 있는 이미지들은 Dockerfile를 함께 제공
- Dockerfile은 한 줄이 하나의 명령어 [ Instruction + option ]

```dockerfile
#---[base Image]
FROM

#---[METADATA]
LABEL

#---[Command in Conatainer]
RUN

#---[Add file to image]
ADD

#---[Working directory]
WORKDIR

#---[Port]
EXPOSE

#---[Start Container with this command]
CMD
```

- `FROM` : Docker 이미지의 베이스 이미지를 지정
  - 사용하려는 이미지가 없다면 docker hub에서 찾아서 pull

- `LABEL`: Docker 이미지에 메타데이터를 추가
  - ket:value 형태로 저장
  - 메타데이터는 Docker 이미지에 대한 정보를 제공
  - `docker inspect` 명령어를 사용하여 확인할 수 있습니다.

- `RUN` : 이미지를 만들기 위해 컨테이너 내부에서 명령어 실행
  - `RUN["Command_Line", "Arguments_1", "Argument_2", ...]`
  - JSON형식의 배열

- `EXPOSE` : 이미지가 사용할 포트 명시
    - 컨테이너 내부에서 사용하는 포트
    - `docker run -P`옵션을 사용하면 EXPOSE로 노출된 포트를 호스트에서 사용 가능한 포트에 차례로 연결
    - 컨테이너가 호스트의 어떤 포트와 연결되어 있는지 확인 필요



## Docker image create
```shell
$ docker build -t {Image}:{Tag} -f {Absolute_path_of_Dockerfile} .

$ docker build -t hello:1.0 -f /home/user/docker/dockerfile .
```
- build 명령어의 끝에는 Dockerfile이 저장된 경로 입력
    - 로컬에 저장된 Dockerfile이 아닌 외부 에서 가져오는 경우 외부 URL 입력 가능


## Cache with Docker Image
- 이미지를 빌드 이후 같은 빌드를 하면 이전의 이미지 빌드에 사용된 캐시 이용

## Docker image Build Fail
- 이미지 빌드 처리를 위해 저장하는 위치의 용량 초과
```shell
$ docker system prune -af .

- 만약 /var/lib/docker/overlay2/ 디렉토리 강제 삭제 후 계속 빌드 에러이면
```shell
$ docker builder prun .
