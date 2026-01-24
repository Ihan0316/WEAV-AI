# WEAV AI Backend

Django REST Framework 기반의 AI 생성 서비스 백엔드입니다.

## 🏗️ 아키텍처

```
Nginx → Django + DRF → PostgreSQL (데이터)
                      → Redis (캐시/작업 큐)
                      → Celery (비동기 작업)
                      → MinIO (파일 저장)
```

## 🚀 빠른 시작

### Docker Compose로 실행 (권장)

```bash
cd infra
docker compose up -d
```

### 로컬 개발 환경

```bash
# Python 가상환경 생성
python3 -m venv venv
source venv/bin/activate

# 의존성 설치
pip install -r requirements.txt

# 환경 변수 설정
cp env.example .env
# .env 파일 편집

# 데이터베이스 마이그레이션
python manage.py migrate

# 서버 실행
python manage.py runserver
```

---

## 📡 주요 API 엔드포인트

### 인증

- `POST /api/v1/auth/verify-firebase-token/` - Firebase ID Token 검증 및 JWT 발급
- `POST /api/v1/auth/token/refresh/` - JWT 토큰 갱신
- `GET /api/v1/auth/profile/` - 사용자 프로필 조회

### AI 작업

- `POST /api/v1/jobs/` - AI 작업 생성
  - `provider`: `openai` 또는 `gemini`
  - `model_id`: 모델 ID (예: `gpt-4o-mini`, `gemini-1.5-flash`)
  - `arguments`: AI 요청 파라미터

- `GET /api/v1/jobs/{job_id}/` - 작업 상태 조회

---

## 🔧 환경 변수

### 필수 설정

```bash
SECRET_KEY=your-secret-key
DEBUG=True
ALLOWED_HOSTS=localhost,127.0.0.1

# 데이터베이스
POSTGRES_DB=weavai
POSTGRES_USER=weavai_user
POSTGRES_PASSWORD=your-password
POSTGRES_HOST=postgres
POSTGRES_PORT=5432

# Redis
REDIS_URL=redis://redis:6379/0

# AI API 키
OPENAI_API_KEY=<openai-api-key>
GEMINI_API_KEY=<gemini-api-key>

# Firebase Admin SDK
FIREBASE_SERVICE_ACCOUNT_KEY_PATH=/path/to/firebase-key.json
# 또는
FIREBASE_SERVICE_ACCOUNT_KEY_JSON='{"type":"service_account",...}'
```

---

## 🗄️ 데이터베이스 모델

### User (Django 기본 + 커스텀)
- Firebase UID를 username으로 사용
- 이메일, 이름 등 기본 정보

### Job
- AI 작업 추적
- 상태: `PENDING`, `COMPLETED`, `FAILED`
- Provider: `openai`, `gemini`

### Artifact
- 생성된 결과물 (텍스트, 이미지, 비디오)
- S3 키, Presigned URL, 메타데이터

---

## 🔒 보안

- Firebase ID Token 검증 필수
- JWT 토큰 기반 인증
- AI API 키는 서버에서만 관리
- CORS 설정으로 허용된 도메인만 접근

---

## 📊 현재 구현 상태

### ✅ 완료
- Firebase 토큰 검증 및 JWT 발급
- OpenAI 텍스트 생성 API 연동
- Jobs API 기본 구조
- PostgreSQL, Redis, MinIO 연동

### 🔄 진행 중
- Gemini API 연동 (코드 작성 완료, 테스트 필요)
- 이미지 생성 (DALL-E 3)
- 비디오 생성 (SORA, VEO)

---

**마지막 업데이트**: 2026-01-24
