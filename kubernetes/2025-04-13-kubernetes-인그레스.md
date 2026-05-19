# Kubernetes 인그레스

> **TL;DR**
> 인그레스는 클러스터 외부에서 들어오는 HTTP/HTTPS 요청을 하나의 진입점에서 받아 내부 서비스로 경로·호스트 기반으로 분배하는 쿠버네티스 오브젝트다.

---

## 인그레스를 왜 쓰는지 감 잡기

서비스가 여러 개일 때 NodePort는 노드마다 포트를 열어야 하고, LoadBalancer는 서비스마다 클라우드 로드밸런서를 하나씩 만들어 비용이 쌓인다. 둘 다 L4(전송 계층)에서 동작하기 때문에 "같은 도메인에서 `/api`는 A 서비스, `/web`은 B 서비스로"처럼 경로 단위 라우팅이 불가능하다.

인그레스는 이 문제를 L7(애플리케이션 계층)에서 해결한다. 외부에 IP 하나만 노출하고, 들어온 요청의 호스트명과 경로를 보고 적절한 서비스로 보낸다. SSL/TLS 처리도 인그레스에서 한 번에 담당하므로 각 서비스는 암호화 부담 없이 내부 통신에만 집중하면 된다.

`핵심 흐름: 외부 요청 → 인그레스 컨트롤러 → 인그레스 규칙 확인 → 내부 서비스`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 인그레스 리소스 | "이 URL이 오면 저 서비스로 보내라"는 라우팅 규칙을 담은 YAML 오브젝트 |
| 인그레스 컨트롤러 | 규칙을 실제로 읽어서 트래픽을 라우팅하는 프록시 프로세스 (별도 설치 필요) |
| L7 라우팅 | 요청 URL의 호스트명과 경로를 보고 목적지를 결정하는 방식 |
| SSL/TLS 종료 | HTTPS 복호화를 인그레스 컨트롤러에서 처리하고, 내부는 HTTP로 통신하는 구조 |
| IngressClass | 클러스터에 컨트롤러가 여럿일 때 이 규칙을 어느 컨트롤러가 담당할지 지정하는 필드 |

## 예를 들어 설명하면

`api.example.com`이라는 도메인 하나로 로그인과 상품 서비스를 동시에 운영하는 경우다.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: api-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: api.example.com
    http:
      paths:
      - path: /login
        pathType: Prefix
        backend:
          service:
            name: login-service
            port:
              number: 8080
      - path: /products
        pathType: Prefix
        backend:
          service:
            name: product-service
            port:
              number: 80
```

`api.example.com/login` 요청은 `login-service:8080`으로, `api.example.com/products`는 `product-service:80`으로 전달된다. 외부에 노출된 IP는 인그레스 컨트롤러 하나뿐이다.

## 이 단계에서 중요한 판단 기준

인그레스 리소스를 아무리 잘 작성해도 클러스터에 인그레스 컨트롤러가 설치되어 있지 않으면 아무 일도 일어나지 않는다.

## 한 줄 요약 — 이것만 기억하면 된다

**인그레스는 규칙(리소스)과 실행 주체(컨트롤러)가 분리되어 있으며, 컨트롤러 없이는 규칙이 적용되지 않는다.**

## 나중에 더 깊게 들어가면

- cert-manager를 이용한 Let's Encrypt 인증서 자동 발급 및 갱신
- 어노테이션으로 제어하는 Nginx 고급 기능 (rewrite, rate-limit, 인증)
- 멀티 컨트롤러 환경에서 IngressClass 전략

---

**원본:** [Kubernetes 인그레스 — https://memoryhub.tistory.com/547](https://memoryhub.tistory.com/547)
