# Airbnb-Clone #10

- #10.0 Introduction to Urls and Views
- #10.1 HttpResponse and render
- #10.2 Introduction to Django Templates
- #10.3 Extending Templates part One
- #10.4 Extending Templates part Two and Includes 

---

## > Introduction to Urls and Views

### 0. MTV 패턴?

- Django는 Model, Template, View라는 MTV 패턴을 따르고 있는데, MTV은 MVC (Model View Controller)와 유사한 점이 많다.
- MTV에서의 Model은 데이타를 표현하는데 사용되며, 하나의 모델 클래스는 DB에서 하나의 테이블로 표현된다.
-  MTV의 View는 HTTP Request를 받아 그 결과인 HTTP Response를 리턴하는 컴포넌트로서, Model로부터 데이타를 읽거나 저장할 수 있으며, Template을 호출하여 데이타를 UI 상에 표현하도록 할 수 있다.
- MTV의 Template은 Presentation Logic 만을 갖는데 HTML을 생성하는 것을 목적으로 하는 컴포넌트이다.

### 1. View?

- View는 필요한 데이타를 모델 (혹은 외부)에서 가져와서 적절히 가공하여 웹 페이지 결과를 만들도록 컨트롤하는 역할

- 뷰view는 어플리케이션의 "로직"을 만드는 곳! 파이썬의 어떠한 라이브러리라도 사용할 수 있다.

- 장고는 요청된 url을 조사해서, 위에서부터 view를 탐색함

  - 예쁜(?) url을 위해서 장고에는 URLconfs 라는 것을 사용
  - ex) URL 패턴 : `/article/new/<year>/<month>`

- View들은 Django App 안의 views.py 라는 파일에 정의하게 되는데, 각 함수가 하나의 View를 정의한다. 

  ```python
  from django.http import HttpResponse
   
  def index(request):
      return HttpResponse("<h1>Hello, World!</h1>")
  ```

- View함수는 입력으로 항상 Request를 받아들이고, Response를 리턴하게 된다.

- View는 HttpResponse 객체를 반환하거나, 혹은 Http404같은 예외를 발생하게 해야한다.

### 2. URL

- 장고는 URLconf (URL configurations) 를 사용해서 URL과 일치하는 뷰를 찾아서 사용자에게 정보를 보여줌

- 웹페이지의 URL을 다음과 같이 설정해준다. (config/urls.py)

  ```python
  urlpatterns = [
      path("", room_views.all_rooms),
      path("admin/", admin.site.urls),
  ]
  ```

  

---

## > HttpResponse and render

### 0. Render

- [`render()`](https://docs.djangoproject.com/ko/3.0/topics/http/shortcuts/#django.shortcuts.render) 함수는 request 객체를 첫번째 인수로 받고, 템플릿 이름을 두번째 인수로 받으며, context 사전형 객체를 세전째 선택적(optional) 인수로 받습니다.

- 인수로 지정된 context로 표현된 템플릿의 [`HttpResponse`](https://docs.djangoproject.com/ko/3.0/ref/request-response/#django.http.HttpResponse) 객체가 반환됩니다.

  ```python
  from django.shortcuts import render
  from . import models
  
  def all_rooms(request):
      all_rooms = models.Room.objects.all()
      return render(request, "rooms/home.html", context={"rooms": all_rooms})
  ```

- render 함수의 두번째 인수인 템플릿은 일정한 형태로 정보들을 보여주기 위해 재사용할 수 있는 파일, 레이아웃을 의미한다. 장고의 템플릿 양식은 HTML!

---

## > Introduction to Django Templates

### 0. Django Template

- 템플릿 (Template)은 View로부터 전달된 데이타를 템플릿에 적용하여 Dynamic 한 웹페이지를 만드는데 사용된다.
- Template은 HTML 파일로서 Django App 폴더 밑에 "templates" 라는 서브폴더를 만들고 그 안에 템플릿 파일(*.html)을 생성한다. 이는 단일 App이거나 동일 템플릿명이 없는 경우 사용할 수 있다.
- Django 개발 가이드라인은 "App폴더/templates/App명/템플릿파일" 처럼, 각 App 폴더 밑에 templates 서브폴더를 만들고 다시 그 안에 App명을 사용하여 서브폴더를 만든 후 템플릿 파일을 그 안에 넣기를 권장한다 (예: /home/templates/home/index.html ).

### 1. Template Language

- **템플릿 변수**  
  : 템플릿 변수는 {{ 와 }} 으로 둘러 싸여 있는 변수로서 그 변수의 값이 해당 위치에 치환된다. 변수에는 Primitive 데이타를 갖는 변수 혹은 객체의 속성 등을 넣을 수 있다.

  ```html
  <h4>
    Name : {{ name }}
    Type : {{ vip.key }}
  </h4>
  ```

- **템플릿 태그**  
  : 템플릿 태크는 {% 와 %} 으로 둘러 싸여 있는데, 이 태그 안에는 if, for 루프 같은 Flow Control 문장에서부터 웹 컨트롤 처럼 내부 처리 결과를 직접 덤프하는 등등 여러 용도로 쓰일 수 있다.

  ```django
  {% if count > 0 %}
      Data Count = {{ count }}
  {% else %}
      No Data
  {% endif %}
   
  {% for item in dataList %}
    <li>{{ item.name }}</li>
  {% endfor %}
  ```

- [**템플릿 확장**](http://pythonstudy.xyz/python/article/312-Django-%ED%85%9C%ED%94%8C%EB%A6%BF-%ED%99%95%EC%9E%A5)  

  - 웹사이트를 개발하다 보면 모든 (혹은 많은) 웹 페이지마다 공통적으로 들어가는 HTML 코드가 있음을 알게 된다. 각 웹페이지마다 공통 코드를 중복해서 넣어 주는 것은 효율적이지 않다.

  - Django 에서는 이러한 공통 부분을 기본 템플릿(Base Template)으로 만들고, ==**각 웹페이지 마다 변경이 필요한 부분만 코드를 작성하게 하는 템플릿 확장(Template Extension) 기능을 제공한다**==

  - ***[base.html]***

    ```django
    <!DOCTYPE html>
    <html lang="en">
    	<head>
    		<meta charset="UTF-8" />
    		<meta name="viewport" content="width=device-width, initial-scale=1.0" />
    		<title>{% block page_name %}{% endblock page_name %}| Nbnb</title>
    	</head>
    	<body>
            {% include "partials/header.html" %}
            {% block content%}{% endblock %}
            {% include "partials/footer.html" %}
    	</body>
    </html>
    ```

    ***[home.html]***

    ```django
    {% extends "base.html" %}
    
    {% block page_name %}
        Home
    {% endblock page_name %}
    
    {% block content %}
        {% for room in rooms %}
            <h1>{{room.name}} / ${{room.price}}</h1>
        {% endfor %}
    {% endblock content %}
    ```

    - 확장 시에는 베이스 템플릿을 extends 하는 코드를 템플릿 윗 부분에작성해준다. 
    -  Base 템플릿을 사용(확장)하는 템플릿은 블럭에 삽입하고자 하는 웹 페이지 내용을 작성한다.
    - 확장 템플릿에서 각 블럭의 이름별로 여러 블럭들을 추가할 수 있다.
    - 웹 페이지의 규모가 커질 수 있음을 고려하여 Base Template이 너무 많은 것을 포함하고 있지 않도록 Divide & Conquer의 관점에서 템플릿을 분할해야한다.
    - 기능 별로 분할 한 후, {% include %}를 통해 Base Template에 불러온다.

  - **템플릿 위치 셋팅**  
    : Django 템플릿 위치와 관련하여 두 가지 중요한 셋팅이 있는데, Django 프로젝트의 settings.py 안의 TEMPLATES 셋팅 중 DIRS 와 APP_DIRS 옵션이 그것이다.

    ```python
    TEMPLATES = [
        {
            'BACKEND': 'django.template.backends.django.DjangoTemplates',
            'DIRS': [ os.path.join(BASE_DIR, 'templates') ],  # 추가
            'APP_DIRS': True,
            'OPTIONS': {
                'context_processors': [
                    'django.template.context_processors.debug',
                    'django.template.context_processors.request',
                    'django.contrib.auth.context_processors.auth',
                    'django.contrib.messages.context_processors.messages',
                ],
            },
        },
    ```

    - **DIRS 옵션**  
      : Django가 템플릿들을 찾는 디렉토리 경로를 지정하는 것으로, 원래는 비어 있었는데, 위의 같이 Base 디렉토리(BASE_DIR) 밑의 templates 폴더 경로를 추가하였다. 즉, BASE_DIR\templates 가 경로에 추가되어야만 base.html 템플릿을 찾을 수 있게 된다. 만약 DIRS에 여러 경로가 추가되면, Django는 경로 순서대로 검색하면서 템플릿을 찾게 된다.
    - **APP_DIRS 옵션**  
      : Django가 Django App 안의 templates 폴더에서 템플릿들을 찾을 것인지의 여부를 설정하는 것이다. 디폴트로 True가 설정되어 있어서 기본적으로 App안의 templates 폴더를 검색하여 템플릿을 찾게 된다.

  

