---
layout: post
title: "Nmap Scripting Engine (NSE) 공부"
date: 2025-09-16 17:00:00 +0900
categories: [해킹 툴]
---

### 1. 개요

이전 [Nmap 공부](https://hamap0.github.io/study/해킹툴/2025/09/04/Nmap_study.html) 게시글에서 다룬 `-sC` 옵션은 Nmap의 핵심 기능인 Nmap Scripting Engine(NSE)의 일부이다. NSE는 Nmap 스캔 과정에서 특정 작업을 자동화하는 Lua 스크립트를 실행하는 기능이다. 단순히 포트와 버전 정보를 확인하는 것을 넘어 특정 취약점을 탐지하거나 더 상세한 정보를 수집하는 등 Nmap의 활용도를 크게 확장시킨다.

---

### 2. `-sC` 옵션의 실제 의미

`-sC` 옵션은 `--script=default`와 동일한 명령어이다. 즉, Nmap에 내장된 스크립트 중 `default` 카테고리로 분류된 안전하고 기본적인 스크립트들을 실행한다. 이 스크립트들은 주로 대상 서버에 intrusive(공격적)하지 않은 방법으로 추가 정보를 얻는 데 사용된다.
*   `http-title`: 웹 서버의 `title` 태그를 가져온다.
*   `ftp-anon`: 익명 FTP 로그인을 시도해본다.

---

### 3. 주요 스크립트 카테고리

`default` 외에도 다양한 목적을 가진 스크립트 카테고리가 존재한다. `--script` 옵션 뒤에 카테고리 이름을 지정하여 사용할 수 있다.

*   **vuln**: 알려진 취약점(CVE 등)을 탐지하는 스크립트 모음이다. 단순 정보 수집을 넘어 능동적인 취약점 분석 단계로 넘어가는 핵심적인 옵션이다.
*   **auth**: 인증과 관련된 정보를 수집하거나 기본적인 계정 정보(admin/admin)로 로그인을 시도하는 등 인증 관련 취약점을 점검한다.
*   **discovery**: 호스트에 대한 더 많은 정보를 수집한다. DNS 정보, SNMP 정보 등을 수집하여 네트워크를 파악하는 데 도움을 준다.
*   **brute**: 무차별 대입 공격을 통해 서비스의 로그인 정보를 알아내는 스크립트 모음이다. 실제 공격에 해당하므로 사용에 주의가 필요하다.

---

### 4. 사용 예시: `vuln` 스크립트를 이용한 취약점 스캔

`-sV`로 버전 정보를 파악한 뒤, 해당 버전에 알려진 취약점이 있는지 `vuln` 스크립트를 이용해 점검한다.

```bash
nmap -sV --script=vuln 192.9.200.11
```
   ![Nmap2vuln](/assets/images/Nmap2_1.png)

이 명령을 실행하면 Nmap은 스캔된 서비스(예: Apache 2.4.58) 버전에 해당하는 `vuln` 카테고리의 스크립트들을 찾아 실행한다. 만약 해당 버전에 알려진 취약점이 있고 Nmap 스크립트가 이를 지원한다면 위 결과처럼 관련 CVE 번호와 함께 취약점의 상태(VULNERABLE)가 출력된다.

이 정보는 [A06: 취약하고 오래된 구성 요소](https://hamap0.github.io/projects/owasp-top-10/2025/08/30/A06_Vulnerable-and-Outdated-Components.html) 분석의 직접적인 증거가 된다.

<hr class="short-rule">
