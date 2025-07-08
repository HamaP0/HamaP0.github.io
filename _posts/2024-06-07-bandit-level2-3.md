---
layout: post
title: "[Bandit] Level 2 → 3 풀이"
date: 2024-05-29
categories: wargame bandit
tags: [overthewire, bandit, linux]
---

> **목표:** 디렉토리 내부에 있는 특정 이름의 파일에서 비밀번호 찾기

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

1. `ls`로 확인 시, `spaces in this filename`이라는 이름의 파일 발견
2. 공백이 있는 파일 이름은 큰따옴표 또는 역슬래시로 감싸서 접근

```bash
cat "spaces in this filename"
```


---

## 🎯 결과

다음 레벨의 비밀번호:
```
<여기에 비밀번호>
```

---

## 💡 배운 점

- 파일 이름에 공백이 있을 경우 처리하는 법 (`"` 또는 `\` 활용)

---
[← 이전 단계](/2024/05/28/bandit-level1-2.html)
[다음 단계 →](/2024/06/30/bandit-level3-4.html)
