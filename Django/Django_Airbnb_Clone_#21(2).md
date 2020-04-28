# Airbnb-Clone #21

- #21.5 Styling User Profile
- #21.6 Update Profile
- #21.7 Change Password
- #21.8 Customizing Stuff
- #21.9 Mixins are Awesome

---

## [RTFM](https://tailwindcss.com/docs/) : TailwindCSS

## [RTFM](https://docs.djangoproject.com/en/3.0/) : Django 

---

## > [UpdateView](http://ccbv.co.uk/projects/Django/3.0/django.views.generic.edit/UpdateView/) : 기존에 작성된 Form을 Update!

### 1. UpdateView?

- 기존에 저장되어있는 Form을 Update 하고자 할 때 유용하게  사용되는 View
- UpdateView는 Form을 만들어 주고 Form을 렌더링 시켜준다.
- Form에 대한 변경사항을 입력하고 Save 버튼을 클릭하면 업데이트 완료
- 이 일련의 과정이 100% 자동화 되어 있다!
- Form을 만들어주고, 렌더링 시켜주고, 필드들을 초기화 시켜주어 업데이트를 위한 환경을 만들어주고 변경 사항을 입력하고 저장할 수 있도록 해준다.
- Update를 하면 즉각적으로 유저를 get_absolute_url로 redirect 해준다.
  (데이터를 저장하고 다시  User Profile 페이지를 Return 해주는 작업을 UpdateView가 해준다.)

### 2. [get_object](http://ccbv.co.uk/projects/Django/3.0/django.views.generic.edit/UpdateView/#get_object)

- Update 할 시에, URL과 PK가 함께 사용되어야만 한다.
- get_object라는 메소드를 이용해서 객체의 PK를 얻어낼 수 있다.

### 3. 코드 예시

```python
class UpdateProfileView(UpdateView):

    model = models.User
    template_name = "users/update-profile.html"
    fields = (
        "first_name",
        "last_name",
        "avatar",
        "gender",
        "bio",
        "birthdate",
        "language",
        "currency",
    )

    def get_object(self, queryset=None):
        return self.request.user
```

---

## > [PasswordChangeView](http://ccbv.co.uk/projects/Django/3.0/django.contrib.auth.views/PasswordChangeView/) : 비밀번호 변경 시에!

### 1. PasswordChangeView?

- 말 그대로 비밀번호 변경에 대한 View
- 이처럼 장고에 내장 되어있는 View는 매우 편리하고 좋으나 통제력이 떨어지는건 사실.
- form 같은 것에 대한 Customizing이 힘들다.
- But, 어느정도 Customizing을 위한 Method들이 존재하니 Docs를 잘 참고하여 내 입맛대로 변경할 수 있도록 하자.
- 패스워드 Update가 완료되었을 때, success_url 을 반환해주어야 한다. 

### 2. [get_form](http://ccbv.co.uk/projects/Django/3.0/django.contrib.auth.views/PasswordChangeView/#get_form)

- 해당 뷰에서 사용하고 있는 form 객체를 반환하는 메소드
- form에 대한 Customizing을 할 때 필수적으로 사용되는 메소드이다.
- 주의할점은, 해당 뷰에서 각각의 form을 어떠한 이름으로 정의해놨는지 확인을 해줘야 한다는 것! (웹페이지의 관리자도구를 이용하여 html 소스를 참고할 것.)

### 3. 예시 코드

```python
class UpdatePasswordView(PasswordChangeView):
    template_name = "users/update-password.html"

    def get_form(self, form_class=None):
        form = super().get_form(form_class=form_class)
        form.fields["old_password"].widget.attrs = {"placeholder": "Current password"}
        form.fields["new_password1"].widget.attrs = {"placeholder": "New password"}
        form.fields["new_password2"].widget.attrs = {
            "placeholder": "Confirm new password"
        }
        return form

    def get_success_url(self):
        return self.request.user.get_absolute_url()
```

