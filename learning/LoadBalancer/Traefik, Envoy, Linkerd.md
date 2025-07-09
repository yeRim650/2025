
## 13. Traefik, Envoy, Linkerd 같은 대안들

Kubernetes 환경에서는 Ingress Controller 또는 서비스 메시로 로드밸런싱과 트래픽 제어를 구현할 수 있다. Istio 외에도 다음과 같은 대안들이 존재한다.

---

### 13.1 Traefik

#### 개요

Traefik은 동적 구성, 자동 서비스 디스커버리, Let's Encrypt 통합 등의 기능을 제공하는 경량 Ingress Controller 겸 Edge Router.

#### 특징

* Kubernetes, Docker 등 다양한 백엔드 자동 탐색
* HTTP/HTTPS, TCP, UDP 지원
* Let's Encrypt 자동 인증서 발급
* CRD 기반 구성 (`IngressRoute`, `Middleware` 등)
* 대시보드 및 Prometheus 내장 지원

#### 예시 구성 (IngressRoute)

```yaml
apiVersion: traefik.containo.us/v1alpha1
kind: IngressRoute
metadata:
  name: example
spec:
  entryPoints:
    - web
  routes:
    - match: Host(`example.com`)
      kind: Rule
      services:
        - name: my-service
          port: 80
```

#### 실무 사용 시 고려

* 간편한 설치와 구성
* 인증서, HTTP-to-HTTPS redirect 자동화
* 유연한 Middleware 구성 가능 (rate limit, auth 등)

---

### 13.2 Envoy

#### 개요

Envoy는 Lyft에서 개발한 고성능 L7 프록시로, 서비스 메시의 프록시 엔진으로 널리 사용됨 (예: Istio, AWS App Mesh).

#### 특징

* L3 \~ L7 트래픽 제어 지원
* 고성능, 멀티스레딩 구조
* 동적 재구성 가능 (xDS API)
* gRPC 및 HTTP/2 지원
* Sidecar 또는 Gateway로 모두 사용 가능

#### 예시: Static Config (Standalone 사용)

```yaml
static_resources:
  listeners:
    - name: listener_0
      address:
        socket_address: { address: 0.0.0.0, port_value: 8080 }
      filter_chains:
        - filters:
            - name: envoy.filters.network.http_connection_manager
              typed_config:
                "@type": type.googleapis.com/envoy.config.filter.network.http_connection_manager.v2.HttpConnectionManager
                stat_prefix: ingress_http
                route_config:
                  name: local_route
                  virtual_hosts:
                    - name: backend
                      domains: ["*"]
                      routes:
                        - match: { prefix: "/" }
                          route: { cluster: backend_service }
                http_filters:
                  - name: envoy.filters.http.router
  clusters:
    - name: backend_service
      connect_timeout: 0.25s
      type: LOGICAL_DNS
      lb_policy: ROUND_ROBIN
      load_assignment:
        cluster_name: backend_service
        endpoints:
          - lb_endpoints:
              - endpoint:
                  address:
                    socket_address: { address: backend, port_value: 80 }
```

#### 실무 사용 시 고려

* 대규모 서비스 메시 또는 고성능 프록시 필요 시 유리
* Istio, AWS App Mesh 등과의 통합이 많음
* 단독 사용은 설정이 복잡할 수 있음

---

### 13.3 Linkerd

#### 개요

Linkerd는 Kubernetes 친화적인 경량 서비스 메시. 간단한 구성과 낮은 리소스 사용량을 장점으로 한다.

#### 특징

* 기본적인 트래픽 라우팅, TLS, 리트라이, 시간제한 기능 제공
* 데이터 평면은 Rust 기반 프록시 사용 (linkerd2-proxy)
* 설정이 단순하고 배포가 쉬움
* 대시보드와 Grafana 통합 가능
* Istio 대비 가볍고 단순함

#### 주요 리소스 예시

```yaml
# 서비스에 자동 프록시 주입 (CLI 또는 annotation 사용)
kubectl annotate deploy my-app linkerd.io/inject=enabled

# HTTP 라우팅 정책 예시 (ServiceProfile CRD 사용)
apiVersion: linkerd.io/v1alpha2
kind: ServiceProfile
metadata:
  name: my-app.default.svc.cluster.local
  namespace: default
spec:
  routes:
    - name: GET /
      condition:
        method: GET
        pathRegex: /
      isRetryable: true
```

#### 실무 사용 시 고려

* Istio보다 가벼우면서도 기본적인 서비스 메시 기능 제공
* MTLS, 자동 리트라이, 라우팅 정책 등 핵심 기능 제공
* 관측(Observability) 및 배포가 쉽고 빠름

---

## 14. 대안별 요약 비교

| 항목     | Traefik                              | Envoy                 | Linkerd         |
| ------ | ------------------------------------ | --------------------- | --------------- |
| 목적     | Ingress Controller, Edge Router      | 고성능 프록시, 서비스 메시 엔진    | 경량 서비스 메시       |
| 복잡도    | 낮음                                   | 높음                    | 낮음              |
| 사용 방식  | Standalone 또는 K8s Ingress Controller | Standalone or Sidecar | Sidecar (자동 주입) |
| TLS 지원 | 자동 (Let's Encrypt 통합)                | 수동 구성                 | 자동 (mTLS 기본 제공) |
| 주요 장점  | 간단한 구성, 자동 인증서                       | 강력한 성능, 고유연성          | 경량, 쉬운 배포       |
| 실무 적합  | 중소규모 Ingress                         | 대규모 서비스 메시            | 단순한 메시 요구에 적합   |

---

## 15. 결론 및 선택 가이드

* **간단하고 빠르게 Ingress 설정이 필요하다면** → Traefik
* **고성능 트래픽 처리, 세밀한 제어가 필요하다면** → Envoy
* **가볍고 단순한 메시 구성만 필요하다면** → Linkerd
* **기능이 풍부하고 산업 표준을 따르고 싶다면** → Istio

---
