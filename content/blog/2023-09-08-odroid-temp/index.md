---
layout: post
title: 'odroid CPU 온도 체크'
date: 2023-09-08 00:01
tags: [ubuntu, odroid,temperature]
description: ubuntu 터미널에서 깃 브랜치를 표시하는 방법
draft: false
---

odroid 에서는 lmsensors 를 사용할 수 없으며 아래와 같은 명령어를 사용한다.

43125 는 43.125 도를 의미한다.

```bash
user1@ubuntu ~$ cat /sys/devices/virtual/thermal/thermal_zone0/temp
43125
```


Ref)
https://forum.odroid.com/viewtopic.php?t=31295
