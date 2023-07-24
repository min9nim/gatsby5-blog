---
layout: post
title: '간단하게 랜덤 문자열을 만드는 방법'
date: 2023-07-24 00:01
tags: [js, random]
description: 
draft: false
---

바닐라 js 를 이용해 간단하게 랜덤 문자열을 만들어내는 방법

```js
Math.random().toString(36).slice(2)
```

예시)
![](./random-js.png)
