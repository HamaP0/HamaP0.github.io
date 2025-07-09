---
layout: post
title: "[Bandit] Level 8 → 9 풀이"
date: 2025-06-14 09:04:00 +0900
categories: bandit
tags: [overthewire, bandit, linux]
---

> 📝 **공식 문제 (Level 8 → 9)**
>
> **Level Goal**
> The password for the next level is stored in the only human-readable file in the inhere directory. Tip: if your terminal is messed up, try the “reset” command.
>
> **Commands you may need to solve this level**
> `ls`, `cd`, `cat`, `file`, `du`, `find`

---

## 🔐 Level Info

- **접속 정보**
  - 사용자: `bandit8`
  - 비밀번호: `dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc`
  
- **접속 명령어**

  ```bash
ssh bandit8@bandit.labs.overthewire.org -p 2220
  ```

## 🧪 풀이 과정

1. 수많은 파일 중에서 조건에 맞는 파일 찾기
2. `file`, `du`, `find` 명령어 활용

```bash
find inhere -type f -size 1033c ! -executable
cat [파일명]
```


---

## 🎯 결과

<details>
<summary>👀 클릭하여 비밀번호 확인하기</summary>

```
4CKMh1JI91bUIZZPXDqGanal4xvAg0JM
```

</details>

---

## 💡 배운 점

- `find`, `file`, `size` 옵션 등 조건 검색 실습

---
