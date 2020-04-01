## > git add 취소하기

### git add 취소하기(파일 상태를 Unstage로 변경하기)

- 아래와 같이 실수로 git add * 명령을 사용하여 모든 파일을 Staging Area에 넣은 경우, 
- Staging Area(git add 명령 수행한 후의 상태)에 넣은 파일을 빼고 싶을 때가 있다.

```bash
// 모든 파일이 Staged 상태로 바뀐다.
$ git add *
// 파일들의 상태를 확인한다.
$ git status
On branch master
Changes to be committed:
(use "git reset HEAD <file>..." to unstage)
  renamed:    README.md -> README
  modified:   CONTRIBUTING.md
```

이때, git reset HEAD [file] 명령어를 통해 git add를 취소할 수 있다.

- 뒤에 파일명이 없으면 add한 파일 전체를 취소한다.
- CONTRIBUTING.md 파일을 Unstaged 상태로 변경해보자.

```bash
// CONTRIBUTING.md 파일을 Unstage로 변경한다.
$ git reset HEAD CONTRIBUTING.md
Unstaged changes after reset:
M	CONTRIBUTING.md

// 파일들의 상태를 확인한다.
$ git status
On branch master
Changes to be committed:
(use "git reset HEAD <file>..." to unstage)
  renamed:    README.md -> README
Changes not staged for commit:
(use "git add <file>..." to update what will be committed)
(use "git checkout -- <file>..." to discard changes in working directory)
  modified:   CONTRIBUTING.md
```



## > git commit 취소하기

### Commit 취소하기

- 완료한 Commit을 취소
  1. 너무 일찍 commit한 경우
  2. 특정 파일을 빼먹고 commit한 경우

```bash
// commit 목록 확인
$ git log

// [방법 1] commit을 취소하고 해당 파일들은 staged 상태로 워킹 디렉터리에 보존
$ git reset --soft HEAD^
// [방법 2] commit을 취소하고 해당 파일들은 unstaged 상태로 워킹 디렉터리에 보존
$ git reset --mixed HEAD^ // 기본 옵션
$ git reset HEAD^ // 위와 동일
$ git reset HEAD~2 // 마지막 2개의 commit을 취소
// [방법 3] commit을 취소하고 해당 파일들은 unstaged 상태로 워킹 디렉터리에서 삭제
$ git reset --hard HEAD^
```



## > git push 취소하기

- 이 명령을 사용하면 자신의 local의 내용을 remote에 강제로 덮어쓰기를 하는 것이기 때문에 주의해야 한다.
  - 되돌아간 commit 이후의 모든 commit 정보가 사라지기 때문에 주의해야 한다.
  - 특히, 협업 프로젝트에서는 동기화 문제가 발생할 수 있으므로 팀원과 상의 후 진행하는 것이 좋다.

1. 워킹 디렉터리에서 commit 되돌린다.

   - 가장 최근의 commit을 취소하고 워킹 디렉터리를 되돌린다.

   ```bash
   // 가장 최근의 commit을 취소 (기본 옵션: --mixed)
   $ git reset HEAD^
   
   // Reflog(브랜치와 HEAD가 지난 몇 달 동안에 가리켰었던 커밋) 목록 확인
   $ git reflog 또는 $ git log -g
   // 원하는 시점으로 워킹 디렉터리를 되돌린다.
   $ git reset HEAD@{number} 또는 $ git reset [commit id]
   ```

2. 되돌려진 상태에서 다시 commit을 한다.

   ````bash
   $ git commit -m "Write commit messages"
   ````

3. 원격 저장소에 강제로 push 한다.

   ```bash
   $ git push origin [branch name] -f
   또는
   $ git push origin +[branch name]
   ```

   

   **==TIP== 경고를 무시하고 강제로 push 하기**

   - [방법 1] -f 옵션
     –force 옵션과 동일하다.
   - [방법 2] +[branch name]
     해당 branch를 강제로 push한다.