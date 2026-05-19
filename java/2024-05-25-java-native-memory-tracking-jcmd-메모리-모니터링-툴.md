# Java Native Memory Tracking(JCMD) 메모리 모니터링 툴

> **TL;DR**
> JCMD는 JVM 프로세스에 직접 명령을 보내 힙·스레드·네이티브 메모리 상태를 실시간으로 진단하는 커맨드라인 도구다.

---

## JCMD를 왜 쓰는지 감 잡기

서버가 갑자기 느려지거나 메모리가 계속 증가할 때, 원인이 Java 힙인지 네이티브 메모리인지 판단하기 어렵다. 로그만 봐서는 알 수 없고, 재시작하면 증거가 사라진다.

JCMD는 실행 중인 JVM 프로세스에 명령을 직접 전달해 힙 덤프, 스레드 덤프, GC 통계, 네이티브 메모리 사용량을 그 자리에서 뽑아낸다. Oracle Java 7부터 JDK에 기본 포함되어 있어 별도 설치가 필요 없다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 프로세스 식별(jcmd) → 명령 전달(jcmd <pid> <command>) → 결과 출력/저장`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| PID | 운영체제가 각 프로세스에 부여하는 고유 번호. jcmd 명령에서 대상을 특정할 때 쓴다. |
| 힙 덤프(Heap Dump) | JVM 힙 전체를 파일로 저장한 스냅샷. 메모리 누수 분석에 쓴다. |
| 스레드 덤프(Thread Dump) | 모든 스레드의 현재 상태를 출력한 것. 데드락이나 병목 위치 파악에 쓴다. |
| NMT(Native Memory Tracking) | Java 힙 바깥, 즉 OS에서 직접 할당된 메모리까지 추적하는 기능. |
| 베이스라인(Baseline) | 특정 시점의 메모리 상태를 기준점으로 저장해두고, 이후 변화를 diff로 비교하는 방법. |

## 예를 들어 설명하면

운영 중인 애플리케이션 서버의 메모리가 조금씩 증가한다고 의심될 때:

```bash
# 1. 실행 중인 Java 프로세스 확인
$ jcmd
1234 com.example.MyApplication

# 2. NMT 베이스라인 설정 (서버 기동 직후에 한 번 실행)
$ jcmd 1234 VM.native_memory baseline

# 3. 일정 시간 후 변화량 확인
$ jcmd 1234 VM.native_memory detail.diff
```

diff 결과에서 `+` 기호가 붙은 항목이 메모리가 증가한 영역이다. `Class` 항목이 지속적으로 증가하면 클래스 로딩 문제, `Thread`가 증가하면 스레드 누수를 의심한다.

NMT를 활성화하려면 JVM 기동 옵션에 다음을 추가해야 한다:

```
-XX:NativeMemoryTracking=summary
```

## 이 단계에서 중요한 판단 기준

힙 덤프와 스레드 덤프는 순간적으로 JVM을 멈추거나 성능을 저하시킬 수 있으므로, 트래픽이 적은 시간대에 실행하거나 사전에 공지해야 한다.

## 한 줄 요약 — 이것만 기억하면 된다

**JCMD는 실행 중인 JVM에 직접 진단 명령을 보내는 도구이고, NMT를 함께 쓰면 힙 외부 메모리 누수까지 잡아낼 수 있다.**

## 나중에 더 깊게 들어가면

- jmap, jstack 등 개별 JDK 진단 도구와 JCMD의 차이점
- Eclipse MAT(Memory Analyzer Tool)로 힙 덤프 파일을 분석하는 방법
- GC 로그와 NMT 결과를 함께 읽어 메모리 문제를 종합 진단하는 방법

---

**원본:** [Java Native Memory Tracking(JCMD) 메모리 모니터링 툴 — https://memoryhub.tistory.com/50](https://memoryhub.tistory.com/50)
