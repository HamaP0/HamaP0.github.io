---
layout: post
title: "Burp Suite 공부"
date: 2025-09-06 17:00:00 +0900
categories: Study 해킹 툴
---

### 1. Burp Suite 개요

Burp Suite는 웹 애플리케이션 보안 테스트를 위한 통합 플랫폼이다. 핵심 기능은 웹 브라우저와 웹 서버 사이의 중간자(Man-in-the-Middle) 역할을 하는 프록시 서버이다.

이 프록시를 통해 사용자가 주고받는 모든 HTTP/HTTPS 요청과 응답을 가로채고 분석하거나 변조할 수 있다. 웹 취약점 분석의 필수 도구로 여겨진다.

---

### 2. 기본 설정

Burp Suite를 사용하려면 먼저 브라우저의 트래픽이 Burp Suite를 거치도록 설정해야 한다.

1.  **Burp Suite 프록시 활성화:** Burp Suite를 실행하고 `Proxy` 탭의 `Intercept` 탭에서 `Intercept is on` 버튼을 활성화한다. 기본적으로 프록시 서버는 `127.0.0.1:8080`에서 동작한다.

2.  **브라우저 프록시 설정:** 웹 브라우저가 `127.0.0.1:8080`으로 트래픽을 보내도록 설정한다. Firefox의 `FoxyProxy`와 같은 확장 프로그램을 사용하면 프록시 설정을 쉽게 켜고 끌 수 있어 편리하다.

3.  **CA 인증서 설치:** HTTPS 트래픽을 분석하려면 Burp Suite의 CA 인증서를 브라우저에 설치해야 한다. Burp Suite가 켜진 상태에서 브라우저로 `http://burpsuite`에 접속하여 인증서를 다운로드하고 브라우저 설정에서 신뢰하는 인증 기관으로 가져온다.

---

### 3. 핵심 기능

#### **Proxy: 트래픽 가로채기**
`Proxy` 탭은 모든 트래픽을 확인하고 제어하는 관문이다. `Intercept` 기능이 활성화된 상태에서 웹 페이지에 접속하면 요청이 서버로 전송되기 전에 Burp Suite에 잡힌다. 여기서 `Forward` 버튼을 눌러 요청을 그대로 보내거나 `Drop` 버튼으로 요청을 버릴 수 있다. 또는 요청 내용을 수정한 뒤 보낼 수도 있다.

[여기에 Intercept된 로그인 요청 패킷 이미지 삽입]

#### **Repeater: 요청 재전송 및 수정**
`Repeater` 탭은 가로챈 요청을 수동으로 여러 번 보내볼 수 있는 기능이다. 특정 파라미터 값을 바꿔가며 서버가 어떻게 다르게 반응하는지 확인할 때 매우 유용하다.

예를 들어 [SQL Injection](https://hamap0.github.io/projects/owasp-top-10/2025/08/27/A03_Injection.html)이나 [Broken Access Control](https://hamap0.github.io/projects/owasp-top-10/2025/08/25/A01_Broken-Access-Control.html) 취약점을 테스트할 때 ID 값이나 다른 파라미터를 변경하며 서버의 응답 변화를 관찰할 수 있다.

[여기에 Repeater에서 파라미터 값을 수정한 뒤 응답을 받은 이미지 삽입]

#### **Intruder: 공격 자동화**
`Intruder` 탭은 요청의 특정 부분을 자동화된 방식으로 바꾸어 대량으로 보내는 기능이다. 주로 무차별 대입 공격(Brute-force)이나 특정 파라미터에 대한 값 추측 공격에 사용된다.

1.  공격할 요청을 `Intruder` 탭으로 보낸다.
2.  `Positions` 탭에서 공격할 위치(파라미터)를 `§` 기호로 지정한다.
3.  `Payloads` 탭에서 주입할 값 목록(예: 비밀번호 사전 파일)을 설정한다.
4.  공격을 시작하면 각 페이로드에 대한 서버의 응답 코드나 길이(Length)를 비교하여 유의미한 차이를 찾아낼 수 있다.

[여기에 Intruder의 Positions 탭 설정 및 결과 테이블 이미지 삽입]

<hr class="short-rule">





### 시각 자료(이미지) 제작을 위한 스크립트

#### **이미지 1 제작 (Proxy Intercept)**

1.  Burp Suite와 브라우저 프록시 설정을 마칩니다.
2.  DVWA 로그인 페이지로 이동합니다.
3.  Burp Suite의 `Proxy` > `Intercept` 탭에서 `Intercept is on` 상태로 둡니다.
4.  DVWA 로그인 페이지에서 아무 아이디/비밀번호나 입력하고 `Login` 버튼을 누릅니다.
5.  Burp Suite에 잡힌 `POST /DVWA/login.php` 요청 화면을 스크린샷으로 찍습니다.
6.  이미지에서 `username=admin&password=password`와 같은 파라미터 부분에 하이라이트를 적용하여 저장합니다.

#### **이미지 2 제작 (Repeater)**

1.  위에서 잡은 로그인 요청을 마우스 오른쪽 클릭하여 `Send to Repeater`를 선택합니다.
2.  `Repeater` 탭으로 이동합니다.
3.  왼쪽 요청(Request) 패널에서 `password=password` 값을 `password=wrongpassword` 와 같이 다른 값으로 수정합니다.
4.  `Send` 버튼을 눌러 요청을 보냅니다.
5.  오른쪽 응답(Response) 패널에 `Login failed` 메시지가 포함된 HTML이 나타납니다.
6.  요청 패널의 수정된 비밀번호 부분과, 응답 패널의 `Login failed` 부분에 각각 하이라이트를 적용하여 전체 화면을 스크린샷으로 찍고 저장합니다.

#### **이미지 3 제작 (Intruder)**

1.  로그인 요청을 마우스 오른쪽 클릭하여 `Send to Intruder`를 선택합니다.
2.  `Intruder` > `Positions` 탭으로 이동합니다.
3.  오른쪽의 `Clear §` 버튼을 눌러 자동 지정된 위치를 모두 지웁니다.
4.  `password=` 뒤의 값(예: `password`)만 드래그하여 선택하고, `Add §` 버튼을 누릅니다.
5.  `Payloads` 탭으로 이동하여 `Payload type`을 `Simple list`로 두고, 아래 `Payload Options`에 `password`, `123456`, `letmein` 등 몇 가지 추측값을 입력합니다.
6.  `Start attack` 버튼을 누릅니다.
7.  새로운 공격 결과 창이 나타나면, 다른 응답과 `Length` 값이 다른 성공적인 응답(예: `letmein`) 줄에 하이라이트를 적용하여 스크린샷으로 찍고 저장합니다.