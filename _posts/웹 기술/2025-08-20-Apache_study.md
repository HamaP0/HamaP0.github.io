---
layout: post
title: "Apache 공부"
date: 2025-08-20 17:00:00 +0900
categories: [웹 기술]
---

### 1. 개요

Apache HTTP Server는 세계적으로 가장 널리 쓰이는 웹 서버 소프트웨어 중 하나이다. APM 스택에서 'A'를 담당하며 사용자의 HTTP 요청을 받아 HTML · CSS · 이미지 같은 정적 파일을 제공하거나 PHP 스크립트를 실행하여 동적 콘텐츠를 생성하는 역할을 한다.

---

### 2. 주요 디렉터리 및 파일 (Ubuntu 기준)

*   **/etc/apache2/**: Apache의 주 설정 파일들이 위치하는 디렉터리이다.
*   **/etc/apache2/apache2.conf**: 메인 설정 파일. 전역 설정을 포함한다.
*   **/etc/apache2/sites-available/**: 사용 가능한 가상 호스트(웹사이트) 설정 파일들이 저장된다.
*   **/var/www/html/**: 웹 콘텐츠가 위치하는 기본 경로(Document Root)이다. DVWA 같은 웹 애플리케이션 파일은 이곳에 위치한다.
*   **/var/log/apache2/**: 로그 파일이 저장되는 곳. `access.log`는 접속 기록 `error.log`는 오류 기록을 담는다.

---

### 3. 주요 설정 지시어

Apache의 동작은 설정 파일 `.conf` 안의 지시어(Directives)를 통해 제어된다.

#### **DocumentRoot**
웹사이트의 최상위 디렉터리를 지정한다. Apache는 이 경로를 기준으로 사용자가 요청한 파일을 찾는다.
```apache
# /etc/apache2/sites-available/000-default.conf
DocumentRoot /var/www/html
```

#### **Options Indexes**
특정 디렉터리에 `index.html`이나 `index.php` 같은 인덱스 파일이 없을 때 해당 디렉터리의 파일 목록을 보여줄지 여부를 결정한다. 이 기능이 활성화되어 있으면 의도치 않은 파일이 노출될 수 있다.

*   **`Options +Indexes` · `Options Indexes`**: 디렉터리 리스팅 허용
*   **`Options -Indexes`**: 디렉터리 리스팅 차단 (보안 권장 설정)

   ![ApacheList](/assets/images/Apache_1.png)

#### **ServerTokens / ServerSignature**
서버 오류 페이지나 HTTP 응답 헤더에 Apache 버전과 같은 상세 정보를 얼마나 노출할지 결정한다. 보안을 위해 최소한의 정보만 보여주는 것이 좋다.

*   **`ServerTokens Prod`**: 서버 정보를 `Apache`로만 표시한다.
*   **`ServerSignature Off`**: 오류 페이지 하단에 서버 정보 표시를 끈다.

### 4. 보안 관점의 로그 분석

Apache 로그 파일, 특히 접근 로그 **`access.log`** 는 정상적인 서비스 통계뿐만 아니라 외부의 공격 시도를 탐지하고 분석하는 데 매우 중요한 정보를 담고 있다. 공격자가 시스템을 공격할 때 남기는 흔적을 로그에서 식별하는 것은 보안 분석의 기본이다.

#### **1. 로그 파일의 위치 및 기본 형식**
*   **위치:** `/var/log/apache2/` (Ubuntu 기준)
*   **파일:**
    *   **`access.log`**: 모든 웹 요청 기록 (누가 · 언제 · 무엇을 요청했는지)
    *   **`error.log`**: 서버 오류 및 경고 기록
*   **기본 형식 (Common Log Format):**

    `[Client IP] - - [Request Time] "GET /path HTTP/1.1" [Status Code] [Response Size]`

#### **2. 사용 예시: 공격 흔적 탐지**
다른 스터디에서 수행했던 공격들이 `access.log`에 어떻게 기록되는지 `grep` 명령어를 통해 확인할 수 있다.

*   **SQL Injection (Sqlmap) 공격 탐지:**
    **`sqlmap`**과 같은 자동화 도구는 다수의 비정상적인 URL 파라미터를 생성한다. **`UNION`**, **`SELECT`**와 같은 SQL 키워드를 로그 파일에서 검색하여 공격 시도를 식별할 수 있다.
    ```bash
    # /var/log/apache2 디렉터리에서 access.log 파일을 대상으로 'UNION' 문자열 검색
    grep "UNION" /var/log/apache2/access.log
    ```
    ```log
    192.9.200.12 - - [20/Aug/2025:21:15:30 +0900] "GET /dvwa/vulnerabilities/sqli/?id=1%27%20UNION%20ALL%20SELECT%20NULL%2CCONCAT%280x...%29--%20-&Submit=Submit HTTP/1.1" 200 1961 "-" "sqlmap/1.9.8"
    ```
    로그에는 URL 인코딩된 형태(`%20UNION%20SELECT...`)로 공격 페이로드가 기록되어 공격이 발생했다는 명백한 증거가 된다.

*   **Command Injection 공격 탐지:**
    명령어 인젝션 공격에 사용되는 쉘 메타 문자 **`;`** · **`|`** · **`&&`** · **`ls`** · **`whoami`** 같은 명령어 흔적을 검색하여 공격을 탐지할 수 있다.
    ```bash
    # URL 인코딩된 세미콜론(%3B)과 ls 명령어를 함께 검색
    grep "%3B%20ls" /var/log/apache2/access.log
    ```
    ```log
    192.9.200.12 - - [20/Aug/2025:21:40:11 +0900] "GET /dvwa/vulnerabilities/exec/?ip=127.0.0.1%3B%20ls+-l HTTP/1.1" 200 1950 "http://192.9.200.11/dvwa/vulnerabilities/exec/" "Mozilla/5.0..."
    ```

    로그를 통해 공격자가 어떤 IP에서 어떤 명령어를 주입하려고 시도했는지 파악할 수 있다. 이러한 로그는 침해 사고 분석 시 공격자의 행위를 재구성하는 데 결정적인 역할을 한다.

<hr class="short-rule">





### 시각 자료(이미지) 제작을 위한 스크립트

#### **디렉터리 리스팅 비교 이미지 제작**

이 이미지는 `Options Indexes` 지시어의 효과를 시각적으로 보여주는 핵심 자료입니다.

**1. "Before" 이미지 (리스팅 활성화)**

1.  (준비) DVWA의 파일 업로드 기능을 이용해 `/hackable/uploads/` 디렉터리에 아무 파일이나 1~2개 업로드해 둡니다. 이 디렉터리에는 인덱스 파일이 없어 리스팅을 확인하기 좋습니다.
2.  Apache 설정이 기본값(`Options Indexes`)인지 확인합니다.
3.  웹 브라우저에서 `http://192.9.200.11/hackable/uploads/` 주소로 접속합니다.
4.  화면에 "Index of /hackable/uploads/" 라는 제목과 함께 파일 목록이 보일 것입니다. 이 화면을 스크린샷으로 찍어 **`리스팅활성화.png`** 로 저장합니다.

**2. "After" 이미지 (리스팅 비활성화)**

1.  서버 터미널에서 Apache 설정 파일을 엽니다. (예: `sudo nano /etc/apache2/apache2.conf`)
2.  파일 내용 중 `<Directory /var/www/>` 섹션을 찾아 `Options Indexes FollowSymLinks` 부분을 `Options -Indexes FollowSymLinks`로 수정합니다. (`-`를 추가하는 것이 핵심입니다.)
3.  설정 파일을 저장하고 Apache를 재시작합니다. (`sudo systemctl restart apache2`)
4.  이전에 접속했던 브라우저 페이지(`http://192.9.200.11/hackable/uploads/`)를 새로고침합니다.
5.  이제 파일 목록 대신 "403 Forbidden" 오류 페이지가 나타날 것입니다. 이 화면을 스크린샷으로 찍어 **`리스팅비활성화.png`** 로 저장합니다.

**3. 최종 이미지 편집**

1.  이미지 편집 프로그램에서 `리스팅활성화.png`(왼쪽)와 `리스팅비활성화.png`(오른쪽)를 나란히 배치합니다.
2.  각 이미지 위에 "**Before (디렉터리 리스팅 활성화)**"와 "**After (디렉터리 리스팅 비활성화)**" 텍스트를 추가합니다.
3.  **왼쪽 이미지**에서는 파일 목록이 보이는 부분에 하이라이트를 적용하여 정보가 노출되고 있음을 강조합니다.
4.  **오른쪽 이미지**에서는 "403 Forbidden" 오류 메시지에 하이라이트를 적용하여 접근이 성공적으로 차단되었음을 보여줍니다.
5.  수정된 이미지를 저장하여 게시글에 삽입합니다.

    #### **로그 파일 분석 이미지 제작**

##### **1. Sqlmap 공격 로그 이미지:**
1.  Target 서버에서 `sqlmap`을 이용한 SQL Injection 공격을 수행한다.
2.  공격이 끝난 후, Target 서버의 터미널에서 `grep "UNION" /var/log/apache2/access.log` 명령어를 실행한다.
3.  `sqlmap`이 생성한 `UNION SELECT` 구문이 포함된 여러 줄의 로그가 출력될 것이다. 이 터미널 화면을 스크린샷으로 찍는다.
4.  이미지 편집 프로그램을 사용하여 `grep` 명령어와, 결과로 출력된 로그 라인들, 특히 URL 인코딩된 `UNION SELECT` 부분에 하이라이트를 적용하여 저장한다.

##### **2. Command Injection 공격 로그 이미지:**
1.  Target 서버의 DVWA `Command Injection` 페이지에서 `127.0.0.1; ls -l` 과 같은 공격을 수행한다.
2.  Target 서버의 터미널에서 `grep "%3B" /var/log/apache2/access.log` 명령어를 실행한다.
3.  공격에 사용된 페이로드(`...ip=127.0.0.1%3B+ls+-l...`)가 포함된 로그 라인이 출력될 것이다. 이 터미널 화면을 스크린샷으로 찍는다.
4.  이미지에서 `grep` 명령어와 결과로 출력된 로그, 특히 URL 인코딩된 세미콜론(**`%3B`**)과 명령어(**`ls`**) 부분에 하이라이트를 적용하여 저장한다.

---