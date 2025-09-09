---
layout: post
title: "Wireshark 공부"
date: 2025-09-12 17:00:00 +0900
categories: [해킹 툴]
---

### 1. Wireshark 개요

Wireshark는 네트워크 패킷 분석 도구이다. 네트워크 인터페이스를 오가는 모든 트래픽을 실시간으로 캡처하여 각 패킷의 내용을 상세하게 보여주는 기능을 한다.

Burp Suite가 웹 트래픽(HTTP)이라는 특정 애플리케이션 계층에 집중한다면 Wireshark는 그보다 낮은 계층인 TCP/IP · UDP · ICMP 등 모든 종류의 패킷을 원시(Raw) 형태로 들여다볼 수 있다. 네트워크 통신의 근본 원리를 이해하는 데 필수적인 도구이다.

---

### 2. 기본 인터페이스

Wireshark의 화면은 크게 세 부분으로 나뉜다.
1.  **패킷 목록 (Packet List Pane)**: 캡처된 패킷들의 요약 정보(번호 · 시간 · 출발지/목적지 IP · 프로토콜 등)를 시간 순서대로 보여준다.
2.  **패킷 상세 (Packet Details Pane)**: 목록에서 선택한 패킷의 구조를 프로토콜 계층별로 나누어 상세하게 보여준다. (Ethernet · IP · TCP 등)
3.  **패킷 바이트 (Packet Bytes Pane)**: 선택한 패킷의 실제 데이터 값을 16진수와 ASCII 형태로 보여준다.

---

### 3. 주요 기능

#### **패킷 캡처 (Packet Capture)**
Wireshark를 실행하고 캡처할 네트워크 인터페이스(예: `eth0` 또는 `VMnet8`)를 선택하면 실시간으로 패킷 수집이 시작된다. 빨간색 사각형 아이콘을 누르면 캡처가 중지된다.

#### **디스플레이 필터 (Display Filters)**
수많은 패킷 중에서 원하는 정보만 걸러보는 핵심 기능이다. 상단의 필터 입력창에 조건을 입력하면 해당 조건에 맞는 패킷만 목록에 표시된다.
*   `ip.addr == 192.9.200.11`: 출발지 또는 목적지 IP가 `192.9.200.11`인 패킷
*   `tcp.port == 80`: TCP 포트 80을 사용하는 패킷
*   `icmp`: `ping`과 관련된 ICMP 프로토콜 패킷
*   `http`: HTTP 프로토콜 패킷
*   `http.request.method == "POST"`: HTTP POST 요청만 필터링한다.

#### **Follow TCP Stream**
흩어져 있는 여러 개의 TCP 패킷을 하나의 대화(Stream)로 재조합하여 보여주는 기능이다. 복잡한 패킷 목록 없이 전체 HTTP 요청과 응답 내용을 한눈에 파악할 수 있다.

---

### 4. 사용 예시: Ping 패킷 분석

가장 기본적인 네트워크 통신인 `ping` 명령의 패킷을 분석해 본다.

1.  Wireshark에서 패킷 캡처를 시작한다.
2.  터미널을 열어 `ping -c 1 192.9.200.11` 명령을 실행한다. (`-c 1`은 한 번만 보내는 옵션)
3.  Wireshark에서 캡처를 중지한다.
4.  디스플레이 필터에 `icmp`를 입력한다.

   ![WiresharkIcmp](/assets/images/Wire_1.png)

결과적으로 두 개의 ICMP 패킷이 나타난다. 하나는 내 PC가 Target 서버로 보낸 `Request`(요청)이고 다른 하나는 Target 서버가 응답한 `Reply`(응답)이다. 이 과정을 통해 간단한 `ping` 명령이 실제 네트워크에서는 어떤 패킷 형태로 오고 가는지 직접 확인할 수 있다.

### 5. 사용 예시 2: HTTP 로그인 패킷 분석

암호화되지 않은 HTTP 로그인 요청을 캡처하여 아이디와 비밀번호가 평문으로 전송되는 것을 확인한다.

1.  Wireshark 캡처를 시작하고 DVWA 로그인 페이지에서 로그인한다.
2.  캡처를 중지하고 필터에 `http.request.method == "POST"` 를 입력한다.
3.  필터링된 POST 패킷의 상세 창에서 `HTML Form URL Encoded` 부분을 확장하면 `username`과 `password` 값을 평문으로 확인할 수 있다.

   ![WiresharkHttppost](/assets/images/Wire_2.png)

4.  해당 패킷에서 `Follow TCP Stream` 기능을 사용하면 `username=admin&password=password` 와 같은 전송 데이터를 더 명확하게 확인할 수 있다.

   ![WiresharkFollowtcpstream](/assets/images/Wire_3.png)

이 과정을 통해 암호화되지 않은 HTTP 통신은 중간에서 얼마든지 감청될 수 있다는 것을 명확히 확인할 수 있다.

### 6. 사용 예시 3: HTTPS (TLS) 트래픽 복호화

대부분의 현대 웹 트래픽은 TLS를 통해 암호화된다. Wireshark는 브라우저로부터 암호화 키 로그를 제공받아 이 암호화된 트래픽을 복호화하고 평문 HTTP처럼 분석하는 강력한 기능을 제공한다.

#### **1. 키 로그 파일 생성 (Pre-Master Secret)**
1.  브라우저가 TLS 세션 키를 특정 파일에 저장하도록 환경 변수를 설정한다. 이 파일은 Wireshark가 트래픽을 복호화하는 데 사용된다.
    ```bash
    # 터미널에서 환경 변수 설정
    export SSLKEYLOGFILE=~/Desktop/ssl_key.log
    ```
2.  동일한 터미널에서 웹 브라우저(예: Firefox, Chrome)를 실행한다. 이렇게 실행된 브라우저에서 발생하는 모든 TLS 트래픽의 키가 지정된 파일에 기록된다.
    ```bash
    firefox
    ```

#### **2. Wireshark 복호화 설정**
1.  Wireshark를 실행하고 `Edit > Preferences > Protocols > TLS` 메뉴로 이동한다.
2.  `(Pre)-Master-Secret log filename` 필드 옆의 `Browse...` 버튼을 클릭하여 위에서 생성한 키 로그 파일(`~/Desktop/ssl_key.log`)의 경로를 지정한다.

   ![WiresharkTls](/assets/images/Wire_4.png)

#### **3. 패킷 캡처 및 복호화 확인**
1.  Wireshark에서 패킷 캡처를 시작한다.
2.  키 로깅이 설정된 브라우저에서 HTTPS를 사용하는 사이트(예: DVWA 로그인 페이지)에 접속하고 로그인한다.
3.  캡처를 중지하고 디스플레이 필터에 `http` 를 입력한다.
4.  설정이 올바르게 되었다면 이전에는 `TLSv1.2` 또는 `Application Data`로만 표시되던 패킷들이 `HTTP/1.1` 또는 `HTTP/2` 프로토콜로 식별되며 패킷 상세 창 하단에 `Decrypted TLS` 탭이 추가된 것을 확인할 수 있다.

   ![WiresharkHttps](/assets/images/Wire_5.png)

`Decrypted TLS` 탭을 선택하면 암호화되었던 데이터가 평문(Plaintext)으로 표시되어 `HTTP` 패킷과 동일하게 `username=admin&password=password`와 같은 민감한 정보를 분석할 수 있게 된다.

<hr class="short-rule">
