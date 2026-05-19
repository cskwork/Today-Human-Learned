# Oracle Cursor — 쿼리 결과를 한 줄씩 처리하는 포인터

> **TL;DR**
> 커서는 SELECT 결과가 담긴 메모리 공간을 가리키는 포인터다. 한 번에 한 행씩 꺼내 처리할 때 쓴다.

---

## 커서를 왜 쓰는지 감 잡기

SQL은 집합(set) 기반 언어다. SELECT 하나로 수백만 행을 한꺼번에 반환할 수 있다.
그런데 현실에서는 "행마다 다른 처리를 해야 할 때"가 있다. 예를 들어 직원별로 급여를 계산하고,
조건에 따라 다른 로직을 적용해야 한다면 집합 연산만으로는 부족하다.
Oracle이 이 문제를 해결하는 방식이 커서다.

커서를 열면 DBMS는 쿼리 결과를 메모리에 올리고 시작 위치를 가리키는 포인터를 돌려준다.
그 포인터를 한 칸씩 옮기며(FETCH) 데이터를 꺼내는 구조다.

`핵심 흐름: DECLARE -> OPEN -> FETCH -> (LOOP) -> CLOSE`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| CURSOR | 쿼리 결과가 저장된 메모리 위치를 가리키는 포인터 |
| OPEN | 커서를 열어 SELECT를 실행하고 결과를 메모리에 올리는 동작 |
| FETCH | 커서가 가리키는 현재 행을 변수에 꺼내는 동작. 호출할 때마다 한 행씩 전진 |
| %NOTFOUND | 더 이상 가져올 행이 없으면 TRUE가 되는 커서 속성. LOOP 탈출 조건으로 사용 |
| 묵시적 커서 | DML(INSERT/UPDATE/DELETE)이나 단순 SELECT 실행 시 Oracle이 자동으로 만드는 커서 |

## 예를 들어 설명하면

직원 테이블에서 id >= 20인 직원 이름을 한 명씩 출력하는 명시적 커서 예제다.

```sql
DECLARE
    p_name employee.name%TYPE;          -- 결과를 담을 변수
    CURSOR cur_name(ff INT) IS
        SELECT name FROM employee WHERE id >= ff;
BEGIN
    OPEN cur_name(20);                  -- 커서 열기, SELECT 실행
    LOOP
        FETCH cur_name INTO p_name;     -- 한 행 꺼내기
        EXIT WHEN cur_name%NOTFOUND;    -- 더 없으면 종료
        DBMS_OUTPUT.PUT_LINE(p_name);
    END LOOP;
    CLOSE cur_name;                     -- 커서 닫기
END;
```

묵시적 커서는 Oracle이 자동으로 관리한다. `SQL%ROWCOUNT`로 영향받은 행 수를,
`SQL%FOUND`로 결과 존재 여부를 바로 확인할 수 있다.

## 이 단계에서 중요한 판단 기준

행 수가 적고 행마다 다른 처리가 필요할 때만 커서를 쓴다. 대용량 처리에는 집합 기반 SQL이 항상 빠르다.

## 한 줄 요약 — 이것만 기억하면 된다

**커서는 SELECT 결과를 한 행씩 꺼내 처리하는 포인터이며, OPEN -> FETCH -> CLOSE 순서로 사용한다.**

## 나중에 더 깊게 들어가면

- FOR 루프 커서: OPEN/FETCH/CLOSE를 자동으로 처리하는 간결한 문법
- REF CURSOR: 런타임에 쿼리를 동적으로 지정하는 커서 타입
- 커서 성능: 대용량 데이터에서 커서 대신 BULK COLLECT + FORALL을 쓰는 이유

---

**원본:** [Oracle Cursor — https://memoryhub.tistory.com/79](https://memoryhub.tistory.com/79)
