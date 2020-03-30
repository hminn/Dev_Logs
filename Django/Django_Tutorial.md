## Django Tutorial

> **Django Migration**

-  migration은 기본적으로 뭔가를 다른쪽으로 보낸다는 개념, 무언가를 특정 장소에서 다른 곳으로 이동시킨다는 의미.

- 데이터 유형이 변경되면 Migration을 생성하고 해당 Migration을 적용한다(Migrate). 그리고 데이터 베이스를 업데이트를 함으로써 데이터베이스와 장고를 동기화 할 수 있다. (?)

- Django의 기본 프로젝트 frame에 User system, Authentication, Administration, Sessions등의 데이터가 포함되어 있다. 이를 데이터베이스에 동일한 데이터 유형을 동기화 시켜주어 데이터베이스를 업데이트 한다. migrate 하는 코드는 다음과 같다.

  ```
  python manage.py migrate
  ```

- migrate 코드를 실행하면 데이터베이스(db.sqlite3 file in tutorial)에 데이터가 업데이트 됨을 확인할 수 있다.

- 결과적으로 동기화를 통해, 데이터베이스는 장고가 ID, password, last_login 등이 포함된 인증된 사용자의 정보를 저장하게 될 것이라는 사실을 알게된다.
- 데이터베이스에는 SQL로 이러한 정보들이 저장되어있는데, 이것은 장고에서 다 처리를 해준 것. 이것이 Migration.

---

> **Django Application**

- 장고 프로젝트는 여러 Application을 포함한다. 다시 말해, Project는 Application의 집합이라고 보면 된다. Application은 Funtion의 집합.

- 코딩하기에 앞서, 언제, 어떻게 어플리케이션을 만드는지, 어떻게 project를 구성할지에 대해 계획을 해야한다. 이때, Django를 효과적으로 사용하기 위해서는 Application을 Divide and Conquer 방식으로 구성해야한다.

  ```
  EX) Airbnb clone coding을 하기에 앞서, Airbnb 페이지에 들어가 어떤 Application이 필요한지 확인해본다.
  
  - 보여지는 여러 기능들을 하나의 집합으로 묶을 수 있는지 check
  - 예를 들어, room을 검색할 수도 있고, 생성할 수도 있으며, 삭제, 수정, 사진 미리보기, 모든 사진 보기, 업로드 등의 여러가지 기능이 Room이라는 집합에 묶여있다면 이러한 기능들을 모아 Room Application을 구성하면 된다.
  - 이런 식으로 기능들을 집합화할 때, 너무 Massive하게 만들지 말아야함.
  - 장고를 효과적으로 사용하기 위해서는 언제 Application을 만들고, 또 만들지 말아야하는지 잘 구분해야한다.
  - 한 문장으로 Application을 표현할 수 있을 정도의 규모와 기능을 갖춰야 한다는 말임!(한 문장 내에는 'and'와 같은 접속사가 들어가면 안된다.)
  - 한마디로, 하나의 Application에 결이 다른 기능 뭉치들을 같이 묶지 말라는 말임!
  ```

- Application의 이름은 항상 복수형이 되어야한다.
  : ex) room (x)  rooms (o) / user (x) users(o)

- Application을 생성하는 코드는 다음과 같다.

  ```
  django-admin startapp [apps_name]
  ```

- 생성된 app 폴더에 들어가보면 Django에서 자동적으로 만들어놓은 migrations폴더와 py 파일들이 있다. **이것들의 이름을 변경한다거나, 삭제하는 등의 행위는 절대! 금한다**. 파일을 추가하거나 코드를 추가하는 것은 상관없음. (우리는 Django라는 **Framework를 사용**하고 있기 때문에, **Framework's Rule을 반드시 따라야만 한다**.)

- > **자동으로 생성된 py 파일에 대한 간략한 설명**
  >
  > (1) admin.py : admin 패널에 반영할 코드를 짜넣는 곳
  >
  > (2) apps.py : 보면 알 수 있듯, 그냥 configuration 파일. 
  >
  > (3) model.py : 우리가 원하는 Database가 어떻게 생겼는지에 대해서 설명해줘야 하는 곳.
  >
  > (4) test.py : 보는 그대로.
  >
  > (5) views.py : UI를 구성하는 곳, Html을 render(보여주는)하는 function을 짜넣게 되는 곳이다.

---

