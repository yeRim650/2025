
# 로드밸런서 실무 지식 정리

## 1. 로드밸런서란?

로드밸런서는 클라이언트의 요청을 여러 서버로 분산시켜 시스템의 부하를 줄이고, 고가용성과 확장성을 보장하는 컴포넌트이다.

### 주요 목적

* 트래픽 분산
* 서버 장애 시 대체 처리
* 확장성 향상
* 보안, 암호화 오프로딩

---

## 2. 로드밸런서의 유형

| 계층     | 설명                             | 예시                      |
| ------ | ------------------------------ | ----------------------- |
| L4     | TCP/UDP 기반 분산                  | AWS NLB, NGINX TCP mode |
| L7     | HTTP/HTTPS 기반 분산, URI/헤더 분석 가능 | AWS ALB, NGINX, HAProxy |
| DNS 기반 | DNS 레벨에서 분산                    | Route53, Cloudflare     |

---

## 3. 분산 방식

| 분산 방식                | 설명                   |
| -------------------- | -------------------- |
| Round Robin          | 순차적으로 서버에 분산         |
| Least Connection     | 현재 연결 수가 가장 적은 서버 선택 |
| IP Hash              | 클라이언트 IP 기반으로 서버 선택  |
| Weighted Round Robin | 서버에 가중치를 부여하여 분산     |
| Sticky Session       | 클라이언트 세션을 특정 서버에 고정  |

---

## 4. 실무 예시: NGINX 기반

```nginx
upstream backend_servers {
    server 192.168.0.1;
    server 192.168.0.2;
}

server {
    listen 80;

    location / {
        proxy_pass http://backend_servers;
    }
}
```

---

## 5. AWS 로드밸런서 예시

* Application Load Balancer (ALB): L7, HTTP/HTTPS 기반
* Network Load Balancer (NLB): L4, 고성능 TCP 처리
* Classic Load Balancer (Deprecated): 과거 사용 방식

설정 요소:

* Target Group
* Listener Rule
* SSL 인증서
* 헬스 체크 URL

---

## 6. 헬스 체크 구성

### 목적

* 비정상 서버로 트래픽 전송 방지

### 구현 방식

* HTTP 응답 코드 확인
* TCP 포트 응답
* NGINX: `proxy_next_upstream`, `health_check`

---

## 7. Kubernetes 환경에서의 로드밸런싱

### 7.1 Service 유형에 따른 분산

| Service 타입   | 설명                                |
| ------------ | --------------------------------- |
| ClusterIP    | 클러스터 내부 통신용                       |
| NodePort     | 외부에서 특정 노드 포트를 통해 접근              |
| LoadBalancer | 클라우드 Provider의 L4/L7 로드밸런서를 자동 생성 |
| ExternalName | 외부 DNS 이름으로 라우팅                   |

### 예시: LoadBalancer 타입

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: LoadBalancer
  selector:
    app: my-app
  ports:
    - port: 80
      targetPort: 8080
```

---

## 8. Kubernetes Ingress Controller

### Ingress란?

Ingress는 L7 기반의 HTTP/HTTPS 라우팅 규칙을 정의하고 외부 트래픽을 서비스로 전달하는 리소스.

### 구성 요소

* **Ingress Resource**: 경로 및 호스트 기반 라우팅 정의
* **Ingress Controller**: 실제 트래픽을 처리하는 엔진 (ex: NGINX, Traefik, HAProxy)

### 예시: NGINX Ingress Resource

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
    - host: example.com
      http:
        paths:
          - path: /web
            pathType: Prefix
            backend:
              service:
                name: web-service
                port:
                  number: 80
```

### 실무 고려 사항

* SSL Termination (Ingress에 인증서 적용)
* Rate Limiting, Header-based Routing
* Path rewriting

---

## 9. Istio 기반 로드밸런싱

### Istio란?

Istio는 서비스 메시로, 트래픽 제어, 보안, 관찰 가능성 기능을 제공하며, 사이드카 프록시(Envoy)를 통해 트래픽을 제어한다.

### 주요 리소스

| 리소스             | 역할                           |
| --------------- | ---------------------------- |
| VirtualService  | L7 라우팅 규칙 정의                 |
| DestinationRule | 백엔드 서비스의 정책 (Subsets, LB 전략) |
| Gateway         | 외부 트래픽의 진입 지점 정의             |

### 예시: VirtualService

```yaml
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: my-app
spec:
  hosts:
    - myapp.example.com
  gateways:
    - my-gateway
  http:
    - route:
        - destination:
            host: my-app
            subset: v1
          weight: 80
        - destination:
            host: my-app
            subset: v2
          weight: 20
```

### 예시: DestinationRule

```yaml
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: my-app
spec:
  host: my-app
  subsets:
    - name: v1
      labels:
        version: v1
    - name: v2
      labels:
        version: v2
  trafficPolicy:
    loadBalancer:
      simple: LEAST_CONN
```

### 실무 고려사항

* Canary, Blue/Green 배포 지원
* A/B 테스트
* mTLS, 인증 정책
* 트래픽 미러링 (Shadowing)
* 로깅/모니터링 (Prometheus, Jaeger)

---

## 10. 모니터링 및 로깅

| 환경         | 툴                            |
| ---------- | ---------------------------- |
| NGINX      | access.log, error.log        |
| Kubernetes | Prometheus, Grafana, Loki    |
| AWS        | CloudWatch, X-Ray            |
| Istio      | Envoy metrics, Kiali, Jaeger |

---

## 11. 실무 설계 팁

* Stateless 아키텍처를 기본으로 하되, 필요 시 Sticky Session 구성
* 내부 통신은 ServiceMesh, 외부는 Ingress → LB로 라우팅
* HTTPS는 LB 또는 Ingress Controller에서 종료
* 오토스케일링과 LB 동기화 필수
* 장애 대응 위해 로깅, 알림, 대시보드 필수

---

## 12. 참고 링크

* [NGINX Docs](https://nginx.org/en/docs/)
* [Kubernetes Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)
* [Istio Documentation](https://istio.io/latest/docs/)
* [AWS ELB Docs](https://docs.aws.amazon.com/elasticloadbalancing/)

---
