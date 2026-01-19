# Security EduComp - 시스템 해킹

## 어셈블리 명령어와 함수 호출 구조

### 외울 필요가 없는 어셈블리 명령어

- PUSH, POP (PUSHAD, POPAD)
- MOV : source를 가져옴
- LEA : source의 주소를 가져옴
- ADD
- SUB
- INT : 인터럽트 명령
- CALL : 함수 호출
- INC, DEC (i++, i--)
- AND, OR, XOR (bit 연산)
- NOP
- CMP, JMP

### 리버스 엔지니어링에 필요한 스택 - 함수 프롤로그

```assembly
push ebp        ebp 삽입
mov ebp, esp    esp 값을 ebp에 저장
sub esp, 50h    esp 50h 위치를 올림(지역 변수 저장 위치)
```

### 함수의 호출 & 리턴 주소
```c
main()
{
  DWORD dwRet = HelloFunction(0x37, 0x38, 0x39);
  if (dwRet)
  // ...
}
```
다음과 같이 메인 함수에서 HelloFunction 함수를 호출하면 LIFO 방식으로 스택에 삽입한다.
```assembly
[함수 실행시 어셈블리 진행]
push 39h
push 38h
push 37h
call 401300h; HelloFunction
```
```assembly
[함수 호출 시 스택 구성]
ebp + 0x16 | 116h | 39h
ebp + 0x12 | 112h | 38h
ebp + 0x8  | 108h | 37h
ebp + 0x4  | 104h | RET 주소 (ebp, esp)
ebp        | 100h | ebp
```

---

## 함수 호출 규약

**간단한 sum 함수의 구조**

```c
int sum(int a, int b)
{
    int c = a + b;
    return c;
}
```
```assembly
push ebp
mov ebp, esp
push ecx
mov eax, [ebp+arg_0]
mov eax, [ebp+arg_4]
mov [ebp+var_4], eax
mov eax, [ebp+var_4]
mov esp, ebp
pop ebp
retn
```

**함수의 호출 규약**

1) cdecl

- 항상 call 문의 다음 줄을 살펴서 스택을 정리하는 곳을 체크한다.
- cdecl 코드는 call 이후 add esp, 8과 같이 스택을 보정
- add esp, 8 그리고 push 문이 2개 (4바이트 파라미터가 두 개임을 추측할 수 있음.)

2) stdcall

- main 함수 내부에서 스택을 처리하지 않음
- stdcall 코드는 retn 8을 사용하여 스택을 처리
- Win32 API는 stdcall 방식을 사용
- 2개의 파라미터로 WinExec 함수 호출
- WinExec 함수 호출 시 프롤로그에서 RETN 8을 발견할 수 있음

3) fastcall

- 파라미터가 2개 이하일 경우, 인자를 push로 넣지 않고 ecx와 edx 레지스터를 이용
- 레지스터를 이용하기에 메모리를 이용하는 것보다 훨씬 빠름
- 함수 호출 전, edx와 ecx 레지스터에 값을 넣는 것을 보면 fastcall 규약 함수임을 짐작할 수 있음

4) thiscall

- 주로 C++의 클래스에서 this 포인터로 이용
- 현재 객체의 포인터에 ecx에 전달하는 특징을 가짐
- 다음과 같이 ecx 포인터에 오프셋 번지를 더함

파라미터  |   주소
   a     | ecx + x
   b     | ecx + y
   c     | ecx + z

---

## OllyDbg 활용

올리디버거: 올리 유스척이 개발한 x86(32bit) 디버거, 무료, 사용의 편의성, 기능 확장을 위한 플러그인

**실행 파일 열기**
- 파일 열기
- 드래그
- 마우스 오른쪽 키를 사용하여 실행
- 프로그램 가장 앞단을 0xCC로 패치하여 실행

**실행 중인 프로세스 덧붙이기**
- File > Attach를 선택

**CPU 인터페이스 - 디스어셈블러 / 레지스터 / 스택 / 덤프 윈도우**

**메모리 맵 - 메모리에 프로그램이 배열 상태 확인 가능**

**View > Therads를 통해 현재 실행 스레드 확인 가능**

- 각 스레드는 개별 스택을 가지고 있음

**OllyDbg 코드 실행 옵션**

- F9: 실행
- F12: 정지
- F4: 선택까지 실행
- Ctrl + F9: 리턴까지 실행
- F7: 싱글 스텝 / 스텝 인투
- F8: 스텝 오버
- Enter: 함수 따라가기
- F2: 소프트웨 브레이크 포인트
- Shift + F2: 조건 브레이크 포인트

**DLL 파일 역시 디버깅 가능**

- 디버거에서 직접 실행하지 않고 loaddll.exe라는 더미 프로그램 사용

**DLL을 로드하면 DllMain에서 정지**

**예외 처리**

- Shift + F7/F8/F9
- Options > Debugging Options > Exceptions
- 패치

---

## OllyDbg 튜토리얼

실습

---

## Lena 튜토리얼 풀이를 통한 이해

실습

---

## 악성코드 분석을 위한 윈도우7 환경 구축

실습

---

## 악성코드 분석 환경 셋팅 후 스냅샷

실습

---

## FLARE VM을 이용한 악성코드 분석 환경 구축하기

실습

---

## PE 파일 개요

**PE 파일**
- Portable Executable File Format (파일이 이식 가능한 다른 곳에 옮겨져도 실행 가능하도록 만든 포맷)
- 윈도우에서 사용하는 실행 파일, DLL 파일 등을 위한 파일 형식
- 윈도우 로더가 실행 가능한 코드를 관리하는데 필요한 정보를 캡슐화한 데이터 구조체
- 링킹을 위한 동적 라이브러리 참조, API 익스포트와 임포트 테이블, 자원 관리 데이터 그리고 TLS 데이터를 포함

**실행 파일 컴파일 과정**
- Source.h -> Source.obj : Compile(모든 헤더파일과 소스 파일을 합쳐 하나의 기계어 코드 생성)
- Source.obj -> Binary.exe : Link(DLL, 리소스 데이터, Import, Export 테이블을 처리할 수 있는 정보를 윈도우에 약속된 규약에 맞춰 기입)
- Binary.exe : 실행파일 로딩 시 PE Header 정보를 토대로 DLL을 로드, 메모리에 적재될 각종 리소스 할당(PE 헤더 정보 오류시 로딩 불가)

**PE 파일 분석에 사용되는 도구**
- PEview
- Stud_PE
- PEiD
- exeinfo
- pestudio
- preframe.py

---

## PE 파일 구조 이해

**IMAGE_DOS_HEADER**
- PE 가장 첫 번째 값으로 (MZ(4D 5A) 마크 즈비코프스키) 헤더를 통해 MS-DOS 헤더의 시작을 알림(이 바이너리가 PE 파일인지 검사)
- 인텔은 리틀 엔디안 방식

**IMAGE_NT_HEADER 1** 
- 시그니처는 "PE\0\0", 4바이트 바이러스에 자신의 시그니처를 심기도 함, 바이러스나 악성코드 감염 표식용으로 사용 (지금은 안됨)

**IMAGE_NT_HEADER 2**
- 나머지는 두 개의 큰 구조체로 이루어짐, FileHeader, OptionalHeader
```
[IMAGE_FILE_HEADER 구조체]
- Machine: 어떤 CPU에서 실행 가능하지 알림(일반 Desktop Labtop에서 사용할 경우 별로 필요 없음)
- NumberOfSections: 이 파일이 가진 섹션의 개수를 알림
- TimeDateStamp: obj -> EXE 파일을 만든 시간을 알림 (델파이로 만들어진 파일은 항상 1992년으로 표시됨)
- SizeOfOptionalHeader: IMAGE_OPTIONAL_HEADER32의 구조체 크기를 알림(PE를 로딩하기 위한 굉장히 중요한 구조체를 담고 있음, 운영체제마다 크기가 다를 수 있어 PE 로더에서는 이 값을 먼저 확인)
- Characteristics: 이 파일이 어떤 형식인지 알림
```
```
[IMAGE_OPTIONAL_HEADER 구조체]
- Magic: 32bit는 0x10B, 64bit는 0x20B
- MajorLinkerVersion, MinorLinkerVersion: 사용한 컴파일러 버전
- SizeOfCode: 코드 양의 전체 크기
   악성코드: 이 값을 참고하여 자신의 코드를 복제할 위치 기준을 잡음
   보안 솔루션: 코드 섹션의 무결성 검사
- AddressOfEntryPoint: 파일이 메모리에서 시작되는 지점
- BaseOfCode: 실행 코드 위치, ImageBase와 BaseofCode를 더한 값부터 코드 시작
- ImageBase: 로드할 가상 메모리 주소
- SectionAlignment, FileAlignment: 각 세션을 정렬하기 위한 정렬 단위(기본값 0x1000)
- SizeOfImage: EXE/DLL이 메모리에 로딩됐을 때 선체 크기
- SizeOfHeader: PE 헤더의 크기를 알림(기본값 0x1000)
- IMAGE_DATA_DIRECTORY 구조체: VirtualAddress와 Size 필드, Export, Import, Rsrc 디렉터리와 IAT 등의 가상 주소와 크기 정보
```
```
PE: 32비트 포맷
PE32+ 또는 PE+: 64비트 버전
```

**섹션(Sections)**
- PE 파일에서 섹션은 프로그램의 실제 내용을 담고 있는 블록
- PE가 가상 주소 공간에 로드된 후 섹션 내용이 참고되고 실행

 **섹션의 종류**
 - .txt: 코드 섹션
 - .data: 데이터 섹션
 - .rdata: 읽기 전용 데이터 섹션
 - .reloc: 기준 재배치 섹션
 - .edata: 내보내기(export) 섹션
 - .idata: 가져오기(import) 섹션
 - .didat: 지연 로드 섹션
 - .tls: TLS 섹션
 - .rsrc: 리소스 섹션
 - .debug: 디버깅 섹션

**IMAGE_Section_Header**
- 주로 각 섹션에 대한 이름, 시작 주소, 사이즈 등의 정보를 관리하는 구조체

**IAT 호출 구조**
- IMPORT Address Table: 로드된 실제 함수 주소 목록
- IMPORT Name Table: 임포트 함수 이름 주소 목록
- IMPORT Hints/Names & DLL Names: 로드하는 임포트 함수 이름 목록

---

## PE 헤더 복구하기

실습습

---

## 패커 개요

**패커(Packer)이란?**
- 실행 파일 압축기
- 사용 목적: PE 파일의 크기를 줄이고자 하는 목적, PE 파일 내부 코드와 리소스(String, API 등)를 감추기 위한 목
- 패킹한 후의 EntryPoint는 OEP가 아님.
- 프로텍터: 패킹 기술, 리버싱을 막기 위한 다양한 기법 추가, 원본 파일보다 크기가 커질 수 있음.

```
[패킹 순서]
1. PE 구조 분석
2. 섹션 선택 (.text 등)
3. 압축/암호화 수행
4. 언패커 Stub 코드 생성
5. 새로운 섹션에 Stub + 압축 데이터 삽입
6. PE Header 재구성 (EntryPoint 변경)
7. 원본 섹션 제거
8. 최종 패킹된 PE 파일 생성
```

**각종 백신 우회 방법**
- 시그니처 변경: 단순히 변수 몇 개를 추가 및 함수 위치를 변경하여 리빌드
- 쓰레기 코드를 통한 우회 방법: 네 줄이 수행된 결과를 보면 아무것도 바뀌는 것이 없음 (대칭 구조)
```
예)
push ebx -> pop ebs
inc ebx -> dec dbx
```
- 헤더 변조: TimeDateStamp, Optional Header, CheckSum, 메모리 속성 변조
- 대체 가능한 어셈블리어
```
예)
sub ebp, 7 = add ebp, -7 : 동일하다.
add ebx, eax = adb ebx, eax : 물론 CF에 따라 변화는 있다.
call 401184 = push 401184 -> retn : call
```
- 실행조차 되지 않는 코드

**Themida**
- 지상 최고의 패커라 불림
- 안티 디버깅과 안티 분석, 언패킹 분석을 매우 어렵게 하는 안전한 패커
- VMware, 디버거, 프로세스 모니터(ProcMon, Process Monitor) 분석을 방지하는 기능
- 패킹된 실행 파일은 이례적으로 크기가 큼
- 언패커가 존재하나 Themida 버전과 프로그램을 패킹할 때 사용한 설정에 따라 성공률이 다름
- 카카오톡 프로그램도 이 패킹 프로그램을 사용

---

## UPX 언패킹

```
upx.exe 프로그램 -o 프로그램 이름: 프로그램 이름으로 패킹
upx.exe -d 프로르램: 패킹된 프로그램 언패킹
```

UPX 패킹의 특징: PUSHAD, POPAD 존재

- PUSHAD: 레지스터 EAX부터 EDI까지 스택에 Push.
- POPAD: 스택에 있던 레지스터들을 Pop.

---

## Pestudio - 새로운 버전의 구글 검색 기능을 살펴보자

**Pestudio이란?**
- 악성코드 분석, PE 파일(윈도우 실행 파일) 구조 분석에 특화된 정적 분석 도구

웹 사이트에 접속해 pestudio - downloads

악성코드 분석시, Imports, Strings 정보가 중요함.

Pestudio에는 search MSDN(Imports), search Google(Strings) 기능이 있어 편리하지만, ChatGPT 같은 생성형 인공지능이 더 편리할 수도 있음.

---

## Pestudio - 새로운 버전의 MITRE ATT&CK 가이드 기능

---

## 악성코드 분석 방법

---

## 기초 정적 분석

---

## 기초 정적 분석 실습

---

## 기초 동적 분석

---

## 기초 동적 분석 실습

---

## 마무리

---

## IDA 개요

---

## IDA 실습 (1)

---

## IDA 실습 (2)

---

## 악성코드 주요 행위 분석 (1) - API, 파일, 시스템

**윈도우 API**
- 대부분의 악성코드는 윈도우 플랫폼에서 동작
- 운영체제와 밀접하게 상호작용

**핸들(Handle)**
- 윈도우, 프로세스, 모듈, 메뉴, 파일 등과 같이 운영체제에서 오픈되거나 생성
- 객체나 메모리 위치를 참조한다는 점에서 포인터와 같음

```
운영프로그램 이 핸들 요청
Executive Object가 객체 생성 후 핸들 반환
운영프로그램이 객체 조작 요청
```

**파일 시스템 함수**
- 악성코드가 시스템과 상호작용하는 가장 일반적인 방식은 파일을 생성하거나 수정해 파일명을 구별하거나 기존 파일명을 변경
- 식별자로 사용 가능
- 파일을 생성해 그 파일에 웹 브라우징 내용을 저장한다면 해당 스파이웨어 형태일 가능성이 높음

1) CreateFile: 파일을 생성하고 열 때 사용, 기존 파일, 파이프, 스트림, I/O 장치를 열고 새로운 파일을 생성
2) ReadFile, WriteFile: 파일을 읽고 쓰는 데 사용, 두 함수 모두 파일을 스트림 형태로 운영
3) CreateFileMapping, MapViewOfFile: 파일 매핑은 파일을 메모리로 로드해 실행, 조작할 수 있게 함.

**특수 파일**
- 드라이브 문자와 폴더명으로 접근할 수 없는 파일도 존재

**공유 파일**
-\\SwerverName\share나 \\ServerName\share로 시작하는 이름을 가짐
- 네트워크 상에 저장된 공유 폴더에서 디렉터리나 파일에 접근

**네임스페이스를 통해 접근 가능한 파일**
- 운영체제 내에서 네임스페이스를 통해 접근
- 네임스페이스: 고정된 숫자의 폴더와 각각 저장하는 다른 유형의 객체
- Win32 장치 네임스페이스는 접두사 \\.\를 이용 -> 물리적 장치 접근하여 파일처럼 읽음

**네임 스페이스를 이용한 사례**
파일 시스템을 무시하여 PhysicalDisk나 Physical Memory에 바로 접근하기 위해 \\.\PhysicalDisk나 \Device\PhysicalMemory를 사용
-> 파일을 직접 생성
-> 비할당 영역에 악성코드 생성(백신 우회)

**ADS(Alternate Data Streams): NTFS 내의 기존 파일에 데이터를 추가 기능 제공**
- 한 파일에 다른 파일을 추가
- 추가 데이터는 디렉터리 목록에 나오지 않고, 파일 내용 출력이 안됨
- ADS 데이터는 normalFile.txt:Stream:$Data라는 규칙을 가짐

---

## 악성코드 주요 행위 분석 (2) - 자동 실행 레지스트리, 서비스

**윈도우 레지스트리**
- 운영체제와 설정이나 옵션 같은 프로그램 구성 정보를 저장
- 수직 구조의 정보 데이터베이스 -> 성능 향상!
- 네트워킹, 드라이버, 시작, 사용자 계정, 다른 정보 등 거의 모든 윈도우 구성 정보 저장
- 레지스트리를 활용하는 악성코드(영구 데이터나 설정 데이터 저장, 컴퓨터 부팅 시마다 자동으로 동작할 수 있게 수정)
- 레지스트리 구성 값: 루트 키, 서브 키, 키, 값 엔트리, 값이나 데이터

1) HKEY_LOCAL_MACHIN: 시스템 전역 설정 저장
2) HKEY_CURRENT_USER: 현재 사용자에 특화된 설정 저장
3) HKEY_CLASSES_ROOT: 정의한 유형 정보를 저장
4) HKEY_CURRENT_CONFIG: 현재 하드웨어 구성 설정, 특히 현재 설정과 표준 설정의 차이를 저장
5) HKEY_USERS: 기본 사용자, 새로운 사용자, 현재 사용자의 설정을 정의

**레지스트리 편집기**
- regedit으로 윈도우에서 접근
- 루트 키: HKLM, 서브 키: SOFTWARE, Microsoft 등..., 값 엔트리: ASPDaemon 등...

**서비스**
- 악성코드가 새로운 코드를 실행하는 또 다른 방식은 서비스로 설치하는 방법
- 백그라운드 애플리케이션으로 실행하는 서비스를 사용, 프로세스나 스레드 없이 실행(코드가 스케줄링돼 사용자 입력 없이 윈도우 서비스 관리자가 실행)
- 서비스 API: OpenSCManager, CreateService, StartService

**서비스 제어 sc.exe**

**자동으로 시작하는 프로그램**
- Autoruns는 운영체제가 시작 시 자동으로 실행되는 코드를 목록화
- IE, 다른 프로그램으로 로드되는 DLL, 커널로 로드되는 드라이버 목록화
- 자동으로 코드가 실행하게 되는 25~30 군데 위치를 조사
- 전체 리스트가 아닐 수 있음

**일반 레지스트리 함**
- 악성코드에서 레지스트리를 수정할 목적으로 사용하는 윈도우 API(RegOpenKeyEx, RegSetValueEx, RegGetValue)

**.reg 파일을 이용한 레지스트리 스크립트**
- reg. 확장자를 가진 파일은 가독성이 있는 레지스트리 데이터를 포함
- 더블 클릭하여 실행하면 담고 있는 정보를 자동으로 레지스트리로 병합해 수정

**버클리 호환 소켓**
- 버클리 호환 소켓 네트워크 기능은 Winsock 라이브러리에서 주로 ws2_32.dll에 구현
- 버클리 호환 소켓 네트워크 함수

---

## 악성코드 주요 행위 분석 (3) - 네트워크, DLL, 스레드 등

**WinlNet API**
- Winsock API보다 상위 수준의 API
- Wininet.dll에 저장
- HTTP, FTP와 같은 프로토콜을 구현
- InternetOpen: 인터넷 연결 초기화
- InternetOpenUrl: URL에 연결할 때 사용한다
- InternetReadFile: ReadFile 함수 같이 프로그램이 인터넷에서 다운로드한 파일에서 데이터를 읽음

**DLL(Dynamic Link Libraries, 동적 링크 라이브러리)**
- 현재 윈도우에서 다양한 애플리케이션끼리 코드를 공유하는 라이브러리를 사용하는 방식
- 다른 애플리케이션에 의해 실행할 수 있는 익스포트 함수를 포함
- 정적 라이브러리는 DLL 이전에 사용하던 표준 -> 프로세스 별로 메모리에 로드

**악성코드 제작자가 DLL을 이용하는 법**
- 악성코드 저장용: exe 파일 프로세스당 하나만 가지기 때문에 dll을 사용하여 다른 프로세스에서 실행 가능
- 윈도우 DLL 사용: 거의 모든 악성코드는 윈도우와 상호작용할 수 있는 윈도우 OS DLL을 사용하여 악성코드 분석가에게 상당한 통찰력을 제공
- 외부 DLL 사용: 다른 프로그램과의 상호동작을 위해 외부 DLL을 사용
- 윈도우 API를 활용해 직접 다운로드하지 않고 파이어폭스의 DLL을 이용

**프로세스**
- 새로운 프로세스를 생성하거나 기존 프로세스를 변형해 현재 프로그램 외부에서 코드를 실행

**스레드**
- 프로세스는 실행 컨테이너, 스레드는 윈도우 운영체제가 실행
- 프로세스 내의 코드 일부를 실행

**스레드 문맥**
- 운영체제가 스레드를 교체하기 전에 CPU의 정보를 스레드 문맥에 저장
- 스레드가 CPU 내부 레지스터 값 변경

**뮤텍스를 이용한 내부 프로세스 조정**
- 뮤텍스: 여러 프로세스와 스레드를 조정하는 전역 개체, 커널에서는 뮤턴트라고 부르기도 함
- 뮤텍스 메커니즘의 특징
1) Atomicity: 하나의 스레드가 mutex를 이용해서 잠금을 시도하는 도중에 다른 스레드가 mutex 잠금 불가
2) Singularity: 만약 스레드가 mutex 잠금을 했다면 해당 스레드가 mutex 잠금을 해제하기 전까지 다른 어떠한 스레드도 mutex 잠금 불가
3) Non-Busy Wait: 스레드는 다른 스레드가 락을 해제하기전까지 해당 지점에 머물러 어떠한 CPU 자원도 소비하지 않음

**Mutex API**
- 생성 - CreateMutex
- 열기 - OpenMutex
- 삭제 - ReleaseMutex

**COM(컴포넌트 객체 모델)**
- 이종의 소프트웨어 컴포넌트에서 상호 세부내용을 모르더라도 서로 다른 코드를 호출할 수 있게 하는 인터페이스 표준
- COM 객체는 클래스 식별자 또는 식별자라고도 불리는 GUID를 통해 접근

**CoCreateInstance**
- COM 기능에 접근할 때 사용

**COM 서버 악성코드**
- BHO라는 IE의 외부 플러그인을 사용하여 구현
- 특정 프로세스 실행 없이, 인터넷 트래픽 모니터링, 브라우저 사용 추적, 인터넷 통신
- 다음 함수를 임포트

**네이티브 API**
- 악성코드 제작사 사이에 흔히 사용하는 하위 수준의 윈도우 상호 작용 인터페이스
- 악의적이지 않은 프로그램은 거의 사용하지 않음
- 시스템, 프로레스, 스레드, 핸들, 기타 항목에 관한 정보 수집에 사용

---

## 악성코드 실전 기초정적분석

실습

안티디버깅: UnhandledExceptionFilter,x,-,x,kernel32.dll

---

## 악성코드 실전 기초동적분석

실습

---

## 악성코드 실전 고급정적분석

---

