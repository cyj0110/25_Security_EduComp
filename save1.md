# 사이버 침해사고 분석도구 교육

http://www.forensic-artifacts.com/

포렌식 개념 설명 참고

## MAGNET AXIOM

### AXIOM 기본 Setting

최초 AXIOM Process로 가져옴 -> 사용자 정의(수집, 분석, 분류) -> AXIOM Examine으로 전달

e01, dd 파일을 가져오는 것은 상관 X 

그러나 백신이 읽을 수 있는 파일(Raw, Zip) <- 안티바이러스가 읽고 삭제

교육용: AXIOM Cyber

설정 - 도구 에서 사전 작업 필수

**[도구]**
1) 원격 수집 - AXIOM Cyber 기능
2) 이미징 중 - 이미징 시 필요한 기능

```
이미지 세그먼테이션(없음 - 1TB)
이미지 해싱 (Disk -> File)
모바일 장치 상태 복원(AXIOM 내에서 Image화 -> 필요 없음)
클라우드도 패스
```
```
[이미지 세그먼테이션]
20GB 파일 세그먼테이션
FTK Imager: 500MB
Encase: 2GB (AAA.E01, AAA.E02, ...)
...

세그먼테이션화해서 저장하는 이유는 저장장치 용량이 넘지 않게 해야하기 때문.
```

3) 처리 중 - AXIOM 분석 내 설정
```
임시 파일 위치(AXIOM 내에서 저장되는 임시파일을 어디 저장할것인가?)
이미지 해시 확인(AAA =/ AAA, 모든 데이터를 기반으로 해시 값을 만들어야 하므로 시간이 오래 걸림)
복제(다른 경로에 같은 파일명, 같은 확장자인 파일이 있을 경우에 어떻게 할 것인가? / 소스 경로: 원본 파일이 있는 위치)
로깅(AXIOMExamine, Case Information 두 개가 가장 중요)
검색 속도(코어 개수 조절 가능)
메모리 분석 검색 속도(메모리 조절)
시간 정밀도(YYYY.MM.DD.HH.MM.SS.... 밀리초 까지 포함?)
```
4) 해싱
```
해싱을 위한 파일 크기 제한(500MB이상 파일들은 해시값 계산 안함)
해시 형식(MD5, SHA1, SHA256)
```
5) AXIOM PROCESS 설정 - 시스템 설정
```
PASSWARE Kit(패스워드 크래킹 툴 사용해서 암호 해독할 것인가?)
언어 설정
```
6) 제품 통합 - 패스

---

### AXIOM Process Case 생성

**[새 사례 생성]**
1) 사례 세부 정보 - 사례 파일의 위치, 인식된 장치의 위치 설정
2) 증거 소스 - 현재 라이센스에서는 컴퓨터, 모바일, 차량 가능
```
컴퓨터 - 윈도우 - 증거 로드(드라이브, 이미지, 파일 및 폴더, 볼륨 섀도 복사본(복구 시점, 랜섬웨어가 싫어함), 메모리, $MFT)
증거 인식(이미징 또는 증거 채증)
검색 유형(기본 - 전체, ex) NTFS 모든 메타데이터 분석) / 빠른, 섹터 레벨(512GB), 맞춤형(커스텀)
```
3) 사례에 추가된 증거 소스

4) 처리 세부 정보

```
[아카이브 및 모바일 백업 검색]
모바일 백업 검색 - itunes와 같이 PW로
중첩된 아카이브 및 모바일 백업 - 몇 번까지 ZIP 파일 해제?
```

```
[검색 키워드 추가]
아티팩트
모든 콘텐츠

키워드 목록 - .txt, .kws
키워드 - 정규표현식
```

```
[파일에서 텍스트 추출(OCR)]
OCR <- 영문만 가능
```

```
[해시 및 일치 항목 찾기]
모든 파일의 해시 값 계산
일치하는 해시 값이 있는 태그가 알려진 파일
```

```
[AI 기능]
잘 쓰지 않음
```

```
[손상 지표 식별]
패스
```

```
[추가 아티팩트 찾기]
동적 앱 찾기 사용 - 모바일 전용 기능
```

5) 아티팩트 세부 정보

```
[컴퓨터 아티팩트]

[아티팩트 구문 분석 및 카빙]

[권한 있는 콘텐츠]

[날짜 범위 필터]
```

6) 사건 발행 - 외부로 증거 보내겠다.

7) 증거 분석

---

### AXIOM Examine 인터페이스

**사전 작업**: 도구 - 커넥션 빌드, 타임 라인 구축

**인터페이스**: 필터 바, 서치 바, 네비게이션 판넬, 에비던스 판넬, 디테일 카드

**서치 바**: 이미 인덱싱된 특정 문자열 검색 편리
  
**고급 검색**

1) 검색어 또는 정규식 패턴으로 +, - 하여 필터링된 결과 표시 가능
2) 윈도우 기본 경로가 포함된 경우, 이를 제외하여 결과를 표시하게 할 수 있다.

**COPILOT**: 분석 결과를 Magnet社로 보내줌. 그러나 인터넷 환경 필수

---

### AXIOM Examine Artifacts 분석

**열에서 필터링**: 각 컬럼별 상세 필터링 가능

**Chrome 웹 방문**: URL 필터링: search - 검색어 기록

**식별자** - 장치

프로필 생성 후 필터링 하면 관련된 내용 전부 출력 가능

---

포렌식(디지털 포렌식)에서 **아티팩트(artifact)**란, 사용자의 행위나 시스템 동작으로 인해 디지털 기기 안에 남은 **‘흔적 정보’**를 의미.

1. 시스템 아티팩트: 운영체제나 시스템이 자동으로 생성·관리하는 흔적, 사용자의 의도와 무관하게 시스템 동작 과정에서 남음
2. 사용자 아티팩트: 사용자의 직접적인 행동으로 생성된 흔적, 파일 열기, 웹 검색, 메시지 전송 등

```
Windows + R -> ID/PW 입력 -> 접속 시도 -> 접속 성공 ->                  폴더                  ->                            폴더 이동 및 파일 실행             ->                       바탕화면이나 또는 인터넷 유출
( NTUSER.DAT ) ( EVENTLOG-SECURITY )  ( EVENTLOG )  ( NTUSER.DAT, USRCLASS.DAT, SHELLBAGS ) ( LNK, JUMPLIST, OPENSAVEPIDMRU, RECENTDOCS, BROWSER URL, WINDOWS HISTORY, ...)  ( BROWSER URL, POWERSHELL HISTORY )
    RUNMRU                              원격지 PC            
```

아티팩트 - 운영체제 - MRU 실행명령: Win+R 실행 명령들
ex) \\192.168.0.68: 공유 폴더에 접근

RUNMRU는 Windows 레지스트리에서 사용자가 **‘실행 창(Run)’**에 입력한 명령어 기록을 저장하는 대표적인 사용자 아티팩트

Windows 이벤트 로그: Event ID

로그인 시도(4648), 로그인 실패(4625), 로그인 성공(4624)의 Event ID가 다 다름 - 컬럼 필터링

실패와 성공 ID는 목적지(대상) PC에 남아 있음.

```
<Data Name="SubjectUserName">insec</Data>
```
-> insec이라는 PC에서 

```
<Data Name="TargetUserName">lab</Data>
```
-> lab으로 로그인 시도

 폴더 접근 흔적은 아티팩트 - 운영체제 - SHELLBAGS에서 조회 가능
 
 파일 실행 흔적은 아티팩트 - 운영체제 - LNK 파일에서 조회 가능

```
드라이브 유형, 대상 파일 생성 날짜/시간
볼륨 일련번호는 포맷할 때마다 달라짐

```

점프 리스트 흔적은 아티팩트 - 운영체제 - 점프 목록에서 조회 가능 
```
 점프 목록은 최근 실행한 항목을 표시하는 것
 액세스 횟수를 따로 표시함
 잠재적 앱 이름: Chrome (pdf 파일을 chrome으로 실행) 
```
 
 인코딩 깨짐은 인코딩 변경 -> 해당 적중 선택 -> korean 

 AXIOM에서 태그는 필터 및 분류를 위해서도 사용하지만, 추후 결과 보고를 위해 뽑아낼때도 사용

** [내보내기 기능]**

 HTML, PDF - AXIOM에서 제공하는 타입

 각종 설정 후 내보내기

 ---

 ## X-Ways Forensics

C:\Program Files\X-Ways Forensics에서 viewer를 포함한 필수 패키지 세팅 필요, MPlayer는 X

 option - general, viewer programs 세팅

```
 [general]
 ✔ Always run as administrator: 실행할 때마다 관리자 권한으로
 임시 파일이 저장될 경로
 백업이 저장될 경로
 케이스와 프로젝트가 저장될 경로
 ...

 X-Tensions 확장 모듈 형식의 스크립트

 Hash 알고리즘(MD5, SHA256)

 Notation에서 날짜-시간 표시 방법 변경 가능
```
```
[Viewer Programs]
✔ Activate separate viewer component: X-way 뷰어 연동 필수
R·V·S(Refine Volume Snapshot): X-way에서 자동으로 정보 수집하는 기능
```

---

### X-Ways 기본 setting

AXIOM과 다르게 New Case를 생성하는 것이 굉장히 간소화

```
[New Case]
Directory: 사건이 저장될 경로
Description: 사건의 경위
Examiner(s), User: Me: 패스
Log 저장 관련: 로그관리가 굉장히 detail 하다 / 활성화 로그가 만들어지는 게 까다로움
✔ Log general activity
✔ With screenshots: 옆의 숫자는 클릭 수, 텍스트와 스크린샷을 같이
Time Zone 세팅 필수: 한국어
BackUp 관련은 기본 설정으로
```

설정이 끝나고 증거 자료 File 눌러 추가

AXIOM은 누구나, X-Way는 파일럿 역량에 따라 결과가 다름

```
[NTFS 파일 시스템 구조]
||  GPT  |  시스템 예약어  |  VBR  |  Data  |   ...   ||
```
전체 크기를 디스크라고 하고, VBR부터 하나의 논리적 단위를 볼륨이라 함.

```
[Refine Volume Snapshot]
✔ Run X-Tensions: 조금 더 디테일하게 보기
✔ Particularly thorough file system data structure search: 010 파일 리더에서 구조체별로 색깔 다르게
✔ File header signature search: 데이터 카빙 기능, 다른 제품에 비해 빠름
✔ Block-wise hashing and matching / Identify knwon documents using FuzZyDoc: 유사도 매치, 원본 파일이 있어야 함, 블록마다 해시패턴 적용
✔ Simulaneous Search: 키워드 서치 / 인덱스 서치는 시간이 더 빠르지만 초기 세팅 시간 있음 / 체크 해제
✔ Match hash values against hash database(s): 파일 타입을 시그니처로 찾음(예: dll -> exe로 임의 변경 파일)
✔ Capture sporadic still images from videos: 썸네일 이미지를 다시 동영상(움짤)으로
```

---

