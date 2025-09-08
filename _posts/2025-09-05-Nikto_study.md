---
layout: post
title: "Nikto 공부"
date: 2025-09-05 17:00:00 +0900
categories: Study 해킹 툴
---

### 1. Nikto 개요

Nikto는 웹 서버 취약점 스캐너이다. 웹 서버의 설정 오류 · 오래된 버전의 소프트웨어 · 기본값으로 남아있는 파일 등 잠재적인 보안 문제점을 찾아내는 데 특화된 오픈소스 도구이다.

Nmap이 네트워크의 문(포트)이 열려있는지 확인한다면 Nikto는 열린 문으로 들어가 어떤 위험 요소가 있는지 살펴보는 역할을 한다.

---

### 2. 기본 사용법 및 옵션

#### **기본 스캔**
`-h` 또는 `-host` 옵션 뒤에 IP 주소나 도메인을 입력하여 스캔한다.
```bash
nikto -h [Target IP or Domain]
```

#### **주요 옵션**
*   **-p [Port]**: 특정 포트를 지정하여 스캔한다. 기본값은 80 포트이다. (예: `-p 80, 443, 8080`)
*   **-T [Tuning]**: 스캔 유형을 지정한다. 번호가 높을수록 더 많은 항목을 검사하지만 눈에 띄기 쉽다. 예: `0`(파일 업로드) `1`(흥미로운 파일) `2`(설정 오류). `x`는 모든 유형을 포함한다.

---

### 3. 사용 예시

Target 서버 `192.9.200.11`의 80번 포트를 대상으로 기본 스캔을 수행했다.

```bash
nikto -h 192.9.200.11 -p 80
```
[여기에 스캔 결과 하이라이트 이미지 삽입]

**결과 분석**
Nikto 스캔 결과에서 주목할 만한 주요 정보는 다음과 같다.
*   **웹 서버 버전 정보:** `Apache/2.4.58`
*   **주요 보안 헤더 누락:** `X-Frame-Options`, `X-XSS-Protection`
*   **불필요한 기본 파일 및 디렉터리 노출:** `/icons/README`, `/config/`
*   **중요 정보 노출 가능성이 있는 페이지:** `setup.php`

이처럼 Nmap이 알려준 버전 정보 외에도 실제 공격에 직접적인 단서가 될 수 있는 구체적인 취약점들을 다수 발견할 수 있다.

<hr class="short-rule">





### 시각 자료(이미지) 제작을 위한 스크립트

위 게시글의 `[여기에 스캔 결과 하이라이트 이미지 삽입]` 부분에 들어갈 이미지를 만드는 절차입니다.

1.  터미널에 `nikto -h 192.9.200.11 -p 80` 명령을 실행하고 결과가 나오면 터미널 창 전체를 스크린샷으로 찍어 **`Nikto결과.png`** 로 저장합니다.
2.  이미지 편집 프로그램(그림판, 포토샵 등)으로 `Nikto결과.png` 파일을 엽니다.
3.  아래의 각 항목에 해당하는 줄에 **각기 다른 색상**으로 반투명한 사각형 하이라이트를 줍니다. 이렇게 하면 각 취약점의 종류를 시각적으로 구분하기 좋습니다.
    *   **서버 정보 (초록색):**
        `+ Server: Apache/2.4.58 ((Ubuntu))`
    *   **보안 헤더 누락 (파란색):**
        `+ The anti-clickjacking X-Frame-Options header is not present.`
        `+ The X-XSS-Protection header is not defined...`
        `+ The X-Content-Type-Options header is not set...` (이 세 줄을 하나의 큰 박스로 묶어도 좋습니다)
    *   **중요 페이지 노출 (빨간색):**
        `+ OSVDB-3092: /setup.php: This file reveals detailed information...`
    *   **기본 파일/디렉터리 노출 (주황색):**
        `+ OSVDB-3233: /icons/README: Apache default file found.`
        `+ OSVDB-3268: /config/: Directory indexing found.`
4.  하이라이트가 적용된 이미지를 저장하여 게시글에 삽입합니다.