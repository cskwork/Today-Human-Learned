+++
title = "Amazon S3 — 인터넷 위의 무한 파일 창고"
date = "2024-11-17"
description = "S3는 파일을 버킷에 던져 넣으면 URL로 꺼낼 수 있는 AWS 객체 스토리지다. 서버 없이 무한 확장된다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> S3는 파일을 버킷에 던져 넣으면 URL로 꺼낼 수 있는 AWS 객체 스토리지다. 서버 없이 무한 확장된다.

---

## 이 주제를 왜 쓰는지 감 잡기

앱에서 이미지, 동영상, 로그 파일을 저장해야 할 때 서버 디스크에 쌓으면 디스크가 금방 찬다. 용량 확장은 번거롭고, 서버가 죽으면 파일도 사라진다. Amazon S3는 이 문제를 "저장은 AWS 인프라에, 접근은 URL이나 API로"라는 방식으로 해결한다. 직접 서버를 운영하지 않아도 되고, 용량 제한도 없다.

`핵심 흐름: 버킷 생성 → 객체 업로드 → URL 또는 SDK로 접근 → 라이프사이클 정책으로 비용 관리`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 버킷(Bucket) | 파일을 담는 최상위 컨테이너. 이름은 전 세계에서 유일해야 한다 |
| 객체(Object) | S3에 저장되는 파일 단위. 파일 내용 + 메타데이터로 구성된다 |
| 스토리지 클래스 | 접근 빈도에 따라 비용이 다른 등급. Standard(자주), IA(가끔), Glacier(거의 안 씀) |
| 라이프사이클 정책 | 일정 기간이 지나면 자동으로 저렴한 스토리지 클래스로 옮기거나 삭제하는 규칙 |
| IAM 정책 | 누가 어떤 버킷/객체에 접근할 수 있는지를 정의하는 AWS 권한 규칙 |

## 예를 들어 설명하면

AWS CLI로 버킷 생성부터 라이프사이클 설정까지 한 흐름으로 볼 수 있다.

```bash
# 버킷 생성 (리전: 서울)
aws s3 mb s3://my-app-bucket --region ap-northeast-2

# 파일 업로드
aws s3 cp ./photo.jpg s3://my-app-bucket/uploads/photo.jpg

# 30일 뒤 STANDARD_IA로 자동 전환하는 라이프사이클 정책
aws s3api put-bucket-lifecycle-configuration \
  --bucket my-app-bucket \
  --lifecycle-configuration '{
    "Rules": [{
      "ID": "move-to-ia",
      "Status": "Enabled",
      "Transitions": [{"Days": 30, "StorageClass": "STANDARD_IA"}]
    }]
  }'
```

30일 이후 자주 열리지 않는 파일은 자동으로 IA 클래스로 내려가면서 저장 비용이 줄어든다.

## 이 단계에서 중요한 판단 기준

버킷을 만들 때 리전을 잘못 고르면 사용자 쪽에서 지연(Latency)이 생기고 데이터 전송 비용도 커진다. 서비스 사용자가 주로 있는 지역과 가장 가까운 리전을 선택하라.

## 한 줄 요약 — 이것만 기억하면 된다

**S3는 버킷에 넣고 URL로 꺼내는 무한 파일 창고다. 라이프사이클 정책으로 비용을 통제한다.**

## 나중에 더 깊게 들어가면

- 버전 관리(Versioning): 같은 이름 파일을 덮어써도 이전 버전 복원 가능
- Pre-signed URL: 특정 시간 동안만 유효한 임시 다운로드 링크 생성
- S3 이벤트 알림: 파일 업로드 시 Lambda를 자동 실행하는 서버리스 파이프라인

---

**원본:** [Amazon S3(Simple Storage Service) 완벽 가이드](https://memoryhub.tistory.com/390)
