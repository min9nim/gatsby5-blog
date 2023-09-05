---
layout: post
title: 'ubuntu 터미널에 git 브랜치 표시'
date: 2023-09-05 00:01
tags: [ubuntu, git-branch]
description: ubuntu 터미널에서 깃 브랜치를 표시하는 방법
draft: false
---

ubuntu 터미널에서 깃 브랜치를 표시하는 방법.

~/.bashrc 맨 끝에 아래 내용 추가 
```bash
parse_git_branch() {
    git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/(\1)/'
}
export PS1="\e[01;32m\u@\h \[\e[34m\]\w\[\e[91m\]\$(parse_git_branch)\[\e[00m\]$ "
```

적용 후 터미널을 다시 열거나 `source ~/.bashrc` 를 하면 적용됩니다.



Ref)
https://inasie.tistory.com/44

