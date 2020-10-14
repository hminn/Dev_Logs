## Docker-Compose

> #### What is Docker-Compose?
>
> : **Docker compose**는 yaml 파일로 여러 개의 도커컨테이너의 정의를 작성하여 한 번에 많은 컨테이너들을 작동시키고 관리할 수 있는 툴입니다.



> #### 1. Docker Compose 배경
>
> ![Image for post](https://miro.medium.com/max/1754/1*tli_E43Q1n-3m5GUAfBWew.png)
>
> : 위와 같은 개발환경의 컨테이너를 실행하기 위해서는 여러 번의 docker run 명령어를 입력해야한다.
> : 여기에 MySQL, php 등의 컨테이너가 추가로 더 올라간다고 한다면 매일 아침 출근해서 귀찮아 질 것이다.
> : 이 때, Docker Compose를 이용하면 명령어 한 번으로 컨테이너가 몇 개든 실행할 수 있다.



> #### 2. Docker Compose 예시
>
> : docker compose는 기본적으로 docker-compose.yml 파일을 기반으로 실행된다.
> : 아래 Nginx와 PHP container를 실행하는 docker-compose.yml 파일을 예시를 위해 간단히 작성해보았다.
>
> ```yaml
> version: ‘3’
> services:
>   nginx:
>     image: nginx:latest
>     ports:
>       - “80:80”
>   php:
>     image: php:7.3-fpm
>     ports:
>       - “9000:9000”
>     volumes:
>       - ./source:/source
> ```
>
> 다음은 각 항목에 대한 설명이다.
>
> - **services**: services 부터 실제 container 설정을 명시합니다.
> - **nginx:** 사용자가 지정하는 이름입니다. 저는 이름을 nginx라고 했지만 여러분이 원하시는 다른 이름으로 변경해도 상관없습니다.
> - **image**: 해당 container가 어떤 image를 기반으로 실행되는지 지정합니다. 제 옵션은 nginx:latest로 docker hub의 최신 nginx 이미지로부터 실행됩니다.
> - **ports**: container와 host 간의 공유할 포트를 지정합니다. docker compose로 container를 실행한 다음 host의 브라우저에서 localhost를 입력하면 nginx 기본 페이지가 보일겁니다.
> - **volumes**: container와 host간의 공유할 디렉토리를 지정합니다. 제 기준에선 아래의 source 디렉토리를 container에서 접근할 수 있습니다.



##### 출처

- [Docker-compose 소개](https://medium.com/sjk5766/docker-compose-%EC%86%8C%EA%B0%9C-f84840ff7203)