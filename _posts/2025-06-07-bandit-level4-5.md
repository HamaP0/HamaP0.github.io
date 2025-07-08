---
layout: post
title: "[Bandit] Level 4 → 5 풀이"
date: 2025-06-07
categories: bandit
tags: [overthewire, bandit, linux]
---

> 📝 **공식 문제 (Level 4 → 5)**
>
> **Level Goal**
> The password for the next level is stored in the only human-readable file in the inhere directory. Tip: if your terminal is messed up, try the “reset” command.
>
> **Commands you may need to solve this level**
> `ls`, `cd`, `cat`, `file`, `du`, `find`

---

## 🔐 Level Info

- **접속 정보**
  - 사용자: `bandit4`
  - 비밀번호: `2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ`
  
- **접속 명령어**

  ```bash
  ssh bandit4@bandit.labs.overthewire.org -p 2220

## 🧪 풀이 과정

1. 수많은 파일 중에서 조건에 맞는 파일 찾기
2. `file`, `du`, `find` 명령어 활용

```bash
find inhere -type f -size 1033c ! -executable
cat [파일명]
```


---

## 🎯 결과

다음 레벨의 비밀번호:
```
4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw
```

---

## 💡 배운 점

- `find`, `file`, `size` 옵션 등 조건 검색 실습

---
