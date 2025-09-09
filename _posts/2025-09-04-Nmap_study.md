---
layout: post
title: "Nmap 공부"
date: 2025-09-04 17:00:00 +0900
categories: [해킹 툴]
---

### 1. Nmap 개요

Nmap은 네트워크 스캐너이다. IP 패킷을 분석해 호스트 정보와 서비스 등을 파악하는 오픈소스 도구이다.

---

### 2. 스캔 타입별 결과 비교

#### **기본 TCP 스캔**
가장 기본적인 스캔. 대상 IP의 열린 포트와 포트가 사용하는 서비스를 보여준다.
```bash
nmap 192.9.200.11
```
**결과**
```
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

#### **-sV (Version Scan)**
기본 스캔에 서비스의 상세 버전을 추가로 확인한다.
```bash
nmap -sV 192.9.200.11
```
**결과**
```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.6p1 Ubuntu 1 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.58 ((Ubuntu))
```
[여기에 비교 이미지 삽입: 기본 스캔 결과와 -sV 스캔 결과를 좌우로 배치하고, -sV 결과에 새로 추가된 VERSION 열 전체에 하이라이트를 적용한 이미지]

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
[여기에 비교 이미지 삽입: 기본 스캔 결과와 -sC 스캔 결과를 좌우로 배치하고 -sC 결과에 새로 추가된 `|_http-server-header` 와 `|_http-title` 두 줄에 하이라이트를 적용한 이미지]

기본 스캔 결과 아래에 스크립트 실행 결과가 추가된다. 웹 서버의 타이틀 같은 구체적인 정보를 얻을 수 있다.

---

### 3. 주요 옵션

*   **-sC -sV**: 두 옵션을 함께 사용하면 효율적으로 많은 정보를 얻을 수 있어 가장 자주 사용된다.
*   **-p-**: 1번부터 65535번까지 모든 포트를 스캔한다. 시간이 오래 걸린다.
*   **-p [Ports]**: 특정 포트만 지정해서 스캔한다. (예: `-p 80, 443`)
*   **-r**: 포트 스캔 순서를 무작위로 섞지 않고 1번부터 순차적으로 진행한다.

<hr class="short-rule">
