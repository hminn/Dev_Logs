## Git basic

> **Git Repository 연결**
> : Local 저장소(repository)를 원격(remote) 저장소에 연결하고, 로컬 저장소에서 변경된 내용을 원격 저장소에 반영(push)하는 방법

```
1. git init
: 로컬 저장소에 .git 이라는 하위 디렉토리를 만든다.
  이는 저장소에 필요한 뼈대 파일(Skeleton)이 들어 있다.
  해당 명령만으로는 아직 프로젝트의 어떤 파일도 관리하지 않는다.

2. git remote add origin [HTTPS or SSH]
: [HTTPS or SSH]에 해당하는 git 저장소와 Local 저장소를 연결

3. git add .
: Git이 관리하게 할 파일 및 디렉토리를 추가한다.
: 위 명령어의 경우에는 현재 경로의 모든 파일 및 디렉토리를 추가함

4. git commit -m "commit message"
: commit message와 함께 변경사항을 commit한다.

5. git push origin master
: commit한 변경사항을 원격 저장소에 반영한다.

# .gitignore?
: 간혹, github에 올리고 싶지 않은 파일들이 있을 수 있다. 
  보안과 관련되어있어 공개되면 안되는 정보라든지, 
  굳이 올릴 필요가 없는 파일 등,, 
  gitignore은 그런 파일들이 올라가지 않게 막아주는 필터 역할을 한다.
```

