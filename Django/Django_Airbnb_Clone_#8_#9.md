# Airbnb-Clone #8

- #8.6 raw_ids and Inline Admin
- #8.7 Explaining Python super()
- #8.8 Intercepting Model save() and admin_save()

---

## > Raw_ids and Inline Admin

### 1. [Raw_id_fields](https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.raw_id_fields)

- Admin option 중 하나로,

- Foreign key Fields에 대해 Select-box interface를 만들어준다.

- Foreign key를 좀 더 나은 방법으로 찾아볼 수 있게 도와준다.

  ```python
  raw_id_fields = ("host", )
  
  # host가 속해있는 User Admin을 사용해서 검색 및 필터링이 가능하도록 해준다.
  ```

### 2. [Inline Model Admin](https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#inlinemodeladmin-objects)

: Admin 내에 또 다른 Admin을 넣는 방법

```python
from django.contrib import admin
from . import models

class PhotoAdmin(admin.TabularInline):
    model = models.Photo

@admin.register(models.Room)
class RoomAdmin(admin.ModelAdmin):

    """ Room Admin Definition """

    inlines = (PhotoAdmin,)
```

- TabularInline? StackedInline?

  : TabularInline은 하나의 항목이 가로 방향으로 퍼져있는 구조.  
  : StackedInline은 하나의 항목이 세로 방향으로 쌓여있는 구조.

---

## > Explaining Python super()

### 1. Super

- 자식 클래스에서 부모클래스의 내용을 사용하고 싶을 경우에 사용된다.

- ```python
  class dog:
      def __init__(self):
          print("dog에요.")
      def pee(self):
          print("쉬!")
          
  class puppy(dog):
      def __init__(self):
          print("puppy에요")
      def pee(self):
          print("go to the park")
          super().pee() #super()를 사용하여 부모클래스에 접근
          
  p = puppy()
  p.pee()
  
  # 결과
  puppy에요
  go to the park
  쉬!
  ```

### 2. [Overriding predefined model methods](https://docs.djangoproject.com/en/3.0/topics/db/models/#overriding-predefined-model-methods)

- 기존에 정의가 되어있는 Save() 함수를 사용자가 Overriding하여 사용한다.

- ```python
  class Room(core_models.TimeStampedModel):
  
      """ Room Model Definition """
  
      def save(self, *args, **kwargs):
          self.city = "potato"
          super().save(*args, **kwargs)
  ```

  : 위와 같이, Room 모델에 save 함수를 overriding하여 Task를 추가하였다.  
  : Room instance를 save 하게 되면, 사용자가 city에 무엇을 입력했던 potato로 저장이 된다.

### 3. [ModelAdmin.``save_model](https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.save_model)

- **Admin에서** 무언가를 저장할 경우에만 어떠한 Event를 일어나게 하고 싶을 때 사용.
- [위의](#2.-Overriding-predefined-model-methods) 경우에는 Model 내에서 Method를 overriding 한거라 Admin이건, Console이건, view건 간에 상관없이 (어디서든 Model을 save하면!) 재정의한 save event가 실행된다.
- **Admin 내에서**의 save event를 재정의 하고 싶을 때 사용하면 된다.

````python
from django.contrib import admin

class ArticleAdmin(admin.ModelAdmin):
    def save_model(self, request, obj, form, change):
        obj.user = request.user
        super().save_model(request, obj, form, change)
````

---



# Airbnb-Clone #9

- #9.0 Custom manage.py commands
- #9.1 seed_amenities command
- #9.2 seed_everything and seed_users
- #9.3 seed_rooms part One
- #9.4 seed_rooms part Two

---

## > Custom manage.py commands

### 1. For What?

: 여러가지 테스트를 하기에 매우 적은 데이터를 어떻게 뻥튀기 할 것인가?  
: 에 대한 해답으로 Custom Commands!  
: 일일이 클릭으로 만드는 것은 프로그래머가 아니다! 우리는 코드로 데이터를 만든다!

### 2. Django-seeds?

- 웹 서비스 개발 초기에 기본적으로 마스터 데이터 또는 테스트 테이터가 필요하다.
- 이러한 데이터들을 빠르게 만들어 줄 수 있는 도구이다.

### 3. Custom commands + Django-seeds

- 이 두개를 결합하여 가짜 데이터를 만들고 실행까지 해보려고 한다.

### 4. How to create 'Custom Commands'?

- 에러를 발생시켜 무엇을 해야할지 찾아가는 방법으로 진행

- ==사전 준비==

  1. 원하는 앱 폴더에 management 폴더 생성
  2. 그 안에 ` __init__.py` 를 생성 (Django에게 해당 폴더가 파이썬 폴더임을 알려주기 위함)
  3. management 폴더에 commands 폴더 생성
  4. commands 폴더 내에 `__init__.py` 와 `[CommandName].py` 를 생성

- 콘솔 창에 내가 원하는 command를 입력하고, 그 command가 실행되기 위해서는 무엇이 필요한지 알아보자!

  ```bash
  python manage.py loveyou --times 50
  
  # Error
  AttributeError: module 'rooms.management.commands.loveyou' has no attribute 'Command'
  ```

  : 위의 에러를 통해 Command라는 클래스가 필요함을 알 수 있다.

- 명령어를 작성할 파이썬 파일에 Command Class를 추가.

  ````python
  #Command class 추가
  class Command:
      print("I love you")
  ````

  ````bash
  # 명령어 실행
  python manage.py loveyou --times 50
  ````

  ```markdown
  *#Error*
  AttributeError: 'Command' object has no attribute 'run_from_argv'
  ```

- Django에서 기대하는 Class들이 존재함을 파악.  
  이럴 경우에는 주저하지 말고 [Django Docs](https://docs.djangoproject.com/en/3.0/howto/custom-management-commands/#module-django.core.management)를 이용한다.

- Django-Tutorial을 통해 다음과 같은 기초 설정을 진행해준다.

  ```python
  from django.core.management.base import BaseCommand
  
  
  class Command(BaseCommand):
      print("I love you")
  ```

  ```markdown
  *#python manage.py loveyou 명령어 실행 후*
  *#Error*
  NotImplementedError: subclasses of BaseCommand must provide a handle() method
  ```

  : BaseCommand의 하위 클래스는 handle() method를 가지고 있어야 한다?!

- BaseCommand를 타고 들어가 handle Method의 형식을 구체적으로 확인

- 이러한 일련의 과정을 통해 command에 무엇이 필요한지 알아갈 수 있다.

  - `def add_arguments(self, parser)`  : arguments 추가
  - `def handle(self, *args, **options):` : The actual logic of the command
  - 등등,, 찾아보면 다 나온다!

- 에러를 통한 시행착오를 겪으며 다음과 같은 Custom Command를 완성할 수 있다.

  ````python
  from django.core.management.base import BaseCommand
  
  class Command(BaseCommand):
      help = "This command tells me that he loves me"
  
      def add_arguments(self, parser):
          parser.add_argument(
              "--times",
              help="How many times do you want me to tell you that i love you?",
          )
  
      def handle(self, *args, **options):
          times = options.get("times")
          for t in range(0, int(times)):
              self.stdout.write(self.style.SUCCESS("I love you"))
  ````

  

---

## > 