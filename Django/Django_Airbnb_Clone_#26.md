## > 배포

### 1. Configuring EB(Elastic Beanstalk) Using AWS CLI

> 1. [AWS CLI 설치](https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/eb-cli3-install-windows.html)
>
>    ```bash
>    pipenv install awsebcli --dev
>    ```
>
> 2. 설치가 끝나면 터미널에 `eb` 를 쳐서 확인해본다.
>
> 3. `eb init` 에서 10(Seoul) 을 입력 (코어 설정)
>
> 4. `(aws-access-id):` 와 같은 메세지가 뜬다.
>
> 5. 서비스 창에 `IAM` 검색
>
> 6. 다음과 같은 순서로 진행
>
>    > 1. 대시보드 -> 사용자 -> 사용자추가
>    > 2. 사용자 이름 작성
>    > 3. AWS 액세스 유형 선택 (Airbnb는 프로그래밍 방식 액세스)
>    > 4. 다음으로 이동
>    > 5. 권한설정 : '기존 정책 직접 연결(?)' -> AdministratorAccess(관리자 권한) [Airbnb 기준]
>    > 6. 사용자 생성이 완료되면 ID와 Key를 받을 수 있음.
>    > 7. ID와 Key는 한 번 밖에 열람이 안되므로 csv 파일을 저장하던 스샷을 남기던 해야함
>
> 7. ID와 Key를 Terminal에 입력
>
> 8. CodeCommit? : No (Airbnb 기준)
>
> 9. Set up SSH for your instances? : No (Airbnb 기준)  
>    (서버를 직접 들어가서 파일을 만들거나 지우는 등의 여러 작업을 하고 싶을 때는 YES! But, 이 부분에서 많은 에러가 난다고 함)



### 2. [Django 애플리케이션을 Elastic Beanstalk에 배포](https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/create-deploy-python-django.html)

> 1. Tutorial 따라하기
>
>    > 1. `.ebextensions` 라는 폴더를 프로젝트 루트 디렉토리에 생성
>    >
>    > 2. `.ebextensions` 폴더 내에 `django.config` 파일 생성
>    >
>    > 3. `django.config` 에 다음과 같이 작성
>    >
>    >    ```
>    >    option_settings:
>    >      aws:elasticbeanstalk:container:python:
>    >        WSGIPath: config/wsgi.py
>    >    ```
>    >
>    > 4. ``eb create airbnb-clone`` : 가상환경 만들기  
>    >
>    >    - (eb는 프로젝트의 모든 파일을 압축시키고 AWS S3로 업로드한다.)  
>    >    - (서버에 필요한 요소들을 자동적으로 생성해준다.)
>    >    - (프로젝트 가상환경은 2개를 만들 것을 추천. 하나는 Testing용, 다른 하나는 프로덕션 서버용[실제 배포])
>    >    - (eb에서 무언가를 변경하고 싶을 때, 먼저 commit을 진행한 후 `eb deploy`를 해야한다. )