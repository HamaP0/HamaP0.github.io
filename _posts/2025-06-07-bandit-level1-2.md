---
layout: post
title: "[Bandit] Level 1 → 2 풀이"
date: 2025-06-07
categories: wargame bandit
tags: [overthewire, bandit, ssh]
---

> **목표:** 숨겨진 파일에서 다음 레벨의 비밀번호를 찾는다.

---

## 🔐 Level Info

- **접속 정보**
  - 사용자: `bandit1`
  - 비밀번호: 이전 레벨에서 얻은 값
  - 호스트: `bandit.labs.overthewire.org`
  - 포트: `2220`

- **명령어**
```bash
ssh bandit1@bandit.labs.overthewire.org -p 2220
```

---

## 🧪 풀이 과정

1. 숨겨진 파일 찾기 (`ls -a`)
2. `.hidden` 파일 발견
3. `cat .hidden`으로 내용 확인

```bash
ls -a
cat .hidden
```

---

## 🎯 결과

다음 레벨의 비밀번호:
```
<여기에 비밀번호>
```

---

## 💡 배운 점

- 숨김 파일 확인 시 `-a` 옵션의 중요성
- `.dotfile`은 리눅스에서 매우 자주 등장

---

[← 이전 단계](/2025/06/07/bandit-level0-1.html)
[다음 단계 →](/2025/06/07/bandit-level2-3.html)
