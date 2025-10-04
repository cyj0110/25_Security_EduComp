
## 웹 프록시 서버의 역할
```
개인 PC(브라우저) ---> 요청(Request) ---> 웹 프록시 서버 (캐시 서버) ---------------> 서버(인터넷)
                 <--- 응답(Response)                             <- 응답(Response)
```

---

## BurpSuite 도구
```
```

---

## 칼리 리눅스 명령어 정리 - 사용자 정보와 디렉터리 확인

### 1) id

- 현재 사용자(혹은 지정한 사용자)의 UID, GID, 소속 그룹들을 보여줌.

- **사용법**: id 또는 id username

- **예시 출력**
```bash
$ id
uid=1000(kali) gid=1000(kali) groups=1000(kali),4(adm),27(sudo)
```
- **의미**: uid=사용자ID(사용자이름), gid=기본그룹, groups=소속 그룹 목록

---

### 2) pwd

- 현재 작업 중인 절대 경로(현재 디렉토리) 출력

- **사용법**: pwd

- **예시**
```bash
$ pwd
/home/kali
```

---

### 3) ls

- 디렉토리의 파일/폴더 목록을 나열

- **자주 쓰는 옵션**
```
ls : 기본 목록
ls -a : 숨김파일(.으로 시작) 포함
ls -l : 상세(long) 형식 (권한, 소유자, 크기, 수정시간 등)
ls -al 또는 ls -la : 숨김파일 포함 + 상세 출력
```

---

### 4) ls -al시 나오는 권한

- **예시 한 줄:**
```bash
-rw-r--r--  1 kali users  4096 Sep 29 10:00 example.txt
```
```
각 부분 설명(왼쪽부터):

-rw-r--r-- — 파일 타입 + 권한 비트
첫 문자: - 파일, d 디렉토리, l 심볼릭 링크 등
다음 9문자: 소유자(owner) / 그룹(group) / 기타(other) 권한, 각각 r(읽기), w(쓰기), x(실행)
예: rw- (소유자 읽기+쓰기), r-- (그룹 읽기만), r-- (그 외 읽기만)
1 — 하드링크 수(디렉토리는 항목 수 등)
kali — 소유자 (owner)
users — 그룹 (group)
4096 — 파일 크기(바이트)
Sep 29 10:00 — 마지막 수정 시각
example.txt — 파일 이름

권한을 숫자로 표시하면: rwxrwxrwx → 각각 4(r)+2(w)+1(x) 해서 owner/group/other 세 자리로 755, 644 등.

예: -rw-r--r-- = owner rw-(6), group r--(4), other r--(4) → 644
```

---

### 5) /home 디렉토리란?

- 시스템 사용자들의 홈 디렉토리들이 모여 있는 기본 위치

- **흔한 구조**:
```
/home/
  ├─ alice/    (alice의 홈)
  ├─ bob/      (bob의 홈)
  └─ kali/
```
- 각 사용자는 자신의 홈(/home/username)에 개인 설정 파일(.bashrc, .profile 등)과 데이터 파일을 둠.

- 루트 사용자 홈은 보통 /root 임.

---

### 6) sudo su -

- 현재 사용자가 root(관리자) 권한의 로그인 쉘로 전환

- **동작**
```
sudo : 명령을 다른 사용자(기본 root) 권한으로 실행
su - : 해당 사용자(root)의 로그인 환경(환경 변수, 홈 디렉토리 등)을 로드한 쉘 실행
합치면 sudo su -는 root로 완전하게 로그인하는 것과 동일
```
- **사용 예**
```bash
$ sudo su -
# whoami
root
# pwd
/root
```
- 주의: root 권한은 시스템 전체를 변경할 수 있으니 신중하게 사용. 가능하면 sudo command 형식으로 필요한 명령만 권한 상승해서 쓰는 게 안전.

---

## 칼리리눅스 명령어 기본 - apt 패키지 관리

### 1) sudo apt update

- 패키지 목록(업데이트 가능한 패키지 정보)을 최신으로 갱신

### 2) sudo dhclient

- DHCP 서버로부터 IP 주소 자동 할당

### 3) sudo apt install vsftpd

- FTP 서버 프로그램(vsftpd) 설치

### 4) sudo apt search vsftp

- 패키지 목록에서 vsftp 관련 패키지 검색

### 5) sudo apt show vsftpd

- vsftpd 패키지의 상세 정보 확인 (버전, 설명, 의존성 등)

### 6) sudo apt list --installed

- 설치된 패키지 전체 목록 출력

### 7) sudo apt list --installed | grep ftp

- 설치된 패키지 중 ftp 관련된 것만 필터링

### 8) sudo apt remove vsftpd

- vsftpd 패키지 제거 (설정파일은 남음)

### 9) sudo apt install fonts-nanum

- 한글 글꼴 나눔폰트 설치

---

## 칼리리눅스 명령어 기본 - 사용자 추가 및 권한 부여

### 1) sudo useradd -d /home/kali2 -m kali2
```
useradd : 새로운 시스템 사용자 계정 생성(저수준).
-d /home/kali2 : 홈 디렉터리 경로를 /home/kali2로 지정.
-m : 지정한 홈 디렉터리를 만들어 줌(존재하지 않으면 생성).
kali2 : 생성할 사용자 이름.
```
- 주의: useradd는 보통 암호를 설정하지 않음 → 다음에 sudo passwd kali2로 비밀번호 설정 필요.

### 2) sudo usermod -a -G sudo kali2 
```
usermod : 기존 사용자 수정.
-G sudo : sudo 그룹에 추가(대상 그룹 지정).
-a : append — 기존 그룹 목록을 덮어쓰지 않고 추가.
```
- 주의: a 없이 -G만 쓰면 사용자가 소속된 그룹 목록이 해당 옵션으로 대체되어 버릴 수 있음

- 결과: kali2가 sudo 그룹의 멤버가 되면, 보통 sudo <command>로 관리자 권한을 쓸 수 있음 (배포판 설정에 따라 다름—우분투 계열은 sudo 그룹이 관리자 권한).

### 3) sudo chsh -s /bin/bash kali2
```
chsh : 사용자 계정의 로그인 셸(login shell) 변경.
-s /bin/bash : 사용할 셸 경로를 /bin/bash로 설정.
kali2 : 대상 사용자.
```
- 변경 대상 셸은 /etc/shells에 등록된 경로여야 함(그렇지 않으면 실패할 수 있음).

- 셸 변경은 다음 로그인부터 적용(또는 su - kali2로 즉시 확인 가능).

### 4) sudo passwd kali2

- useradd로 계정 만들고 나면 비밀번호가 없거나 잠긴 상태일 수 있음.

- passwd로 비밀번호 지정(또는 잠금 해제).

### 5) 배쉬(Bash) vs 제트(Zsh) — 간단 비교

- Bash (Bourne Again SHell)
```
1) 널리 사용되고 대부분 시스템에 기본으로 설치되어 있음.
2) 스크립트 호환성이 좋고 학습/디버깅이 쉬움.
3) 안정적이고 표준적.
```

- Zsh (Z shell)
```
1) 더 풍부한 기능(향상된 탭 완성, 글로브 확장, 테마/플러그인 풍부).
2) oh-my-zsh 같은 프레임워크로 생산성/시각적 편의성 향상 가능.
3) 기본 설치는 안 되어 있을 수 있으므로 sudo apt install zsh 필요.
4) 셸을 바꾸려면 chsh -s /bin/zsh kali2 (그리고 /etc/shells에 /bin/zsh가 있어야 함).
```

- 추천(간단): 초반엔 bash로 익숙해지는 걸 추천 (스크립트/문제 해결에 유리).

---

## 칼리리눅스 명령어 기본 - vim 파일편집

### 1) 파일 다루기 (cat)
```
cat > test.txt
```
- 새로운 파일 생성 후 입력 (기존 내용 덮어씀)
```
cat >> test.txt
```
- 기존 파일 끝에 내용 추가(append)

### 2) vi/vim 편집기 기본
```
vim test2.txt
```
- test2.txt 파일 열기

**입력 모드**
- i : Insert 모드 진입 → 텍스트 입력 가능

**저장 & 종료**
- ESC + :wq! : 저장하고 강제 종료

**편집 기능**
- dd : 현재 줄 삭제

- :set nu : 줄 번호 표시

**복사/붙여넣기 (터미널 단축키)**
- Ctrl + Shift + C : 복사

- Ctrl + Shift + V : 붙여넣기

---

## 칼리리눅스 명령어 기본 - find, locate 명령어

### 1) locate ftp [-c]

- 파일 DB에서 ftp가 들어간 경로 빠르게 검색

- -c : 결과 개수만 출력

- DB 기반 → 최신화 필요: updatedb(locate가 사용하는 파일 경로 DB 갱신)

- man locate : locate 사용법 매뉴얼 확인

### 2) find 주요 명령어
```
find 명령어(실제 파일시스템에서 직접 검색)

find / -name "ftp"
-> 루트(/) 아래에서 이름이 정확히 "ftp"인 파일 검색

find / -name "*ftp*"
-> 이름에 ftp가 포함된 모든 파일 검색

find / -type d -name "ftp"
-> 이름이 "ftp"인 디렉터리 검색

find /home -user kali -name "*.txt"
-> /home 밑에서 kali 사용자가 가진 .txt 파일 검색

find / -mtime +7 -name "*.txt"
-> 7일 이전에 수정된 .txt 파일 검색

find / -size +10M -name "*.txt"
-> 크기가 10MB 이상인 .txt 파일 검색
```

### 3) fdisk -l

- 디스크 파티션/용량 정보 확인 (root 권한 필요)

---

## 칼리리눅스 명령어 기본 - dpkg 패키지 관리

### 1) apt와 dpkg 차이
```
[dpkg]
저수준 패키지 관리 도구
.deb 패키지를 직접 설치/제거/조회
의존성 자동 해결 X (수동으로 처리해야 함)

[apt]
고수준 패키지 관리 도구
dpkg를 내부적으로 사용
저장소에서 패키지를 내려받고, 의존성 자동 해결 가능
```

### 2) dpkg 주요 명령어
```
dpkg -l
-> 설치된 패키지 목록 출력

dpkg -L apache2
-> 특정 패키지가 설치한 파일 경로 목록 확인

dpkg -s vsftpd
-> 특정 패키지의 상태/정보 확인

dpkg -r vsftpd
-> 패키지 제거 (설정 파일은 남음)

dpkg -P vsftpd
-> 패키지 완전 제거 (설정 파일까지 삭제)

dpkg -I
-> .deb 파일의 정보 조회 (설치 X, 내용만 확인)

dpkg -i
-> .deb 파일 설치
```

### 3) 주요 디렉터리 용도

**/usr/share**

- 공용 데이터 (아이콘, 문서, 매뉴얼, 지역화 파일 등)

- 실행파일은 아님, 주로 읽기 전용 리소스

**/usr/sbin**

- 시스템 관리용 실행 파일 (관리자 명령어: apache2, vsftpd 등)

**/etc**

- 시스템/프로그램 설정 파일 저장 위치

- 예: /etc/passwd, /etc/ssh/sshd_config, /etc/apache2/

---

## OSINT를 활용한 정보 수집 단계 - whois, dns 정보 확인

### 1) OSINT(Open Source Intelligence)

- 공개된 정보(오픈소스 데이터)를 수집·분석해 유용한 인텔리전스로 활용하는 것

- 대상: 웹사이트, 도메인, SNS, 검색엔진, 공개 DB 등

### 2) 관점별 OSINT 활용

**offensive 관점(공격자 입장)**

- 공격 전에 목표 시스템/조직의 정보를 수집 → 취약점 찾기

- 예: 도메인 정보, 서버 IP, 이메일, 네트워크 구조, 직원 SNS 정보

**defensive 관점(방어자 입장)**

- 공격자가 노릴 수 있는 공개된 정보 노출을 줄이고 관리

- 예: DNS 설정 점검, WHOIS 정보 보호, 불필요한 서비스/포트 차단

### 3) 주요 명령어 & 도구

**whois -H domain.com**

- 도메인 등록자, 네임서버, 만료일 등 조회

- -H : 긴 출력(약관/경고문) 숨기고 깔끔하게 보기

**DNS 정보 수집**

[fierce]

- 도메인 기반 DNS 수집/브루트포스 도구

- 예: fierce -dns *.google.com → 구글 관련 서브도메인 수집

[recon-ng]

- OSINT 프레임워크 (모듈 기반)

- 다양한 API/데이터 소스로 자동화된 정보 수집

한 가지 도구를 100% 신뢰할 수 없음

여러가지 취약점 분석 도구를 사용하며 비교/분석 하면서 명확하게 찾아낼 필요가 있음.

---

## OSINT를 활용한 정보 수집 단계 - 해킹 할 때 서브도메인(subdomain) 검색 방법 및 중요성

