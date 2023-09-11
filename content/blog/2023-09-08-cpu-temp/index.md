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

CPU 온도를 기록하는 셀스크립트로서 `temp.sh` 파일을 아래와 같이 작성할 수 있다.
```bash
#!/bin/bash

echo '' >> temp.log
date >> temp.log
t=$(cat /sys/devices/virtual/thermal/thermal_zone0/temp)
echo "${t:0:2}.${t:2:3} C" >> temp.log
cat temp.log
```
<br/>

결과
```
min@ubuntu ~$ ./temp.sh

Mon 11 Sep 14:05:36 KST 2023
50.625 C

Mon 11 Sep 14:05:37 KST 2023
47.777 C

Mon 11 Sep 14:05:38 KST 2023
47.222 C

Mon 11 Sep 14:05:39 KST 2023
46.666 C

Mon 11 Sep 14:05:39 KST 2023
49.444 C

Mon 11 Sep 14:05:40 KST 2023
50.000 C

Mon 11 Sep 14:05:41 KST 2023
49.444 C
```
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
