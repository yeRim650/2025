
## ✅ AWS 주요 서비스 카테고리 상세

아래 표는 핵심 서비스들을 **범주별로 정리**한 것입니다.

| 카테고리 | 대표 서비스 | 설명 |
| ---- | ------ | -- |

### 🖥️ **1. 컴퓨팅 (Compute)**

| 서비스                                  | 설명                                  |
| ------------------------------------ | ----------------------------------- |
| **EC2 (Elastic Compute Cloud)**      | 가상 머신 인스턴스 제공. 가장 유연한 컴퓨팅 환경 (IaaS) |
| **Lambda**                           | 서버리스 컴퓨팅. 이벤트 기반 함수 실행              |
| **ECS (Elastic Container Service)**  | 컨테이너 오케스트레이션, Fargate와 통합 가능        |
| **EKS (Elastic Kubernetes Service)** | 관리형 Kubernetes 클러스터 서비스             |
| **App Runner**                       | 컨테이너 기반 웹앱/API의 자동화된 배포             |
| **Lightsail**                        | 간단한 가상 서버 및 웹 호스팅 환경 (소규모 프로젝트용)    |

---

### 💾 **2. 스토리지 (Storage)**

| 서비스                                   | 설명                                 |
| ------------------------------------- | ---------------------------------- |
| **S3 (Simple Storage Service)**       | 확장 가능하고 내구성 높은 객체 스토리지             |
| **EBS (Elastic Block Store)**         | EC2용 블록 스토리지 (디스크 볼륨)              |
| **EFS (Elastic File System)**         | 리눅스 기반 공유 파일 시스템                   |
| **Glacier / S3 Glacier Deep Archive** | 장기 보관용 아카이빙 스토리지                   |
| **FSx**                               | 고성능 파일 스토리지 (Windows, Lustre 등 지원) |

---

### 🗄️ **3. 데이터베이스 (Database)**

| 서비스                                   | 설명                                       |
| ------------------------------------- | ---------------------------------------- |
| **RDS (Relational Database Service)** | 관리형 관계형 DB (MySQL, PostgreSQL, Oracle 등) |
| **Aurora**                            | RDS 기반 고성능 DB. MySQL/PostgreSQL 호환       |
| **DynamoDB**                          | 고속 NoSQL 키-값 및 문서 DB                     |
| **Redshift**                          | 페타바이트급 데이터 웨어하우징                         |
| **ElastiCache**                       | Redis/Memcached 기반 인메모리 캐시               |
| **Neptune**                           | 그래프 DB 서비스 (SPARQL, Gremlin 지원)          |
| **DocumentDB**                        | MongoDB 호환 문서 DB                         |

---

### 🌐 **4. 네트워킹 & 콘텐츠 전송 (Networking & CDN)**

| 서비스                             | 설명                    |
| ------------------------------- | --------------------- |
| **VPC (Virtual Private Cloud)** | 가상 네트워크 생성 및 리소스 분리   |
| **Route 53**                    | DNS 및 도메인 관리, 지능형 라우팅 |
| **CloudFront**                  | 글로벌 콘텐츠 전송 네트워크 (CDN) |
| **API Gateway**                 | API 엔드포인트 구성 및 트래픽 제어 |
| **PrivateLink, Direct Connect** | 온프레미스와의 보안 연결         |
| **Global Accelerator**          | 글로벌 애플리케이션 가속화 서비스    |

---

### 🔐 **5. 보안, 신원 및 규정 준수 (Security, Identity & Compliance)**

| 서비스                                    | 설명                           |
| -------------------------------------- | ---------------------------- |
| **IAM (Identity & Access Management)** | 사용자 및 리소스 권한 제어              |
| **KMS (Key Management Service)**       | 암호화 키 관리                     |
| **Secrets Manager / Parameter Store**  | 민감한 정보 저장 및 보안 관리            |
| **AWS WAF & Shield**                   | 웹 애플리케이션 방화벽 및 DDoS 보호       |
| **Macie**                              | 민감한 데이터 자동 탐지 및 보호 (예: 개인정보) |
| **Security Hub**                       | 보안 경고 및 규정 준수 통합 모니터링        |

---

### 📊 **6. 분석 & 데이터 처리 (Analytics)**

| 서비스                         | 설명                        |
| --------------------------- | ------------------------- |
| **Athena**                  | S3에 저장된 데이터에 대한 SQL 분석    |
| **Glue**                    | 서버리스 ETL (추출, 변환, 적재) 서비스 |
| **EMR (Elastic MapReduce)** | Hadoop, Spark 기반 빅데이터 처리  |
| **Redshift**                | 고속 데이터 웨어하우징              |
| **Kinesis**                 | 실시간 스트리밍 데이터 수집 및 분석      |
| **QuickSight**              | BI 도구 (시각화 대시보드 생성)       |

---

### 🤖 **7. 머신러닝 & 인공지능 (AI/ML)**

| 서비스                                | 설명                                           |
| ---------------------------------- | -------------------------------------------- |
| **SageMaker**                      | 머신러닝 모델 학습, 배포, 관리 통합 플랫폼                    |
| **Bedrock**                        | 생성형 AI API 제공 (Anthropic, Meta, Cohere 등 통합) |
| **Rekognition**                    | 이미지 및 영상 분석 (얼굴 인식 등)                        |
| **Lex**                            | 챗봇 제작 플랫폼 (Alexa 기술 기반)                      |
| **Comprehend**                     | 자연어 처리 서비스 (감정 분석, 엔티티 추출)                   |
| **Transcribe / Translate / Polly** | 음성 → 텍스트, 언어 번역, 텍스트 → 음성 변환                 |

---

### 🛠️ **8. 개발자 도구 및 DevOps**

| 서비스                             | 설명                      |
| ------------------------------- | ----------------------- |
| **CodeCommit**                  | Git 기반 소스 코드 저장소        |
| **CodeBuild**                   | 소스 코드 빌드 자동화            |
| **CodeDeploy**                  | EC2, Lambda 등에 코드 자동 배포 |
| **CodePipeline**                | CI/CD 파이프라인 구성          |
| **Cloud9**                      | 클라우드 기반 IDE             |
| **CDK (Cloud Development Kit)** | 프로그래밍 언어로 인프라 정의 (IaC)  |

---

### 📦 **9. 애플리케이션 통합 (App Integration)**

| 서비스                                   | 설명                    |
| ------------------------------------- | --------------------- |
| **SNS (Simple Notification Service)** | 메시지 푸시 및 알림 시스템       |
| **SQS (Simple Queue Service)**        | 메시지 큐 서비스 (비동기 처리)    |
| **Step Functions**                    | 워크플로우 오케스트레이션         |
| **EventBridge**                       | 이벤트 기반 애플리케이션 아키텍처 구현 |
| **AppSync**                           | 서버리스 GraphQL API 서비스  |

---

### 🏛️ **10. 관리 및 거버넌스**

| 서비스                               | 설명                     |
| --------------------------------- | ---------------------- |
| **CloudWatch**                    | 로그 수집, 지표 모니터링, 알림 설정  |
| **CloudTrail**                    | API 호출 기록 및 감사 로그      |
| **AWS Config**                    | 리소스 구성 변경 추적           |
| **Systems Manager**               | 하이브리드 환경 리소스 통합 관리     |
| **Organizations & Control Tower** | 멀티 계정 관리, 보안 정책 자동화    |
| **Trusted Advisor**               | 비용, 보안, 가용성 개선 권장사항 제공 |

---

이 외에도 IoT, 로보틱스, AR/VR, 양자 컴퓨팅 등 다양한 전문 서비스도 제공되고 있습니다.

---

## ✅ 참고: 잘 알려진 조합 예시

| 목적           | 서비스 조합                             |
| ------------ | ---------------------------------- |
| 정적 웹사이트      | S3 + CloudFront + Route 53         |
| REST API 백엔드 | Lambda + API Gateway + DynamoDB    |
| 마이크로서비스      | ECS/EKS + ALB + RDS                |
| 실시간 분석       | Kinesis + Lambda + S3 + QuickSight |
| 생성형 AI       | Bedrock + Lambda + API Gateway     |

---

원하신다면 위 카테고리를 다이어그램 형태로 정리해드릴 수도 있어요.
특정 워크로드(예: 웹, 데이터 분석, 머신러닝)에 대한 서비스 조합이 필요하신가요?
