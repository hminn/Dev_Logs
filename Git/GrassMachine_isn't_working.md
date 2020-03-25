#### Github의 Commit Log가 남지않을때? (잔디 심는 기계 고장)

> **들어가며**

: 1일 1커밋을 시작한지 이틀째 되는 날, 나의 소중한 커밋이들이 github commit log(속칭 잔디밭)에 찍히지 않고 있다는 사실을 발견하게 되었다. 이미 날아간 커밋 친구들에 대한 아쉬움을 뒤로하고 무엇이 문제인지 구글링을 해봤다.

> **문제 : *"git config에 user.name과 user.email이 제대로 등록되지 않아서.."***

: 문제는 무척 간단했다. git config에 user.name과 user.email이 잘못 등록되어 있었다. VScode terminal과 github desktop, 두 아이를 이용하여 각각의 프로젝트를 commit 하곤 했는데, 둘 다 잔디가 심어지지 않는 걸로 보아하니 두 개 다 잘못 등록되어있으리라 짐작했다.

> **문제해결**

- 해결1 [*in Terminal*]

```
git config user.name [git에 등록되어있는 username]
git config user.email [git에 등록되어있는 user_email]
```

: 제대로 된 name과 email을 추가한 후, 다시 push 해봤더니 잘만 찍히더라야.

- 해결2 [*in Github_desktop*]

```
File tap -> options -> git -> 제대로 등록
```

: 이 경우에도 제대로 등록했더니 잘만 찍혔다.



이제 해결했으니 열싐히 코딩하고 틈틈이 커밋하자 (｡･∀･)ﾉﾞ

