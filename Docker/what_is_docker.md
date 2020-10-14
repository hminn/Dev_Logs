## What is Docker?

> #### 도커(docker)란?
>
> : **컨테이너 기반의 오픈소스 가상화 플랫폼**



> #### 컨테이너(Container)?
>
> - 컨테이너는 격리된 공간에서 프로세스가 동작하는 기술입니다.
>
> - 가상화 기술의 하나지만 기존방식과는 차이가 있습니다.
>
> - **기존의 가상화 방식과의 차이점?**
>
>    기존의 가상화 방식은 주로 **OS를 가상화**하였습니다.
>
>   우리에게 익숙한 [VMware](http://www.vmware.com/)나 [VirtualBox](https://www.virtualbox.org/)같은 가상머신은 호스트 OS위에 게스트 OS 전체를 가상화하여 사용하는 방식입니다. 이 방식은 여러가지 OS를 가상화(리눅스에서 윈도우를 돌린다던가) 할 수 있고 비교적 사용법이 간단하지만 무겁고 느려서 운영환경에선 사용할 수 없었습니다.
>
>   이러한 상황을 개선하기 위해 CPU의 가상화 기술([HVM](https://en.wikipedia.org/wiki/Hardware-assisted_virtualization))을 이용한 [KVM](http://www.linux-kvm.org/)(Kernel-based Virtual Machine)과 [반가상화](https://en.wikipedia.org/wiki/Paravirtualization) Paravirtualization방식의 [Xen](https://www.xenproject.org/)이 등장합니다. 이러한 방식은 게스트 OS가 필요하긴 하지만 전체OS를 가상화하는 방식이 아니였기 때문에 호스트형 가상화 방식에 비해 성능이 향상되었습니다. 이러한 기술들은 [OpenStack](https://www.openstack.org/)이나 AWS, [Rackspace](https://www.rackspace.com/)같은 클라우드 서비스에서 가상 컴퓨팅 기술의 기반이 되었습니다.
>
>   전가상화든 반가상화든 추가적인 OS를 설치하여 가상화하는 방법은 어쨋든 성능문제가 있었고 이를 개선하기 위해 **프로세스를 격리** 하는 방식이 등장합니다.
>
>   리눅스에서는 이 방식을 리눅스 컨테이너라고 하고 단순히 프로세스를 격리시키기 때문에 가볍고 빠르게 동작합니다. CPU나 메모리는 딱 프로세스가 필요한 만큼만 추가로 사용하고 성능적으로도 거의 손실이 없습니다.
>
>   하나의 서버에 여러개의 컨테이너를 실행하면 서로 영향을 미치지 않고 독립적으로 실행되어 마치 가벼운 VM(Virtual Machine)을 사용하는 느낌을 줍니다. 실행중인 컨테이너에 접속하여 명령어를 입력할 수 있고 `apt-get`이나 `yum`으로 패키지를 설치할 수 있으며 사용자도 추가하고 여러개의 프로세스를 백그라운드로 실행할 수도 있습니다. CPU나 메모리 사용량을 제한할 수 있고 호스트의 특정 포트와 연결하거나 호스트의 특정 디렉토리를 내부 디렉토리인 것처럼 사용할 수도 있습니다.
>
> 
>
> #### Docker 컨테이너 관련 커맨드
>
> - 컨테이너 조회 : `docker ps`
>
>   > `-a` 옵션을 사용하면 현재 중지되어 있는 컨테이너까지 함께 출력
>   > `-s` 옵션을 사용하면 각 컨테이너의 디스크 사용량까지 확인 가능
>   > `-f` 옵션을 사용하면 조회 결과를 특정 조건에 따라 필터링해서 볼 수 있다.
>   >
>   > > 만약, 특정 이미지로 부터 만들어진 컨테이어만 보고 싶다면 `ancestor` 필터를 사용하면 된다.
>   > > `docker ps -af "ancestor=python:alpine"`
>
> - 컨테이너 실행 : `docker run`
>   형식 : `docker run [OPTIONS] IMAGE[:TAG|@DIGEST] [COMMAND] [ARG...]`
>
>   다음은 자주 사용하는 옵션들이다.
>
> | 옵션  | 설명                                                   |
> | :---- | :----------------------------------------------------- |
> | -d    | detached mode 흔히 말하는 백그라운드 모드              |
> | -p    | 호스트와 컨테이너의 포트를 연결 (포워딩)               |
> | -v    | 호스트와 컨테이너의 디렉토리를 연결 (마운트)           |
> | -e    | 컨테이너 내에서 사용할 환경변수 설정                   |
> | –name | 컨테이너 이름 설정                                     |
> | –rm   | 프로세스 종료시 컨테이너 자동 제거                     |
> | -it   | -i와 -t를 동시에 사용한 것으로 터미널 입력을 위한 옵션 |
> | –link | 컨테이너 연결 [컨테이너명:별칭]                        |
>
> - 컨테이너 중지 : `docker stop`
>
> - 컨테이너 시작 : `docker start`
>
> - 컨테이너 상대로 명령어 실행 : `docker exec`
>
>   > `-it` 옵션을 사용하면,
>   >  해당 컨테이너의 쉘(shell)과 작접 상호작용하면서 연속으로 명령어를 실행할 수 있습니다.
>   >
>   > ```bash
>   > $ docker exec -it amazing_chatelet /bin/sh
>   > / # ls
>   > bin    etc    lib    mnt    proc   run    srv    tmp    var
>   > dev    home   media  opt    root   sbin   sys    usr
>   > / # touch test.txt
>   > / # ls test.txt
>   > test.txt
>   > / #
>   > ```
>
> - 컨테이너 로그 확인 : `docker logs`
>
> - 컨테이너 제거 : `docker rm`
>
>   > `-f` 옵션을 사용하면 해당 컨테이너를 먼저 정지시킨 다음에 제거해줍니다.



> #### 이미지(Image)?
>
> - 도커에서 가장 중요한 개념은 컨테이너와 함께 이미지라는 개념입니다.
> - 이미지는 **컨테이너 실행에 필요한 파일과 설정값등을 포함하고 있는 것**으로 상태값을 가지지 않고 변하지 않습니다(Immutable).
> - 컨테이너는 이미지를 실행한 상태라고 볼 수 있고 추가되거나 변하는 값은 컨테이너에 저장됩니다.
> - 같은 이미지에서 여러개의 컨테이너를 생성할 수 있고 컨테이너의 상태가 바뀌거나 컨테이너가 삭제되더라도 이미지는 변하지 않고 그대로 남아있습니다.



> #### Docker Hub
>
> - 도커 이미지의 용량은 보통 수백 메가로 수 기가가 넘는 경우도 흔하다. 이렇게 큰 용량의 이미지를 서버에 저장하고 관리하는 것은 쉽지 않은데 도커는 **Docker hub**를 통해 공개 이미지를 무료로 관리해 줍니다.
> - [Docker hub](https://hub.docker.com/)에서 공개된 이미지를 다운받아 사용하거나, [Docker Registry](https://docs.docker.com/registry/) 저장소를 직접 만들어 관리할 수 있다.
> - 현재 공개된 도커 이미지는 50만개가 넘고 Docker hub의 이미지 다운로드 수는 80억회에 이른다고 한다. 누구나 쉽게 이미지를 만들고 배포할 수 있습니다. 이 모든게 무료.
>   
>
> #### Dockerfile
>
> - 도커는 이미지를 만들기 위해 `Dockerfile`이라는 파일에 자체 DSL(Domain-specific language)언어를 이용하여 이미지 생성 과정을 적습니다.
> - 이것은 굉장히 간단하지만 유용한 아이디어인데, 서버에 어떤 프로그램을 설치하려고 이것저것 의존성 패키지를 설치하고 설정파일을 만들었던 경험이 있다면 더 이상 그 과정을 블로깅 하거나 메모장에 적지 말고 `Dockerfile`로 관리하면 됩니다. 이 파일은 소스와 함께 버전 관리 되고 원한다면 누구나 이미지 생성과정을 보고 수정할 수 있습니다.
> - `dockerfile`은 도커 명령어를 순서에 따라 빌드하며, `dockerfile`을 빌드할 때(이미지 파일로 변환 시킬 때)는 **layer 구조**를 보인다. 이미지가 계층적으로 하나씩 쌓이면서 생성되는 것이다.
>
> 
>
> ##### Dockerfile 작성예시 및 명령어
>
> ```dockerfile
> FROM	debian:buster
> 
> LABEL	maintainer="hyeokim@student.42seoul.kr"
> 
> RUN	apt-get update && apt-get install -y \
> 	nginx \
> 	mariadb-server \
> 	php-mysql \
> 	php-mbstring \
> 	openssl \
> 	vim \
> 	wget \
> 	php7.3-fpm
> 
> COPY	./srcs/run.sh ./
> COPY	./srcs/default ./tmp
> COPY	./srcs/wp-config.php ./tmp
> COPY	./srcs/config.inc.php ./tmp
> 
> EXPOSE	80 443
> 
> CMD 	bash run.sh
> ```
>
> - ##### FROM
>
>   - 유효한 Docker 파일은 FROM 명령으로 시작해야 한다.
>   - 새 작업을 시작할 베이스 이미지를 지정한다.
>   - 우리 과제에서는 `debian:buster`로 설정.
>
> - ##### LABEL
>
>   - 이미지에 메타데이터를 추가한다.
>
>     - 이미지의 버전 정보, 작성자, 코멘트와 같이 이미지 상세 정보를 작성해두기 위한 명령.
>
>   - 아래 명령어로 이미지의 메타데이터를 확인할 수 있다.
>
>     ```bash
>     docker image inspect --format="{{ .Config.Lables }}" [이미지명]
>     ```
>
> - ##### RUN
>
>   - **새 이미지 레이어를 만들어 내** 명령을 실행하고 결과를 커밋한다.
>   - 백슬래시(`\`)를 사용하여 다음 줄에 RUN 명령을 계속할 수 있다.
>   - *주의* : 항상 `apt-get update` 와 `apt-get install`는 같은 RUN 실행줄에서 동시에 실행해 캐싱 문제를 방지. (같은 결과를 가져오더라도 RUN을 여러줄로 작성하면 image layer가 여러개 생성되고, RUN을 한줄로 작성하면 image layer가 하나 생성된다.)
>
> - ##### COPY
>
>   - 호스트OS의 파일 또는 디렉토리를 컨테이너 안의 경로로 복사한다.
>   - ft_server 과제를 위해 수정해줬던 설정 파일들을 이 명령어로 미리 src 폴더에 넣어둘 것이다.
>
> - ##### EXPOSE
>
>   - 해당 컨테이너가 런타임에 지정된 네트워크 포트에서 수신 대기중 이라는것을 알려준다.
>
>   - 일반적으로 dockerfile을 작성하는 사람과 컨테이너를 직접 실행할 사람 사이에서 공개할 포트를 알려주기 위해 문서 유형으로 작성할 때 사용한다.
>
>   - 이 명령 자체가 작성된 포트를 실행하여 listening 상태로 올려주거나 하지는 않기 때문에, 실제로 포트를 열기 위해선 container run 에서 -p 옵션을 사용해야 한다.
>
>     ```bash
>     docker run -p 80:80/tcp -p 80:80/udp ...
>     ```
>
>   - 프로토콜을 지정하지 않으면 기본값은 TCP.
>
> - ##### CMD
>
>   - 생성된 컨테이너를 실행할 명령어를 지정한다.
>   - 도커 파일에 CMD가 두 개 이상 있는 경우 마지막 CMD만 유효하다.



##### 출처

- [https://velog.io/@hidaehyunlee/ftserver-%EC%84%A0%ED%96%89%EC%A7%80%EC%8B%9D-Docker-Debian-Buster-Nginx-](https://velog.io/@hidaehyunlee/ftserver-선행지식-Docker-Debian-Buster-Nginx-)