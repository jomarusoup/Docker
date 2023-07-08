# Docker란 무엇인가?
Docker는 컨테이너 기술을 사용하여 소프트웨어 개발 및 배포를 단순화하는 도구입니다. 컨테이너는 코드와 해당 코드를 실행하는 데 필요한 종속성 및 도구가 포함된 소프트웨어 패키지입니다. Docker를 사용하면 동일한 코드와 도구를 포함하는 컨테이너를 만들 수 있으며, 이는 Docker가 활성화된 모든 환경에서 동일한 결과를 생성하는 장점이 있습니다.

Docker를 사용하면 개발자는 소프트웨어 개발 및 배포를 위한 표준화된 환경을 구축할 수 있습니다. 이를 통해 개발자는 코드가 항상 올바른 환경에서 실행되도록 보장할 수 있습니다. 또한, Docker는 모든 OS환경에서 실행되므로 개발자는 각자의 상황에 맞는 환경에서 개발하고 테스트할 수 있습니다.

Docker는 또한 컨테이너의 생성 및 관리를 단순화하는 도구입니다. 이를 통해 개발자는 응용 프로그램을 배포할 때 독립적이고 표준화된 응용 프로그램 패키지가 필요합니다. Docker를 사용하면 범용적인 프로그래밍 언어를 Docker 컨테이너에 고정하여 코드가 항상 올바른 언어 버전에서 실행되도록 할 수 있습니다.

## Docker를 사용하는 방법

1. Docker 설치
   - Docker는 Windows, macOS, Linux 등 다양한 운영체제에서 사용 가능
   - 각 운영체제에 맞는 Docker 설치 방법을 찾아 설치
2. Dockerfile 작성
   - Dockerfile은 Docker 이미지를 만드는 데 사용되는 스크립트
   - Dockerfile을 작성하여 Docker 이미지 생성
3. Docker 이미지 빌드
   - Dockerfile을 사용하여 Docker 이미지를 빌드
4. Docker 컨테이너 실행
   - Docker 이미지를 사용하여 Docker 컨테이너를 실행

## Docker를 사용하여 개발 및 배포하는 방법
Docker를 사용하면 개발자는 동일한 환경에서 어플리케이션을 실행할 수 있으며, 이는 응용 프로그램을 배포할 때 매우 중요한 재현성을 보장합니다. 또한, Docker는 컨테이너 기술을 사용하여 소프트웨어 개발 및 배포를 단순화하는 도구입니다.

1. 개발
   - Docker를 사용하여 개발하면 동일한 환경에서 어플리케이션을 실행 가능
   - 동일한 환경에서 어플리케이션을 실행 가능한 것은 응용 프로그램을 배포할 때 매우 중요한 재현성을 보장
2. 테스트
   - Docker를 사용하여 테스트하면 응용 프로그램을 실행하는 데 필요한 모든 종속성을 포함하는 컨테이너 생성 가능
   - 응용 프로그램을 실행하는 데 필요한 모든 것이 포함된 환경을 보장 가능 
3. 배포
   - Docker를 사용하여 배포하면 응용 프로그램을 배포하는 데 필요한 모든 것을 포함하는 컨테이너를 생성 가능
   - 응용 프로그램을 배포하는 데 필요한 모든 것이 포함된 환경을 보장 가능

# Problem Solution : Docker
## When distributing the application

왜 소프트웨어 개발에서 독립적으로 표준화된 어플리케이션 패키지를 원하는지 알아야 한다. 우리는 각기 다른 개발 환경에 있다. 어떤 **최신버전의 언어와 도구**로 개발하고 성공적으로 실행되는 경우 이전에 버전에서는 실할할 수 없는 문법이나 기능이 있을 것이다. 여기서 문제는 그 최신버전이 로컬 개발환경에만 적용되어있을 수 있다는 것이다. 하지만 우리는 이 응용 프로그램을 가져와 호스트되어야 하는 서버의 일부 원격 시스템에 배포아여 모든 사용자가 사용가능하고 연결할 수 있도록 하는 경우 해당 원격 시스템에는 어떤 버전이 설치되어 있는지 모르고, 최신 버전이 아니라면 실행이 불가능할 수 도 있다.

이렇게 실제로 개발 로컬 환경에서 잘 작동하는 코드가 원격에서는 더 이상 작동하지 않고, 무엇이 문제인지 무엇이 잘못되었는지 문제를 파악하는데 상당한 시간이 걸릴 수 있다. 이런 문제를 해결하기 위해 개발환경과 동일한 환경을 갖는데에 매우 큰 의미가 있다. **특정 노드 버전을 도커 컨테이너에 고정(lock)**할 수 있기에 코드가 항상 정확한 버전으로 실행할 수 있게 만들 수 있다.  어플리케이션이 자체적으로 필요한 버전을 제공하는 컨테이너에서 실행할 수 있어 이와 같이 잠재적인 문제들은 사라질 수 있게 된다.

## Each development environment within a team or company

한 팀 내에 속해 있어 동일한 프로젝트를 수행하는 경우, 각각의 다른 버전의 사용하고 있을 수 있다. 이때 서로 코드를 공유하면 하위 버전을 사용하는 사람들에게는 작동하지 않을 수 있다. 하위 버전을 사용하느 사람들이 최신 버전으로 업데이트 하는 것은 문제가 되지 않으며. 시스템에서 쉽게 업데이트를 할 수 있다. 

관리하고 설치해야하는 더 복잡한 종속성이 있는 더 복잡한 프로젝트가 있을 경우 모든 사람이 같은 코드 기반에서 함께 작업할 수 없다는 잠재적인 문제점이 존재하고 같은 환경을 사용할 것이라는 보장도 없다. 개발에 있어서는 이러한 **재현성**을 원하므로 현재 배포하지 않은 경우에도 **컨테이너에 코드가 필요로 하는 모든 것을 포함하는 환경을 보유하는데 매우 큰 의미**가 있다.

## If your or our project has multiple versions

혼자 작업하는 프로젝트가 여러 개일 경우 충돌하는 버전이 있을 수 있다. 어떤 프로젝트에서 어떤 이류도든 각 프로젝트마다 다른 버전의 언어를 사용하고 있을 수 있다. 이럴 경우 프로젝트 A에서 프로젝트 B로 전환할 때마다 잘못된 버전을 제거하고 올바른 버전을 설치해야 한다.

이렇게 프로젝트를 전환하는 경우 버전의 업그레이드 혹은 다운그레이드가 필요할 수 있다. 따라서 프로젝트 간에 전환이 매우 번거럽기에 도커와 컨테이너를 사용하면 각각의 프로젝트에 그들만의 컨테이너가 존재하도록 도와준다. 따라서 프로젝트를 전환하면 그대로 작동하는 것이다. 

호스트 컴퓨터가 아닌, 컨테이너에 모든 것이 있기 때문에 매번 제거하고, 다시 설치할 필요가 없는 것이다. 다른 컨테이너를 시작하는 것으로 쉽게 프로젝트를 전환 가능하다. 이런 문제들을 해결하는데 컨테이너 작업을 고려해야하고 도커가 컨테이너에 매우 유용한 도구인 것이다.