---
layout: post
title: "[Bandit] Level 3 → 4 풀이"
date: 2025-06-07
categories: bandit
tags: [overthewire, bandit, linux]
---

> 📝 **공식 문제 (Level 3 → 4)**
>
> **Level Goal**
> The password for the next level is stored in a hidden file in the inhere directory.
>
> **Commands you may need to solve this level**
> `ls`, `cd`, `cat`, `file`, `du`, `find`

---

## 🔐 Level Info

- **접속 정보**
  - 사용자: `bandit3`
  - 비밀번호: `MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx`

- **접속 명령어**

  ```bash
ssh bandit3@bandit.labs.overthewire.org -p 2220

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
2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ
```

---

## 💡 배운 점

- `ls -a`, `cd`, `cat` 활용 및 디렉토리 탐색 능력

---
