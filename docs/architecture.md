# EDF Architecture

# 📌 전체 아키텍처

EDF는 AWS EKS 기반 Kubernetes 환경에서 동작하는 GitOps 아키텍처를 사용합니다.

전체 서비스는:
- Frontend
- Backend
- AI Service

로 구성됩니다.

---

# 🏗️ 전체 구조

# 🏗️ 전체 구조

```text
                    ┌────────────────────┐
                    │       User         │
                    └─────────┬──────────┘
                              │
                              ▼
                 ┌────────────────────────┐
                 │ AWS Application Load   │
                 │ Balancer (ALB)         │
                 └─────────┬──────────────┘
                           │
          ┌────────────────┴────────────────┐
          │                                 │
          ▼                                 ▼
   ┌──────────────┐                 ┌──────────────┐
   │ Frontend Pod │                 │ Backend Pod  │
   └──────┬───────┘                 └──────┬───────┘
          │                                 │
          │ API Request                     │
          └────────────────────────────────▶│
                                            │
                                            ▼
                                   ┌──────────────┐
                                   │ AI Service   │
                                   └──────┬───────┘
                                          │
                                          ▼
                                ┌─────────────────┐
                                │ OCR / AI Logic  │
                                └─────────────────┘

                                            │
                                            ▼
                                   ┌──────────────┐
                                   │ PostgreSQL   │
                                   └──────────────┘

                                            │
                                            ▼
                                   ┌──────────────┐
                                   │ Redis        │
                                   └──────────────┘
```
---

# 🌐 Ingress 구조

AWS Load Balancer Controller를 사용하여 ALB를 생성합니다.

Ingress 설정:

```text
/      → frontend-service
/api  → backend-service
```

HTTPS/TLS는 ACM 인증서를 통해 처리됩니다.

---

# ☸️ Kubernetes 구성

각 서비스는 독립적인 Kubernetes Deployment로 동작합니다.

## Backend
- Spring Boot
- HPA 적용
- Prometheus Metrics 노출

## Frontend
- React/Next.js
- 정적 웹 서비스

## AI
- Python FastAPI
- OCR 및 AI 분석 수행

## Redis
- Cache / Session 저장

---

# 🔄 GitOps 구조

ArgoCD를 사용하여 GitOps 기반 운영을 수행합니다.

```text
GitHub Push
    ↓
GitHub Actions
    ↓
Docker Build
    ↓
Amazon ECR Push
    ↓
Manifest Update
    ↓
ArgoCD Sync
    ↓
Kubernetes Deployment
```

---

# 📦 Docker Image 관리

각 서비스는 독립적인 ECR Repository를 사용합니다.

```text
edf-backend
edf-frontend
edf-ai
```

GitHub Actions가 이미지를 빌드하고 ECR에 Push합니다.

---

# 📊 Monitoring 구조

Prometheus + Grafana 기반 모니터링을 사용합니다.

수집 대상:
- Pod 상태
- CPU / Memory
- Spring Boot Metrics
- Kubernetes Metrics

---

# 🔐 보안 구조

## IAM OIDC

GitHub Actions와 EKS는 OIDC 기반 인증을 사용합니다.

장점:
- Access Key 불필요
- 최소 권한 정책 적용 가능

---

## Secret 관리

민감정보는:
- GitHub Secrets
- Kubernetes Secrets

를 통해 관리됩니다.

---

# 🧠 AI 서비스 구조

사용자가 영수증 이미지를 업로드하면:

```text
Image Upload
    ↓
AI OCR
    ↓
데이터 추출
    ↓
탄소 배출량 계산
    ↓
사용자 피드백 생성
```

흐름으로 처리됩니다.

---

# 📌 운영 목표

EDF 플랫폼의 목표는:
- 안정적인 24시간 서비스 운영
- GitOps 기반 자동 배포
- 클라우드 네이티브 구조 학습
- AI 기반 친환경 서비스 제공

입니다.