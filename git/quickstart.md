---
icon: bullseye-arrow
---

# \[git] reflog - rebase 취소하기

git rebase를 잘못해서 돌아가고 싶은 경우가 있다.&#x20;

git commit 의 경우, git log 를 통해 돌아가고 싶은 commit 의 hash 를 찾아 해당 커밋으로 돌아갈 수 있지만,

하지만, git rebase 의 경우, commit 의 시점이 아니라 rebase 이전의 상태로 돌아가고자 하는 것이기 때문에, \
어디로 돌아가야 할지 모르겠다는 문제가 생긴다.&#x20;

이때 사용하는 것이 `git reflog`다.

**1. `reflog` 확인**

```bash
abc1234 HEAD@{0}: rebase: moving to main 
def5678 HEAD@{1}: commit: Fix issue in login 
789abcd HEAD@{2}: rebase: checkout main 
123efgh HEAD@{3}: commit: Add new feature 
456ijkl HEAD@{4}: rebase (start): checkout feature/epic
```

* `rebase (start):` 이 부분이 리베이스가 시작된 시점입니다.
* `HEAD@{4}`가 리베이스 이전 상태입니다.

**2. 리베이스 이전 상태로 되돌리기**

```
git reset --hard {해시}
```

#### git reflog

* Git에서 **로컬 저장소의 모든 참조(HEAD 포함)의 이동 기록을 보여주는 명령어**&#x20;
* 로컬에서 브랜치가 어디로 이동했는지, 커밋이 어덯게 변경되었는 지를 추적할 수 있는 로그다.&#x20;
* HEAD 가 특정 커밋을 가리킬 때마다 자동으로 저장 됩니다.&#x20;
* 작동 원리
  * **HEAD 이동**: `git commit`, `git merge`, `git checkout` 등으로 `HEAD`가 다른 커밋을 가리킬 때마다 해당 이벤트가`reflog`에 기록됩니다.
  * **로컬에서만 유효**: `reflog`는 로컬에서만 유지되며, 원격으로 푸시되거나 공유되지 않습니다. 따라서 로컬에서만 확인할 수 있습니다.

<figure><img src="../.gitbook/assets/스크린샷 2025-01-01 오후 6.09.18.png" alt=""><figcaption></figcaption></figure>

* 3542e5 : HEAD 가 가리키는 커밋 해시
* `HEAD@{0}` – reflog 인덱스다.&#x20;
* `reset: moving to HEAD` – 실행한 명령어 및 설명





{% hint style="info" %}
Want to learn about writing content from scratch? Head to the [Basics](https://github.com/GitbookIO/onboarding-template/blob/main/getting-started/broken-reference/README.md) section to learn more.
{% endhint %}
