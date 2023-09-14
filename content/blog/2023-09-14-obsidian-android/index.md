---
layout: post
title: '안드로이드에서 옵시디안 깃으로 동기화하기'
date: 2023-09-14 00:01
tags: [obsidian-git, android]
description: 옵시디안은 로컬 파일 시스템을 이용하기 때문에 장비간 동기화가 이루어지지 않는다. 서로 다른 장비간 데이터 동기화를 위해서는 별도 설정이 필요하다. 옵시디안 싱크라는 유료 서비스도 있지만 옵시디안 깃을 이용하여 무료로 데이터 동기화를 설정하는 것도 가능하다.
draft: false
---

옵시디안은 로컬 파일 시스템을 이용하기 때문에 장비간 동기화가 이루어지지 않는다. 서로 다른 장비간 데이터 동기화를 위해서는 별도 설정이 필요하다. 옵시디안 싱크라는 유료 서비스도 있지만 옵시디안 깃을 이용하여 무료로 데이터 동기화를 설정하는 것도 가능하다.

특별히 android 에서 obsidian-git 설정을 하기 위해서는 안드로이드에서 깃을 사용할 수 있어야 한다.

먼저 안드로이드에서 깃을 설치하기 위해서는 termux 라는 앱을 설치하고 터미널로 Android 에 접속할 수 있어야 한다.

하지만 termux 앱은 오래된 앱이라서 최신 안드로이드 버젼에서는 플레이스토어에서 검색도 되지 않을 뿐 아니라 웹링크로 접근을 해도 바로 설치가 되지 않는다.

최신 폰에서 termux 를 설치하는 방법은 아래 포스트를 참고하기 바란다.

https://www.techrepublic.com/article/how-to-install-git-on-android/


termux 설치가 완료되었으면 아래 명령들을 순서대로 실행한다.

설치프로그램 업데이트
```bash
pkg update && pkg upgrade
```

<br/>

깃설치
```bash
pkg install git
```

<br/>

스토리지 셋업(파일시스템 접근 권한 허용 필요)
```bash
termux-setup-storage
```

<br/>

깃헙 CLI도구 설치
```bash
pkg install gh
```

<br/>

깃헙 로그인
```bash
gh auth login
```
이때 깃헙에서 발급한 엑세스토큰 필요한데 codspaces 읽고쓰기 권한과 `git clone` 시 필요한 접근권한을 허용해 주어야 한다.
그런데 `git clone` 시 필요한 접근 권한이 무엇인 지 정확히 확인은 못함;(8개 정도 필요할꺼 같은 권한 모두 부여햇더니 git clone 성공함)


<br/>

깃헙 환경설정
```bash
git config --global user.name "name"
git config --global user.email "email"
```


옵시디안 깃 레포를 클론
```bash
cd /storage/emulated/0
git clone https://github.com/your-account/obsidian-repo.git
```



이후 해당 폴더를 vault 경로로 사용하면 끄읕~


<br/>
<br/>

### Ref.
https://www.greghilston.com/post/how-i-use-obsidian-mobile-with-git-on-android/
