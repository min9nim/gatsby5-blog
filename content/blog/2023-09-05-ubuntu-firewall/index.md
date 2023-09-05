---
layout: post
title: 'ubuntu 방화벽 관리'
date: 2023-09-05 00:01
tags: [ubuntu, iptables]
description: 우분투에서 방화벽을 관리하는 명령어들 모음
draft: false
---

우분투에서 방화벽을 관리하는 명령어들 모음.


iptables 조회
```bash
sudo iptables  -L
```

포트 허용 규칙
```bash
sudo iptables -A INPUT -p tcp --dport 42001 -j ACCEPT
```


포트 차단 규칙
```bash
sudo iptables -A INPUT -p tcp --dport 42001 -j DROP
```


정책 삭제
```bash
sudo iptables -D INPUT -p tcp --dport 80 -j DROP
```


열린 포트 확인
```bash
netstat -nap | grep 42001
```


방화벽 활성화
```bash
sudo ufw enable
```

방화벽 상태 확인
```bash
sudo ufw status
```
