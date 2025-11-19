## 어셈블리 명령어와 함수 호출 구조

# 외울 필요가 없는 어셈블리 명령어

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

# 리버스 엔지니어링에 필요한 스택 - 함수 프롤로그

```
push ebp        ebp 삽입
mov ebp, esp    esp 값을 ebp에 저장
sub esp, 50h    esp 50h 위치를 올림(지역 변수 저장 위치)
```

# 함수의 호출 & 리턴 주소
```c
main()
{
  DWORD dwRet = HelloFunction(0x37, 0x38, 0x39);
  if (dwRet)
  // ...
}
```
다음과 같이 메인 함수에서 HelloFunction 함수를 호출하면 LIFO 방식으로 스택에 삽입한다.
```
[함수 실행시 어셈블리 진행]
push 39h
push 38h
push 37h
call 401300h; HelloFunction
```
```
[함수 호출 시 스택 구성]
ebp + 0x16 | 116h | 39h
ebp + 0x12 | 112h | 38h
ebp + 0x8  | 108h | 37h
ebp + 0x4  | 104h | RET 주소 (ebp, esp)
ebp        | 100h | ebp
```

---

## 함수 호출 규약

