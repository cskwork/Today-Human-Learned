# OpenStack Cinder — 클라우드 VM에 붙이는 가상 하드디스크

> **TL;DR**
> Cinder는 OpenStack에서 VM에 연결하는 영구 블록 스토리지를 제공한다. VM을 꺼도 데이터가 사라지지 않는다.

---

## Cinder를 왜 쓰는지 감 잡기

클라우드 VM의 기본 디스크는 VM이 삭제되면 데이터도 함께 사라진다. 데이터베이스나 파일 서버처럼 VM 수명과 무관하게 데이터를 유지해야 하는 경우에는 별도의 영구 스토리지가 필요하다.

Cinder는 물리 디스크를 직접 다루지 않고도 소프트웨어로 정의된 블록 볼륨을 만들고, VM에 연결하고, 스냅샷을 찍고, 백업하는 기능을 API 하나로 제공한다.

초보자는 처음에 이렇게 이해하면 된다.

`볼륨 생성 → VM에 연결 → 마운트해서 사용 → VM 삭제 후에도 볼륨은 유지`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 볼륨(Volume) | Cinder가 만드는 가상 블록 디스크. VM에 연결해서 파일시스템처럼 쓴다. |
| cinder-api | 사용자 요청을 받아 처리하는 진입점. REST API를 제공한다. |
| cinder-scheduler | 볼륨을 어떤 스토리지 백엔드에 만들지 결정하는 스케줄러. |
| cinder-volume | 실제 스토리지와 통신해서 볼륨을 만들고 관리하는 서비스. |
| 스냅샷(Snapshot) | 볼륨의 특정 시점 상태를 그대로 보존한 복사본. |

## 예를 들어 설명하면

볼륨을 만들고 VM에 연결하는 기본 흐름이다.

```bash
# 1GB 볼륨 생성
openstack volume create --size 1 my-new-volume

# VM에 볼륨 연결 (/dev/vdb 장치로 나타남)
openstack server add volume my-server my-new-volume --device /dev/vdb

# 스냅샷 생성 (현재 상태 보존)
openstack volume snapshot create --volume my-new-volume my-snapshot
```

볼륨은 생명주기를 따라 상태가 바뀐다.

| 상태 | 의미 |
|---|---|
| 생성 중 | 볼륨을 만드는 중 |
| 사용 가능 | VM에 연결할 준비 완료 |
| 사용 중 | VM에 연결되어 사용 중 |
| 분리 중 | VM에서 분리하는 중 |

## 이 단계에서 중요한 판단 기준

Cinder 볼륨은 한 번에 하나의 VM에만 연결할 수 있다. 여러 VM이 동시에 같은 데이터에 접근해야 한다면 공유 파일시스템 서비스인 Manila를 대신 고려한다.

## 한 줄 요약 — 이것만 기억하면 된다

**Cinder는 VM과 독립적으로 존재하는 영구 블록 스토리지를 API로 관리하게 해준다.**

## 나중에 더 깊게 들어가면

- 다중 백엔드 구성으로 성능/비용 프로필이 다른 스토리지 티어 운영
- LVM 드라이버 외 Ceph, NetApp 등 엔터프라이즈 백엔드 연동
- 증분 백업과 QoS 프로필을 활용한 워크로드별 성능 보장

---

**원본:** [OpenStack Cinder 블록 스토리지 서비스 — https://memoryhub.tistory.com/420](https://memoryhub.tistory.com/420)
