---
layout: post
title: "[Bandit] Level 7 → 8 풀이"
date: 2025-06-14 09:03:00 +0900
categories: bandit
tags: [overthewire, bandit, linux]
---

> 📝 **공식 문제 (Level 7 → 8)**
>
> **Level Goal**
> The password for the next level is stored in the only human-readable file in the inhere directory. Tip: if your terminal is messed up, try the “reset” command.
>
> **Commands you may need to solve this level**
> `ls`, `cd`, `cat`, `file`, `du`, `find`

---

## 🔐 Level Info

- **접속 정보**
  - 사용자: `bandit7`
  - 비밀번호: `morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj`
  
- **접속 명령어**

  ```bash
ssh bandit7@bandit.labs.overthewire.org -p 2220
  ```

## 🧪 풀이 과정

1. 수많은 파일 중에서 조건에 맞는 파일 찾기
2. `file`, `du`, `find` 명령어 활용

```bash
find inhere -type f -size 1033c ! -executable
```bash
cat [파일명]
```


---

## 🎯 결과

<details markdown="1">
<summary>👀 클릭하여 비밀번호 확인하기</summary>

```
dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc
```

</details>

---

## 💡 배운 점

- `find`, `file`, `size` 옵션 등 조건 검색 실습

---
