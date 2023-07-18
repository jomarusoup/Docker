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
- Registry Container는 5000번 포트를 사용
```shell
$ firewall-cmd --permanent --zone=public --add-port=5000/tcp
$ firewall-cmd --reload
$ firewall-cmd --list-all
```
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
 - Container 5000번 포트와 Host의 5000번 포트를 연결

## 4. 사설 레지스트리로 이미지 업로드 및 조회
```shell
# 사설 레지스트리에 이미지 업로드를 위한 이미지 이름(태그) 지정
$ docker image tag {Image:Tag} {localhost_IP}:5000/{Image:Tag}

# 도커 엔진의 이미지 목록 확인
$ docker images

#사설 레지스트리에 이미지 업로드
$ docker image push {Host_registry_IP}:5000/{Image:Tag}

# 인증을 비활성화 하고 사설 레지스트리의 이미지 레포지토리 목록 조회
$ curl -k -X GET https://{Host_registry_IP:Port}/v2/_catalog
{"repositories":[Image_Name]}
# 인증을 비활성화 이미지의 세부 목록(태그 목록) 확인
$ curl -k -X GET https://{Host_registry_IP:Port}/v2/{Image_Name}/tags/list
{"name":"Image_Name","Tag":["1.1","1.2"]}

# --user login Image list
$ curl --user {ID:PW} -X GET https://{Host_registry_IP:Port}/v2/_catalog
# --user login Image tag list
$ curl --user {ID:PW} -X GET https://{Host_registry_IP:Port}/v2/{Image_Name}/tags/list

# 사설 레지스트리로부터 이미지 다운로드
$ docker image pull {localhost_IP:Port}/{Image:Tag}
```

- `curl`은 URL을 통해 HTTP 요청
- `-k` 옵션은 SSL 인증서 검증을 비활성화
- `-X GET` 옵션은 HTTP GET 요청


## 5. SSL 인증서 생성 및 적용(Locky & RHEL)
### 5.1. Pravate Registry Container를 위한 인증서 생성
- Nginx 서버는 443 포트를 사용하여 HTTPS 연결을 허용
- 443 포트는 Docker 로컬 레지스트리에 대한 인증을 처리하는 데 사용
```shell
$ firewall-cmd --permanent --zone=public --add-port=443/tcp
$ firewall-cmd --reload
$ firewall-cmd --list-all
```

1. 로컬 레지스트리를 HTTPS로 지원하기 위해 자체 서명 SSL/TLS 인증 기관(CA) 및 CA 인증서를 생성
```shell
# 현재 작업 디렉토리에 certs 디렉토리를 생성
$ mkdir certs

# CA의 개인 키를 생성하고 ./certs/ca.key에 저장
$ openssl genrsa -out ./certs/ca.key 2048

# 개인 키를 사용하여 자체 서명 CA 인증서를 생성하고 ./certs/ca.crt에 저장
$ openssl req -x509 -new -key ./certs/ca.key -days 10000 -out ./certs/ca.crt
```

2.  Docker 로컬 레지스트리를 HTTPS로 지원하기 위해 서버 인증서를 생성
```shell
#  ./certs 디렉토리에 2048비트 RSA 개인 키를 생성하고 ./certs/domain.key에 저장
$ openssl genrsa -out ./certs/domain.key 2048

# 개인 키를 사용하여 서버 인증서 요청(CSR)을 생성하고 ./certs/domain.csr에 저장
$ openssl req -new -key ./certs/domain.key -subj /CN=211.240.28.248 -out ./certs/domain.csr

# ./certs/extfile.cnf 파일에 IP 주소를 추가
$ echo subjectAltName = IP:211.240.28.248 > extfile.cnf

# CSR을 사용하여 CA 인증서를 사용하여 서버 인증서를 생성하고 ./certs/domain.crt에 저장
$ openssl x509 -req -in ./certs/domain.csr -CA ./certs/ca.crt -CAkey ./certs/ca.key -CAcreateserial -out ./certs/domain.crt -days 1000 -extfile extfile.cnf
```

3. Docker 로컬 레지스트리에 대한 인증을 설정하는 데 사용 
```shell
# 사용자를 위한 htpasswd 파일을 생성
$ htpasswd -c htpasswd {USER_ID}

# htpasswd 파일을 certs 디렉토리로 이동
$ mv htpasswd certs/
```

4. Docker 로컬 레지스트리를 HTTPS로 지원하기 위해 Nginx 서버를 구성
```shell
$ vi certs/nginx.conf

# Nginx 서버가 Docker 레지스트리로의 요청을 수신하고, 인증을 확인하고, 요청을 Docker 레지스트리로 전달하는 방법을 정의
# certs 디렉토리에 저장된 SSL/TLS 인증서 및 htpasswd 파일을 사용
# SSL/TLS 인증서는 HTTPS 연결을 보호하고, htpasswd 파일은 Docker 레지스트리에 대한 인증을 처리

upstream docker-registry {
    server registry:5000;
}
server {
    listen 443;
    server_name {HOST_IP};
    ssl on;
    ssl_certificate /etc/nginx/conf.d/domain.crt;
    ssl_certificate_key /etc/nginx/conf.d/domain.key;
    client_max_body_size 0;
    chunked_transfer_encoding on;
    
    location /v2/ {
    	if ($http_user_agent ~ "^(docker\/1\.(3|4|5(?!\.[0-9]-dev))|Go ).*$" ) {
        	return 404;
        }
        
        auth_basic "registry.localhost";
        auth_basic_user_file /etc/nginx/conf.d/htpasswd;
        add_header 'Docker-Distribution-Api-Version' 'registry/2.0' always;
        
        proxy_pass								http://docker-registry;
        proxy_set_header	Host				$http_host;
        proxy_set_header	X-Real-IP			$remote_addr;
        proxy_set_header	X-Forwarded-For		$proxy_add_x_forwarded_for;
        proxy_set_header	X-Forwarded-Proto	$scheme;
        proxy_read_timeout						900;
    }
}
```

4. Pravate Registry Crate
```shell
$ docker run -d --name registry --restart=always registry
```

5. Docker 로컬 레지스트리를 HTTPS로 지원하기 위해 Nginx 서버를 실행
```shell
$ docker run -d --name nginx_registry \
    -p 443:443 \
    --link registry:registry \
    -v home/user/certs/:/etc/nginx/conf.d \
    nginx
```
-  nginx_registry라는 이름의 Docker 컨테이너를 생성하고, 이 컨테이너는 registry 컨테이너와 연결
- 호스트의 443 포트와 컨테이너의 443 포트를 매핑하여 HTTPS 연결을 허용
- certs 디렉토리를 Nginx 서버의 설정 파일 디렉토리로 마운트

```shell 
#  ca.crt 파일을 /etc/pki/ca-trust/source/anchors/ 디렉토리로 복사
$ cp ca.crt /etc/pki/ca-trust/source/anchors/

# 시스템의 CA 저장소를 업데이트
$ update-ca-trust extract

# ca.crt 파일이 올바른지 확인
$ openssl verify ca.crt
ca.crt: OK
 
# Docker restart
$ service docker restart
$ docker start nginx_registry
```

7. docker login in Pravate Registry
```shell
$ docker login https://{HOST_IP}
Username: {USER_ID}
Password: 
```


### 5.2. Pravate Registry Server에 접근할 서버에 인증서 등록
- 접근할 모든 서버에 인증서 등록

1. 인증서를 생성한 서버에서 certs 디렉토리 전송
```shell
$ scp -r ./certs/ username@remote:/home/user/certs/
```

2. 인증서 복사 & 등록
```shell
$ cd /home/user/certs
$ cp ca.crt /etc/pki/ca-trust/source/anchors/
$ update-ca-trust extract

# 인증서의 유효성 검토
$ openssl verify ca.crt
ca.crt: OK
```

3. Docker restart
```shell
$ service docker restart
```

4. docker login in Pravate Registry
```shell
$ docker login https://{HOST_IP:443}
Username: {USER_ID}
Password: 
```

