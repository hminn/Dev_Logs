## Django Install

> ### pipenv를 이용한 Django Install

- **pip로 global하게 설치할 시의 문제점**

  ```
  pip install = Django==2.2.5
  ```

  Django Download page에 가보면 위와 같이 pip로 설치를 하라고 되어있다. <br>pip (package installer for Python) 은 기본적으로 인터넷으로부터 패키지를 다운받는 수단.

  pip의 경우에는 시스템에 global하게 설치하게 된다. 이 말은 즉슨, 어느곳에서나 Django version2.2.5 에 접근할 수 있게 된다는 것. 뭐 그래도 괜찮겠다고 생각할 수 있지만, 다음과 같은 상황에서는 문제가 된다. <br>

   ***현재 나의 컴퓨터에 Django version2로 작업한 프로젝트들이 여러 개 존재한다고 하자. 그런데 어느 날 Django version3가 새로 나와서 (global 하게) 업데이트를 하게 된다면, 이전에 Django v2로 작업한 프로젝트들이 망가지게 된다. 다시 말해서, Django를 새 버전으로 업데이트 한다면 서로 다른 버전의 Django를 사용하는 두 개의 프로젝트를 동시에 가질 수가 없다는 것!***

  따라서, pip로 global하게 설치하는 것보다는 각각의 환경(bubble)에 local install을 하는 게 안정성 측면에서 좋다.

- **Pipenv [python 세계에서의 패키지 매니저!]**

  - Pipenv는 Python-Django 프로젝트의 셋업 단계에서 가장 중요한 부분!

  - ```
    # os가 windows라 powershell 환경에서 다음의 명령어들을 실행.
    
    # (1) 가상환경을 설치할 프로젝트 폴더에 접근한다.
    
    # (2) python3.7 버전을 기반으로 하는 가상환경 생성
    pipenv --three
    
    # (3) 생성한 가상환경(버블) 안으로 접속(?)
    pipenv shell
    
    # (4) 가상환경 내에 Django 설치
    pipenv install Django==2.2.5
    ```

