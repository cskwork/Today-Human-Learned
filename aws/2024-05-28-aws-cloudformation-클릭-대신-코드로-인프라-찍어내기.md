# AWS CloudFormation — 클릭 대신 코드로 인프라를 찍어내는 방법

> **TL;DR**
> CloudFormation은 AWS 인프라를 YAML/JSON 파일 하나로 선언하고, AWS가 그 파일을 읽어 자원을 자동으로 생성·관리하는 IaC 서비스다.

---

## CloudFormation을 왜 쓰는지 감 잡기

서버 한 대를 새 환경에 구성하려면 EC2, VPC, 보안 그룹, IAM 역할을 AWS 콘솔에서 하나씩 클릭해야 한다. 개발·스테이징·운영 환경을 동일하게 맞추려 할 때마다 이 과정을 반복하고, 누군가 설정을 바꿔도 무엇이 변했는지 추적하기 어렵다.

CloudFormation은 이 문제를 IaC(Infrastructure as Code) 방식으로 해결한다. 인프라 구성을 코드 파일(템플릿)로 작성해두면, CloudFormation이 해당 파일을 읽어 리소스 간 의존성을 파악하고 순서에 맞게 자동으로 생성한다. 파일을 Git에 올려두면 변경 이력도 자동으로 남는다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 템플릿 파일 작성 → CloudFormation에 업로드 → 스택 생성 → AWS 리소스 자동 프로비저닝`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| IaC (Infrastructure as Code) | 서버·네트워크 같은 인프라를 코드로 정의하고 관리하는 방법론 |
| 템플릿 (Template) | 어떤 AWS 리소스를 만들지 적어놓은 설계도. YAML 또는 JSON 형식 |
| 스택 (Stack) | 템플릿 하나로 생성된 AWS 리소스들의 묶음. 스택 단위로 생성·업데이트·삭제 |
| 파라미터 (Parameters) | 템플릿을 재사용할 때 외부에서 입력받는 값 (예: 버킷 이름, 인스턴스 타입) |
| 변경 세트 (Change Set) | 스택을 업데이트하기 전에 "무엇이 어떻게 바뀌는지" 미리 확인하는 기능 |

## 예를 들어 설명하면

S3 버킷 하나를 코드로 만드는 최소 템플릿이다.

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: S3 버킷 생성 예제

Parameters:
  BucketName:
    Type: String
    Description: 생성할 버킷 이름

Resources:
  MyBucket:
    Type: 'AWS::S3::Bucket'
    Properties:
      BucketName: !Ref BucketName

Outputs:
  BucketArn:
    Value: !GetAtt MyBucket.Arn
```

이 파일을 CloudFormation에 올리고 `BucketName` 값을 입력하면, AWS가 S3 버킷을 자동으로 만든다. 스택을 삭제하면 버킷도 함께 삭제된다. 콘솔을 직접 조작할 필요가 없다.

## 이 단계에서 중요한 판단 기준

CloudFormation 도입을 고려할 때 가장 먼저 물어야 할 질문은 "같은 인프라 구성을 두 번 이상 반복해야 하는가?"이다. 반복이 있다면 템플릿화하는 것이 수동 작업보다 항상 안전하고 빠르다.

## 한 줄 요약 — 이것만 기억하면 된다

**CloudFormation은 AWS 인프라를 코드(YAML/JSON 템플릿)로 선언하면 자동으로 생성·관리해주는 서비스로, 반복 구성과 환경 일관성 문제를 해결한다.**

## 나중에 더 깊게 들어가면

- 네스티드 스택: 대규모 인프라를 여러 템플릿으로 나눠 모듈화하는 방법
- 드리프트 탐지: 템플릿과 실제 리소스 구성이 달라졌을 때 감지하는 기능
- Terraform / AWS CDK: CloudFormation의 대안 IaC 도구와 비교

---

**원본:** [AWS CloudFormation, 클릭 대신 코드로 인프라 찍어내기? — https://memoryhub.tistory.com/115](https://memoryhub.tistory.com/115)
