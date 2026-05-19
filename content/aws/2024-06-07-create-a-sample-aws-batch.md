+++
title = "AWS Batch 실습 — Python 스크립트를 컨테이너로 실행하기"
date = "2024-06-07"
description = "Python 스크립트를 Docker 이미지로 만들어 ECR에 올리고, AWS Batch Job Definition에 등록하면 대규모 배치 작업을 자동으로 실행할 수 있다."
tags = ["aws"]
categories = ["aws"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Python 스크립트를 Docker 이미지로 만들어 ECR에 올리고, AWS Batch Job Definition에 등록하면 대규모 배치 작업을 자동으로 실행할 수 있다.

---

## 이 실습이 왜 필요한지 감 잡기

AWS Batch 개념을 알더라도 "내 코드를 어떻게 실행시키지?"라는 질문이 남는다. Batch는 Docker 컨테이너 단위로 작업을 실행한다. 따라서 코드 작성 → Dockerfile 작성 → 이미지 빌드 → ECR 업로드 → Job Definition 등록 → Job 제출의 순서를 직접 경험해야 실제 활용이 가능하다.

초보자는 처음에 이렇게 이해하면 된다.

`Python 스크립트 → Dockerfile → Docker 이미지 → ECR → Job Definition → Job 제출`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| ECR (Elastic Container Registry) | AWS가 제공하는 Docker 이미지 저장소. Docker Hub와 같은 역할을 AWS 안에서 한다. |
| Dockerfile | 컨테이너 이미지를 만드는 설계 파일. 어떤 베이스 이미지를 쓰고, 어떤 파일을 넣고, 어떤 명령을 실행할지 기술한다. |
| Job Definition | Batch가 컨테이너를 실행할 때 참조하는 설정. 이미지 주소, CPU, 메모리, 실행 명령이 포함된다. |
| vCPUs / memory | Job 하나에 할당할 가상 CPU 코어 수와 메모리(MB). 작업 규모에 맞게 설정한다. |
| job-status | Batch Job의 현재 상태. SUBMITTED → PENDING → RUNNABLE → STARTING → RUNNING → SUCCEEDED/FAILED 순으로 전이된다. |

## 예를 들어 설명하면

데이터 처리 Python 스크립트를 Batch로 실행하는 전체 과정이다.

**process_data.py**
```python
import sys

def process_data():
    print("데이터 처리 시작")
    print("전달된 인자:", sys.argv[1:])

if __name__ == "__main__":
    process_data()
```

**Dockerfile**
```dockerfile
FROM python:3.8-slim
COPY process_data.py /app/process_data.py
WORKDIR /app
CMD ["python", "process_data.py"]
```

**이미지 빌드 및 ECR 업로드**
```bash
docker build -t mybatchjob:latest .

# ECR 태그 및 푸시 (ACCOUNT_ID, REGION을 실제 값으로 교체)
docker tag mybatchjob:latest ACCOUNT_ID.dkr.ecr.REGION.amazonaws.com/mybatchjob:latest
aws ecr get-login-password --region REGION | docker login --username AWS \
    --password-stdin ACCOUNT_ID.dkr.ecr.REGION.amazonaws.com
docker push ACCOUNT_ID.dkr.ecr.REGION.amazonaws.com/mybatchjob:latest
```

**Job Definition 등록 및 Job 제출**
```bash
aws batch register-job-definition \
    --job-definition-name MyJobDef \
    --type container \
    --container-properties \
    image=ACCOUNT_ID.dkr.ecr.REGION.amazonaws.com/mybatchjob:latest,\
vcpus=2,memory=2000,command='["python","process_data.py","arg1","arg2"]'

aws batch submit-job \
    --job-name MyFirstBatchJob \
    --job-queue MyQueue \
    --job-definition MyJobDef

# 실행 중인 Job 목록 확인
aws batch list-jobs --job-queue MyQueue --job-status RUNNING
```

## 이 단계에서 중요한 판단 기준

Job이 RUNNABLE 상태에서 오래 머문다면 Compute Environment의 maxvCpus가 부족하거나 서브넷, IAM 권한 설정을 먼저 점검하라.

## 한 줄 요약 — 이것만 기억하면 된다

**AWS Batch 실습의 핵심은 코드를 Docker 이미지로 패키징해 ECR에 올리고, Job Definition으로 등록한 뒤 제출하는 흐름이다.**

## 나중에 더 깊게 들어가면

- Job 의존성 설정 — 선행 Job 완료 후 후속 Job을 자동으로 트리거하기
- S3 연동 패턴 — 입력 파일을 S3에서 읽고 결과를 S3에 쓰는 구조
- Lambda로 Job 자동 제출 — 스케줄 기반 또는 이벤트 기반 Batch 트리거

---

**원본:** [Create a sample AWS Batch — https://memoryhub.tistory.com/212](https://memoryhub.tistory.com/212)
