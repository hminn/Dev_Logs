# Airbnb-Clone #11

- #11.0 HomeView Intro
- #11.1 Pagination with Limit and Offset
- #11.2 Pages List Navigation
- #11.3 Next Previous Page Navigation
- #11.4 Using Django Paginator
- #11.5 get_page vs page
- #11.6 Handling Exceptions
- #11.7 Class Based Views
- #11.8 Class Based Views part Two

---

## > HomeView Intro

### 0. Goal?

- Rooms View 최적화
- 하나의 페이지에 10개의 아이템만 Display 되도록.
- 화면 밑에는 페이지를 이동할 수 있는 숫자 버튼 Display

### 1. How to?

- 3가지의 방법으로 구현
  1. Django의 도움 없이 오로지 Python 100%로 수동적으로 작업
  2. Django의 도움을 살짝 받아서
  3. Django로 대부분을 구현
- 이렇게 다양하게 구현해보는 이유는?  
  : Django가 백그라운드에서 어떻게 돌아가는지에 대해 알 수 있다!

---

## > HTTP 요청과 응답

### 0. HTTP 요청과 응답을 처리에 필요한 요소

- **URL 매퍼들** : 적절한 view 함수들을 위해 지원되는 URL들(그리고 URL들 안에 인코딩된 어떤 정보라도)을 포워딩하기 위해.
- **View 함수들**: 요청된 데이터를 모델들에게서 가져오고, 데이터를 표시하는 HTML 페이지를 생성하고 그리고 브라우저 안의 view로 페이지들을 사용자에게 반환하기 위해.
- **탬플릿들**: 데이터를 뷰들 안에 렌더링할 때 사용하기 위해.

### 1. [요청과 응답의 프로세스](https://developer.mozilla.org/ko/docs/Learn/Server-side/Django/Home_page)

- 웹 페이지로부터 HTTP Request가 URL과 함께 Back으로.
- Back으로 오면 요청이 View로 전달이 된다.
- View에서 짜여진 로직대로 요청된 데이터를 모델들에게서 가져오고, 데이터를 표시하는 HTML 페이지를 생성.
- 템플릿이 데이터를 View들 안에 렌더링 하는데 사용.
- 브라우저 안의 View로 페이지들을 사용자에게 반환.

### 2. Request Dictionary

- 웹페이지에서 HTTP 요청 시에 넘어가는 URL에 Dictionary 형태로 키와 값을 함께 넘겨줄 수 있다.

  `http://127.0.0.1:8000/?page=10`

  위와 같이 HTTP Request가 넘어가면 View에서는 {"page" : 10} 형태의 Dictionary를  전달받는다.

- View에서는 `page = request.GET.get("page", 1)` 이와 같이 해당 Key에 대한 Value를 얻을 수 있다.

---

## > Pagination : python 100% with Django

### 0. [Template Filter](https://docs.djangoproject.com/en/3.0/ref/templates/builtins/#built-in-template-tags-and-filters)

- Template에서는 python의 모든 문법을 지원하지 않는다.
- Django Template에 존재하는 Tag와 다양한 filter들을 통해 Template 내에서도 python의 기능을 사용할 수 있다.
- `{{value | add : "2"}}` : If value is 4, then the output will be 6.

### 1. Pagination : Python 100% Code

**[view.py]**

```python
from math import ceil
from django.shortcuts import render
from . import models


def all_rooms(request):
    page = request.GET.get("page", 1)
    page = int(page or 1)
    page_size = 10
    limit = page_size * page
    offset = limit - page_size
    all_rooms = models.Room.objects.all()[offset:limit]
    page_count = ceil(models.Room.objects.count() / page_size)
    return render(
        request,
        "rooms/home.html",
        context={
            "rooms": all_rooms,
            "page": page,
            "page_count": page_count,
            "page_range": range(1, page_count),
        },
    )

```

**[home.html]**

```django
{% extends "base.html" %}

{% block page_name %}
    Home
{% endblock page_name %}

{% block content %}
    {% for room in rooms %}
        <h1>{{room.name}} / ${{room.price}}</h1>
    {% endfor %}
    <h5>
    {% if page is not 1 %}
        <a href="?page={{page|add:-1}}">Previous</a>
    {% endif %}
    page {{page}} of {{page_count}}
    {% if not page == page_count %}
    <a href="?page={{page|add:1}}">Next</a>
    {% endif %}
    </h5>
{% endblock content %}
```

---

## > Pagination : Django Power

### 0. QuerySets are lazy

```python
q = Entry.objects.filter(headline__startswith="What")
q = q.filter(pub_date__lte=datetime.date.today())
q = q.exclude(body_text__icontains="food")
print(q)
```

: 위 코드를 봤을 때 Database Hits가 세 번 일어난 것 처럼 보이지만, 사실 마지막 줄인 print(q)에서만 Database Hit이 일어난다. 1~3번째줄은 단순히 QuerySet만 반환이 되는 것이고 이러한 QuerySet을 가지고 DB에 요청(ask)하는 순간에 DB hit이 일어난다.

### 1. Django Paginator Work Process

- 페이지를 구성할 object_list를 불러온다.  
  : `room_list = models.Room.objects.all()`

- Paginator를 생성하기위해 불러온 Object_list를 넘겨준다. (한 페이지의 Object 수와 함께)  
  : `paginator = Paginator(room_list, 10)`

- 요청받은 Page number를 Paginator에 넘겨주어 해당 페이지를 불러온다.

  ```python
  page = request.GET.get("page")
  rooms = paginator.get_page(page)
  ```

- Paginator가 구성한 rooms라는 페이지를 render's context로 넘겨준다.  
  : `render(request, "rooms/home.html", context={"page": rooms},)`

### 2. [Paginator Class](https://docs.djangoproject.com/en/3.0/ref/paginator/#paginator-class), [Page Class](https://docs.djangoproject.com/en/3.0/ref/paginator/#page-class)

- Paginator와 Page의 다양한 Method를 통해 템플릿을 구성한다.

  ```django
  {% extends "base.html" %}
  
  {% block page_name %}
      Home
  {% endblock page_name %}
  
  {% block content %}
      {% for room in page.object_list %}
          <h1>{{room.name}} / ${{room.price}}</h1>
      {% endfor %}
      <h5>
      {% if page.has_previous %}
          <a href="?page={{page.previous_page_number}}">Previous</a>
      {% endif %}
      page {{page.number}} of {{page.paginator.num_pages}}
      {% if page.has_next %}
      <a href="?page={{page.next_page_number}}">Next</a>
      {% endif %}
      </h5>
  {% endblock content %}
  ```

---

## > Handling Exception

### 0. Get_page vs Page?

- Paginator에는 페이지를 받아오는 두가지의 Method가 존재한다 : **get_page & page**
- get_page는 EmptyPage, InvalidPage, PageNotAnInteger에 대한 예외처리가 Default로 되어있다.
- page method를 사용하면 위에 나열한 오류들에 대한 예외처리를 직접 해주어야 한다.

### 1. try ... except 구문

```
try:
    ...
except [발생 오류[as 오류 메시지 변수]]:
    ...
```

- 오류 처리에 대한 파이썬의 유연한 프로그래밍 기법이다.

### 2. How to Handle Exception?

```python
def all_rooms(request):
    page = request.GET.get("page", 1)
    room_list = models.Room.objects.all()
    paginator = Paginator(room_list, 10)
    try:
        rooms = paginator.page(int(page))
        return render(request, "rooms/home.html", context={"page": rooms},)
    except EmptyPage:
        return redirect("/")
```

---

## > Class Based Views

### 0. Why Use Class Based Views?

- 다양한 Applications의 View에서 Paginate라는 작업을 동일하게 반복해야한다.
- 반복되는 작업을 효율적으로 다루려면 abstract의 개념을 이용하여 프로그래밍!
- Class Based Views는 공통된 Paginate 작업에 대한 기본적인 틀을 만들어 놓은 것이다.
- 굉장히 편하게 기능을 구현할 수 있는 방법이지만, **어떠한 구조로 돌아가는지에 대해서는 숙지할 필요가 있다.** (~~Function based View로도 짜볼것,,~~)

### 1. ListView

- Rooms Application에서 사용할 Class based views 중 하나이다.
- [다양한 클래스](https://docs.djangoproject.com/en/3.0/ref/class-based-views/generic-display/#listview)와 [다양한 속성](http://ccbv.co.uk/projects/Django/3.0/django.views.generic.list/ListView/)들이 존재하며 마법같은 기능들을 제공한다.
- Function Based Views 와 비교했을 때 말이다.

- 다음은 위에서 구현한 Pagination을 Class Based Views로 구현한 코드이다.
  **[views.py]**

  ```python
  from django.views.generic import ListView
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
  
      def get_context_data(self, **kwargs):
          context = super().get_context_data(**kwargs)
          now = timezone.now()
          context['now'] = now
          return context
  ```

  **[room_list.html]**

  ```django
  {% extends "base.html" %}
  
  {% block page_name %}
      Home
  {% endblock page_name %}
  
  {% block content %}
      {{now}}
      {% for room in rooms %}
          <h1>{{room.name}} / ${{room.price}}</h1>
      {% endfor %}
      <h5>
      {% if page_obj.has_previous %}
          <a href="?page={{page_obj.previous_page_number}}">Previous</a>
      {% endif %}
      page {{page_obj.number}} of {{page_obj.paginator.num_pages}}
      {% if page_obj.has_next %}
      <a href="?page={{page_obj.next_page_number}}">Next</a>
      {% endif %}
      </h5>
  {% endblock content %}
  ```

  