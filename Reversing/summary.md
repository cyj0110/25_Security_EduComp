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

---

## Lena 튜토리얼 풀이를 통한 이해

---

## 악성코드 분석을 위한 윈도우7 환경 구축

---

## 악성코드 분석 환경 셋팅 후 스냅샷

---

## FLARE VM을 이용한 악성코드 분석 환경 구축하기

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

