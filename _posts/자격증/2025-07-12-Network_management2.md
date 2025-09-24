---
layout: post
title: 네트워크 관리사 2급 취득
date: 2025-07-12 15:00:00 +0900
categories: [자격증]
tags: [네트워크 관리사, ICQA, network]
---

한국정보통신자격협회(ICQA) 네트워크 관리사 2급 자격증을 필기와 실기시험을 모두 거쳐 한 번에 취득했습니다. 이 과정에서 TCP/IP, OSI 모델, NOS, 네트워크 운용기기와 같은 핵심 이론은 물론 라우터 설정, 서버 구축, 케이블 제작과 같은 실제 설정 절차를 익히며 네트워크 관리 역량을 쌓았습니다.

---

### 1. 네트워크 기본 개념

#### TCP/IP 프로토콜 스택
- **TCP/IP**: 네트워크 통신을 위한 계층 구조. 4계층 구성.
  - **링크 계층**: 물리적 연결 및 MAC 주소 처리.
  - **인터넷 계층**: IP 주소 기반 라우팅. IPv4/IPv6 지원.
  - **송신 계층**: 데이터 전송 방식 결정. TCP/UDP 사용.
  - **응용 계층**: HTTP, FTP, DNS 등 애플리케이션 서비스 제공.

> **비유**: 우편 시스템에서 각 계층은 발송자, 주소, 배달 방법, 내용물 역할.

#### OSI 7계층 모델
- **물리계층**: 전선, 케이블, 전압 신호.
- **데이터링크계층**: MAC 주소, 프레임 전송.
- **네트워크계층**: IP 주소, 라우팅.
- **전송계층**: TCP/UDP, 오류 회복.
- **세션계층**: 연결 유지.
- **표현계층**: 데이터 포맷 변환.
- **응용계층**: 사용자 인터페이스 제공.

> **비유**: 문서를 전송하는 과정에서 각 계층은 포장, 주소 부착, 운송, 수취 확인 역할.

---

### 2. 라우터 정적 라우팅 설정

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
→ 목적지 `192.168.2.0/24` → 다음 홉 `192.168.1.2`

#### 설정 확인
```bash
R1# show ip route static        # 정적 라우트 확인
R1# ping 192.168.2.10           # 연결성 테스트
```

> **주의**: 다음 홉 IP가 직접 연결된 서브넷에 없으면 라우트 추가 불가  
> **검증 필수**: `show ip route`에서 `S` 표시 확인 → 라우팅 테이블에 정상 등록

---

### 3. Windows Server DNS 서버 설정

#### DNS 역할 설치 (PowerShell)
```powershell
Install-WindowsFeature -Name DNS -IncludeManagementTools
```

#### 정방향 조회 영역 생성
```powershell
Add-DnsServerPrimaryZone -Name "test.com" -ZoneFile "test.com.dns"
```

#### A 레코드 추가
```powershell
Add-DnsServerResourceRecordA -Name "www" -ZoneName "test.com" -IPv4Address "192.168.1.10"
```

#### 동작 확인
```cmd
nslookup www.test.com 127.0.0.1
```
→ 응답: `192.168.1.10`

> **주의**: 클라이언트 PC에서 DNS 서버 IP를 해당 서버로 지정해야 함

---

### 4. Linux(CentOS) Apache 설치 및 방화벽 설정

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

> **주의**: `--permanent` 누락 → 재부팅 시 규칙 사라짐  
> **검증 필수**: 외부 PC에서 `http://서버IP` 접속 성공 여부 확인

---

### 5. 실습을 통해 얻은 기술적 성과

#### DHCP 서버 DNS 설정 오류 해결
- 증상: 클라이언트가 도메인명으로 웹서버 접속 불가
- 진단: DHCP 서버에서 제공한 DNS 서버 IP가 잘못됨
- 해결: DHCP 스코프 옵션 006 수정 → 클라이언트 재연결 → `nslookup` 정상 응답

#### 라우터 명령어 오타 복구
- 증상: `ip route 192.168.2.0 255.255.255.0 192.168.1.2x` → Invalid input
- 진단: 다음 홉 IP에 문자 `x` 포함
- 해결: `no ip route 192.168.2.0 255.255.255.0 192.168.1.2x` → 정상 명령어 재입력

#### UTP 케이블 제작 불량 복구
- 증상: 라우터-스위치 연결 후 링크 라이트 미점등
- 진단: 크로스 케이블 제작 시 핀 1-3, 2-6 미교차
- 해결: 케이블 재제작 → 테스터기로 Green LED 전 핀 점등 확인 → 링크 정상화

---

### 6. 네트워크 관리사 2급 핵심

#### 필수 명령어 및 설정
```bash
# Windows
ipconfig /all                  # IP, DNS, MAC 확인
ping -t 8.8.8.8                # 지속적 연결 테스트
nslookup example.com           # DNS 해석 확인

# Linux
ip addr show                   # IP 주소 확인
systemctl status firewalld     # 방화벽 상태 확인
journalctl -u httpd --since "1 hour ago"  # Apache 로그 조회

# 라우터
show running-config            # 현재 설정 확인
show ip interface brief        # 인터페이스 상태 확인
copy running-config startup-config  # 설정 저장
```

#### 서브넷 계산 핵심 공식
- 네트워크 주소 = IP AND 서브넷마스크
- 브로드캐스트 주소 = IP OR (NOT 서브넷마스크)
- 사용 가능 호스트 수 = 2^(32 - 마스크비트) - 2

#### 주요 포트 번호
- 22: SSH
- 23: Telnet
- 53: DNS (UDP/TCP)
- 80: HTTP
- 443: HTTPS
- 20/21: FTP
- 25: SMTP
- 67/68: DHCP

#### UTP 케이블 핀아웃
- **Straight-through (PC ↔ 스위치)**:
  - 양쪽: 1-White/Orange, 2-Orange, 3-White/Green, 6-Green
- **Crossover (스위치 ↔ 스위치)**:
  - 한쪽: 1-White/Orange, 2-Orange, 3-White/Green, 6-Green
  - 반대쪽: 1-White/Green, 2-Green, 3-White/Orange, 6-Orange

#### 실기시험 대비 체크리스트
- 라우터: 인터페이스 활성화(`no shutdown`), 정적 라우트 정확한 다음 홉
- Windows Server: DHCP 스코프 활성화, DNS 레코드 정확한 IP 매핑
- Linux: 방화벽 서비스 허용, `systemctl enable`으로 부팅 자동 실행
- 케이블: 테스터기로 모든 핀 점등 확인 → Green LED


<hr class="short-rule">
