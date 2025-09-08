---
layout: post
title: "John the Ripper 공부"
date: 2025-09-25 17:00:00 +0900
categories: [해킹 툴]
---

### 1. 개요

John the Ripper(JtR)는 오프라인 패스워드 크래킹 도구이다. 시스템에서 탈취한 패스워드 해시 값을 입력받아 사전 파일(Wordlist)을 이용한 대입이나 무차별 대입 공격을 통해 원본 패스워드를 찾아내는 데 사용된다.

[A02: 암호화 실패](https://hamap0.github.io/projects/owasp-top-10/2025/08/26/A02_Cryptographic-Failures.html) 보고서에서 확인했듯이 MD5와 같이 취약한 알고리즘으로 저장된 해시는 JtR과 같은 도구를 통해 쉽게 원본 값을 알아낼 수 있다.

---

### 2. 기본 사용법

JtR의 가장 기본적인 사용법은 사전 파일을 이용한 대입 공격이다.

```bash
john --wordlist=[사전 파일 경로] [해시 파일 경로]
```

*   `--wordlist`: 대입해 볼 단어들이 들어있는 사전 파일을 지정한다. 칼리 리눅스에는 기본적으로 `/usr/share/wordlists/rockyou.txt` 와 같은 대규모 사전 파일이 포함되어 있다.
*   크래킹에 성공한 패스워드는 `john.pot` 파일에 저장되며 `--show` 옵션을 통해 확인할 수 있다.

---

### 3. 사용 예시: MD5 해시 크래킹

`A03: Injection` 보고서에서 `sqlmap`을 통해 탈취한 `admin` 계정의 MD5 해시 `5f4dcc3b5aa765d61d8327deb882cf99` 를 크래킹하는 상황이다.

#### **1. 해시 파일 준비**
크래킹할 해시 값을 `hash.txt` 라는 파일로 저장한다.
```bash
echo "5f4dcc3b5aa765d61d8327deb882cf99" > hash.txt
```

#### **2. 사전 공격 실행**
`rockyou.txt` 사전 파일을 이용하여 `hash.txt` 파일에 대한 크래킹을 수행한다. JtR은 해시의 종류(MD5, SHA1 등)를 자동으로 탐지한다.
```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

#### **3. 결과 확인**
크래킹이 완료되면 `--show` 옵션으로 결과를 확인한다.
```bash
john --show hash.txt
```
[여기에 JtR MD5 크래킹 성공 이미지 삽입]

결과를 보면 `5f4dcc3b5aa7...` 해시의 원본 값이 `password`임을 성공적으로 찾아낸 것을 확인할 수 있다. 이는 취약한 해시 알고리즘이 얼마나 위험한지를 명확히 보여준다.

<hr class="short-rule">





### 시각 자료(이미지) 제작을 위한 스크립트

#### **JtR MD5 크래킹 성공 이미지 제작**

1.  칼리 리눅스 터미널을 엽니다.
2.  **1단계 (파일 준비):** `echo "5f4dcc3b5aa765d61d8327deb882cf99" > hash.txt` 명령어를 실행하여 해시 파일을 생성합니다.
3.  `cat hash.txt` 명령어로 파일 내용이 올바르게 저장되었는지 확인합니다.
4.  **2단계 (공격 실행):** `john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt` 명령어를 실행합니다. 크래킹이 진행되고 완료되는 메시지가 출력됩니다.
5.  **3단계 (결과 확인):** `john --show hash.txt` 명령어를 실행합니다. 아래와 같이 크래킹된 결과가 출력됩니다.
    ```
    ?:password:5f4dcc3b5aa765d61d8327deb882cf99
    
    1 password hash cracked, 0 left
    ```
6.  위 1단계부터 5단계까지의 모든 명령어와 결과가 하나의 터미널 창에 순서대로 모두 표시된 상태에서, 터미널 창 전체를 스크린샷으로 찍습니다.
7.  이미지 편집 프로그램을 사용하여 스크린샷을 엽니다.
8.  두 부분에 하이라이트를 적용하여 원인과 결과를 명확히 보여줍니다.
    *   **입력:** `hash.txt` 파일에 저장된 MD5 해시 값
    *   **결과:** `--show` 옵션으로 출력된 `password:5f4dcc...` 라인 전체, 특히 `password` 부분
9.  수정된 이미지를 저장하여 게시글에 삽입합니다.