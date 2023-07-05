# 사설 도커 이미지 레지스트리 구성
- 생성한 이미지를 다른 도커 엔진에 배포하기 위한 이미지 저장소 생성

## 1. 도커 허브에서 도커 레지스트리 공식 이미지 검색

```shell
$ docker search registry

NAME                DESCRIPTION                                      STARS     OFFICIAL   AUTOMATED
registry            The Docker Registry 2.0 implementation…           3841       [OK]   
```

## 2. registry 컨테이너 이미지 다운로드

```shell
$ docker image pull registry
```

## 3. registry 컨테이너 생성 및 실행 
- registry 이미지는 docker hub에서 공식적으로 지원하는 이미지
- 다른 여타 이미지와 다르게 `-it`로 접근 불가능
    - registry 이미지가 백구라운드에서 실행되는 서버의 역할이기 때분
- `-d` 옵션으로 detached모드로 실행

```shell
$ docker run -d --name={Container_Name} \
> -p 5000:5000 \
> --restart=always \
> registry

$ docker run -d --name={Container_Name} -p 5000:5000 --restart=always registry
```
 - Registry Container는 5000번 포트를 사용
 - Container 5000번 포트와 Host의 5000번 포트를 연결

## 4. 사설 레지스트리의 이미지 레포지토리 목록 조회

```shell
$ curl -X GET https://{localhost_IP}:5000/v2/_catalog

{"repositories":[]}
```
- `-X GET` : The HTTP request method to GET
- `https://{localhost_IP}:5000/v2/_catalog` : local registry의 _catalog API 엔드포인트에 GET 요청
- 사용 가능한 이미지 목록 조회

### 4.1. 사설 레지스트리로 이미지 업로드
- 사설 레지스트리에 이미지 업로드를 위한 이미지 이름(태그) 지정

```shell
$ docker image tag {Image:Tag} {localhost_IP}:5000/{Image:Tag}
```

### 4.2. 도커 엔진의 이미지 목록 확인

```shell
$ docker images
```

### 4.3. 사설 레지스트리에 이미지 업로드

```shell
$ docker image push {localhost_IP}:5000/{Image:Tag}
```

### 4.4. 사설 레지스트리의 이미지 레포지토리 목록 조회

```shell
$ curl -X GET https://{localhost_IP}:5000/v2/_catalog

{"repositories":[Image]}
```

### 4.5. 이미지의 세부 목록(태그 목록) 확인

```shell
$ curl -X GET https://{localhost_IP}:5000/v2/{Image}/tags/list
{"name":"Image","Tag":["0.1","0.2"]}
```

### 4.6. 사설 레지스트리로부터 이미지 다운로드

```shell
$ docker image pull {localhost_IP}:5000/{Image:Tag}
```

## 5. docker deamon

- 도커 데몬은 HTTPS를 사용하지 않으면 레지스트리 컨테이너에 접근이 불가능
- HTTPS를 사용하기 위한 인증서 적용 필요
- HTTPS를 사용하지 않는 레지스트리 컨테이너에 접근하지 못하도록 설정

### Not use HTTPS
- 도커 데몬에 --insecure-registry 옵션을 추가
- 인증 없이 레지스트리 컨테이너에 접근 가능
- 미리 정의된 계정으로 로그인하도록 설정함으로써 접근을 제한 가능

```shell


# deamon reload
$ sudo systemctl daemon-reload

# docker restart
$ sudo systemctl restart docker
```

## SSL 인증서 생성 및 적용

- Registry가 원격지에 있는 경우, 클라이언트와 registry 서버는 https 프로토콜을 사용
- SSL 인증서를 생성하고 설치 필요
- SSL 인증서에 대한 자세한 내용은 https://5equal0.tistory.com/9?category=727993를 참고
- 무료 SSL 인증서 사용을 위해 자체서명인증서를 생성하여 registry 컨테이너에 적용

## 1. SSL 인증서 생성

- '/home/user/docker/registry/certs' 디렉토리에 SSL 인증서와 관련된 파일을 저장
- 인증서 위치는 고정이 아님

```shell
$ mkdir /home/user/docker/registry/certs
$ cd /home/user/docker/registry/certs
```

### 1.1. Private Key를 생성

(pass phrase로 임의의 암호를 사용 : Password)

```shell
$ openssl genrsa -des3 -out server.key 2048
```

### 1.2. CSR 파일을 생성

- CN 값에 registry를 위치할 Host_Server_IP 주소 (또는 도메인명)을 입력
- 다른 값은 공백이여도 상관 없음
- server.key 파일을 개인 키로 사용하여 server.csr 파일에 새 CSR을 생성
```shell
$ openssl req -new -key server.key -out server.csr
```
-  OpenSSL을 사용하여 새 인증서 서명 요청(CSR)을 생성하는 명령어
- `openssl req` 명령어는 OpenSSL에서 CSR을 생성
- `-new` 옵션은 새로운 CSR을 생성하겠다는 것을 의미
- `-key` 옵션은 CSR에 사용될 개인 키 파일을 지정
- `-out` 옵션은 생성된 CSR을 저장할 파일 이름을 지정

### 1.3. Key 파일의 암호화를 해제
- OpenSSL에서 생성된 개인 키 파일(server.key)을 읽어와 RSA 알고리즘으로 암호화된 개인 키 파일(server.key)을 생성
```shell
$ openssl rsa -in server.key -out server.key
```
- `openssl rsa` 명령어는 OpenSSL에서 RSA 알고리즘으로 암호화된 개인 키 파일을 생성
- `-in` 옵션은 읽어올 개인 키 파일을 지정
- `-out` 옵션은 생성된 개인 키 파일을 저장할 파일 이름을 지정

### 1.4. SAN 설정을 위한 Config 파일을 생성
-  OpenSSL에서 사용되는 확장 파일(extfile.cnf)을 생성
```shell
# registry 컨테이너가 있는 master node IP
$ echo subjectAltName=IP:{registry_Server_IP},IP:127.0.0.1 > extfile.cnf
```
- 위 설정을 안하면 후에 이미지 push 시도 시 에러 발생
- `subjectAltName` 필드에 `IP:{registry_Server_IP},IP:127.0.0.1` 값을 가지는 `extfile.cnf` 파일을 생성
- `subjectAltName` 필드는 SSL/TLS 인증서에서 사용되는 필드 중 하나
  - 인증서가 발급된 도메인 이름이 아닌 IP 주소로 인증서를 사용할 때 사용
- `{registry_Server_IP}` 부분을 실제 레지스트리 서버의 IP 주소로 대체하여 사용

### 1.5. 자체서명인증서를 생성

```shell
$ openssl x509 -req -days 800 -signkey server.key -in server.csr -out server.crt -extfile extfile.cnf
```

## 2. SSL 인증서 적용 [Master(Leader) Node]

- SSL 인증서를 생성 후 인증서를 레지스트리에 접근하고 싶은 모든 서버에 적용
- Master(Leader)에서는 생성된 인증서를 사용해 registry 컨테이너를 다시 시작

### 2.1. 이미 registry가 존재하면 멈추고 삭제

```shell
$ docker stop {registry_Container_Name} && docker rm {registry_Container_Name}
```

### 2.2. 새로운 Registry를 실행

- `-v` 옵션으로 인증서가 저장되어있는 '~/certs' 디렉토리를 registry 컨테이너의 '/cert' 경로로 마운트
- Registry 컨테이너의 환경변수로 SSL key 및 인증서 위치를 전달

```shell
# registry 컨테이너가 있는 master node에서 
$ docker run -d \
> --name={registry_Container_Name} \
> -v {host_Cert_Dir}:{registry_Cert_Dir} \
> -e REGISTRY_HTTP_ADDR=0.0.0.0:5000 \
> -e REGISTRY_HTTP_TLS_CERTIFICATE={registry_Cert_Dir}/server.crt \
> -e REGISTRY_HTTP_TLS_KEY={registry_Cert_Dir}/server.key -p 5000:5000 registry

$ docker run -d --name=registry -v /home/user/docker/registry/certs:/certs -e REGISTRY_HTTP_ADDR=0.0.0.0:5000 -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/server.crt -e REGISTRY_HTTP_TLS_KEY=/certs/server.key -p 5000:5000 registry
```

- `-d`: 컨테이너를 detached 모드로 실행
- `--name={registry_Container_Name}`: 컨테이너의 이름을 설정
- `-v {host_Cert_Dir}:{registry_Cert_Dir}`: 호스트 머신의 디렉토리를 컨테이너 내부의 디렉토리와 공유
- `-e REGISTRY_HTTP_ADDR=0.0.0.0:5000`: 레지스트리 서버의 주소를 설정(호스트IP가 아닌 0.0.0.0으로 설정)
  - 0.0.0.0:5000으로 설정하면 Docker 레지스트리가 컨테이너 내부에서 사용 가능한 모든 IP 주소와 5000 포트를 사용
  - 호스트 IP와 관계없이 컨테이너 내부에서 Docker 레지스트리에 접근 가능
- `-e REGISTRY_HTTP_TLS_CERTIFICATE={registry_Cert_Dir}/server.crt`: TLS 인증서 파일의 경로를 설정
- `-e REGISTRY_HTTP_TLS_KEY={registry_Cert_Dir}/server.key` : TLS 인증서의 개인 키 파일의 경로를 설정
- `-p 5000:5000`: 호스트 머신의 5000번 포트와 컨테이너 내부의 5000번 포트를 연결

## 3. SSL 인증서 적용

- 원격 Registry에 이미지를 업/다운 로드 하려는 노드에서의 SSL 인증서 설정을 수행

### 3.1. Master에서 Worker로 인증서 이동
-  `scp`를 통해 Registry서버(master)에서 생성했던 SSL 인증서를 클라이언트(worker)로 이동
```shell
$ scp {master_node_ID}@{master_node_IP}:{master_Dir} {worker_Dir}
```

### 3.2. SSL 인증서를 Worker Node에 적용

- SSL 인증서를 시스템의 인증서 보관 폴더로 이동합니다.

```shell
$ cp ~/docker-registry/cert/server.crt /etc/pki/ca-trust/source/anchors/ 
$ update-ca-trust
```

### 3.3. Worker Node의 도커 데몬을 restart
```shell
$ sudo service docker restart
```

## 4. Image Pull/Push/Delete

- 업로드할 이미지의 tag를 {registryServerIP}:{registryServerPort}/{imageName} 과 같은 형식으로 변경

```shell
$ docker tag {Image_1} {registry_Server_IP}:{registry_Server_Port}/{Image_2}
```

- docker push 명령을 통해 해당 이미지를 원격 registry로 업로드 합니다.

```shell
$ docker push {Image}
```

- registry 서버 주소를 입력하실때 https를 사용

```shell
$ curl -X GET https://{Registry_Server_IP}:{Registry_Server_Port}/v2/_catalog
```

- Docker registry에 있는 이미지 삭제
```shell
$ $ curl -X DELETE https://{Registry_Server_IP}:{Registry_Server_Port}/v2/{Image_name}/manifests/{Image_tag}
```