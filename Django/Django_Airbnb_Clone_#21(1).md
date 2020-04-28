# Airbnb-Clone #21

- #21.0 Messages part One
- #21.1 Messages part Two
- #21.2 Styling Messages
- #21.3 User Profile part One
- #21.4 User Profile part Two

---

## [RTFM](https://tailwindcss.com/docs/) : TailwindCSS

---

## > [Django Messages Framework](https://docs.djangoproject.com/en/3.0/ref/contrib/messages/)

### 참고 자료

- [Message framework 알아보기](https://ssungkang.tistory.com/entry/Djangomessage-framework-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0)

---

### 1. What is Messages Framework?

- `로그인을 실패하였습니다.` 등 과 같은 1회성 메세지를 담는 용도로 사용한다.
- 메세지를 남기는 방식은 HttpRequest 인스턴스를 통해 남기며 1회성이기 때문에 새로고침하면 사라진다.
- 전체 유저에게 한 번에 메세지를 보낼 수 없고 한 명의 유저에 대해서 메세지를 보낼 수 있다.

### 2. Message Level

- message 에는 레벨이 존재합니다. 레벨의 역할은 레벨에 다른 설정을 해줌으로서 각 레벨을 구별해준다. 예를 들어 에러를 의미하는 message는 빨간 글씨로, 성공을 의미하는 글씨는 초록색 글씨로 구현을 할 수 있다. 메세지의 종류는 다음과 같다.
  - DEBUG : 디폴트 설정 상으로 메세지를 남겨도 무시합니다.
  - INFO
  - SUCCESS
  - WARNING
  - ERROR

### 3. Example Code

```django
{% if messages %}
<ul class="top-0 absolute mx-auto left-0 right-0 flex justify-center">
    {% for message in messages %}
        <li class="message font-medium bg-gray-700 z-10 rounded-full text-white py-4 px-6 {% if message.tags %}{{ message.tags }}{% endif %}">{{ message }}</li>
    {% endfor %}
</ul>
{% endif %}
```

---

## > [Absolute URL](https://docs.djangoproject.com/en/3.0/ref/models/instances/#get-absolute-url)

### 참고 자료

- [get_absolute_url 강추(?), 사용법](https://wayhome25.github.io/django/2017/05/05/django-url-reverse/) 

---

- Admin Panel에서 객체(object)들을 보고 싶다면, get_absolute_url이 매우 요긴하게 쓰인다.

---

## > Issue Check

### 1. Room List View에서 host를 클릭했을 때 해당 host의 profile로 이동하는 기능은 구현하였으나, 그 상황에서 nav의 Profile을 눌렀을 때 나의 Profile로 이동하는 것이 아닌 RoomListView에서 클릭했던 host의 Profile로 넘어가는 문제!

- **문제 해결 과정**

  1. nav 탭의 Profile을 눌렀을 때 유저의 Profile로 넘어가는 View를 확인해보자.

     ```python
     class UserProfileView(DetailView):
         model = models.User
     ```

  2. DetailView에서 기본적으로 짜놓은 User에 대한 정의는 '로그인한 유저'가 아닌 'View에서 찾고자 하는 사람' 이다.

  3. 쉽게 말해, host를 클릭했을 때의 Profile 상에서 User는 로그인한 유저가 아니라 내가 클릭한 host가 된다는 것.

  4. 따라서 DetailView의 내부구조를 파보면서 어떤 부분을 고정해주어야 해당 문제가 발생하지 않을지 탐색해봐야 한다.

  5. 그에 해당하는 속성이 context_object_name. 다음과 같이 설정을 해준다.

     ```python
     class UserProfileView(DetailView):
     
         model = models.User
         context_object_name = "user_obj"
     ```

  6. user object를 가리키는 user_obj로 context_object_name을 설정해준다.

  7. 템플릿에서 변수를 끌어다 쓸 때에도 `{{user_obj.first_name}}` 와 같이 user_obj에 대한 data를 Showing 해주면 된다.

