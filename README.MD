# EDF Project

## 📌 프로젝트 소개

EDF(Project Eco Data Footprint)는 사용자의 영수증, 거래 명세서 등의 데이터를 분석하여 탄소 발자국을 계산하고, AI 기반 피드백 및 친환경 활동 리워드를 제공하는 플랫폼입니다.

사용자는 영수증 이미지를 업로드하면 AI OCR 기술을 통해 데이터를 추출하고, 이를 기반으로 탄소 배출량을 계산받을 수 있습니다.

또한 사용자 행동 데이터를 기반으로:
- 친환경 소비 피드백
- 탄소 절감 분석
- 포인트 및 레벨 시스템
- 리워드 시스템

등을 제공합니다.

---

# 🏗️ 프로젝트 구조

## EDF-Infra Repository

인프라 전용 레포지토리입니다.

Terraform 기반 AWS Infrastructure as Code(IaC)를 관리합니다.

```bash
EDF-Infra/
├── environments/
├── modules/
├── bootstrap/
└── .github/workflows/
```

### 주요 역할

- AWS VPC 생성
- EKS 클러스터 생성
- RDS PostgreSQL 생성
- ECR Repository 생성
- IAM / OIDC 구성
- AWS Load Balancer Controller IAM 구성

---

## EDF-App Repository

실제 서비스 코드 및 Kubernetes 배포 레포지토리입니다.

```bash
EDF-App/
├── backend/
├── frontend/
├── ai/
├── k8s/
├── argocd/
└── .github/workflows/
```

### 주요 역할

- Backend / Frontend / AI 서비스 개발
- Docker Image Build
- Kubernetes Deployment
- ArgoCD GitOps 관리
- GitHub Actions CI/CD

---

# ⚙️ 기술 스택

## Backend
- Spring Boot
- Spring Security
- PostgreSQL
- Redis
- JWT
- OAuth2

## Frontend
- React / Next.js

## AI
- Python
- FastAPI
- OCR
- OpenAI API

## Infrastructure
- AWS EKS
- Terraform
- ArgoCD
- GitHub Actions
- Grafana
- Prometheus
- AWS Load Balancer Controller

---

# 🚀 전체 동작 흐름

```text
Developer Push
    ↓
GitHub Actions
    ↓
Docker Image Build
    ↓
Amazon ECR Push
    ↓
Kubernetes Manifest Image Tag Update
    ↓
ArgoCD Sync
    ↓
Amazon EKS Deployment
```

---

# 📦 서비스 구조

```text
User
  ↓
AWS ALB (Ingress)
  ├── /      → Frontend
  └── /api  → Backend
                    ↓
                  AI Service
                    ↓
              PostgreSQL / Redis
```

---

# 🔄 CI/CD 구조

## GitHub Actions

### deploy.yml
- Docker 이미지 빌드
- ECR Push
- Kubernetes image tag 변경

### bootstrap.yml
- ArgoCD 설치
- Monitoring 설치
- AWS Load Balancer Controller 설치

---

# ☸️ Kubernetes 구성

```bash
k8s/
├── backend/
├── frontend/
├── ai/
├── redis/
├── ingress/
└── monitoring/
```

각 서비스는 다음 리소스로 구성됩니다.

- Deployment
- Service
- HPA
- ConfigMap
- Secret

---

# 🔐 Secret 관리

민감 정보는 GitHub Secrets를 통해 관리합니다.

예시:
- DB_PASSWORD
- JWT_SECRET
- OPENAI_API_KEY
- OAuth Client Secret

Kubernetes Secret은 bootstrap workflow를 통해 자동 생성됩니다.

---

# 📊 Monitoring

Prometheus + Grafana 기반 모니터링 시스템을 사용합니다.

수집 대상:
- CPU
- Memory
- Pod 상태
- Spring Boot Metrics
- Kubernetes Metrics

---

# 🛠️ 실행 순서

## 1. Infra Repository

Terraform 실행

```bash
terraform init
terraform plan
terraform apply
```

---

## 2. App Repository

bootstrap workflow 실행

설치 대상:
- ArgoCD
- AWS Load Balancer Controller
- Monitoring Stack

---

## 3. deploy workflow 실행

자동 수행:
- Docker Build
- ECR Push
- Manifest Update
- ArgoCD Sync

---

# 📌 팀원 참고 사항

## Backend Developer
- `/actuator/health`
- `/actuator/prometheus`

endpoint 필수

---


## Frontend Developer

환경변수:
- API BASE URL
- OAuth Redirect URL

확인 필요

---

# 📄 환경 변수

`.env.example` 참고

민감정보는 GitHub Secrets 사용/ 이 부분은 환경변수 통일 및 설정이 끝난 후 전체 수정하겠습니다!!
아직 참고용으로 생각하시고 넘어가시면 됩니다!

---

# 👨‍💻 운영 방식

본 프로젝트는 GitOps 기반으로 운영됩니다.

즉:
- GitHub 상태가 실제 클러스터 상태
- ArgoCD가 Git 상태를 자동 반영하는 구조입니다.