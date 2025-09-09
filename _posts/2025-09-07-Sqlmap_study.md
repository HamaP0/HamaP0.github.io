---
layout: post
title: "Sqlmap 공부"
date: 2025-09-07 17:00:00 +0900
categories: [해킹 툴]
---

### 1. Sqlmap 개요

Sqlmap은 SQL Injection 취약점을 탐지하고 공격을 자동화하는 오픈소스 도구이다. 명령 줄 인터페이스(CLI) 기반으로 동작하며 데이터베이스 정보를 탈취하거나 서버의 운영체제 명령어까지 실행할 수 있는 강력한 기능을 갖추고 있다.

수동으로 하려면 매우 오래 걸리는 Blind SQL Injection과 같은 공격을 자동화하여 공격 시간을 획기적으로 단축시킨다.

---

### 2. 기본 사용법 및 옵션

#### **기본 스캔**
`-u` 옵션으로 취약점을 점검할 URL을 지정한다. Sqlmap이 테스트할 파라미터를 URL에 포함해야 한다.
```bash
sqlmap -u "http://[Target IP]/vulnerabilities/sqli/?id=1&Submit=Submit#"
```

#### **주요 옵션**
*   **--cookie="[COOKIE]"**: 인증이 필요한 페이지를 점검할 때 사용한다. 브라우저에서 복사한 쿠키 값을 입력한다.
*   **--dbs**: 공격 가능한 데이터베이스의 목록을 보여준다.
*   **-D [DB_NAME] --tables**: 특정 데이터베이스에 포함된 테이블 목록을 보여준다.
*   **-D [DB_NAME] -T [TABLE_NAME] --columns**: 특정 테이블에 포함된 컬럼 목록을 보여준다.
*   **-D [DB_NAME] -T [TABLE_NAME] -C [COLUMN_NAME] --dump**: 특정 컬럼의 데이터를 모두 추출하여 보여준다.
*   **--batch**: 스캔 과정에서 Sqlmap이 묻는 모든 질문에 기본값(Y)으로 자동 응답한다.

---

### 3. 사용 예시

Target 서버 `192.9.200.11`의 DVWA SQL Injection 페이지를 대상으로 데이터베이스 목록을 확인하고 사용자 정보를 탈취했다.

#### **데이터베이스 목록 확인**
```bash
sqlmap -u "http://192.9.200.11/vulnerabilities/sqli/?id=1&Submit=Submit#" --cookie="security=low; PHPSESSID=..." --dbs
```
[여기에 데이터베이스 목록이 출력된 결과 이미지 삽입]

#### **`users` 테이블 데이터 탈취**
위에서 확인한 `dvwa` 데이터베이스의 `users` 테이블에서 `user`, `password` 컬럼의 내용을 탈취한다.
```bash
sqlmap -u "..." --cookie="..." -D dvwa -T users -C user,password --dump
```
[여기에 users 테이블의 내용이 출력된 결과 이미지 삽입]

---

### 4. 파일 시스템 접근 및 OS 명령어 실행

데이터베이스 사용자가 충분한 권한(예: FILE 권한)을 가졌을 때 Sqlmap은 파일 시스템에 접근하거나 운영체제 쉘을 획득하는 기능을 제공한다.

#### **파일 시스템 접근 `--file-read`**
`--file-read` 옵션을 사용하면 서버의 로컬 파일을 직접 읽을 수 있다. 리눅스 시스템의 사용자 정보를 담고 있는 `/etc/passwd` 파일을 읽는 예시이다.
```bash
sqlmap -u "..." --cookie="..." --file-read "/etc/passwd" --batch
```

#### **운영체제 쉘 획득 (`--os-shell`)**
`--os-shell` 옵션은 Sqlmap의 가장 강력한 기능 중 하나로, 성공 시 대상 서버의 운영체제 쉘을 획득하여 직접 명령을 내릴 수 있다. Sqlmap은 이 과정에서 웹 서버의 쓰기 가능한 경로에 명령어 실행을 위한 작은 스크립트(Stager)를 업로드한다.
```bash
sqlmap -u "..." --cookie="..." --os-shell
```
[여기에 --os-shell 획득 이미지 삽입]

`os-shell` 세션에 성공적으로 진입하면 `whoami`, `ls -l` 등 원하는 시스템 명령어를 실행하여 서버를 직접 제어할 수 있다.

<hr class="short-rule">
