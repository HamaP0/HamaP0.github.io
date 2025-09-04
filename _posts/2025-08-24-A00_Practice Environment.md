---
layout: post
title: "A00:OWASP Top 10 취약점 분석 및 실습 환경 구축"
date: 2025-08-24 17:00:00 +0900
categories: Projects OWASP-Top-10
---

---

### **프로젝트 개요: OWASP Top 10 취약점 분석 및 실습 환경 구축**

#### **1. 프로젝트 목표**

본 프로젝트는 웹 애플리케이션 보안의 핵심인 **OWASP Top 10**의 각 취약점에 대한 심층적인 이해를 목표로 합니다. 이론적  학습을 넘어, 자체적으로 구축한 안전한 가상 환경 내에서 각 취약점의 공격 시나리오를 직접 재현하고 그 근본 원인을 분석합니다. 궁극적으로는 실질적인 보안 설계 및 대응 역량을 개발하는 것을 목표로 합니다.

---

#### **2. 분석 관점: 4단계 침투 테스트 프로세스**

본 프로젝트는 실제 모의 해킹에서 사용되는 표준 침투 테스트 프로세스를 기반으로 전체적인 관점과 설계를 구성했습니다. 보고서는 OWASP 표준에 맞춰 A01부터 A10까지 순차적으로 작성하여 가독성을 높였으며, 각 취약점은 실제 공격 흐름의 단계별로 이해할 수 있도록 구성했습니다. 이를 통해 개별 취약점이 전체 공격 시나리오 내에서 어떻게 연결되는지 파악하는 데 도움이 될 것입니다.

*   **1단계: 정보 수집 (Reconnaissance)**
    *   *시스템의 버전, 설정 등 외부 정보를 수집하여 공격의 실마리를 찾는 단계입니다.*
    *   → **[[A05] 보안 설정 오류 (Security Misconfiguration)](/projects/owasp-top-10/2025/08/29/A05_Security-Misconfiguration.html)**

*   **2단계: 취약점 분석 (Vulnerability Analysis)**
    *   *수집된 정보를 바탕으로 시스템을 구성하는 요소의 알려진 취약점(CVE)을 식별하는 단계입니다.*
    *   → **[[A06] 취약하고 오래된 구성 요소 (Vulnerable and Outdated Components)](/projects/owasp-top-10/2025/08/30/A06_Vulnerable-and-Outdated-Components.html)**

*   **3단계: 공격 실행 (Exploitation)**
    *   *식별된 취약점을 이용해 데이터를 탈취하거나 시스템 제어권을 획득하는 본격적인 공격 단계입니다.*
    *   → **[[A01] 접근 통제 실패 (Broken Access Control)](/projects/owasp-top-10/2025/08/25/A01_Broken-Access-Control.html)**
    *   → **[[A02] 암호화 실패 (Cryptographic Failures)](/projects/owasp-top-10/2025/08/26/A02_Cryptographic-Failures.html)**
    *   → **[[A03] 인젝션 (Injection)](/projects/owasp-top-10/2025/08/27/A03_Injection.html)**
    *   → **[[A04] 안전하지 않은 설계 (Insecure Design)](/projects/owasp-top-10/2025/08/28/A04_Insecure-Design.html)**
    *   → **[[A07] 식별 및 인증 실패 (Identification and Authentication Failures)](/projects/owasp-top-10/2025/08/31/A07_Identification-and-Authentication-Failures.html)**
    *   → **[[A08] 소프트웨어 및 데이터 무결성 실패 (Software and Data Integrity Failures)](/projects/owasp-top-10/2025/09/01/A08_Software-and-Data-Integrity-Failures.html)**
    *   → **[[A10] 서버 측 요청 위조 (Server-Side Request Forgery)](/projects/owasp-top-10/2025/09/03/A10_Server-Side-Request-Forgery.html)**


*   **4단계: 사후 분석 (Post-Exploitation & Analysis)**
    *   *공격이 시스템에 남긴 흔적을 추적하고, 방어 시스템의 탐지 능력을 평가하는 단계입니다.*
    *   → **[[A09] 보안 로깅 및 모니터링 실패 (Security Logging and Monitoring Failures)](/projects/owasp-top-10/2025/09/02/A09_Security-Logging-and-Monitoring-Failures.html)**

---

#### **3. 실습 환경 구축: 모의 해킹 랩 (Lab)**

안전하고 효과적인 분석 방법론의 수행을 위해 VMware를 기반으로 외부와 완전히 격리된 가상 실습 환경(Lab)을 직접 설계 및 구축했습니다. 이 환경은 실제 서비스에 어떠한 영향을 주지 않으면서 현실적인 공격 및  방어 시나리오를 시뮬레이션할 수 있는 기반을 제공합니다.

**1. 가상 네트워크 구성도**

VMware의 VMnet8(NAT) 기능을 활용하여 외부 인터넷과 분리된 `192.9.200.0/24` 대역의 사설 네트워크를 구성했습니다. 이 네트워크 내에는 공격 대상인 'Target' 서버와 공격을 수행할 'Attacker' 머신이 위치합니다.

   ![가상 네트워크 구성도]({{ "/assets/images/A00_P1-1.png" | relative_url }})

**2. 가상머신 및 IP 할당**

*   **Target (목표 서버):**
    *   **OS:** Ubuntu Server
    *   **IP:** `192.9.200.11`
    *   **Services:** Apache, PHP, MySQL (APM 스택), DVWA
*   **Attacker (공격자):**
    *   **OS:** Kali Linux
    *   **IP:** `192.9.200.12`
    *   **Services:** Burp Suite, Nmap, Nikto, Sqlmap 등

   ![Ubuntu 서버 IP 주소 확인]({{ "/assets/images/A00_P2-1.png" | relative_url }})
   ![Kali Linux IP 주소 확인]({{ "/assets/images/A00_P2-2.png" | relative_url }})

**3. 타겟 애플리케이션 배포**

취약점 분석을 위해 Damn Vulnerable Web Application (DVWA)를 타겟 서버에 직접 배포하고 데이터베이스 연동을 완료하여, 모든 분석 준비를 마쳤습니다.

   ![DVWA 메인 페이지]({{ "/assets/images/A00_P3-1.png" | relative_url }}) <!-- 추천 파일명: DVWA-Main.png -->

**4. 분석 도구 세팅 및 연동 확인**

웹 프록시의 핵심 도구인 Burp Suite를 설정하여 공격자 머신과 타겟 서버 간의 모든 HTTP/HTTPS 통신을 가로채고 분석할 수 있는 환경을 구축했습니다. 또한 Nikto, Sqlmap 등 자동화 도구의 정상 작동을 확인함으로써, 수동 분석과 자동화 분석을 병행할 준비를 완료했습니다.

   ![Burp Suite 패킷 캡처]({{ "/assets/images/A00_P4-1.png" | relative_url }}) <!-- 추천 파일명: Burp-Capture.png -->
   ![Nikto 스캔 결과]({{ "/assets/images/A00_P4-2.png" | relative_url }}) <!-- 추천 파일명: Nikto-Scan.png -->

---

*이어지는 보고서들에서는 위 환경과 방법론을 기반으로 OWASP Top 10의 각 취약점 항목을 분석합니다.*

<hr class="short-rule">

