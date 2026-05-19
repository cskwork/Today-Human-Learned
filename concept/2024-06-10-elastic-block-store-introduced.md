# AWS EBS — EC2 인스턴스에 붙이는 영구 블록 스토리지

> **TL;DR**
> Amazon EBS는 EC2 인스턴스에 연결해 쓰는 가상 하드드라이브로, 인스턴스가 재시작되어도 데이터가 유지된다.

---

## EBS를 왜 쓰는지 감 잡기

EC2 인스턴스 자체에 내장된 스토리지(인스턴스 스토어)는 인스턴스를 종료하면 데이터가 사라진다. 데이터베이스나 파일 시스템처럼 인스턴스 재시작 후에도 데이터를 유지해야 하는 경우에는 별도의 영구 스토리지가 필요하다.

EBS(Elastic Block Store)는 이 문제를 해결하는 AWS의 블록 스토리지 서비스다. 노트북에 외장 SSD를 연결하듯, EC2 인스턴스에 EBS 볼륨을 붙여서 사용한다. 인스턴스를 종료해도 EBS 볼륨과 그 안의 데이터는 그대로 남는다. 다른 인스턴스에 다시 붙여서 이어 쓸 수 있다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: EBS 볼륨 생성 → EC2에 연결(attach) → 마운트 → 파일 시스템으로 사용`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 블록 스토리지(Block Storage) | 데이터를 고정 크기의 블록 단위로 저장하는 방식. 읽기/쓰기 속도가 빠르다. |
| EBS 볼륨(Volume) | 실제로 생성해서 EC2에 연결하는 스토리지 단위. 크기와 유형을 선택할 수 있다. |
| 스냅샷(Snapshot) | 특정 시점의 볼륨 상태를 S3에 백업해두는 것. 복구나 볼륨 복제에 활용한다. |
| 가용 영역(Availability Zone) | AWS 데이터센터 단위. EBS 볼륨은 같은 가용 영역의 EC2에만 연결할 수 있다. |
| gp3 / io2 | EBS 볼륨 유형. gp3는 범용 SSD, io2는 고성능 I/O가 필요한 워크로드용이다. |

## 예를 들어 설명하면

AWS CLI로 EBS 볼륨을 만들고 EC2에 연결하는 흐름이다.

```bash
# 1. 10GB gp2 볼륨 생성
aws ec2 create-volume --size 10 --region us-east-1 \
  --availability-zone us-east-1a --volume-type gp2

# 2. EC2 인스턴스에 연결
aws ec2 attach-volume \
  --volume-id vol-0123456789abcdef0 \
  --instance-id i-1234567890abcdef0 \
  --device /dev/sdf

# 3. EC2에 SSH 접속 후 파일 시스템 포맷 및 마운트
sudo mkfs -t ext4 /dev/xvdf
sudo mkdir /data
sudo mount /dev/xvdf /data
```

이후 `/data` 디렉토리에 파일을 쓰면 EBS 볼륨에 저장된다. 인스턴스를 재시작해도 데이터는 유지된다.

## 이 단계에서 중요한 판단 기준

EBS 볼륨은 같은 가용 영역의 인스턴스에만 붙일 수 있으므로, 볼륨 생성 시 EC2 인스턴스와 동일한 가용 영역을 반드시 지정한다.

## 한 줄 요약 — 이것만 기억하면 된다

**EBS는 EC2에 붙이는 가상 하드드라이브로, 인스턴스 생애와 무관하게 데이터를 영구 보존한다.**

## 나중에 더 깊게 들어가면

- EBS 볼륨 유형 비교: gp3, io2, st1, sc1의 성능과 비용 차이
- 스냅샷으로 다른 리전에 데이터를 복제하는 방법
- 블록 스토리지 vs 오브젝트 스토리지(S3) vs 파일 스토리지(EFS) 선택 기준

---

**원본:** [Elastic Block Store Introduced — https://memoryhub.tistory.com/247](https://memoryhub.tistory.com/247)
