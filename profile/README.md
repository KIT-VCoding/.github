# ThinkMap - AI 기반 소크라테스 학습 플랫폼

> 질문하고, 생각하고, 성장하는 학습 경험

**ThinkMap**은 AI와의 대화를 통해 학습자가 스스로 사고를 확장할 수 있도록 돕는 마인드맵 기반 학습 플랫폼입니다.  
단순히 답을 제공하는 것이 아니라, 소크라테스식 문답법으로 학습자의 자기 주도적 탐구를 유도합니다.

**배포 URL:** [https://kit-vcoding.netlify.app](https://kit-vcoding.netlify.app)

---

## 핵심 가치

| 가치 | 설명 |
|------|------|
| **소크라테스식 학습** | AI가 직접 답을 주지 않고 질문과 힌트로 학습자의 사고를 유도 |
| **시각적 사고 정리** | 질문과 답변이 마인드맵 트리 구조로 누적·시각화 |
| **학습 회고** | 세션 종료 후 AI가 6가지 항목으로 학습 내용을 돌아보는 인사이트 제공 |
| **포트폴리오 생성** | 세션 전체 흐름을 바탕으로 10가지 항목의 포트폴리오 리포트 자동 생성 |

---

## 기술 스택

### Backend
![Java](https://img.shields.io/badge/Java_17-ED8B00?style=flat-square&logo=openjdk&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring_Boot_3.5-6DB33F?style=flat-square&logo=springboot&logoColor=white)
![Spring Security](https://img.shields.io/badge/Spring_Security-6DB33F?style=flat-square&logo=springsecurity&logoColor=white)
![JPA](https://img.shields.io/badge/Spring_Data_JPA-6DB33F?style=flat-square&logo=spring&logoColor=white)
![Gemini](https://img.shields.io/badge/Gemini_API-4285F4?style=flat-square&logo=google&logoColor=white)

| 구분 | 기술 |
|------|------|
| Language | Java 17 |
| Framework | Spring Boot 3.5 |
| Security | Spring Security 6, JWT (JJWT) |
| ORM | Spring Data JPA (Hibernate) |
| Database | H2 |
| HTTP Client | Spring WebFlux (Reactor) |
| AI | Google Gemini API (gemini-2.5-flash) |
| API Docs | SpringDoc OpenAPI 3.0 (Swagger UI) |
| Build | Gradle 8 |
| Deploy | AWS EC2 + GitHub Actions CI/CD |

### Frontend
![React](https://img.shields.io/badge/React-61DAFB?style=flat-square&logo=react&logoColor=black)
![Netlify](https://img.shields.io/badge/Netlify-00C7B7?style=flat-square&logo=netlify&logoColor=white)

| 구분 | 기술 |
|------|------|
| Language | TypeScript |
| Framework | React 19 |
| Build Tool | Vite |
| Styling | CSS |
| Lint | ESLint |
| Package Manager | Yarn |
| Deploy | Netlify |

---

## 주요 기능

### 1. 인증
- **로컬 회원가입 / 로그인** — 이메일 + 비밀번호 기반, BCrypt 해싱
- **JWT 인증** — Stateless 토큰 방식, 24시간 만료

### 2. 학습 세션 관리
- 세션 생성 / 목록 조회 / 상세 조회 / 제목 수정 / 삭제
- 최근 활동 순 정렬 (노드 추가 시 세션 `updatedAt` 자동 갱신)
- 세션 삭제 시 하위 노드 전체 Cascade 삭제

### 3. 마인드맵 노드 시스템
- **QUESTION** — 학습자가 입력한 질문
- **AI_ANSWER** — Gemini가 소크라테스식으로 반환하는 힌트/재질문
- **REVISION** — 학습자의 추가 사고 및 수정
- **INSIGHT** — 세션 회고 노드 (6항목)
- 노드는 자기 참조(self-join) 트리 구조로 저장되어 마인드맵으로 시각화

### 4. AI 기능 (Gemini 2.5 Flash)
- **소크라테스 응답** — 답 대신 힌트·반문으로 자기 발견 유도
- **학습 인사이트** — 세션 전체를 분석한 6가지 회고 항목 생성
- **포트폴리오 리포트** — 세션 흐름 기반 10가지 포트폴리오 항목 자동 생성
- WebFlux 비동기 호출 + 지수 백오프 재시도 (429/503)

---

## API 엔드포인트

> 전체 API 명세는 배포 서버의 `/swagger-ui` 에서 확인할 수 있습니다.

### 인증 (`/api/auth`)
| Method | Endpoint | 설명 | 인증 |
|--------|----------|------|------|
| POST | `/api/auth/signup` | 회원가입 | - |
| POST | `/api/auth/login` | 로그인 (JWT 발급) | - |
| GET | `/api/auth/me` | 현재 사용자 정보 조회 | JWT |

### 세션 (`/api/sessions`)
| Method | Endpoint | 설명 | 인증 |
|--------|----------|------|------|
| POST | `/api/sessions` | 세션 생성 | JWT |
| GET | `/api/sessions` | 내 세션 목록 조회 | JWT |
| GET | `/api/sessions/{id}` | 세션 상세 조회 | JWT |
| PATCH | `/api/sessions/{id}` | 세션 제목 수정 | JWT |
| DELETE | `/api/sessions/{id}` | 세션 삭제 | JWT |

### 노드 (`/api/sessions/{sessionId}/nodes`)
| Method | Endpoint | 설명 | 인증 |
|--------|----------|------|------|
| POST | `/api/sessions/{sessionId}/nodes` | 질문/수정 노드 추가 + AI 응답 | JWT |
| POST | `/api/sessions/{sessionId}/nodes/insight` | 학습 인사이트 생성 | JWT |
| GET | `/api/sessions/{sessionId}/nodes/tree` | 마인드맵 트리 조회 | JWT |

### 포트폴리오 (`/api/sessions/{sessionId}/portfolio-report`)
| Method | Endpoint | 설명 | 인증 |
|--------|----------|------|------|
| POST | `/api/sessions/{sessionId}/portfolio-report` | 포트폴리오 리포트 생성 | JWT |

---

## 데이터베이스 구조

```
users
├── id (PK)
├── name
├── email (unique)
├── password (nullable, OAuth용)
├── provider (LOCAL)
├── provider_id
└── created_at / updated_at

learning_session
├── id (PK)
├── user_id (FK → users)
├── title
└── created_at / updated_at

thought_node
├── id (PK)
├── session_id (FK → learning_session)
├── parent_node_id (자기참조 FK, nullable)
├── node_type (QUESTION / AI_ANSWER / REVISION / INSIGHT)
├── content (TEXT)
├── summary (마인드맵 라벨, 80자)
└── created_at
```

---

## 프로젝트 구조 (Backend)

```
src/main/java/com/example/thinkmap/
├── client/                  # Gemini API 통신
├── config/                  # Security, CORS, OpenAPI, 예외 처리
├── controller/              # REST 컨트롤러 (Auth, Session, Thought, Portfolio)
├── domain/
│   ├── entity/              # User, LearningSession, ThoughtNode
│   └── repository/          # JPA 레포지토리
├── dto/                     # 요청/응답 DTO
├── security/
│   ├── jwt/                 # JWT 필터 & 토큰 프로바이더
│   └── oauth2/              # OAuth2 핸들러
└── service/                 # 비즈니스 로직 (Thought, LocalAuth, Portfolio)
```

---

## 로컬 실행 방법

### 사전 준비
- Java 17+
- Gradle 8+
- Gemini API Key

### 환경변수 설정

`.env.example`을 참고하여 환경변수를 설정합니다.

```env
GEMINI_API_KEY=your_gemini_api_key
JWT_SECRET=your_jwt_secret_256bits
```

### 실행

```bash
git clone https://github.com/<org>/contest_BE.git
cd contest_BE

# 환경변수 설정 후
./gradlew bootRun
```

기본 포트: `http://localhost:8080`  
Swagger UI: `http://localhost:8080/swagger-ui`  
H2 Console: `http://localhost:8080/h2-console`

---

## 배포 아키텍처

```
사용자
  │
  ▼
Netlify (Frontend, React)
  │  API 요청
  ▼
AWS EC2 (Backend, Spring Boot :8080)
  │
  ├── MySQL (운영 DB)
  └── Google Gemini API
```

- **CI/CD**: GitHub Actions — PR/push 시 테스트 & 빌드, main 브랜치 push 시 EC2 자동 배포
- **HTTPS**: Cloudflare 프록시를 통한 SSL 처리

---

## 에러 응답 형식

```json
{
  "status": 400,
  "error": "Bad Request",
  "message": "에러 상세 메시지",
  "path": "/api/sessions"
}
```

| 상태 코드 | 상황 |
|-----------|------|
| 400 | 잘못된 요청 (유효성 검사 실패 등) |
| 401 | 인증 실패 (JWT 없음 또는 만료) |
| 403 | 권한 없음 (다른 사용자 리소스 접근) |
| 404 | 리소스를 찾을 수 없음 |
| 500 | 서버 내부 오류 |
