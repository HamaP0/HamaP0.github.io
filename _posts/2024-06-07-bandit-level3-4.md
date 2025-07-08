---
layout: post
title: "[Bandit] Level 3 → 4 풀이"
date: 2024-05-30
categories: wargame bandit
tags: [overthewire, bandit, linux]
---

> **목표:** 숨겨진 디렉토리 안의 숨겨진 파일에서 비밀번호 찾기

---

## 🔐 Level Info

- **사용자:** banditX
- **비밀번호:** 이전 레벨에서 획득
- **접속 명령어**
```bash
ssh banditX@bandit.labs.overthewire.org -p 2220
```

---

## 🧪 풀이 과정

1. `ls` 명령어로 확인했을 때 아무것도 안 보임
2. `ls -a`로 숨김 파일과 디렉토리 확인
3. `.hidden` 디렉토리 발견 후 진입
4. 내부 파일 확인 후 `cat`으로 출력

```bash
cd .hidden
ls
cat [파일명]
```


---

## 🎯 결과

다음 레벨의 비밀번호:
```
<여기에 비밀번호>
```

---

## 💡 배운 점

- `ls -a`, `cd`, `cat` 활용 및 디렉토리 탐색 능력

---
[← 이전 단계](/2024/05/29/bandit-level2-3.html)
[다음 단계 →](/2024/06/31/bandit-level4-5.html)
