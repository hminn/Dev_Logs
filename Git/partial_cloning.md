# 하위 디렉토리만 Clone하기



##### 1. `git init`

-  Clone 작업을 진행할 디렉토리 내부(Clone 파일을 받아올 디렉토리)에서 git init

##### 2. `git config core.sparseCheckout true`

- 부분 체크아웃 설정을 true로 설정해준다.

##### 4. `git remote add -f origin <git 주소>`

##### 5. `echo "디렉토리1/디렉토리2/" >> .git/info/sparse-checkout`

- 부분 체크아웃을 받을 세부 디렉토리를 설정에 추가한다.
- 받아오고 싶은 디렉토리가 여러 개라면 위의 명령으로 한번 더 추가 디렉토리를 지정해주면 된다.

###### 6. `git pull origin master`
