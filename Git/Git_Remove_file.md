## Git : Remove File

> **Github에 잘못 올라간 파일 삭제하기**
>
> - github에 잘못 올라간 파일을 삭제한다.
> - .gitignore을 설정하지 않고 push한 경우, 잘못 올라간 파일을 삭제한다.

### > git rm ? git rm --cached ?

```bash
// 원격 저장소와 로컬 저장소에 있는 파일을 삭제한다.
$ git rm [File Name]

// 원격 저장소에 있는 파일을 삭제한다. 로컬 저장소에 있는 파일은 삭제하지 않는다.
$ git rm --cached [File Name]

출처 : https://gmlwjd9405.github.io/2018/05/17/git-delete-incorrect-files.html
```
