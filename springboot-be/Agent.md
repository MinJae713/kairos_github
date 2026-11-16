# Agent.md

## 목적
이 저장소는 Spring Boot 기반 백엔드 프로젝트입니다.
Codex는 반드시 기존 구조와 규칙을 우선적으로 따르며 작업해야 합니다.

---

## 작업 전 필수 확인

다음 파일을 반드시 먼저 읽고 분석한다:

1. src/resources/env.txt
2. src/resources/application.yml
3. docs/package-rules.md
4. docs/coding-rules.md
5. docs/api-spec.md
6. docs/api-changes.md
7. docs/erd.md
8. docs/redis.md

---

## 핵심 아키텍처 이해 (중요)

### 분석 구조

- 분석은 Spring Boot가 담당하지 않는다
- FastAPI가 분석을 수행한다
- Frontend ↔ FastAPI가 직접 SSE 통신한다
- Spring Boot는 분석 결과를 저장하지 않는다

즉:

❌ Spring Boot에서 SSE 처리 금지  
❌ 분석 결과 DB 저장 금지  
❌ analysis_report 테이블 사용 금지  

---

## 온보딩 흐름

1. Google 로그인 → onboarding Redis 저장
2. GitHub 연동 → USER 생성 (GUEST)
3. Velog 연동 → FastAPI 수집 시작
4. 설문조사 → 선호도 저장 + FastAPI 분석 트리거
5. 분석 진행 → Frontend ↔ FastAPI SSE
6. 분석 완료 → FastAPI → Spring Boot (/analysis/complete)

---

## Domain 제한 (중요)

허용된 도메인만 작업 가능:

- auth
- onboarding
- analysis
- quizzes
- recommendations

이외 도메인은 절대 생성/수정 금지

---

## 구현 규칙

- Controller → Service → Repository 구조 유지
- Service는 interface + ServiceImpl 구조
- DTO는 request/response 분리
- Entity는 persistence 패키지에만 존재
- Controller에서 Entity 반환 금지

---

## Redis 규칙

- 인증 관련 (onboarding, refresh_token, blacklist)
- 캐시 (user profile, calendar 등)
- preview 데이터 (curriculum_preview)

❌ 분석 결과 Redis 저장 금지

---

## FastAPI 연동

Spring Boot는 다음 API만 호출:

- /api/v1/ai/github/collect-async
- /api/v1/ai/velog/collect-async
- /api/v1/ai/profile/analyze-async

분석 결과는 FastAPI가 Frontend로 직접 전달

---

## 절대 금지 사항

- 새로운 패키지 생성
- Allowed Domain 외 코드 작성
- 분석 결과 DB 저장
- SSE 직접 구현
- 기존 구조 무시

---

## 작업 후 반드시 출력

1. 변경된 파일 목록
2. 핵심 로직 설명
3. 실행 방법
4. 테스트 방법
5. 위험 요소

---

## 판단 불가 시

구현하지 말고 아래를 보고:

- 충돌되는 규칙
- 부족한 정보
- 추가 확인 필요 사항

