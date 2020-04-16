# Airbnb-Clone #17

- #17.0 Github Login part One
- #17.1 Github Login part Two
- #17.2 Getting Github Profile
- #17.3 Recap OMG
- #17.4 Finishing Github Login

---

## > Github Login

### 1. [OAuth](https://minwan1.github.io/2018/02/24/2018-02-24-OAuth/)

- OAuth는 Open Authorization, Open Authentication 뜻하는 것으로 애플리케이션(페이스북,구글,트위터)(Service Provider)의 유저의 비밀번호를 Third party앱에 제공 없이 인증,인가를 할 수 있는 오픈 스탠다드 프로토콜이다.
- [Authorizing OAuth apps](https://developer.github.com/apps/building-oauth-apps/authorizing-oauth-apps/)

- Github OAuth Apps 이용
  1. github.com 에서 자기 계정 Settings
  2. Developer settings
  3. New OAuth App
  4. 정보 입력하고 Register
  5. Client ID, Client Secret을 .env 파일에 옮겨 적어둔다.

- The flow to authorize users for your app
  1. Users are redirected to request their GitHub identity
  2. Users are redirected back to your site by GitHub
  3. Your app accesses the API with the user's access token

### 2. if & return -> try & except

- 에러 발생 시, return redirect 하는 작업을 if & return으로 작성하면 코드가 매우 길어지고 중복이 많아 비효율적이다.
- Exeption Class를 하나 생성해서 try & except 구문으로 오류 처리를 하자!
- 이러한 Skill의 구조를 잘 이해하면 다른 부분으로도 확장이 가능할 듯 싶다.

***"자세한 과정은 코드를 보며 이해할 것,,"***

```python
def github_login(request):
    client_id = os.environ.get("GH_ID")
    redirect_uri = "http://127.0.0.1:8000/users/login/github/callback"
    return redirect(
        f"https://github.com/login/oauth/authorize?client_id={client_id}&redirect_uri={redirect_uri}&scope=read:user"
    )


class GithubException(Exception):
    pass


def github_callback(request):
    try:
        client_id = os.environ.get("GH_ID")
        client_secret = os.environ.get("GH_SECRET")
        code = request.GET.get("code", None)
        if code is not None:
            token_request = requests.post(
                f"https://github.com/login/oauth/access_token?client_id={client_id}&client_secret={client_secret}&code={code}",
                headers={"Accept": "application/json"},
            )
            token_json = token_request.json()
            error = token_json.get("error", None)
            if error is not None:
                raise GithubException()
            else:
                access_token = token_json.get("access_token")
                api_request = requests.get(
                    "https://api.github.com/user",
                    headers={
                        "Authorization": f"token {access_token}",
                        "Accept": "application/json",
                    },
                )
                profile_json = api_request.json()
                print(profile_json)
                username = profile_json.get("login", None)
                if username is not None:
                    name = profile_json.get("name")
                    email = profile_json.get("email")
                    bio = profile_json.get("bio")
                    try:
                        user = models.User.objects.get(email=email)
                        if user.login_method != models.User.LOGIN_GITHUB:
                            raise GithubException()
                    except models.User.DoesNotExist:
                        user = models.User.objects.create(
                            email=email,
                            first_name=name,
                            username=email,
                            bio=bio,
                            login_method=models.User.LOGIN_GITHUB,
                        )
                        user.set_unusable_password()
                        user.save()
                    login(request, user)
                    return redirect(reverse("core:home"))
                else:
                    raise GithubException()
        else:
            raise GithubException()
    except GithubException:
        # send error Messages
        return redirect(reverse("users:login"))
```

