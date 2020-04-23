# Airbnb-Clone #20-(2)

- #20.5 Room Cards part One
- #20.6 Room Cards part Two
- #20.7 Finishing Room Cards and Pagination
- #20.8 Footer, Social Login
- #20.9 Login
- #20.10 SignUp
- #20.11 Forms Shortcut
- #20.12 SO MUCH EASIER NOW

---

## [RTFM](https://tailwindcss.com/docs/) : 찾으면 다 나온다

---

## > [Responsive Design In Tailwind](https://tailwindcss.com/docs/responsive-design/#app)

### 1. Responsive Design?

- 웹 디자인 기법 중 하나로, 웹에 접속하는 디바이스에 반응하는 디자인을 말한다.

### 2. In Tailwind?

- Property와 Class Name들을 Mobile을 기준으로 작동하도록.
- 단적인 예로, 웹의 width에 반응하는 디자인을 추가하려고 한다면?  
  `xl:w-1/4 lg:w-5/12 md:w-1/2 sm:w-1/2`  
  위와 같이, 웹사이트의 size에 해당하는 width를 지정해주면 사이즈에 반응하여 width가 결정되는 Design이 완성된다!
- 위의 예시는 매우 간단한 수준의 Responsive Design.
- 다양한 문서들을 참고하여 수준 높은 Responsive Designed Website를 만들도록 하자!

---

## > Django Issues

### 1. Input Placeholder

- 로그인 페이지, 회원가입 페이지에서 입력받는 Input이 Django 방식으로 렌더링 되었을경우, Placeholder를 넣어주는 방법이 조금 특별하다(~~귀찮다~~).

  1. 해당 Form이 작성되어있는 forms.py로 이동

  2. Placeholder를 넣어줄 Field에 다음과 같은 옵션을 추가해준다.
     `widget=forms.EmailInput(attrs={'placeholder':'Email'})`

  3. 만약, 직접 작성한 Field가 아닌 상위 클래스에서 정의한 Field라면?

     ```python
     widgets = {
         		"first_name": forms.TextInput(attrs={"placeholder": "First Name"}),
                 "last_name": forms.TextInput(attrs={"placeholder": "Last Name"}),
                 "email": forms.TextInput(attrs={"placeholder": "Email"}),
             }
     ```

     위와 같이 입력하면 Placeholder가 적용이 된다!

### 2. Rendering Errors In Django

- 만약 회원가입 페이지에서 아이디 중복,  비밀번호 조건 미충족 등의 오류가 일어난다면 어떻게 해야할까?

- 이러한 오류를 사용자에게 알려줄 수 있도록 웹페이지에 Rendering하는 방법이 있다.

- 만약 Django에서 정의한 Form을 상속받아 사용했다면 해당 Field에 대한 오류는 이미 정의되어있어서 다음과 같이 적어주면 된다.

  ```django
  # [signup.html]
  
  {{form.email}}
  {{form.email.errors}}
  {{form.password}}
  {{form.password.errors}}
  ```

  : 위와 같이 Django의 방식으로 렌더링시킨 Input Part 아래에 Django의 방식대로 에러를 처리하는 코드를 적어주면 된다.

- 이미 정의되어있는 에러 외에 추가적인 에러 처리를 해주고 싶다면 다음과 같이 적어주자.

  ```django
  {% if form.email.errors %}
      {% for error in form.email.errors %}
      	{{error}}
      {% endfor %}
  {% endif %}
  ```

### 3. Looping