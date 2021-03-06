## Creating a Django Project

> ### 장고 프로젝트 만들기
>
> - ```
>   django-admin startproject mysite
>   ```
>
>   : 장고 튜토리얼에는 위와 같은 방식으로 장고 프로젝트를 개시한다고 되어있으나, 이 방법은 첫 발걸음을 떼기에는 괜찮지만 앱의 규모를 확장하는데에는 썩 좋지 못한 방법이다.
>
> - 앱을 구조화함에 있어서 더 좋은 방법은?! **(정리 추가 필요;)**



> **Django 프로젝트 기본 파일**
>
> - config : 프로젝트의 마스터 폴더. config 외에 프로젝트에 포함되어있는 폴더들은 Applications
>
> - _\_init\_\_.py : 장고 관련 파일이 아닌 파이썬 관련 파일. 새로운 폴더를 만들 때, 파이썬 내에서 그 폴더의 파일들을 써야 할 때는 항상 이 파일을 둬야한다.
> - settings.py : 장고 프로젝트의 기본 세팅이 설정되어있는 파일. 이 파일을 보면 장고가 왜 사랑받는지 알 수 있음. 각각의 설정에 친절한 설명과 더불어 관련된 내용에 대한 **Django Documentation** Link가 달려있는데, 이 Django Documentation은 여태까지 만들어진 문서 사이트들 중 단연 최고라고 말할 수 있음. 개발에 필요한 것들이 웬만해선 다 설명되어있다! 실제 소스코드뿐만 아니라, 주석과 설명이 친절하게 달려있다구.

> **VS Code Editor를 이용한 개발환경 세팅하기**
>
> - "Linter : flake8" + "Formatter : black"

> **Django 기본 프로젝트 실행하기 Tutorial**
>
> - `python manage.py runserver` 명령어 로 서버를 구동시킨다.
> - 콘솔창에 등장한 주소 `http://127.0.0.1:8000/ ` 가 웹서버.
> - 이 상태에서 `http://127.0.0.1:8000/admin` 에 들어가보면 Error 창이 뜬다. (Traceback 페이지)
> - 서버를 일단 끄고 (Window : CTRL + c)
> - `python manage.py migrate` 명령어를 실행한 후, 다시 서버를 구동!
> - 다시 `http://127.0.0.1:8000/admin` 에 들어가보면 Django administration 페이지가 잘 작동하는 것을 볼 수 있음.
> - 등록된 유저가 없기 때문에 Login을 할 수 없음!
> - ㄴ다시 또 서버를 끄고
> - 슈퍼유저를 생성하는 명령어 `python manage.py createsuperuser` 실행하여 슈퍼유저 생성!
> - 서버 구동 후, admin 페이지에서 슈퍼유저 계정으로 로그인하면 관리자 패널을 사용할 수 있음. 패스워드 변경, 로그아웃, 사용자들의 목록, 사용자 추가 등 다양한 기능들이 기본적으로 세팅되어있음을 볼 수 있다! Django jjang (｡･∀･)ﾉﾞ

※ Linter?

- 인터프리터 언어(Runtime La nguage) 특성 상, 프로그램이 시작되기 이전에 에러를 잡아줄 수 가 없음. 이러한 문제를 방지하기 위해 Linter라는 것을 사용함. Linter는 프로그램이 실행되기전에 기본적으로 작성한 코드를 보면서 에러가 생길 부분을 미리 감지해준다.
- Python의 pep이라는 스타일 가이드에 맞춰 코드를 작성할 수 있게끔 도와준다.