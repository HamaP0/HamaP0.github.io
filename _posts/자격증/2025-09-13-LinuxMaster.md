---
layout: post
title: 리눅스 마스터 2급 취득
date: 2025-09-13 15:00:00 +0900
categories: [자격증]
tags: [linux]
---

리눅스 마스터 2급 자격증을 취득했습니다. 한국정보통신기술협회(KAIT)에서 주관하는 이 자격증 과정을 통해 파일 시스템부터 네트워크 설정까지, 리눅스 운영에 필수적인 핵심 관리 기술을 학습했습니다.

---

### 1. 시험 핵심 요약

#### 리눅스 일반 
- **FHS (Filesystem Hierarchy Standard)**: 리눅스 디렉터리 구조의 표준.
  - `/bin`: 기본 명령어 (모든 사용자가 사용).
  - `/sbin`: 시스템 관리자용 명령어.
  - `/etc`: 시스템 설정 파일.
  - `/var`: 로그, 스풀 등 가변적인 데이터.
  - `/usr`: 사용자가 설치한 프로그램 및 라이브러리.
  - `/home`: 사용자 홈 디렉터리.
  - `/root`: root 사용자의 홈 디렉터리.
  - `/dev`: 장치 파일 (Device Files).
  - `/proc`: 커널 및 프로세스 정보를 담은 가상 파일 시스템.

- **파일 권한**:
  - `rwx` 형태로 표현, 8진수 숫자로도 표기 가능 (`r=4`, `w=2`, `x=1`).
  - `chmod 755 file.sh`: 소유자는 `rwx`(7), 그룹과 다른 사용자는 `r-x`(5) 권한을 부여.
  - `chown user:group file.txt`: 파일의 소유자와 소유 그룹을 변경.

- **특수 권한**:
  - **SetUID (4000)**: 파일 실행 시 소유자의 권한으로 실행.
  - **SetGID (2000)**: 파일 실행 시 그룹의 권한으로 실행. 디렉터리에 설정 시 해당 디렉터리에 생성되는 파일의 그룹이 상위 디렉터리의 그룹으로 지정.
  - **Sticky Bit (1000)**: 디렉터리에 설정. 해당 디렉터리 내에서 파일 소유자와 root만 파일 삭제 가능. `/tmp` 디렉터리에 사용.

- **링크 파일**:
  - **하드 링크**: 원본 파일과 동일한 inode를 공유. 원본이 삭제되어도 데이터는 유지.
  - **심볼릭 링크 (소프트 링크)**: 원본 파일을 가리키는 포인터. 원본이 삭제되면 링크는 무효화.

#### 명령어 및 셸
- **셸(Shell)**: 커널과 사용자 사이의 인터페이스. `bash`, `sh`, `csh`, `tcsh` 등.
  - `echo $SHELL`: 현재 사용 중인 셸 확인.

- **리다이렉션(Redirection)**:
  - `>`: 표준 출력을 파일로 저장 (덮어쓰기).
  - `>>`: 표준 출력을 파일에 추가.
  - `<`: 파일의 내용을 표준 입력으로 사용.
  - `2>`: 표준 에러를 파일로 저장.
  - `&>`: 표준 출력과 표준 에러를 모두 파일로 저장.

- **파이프(Pipe `|`)**: 한 명령어의 실행 결과를 다른 명령어의 입력으로 전달.
  - 예시: `ps -ef | grep httpd`

- **환경 변수**:
  - `PATH`: 실행 파일 검색 경로.
  - `HOME`: 사용자 홈 디렉터리.
  - `PS1`: 셸 프롬프트 형식.
  - `LANG`: 기본 언어 설정.
  - `export`: 환경 변수 설정 및 확인.

#### 프로세스 및 시스템 관리
- **프로세스 상태**:
  - `R` (Running): 실행 중.
  - `S` (Sleeping): 대기 상태.
  - `Z` (Zombie): 종료되었으나 부모 프로세스가 정리하지 않은 상태.

- **프로세스 관리 명령어**:
  - `ps aux`: BSD 계열 옵션, 시스템의 모든 프로세스를 상세하게 출력.
  - `ps -ef`: System V 계열 옵션, 전체 프로세스를 PID, PPID와 함께 출력.
  - `kill -9 [PID]`: 프로세스를 강제 종료 (SIGKILL 신호).
  - `jobs`: 백그라운드로 실행 중인 작업 목록 확인.
  - `fg %[job_number]`: 백그라운드 작업을 포그라운드로 전환.

- **패키지 관리**:
  - **RPM (Red Hat Package Manager)**: `.rpm` 파일을 사용하는 시스템 (CentOS, Fedora).
    - `rpm -ivh package.rpm`: 패키지 설치.
    - `rpm -qa | grep httpd`: `httpd` 패키지 설치 여부 확인.
  - **DPKG (Debian Package)**: `.deb` 파일을 사용하는 시스템 (Ubuntu, Debian).
    - `dpkg -i package.deb`: 패키지 설치.
    - `apt-get install httpd`: 저장소에서 `httpd` 패키지를 설치.

- **vi 편집기 명령어**:
  - **모드**: 명령 모드, 입력 모드, 실행 모드.
  - `:w`: 저장.
  - `:q`: 종료.
  - `:wq`: 저장 후 종료.
  - `:q!`: 강제 종료.

---

### 2. 신규 디스크 추가 및 영구 마운트
1.  **파티션 생성**: `fdisk`로 `/dev/sdb` 디스크에 새 파티션 생성.
    - 실행: `fdisk /dev/sdb`
    - 입력 순서: `n` (new) → `p` (primary) → `1` (partition number) → `Enter` → `Enter` → `w` (write)

2.  **파일 시스템 포맷**: `/dev/sdb1` 파티션을 ext4로 포맷.
    - 실행: `mkfs.ext4 /dev/sdb1`

3.  **마운트 포인트 생성**: 파티션을 연결할 디렉터리 생성.
    - 실행: `mkdir /data`

4.  **임시 마운트 및 확인**: 파티션을 마운트 포인트에 연결 후 `df`로 확인.
    - 실행: `mount /dev/sdb1 /data`
    - 확인: `df -h`

5.  **영구 마운트 등록**: 재부팅 후에도 유지되도록 `/etc/fstab` 파일에 등록.
    - 파일 편집: `vi /etc/fstab`
    - 추가 내용: `/dev/sdb1 /data ext4 defaults 0 0`

### 3. 신규 사용자 생성 및 관리
1.  **옵션을 사용한 사용자 생성**: `useradd` 명령어로 속성 지정.
    - `-d`: 홈 디렉터리 지정.
    - `-s`: 기본 셸 지정.
    - `-g`: 기본 그룹 지정.
    - `-u`: UID 지정.
    - 실행: `useradd -d /home/newuser -s /bin/bash -g users newuser`

2.  **패스워드 설정**: `passwd`로 비밀번호 설정.
    - 실행: `passwd newuser`

3.  **생성 확인**: `/etc/passwd` 파일 확인.
    - 확인: `tail -n 1 /etc/passwd`

### 4. 주요 명령어 및 옵션
- **프로세스 확인 (`ps`)**
  - `aux`: BSD 계열 옵션. CPU, 메모리 사용률 등 상세 정보 출력.
  - `ef`: System V 계열 옵션. PID와 PPID 관계를 트리 구조로 출력.

- **패키지 관리 (`rpm`, `yum`/`dnf`)**
  - `rpm -qilp [package.rpm]`: 패키지 파일(`p`)의 정보(`i`), 포함된 파일 목록(`l`)을 조회(`q`).
  - `yum install [package]`: 패키지 설치.
  - `yum remove [package]`: 패키지 제거.

- **네트워크 상태 (`netstat`, `ss`)**
  - `netstat -anp`: 모든(`a`) 포트 상태를 숫자(`n`)로 표시하고, 사용하는 프로세스(`p`)를 함께 출력.
  - `ss -ltnp`: TCP(`t`) 리스닝(`l`) 소켓을 숫자(`n`)와 프로세스(`p`) 정보와 함께 출력.

- **파일 검색 (`find`)**
  - `find . -name "*.log" -exec rm {} \;`: 현재 디렉터리(` . `)에서 이름이 `.log`로 끝나는 파일을 찾아(`-name`) 삭제(`-exec rm {} \;`) 실행. `{}`는 검색된 파일명을 의미.

- **압축 및 아카이빙 (`tar`)**
  - `cvf`: 파일을 묶음 (Create, Verbose, File).
  - `xvf`: 묶음을 해제 (Extract, Verbose, File).
  - `z`: gzip 압축/해제 옵션 추가 (`tar czf`, `tar xzf`).
  - `j`: bzip2 압축/해제 옵션 추가 (`tar cjf`, `tar xjf`).

#### 시스템 설정 파일
- **/etc/passwd**: 사용자 계정 정보.
- **/etc/shadow**: 사용자 패스워드 및 만료 정보.
- **/etc/group**: 그룹 정보.
- **/etc/fstab**: 부팅 시 마운트할 파일 시스템 정보.
- **/etc/sysconfig/network-scripts/ifcfg-ens33**: 네트워크 인터페이스 설정.
- **/etc/resolv.conf**: DNS 서버 주소 설정.
- **/etc/hosts**: 호스트 이름과 IP 주소 매핑.
- **/etc/inittab** (구형) / **/etc/systemd/system** (신형): 부팅 프로세스 및 서비스 실행 레벨(타겟) 설정.
- **/boot/grub2/grub.cfg**: 부트 로더 설정 파일.

<hr class="short-rule">