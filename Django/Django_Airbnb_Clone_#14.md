# Airbnb-Clone #14

- #13.0 Setting Up!
- #14.1 Form and CSRF!
- #14.2 Validating Email
- #14.3 Validating Password
- #14.4 Log In and Log Out
- #14.5 Always an Easier Way

---

## > Login Form : Not Using Django's Util

### 1. [CSRF](https://docs.djangoproject.com/en/3.0/ref/csrf/) : Cross Site Request Forgery 

- [**CSRF 공격이란?**](https://velog.io/@ground4ekd/django-csrf)
  CSRF 공격(Cross Site Request Forgery)은 웹 어플리케이션 취약점 중 하나로, 인터넷 사용자(희생자)가 자신의 의지와는 무관하게 공격자가 의도한 행위(수정, 삭제, 등록 등)를 특정 웹사이트에 요청하게 만드는 공격
- Django 에서는 기본적으로 CSRF 토큰을 이용해 CSRF공격을 방지한다.
- Django의 자체적인 템플릿에서  `{% csrf_token %}`을 Form 안에 넣는 것만으로 토큰을 주고 받는 식으로 쉽게 해결이 되었다.
- **How to deffense CSRF in Django?**  
  클라이언트에서 해당 페이지를 접속하게 되면 Django 에서 자동으로 csrf_token을 클라이언트로 보내어 cookie에 저장시키고, POST로 전송할 때 cookie의 csrf_token 이 함께 전송되어 인증하는 방식이다.

### 2. Checking Email & Password

- LoginForm Class 내에서 진행

- Email 이나 Password가 있는 Field를 확인하고 싶을 때 사용하는 메소드의 이름은 `clean_`으로 시작해야만 한다.

- `clean_` 으로 시작되는 Method들이 하는 일은 에러를 출력하는 것뿐만 아니라 데이터를 정리해주는 기능도 한다.

- ==주의!== `clean_` Method 사용 시, 값을 Return 해주어야 cleaned_data에 정리된 값이 들어온다. 값을 Return 해주지 않으면 아무것도 넘어가지 않아 None값이 출력됨을 확인할 수 있다. (clean_ Method를 사용하지 않으면 입력받은 값이 그대로 cleaned_data에 담긴다)

- **Clean_ Method를 사용한 코드**

  ```python
  class LoginForm(forms.Form):
      email = forms.EmailField()
      password = forms.CharField(widget=forms.PasswordInput)
  
      def clean_email(self):
          email = self.cleaned_data.get("email")
          try:
              models.User.objects.get(email=email)
              return email
          except models.User.DoesNotExist:
              raise forms.ValidationError("User does not exist")
          print("clean email")
  
      def clean_password(self):
          email = self.cleaned_data.get("email")
          password = self.cleaned_data.get("password")
          try:
              user = models.User.objects.get(email=email)
              if user.check_password(password):
                  return password
              else:
                  raise forms.ValidationError("Password is wrong")
          except models.User.DoesNotExist:
              raise forms.ValidationError("User does not exist")
  ```

- **위의 코드를 좀 더 간결하게** (중복되는 부분을 하나로! : Clean 메소드 하나로 통합)

  ```python
  class LoginForm(forms.Form):
      email = forms.EmailField()
      password = forms.CharField(widget=forms.PasswordInput)
  
      def clean(self):
          email = self.cleaned_data.get("email")
          password = self.cleaned_data.get("password")
          try:
              user = models.User.objects.get(email=email)
              if user.check_password(password):
                  return self.cleaned_data
              else:
                  self.add_error("password", forms.ValidationError("Password is wrong"))
          except models.User.DoesNotExist:
              self.add_error("email", forms.ValidationError("User does not exist"))
  ```

  - Clean 메소드를 사용할 시에는 에러 처리를 각각 해줘야한다.   
    `: self.add_error(field, Error type)`
  - 오류가 없을 시에는 return 값은 `Cleaned_data`
  - user의 Password를 체크할 때는 `user.check_password(password)` 를 이용

### 3. [Authenticate](https://docs.djangoproject.com/en/3.0/topics/auth/default/#authenticating-users)

- `authenticate(request=None, **credentials)`  
  : request와 로그인 정보를 넘겨주면 backend로 넘어가 Authentication을 진행한다.  
  : 만약 PermissionDenied 에러가 발생하면 None 값을 반환한다.
- 로그인 정보의 ID는 username field를 사용한다.

### 4. [Context_processors](https://blog.isaccchoi.com/programing/Template-context-processors%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EC%97%AC-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EB%A7%88%EC%9D%8C%EB%8C%80%EB%A1%9C-%EB%A1%9C%EB%93%9C%ED%95%98%EA%B8%B0/)

- template에 정보를 추가하는 함수. 어떤 Template에서든 특정 변수에 대한 접근이 가능하게 만들어준다.
- Context_processors를 사용하는 과정은 링크 되어있는 페이지에 설명이 잘 되어있다.

### 5. 전체 코드

**[users / views.py]**

```python
from django.views import View
from django.shortcuts import render, redirect, reverse
from django.contrib.auth import authenticate, login, logout
from . import forms


class LoginView(View):
    def get(self, request):
        form = forms.LoginForm()
        return render(request, "users/login.html", {"form": form})

    def post(self, request):
        form = forms.LoginForm(request.POST)
        if form.is_valid():
            email = form.cleaned_data.get("email")
            password = form.cleaned_data.get("password")
            user = authenticate(request, username=email, password=password)
            if user is not None:
                login(request, user)
                return redirect(reverse("core:home"))
        return render(request, "users/login.html", {"form": form})

def log_out(request):
    logout(request)
    return redirect(reverse("core:home"))
```

**[users / forms.py]**

```python
from django import forms
from . import models


class LoginForm(forms.Form):
    email = forms.EmailField()
    password = forms.CharField(widget=forms.PasswordInput)

    def clean(self):
        email = self.cleaned_data.get("email")
        password = self.cleaned_data.get("password")
        try:
            user = models.User.objects.get(email=email)
            if user.check_password(password):
                return self.cleaned_data
            else:
                self.add_error("password", forms.ValidationError("Password is wrong"))
        except models.User.DoesNotExist:
            self.add_error("email", forms.ValidationError("User does not exist"))

```

**[templates / users / login.html]**

```django
{% extends "base.html" %}

{% block page_title %}
    Log In
{% endblock page_title %}

{% block search-bar %}
{% endblock search-bar %}

{% block content %}
    <form method="POST" action='{% url "users:login"%}'>
        {% csrf_token %}
        {{form.as_p}}
    <button>Login</button>
    </form>
{% endblock content %}
```

---

## > Login Form : Using [LoginView](http://ccbv.co.uk/projects/Django/3.0/django.contrib.auth.views/LoginView/)? [FormView](http://ccbv.co.uk/projects/Django/3.0/django.views.generic.edit/FormView/)?

### 1. Form View

- LoginView는 Login 작업을 username과 password로만 진행.
- 우리는 email을 이용하여 작업하고 싶기에 FormView를 사용.
- FormView를 사용하여 작업한 내용은 다음과 같다.

**[users / views.py]**

```python
from django.views import View
from django.views.generic import FormView
from django.shortcuts import render, redirect, reverse
from django.urls import reverse_lazy
from django.contrib.auth import authenticate, login, logout
from . import forms


class LoginView(FormView):

    template_name = "users/login.html"
    form_class = forms.LoginForm
    success_url = reverse_lazy("core:home")

    def form_valid(self, form):
        email = form.cleaned_data.get("email")
        password = form.cleaned_data.get("password")
        user = authenticate(self.request, username=email, password=password)
        if user is not None:
            login(self.request, user)
        return super().form_valid(form)


def log_out(request):
    logout(request)
    return redirect(reverse("core:home"))

```

- `reverse_lazy`
  - reverse와 기능은 같지만 호출 방식이 다르다.
  - reverse와 달리 자동으로 호출하지 않고 View가 필요로 할 때 호출한다.
  - 간단하게 말해서, View를 불러올 때 URL이 불려지지 않는 상황에 대한 처리가 들어가있는 함수. (**success라는 event가 항상 일어나는 것이 아니기 때문에**)
  - **"reverse_lazy는 reverse와 같은 기능인데 generic view에서 주로 사용한다.**
    **타이밍 로딩 문제로 generic view에서는 reverse는 사용할 수 없고 reverse_lazy를 사용해야한다."** - [link](https://wayhome25.github.io/django/2017/03/14/django-07-kilogram-03-signup/)

