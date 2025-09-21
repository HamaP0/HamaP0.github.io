---
layout: post
title: 네트워크 관리사 2급 취득
date: 2025-07-12 15:00:00 +0900
categories: [자격증]
tags: [네트워크 관리사, ICQA, network]
---

한국정보통신자격협회(ICQA) 네트워크 관리사 2급 자격증을 취득했다. 필기시험은 네트워크 일반, TCP/IP, NOS, 네트워크 운용기기 4과목으로 구성되며, 실기시험은 라우터 설정, 서버 구축, 케이블 제작 능력을 평가한다.

---

## 1. 필기 시험 핵심 요약

### 네트워크 일반 및 OSI 7계층
- **OSI 7계층 모델**: 네트워크 통신 과정을 7개의 논리적 계층으로 구분한 모델.
  - **7계층 (응용)**: 사용자와의 인터페이스 제공. HTTP, FTP, SMTP, DNS, Telnet.
  - **6계층 (표현)**: 데이터의 형식(Format) 정의, 코드 변환, 암호화, 압축 수행.
  - **5계층 (세션)**: 통신 세션의 설정, 유지, 종료 담당.
  - **4계층 (전송)**: 종단 간(End-to-end) 신뢰성 있는 데이터 전송 담당. TCP, UDP 프로토콜 동작, 오류 제어 및 흐름 제어 수행.
  - **3계층 (네트워크)**: IP 주소를 사용해 최적의 경로(Routing) 설정. 패킷의 분할과 병합 수행.
  - **2계층 (데이터링크)**: 물리적 주소(MAC)를 사용해 프레임(Frame) 전송. 흐름 제어, 오류 제어 기능.
  - **1계층 (물리)**: 0과 1의 비트(Bit)를 전기적 신호로 변환하여 전송. 케이블, 허브, 리피터가 이 계층에 해당.

- **계층별 데이터 단위(PDU)**
  - 응용/표현/세션 계층: 데이터 (Data/Message)
  - 전송 계층: 세그먼트 (Segment)
  - 네트워크 계층: 패킷 (Packet)
  - 데이터링크 계층: 프레임 (Frame)
  - 물리 계층: 비트 (Bit)

### TCP/IP
- **TCP/IP 4계층 모델**: OSI 7계층을 4개의 계층으로 단순화한 실용 모델.
  - **4계층 (응용)**: OSI 5, 6, 7계층에 해당.
  - **3계층 (전송)**: OSI 4계층에 해당.
  - **2계층 (인터넷)**: OSI 3계층에 해당.
  - **1계층 (네트워크 인터페이스)**: OSI 1, 2계층에 해당.

- **프로토콜**:
  - **TCP**: 연결형 프로토콜, 3-way-handshaking으로 연결 수립. 신뢰성 있는 데이터 전송 보장.
  - **UDP**: 비연결형 프로토콜, 신뢰성보다 속도가 중요할 때 사용.
  - **ARP**: IP 주소를 MAC 주소로 변환.
  - **ICMP**: IP 통신 중 발생하는 오류 메시지 보고.
  - **IGMP**: 멀티캐스트 그룹 관리를 위한 프로토콜.
  - **SNMP**: 네트워크 장비를 원격으로 관리 및 감시하는 프로토콜.

- **IPv4 주소**: 32비트로 구성. A, B, C, D 클래스로 구분.
- **IPv6 주소**: 128비트로 구성. IPv4의 주소 고갈 문제 해결, 보안 기능 강화.

### NOS (Network Operating System)
- **Windows Server**:
  - **Active Directory**: 사용자, 컴퓨터, 프린터 등 네트워크 리소스를 관리하는 디렉터리 서비스.
  - **DNS 레코드 종류**: A (IPv4 주소 매핑), AAAA (IPv6 주소 매핑), CNAME (별칭), MX (메일 서버).
  - **파일 시스템**: NTFS는 FAT32에 비해 대용량 디스크 지원, 오류 자동 복구, 보안 기능이 강화됨.

- **Linux**:
  - **주요 명령어**:
    - `ps`: 현재 실행 중인 프로세스 상태 확인.
    - `ls`: 디렉터리 내용 출력.
    - `cd ~`: 홈 디렉터리로 이동.
    - `chmod`: 파일 또는 디렉터리의 권한 변경.
    - `ifconfig` / `ip addr`: 네트워크 인터페이스 설정 확인.
  - **로그 파일 위치**: `/var/log` 디렉터리에 시스템의 주요 로그가 저장됨 (`dmesg`, `lastlog` 등).

### 네트워크 운용기기
- **라우터(Router)**: OSI 3계층 장비. IP 주소를 기반으로 패킷의 최적 경로를 결정하여 전송.
- **L2 스위치(Switch)**: OSI 2계층 장비. MAC 주소를 학습하여 특정 포트로만 프레임을 전송.
- **게이트웨이(Gateway)**: OSI 7계층 장비. 프로토콜이 다른 두 네트워크를 연결.
- **방화벽(Firewall)**: 외부의 불법 침입으로부터 내부 네트워크를 보호하는 보안 시스템.
- **RAID**: 여러 개의 디스크를 하나처럼 사용하여 성능 및 안정성을 높이는 기술.
  - **RAID 0 (Striping)**: 데이터를 여러 디스크에 분산 저장하여 속도 향상. 안정성은 낮음.
  - **RAID 1 (Mirroring)**: 데이터를 다른 디스크에 동일하게 복제하여 안정성 확보.

---

## 2. 라우터 정적 라우팅 설정

#### 인터페이스 설정 및 활성화
```bash
Router> enable
Router# configure terminal
Router(config)# hostname R1
R1(config)# interface GigabitEthernet0/0
R1(config-if)# ip address 192.168.1.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit
```

#### 정적 라우트 추가
```bash
R1(config)# ip route 192.168.2.0 255.255.255.0 192.168.1.2
```

#### 설정 확인
```bash
R1# show ip route static
R1# ping 192.168.2.10
```

---

## 3. Windows Server DNS 서버 설정

1.  **서버 관리자** 실행 > **도구** > **DNS**.
2.  좌측 트리에서 서버 이름 마우스 우클릭 > **새 영역**.
3.  **새 영역 마법사** 실행:
    - **영역 종류**: 주 영역.
    - **영역 이름**: `test.com` 입력.
    - **영역 파일**: 기본값 사용 (새 파일 만들기).
    - **동적 업데이트**: 허용 안 함 선택.
4.  생성된 `test.com` 영역 마우스 우클릭 > **새 호스트(A 또는 AAAA)**.
5.  **새 호스트** 창:
    - **이름**: `www` 입력.
    - **IP 주소**: `192.168.1.10` 입력.
    - **호스트 추가** 클릭.

#### 동작 확인 (CMD)
```cmd
nslookup www.test.com 127.0.0.1
```

---

## 4. Linux(CentOS) Apache 설치 및 방화벽 설정

#### Apache 설치 및 실행
```bash
sudo yum install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd
```

#### 방화벽 허용
```bash
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload
```

#### 상태 확인
```bash
sudo systemctl status httpd
sudo firewall-cmd --list-services
curl http://localhost
```

---

## 5. 주요 명령어 및 설정

#### 필수 명령어
```bash
# Windows
ipconfig /all
ping 8.8.8.8
nslookup example.com

# Linux
ip addr show
systemctl status firewalld
chmod 755 [file]

# Router
show running-config
show ip interface brief
copy running-config startup-config
```

#### 주요 포트 번호
- **20/21**: FTP
- **22**: SSH
- **23**: Telnet
- **25**: SMTP
- **53**: DNS
- **80**: HTTP
- **110**: POP3
- **443**: HTTPS

#### UTP 케이블 핀 배열 (T568B 기준)
- **다이렉트 (Straight-through)**: PC-스위치 등 다른 장비 연결. 양쪽 모두 T568B 배열.
- **크로스오버 (Crossover)**: 스위치-스위치 등 동일 장비 연결. 한쪽 T568B, 다른 쪽 T568A 배열.
- 
<hr class="short-rule">
