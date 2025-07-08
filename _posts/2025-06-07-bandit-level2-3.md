---
layout: post
title: "[Bandit] Level 2 → 3 풀이"
date: 2025-06-07
categories: bandit
tags: [overthewire, bandit, linux]
---

> 📝 **공식 문제 (Level 2 → 3)**
>
> **Level Goal**
> The password for the next level is stored in a file called spaces in this filename located in the home directory.
>
> **Commands you may need to solve this level**
> `ls`, `cd`, `cat`, `file`, `du`, `find`
>
> **Helpful Reading Material**
> - [Google Search for “spaces in filename”](https://www.google.com/search?q=spaces+in+filename)

---

## 🔐 Level Info

- **접속 정보**
  - 사용자: `bandit2`
  - 비밀번호: `263JGJPfgU6LtdEvgfWU1XP5yac29mFx`
  
- **접속 명령어**

  ```bash
  ssh bandit2@bandit.labs.overthewire.org -p 2220

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
MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx
```

---

## 💡 배운 점

- 파일 이름에 공백이 있을 경우 처리하는 법 (`"` 또는 `\` 활용)

---
