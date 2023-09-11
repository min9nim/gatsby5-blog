---
layout: post
title: 'CPU 온도 체크'
date: 2023-09-08 00:01
tags: [ubuntu, odroid,mac,temperature]
description: odroid 에서는 lmsensors 를 사용할 수 없으며 아래와 같은 명령어를 사용한다.
draft: false
---

CPU 온도를 확인하는 명령어들

## ODROID
odroid 에서는 lmsensors 를 사용할 수 없으며 아래와 같은 명령어를 사용한다.

43125 는 섭씨 43.125도를 의미한다.

```bash
user1@ubuntu ~$ cat /sys/devices/virtual/thermal/thermal_zone0/temp
43125
```

<br/>
<br/>
<br/>

## MAC

```bash
sudo powermetrics --samplers smc | grep -i "CPU die temperature"
```

<br/>
<br/>

## Ref
- https://forum.odroid.com/viewtopic.php?t=31295
- https://kr.moyens.net/how-to/184495/
