# Security EduComp - 웹 취약점 분석과 시큐어코딩

## Nmap 포트스캔 이해 및 서비스 정보 수집

```sh
nmap -sV [ip] -p-
```
-sV: 포트의 서비스 버전까지 확인
-p-: 모든 TCP 포트(1~65535) 스캔

```
https://www.exploit-db.com
```
서비스 버전 정보별 취약점 탐색 확인 사이트

---

## 메타스플로잇 portscan을 이용한 포트 정보 수집

메타스플로잇은 **search -> use -> show** 의 과정을 거침

### search

```
msf > search portscan
```
-공격 모듈 찾아보기

```
msf > info [num]
```
-해당 모듈 넘버로 정보 조회

### use

```
msf > use [num]
```
-해당 모듈 사용

```
msf > show options
```
-옵션을 보는 방법

```
[옵션]
Delay를 거쳐 IPS의 의심을 피함.
PORTS: 1-10000
RHOSTS: Remote Hosts의 주소를 적음
THREADS: 너무 많이 사용할 경우 CPU 과부하, 정확성 떨어짐
```

```
msf > set RHOSTS [ip]
```
-RHOSTS 지정

```
msf > set THREADS 10
```
-Threads 개수 지정

```
msf > exploit
```
-툴 시작

### show

```
msf > services
```
-스캔 내용 확인

---

## FTP 버전정보 및 익명 연결 확인

```
msf > search ftp_version
```
-FTP Version 확인

```
msf > setg RHOSTS [ip]
```
-setg: global한 전역 설정

```
msf > getg RHOSTS
```
-getg: getg로 전역 설정 확인

```
msf > run
```
-메타스플로잇 실행

```
msf > serach ftp/anonymous
```
-ftp/anonymous 검색

```
msf > use [num]
```
-사용할 num 선택

```
msf > exploit
```
-exploit 시작

---

## FTP 무작위 대입 공격 및 와이어샤크 패킷 확인

```
msf > search ftp_login
```
-ftp_login과 관련된 항목 검색

Kali에는 사전 파일이 존재하기 때문에 ID, PASSWD과 관련된 항목 Brute Force 가능.

```
msf > use 0
```
-ftp_login과 관련된 항목은 1개. 즉 use 0

```
msf > set RHOSTS [ip]
```
-대상 IP로 RHOSTS 설정

```
msf > set USER_FILE [dir]
```
-사전 파일 위치 등록(user)

```
msf > set PASS_FILE [dir]
```
-사전 파일 위치 등록(password)

```
msf > set THREADS 5
```
-스레드 개수는 5개 정도가 적절

```
msf > exploit
```
-공격 진행

### Wireshark Statistics

- Statistics - Conversations: Wireshark에서 제공하는 기능 중 하나로, 두 호스트, 두 포트, 또는 두 프로토콜 간의 통신 대화(conversation)를 분석할 때 사용한다.
- Statistics - Endpoints: Wireshark에서 제공하는 기능 중 하나로, 개별 호스트(Endpoint)의 트래픽 통계를 보여준다.

msfconsole을 이용해서 FTP BruteForce 공격을 진행하게 될 시, Wireshark Statistics 기능을 이용해서 조회 가능

```
frame[offset:length] == value
```
Wireshark에서는 패킷 내 특정 위치의 바이트를 직접 비교할 수 있다.

이를 통해 "login successful"이 진행된 위치의 오프셋을 직접 조회할 수도 있음.

---

## vsFTP 2.3.4 발견된 백도어 공격



