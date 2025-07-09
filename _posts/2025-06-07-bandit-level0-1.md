---
layout: post
title: "[Bandit] Level 0 → 1 풀이"
date: 2025-06-07 09:01:00 +0900
categories: bandit
tags: [overthewire, bandit, ssh]
---

> 📝 **공식 문제 (Level 0 → 1)**
>
> **Level Goal**
> The password for the next level is stored in a file called readme located in the home directory. Use this password to log into bandit1 using SSH. Whenever you find a password for a level, use SSH (on port 2220) to log into that level and continue the game.
>
> **Commands you may need to solve this level**
> `ls`, `cd`, `cat`, `file`, `du`, `find`

---

## 🔐 Level Info

- **접속 정보**
  - 호스트: `bandit.labs.overthewire.org`
  - 포트: `2220`
  - 사용자: `bandit0`
  - 비밀번호 : `bandit0`

- **접속 명령어**

  ```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
  ```

## 🧪 풀이 과정

1. SSH로 접속
2. 기본 홈 디렉토리에 있는 `readme` 파일 확인
3. `cat readme` 명령어로 플래그 출력

```bash
cat readme
```


---

## 🎯 결과

<details>
<summary>👀 클릭하여 비밀번호 확인하기</summary>

```
ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If
```

</details>

---

## 💡 배운 점

- 리눅스 기본 명령어 복습: `ssh`, `cat`
- 첫 레벨은 가볍게 구조 파악에 집중

---
