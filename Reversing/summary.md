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

##
