# Docker


# What is Docker?

![스크린샷 2023-05-11 오후 7.49.25.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d631f745-7cb7-4e12-b02e-bdfaad4fcb09/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-05-11_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_7.49.25.png)

도커는 컨테이너 기술로 컨테이너를 생성하고 관리하기 위한 도구

# Container

* 소프트웨어 개발에서 컨데이너는 표준화된 소프트웨어 유닛을 의미
* 기본적으로 코드 패키지
* 해당 코드를 실행하는데 필요한 종속성과 도구가 포함

<aside>
💡 어플리케이션을 구축하는 경우 서버에서 코드를 실행할 수 있고 도커로 빌드된 컨테이너가 있는 이러한 애플리케이션이 있는 경우 그 컨테이너에는 애플리케이션 소스 코드 뿐만 아니라 런타임 그리고 코드를 실행하는데 필요한 기타 도구가 있을 수 있다.

</aside>

동일한 Node.js코드와 동일한 Node.js 도구를 사용하는 동일한 컨테이너는 항상 동일한 버전을 사용하는 동일한 Node.js 런타임에 항상 동일한 결과를 제공한다는 장점이 있다. 그저 컨테이너에 모두 넣는 것으로 항상 동일하다.

# Docker = Container

Docker과 Container는 같은  개념이다. 표준화된 컨테이너를 가지고 있으면 그 컨테이너 안에 다양한 것들을 넣고 자체적으로 보관 및 격리한다. 즉, 컨테이너끼리 섞을 수 없다. Docker도 마찬가지로 소프트웨어 유닛, 코드가 포함된 패키지 및 코드를 실행하는 종속성을 보관하고 Docker이 실행되는 모든 곳에서 가져올 수 있다.

이렇게 도커가 실행되는 동일한 환경에서 동일한 어플리케이션을 실행할 수 있다. 모든 것이 Container에 있기 때문에 어플리케이션을 실행하려는 위치에 추가 도구를 설치하는 것에 대해 걱정할 필요가 없다. Docker이 Container이고 Container이 Docker이다. Docker은 그저 Container을 구축하기 위한 도구일 뿐이다.

Docker의 장점은 Container의 지원이 최신OS에 내장되어 있거나 시작하기 쉽고 Docker를 모든 최신 OS에 설치하여 작업할 수 있다는 것이다. 결국 Dockerd는 컨테이너의 생성 및 관리 프로세스를 단순화하는 도구이다.

<aside>
💡 Container를 만들지 않아도 되지만, Container를 만드는 것이 작업을 간단하게 만들기 때문에 사실상 표준이다.

</aside>

# Why use Docker as a container? Problem Solution

* 소프트웨어 개발에 도커가 중요한가? **Yes**

## When distributing the application

왜 소프트웨어 개발에서 독립적으로 표준화된 어플리케이션 패키지를 원하는지 알아야 한다. 우리는 각기 다른 개발 환경에 있다. 어떤 **최신버전의 언어와 도구**로 개발하고 성공적으로 실행되는 경우 이전에 버전에서는 실할할 수 없는 문법이나 기능이 있을 것이다. 여기서 문제는 그 최신버전이 로컬 개발환경에만 적용되어있을 수 있다는 것이다. 하지만 우리는 이 응용 프로그램을 가져와 호스트되어야 하는 서버의 일부 원격 시스템에 배포아여 모든 사용자가 사용가능하고 연결할 수 있도록 하는 경우 해당 원격 시스템에는 어떤 버전이 설치되어 있는지 모르고, 최신 버전이 아니라면 실행이 불가능할 수 도 있다.

이렇게 실제로 개발 로컬 환경에서 잘 작동하는 코드가 원격에서는 더 이상 작동하지 않고, 무엇이 문제인지 무엇이 잘못되었는지 문제를 파악하는데 상당한 시간이 걸릴 수 있다. 이런 문제를 해결하기 위해 개발환경과 동일한 환경을 갖는데에 매우 큰 의미가 있다. **특정 노드 버전을 도커 컨테이너에 고정(lock)**할 수 있기에 코드가 항상 정확한 버전으로 실행할 수 있게 만들 수 있다.  어플리케이션이 자체적으로 필요한 버전을 제공하는 컨테이너에서 실행할 수 있어 이와 같이 잠재적인 문제들은 사라질 수 있게 된다.

## Each development environment within a team or company

한 팀 내에 속해 있어 동일한 프로젝트를 수행하는 경우, 각각의 다른 버전의 사용하고 있을 수 있다. 이때 서로 코드를 공유하면 하위 버전을 사용하는 사람들에게는 작동하지 않을 수 있다. 하위 버전을 사용하느 사람들이 최신 버전으로 업데이트 하는 것은 문제가 되지 않으며. 시스템에서 쉽게 업데이트를 할 수 있다.

관리하고 설치해야하는 더 복잡한 종속성이 있는 더 복잡한 프로젝트가 있을 경우 모든 사람이 같은 코드 기반에서 함께 작업할 수 없다는 잠재적인 문제점이 존재하고 같은 환경을 사용할 것이라는 보장도 없다. 개발에 있어서는 이러한 **재현성**을 원하므로 현재 배포하지 않은 경우에도 **컨테이너에 코드가 필요로 하는 모든 것을 포함하는 환경을 보유하는데 매우 큰 의미**가 있다.

## If your or our project has multiple versions

혼자 작업하는 프로젝트가 여러 개일 경우 충돌하는 버전이 있을 수 있다. 어떤 프로젝트에서 어떤 이류도든 각 프로젝트마다 다른 버전의 언어를 사용하고 있을 수 있다. 이럴 경우 프로젝트 A에서 프로젝트 B로 전환할 때마다 잘못된 버전을 제거하고 올바른 버전을 설치해야 한다.

이렇게 프로젝트를 전환하는 경우 버전의 업그레이드 혹은 다운그레이드가 필요할 수 있다. 따라서 프로젝트 간에 전환이 매우 번거럽기에 도커와 컨테이너를 사용하면 각각의 프로젝트에 그들만의 컨테이너가 존재하도록 도와준다. 따라서 프로젝트를 전환하면 그대로 작동하는 것이다.

호스트 컴퓨터가 아닌, 컨테이너에 모든 것이 있기 때문에 매번 제거하고, 다시 설치할 필요가 없는 것이다. 다른 컨테이너를 시작하는 것으로 쉽게 프로젝트를 전환 가능하다. 이런 문제들을 해결하는데 컨테이너 작업을 고려해야하고 도커가 컨테이너에 매우 유용한 도구인 것이다.

# Docker vs Virtual Operating Systmes

* Why Docker?
  → 왜 도커와 컨테이너인가? 그게 문제가 아닌가?
  → 재현 가능한 문제는 Virtual machine으로 해결할 수 있는 것이 아닌가?

## Virtual Operating Systems

![슬라이드1.PNG](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d2b58fdd-1d5c-42d0-9909-706951751697/%EC%8A%AC%EB%9D%BC%EC%9D%B4%EB%93%9C1.png)

호스트 운영체제에서 독립적인 자체 쉘을 지닌 캡슐화된 가상OS를 지닌 virtual machine이 솔루션션일 수 있다. virtual machine을 사용하는 것은 호스트 OS(Linux, Window, Mac OS)가 있고, 그 위에 virtual machine를 설치하는 것이다. 컴퓨터 내부에 컴퓨터를 설치하는 것이다. virtual machine내부에서 실행되는 자체 OS인 가상 OS를 리눅스라고 가정할 때 virtual machine은 컴퓨터와 동일하기 때문에 가상머신 내부에서 에뮬레이트가 가능하고, 추가 도구를 설치하느 것도 가능하다. 이것의 의미는 가상으로 존재하지만 다른 머신이기 때문에 필요한 것들을 따로 설치해야하는 것이다. 필요한 모든 라이브러리, 종속성 및 도구를 설치하고 소스코드를 그 위치로 이동 가능하다. 따라서 프로그램에 필요한 모든 것과 거기에 설치되는 모든 도구가 포함된 캡슐화된 버츄얼 머신이기 때문에 도커, 컨테이너와 동일한 결과를 얻을 수 있다. 모든 것이 보유된 캡슐화된 환경인 경우이다. 이후 서로 다른 프로젝트에 대해 이런 환경을 여러개 가질 수 있다. 또는 버츄얼 머신 구성을 동료와 공유하며 동일한 환경에서 작업하고 있는지 확인 가능하다.

하지만 이런 방식에는 문제가 있다. 가장 큰 문제는 가상 운영체제를 지닌 여러 virtual machine에서 발생하는  overhaed이다. 모든 virtual machine은 실제로 우리의 machine 위에서 실행되는 standalone computer(독립적인 컴퓨터)와 동일하다. 특히 이런 machine이 여러개인 경우 매번 새로운 컴퓨터를 머신 내부에 설치해야하고 CPU, Memory, Hard drive를 당비하게 된다. 이렇게 System Virtual machine이 점점더 많아진다면 문제가 되는 것이다.

동일하게 복제되는 많은 것들이 여전히 존재한다. 특히 OS가 그렇다. 모든 Virtual machine에서 리눅스를 사용한다 해도 여전히 모든 머신에 별도로 설치되어 있고 많은 공간을 낭비한다. 또한 모든 virtual machine에 다른 많은 도구도 설치되어 있고 직접적으로 어플리케이션에 필요하지는 않지만 여전히 디폴트로 설정되어 있어 무넺가 될수 있다

즉, Virtual machine에는 몇 가지 장단점이 있다.

* 장점
  * 분리된 환경 생성
  * 분리된 환경 내에 환경별 구성 가능
  * 모든 것을 안정적으로 공유하고 재생산 가능
* 단점
  * 중복 복제로 인한 공간 낭비
  * 호스트 시스템 위에서 작동해 성능 감소
  * 여러개의 Virtual machine가 존재할 경우 각각을 정확히 동일한 방식으로 설정
    → 공유가능한 단일 구성 파일이 없다

여기서 도커와 컨테이너를 사용하는 명확한 이유가 드러나게 된다. 개발에서 제품 생산으로 어플리케이션을 배포하려면 Virtual machine과 동일한 방식으로 프로더션 머신을 구성해야 한다. 혹은 프로덕션 머신에서 Virtual machine을 실행하지만 성능이 낭비되므로 프로덕션에서는 같은 환경으로 구성하고 싶어하지 않을 수 있다. 따라서 문제를 해결하기 위한 완벽한 방법은 아닌 것이다.

## Docker

![슬라이드2.PNG](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/89fa3e74-5c98-4ad8-9209-69fe56dcaca4/%EC%8A%AC%EB%9D%BC%EC%9D%B4%EB%93%9C2.png)

실질적으로 Container가 핵심 개념이 되는 것이다. Docker는 Container을 만들고 관리하귀 위한 사실상의 표준 도구일 뿐이다. Container가 Virtual machine보다 더 나은 방식으로 Docker가 도와주는 방법은 Container를 사용하면 여전히 호스트 OS(Linux, Window, Mac OS)가 존재하지만 하나의 머신에 여러개의 머신을 설치하지 않는다. 그 대신 우리는 OS가 기본적으로 내재하고 있거나 컨테이너 에뮬레이트를 지원하는 내장 컨테이너를 활용한다. 여기서 Docker가 작동하도록 처리한다. 그 위에 Docker Engine리라는 도구를 실행한다. 뿐만 아니라 우리가 그것을 설치할때 Docker에 의해 모두 설정된다. 이 후 현재 시스템에서 실행되는 도커 엔진을 기반으로 Container를 가동할 수 있다

Docker Engine은 하나의 도구에 불과하며, 여기에는 하나의 가벼운 도구가 있을 뿐이고 여러개의 Container로 분리할 수 있다. 이러한 Container에는 코드와 코드에 필요한 도구 및 런타임이 포함되어있지만 운영체제나, 수많은 추가 도구 또는 유사한 것을 포함하지 않는다. 컨테이너 내부에 작은 운영체제 레이어가 있을 수는 있지만 Virtual machine에 설치하는 것보다 훨씬 작은 OS의 가벼운 버전일뿐이다.

Container의 또 다른 장점은 구성 파일을 사용하여 컨테이너를 구성하고 설명 가능하다. 이후 파일을 공유해 다른 사람들이 컨테이너를 다시 만들수 있도록 하거나, 컨테이너 이미지를 빌드할 수 있다. 그 이미지를 다른 사람과 공유하여 모든 사람이 자신의 시스템에서 동일한 컨테이너를 시작하는 것이 가능하다.

## Docker vs Virtual machine

![슬라이드2.PNG](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fde78579-4fde-4345-911f-4391c14763e5/%EC%8A%AC%EB%9D%BC%EC%9D%B4%EB%93%9C2.png)

컨테이너의 주요 이점 중 하나는 운영 체제 및 시스템 리소스에 미치는 영향이 적다는 것입니다. 컨테이너는 가볍고 빠르도록 설계되었으므로 상당한 시스템 리소스 없이 애플리케이션의 여러 인스턴스를 실행할 수 있습니다. 또한 전체 운영 체제가 아닌 이미지 및 구성 파일에 의존하므로 최소한의 디스크 공간을 사용하며 이미지와 구성파일이 있기 때문에 다양한 환경에서 애플리케이션을 쉽게 공유, 재구축 및 배포할 수 있습니다. 개발자는 애플리케이션과 종속성을 컨테이너 이미지로 패키징할 수 있으며, 그런 다음 다른 호스트나 클라우드 플랫폼 간에 쉽게 이동할 수 있습니다. 컨테이너를 필요에 따라 쉽게 복제하고 배포할 수 있으므로 애플리케이션을 훨씬 더 간단하게 관리하고 확장할 수 있습니다.

컨테이너는 또한 기존 가상 머신보다 더 나은 캡슐화를 제공합니다. 전체 운영 체제와 하드웨어 스택을 캡슐화하는 가상 머신과 달리 컨테이너는 애플리케이션과 해당 종속성만 캡슐화합니다. 이를 통해 종속성을 보다 쉽게 관리하고 동일한 호스트에서 실행되는 서로 다른 애플리케이션 간의 충돌을 피할 수 있습니다.

반대로 가상 머신은 컨테이너보다 느리고 더 많은 디스크 공간을 차지하는 경향이 있습니다. 가상 머신도 공유, 재구축 및 배포할 수 있지만 컨테이너보다 더 까다롭고 시간이 많이 소요될 수 있습니다. 가상 머신은 앱과 앱이 실행하는 데 필요한 것을 캡슐화하는 것이 아니라 전체 컴퓨터를 캡슐화하므로 훨씬 더 큰 스토리지 공간을 차지할 수 있습니다.

전반적으로 컨테이너는 기존 가상 머신보다 애플리케이션 배포에 대해 더 효율적이고 유연한 접근 방식을 제공합니다. 경우에 따라 가상 머신이 여전히 유용할 수 있지만 컨테이너는 빠르게 많은 개발자와 IT 전문가가 선호하는 선택이 되고 있습니다. 아직 컨테이너를 사용하고 있지 않다면 이 기술이 조직에 어떤 이점을 줄 수 있는지 살펴보는 것이 좋습니다.

# Docker setup

![슬라이드4.PNG](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e6ebd028-92e4-4428-8aa3-ab6ac1632ffb/%EC%8A%AC%EB%9D%BC%EC%9D%B4%EB%93%9C4.png)

## Docker setup on Linux(CentOS)

```bash
# Docker 설치
# <https://docs.docker.com/engine/install/centos/> 사이트 자료를 참고하여 설치
yum -y update
yum install -y yum-utils
 
# Docker repository 시스템에 추가
yum-config-manager --add-repo <https://download.docker.com/linux/centos/docker-ce.repo>
yum-config-manager --enable docker-ce-nightly
 
# 최신버전의 Docker 설치(Install Docker Engine)
yum -y install docker-ce docker-ce-cli containerd.io
 
# Docker 데몬 시작 및 부팅 시 Docker 데몬 자동 시작
systemctl start docker
systemctl enable docker
 
# Docker 실행중인지 확인
systemctl status docker

# 실행 중인 컨테이너를 나열
docker container ls
 
# Docker Hub 저장소에서 이미지 검색
docker search centos
 
# CentOS 7의 공식 빌드를 다운로드 받고 싶다면 image pull 하위 명령을 사용하여 이를 수행
docker image pull centos
# 위 명령어로 하면 CentOS 8.X가 설치
# centOS 7 설치
docker image pull centos:7

docker image ls
 
# docker 이미지 목록 보기
docker images
 
```

```bash
#################################################################################
# CentoOS 이미지를 기반으로 도커 컨테이너를 시작
# docker container run -it centos:7 /bin/bash 를 하면 centos:latest 로 접속된다.
# 즉, centos:8로 접속된다.
# --privileged 옵션과 -d 옵션으로 /sbin/init을 실행한후 exec로 /bin/bash를 실행시켜야 한다
 
docker run --name mycentos -p 8022:22 -p 8080:80 -p 8000:8000 \\
 --privileged -d —cap-add=SYS_ADMIN centos:7 /sbin/init
docker exec -it mycentos /bin/bash
 
# 위에 두줄로 docker 를 실행하거나 아래 세줄로 실행한다.
docker create --name mycentos -p 8022:22 -p 8080:80  -p 8000:8000 \\
 --privileged —cap-add=SYS_ADMIN centos:7 /sbin/init
docker start mycentos
docker exec -it mycentos /bin/bash
 
# -i : 연결이 종료되어도 컨테이너 상태를 유지한다.
# -t : 가상 tty를 할당한다.
# -d : 백그라운드에서 컨테이너를 실행하고 컨테이너 ID를 인쇄한다.
# -p : 호스트와 컨테이너의 포트를 포워딩한다.
# -name : 컨테이너의 이름을 지정한다.
# -v : 호스트와 컨테이너의 디렉토리를 마운트한다.
# /bin/bash : 컨테이너 생성 후 /bin/bash를 실행하여 bash 쉘을 이용할 수 있게 해 줌.
 
# 위 명령을 실행하고 나면 docker 명령어 사이트로 접속되어 있어 docker container ls 명령어는 수행되지 않는다.
# docker 접속 상태에서 빠져나오기
exit
 
# docker centos:7 으로 접속된 상태에서 CentOS 7 Python 3.6.8 설치를 한다.
# docker run 하는 시점에 포트를 바인딩 하지 않았다면 도커 container를 다시 띄워야 한다.
# 포트 추가를 미쳐 못한 경우 추가 방법 ==> 이미지를 새로 만들어 실행
docker ps -a
# STATUS 가 UP으로 보일 것이다.
docker stop mycentos
# 이미지를 다른 이미지 이름으로 저장 ==> docker commit mycentos 이미지이름
# docker 백업전 상태 저장
#sudo docker commit -p [CONTAINER ID] [NAMES]
docker commit -p mycentos mycentos7_backup
 
# docker 이미지 백업
# sudo docker save -o [저장할이름].tar [이미지 이름]
sudo docker save -o mycentos7.tar mycentos7_backup
 
 
# 새로운 container 이름 부여하고 포트 추가
docker run --privileged -d -p 8022:22 -p 8080:80 -p 8000:8000 \\
 --name mycentos7 mycentos7_backup /sbin/init
docker exec -it mycentos7 /bin/bash
 
 
 
# Failed to get D-Bus connection: Operation not permitted 
# 메시지가 나오는 것은 --privileged 옵션과 /sbin/init 을 생략했을 때 나온다.
 
 
# 정지된 컨테이너를 다시 띄울 땐 docker start로 띄움
docker start <컨테이너명>
docker start <컨테이너ID>
docker attach <컨테이너명>
# docker 컨테이너 내부로 접속
docker exec -it <컨테이너명> /bin/bash
```

## Docker SSH 접속환경 구축

```bash
###############################################################
# CentOS 컨테이너에 설치되어 있는 게 거의 없다.
yum -y install ntsysv
yum -y install initscripts && yum clean all
yum -y install net-tools
yum -y groupinstall 'Development Tools'
yum -y install sudo
yum -y install policycoreutils selinux-policy-targeted
yum -y update
 
# docker root 암호 변경 ==> SSH 에서 root 접속하기 위한 암호 설정
passwd root
 
# docker SSH 설정
yum -y install openssh-server openssh-clients openssh-askpass 
cd ~ 
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa 
cd .ssh 
cat id_rsa.pub >> authorized_keys 
mkdir /var/run/sshd 
sed -i 's/#Port 22/Port 22/g' /etc/ssh/sshd_config 
 
vi /etc/ssh/sshd_config
# PermitRootLogin yes 찾아 주석을 해제하고 저장(:wq)하고 빠져나온다.
 
service sshd start 
 
# 여기까지 하면 root 권한으로 SSH 로그인이 가능해진다.
# 가령 CentOS 7 의 IP 주소가 192.168.1.20 이라면
# docker 로 접속하기 위한 IP주소는 동일하고, Container 에 매핑한 포트가 8022이면
# 포트를 8022로 설정하고 접속하면 접속된다.
 
# docker 컨테이너 밖에서 SSH 접속 테스트 방법
# IP 172.16.100.3 인 경우
ssh -p 8022 root@172.16.100.3
```

```bash
#docker SSH 접속 테스트
#docker cpntainer 밖에서 실행

ssh -l root -p 8022 192.168.1.20
#또는
ssh -p 8022 root@192.168.1.20
```

# Docker Tools & Building Blocks

![슬라이드5.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d50db63f-391d-48c3-98e1-e20487a6f985/%EC%8A%AC%EB%9D%BC%EC%9D%B4%EB%93%9C5.png)

Docker Desktop 설치했든 Linux에 직접 설치했든 Docker Toolbox를 사용했든 Docker Engine을 설치했을 것이다. Docker Engine은 단순히 Docker를 실행하는데 필요한 Linux를 호스팅하는 가상 머신이 설정되어 있다. 명확하게 말하자면, 가상 머신이 필요한 이유는 운영체제가 기본적으로 도커를 지원하지 않기 때문이다.운영체제가 도커를 지원한다면 가상 머신은 필요가 없다. 가상 머신을 사용하지 않는 것이 이상적이지만 그렇지 않다면 도커를 실행하기 위해서는 가상머신은 필수가 된다.

![스크린샷 2023-05-21 오전 10.54.18.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8e2ec60d-e4da-41bd-bd38-6986d28c0622/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-05-21_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_10.54.18.png)

가상 머신에서 컨테이너가 실행되고 컨테이너로 작업이 가능하게 된다. docker demon은 docker Engine이라고도 하며, docker container을 만들고 실행하는  demon이다. dock toolbox는 Window나 macOS를 사용하는 사용자들이 편하게 docker를 사용하기 위해 만든 도구이다.  Docker Desktop는 docker engine과 docker compose, docker machine등을 포함한 통합 개발환경이다. 실제로 Docker Engine이 설치되고 작동하는지 그것을 확인하는 도구일 뿐이다

데몬(Daemon)이라고 불리우는 프로세스를 포함하는데 계속 실행되며 도커가 작동하는지 확인하게 된다. 말하자면 도커의 핵심고 명령줄 인터페이스가 포함되어 있다. 또한 Docker Toolbox를 사용하여 이에 접근할 수 있다.

Linux에서는 도커를 바로 설치할 수 있고 명령줄 인터페이스는 중요한 역할을 수행합니다. 왜냐면 전체 과정에서 명령을 실행하기 위해 사용할 도구이기 때문이다. 이미지와 컨테이너를 생성하고, 도커와 작동하도록 하는 명령어를 의미한다.

Docker Hub라 불리우는 서비스를 위해 아무것도 설치할 필요가 없습니다. 그저 클라우드, 웹에서 이미지를 호스팅하여 다른 시스템과 사람들에게 쉽게 공유할 수 있게 해주는 서비스다. Docker Compose라는 독립 섹션에서 Docker Compose라는 도구는 일종의 도커를 기반으로 하는 도구로  더 복잡한 컨테이너 또는 다중 컨테이너 프로젝트를 더 쉽게 관리할 수 있다. 이후 쿠버네티스에는 복잡하게 컨테이너화된 애플리케이션 배포할 때, 배포된 어플리케이션을 관리하는데 도움이 된다. 즉, 알아둬야만 하는 도구들. 그리고 그와 함께 이제 첫번째 실제 컨테이너를 생성하고 실행하기 위해 본격적으로 도커 도구를 사용해야 한다.
