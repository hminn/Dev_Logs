## What is Nginx?

> ### 사전 지식
>
> - **웹 서버가 하는 일**
>
> 1. 커넥션을 맺는다 *(클라이언트의 접속을 받아들이거나, 원치 않는 클라이언트라면 닫는다)*
> 2. 요청을 받는다 *(HTTP 요청 메세지를 네트워크로부터 읽어들인다)*
> 3. 요청을 처리한다 *(요청 메세지를 해석하고 행동을 취한다)*
> 4. 리소스에 접근한다 *(메세지에서 지정한 리소스에 접근한다)*
> 5. 응답을 만든다 *(올바른 헤더를 포함한 HTTP 응답 메세지를 생성한다)*
> 6. 응답을 보낸다 *(응답을 클라이언트에게 돌려준다)*
> 7. 트랜잭션을 로그로 남긴다 *(로그파일에 트랜잭션 완료에 대한 기록을 남긴다)*



> ### Nginx?
>
> : Nginx는 간단하게 말씀드리자면 경량 웹 서버입니다. 클라이언트로부터 요청을 받았을 때 요청에 맞는 정적 파일을 응답해주는 HTTP Web Server로 활용되기도 하고, Reverse Proxy Server로 활용하여 WAS 서버의 부하를 줄일 수 있는 로드 밸런서로 활용되기도 합니다.



> ### Nginx 특징
>
> **Apache 서버와 다른 점** 
>
> ![image-20201007171003151](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FTPBD0%2FbtqGdDFarXK%2FjRtmeHaWYBJQQxsKYa4cKK%2Fimg.png)
>
> :  Nginx는 **Event-Driven** 구조로 동작하기 때문에 한 개 또는 고정된 프로세스만 생성하여 사용하고, 비동기 방식으로 요청들을 Concurrency 하게 처리할 수 있습니다. 위의 그림에서 보이듯이 Nginx는 새로운 요청이 들어오더라도 새로운 프로세스와 쓰레드를 생성하지 않기 때문에 프로세스와 쓰레드 생성 비용이 존재하지 않고, 적은 자원으로도 효율적인 운용이 가능합니다. 이러한 Nginx의 장점 덕분에 단일 서버에서도 동시에 많은 연결을 처리할 수 있습니다.
>
> > Apache와 같은 웹서버는 클라이언트로부터 받은 요청을 처리할 때 새로운 프로세스 또는 쓰레드를 생성하여 처리합니다. 요청마다 쓰레드가 생성되므로 접속하는 사용자가 많으면 그만큼 쓰레드가 생성되어 CPU와 메모리 자원의 소모가 커집니다.
>
> ##### 정리
>
> 1. Nginx는 **Event-Driven** 구조로 동작하기 때문에 한 개 또는 고정된 프로세스만 생성하여 사용
> 2. 비동기 방식으로 요청들을 Concurrency 하게 처리할 수 있다.
> 3. Nginx는 새로운 요청이 들어오더라도 새로운 프로세스와 쓰레드를 생성하지 않기 때문에 프로세스와 쓰레드 생성 비용이 존재하지 않는다.
> 4. 적은 자원으로도 효율적인 운용이 가능하다.
> 5. 위와 같은 Nginx의 장점 덕분에 단일 서버에서도 동시에 많은 연결을 처리할 수 있습니다.



> ### Nginx의 구조
>
> ![image-20201007171003152](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcWiJY4%2FbtqGexrcf2p%2FdTPGI6Dkbuscs3bIZyNAY0%2Fimg.jpg)
>
> :  Nginx는 하나의 Master Process와 다수의 Worker Process로 구성되어 실행됩니다. Master Process는 설정 파일을 읽고, 유효성을 검사합니다. 그리고 Worker Process를 관리합니다. 모든 요청은 Worker Process에서 처리합니다. Nginx는 **이벤트 기반** 모델을 사용하고, Worker Process 사이에 요청을 효율적으로 분배하기 위해 OS에 의존적인 메커니즘을 사용합니다. Worker Process의 개수는 설정 파일에서 정의되며, 정의된 프로세스 개수와 사용 가능한 CPU 코어 숫자에 맞게 자동으로 조정됩니다.
>
> ##### 정리
>
> 1. 하나의 Master Process와 다수의 Worker Process로 구성되어 실행.
> 2. Master Process는 설정 파일을 읽고, 유효성을 검사, Worker Process를 관리한다.
> 3. 모든 요청은 Worker Process에서 처리.
> 4. Nginx는 **이벤트 기반** 모델을 사용.
> 5. Worker Process 사이에 요청을 효율적으로 분배하기 위해 OS에 의존적인 메커니즘을 사용.
> 6. Worker Process의 개수는 설정 파일에서 정의되며, 정의된 프로세스 개수와 사용 가능한 CPU 코어 숫자에 맞게 자동으로 조정.



> ### Nginx 설정
>
> - Nginx는 설정 파일 구조 : Nginx는 지시어에 부여하는 값에 의해 작동한다.
> - Nginx의 메인 설정 파일 경로는 /etc/nginx/nginx.conf 
>   (/etc/nginx/nginx.conf 에 없다면 /usr/local/nginx/conf/nginx.conf 또는 /usr/local/etc/nginx/nginx.conf 에 위치)
> - Nginx 모듈의 동작은 configuration 파일에 있는 directives(지시어)에 의해 제어된다.
> - directive는 **simple directive**와 **block directive** 두 가지 종류가 있다.
>
> #### 
>
> #### Nginx 메인 설정 예시
>
> ```bash
> # worker 프로세스를 실행할 사용자 설정
> # - 이 사용자에 따라 권한이 달라질 수 있다.
> user  nginx;
> # 실행할 worker 프로세스 설정
> # - 서버에 장착되어 있는 코어 수 만큼 할당하는 것이 보통, 더 높게도 설정 가능
> worker_processes  1;
> 
> # 오류 로그를 남길 파일 경로 지정
> error_log  /var/log/nginx/error.log warn;
> # NGINX 마스터 프로세스 ID 를 저장할 파일 경로 지정
> pid        /var/run/nginx.pid;
> 
> 
> # 접속 처리에 관한 설정을 한다.
> events {
>     # 워커 프로레스 한 개당 동시 접속 수 지정 (512 혹은 1024 를 기준으로 지정)
>     worker_connections  1024;
> }
> 
> # 웹, 프록시 관련 서버 설정
> http {
>     # mime.types 파일을 읽어들인다.
>     include       /etc/nginx/mime.types;
>     # MIME 타입 설정
>     default_type  application/octet-stream;
> 
>     # 엑세스 로그 형식 지정
>     log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
>                       '$status $body_bytes_sent "$http_referer" '
>                       '"$http_user_agent" "$http_x_forwarded_for"';
> 
>     # 엑세스 로그를 남길 파일 경로 지정
>     access_log  /var/log/nginx/access.log  main;
> 
>     # sendfile api 를 사용할지 말지 결정
>     sendfile        on;
>     #tcp_nopush     on;
> 
>     # 접속시 커넥션을 몇 초동안 유지할지에 대한 설정
>     keepalive_timeout  65;
> 
>     # (추가) nginx 버전을 숨길 수 있다. (보통 아래를 사용해서 숨기는게 일반적)
>     server_tokens off
> 
>     #gzip  on;
> 
>     # /etc/nginx/conf.d 디렉토리 아래 있는 .conf 파일을 모두 읽어 들임
>     include /etc/nginx/conf.d/*.conf;
> }
> ```
>
> 
>
> > #### Directive
> >
> > **Simple directive** : 이름, 값이 있고 세미콜론(;)으로 끝난다.
> >
> > ```bash
> > worker_process 1;
> > ```
> >
> > ---
> >
> > **Block directive** : simple directive의 구조에 블록("{", "}")을 감싼 형태의 지시어.
> >
> > ```bash
> > events {
> >     worker_connections  1024;
> > }
> > ```
> >
> > -  block directive는 해당 directive 안에 또 다른 block directive가 포함될 수 있다.
> >
> > > **Block**
> > >
> > > 1. **http 블록**
> > >    - http 블록은 HTTP 부분과 관련된 모듈의 지시어와 블록을 정의
> > >    - server와 location의 루트 블록이라고 할 수 있다.
> > >    - http, server, location 블록은 계층 구조를 가지고 있다.
> > >    -  http 블록의 내용은 server 블록의 기본값이 되고, server 블록의 내용은 location 블록의 기본값이 된다.
> > >    - 만약 상위 블록에서 선언된 지시어를 하위 블록에서 다시 선언하면 상위의 지시어는 무시된다.
> > >    - http 블록 안에 한 개 이상의 server 블록을 선언할 수 있다.
> > > 2. **Server 블록**
> > >    - server 블록은 하나의 호스트를 선언하는데 사용하며, http 블록 안에서만 사용 가능.
> > >    - server 블록에는 한 개 이상의 location 블록을 선언할 수 있다.
> > > 3. **Location 블록**
> > >    - server 블록 안에 정의되며, 특정 URL을 처리하는 방법을 정의한다.
> > >    - 예를 들면 http://example.com/hello/1 과 http://example.com/world/1 접근하는 요청을 다르게 처리하고 싶을 때 사용
> > > 4. **Event 블록**
> > >    - events 블록은 네트워크의 작동 환경을 설정하는 지시어를 제공한다.
> > >    - 이벤트 블록의 지시어는 이벤트 블록에서만 사용할 수 있고, http, server, local 블록과는 상속 관계를 갖지 않는다.



> #### Proxy
>
> : **프록시 서버**는 클라이언트가 자신을 통해서 다른 네트워크 서비스에 간접적으로 접속할 수 있게 해주는 컴퓨터 시스템이나 응용 프로그램을 가리킨다. 서버와 클라이언트 사이에 중계기로서 대리로 통신을 수행하는 것을 가리켜 '프록시', 그 중계 기능을 하는 것을 프록시 서버라고 부른다.
>
> #### Reverse Proxy
>
> ![image-20201007171003153](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbbdANA%2FbtqGg5NZBTO%2FkEZK3ZAAojw5CN75twXLVK%2Fimg.jpg)
>
> : Nginx는 리버스 프록시로도 활용할 수 있다. 리버스 프록시란 외부 클라이언트에서 서버로 접근 시, 중간에서 중개자 역할을 하여 내부 서버로 접근할 수 있도록 도와주는 서버이다. 리버스 프록시를 활용했을 때 얻을 수 있는 장점은 다음과 같다.
>
> - **보안** : 외부 사용자로부터 내부망에 있는 서버의 존재를 숨길 수 있음. 모든 요청은 리버스 프록시 서버에서 받으며, 매핑되는 내부 서버로 요청을 전달한다. 또한 Nginx는 SSL 설정도 가능하다.
> - **로드밸런싱** : 리버스 프록시 서버가 내부 서버에 대한 정보를 알고 있으므로, 각 서버의 상태에 따라 부하를 분산시키며 요청을 전달할 수 있다.
>
> ##### Reverse Proxy 설정하기
>
> ```bash
> http {
>     server {
>         listen 80;
>         location / {
>             proxy_pass http://127.0.0.1:8081;
>         }
>     }
> }
> ```



**출처**

- https://icarus8050.tistory.com/57
- [https://velog.io/@hidaehyunlee/ftserver-%EC%84%A0%ED%96%89%EC%A7%80%EC%8B%9D-Docker-Debian-Buster-Nginx-](https://velog.io/@hidaehyunlee/ftserver-선행지식-Docker-Debian-Buster-Nginx-)
- [https://ko.wikipedia.org/wiki/%ED%94%84%EB%A1%9D%EC%8B%9C_%EC%84%9C%EB%B2%84](https://ko.wikipedia.org/wiki/프록시_서버)