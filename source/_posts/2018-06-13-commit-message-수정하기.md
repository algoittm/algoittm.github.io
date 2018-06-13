---
layout: post
title: commit 메세지 수정하기
author: canor
githublink: https://canorus.github.io
tags: [git]
---

git을 쓰다보면 커밋 메세지를 수정하고 싶을 때가 있습니다. 커밋 메세지에 추가할 게 생각났다거나, 커밋을 날려보내는 순간에 오타를 발견했다거나 뭐 그런 거요.

단순히 웹에 접속해서 커밋 메세지를 변경할 수도 있겠지만 커맨드라인에서도 커밋 메세지를 변경할 수 있는 기능이 있습니다.

### 아직 commit을 push하지 않은 경우

`git commit --amend` 명령어를 사용하면 가장 최근의 commit 메세지를 불러오게 됩니다.

최상단의 커밋 메세지를 수정한 후 `git push`해서 커밋을 날려보내면 됩니다.

### push한 최근 commit의 메세지를 수정하려는 경우

`git commit --amend` 명령어를 사용하는 것은 동일하지만 `git push --force` 명령어를 사용해서 강제로 덮어씌워야 합니다.

### 최근보다 이전의 commit 메세지를 수정하려는 경우

`git rebase -i HEAD~n` 명령어를 사용해서 최근으로부터 `n` 개의 commit 메세지를 불러올 수 있습니다.

불러온 commit 메세지들은 EDITOR 최상단에 자리하고 있으며 수정해서 저장한 후 `git push --force` 명령어를 사용해서 덮어씌워야 합니다.

- - -

via 

- original post from [Fabulam Canor](https://canor.cf/2018/06/13/commit-message-수정하기/)

- [Changing a commit message - User Documentation](https://help.github.com/articles/changing-a-commit-message/)