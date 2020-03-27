## Airbnb-clone #1

> #### Substituting a custom User model

- 프로젝트에 따라서 인증 요구사항이 다르기 때문에 장고에서 제공하는 user 모델이 맞지 않을 수 있다. 장고에서는 user 모델을 덮어쓰려면 'AUTH_USER_MODEL' 값을 설정해야한다. (*in config-settings.py*)

  ```
  AUTH_USER_MODEL = 'myapp.MyUser'
  ```

- **Custom User model**

  - 장고에서 기본으로 제공하는 User model의 기능에다가 다양한 기능을 추가 한 형태의 model을 사용하고 싶다면?

  - 장고의 User model을 상속받은 객체에 원하는 기능을 추가하여 Customizing 하면 된다.

  - 원하는 User Model의 기능을 가져오기 위해 import를 진행

    ```python
    from django.contrib.auth.models import AbstractUser
    ```

  - 그런 다음, 다음과 같이 **객체 상속**을 해주면 기본 기능이 추가되고 그 외 다양한 기능을 추가할 수 있는 판이 깔리게 된다.

    ```python
    class User(AbstractUser):
    ```

  - 장고의 User를 내가 만든 User로 대체하기 위해선 먼저, User Application을 settings.py에 설치해야한다.

    ```python
    # 기존의 INSTALLED_APPS를 다음과 같이 변경
    
    DJANGO_APPS = [
        "django.contrib.admin",
        "django.contrib.auth",
        "django.contrib.contenttypes",
        "django.contrib.sessions",
        "django.contrib.messages",
        "django.contrib.staticfiles",
    ]
    
    PROJECT_APPS = [
        "users.apps.UsersConfig",
    ]
    
    INSTALLED_APPS = DJANGO_APPS + PROJECT_APPS
    ```

  - 설정을 바꿔준 뒤 다시 서버를 구동시켜 보았더니 다음과 같은 오류가 발생.

    ```
    ValueError: Dependency on app with no migrations: users
    ```

  - 오류에 대한 해결 방법은 다음과 같다.
  
    ```powershell
    python manage.py makemigrations
    # 위 코드를 실행하면, 변경된 users앱의 migrations폴더에 0001_initial.py라는 User model 파일이 생성되어있다.
    
    python manage.py migrate
    ```
    
  - User Model 에 대한 Customize
  
    ```python
    class User(AbstractUser):
    
        """ Custom User Model """
    
        GENDER_MALE = "male"
        GENDER_FEMALE = "female"
        GENDER_OTHER = "other"
    
        GENDER_CHOICES = (
            (GENDER_MALE, "Male"),
            (GENDER_FEMALE, "Female"),
            (GENDER_OTHER, "Other"),
        )
    
        LANGUAGE_ENGLISH = "en"
        LANGUAGE_KOREAN = "kr"
    
        LANGUAGE_CHOICES = (
            (LANGUAGE_ENGLISH, "English"),
            (LANGUAGE_KOREAN, "Korean"),
        )
    
        CURRENCY_USD = "usd"
        CURRENCY_KRW = "krw"
    
        CURRENCY_CHOICES = (
            (CURRENCY_USD, "USD"),
            (CURRENCY_KRW, "KRW"),
        )
    
        avatar = models.ImageField(null=True, blank=True)
        gender = models.CharField(
            choices=GENDER_CHOICES, max_length=10, null=True, blank=True
        )
        bio = models.TextField(default="", blank=True)
        birthdate = models.DateField(null=True, blank=True)
        language = models.CharField(
            choices=LANGUAGE_CHOICES, max_length=10, null=True, blank=True
        )
        currency = models.CharField(
            choices=CURRENCY_CHOICES, max_length=10, null=True, blank=True
        )
        superhost = models.BooleanField(default=False)
    ```
  
    - avatar, gender, bio, birthdate.. 등등 사용자 프로필에 필요할 것 같은 다양한 필드들을 추가함.
    - 각각의 데이터 속성에 맞는 Field들을 설정해주었다! 이에 대한 정보는 Django Docs에 널림!
      : https://docs.djangoproject.com/ko/3.0/
    - Field 함수에 들어가는 인자들에 대한 설명은 잘 찾아보면 될 것 같다.

> #### Creating Model field

- **새로운 field를 추가하는 경우 주의할 점**

```
기존 유저들이 존재하는 상황에서 새로운 field를 추가할 때,
아무 조치를 취해주지 않으면 오류가 발생한다.

Why? 기존 유저들의 row에도 새로운 field 값이 들어갈 자리가 생기는데 이에 대한 처리를 해주지 않으면 DB가 어리둥절 한다는 것이다.

따라서, 새로운 field를 생성할 때 
(1) default 값에 대한 처리를 해주던지
(2) null=True 조건을 걸어주어 null 값을 허용해주던지
위 같은 처리를 해줘야 새로운 field 생성 시에 오류가 발생하지 않는다.

새로운 field를 추가한 후에는?
python manage.py makemigrations
python manage.py migrate 
를 통해 장고에서 바꾼 변경사항을 DB와 동기화 시켜준다.

※ 장고에서 변경한 사항이 데이터베이스에 직접 영향을 주지 않는 경우에는 migrate할 필요가 없다. 예를 들어, 'gender field를 입력받는 form에 대한 변경' 등은 DB에 영향을 주는 부분이 아니기때문에 웹페이지를 새로고침하면 바로 반영이 되는 것을 볼 수 있다.
```