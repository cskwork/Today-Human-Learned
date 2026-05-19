# Firewalld에서 포트 구성 방법

> **TL;DR**
> firewall-cmd 명령어로 포트를 영구 개방하고, --reload로 적용한 뒤, --list-ports로 검증한다.

---

## Firewalld를 왜 쓰는지 감 잡기

리눅스 서버에 서비스를 올리면 기본적으로 외부 접속이 막혀 있다. Firewalld는 iptables 위에 앉아서 규칙 관리를 편하게 해 주는 방화벽 데몬이다. 직접 iptables를 편집하면 실수하기 쉬운데, Firewalld는 명령어 한 줄로 포트를 열고 닫을 수 있어서 운영 서버에서 많이 쓴다. 특히 RHEL·CentOS·Fedora 계열에서 기본으로 탑재되어 있다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 포트 열기 → 규칙 영구 저장(--permanent) → 방화벽 재로드(--reload) → 검증(--list-ports)`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 포트(Port) | 서버 문의 번호. 80번은 HTTP, 3306번은 MySQL 같은 식으로 서비스마다 약속된 번호가 있다. |
| --permanent | 재부팅 후에도 규칙이 유지되도록 파일에 저장하는 옵션. 없으면 재시작 시 규칙이 사라진다. |
| --reload | 실행 중인 방화벽에 변경된 규칙을 적용하는 명령. 서비스 중단 없이 갱신된다. |
| zone | 네트워크 인터페이스별로 신뢰 수준을 구분하는 단위. 기본 zone은 public이다. |
| tcp/udp | 포트를 열 때 어떤 전송 프로토콜을 허용할지 지정. 대부분의 웹·DB 서비스는 tcp다. |

## 예를 들어 설명하면

MySQL(3306), 대체 HTTP(8080), Oracle(1521)을 한 번에 여는 경우:

```bash
# 포트 개방 (영구 적용)
sudo firewall-cmd --add-port=8080/tcp --permanent
sudo firewall-cmd --add-port=3306/tcp --permanent
sudo firewall-cmd --add-port=1521/tcp --permanent

# 또는 한 번에
sudo firewall-cmd --add-port={8080/tcp,3306/tcp,1521/tcp} --permanent

# 변경 사항 활성화
sudo firewall-cmd --reload

# 확인
firewall-cmd --list-ports
```

`--permanent` 없이 실행하면 규칙이 현재 세션에만 적용되고, 서버 재시작 시 사라진다.

## 이 단계에서 중요한 판단 기준

운영 서버에서 포트를 열 때는 반드시 `--permanent`와 `--reload`를 세트로 사용하고, 테스트 후 불필요한 포트는 `--remove-port`로 즉시 닫는다.

## 한 줄 요약 — 이것만 기억하면 된다

**--permanent로 포트를 열고, --reload로 적용하고, --list-ports로 확인한다.**

## 나중에 더 깊게 들어가면

- Firewalld의 zone 개념과 인터페이스별 정책 설정
- 서비스(service) 단위로 포트 묶음을 관리하는 방법
- rich rule을 이용한 IP 대역 기반 접근 제어

---

**원본:** [Firewalld에서 포트 구성 방법 — https://memoryhub.tistory.com/282](https://memoryhub.tistory.com/282)
