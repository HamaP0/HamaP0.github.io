---
layout: post
title: "Hydra 공부"
date: 2025-08-28 17:00:00 +0900
categories: [해킹 툴]
---

### 1. 개요

Hydra는 네트워크 로그인 서비스에 무차별 대입 공격(Brute-force Attack)을 수행하는 도구이다.

다양한 프로토콜(SSH, FTP, HTTP 등)을 지원하며, 사용자 이름과 비밀번호 목록(사전 파일)을 이용해 빠르고 병렬적으로 로그인을 시도하여 계정 정보를 알아내는 데 특화되어 있다.

---

### 2. 기본 사용법

Hydra는 여러 옵션을 조합하여 공격을 수행한다.

```bash
hydra -l [사용자 이름] -P [비밀번호 사전 파일] [대상 IP] [프로토콜]
```*   **-l [username]**: 단일 사용자 이름을 지정한다.
*   **-L [userlist]**: 사용자 이름 목록 파일을 지정한다.
*   **-p [password]**: 단일 비밀번호를 지정한다.
*   **-P [passlist]**: 비밀번호 목록(사전) 파일을 지정한다.
*   **-t [tasks]**: 동시에 시도할 스레드 수를 지정하여 속도를 조절한다.

---

### 3. 사용 예시: SSH 무차별 대입 공격

Target 서버(`192.9.200.11`)의 SSH 서비스를 대상으로 `user`라는 계정에 대해 `rockyou.txt` 사전 파일을 이용해 비밀번호를 크랙하는 상황이다.

```bash
hydra -l user -P /usr/share/wordlists/rockyou.txt 192.9.200.11 ssh
```
   ![HydraSsh](/assets/images/Hydra_1.png)

**결과 분석**
Hydra는 `rockyou.txt` 파일의 비밀번호들을 순차적으로 대입하여 로그인을 시도한다. 공격에 성공하면 위 결과와 같이 `[22][ssh] host: 192.9.200.11 login: user password: password123` 형식으로 알아낸 계정 정보를 출력한다.

이 결과는 시스템의 첫 번째 접근 권한을 획득하는 중요한 발판이 된다.

<hr class="short-rule">

### 시각 자료(이미지) 제작을 위한 스크립트

#### **Hydra SSH 공격 성공 이미지 제작**

1.  (준비) Target 서버에 `user`라는 계정을 만들고, 비밀번호를 `rockyou.txt` 사전에 있을 법한 간단한 것(예: `password123`)으로 설정한다.
2.  공격자 PC의 터미널에서 `hydra -l user -P /usr/share/wordlists/rockyou.txt 192.9.200.11 ssh` 명령어를 실행한다.
3.  Hydra가 공격을 수행하고, 성공적으로 비밀번호를 찾은 결과가 터미널에 출력된다.
4.  실행한 명령어와 성공 결과가 모두 보이는 터미널 화면 전체를 스크린샷으로 찍는다.
5.  이미지 편집 프로그램을 사용하여 스크린샷을 연다.
6.  두 부분에 하이라이트를 적용하여 원인과 결과를 명확히 보여준다.
    *   **원인:** 실행한 `hydra` 명령어 전체
    *   **결과:** `password: password123`가 포함된 성공 결과 라인