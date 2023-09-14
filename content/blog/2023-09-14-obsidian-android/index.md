---
layout: post
title: '안드로이드에서 옵시디안 깃으로 동기화하기'
date: 2023-09-14 00:01
tags: [obsidian-git, android]
description: 
draft: false
---

옵시디안은 로컬 파일 시스템을 이용하기 때문에 장비간 동기화가 이루어지지 않는다. 서로 다른 장비간 데이터 동기화를 위해서는 별도 설정이 필요하다. 옵시디안 싱크라는 유료 서비스도 있지만 옵시디안 깃을 이용하여 무료로 데이터 동기화를 설정하는 것도 가능하다.

특별히 android 에서 obsidian-git 설정을 하기 위해서는 안드로이드에서 깃을 사용할 수 있어야 한다.

먼저 안드로이드에서 깃을 설치하기 위해서는 termux 라는 앱을 설치하고 터미널로 Android 에 접속할 수 있어야 한다. 해당 방법은 아래 포스트를 참고하기 바란다.

https://www.techrepublic.com/article/how-to-install-git-on-android/


이후 영구 저장소의 폴더를 생성하고 미리 준비해둔 옵시디안 깃 레포를 클론한다.

https://gist.github.com/Makeshift/43c7ecb3f1c28a623ea4386552712114
