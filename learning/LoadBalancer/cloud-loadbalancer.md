# 클라우드별 로드밸런서 비교 및 실무 활용 가이드

## 1. 개요

실무 환경에서 로드밸런서는 고가용성과 확장성을 위한 핵심 인프라입니다. 이 문서에서는 Naver Cloud Platform(NCP)을 포함한 주요 클라우드 벤더들의 로드밸런서 서비스를 비교하고, 각각의 특징과 실무에서의 사용 포인트를 정리합니다.

---

## 2. 주요 클라우드별 로드밸런서 서비스

| 클라우드 | 서비스명 | 계층 | 특징 |
|----------|----------|------|------|
| AWS | ELB (ALB, NLB, CLB) | L4/L7 | 글로벌, 정교한 라우팅, 다양한 배포 전략 지원 |
| GCP | Cloud Load Balancing | L4/L7 | 글로벌 HTTP(S) LB, 멀티 리전 라우팅 지원 |
| Azure | Azure Load Balancer / Application Gateway | L4/L7 | 통합 WAF, SSL 종료 지원 |
| NCP | Load Balancer | L4/L7 | 한국 최적화, 콘솔 UI 친화적, 간편한 설정 |
| KT Cloud | Load Balancer | L4/L7 | 국내 최적화, UI는 단순한 편 |
| NHN Cloud | Load Balancer | L4/L7 | 가격 경쟁력 있고 구성 쉬움 |
| DigitalOcean | Load Balancer | L4/L7 단순 | 소규모 프로젝트 적합, 간편한 API |
| Linode / Vultr | NodeBalancer / LB | 주로 L4 | 저렴하고 간단한 구조 |

---

## 3. 주요 기능 비교

| 기능 | AWS | GCP | Azure | NCP | KT/NHN | DigitalOcean |
|------|-----|-----|--------|-----|--------|---------------|
| L7 라우팅 | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| L4 라우팅 | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| SSL 종료 | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| 헬스 체크 | 고급 | 고급 | 고급 | 기본 제공 | 기본 | 단순 |
| WAF 통합 | ✅ | ✅ (Cloud Armor) | ✅ | 별도 서비스 필요 | 별도 필요 | ❌ |
| Auto Scaling 연동 | ✅ | ✅ | ✅ | ✅ | 일부 제한 | ❌ |
| 콘솔 사용성 | 우수 | 좋음 | 복잡 | 직관적 | 보통 | 간단 |
| 한국 내 latency | 중간 | 중간 | 중간 | **매우 우수** | **우수** | 낮음 |

---

## 4. 실무에서의 선택 기준

| 목적/상황 | 추천 플랫폼 |
|------------|--------------|
| **한국 사용자 대상 서비스** | NCP, KT Cloud, NHN Cloud |
| **글로벌 트래픽 및 복잡한 라우팅** | AWS ALB, GCP LB |
| **보안 및 정책 중심 기업 환경** | Azure Application Gateway |
| **스타트업, MVP, 테스트 용도** | DigitalOcean, Linode |
| **Kubernetes 기반 운영** | 클라우드 LB + Ingress Controller 조합 (예: AWS ALB + NGINX) |

---

## 5. NCP와 타 벤더 비교 요약

- **NCP**는 **한국 내 서비스 최적화**, 콘솔 UI 편의성, 로드밸런서 설정의 간편함이 장점.
- **AWS/GCP**는 글로벌 리전과 멀티존 설정, 다양한 트래픽 제어 기능을 제공.
- **Azure**는 보안 및 엔터프라이즈 연계에 강점.
- **DigitalOcean**은 소규모 프로젝트에 적합.

---

## 6. 실무 팁

- 헬스 체크 URL은 반드시 `/healthz`처럼 경량 경로로 구성
- SSL 종료는 LB에서 하고, 백엔드 통신은 HTTP로 간소화 가능
- 로그/모니터링은 LB와 연계해 CloudWatch, Stackdriver, NCP Monitoring 연동
- 인프라 자동화 시 LB 자원도 IaC(Terraform, Pulumi 등)로 관리 추천

---

## 7. 참고 링크

- [AWS Elastic Load Balancing](https://docs.aws.amazon.com/elasticloadbalancing/)
- [GCP Cloud Load Balancing](https://cloud.google.com/load-balancing)
- [Azure Application Gateway](https://learn.microsoft.com/en-us/azure/application-gateway/)
- [Naver Cloud Load Balancer](https://guide.ncloud-docs.com/docs/network-loadbalancer-overview)
