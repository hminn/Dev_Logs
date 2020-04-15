# Airbnb-Clone #16

- #16.0 Setting up Mailgun
- #16.1 Dotenv
- #16.2 Verifying Email part One
- #16.3 Verigying Email part Two
- #16.4 Completing Verification

---

## > Email Verification

### 1. Django : [Sending email](https://docs.djangoproject.com/en/3.0/topics/email/#module-django.core.mail)

- Mail is sent using the SMTP host and port specified in the [`EMAIL_HOST`](https://docs.djangoproject.com/en/3.0/ref/settings/#std:setting-EMAIL_HOST) and [`EMAIL_PORT`](https://docs.djangoproject.com/en/3.0/ref/settings/#std:setting-EMAIL_PORT) settings. The [`EMAIL_HOST_USER`](https://docs.djangoproject.com/en/3.0/ref/settings/#std:setting-EMAIL_HOST_USER) and [`EMAIL_HOST_PASSWORD`](https://docs.djangoproject.com/en/3.0/ref/settings/#std:setting-EMAIL_HOST_PASSWORD) settings.
- 위의 Setting Data들은 어디서 받아와야할까? : **[Mailgun](https://www.mailgun.com/)**
- ***Mailgun에서 Setting Data 받아오기***
  1. Mailgun 가입
  2. Sending -> Domain Settings
  3. 하단에 SMTP Settings
  4. 위에서부터 3줄이 차례대로 EMAIL_HOST, EMAIL_PORT, EMAIL_HOST_USER  
     (EMAIL_PORT는 세개중에 하나 골라서)
  5. Settings.py에 입력한다.

### 2. Dotenv

>  포트번호, DB 관련 정보등 전역으로 필요한 변수(정보)들이 존재한다. 때로는 이러한 정보들이 오픈소스(깃허브)에 프로젝트를 공개했을 경우, 민감한 정보들을 다른 사람으로부터 숨겨야(보안성)할 경우가 생긴다. **dotenv 패키지**를 사용하여 환경변수 파일을 외부에 만들고, 접근 관리할 수 있다.

1. django-dotenv 설치 : `pipenv install django-dotenv` 

2. .env 파일을 생성 (환경변수들을 저장할 파일)

3. 해당 파일에 EMAIL_HOST_USER, EMAIL_HOST_PASSWORD 정보를 저장

4. manage.py에 다음과 같이 내용을 추가  
   : main() 함수를 call 하기 전에 dotenv.read_dotenv()부터 실행시키기 위함

   ```python
   if __name__ == "__main__":
       dotenv.read_dotenv()
       main()
   ```

5. Settings.py에 다음과 같이 추가

   ````python
   EMAIL_HOST_USER = os.environ.get("MAILGUN_USERNAME")
   EMAIL_HOST_PASSWORD = os.environ.get("MAILGUN_PASSWORD")
   ````

6. .gitignore에 .env 추가

### 3. Verify_email

- 이메일 검증 Event가 회원가입 시뿐만 아니라, 이메일 변경시에도 일어난다.

- 따라서 이메일 검증 함수를 SignUpView에 작성하기보다는 User Models에 작성

- 코드는 다음과 같다.

  ```python
      email_verified = models.BooleanField(default=False)
      email_secret = models.CharField(max_length=120, default="", blank=True)
      
      def verify_email(self):
          if self.email_verified is False:
              secret = uuid.uuid4().hex[:20]
              self.email_secret = secret
              html_message = render_to_string(
                  "emails/verify_email.html", {"secret": secret}
              )
              send_mail(
                  "Verify Airbnb Account",
                  strip_tags(html_message),
                  settings.EMAIL_FROM,
                  [self.email],
                  fail_silently=False,
                  html_message=html_message,
              )
          return
  ```

  - uuid : 난수 String을 생성하는 라이브러리
  - 만약 Email 내용에 html이 포함된 메세지를 보내고 싶다면 html_massage라는 인자에 위처럼 넘겨주어야 한다.
  - [render_to_string](https://docs.djangoproject.com/en/3.0/topics/templates/#django.template.loader.render_to_string) : 템플릿을 렌더링한 문자열을 리턴한다. 렌더링할 템플릿 파일명과 템플릿 내에 사용할 변수를 Dict의 형식으로 넘겨주면 문자열로 변환하여 리턴하는 형식.