---
layout: post
title: "하이브리드 클라우드 보안 아키텍처 구축"
date: 2025-09-26 17:00:00 +0900
categories: [보안 아키텍처]
---

### Azure CLI로 구축한 하이브리드 클라우드 보안 아키텍처

이 프로젝트는 Azure CLI를 사용해 온프레미스와 클라우드를 안전하게 연결하는 하이브리드 네트워크를 구축하는 과정입니다. 실제 기업 환경에서는 단일 클라우드가 아닌 **온프레미스 + 클라우드 혼합** 아키텍처가 일반적입니다. 본 프로젝트에서는 **Site-to-Site VPN**, **BGP 기반 동적 라우팅**, **2-Tier 애플리케이션**, **NSG 정책**을 조합해 네트워크 수준에서부터 보안 경계를 설정하는 방법을 다룹니다.

---

### 1. 전체 아키텍처 개요

  ![아키텍처 다이어그램](/assets/images/Hybrid_1.png)

* ***온프레미스 시뮬레이션 VNet***: `192.168.0.0/16`  
  - Azure 내에서 온프레미스 네트워크를 구축
* ***Azure 클라우드 VNet***: `10.42.0.0/16`  
  - 실제 애플리케이션 배포 영역  
* ***연결 방식***: **Route-based VPN Gateway + BGP**  
  - IPsec 기반 암호화 터널  
  - BGP를 통해 서브넷 경로 자동 학습  
* ***애플리케이션 구조***: **2-Tier**  
  - Web 서버(`10.42.10.0/24`): 외부 요청 처리  
  - DB 서버(`10.42.20.0/24`): 내부 데이터 저장  
* ***보안 정책***: **NSG**(Network Security Group)  
  - Web: On-Prem → 80/443만 허용  
  - DB: Web → 3306만 허용  

> 이 구조는 정보 보안의 기본 원칙인 **최소 권한 원칙(Principle of Least Privilege)**과 **심층 방어(Defense in Depth)**를 네트워크 아키텍처 수준에서 구현합니다.

---

### 2. 환경 구축 절차

#### 2.1 리소스 그룹 생성

```bash
# 온프레미스 시뮬레이션용 리소스 그룹 생성
az group create --name st421-rg-onprem --location koreacentral

# Azure 클라우드용 리소스 그룹 생성
az group create --name st421-rg-azure --location koreacentral
```

  ![On-Prem 리소스 그룹 확인](/assets/images/Hybrid_2.png)
  ![Azure 리소스 그룹 확인](/assets/images/Hybrid_3.png)

> 리소스 그룹은 Azure 리소스를 논리적으로 묶는 단위입니다.  
> 온프레미스와 클라우드를 **분리된 리소스 그룹**으로 관리하면 충돌을 방지하고, 정리가 용이합니다.

---

#### 2.2 가상 네트워크(VNet) 및 서브넷 생성

```bash
# 온프레미스 VNet 생성 (192.168.0.0/16)
az network vnet create \
  --resource-group st421-rg-onprem \
  --name st421-vnet-onprem \
  --address-prefixes 192.168.0.0/16 \
  --subnet-name st421-subnet-client \
  --subnet-prefixes 192.168.10.0/24

# Azure VNet 생성 (10.42.0.0/16)
az network vnet create \
  --resource-group st421-rg-azure \
  --name st421-vnet-azure \
  --address-prefixes 10.42.0.0/16

# Azure 내 서브넷 분리
az network vnet subnet create \
  --resource-group st421-rg-azure \
  --vnet-name st421-vnet-azure \
  --name GatewaySubnet \
  --address-prefixes 10.42.254.0/27

az network vnet subnet create \
  --resource-group st421-rg-azure \
  --vnet-name st421-vnet-azure \
  --name st421-subnet-web \
  --address-prefixes 10.42.10.0/24

az network vnet subnet create \
  --resource-group st421-rg-azure \
  --vnet-name st421-vnet-azure \
  --name st421-subnet-app \
  --address-prefixes 10.42.20.0/24
```
  ![On-Prem Net 주소 공간 확인](/assets/images/Hybrid_4.png)
  ![Azure VNet 및 서브넷 구성 확인](/assets/images/Hybrid_5.png)

> - `GatewaySubnet`은 **이름이 고정**되어야 하며, VPN Gateway 전용입니다.  
> - Web과 App 서브넷을 분리함으로써 **네트워크 격리**를 구현합니다.  
> - CIDR 범위는 **온프레미스와 겹치지 않도록** 설정해야 합니다.

---

#### 2.3 BGP 활성화된 VPN Gateway 생성

```bash
# 온프레미스 측 공인 IP 생성
az network public-ip create \
  --resource-group st421-rg-onprem \
  --name st421-pip-onprem \
  --sku Standard

# 온프레미스 측 VPN Gateway 생성 (ASN: 65501)
az network vnet-gateway create \
  --resource-group st421-rg-onprem \
  --name st421-vng-onprem \
  --public-ip-address st421-pip-onprem \
  --vnet st421-vnet-onprem \
  --gateway-type Vpn \
  --vpn-type RouteBased \
  --sku VpnGw1 \
  --asn 65501

# Azure 측 공인 IP 생성
az network public-ip create \
  --resource-group st421-rg-azure \
  --name st421-pip-azure \
  --sku Standard

# Azure 측 VPN Gateway 생성 (ASN: 65502)
az network vnet-gateway create \
  --resource-group st421-rg-azure \
  --name st421-vng-azure \
  --public-ip-address st421-pip-azure \
  --vnet st421-vnet-azure \
  --gateway-type Vpn \
  --vpn-type RouteBased \
  --sku VpnGw1 \
  --asn 65502
```
 
  ![Azure Azure VPN Gateway 상태 및 ASN 확인](/assets/images/Hybrid_6.png)

> - `--vpn-type RouteBased`는 **BGP 지원 필수 조건**입니다.  
> - `--asn`은 **Autonomous System Number**로, 서로 다른 값을 지정해야 BGP 피어링이 성립합니다.  
> - 이 과정은 약 **30~40분 소요**됩니다.

---

#### 2.4 Local Network Gateway 및 VPN 연결 생성

```bash
# 공인 IP 자동 추출
ONPREM_IP=$(az network public-ip show --resource-group st421-rg-onprem --name st421-pip-onprem --query ipAddress -o tsv)
AZURE_IP=$(az network public-ip show --resource-group st421-rg-azure --name st421-pip-azure --query ipAddress -o tsv)

# Azure 측에서 온프레미스 네트워크 정의
az network local-gateway create \
  --resource-group st421-rg-azure \
  --name st421-lng-onprem \
  --gateway-ip-address $ONPREM_IP \
  --local-address-prefixes 192.168.0.0/16 \
  --asn 65501

# 온프레미스 측에서 Azure 네트워크 정의
az network local-gateway create \
  --resource-group st421-rg-onprem \
  --name st421-lng-azure \
  --gateway-ip-address $AZURE_IP \
  --local-address-prefixes 10.42.0.0/16 \
  --asn 65502

# BGP 활성화된 VPN 연결 생성
az network vpn-connection create \
  --resource-group st421-rg-azure \
  --name st421-conn-azure-to-onprem \
  --vnet-gateway1 st421-vng-azure \
  --local-gateway2 st421-lng-onprem \
  --shared-key " SecureBgpKey-st421-2025" \
  --enable-bgp true
```
 
> - `Local Network Gateway`는 **상대방 네트워크 정보**를 Azure에 알려주는 객체입니다.  
> - `--enable-bgp true`로 설정해야 **동적 라우팅**이 활성화됩니다.  
> - 공유 키(`shared-key`)는 양쪽에서 **동일하게 설정**해야 연결이 성립합니다.

---

#### 2.5 NSG 정책 적용

```bash
# Web 서버용 NSG 생성
az network nsg create --resource-group st421-rg-azure --name web-nsg

# On-Prem(192.168.0.0/16)에서만 80/443 허용
az network nsg rule create \
  --resource-group st421-rg-azure \
  --nsg-name web-nsg \
  --name Allow-HTTP-From-OnPrem \
  --priority 100 \
  --source-address-prefixes 192.168.0.0/16 \
  --destination-port-ranges 80 443 \
  --access Allow \
  --protocol Tcp
  ![Web 티어 NSG 규칙 확인](/assets/images/Hybrid_8.png)

# App 서버용 NSG 생성
az network nsg create --resource-group st421-rg-azure --name app-nsg

# Web 서브넷(10.42.10.0/24)에서만 3306 허용
az network nsg rule create \
  --resource-group st421-rg-azure \
  --nsg-name app-nsg \
  --name Allow-MySQL-From-Web \
  --priority 100 \
  --source-address-prefixes 10.42.10.0/24 \
  --destination-port-ranges 3306 \
  --access Allow \
  --protocol Tcp
```
  ![App 티어 NSG 규칙 확인](/assets/images/Hybrid_9.png)

> - NSG는 **L4 가상 방화벽**으로, 서브넷 또는 VM 단위로 트래픽을 제어합니다.  
> - **기본 정책은 모든 인바운드 차단**이므로, **명시적 허용 규칙만이 통신을 가능하게** 합니다.  
> - 이는 **최소 권한 원칙**을 구현하는 핵심입니다.

---

#### 2.6 cloud-init 기반 VM 배포

```bash
# Web 서버 cloud-init 스크립트 생성
cat > web-init.yml << 'EOF'
#cloud-config
package_upgrade: true
packages:
  - httpd
runcmd:
  - systemctl enable --now httpd
  - echo "<h1>Welcome to st421 Hybrid Cloud Web!</h1>" > /var/www/html/index.html
EOF

# DB 서버 cloud-init 스크립트 생성
cat > db-init.yml << 'EOF'
#cloud-config
package_upgrade: true
packages:
  - mysql-server
runcmd:
  - systemctl enable --now mysqld
  - mysql -e "ALTER USER 'root'@'localhost' IDENTIFIED BY 'StrongPass123!';"
  - mysql -e "CREATE DATABASE hybriddb;"
  - mysql -e "CREATE USER 'webuser'@'%' IDENTIFIED BY 'WebPass123!';"
  - mysql -e "GRANT ALL PRIVILEGES ON hybriddb.* TO 'webuser'@'%';"
  - mysql -e "FLUSH PRIVILEGES;"
EOF

# VM 배포
az vm create \
  --resource-group st421-rg-onprem \
  --name st421-vm-onprem \
  --image RedHat:RHEL:9-lvm-gen2:latest \
  --vnet-name st421-vnet-onprem \
  --subnet st421-subnet-client \
  --admin-username azureuser \
  --generate-ssh-keys \
  --public-ip-address "" \
  --size Standard_B2s

az vm create \
  --resource-group st421-rg-azure \
  --name st421-vm-web \
  --image RedHat:RHEL:9-lvm-gen2:latest \
  --vnet-name st421-vnet-azure \
  --subnet st421-subnet-web \
  --nsg web-nsg \
  --admin-username azureuser \
  --ssh-key-values ~/.ssh/id_rsa.pub \
  --custom-data @web-init.yml \
  --public-ip-address "" \
  --size Standard_B2s

az vm create \
  --resource-group st421-rg-azure \
  --name st421-vm-db \
  --image RedHat:RHEL:9-lvm-gen2:latest \
  --vnet-name st421-vnet-azure \
  --subnet st421-subnet-app \
  --nsg app-nsg \
  --admin-username azureuser \
  --ssh-key-values ~/.ssh/id_rsa.pub \
  --custom-data @db-init.yml \
  --public-ip-address "" \
  --size Standard_B2s
```
 
> - `cloud-init`은 **VM 최초 부팅 시 자동 설정**을 적용하는 표준 도구입니다.  
> - `--public-ip-address ""`로 설정해 **외부에서 직접 접근 불가**하도록 보안 강화합니다.  
> - NSG는 VM 생성 시 **자동 연결**됩니다.

---

### 3. 검증 결과

구축한 아키텍처가 의도대로 동작하는지 검증을 진행합니다

  ![Web/DB 서버의 Private IP 주소 확인]](/assets/images/Hybrid_10.png)

검증에 사용할 VM들의 내부 IP 주소를 먼저 확인합니다.

#### 3.1 On-Prem → Web 서버 접속

온프레미스 클라이언트 VM에서 Azure의 Web 서버로 HTTP 요청을 보내 정상적으로 응답을 받는지 확인합니다. 이는 Web 티어 NSG 정책(`Allow-HTTP-From-OnPrem`)에 의해 허용된 통신입니다.

```bash
# On-Prem VM에서 실행
curl http://10.42.10.4
```
  ![On-Prem VM -> Web 서버 HTTP 접속 테스트]](/assets/images/Hybrid_11.png)

> Welcome to st421 Hybrid Cloud Web! 메시지 수신을 통해, VPN과 BGP 라우팅이 정상적으로 동작하며 NSG 인바운드 규칙이 올바르게 적용되었음을 확인했습니다.

#### 3.2 On-Prem → DB 직접 접속 차단 

보안 정책이 제대로 동작하는지 검증하기 위해 의도적으로 규칙을 위반하는 통신을 시도합니다. 온프레미스 클라이언트 VM에서 App 티어의 DB 서버로 직접 접속을 시도합니다.

  ![On-Prem VM -> DB 서버 MySQL 접속 테스트]](/assets/images/Hybrid_12.png)

ERROR 2003 (HY000): Can't connect to MySQL server... 오류가 발생했습니다. 이는 App 티어 NSG 정책이 허용되지 않은 소스(On-Prem)로부터의 직접적인 접근을 차단했음을 의미합니다. 이것이 **"최소 권한 원칙"**이 적용된 결과입니다. 

#### 3.3 BGP 동적 라우팅 상태 검증

하이브리드 연결의 핵심인 BGP(Border Gateway Protocol) 동적 라우팅이 정상적으로 수립되었는지 확인하는 것은 중요합니다. BGP 피어링이 성공했다는 것은 양쪽 네트워크가 서로의 경로를 **자동으로 학습하고 있음**을 의미합니다.

***CLI를 통한 검증***

`az network vnet-gateway list-bgp-peer-status` 명령어를 사용하면 코드 기반으로 BGP 피어의 상태를 정확히 확인할 수 있습니다.

```bash
# Azure 측 Gateway에서 BGP 피어 상태를 확인
az network vnet-gateway list-bgp-peer-status -g st421-rg-azure -n st421-vng-azure -o table
```
  ![BGP 피어링 상태 확인](/assets/images/Hybrid_7.png)

> - **State = `Connected`**: BGP 피어링 세션이 성공적으로 수립되었음을 의미합니다.
> - **RoutesReceived = `1`**: Azure Gateway가 상대방인 On-Prem Gateway로부터 **온프레미스 네트워크(`192.168.0.0/16`)의 경로를 성공적으로 학습**했음을 나타냅니다.

이와 동일한 정보는 **Azure Portal의 `VPN Gateway` → `BGP 피어` 메뉴**에서도 시각적으로 확인할 수 있습니다.

BGP의 가장 큰 장점은 바로 이 **동적 경로 학습** 능력입니다. 이제 온프레미스나 클라우드에 새로운 서브넷이 추가되어도 수동으로 라우팅 테이블을 수정할 필요 없이 BGP가 알아서 경로를 교환해줍니다. 이는 네트워크의 **확장성**과 **장애 복구 능력**을 크게 향상시키는 핵심 기능입니다.

---

### 4. 마무리

이 프로젝트는 BGP 기반 동적 라우팅과 NSG 정책을 조합하여 **실제 기업 환경과 유사한 수준의 보안과 확장성을 갖춘** 하이브리드 클라우드 연결을 성공적으로 구축한 사례입니다.