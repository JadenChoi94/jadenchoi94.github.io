---
title: '[Git]rebase'
excerpt: rebase에 대해 이해하기
categories:
    - Etc

tag:
    - github
    - git
    

comments: true
last_modified_at: 2021-06-28T10:00:00+09:00
toc: true
---

![post-thumbnail](https://media.vlpt.us/images/suasue/post/837ff1a4-74f4-4a3e-9184-f565585f3465/%EC%A0%9C%EB%AA%A9%EC%9D%84%20%EC%9E%85%EB%A0%A5%ED%95%B4%EC%A3%BC%EC%84%B8%EC%9A%94._001%20(5).png)

### git rebase를 사용하는 이유

두 개의 브랜치를 병합하기 위한 방법으로 Merge와 Rebase가 있다.
merge를 사용하게 되면 merge commit이 안 생겨서 commit history가 더러워진다.
rebase로 commit history를 조작하여 깔끔하게 관리한다.

### git rebase 전략

branch에서 작업 시 2~3개 commit이 생기면 rebase한다.
(업데이트된 base가 없어도 squash로 commit message를 정리하기 위해 rebase한다.)
즉, branch 하나당 commit 하나가 되도록 유지해야 한다.

### git rebase 사용하기

main branch로 가서 `git pull origin main`

feature branch로 가서 `git rebase -i main`하면 commit log들이 있는 에디터 창이 뜬다. 모든 commit 앞에 `pick`이 적혀있는데, 가장 위(가장 오래된) commit의 `pick`만 남겨두고 나머지는 `s`로 바꿔준다.

`:wq` 또는 `ctrl+x`로 저장하고 나가면 commit message를 수정할 수 있는 에디터 창이 한 번 더 나타난다. 하나의 commit message만 남기고 나머지는 주석 처리하거나 삭제한다. 이 commit message는 branch를 대표하는 commit message이므로 굉장히 상세해야 한다.

```
git log`로 정상적으로 rebase가 됐는지 확인한다.
push를 시도하는데 만약 이미 해당 브랜치에 pull request가 있을 경우 push가 안될 때가 있다. 왜냐하면 이미 올라가 commit과 올리려는 commit의 히스토리가 달라졌기 때문이다.
히스토리를 조작한 것은 우리가 의도한 것이므로 -f 옵션을 사용하여 force
push를 한다. `git push origin feature/브랜치명 -f
```

### git rebase 충돌 발생 시

`git rebase -i main`을 했는데 conflict가 발생하는 경우가 있다.
충돌이 발생한 부분을 수정 후 `git add .`을 한다. (이 때 `git commint`은 하지 않는다.) 그리고 rebase를 계속 진행하기 위해 `git rebase --continue`를 한다. 충돌이 발생하지 않을 때까지 add와 continue를 반복한다.
계속 해결이 안 된다면 `git rebase --abort`로 rebase를 진행하기 전 상황으로 돌아갈 수 있다.