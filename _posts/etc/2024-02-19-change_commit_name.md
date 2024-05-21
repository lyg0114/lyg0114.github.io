---
title: "git commit name을 수정해 보자"
date: 2024-02-19 15:27:00 +09:00
categories: [ 기타 ]
tags:
  [
    Git, Commit
  ]
---

1. 명령줄에서, 수정하고자 하는 커밋이 포함된 리포지토리로 이동.
2. 텍스트 편집기에서 마지막 `n` 개 커밋의 목록을 표시하려면 `git rebase -i HEAD ~ n` 명령 입력.

```bash
# Displays a list of the last 3 commits on the current branch
$ git rebase -i HEAD~3
```

아래의 shell 화면이 출력.

```bash
pick e499d89 Delete CNAME
pick 0c39034 Better README
pick f7fde4a Change the commit message but push the same commit.

# Rebase 9fdb3bd..f7fde4a onto 9fdb3bd
#
# Commands:# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message# x, exec = run command (the rest of the line) using shell#
# These lines can be re-ordered; they are executed from top to bottom.#
# If you remove a line here THAT COMMIT WILL BE LOST.#
# However, if you remove everything, the rebase will be aborted.#
# Note that empty commits are commented out
```

1. 변경하려는 각 커밋 메시지 앞의 `pick` 키워드를 `reword`로 변경후 `[wq]`
2. 화면이 하나 더 나올텐데 여기서 새 커밋 메시지를 입력하고 파일을 저장한 & 종료 `[wq]`
3. 변경 내용을 GitHub로 푸시할 준비가 되면 `push --force` 명령을 사용하여 이전 커밋을 강제로 푸시.

```bash
git push --force origin [branch-name]  # (가능하면 PUSH 하기전에 다 처리하고 push 하자)
```

### 결론 
처음부터 커밋을 잘 하자...


