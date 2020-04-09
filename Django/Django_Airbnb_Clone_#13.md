# Airbnb-Clone #13

- #13.0 Template. Form, Url, Setup
- #13.1 Starting the Form
- #13.2 Select Choices
- #13.3 Amenities and Facilities Form
- #13.4 Finishing the Form
- #13.5 Filtering Like a Boss part One
- #13.6 Filtering Like a Boss part Two
- #13.7 Introduction to Django Forms
- #13.8 I love Django Forms For Ever
- #13.9 Forms are Awesome!
- #13.10 Finishing Up!

---

## > URL

### 0. [URL 기본문법](https://wikidocs.net/9649)

### 1. [URL namespaces](https://docs.djangoproject.com/en/3.0/topics/http/urls/#url-namespaces)

### 2. [Removing hardcoded URLs in templates](https://docs.djangoproject.com/ko/3.0/intro/tutorial03/#removing-hardcoded-urls-in-templates)

- 수 많은 템플릿에 존재하는 링크에 대한 하드코딩은 매우 번거로운 일.
- 수정사항이 있을 때마다 일일이 다 들어가서 바꿔줘야 하는 불상사가 발생할 수 있다.

### 3. Django url Tag process

- ``{% url %}` template 태그를 사용하여 url 설정에 정의된 특정한 URL 경로들의 의존성을 제거할 수 있다.

- **Example**

  ```django
  <li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
  ```

  > 이것이 `polls.urls` 모듈에 서술된 URL 의 정의를 탐색하는 식으로 동작한다. 다음과 같이 'detail' 이라는 이름의 URL 이 어떻게 정의되어 있는지 확인할 수 있다.

  ```python
  ...
  # the 'name' value as called by the {% url %} template tag
  path('<int:question_id>/', views.detail, name='detail'),
  ...
  ```

  > 만약 상세 뷰의 URL을 `polls/specifics/12/`로 바꾸고 싶다면, 템플릿에서 바꾸는 것이 아니라 `polls/urls.py`에서 바꿔야 한다.

  ```python
  ...
  # added the word 'specifics'
  path('specifics/<int:question_id>/', views.detail, name='detail'),
  ...
  ```

  > Django 프로젝트는 앱이 몇개라도 올 수 있다. Django는 이 여러 앱들의 URL을 어떻게 구별해낼까? 정답은 URLconf에 이름공간(namespace)을 추가하는 것이다.

  ```python
  #polls/urls.py¶
  
  from django.urls import path
  from . import views
  
  app_name = 'polls'
  urlpatterns = [
      path('', views.index, name='index'),
      path('<int:question_id>/', views.detail, name='detail'),
      path('<int:question_id>/results/', views.results, name='results'),
      path('<int:question_id>/vote/', views.vote, name='vote'),
  ]
  ```

  ```django
  #polls/templates/polls/index.html
  
  <li><a href="{% url 'polls:detail' question.id %}">{{ question.question_text }}</a></li>
  ```

### 4. [Reverse](https://docs.djangoproject.com/en/3.0/topics/http/urls/#reversing-namespaced-urls)

- URL Pattern Name을 인자로 넘겨주면 해당 URL을 반환한다.
- 자세한 예시와 쓰임새는 Docs를 참고하도록 하자.

---

## > DetailViews : Function Based Views

### 0. Example Code

**[rooms/views.py]**

```python
from django.views.generic import ListView, DetailView
from django.http import Http404
from django.shortcuts import render
from django.utils import timezone
from . import models

class HomeView(ListView):
    """ HomeView Definition """

    model = models.Room
    page_kwarg = "page"
    paginate_by = 10
    paginate_orphans = 5
    ordering = "created"
    context_object_name = "rooms"

def room_detail(request, pk):
    try:
        room = models.Room.objects.get(pk=pk)
        return render(request, "rooms/detail.html", {"room": room})
    except models.Room.DoesNotExist:
        raise Http404()
```

**[rooms/urls.py]**

```python
from django.urls import path
from . import views

app_name = "rooms"
urlpatterns = [path("<int:pk>", views.room_detail, name="detail")]
```

**[config/urls.py]**

```python
urlpatterns = [
    path("", include("core.urls", namespace="core")),
    path("rooms/", include("rooms.urls", namespace="rooms")),
    path("admin/", admin.site.urls),
]

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

---

## > DetailViews : Class Based Views

### [0. DetailView Class (Built in Django)](http://ccbv.co.uk/projects/Django/3.0/django.views.generic.detail/DetailView/)

### 1. Example Code

**[rooms/views.py]**

```python
from django.views.generic import ListView, DetailView
from . import models


class HomeView(ListView):
    """ HomeView Definition """

    model = models.Room
    page_kwarg = "page"
    paginate_by = 10
    paginate_orphans = 5
    ordering = "created"
    context_object_name = "rooms"

class RoomDetail(DetailView):
    model = models.Room

```

- CBV를 사용하면 매우 간단해진다.
- 피상적으로 간단하게 보이는 것이 오히려 개발자 관점에서는 복잡한 것!
- 전체적인 원리를 이해하고 내 것으로 만든 뒤에 위처럼 간편한 코드를 사용하도록 하자.