# Django Airbnb-Clone #6, #7

- Room Admin Panel part Two
- Custom Admin Functions
- Managers and QuerySets

---

## > Admin Panel Option

- **[Ordering](https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.ordering) : Admin page에 접속했을 때, 해당 Field를 기준으로 자동 ordering을 해주는 option**<br> * 설정하지 않을 경우에는 Admin page에서 Field Name을 클릭하면 해당 Field 기준으로 정렬 <br> * 해당 Field가 함수 형식이면 클릭해도 정렬이 되지 않는다. (애초에 클릭 x)

## > Custom Admin Functions

- **Admin Class 내에 Custom Functions 추가하기**<br>: Custom Function을 추가하여 원하는 방식대로 페이지를 구성할 수 있다.

  ```python
  # Room Admin에서 Amenities의 개수 Field를 List에 추가하려고 한다.
  
  def count_amenities(self, obj):
  	return "Potato"
  # 이 때 self에는 RoomAdmin Class가, obj에는 Room 객체가 담긴다.
  # obj를 이용하여 각각의 객실에 접근이 가능하다.
  # obj의 메소드를 이용하여 다양한 핸들링이 가능하다.
  
  count_amenities.short_description = "Amenities"
  # Field를 함수로 정의를 하면 Field명이 Default로 함수명이 된다.
  # 이 때 위와 같이 옵션을 추가해주면 Field명을 따로 설정해줄 수 있다.
  
  def count_amenities(self, obj):
  	return obj.amenities.count()
  # 위처럼 obj의 메소드를 이용하여 amenities의 항목 개수를 반환할 수 있다.
  ```

  

## > Managers and QuerySets

- 이에 대한 학습을 위해 Python console에서 Tutorial을 진행한다.

  ```python
  # In console (windows : cmd-powershell)
  
  # Powershell 경로 설정 방법
  # Set-Location -Path '설정할 경로'
  # ex) Set-Location -Path 'D:\Nomadcoder_projects\airbnb-clone\'
  
  # Django 환경 설정
  pipenv shell
  python manage.py shell
  from users.models import User
  ```

- User Class에서 사용할 수 있는 Method를 확인해보자<a>

  ```python
  dir(User) #Class 안의 Method names list를 리턴
  vars(User) #Class 안의 Method dictionary를 리턴
  ```

- **UserManager**

  ```python
  # console 화면
  
  >>> User.objects
  <django.contrib.auth.models.UserManager object at 0x0000028BD7490348>
  # User.objects 명령어를 통해 UserManager를 확인할 수 있다.
  # 이는 데이터베이스로부터 Elements를 가져오게 해준다.
  # SQL 문법이 아닌, 파이썬을 이용하여 Elements에 접근하는 것으로 장고의 장점 중 하나.
  
  >>> User.objects.all()
  <QuerySet [<User: 143011>, <User: hminn>]>
  # User.objects.all()을 이용해 모든 Elements의 리스트를 가져온다.
  # 이 때, QuerySet 형태로 반환이 된다.
  # QuerySet이란? Object 리스트를 의미한다. DB로 부터 온 장고 Objects
  # 단순한 리스트가 아닌 다양한 기능을 제공하는 리스트이다.
  
  >>> all_user = User.objects.all() #Return QuerySet
  >>> all_user.filter(superhost=True) #Query using specific condition
  <QuerySet [<User: hminn>]>
  # 이처럼 QuerySet은 다양한 메소드를 가지고 있다.
  # QuerySet API reference : https://docs.djangoproject.com/en/3.0/ref/models/querysets/
  
  >>> hminn.room_set.all()
  <QuerySet [<Room: Very Awesome Place Hotel>]>
  # [Following relationships “backward”]
  # foreign-key model instance 는 Manager 에 접근할수있다. Manager는 ForeignKey를 가지고 있는 model의 모든 instance를 반환한다.
  # (즉, 어떤 model (User)에서 자신을 foreign key로 가지고 있는 모델(room)에 접근하기위해 Manager를 이용하면 된다.)
  # Manager 명명법 : FOO_set   (단, FOO 는 소문자 source model name)
  # Manager  : QuerySet 반환
  # 참고 : https://freeprog.tistory.com/55
  
  # In Room Model Django code
  host = models.ForeignKey("users.User", related_name="rooms", on_delete=models.CASCADE)
  # Manager 명명법을 바꾸는 옵션 : related_name
  # related_name의 의미를 풀어보면 다음과 같다.
  # User Model이 나에게 접근할 때, 어떤 이름으로 찾아지기를 원합니까?
  # (UserModel).room_set -> (UserModel).rooms (접근 형식이 달라졌다!)
  # related_name 변경 후에는 migrate를 해줘야한다.
  ```

