
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.6p1 Ubuntu 1 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.58 ((Ubuntu))
```
   ![NmapsV](/assets/images/Nmap_1.png)

`VERSION` 항목이 추가되어 정확한 버전 정보가 나타난다. 이 정보는 [A06: 취약하고 오래된 구성 요소](https://hamap0.github.io/projects/owasp-top-10/2025/08/30/A06_Vulnerable-and-Outdated-Components.html) 분석의 핵심 근거가 된다.

#### **-sC (Script Scan)**
기본 스캔에 Nmap의 기본 스크립트를 실행하여 추가 정보를 얻는다.
```bash
nmap -sC 192.9.200.11
```
**결과**
```
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
|_http-server-header: Apache/2.4.58 ((Ubuntu))
|_http-title: DVWA - Damn Vulnerable Web Application
```
   ![NmapsC](/assets/images/Nmap_2.png)

기본 스캔 결과 아래에 스크립트 실행 결과가 추가된다. 웹 서버의 타이틀 같은 구체적인 정보를 얻을 수 있다.

---

### 3. 주요 옵션

*   **-sC -sV**: 두 옵션을 함께 사용하면 효율적으로 많은 정보를 얻을 수 있어 가장 자주 사용된다.
*   **-p-**: 1번부터 65535번까지 모든 포트를 스캔한다. 시간이 오래 걸린다.
*   **-p [Ports]**: 특정 포트만 지정해서 스캔한다. (예: `-p 80, 443`)
*   **-r**: 포트 스캔 순서를 무작위로 섞지 않고 1번부터 순차적으로 진행한다.

<hr class="short-rule">
