# AWS Batch — 대량 작업을 자동으로 스케줄링하는 서비스

> **TL;DR**
> AWS Batch는 대규모 배치 작업을 제출하면 컴퓨팅 자원 할당과 실행 순서를 자동으로 관리해 주는 서비스다. 직접 EC2를 켜고 끌 필요가 없다.

---

## AWS Batch를 왜 쓰는지 감 잡기

매일 새벽 수만 건의 데이터를 처리해야 한다고 가정하자. EC2를 직접 띄워 스크립트를 돌리면 작업이 끝나도 서버가 계속 켜져 있어 비용이 낭비된다. 반대로 서버를 끄고 켜는 작업을 직접 관리하면 운영 부담이 크다.

AWS Batch는 이 문제를 해결한다. 작업(Job)을 제출하면 필요한 만큼 컴퓨팅 자원을 자동으로 늘렸다가, 작업이 끝나면 줄인다. 각 작업은 Docker 컨테이너 안에서 실행되므로 환경이 일관되게 유지된다.

초보자는 처음에 이렇게 이해하면 된다.

`Job 제출 → Job Queue 대기 → Compute Environment 자원 할당 → Job 실행 → 완료`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Compute Environment | 작업이 실제로 실행되는 EC2 인스턴스 풀. managed 타입이면 AWS가 자원을 자동으로 늘리고 줄인다. |
| Job Queue | 제출된 작업이 실행을 기다리는 대기열. 우선순위를 설정할 수 있다. |
| Job Definition | 컨테이너 이미지, CPU, 메모리, 실행 명령을 정의한 작업 설계서. 한 번 등록하면 재사용한다. |
| Job | 실제로 제출되는 작업 단위. Job Definition을 기반으로 실행된다. |
| allocationStrategy | Compute Environment가 EC2 인스턴스를 선택하는 방식. BEST_FIT은 비용 최적화, BEST_FIT_PROGRESSIVE는 가용성 우선. |

## 예를 들어 설명하면

CLI로 Batch 작업을 설정하는 최소 흐름이다.

```bash
# 1. Compute Environment 생성
aws batch create-compute-environment \
    --compute-environment-name MyEnv \
    --type MANAGED \
    --compute-resources type=EC2,allocationStrategy=BEST_FIT,\
instanceTypes=m4.large,minvCpus=0,maxvCpus=16,\
subnets=subnet-xxxx,securityGroupIds=sg-xxxx,instanceRole=ecsInstanceRole

# 2. Job Queue 생성
aws batch create-job-queue \
    --job-queue-name MyQueue \
    --state ENABLED \
    --priority 1 \
    --compute-environment-order order=1,computeEnvironment=MyEnv

# 3. Job Definition 등록
aws batch register-job-definition \
    --job-definition-name MyJobDef \
    --type container \
    --container-properties image=amazonlinux,vcpus=2,memory=2000,command='["echo","hello"]'

# 4. Job 제출
aws batch submit-job \
    --job-name MyJob \
    --job-queue MyQueue \
    --job-definition MyJobDef
```

## 이 단계에서 중요한 판단 기준

Batch 적용 여부를 판단할 때는 "이 작업이 특정 시점에 대량으로 실행되고, 실시간 응답이 필요 없는가?"를 먼저 따져라 — 맞다면 Batch가 Lambda나 상시 실행 EC2보다 비용과 운영 부담 면에서 유리하다.

## 한 줄 요약 — 이것만 기억하면 된다

**AWS Batch는 작업 제출만 하면 자원 할당과 스케줄링을 자동으로 처리하는 배치 컴퓨팅 서비스다.**

## 나중에 더 깊게 들어가면

- Job 의존성(dependencies) — 선행 Job이 완료된 후 후속 Job을 실행하는 방법
- Spot 인스턴스 활용 — 비용을 더 줄이는 전략과 재시도 설정
- S3 연동 — 입력 데이터를 S3에서 읽고 결과를 S3에 저장하는 패턴

---

**원본:** [AWS Batch Introduced — https://memoryhub.tistory.com/211](https://memoryhub.tistory.com/211)
