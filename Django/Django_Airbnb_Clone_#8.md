# Airbnb-Clone #8

- #8.0 Review Admin and Room Average
- #8.1 Reservations Admin
- #8.2 Conversations, Lists, Reservations Admin
- #8.3 Configuring User Uploads part One
- #8.4 Configuring User Uploads part Two
- #8.5 Photo Admin

---

## > Review Admin and Room Average

### 1. Method in models

- Admin page 뿐만 아니라 User들이 사용하는 Site에서도 함수의 결과가 필요한 경우에는 Admin이 아닌 Models내에서 함수를 생성

### 2. list_display

- \_\_str\_\_ 을 list_display에 쓸 수 있다. 

### 3. short_description

- 함수명.short_description = "바꿀 명칭"
- 위의 메소드를 이용하여 list에 출력되는 명칭을 변경할 수 있다.

---

## > Configuring User Uploads

### 1. [MEDIA_ROOT](https://docs.djangoproject.com/en/3.0/ref/settings/#media-root)

- `MEDIA_ROOT = os.path.join(BASE_DIR, "uploads")`  
  : 파일을 업로드 하기 위해선 먼저 업로드 할 장소를 설정해야한다.  
  : Django 에서는 업로드 설정을 MEDIA_ROOT로 정의되어있다. (in settings.py)  
  : 위 코드를 config/settings.py에 저장하면 ImageField에 업로드 되는 파일들이 'uploads' 폴더에 올라간다. 

  > BASE_DIR 은 `settings.py`에 설정되어 있다(root 경로와 같다)

### 2. [MEDIA_URL](https://docs.djangoproject.com/en/3.0/ref/settings/#media-url)

- `MEDIA_URL = '/media/'`
  : MEDIA_ROOT는 파일을 업로드 할 장소를 뜻한다면,  
  : MEDIA_URL은 MEDIA_ROOT에 접근할 URL을 뜻한다.

- **MEDIA_URL 활성화**  

  ```python
  from django.conf import settings
  from django.conf.urls.static import static
  
  urlpatterns = [
      path("admin/", admin.site.urls),
  ]
  
  # DEBUG : 해당 매크로를 통해 개발모드인지 프로덕션모드인지 알 수 있다.
  # DEBUG == True라면 개발모드 : 개발중이라면, 폴더 안의 파일들을 제공한다.
  if settings.DEBUG:
      urlpatterns += static(settings.MEDIA_URL, 				document_root=settings.MEDIA_ROOT)
  ```

  : 장고에게 폴더 안의 파일들을 어떻게 제공하는지 알려주는 작업  
  : 해당 작업은 개발에서만 적용되고 프로덕션 단계에선  X  
  : 일반적으로 서버 코드 환경에 사용자가 업로드 한 파일들을 올리지 않는다.  
  : 프로덕션 단계에서 사용자 업로드 파일들은 Amazon S3와 같은 클라우드 스토리지에 올린다. 
  : 지금은 개발 모드에서만 작업이 되어지기에, 학습을 위한 코드로 이해하면 되겠다.

---

## > Photo Admin

### 1. Tag Escape

- 장고에서는 `파이썬 문자열`을 템플릿을 통해 표현할 때에는 태그가 `ESCAPE`처리가 된다. 이는 보안에도 꼭 필요한 기능. (ex. 악성 스크립트 실행 방지)

  ````
  만약, 유저가 City라는 필드에 alert("Hello")를 입력했다?
  Tag Escape가 이루어지지 않으면 패널이 작동하면서 그 스크립트(alert)를 실행하게 된다.
  ````

- 유저가 올린 포스팅에서 이미지/자바스크립트를 통한 해킹을 사전에 방지할 수 있는 꼭 필요한 기능.

### 2. [Mark_Safe](https://www.askcompany.kr/r/articles/e81f15b/)

- Django 서버 개발 필요에 의해 만든 HTML 문자열에 대해서도 TAG ESCAPE 처리가 되는 것을 방지해주는 함수
- mark_safe 함수를 걸어주면 HTML 문자열 등이 SafeText로 변환된다.

## > ※ Django 마이그레이션 초기화하기

- https://yuda.dev/216

