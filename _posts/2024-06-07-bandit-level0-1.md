---
layout: post
title: "[Bandit] Level 0 → 1 풀이"
date: 2024-05-27
categories: wargame bandit
tags: [overthewire, bandit, ssh]
---

> **목표:** SSH 접속을 통해 다음 레벨 비밀번호를 획득한다.

---

## 🔐 Level Info

- **접속 정보**
  - 호스트: `bandit.labs.overthewire.org`
  - 포트: `2220`
  - 사용자: `bandit0`

- **명령어**
```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

---

## 🧪 풀이 과정

1. SSH로 접속
2. 기본 홈 디렉토리에 있는 `readme` 파일 확인
3. `cat readme` 명령어로 플래그 출력

```bash
cat readme
```

---

## 🎯 결과

다음 레벨의 비밀번호:
```
<여기에 비밀번호>
```

---

## 💡 배운 점

- 리눅스 기본 명령어 복습: `ssh`, `cat`
- 첫 레벨은 단순하지만 구조 파악에 중요

---

[다음 단계 →](/2024/05/28/bandit-level1-2.html)
