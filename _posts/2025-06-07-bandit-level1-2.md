---
layout: post
title: "[Bandit] Level 1 → 2 풀이"
date: 2025-06-07 09:02:00 +0900
categories: bandit
tags: [overthewire, bandit, ssh]
---

> 📝 **공식 문제 (Level 1 → 2)**
>
> **Level Goal**
> The password for the next level is stored in a file called - located in the home directory.
>
> **Commands you may need to solve this level**
> `ls`, `cd`, `cat`, `file`, `du`, `find`
>
> **Helpful Reading Material**
> - [Google Search for “dashed filename”](https://www.google.com/search?q=dashed+filename)
> - [Advanced Bash-scripting Guide - Chapter 3 - Special Characters](https://tldp.org/LDP/abs/html/special-chars.html)

---

## 🔐 Level Info

- **접속 정보**
  - 사용자: `bandit1`
  - 비밀번호: `ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If`

- **접속 명령어**

  ```bash
ssh bandit1@bandit.labs.overthewire.org -p 2220
  ```

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

<details markdown="1">
<summary>👀 클릭하여 비밀번호 확인하기</summary>

```
263JGJPfgU6LtdEvgfWU1XP5yac29mFx
```

</details>

---

## 💡 배운 점

- 숨김 파일 확인 시 `-a` 옵션의 중요성
- `.dotfile`은 리눅스에서 매우 자주 등장

---
