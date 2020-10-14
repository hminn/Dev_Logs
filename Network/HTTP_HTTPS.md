## HTTP / HTTPS

![img](https://blog.kakaocdn.net/dn/JzEWk/btqw8BCKyVs/5bHeRdeKF5B6Qgw9MAoFhK/img.png)

> ## HTTP
>
> 
>
> > #### HTTP 란?
>
>  HTTP(Hypertext Transfer Protocol)는 인터넷상에서 데이터를 주고 받기 위한 [서버/클라이언트 모델](https://www.joinc.co.kr/w/Site/Network_Programing/Documents/Programming_Model)을 따르는 프로토콜 이다. 애플리케이션 레벨의 프로토콜로 [TCP](https://www.joinc.co.kr/w/man/12/TCP)/[IP ](https://www.joinc.co.kr/w/man/12/IP)위에서 작동한다.
>
> 가장 성공적인 인터넷 프로토콜이다. HTTP가 없었다면 인터넷은 지금과는 전혀 다른 모습이였을거다.
>
> HTTP는 어떤 종류의 데이터든지 전송할 수 있도록 설계돼 있다. 인터넷상에서 흔히 볼수 있는 HTML로 작성된 문서는 HTTP로 보낼 수 있는 데이터의 한 종류일 뿐이다. 이미지, 동영상, 오디오, 텍스트 문서들 아무튼 종류를 가리지 않는다.
>
> **Transfer**라는 해석 그대로 데이터를 전송하겠다라는 의미로 앞에 **Hypertext** 가 붙은 이유는 하이퍼텍스트 기반으로 데이터를 전송하겠다는. 간단히 말해서 링크기반으로 데이터에 접속하겠다는 의미가 되겠다.
>
> 
>
> > #### 작동 방식
>
>  HTTP는 서버/클라이언트 모델을 따른다. 클라이언트에서 요청(request)를 보내면 서버는 요청을 처리해서 응답(response)한다.
>
> 1. 클라이언트 : 서버에 **요청하는 클라이언트 소프트웨어**가 설치된 컴퓨터. chrom, firefox, ie등의 클라이언트 소프트웨어를 이용한다. 클라이언트는 URI를 이용해서 서버에 접속하고, 데이터를 요청할 수 있다.
> 2. 서버 : 클라이언트의 요청을 받아서, 요청을 해석하고 응답을 하는 소프트웨어가 설치된 컴퓨터. Apache, nginx, IIS, lighttpd 등이 서버 소프트웨어다.
>
> 웹서버는 보통 표준[포트](https://www.joinc.co.kr/w/man/12/포트번호)인 80번 포트로 서비스한다.
>
> 
>
> > #### Connectless & Stateless
>
>  HTTP는 Connectless 방식으로 작동한다 [[1]](https://www.joinc.co.kr/w/Site/Network_Programing/AdvancedComm/HTTP#fid_1). 서버에 연결하고, 요청해서 응답을 받으면 연결을 끊어버린다. 기본적으로는 자원 하나에 대해서 하나의 연결을 만든다. 이런 작동방식은 각각 아래의 장점과 단점을 가진다.
>
> - 장점 
>   1. 불특정 다수를 대상으로 하는 서비스에 적합한 방식이다. 
>   2. 수십만명이 웹 서비스를 사용하더라도 접속유지는 최소한으로 할 수 있기 때문에, 더 많은 유저의 요청을 처리할 수 있다.
> - 단점
>   1. 연결을 끊어버리기 때문에, 클라이언트의 이전 상태를 알 수가 없다.
>   2.  이러한 HTTP의 특징을 **stateless**라고 하는데, Connectless로 부터 파생되는 특징이라고 할 수 있다.
>   3.  **클라이언트의 이전 상태** 정보를 알 수 없게 되면, 웹 서비스를 하는데 당장에 문제가 생긴다.
>   4.  예컨데, 클라이언트가 과거에 로그인을 성공하더라도 로그 정보를 유지할 수가 없다. HTTP는 **cookie**를 이용해서 이 문제를 해결하고 있다.
>
> > #### URI 
>
>  클라이언트 프로그램(이하 웹 브라우저)은 **URI**를 이용하여 자원의 위치를 찾는다. URI는 HTTP와는 독립된 다른 체계다. HTTP는 전송 프로토콜이고, URI는 자원의 위치를 알려주기 위한 프로토콜이다. Uniform Resource Identifiers 의 줄임로, World Wide Web 상에서 접근하고자 하는 자원의 위치를 나타내기 위해서 사용한다. 자원은 "문서", "이미지", "동영상", "프로그램", "이메일"등 모든 것이 될 수 있다. 메일을 받을 상대방의 위치를 나타내기 위해서 사용하는 email://yundream@joinc.co.kr, 웹페이지의 위치를 나타내기 위해서 사용하는 http://www.joinc.co.kr/index.php 등이 URI의 예다.
>
> http://www.joinc.co.kr/index.php 를 분석해보자.
>
> 1. http : 자원에 접근하기 위해서 http 프로토콜을 사용한다.
> 2. [www.joinc.co.kr](http://www.joinc.co.kr/) : 자원의 인터넷 상에서의 위치는 [www.joinc.co.kr이다](http://www.joinc.co.xn--kr-q35ig39d/). 도메인은 ip 주소로 변환되므로, ip 주소로 서버의 위치를 찾을 수 있다.
> 3. index.php : 요청할 자원의 이름이다.
>
> 이렇게 "프로토콜", "위치", "자원명"으로 (인터넷 상에서) 어디에 있던지 자원에 접근할 수 있다.
> 

> ## HTTPS (Hypertext Transfer Protocol over Secure Socket Layer)
>
> > #### SSL
> >
> >  [SSL](https://www.joinc.co.kr/w/man/12/SSL)은 전자상거래에서의 데이터 보안을 위해서 개발한 통신 레이어다. SSL은 [표현계층](https://www.joinc.co.kr/w/man/12/OSI7)의 프로토콜로 응용 계층 아래에 있기 때문에, 어떤 응용 계층의 데이터라도 암호화해서 보낼 수 있다.
>
>  HTTP는 기본적으로 평문 데이터 전송을 원칙으로 하기 때문에 개인의 프라이버시가 오가는 서비스들(전자상거래, 전자메일, 사내문서)에 사용하기 힘들다. HTTPS는 SSL 레이어위에 HTTP를 통과 시키는 방식이다. 즉 평문의 HTTP 문서는 SSL 레이어를 통과하면서 암호화 돼서 목적지에 도착하고, 목적지에서는 SSL 레이어를 통과하면서 복호화 돼서 웹 브라우저에 전달된다.
> ![img](https://docs.google.com/drawings/d/1VclZ-SIt2mng9K-ss0GK-LspStBi7do5nZVbNBF7jJU/pub?w=737&h=482)
>
> 간혹, **HTTPS**를 하나의 프로토콜로 인식하기도 하는데, HTTP와 SSL은 전혀 다른 계층의 프로토콜들의 조합이다. HTTPS over SSL로 보는게 좀더 정확한 시각이다. 거의 모든 웹 서버와 웹 브라우저와 HTTP 기반의 툴들 - wget, [curl](https://www.joinc.co.kr/w/Site/Tip/curl), [ab](https://www.joinc.co.kr/w/Site/System_management/PerformanceTuning/WebServerPerformance) 기타등등 - 이 SSL을 지원한다.
>
> 
>
> > #### HTTP와 다른 점
> >
> > - HTTPS URL은 "[https://"로](https://xn--"-ky7m/) 시작한다. 기본 포트번호는 443이다.  
> >   HTTP URL은 "[http://"로](http://xn--"-ky7m/) 시작한다. 기본 포트번호는 80이다.
> > - HTTP는 평문 데이터를 기반으로 하기 때문에, 유저정보와 같은 민감한 정보가 인터넷 상에 그대로 노출된다. 이 정보는 수집되거나 변조될 수 있다. HTTPS는 이러한 공격을 견딜 수 있도록 설계돼 있다.
> > - HTTPS는 인증서를 이용해서, 접속 사이트를 신뢰할 수 있는지 평가할 수 있다.
> > - 일반적으로 HTTPS는 HTTP에 비해서 (매우 많이)느리다. 많은 양의 데이터를 처리할 경우 성능의 차이를 체감할 수 있다. 많은 웹 사이트들이 민감한 정보를 다루는 페이지(로그인 혹은 유저정보) 페이지를 HTTPS로 전송하고, 기타 페이지는 HTTP로 전송하는 방법을 사용한다. 하드웨어 SSL 가속기를 이용해서 암/복호화 성능을 높이는 방법을 사용하기도 한다.

##### 출처

- https://www.joinc.co.kr/w/Site/Network_Programing/AdvancedComm/HTTP

