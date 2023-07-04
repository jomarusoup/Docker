# Docker Images

## docker image create

- 특정 어플리케이션을 위한 특정 개발 환경 구축 후 사용자만의 이미지 직접 생성

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
