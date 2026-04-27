# {{PROJECT_NAME}} — 프로젝트 최소 요구사항 정의서

> **문서 버전:** v1.0
> **작성일:** YYYY-MM-DD
> **용도:** {{과제/교과 등}} 최소 필수 요건 체크리스트

---

## 1. 프로젝트 개요
- **프로젝트명:** {{PROJECT_NAME}}
- **도메인:** {{DOMAIN}}
- **목적:** {{한 줄 목적}}

---

## 2. 최소 필수 요구사항 (모두 구현해야 프로젝트 완료로 인정)

### 2.1 백엔드
- [ ] Spring Boot 3.x 기반 REST + SSR 혼합
- [ ] JPA 연관관계 (1:1 / 1:N / N:M 각 1개 이상 포함)
- [ ] Spring Security 인증·인가 (세션 기반)
- [ ] Redis 세션 저장소
- [ ] @Valid 서버 사이드 유효성 검증
- [ ] 글로벌 예외 핸들러 (`@RestControllerAdvice` / `@ControllerAdvice`)
- [ ] 페이징 구현 (`Pageable`)
- [ ] 외부 API 연동 1종 이상

### 2.2 프론트엔드 (Mustache SSR)
- [ ] 공통 레이아웃(`layout.mustache`)과 partial 분리
- [ ] Role별 사이드바 분기 노출
- [ ] CSRF 토큰 처리 (폼 + AJAX)
- [ ] Flash 메시지 (PRG 패턴)

### 2.3 DB
- [ ] MySQL 8.0
- [ ] 테이블 최소 {{N}}개 이상
- [ ] 핵심 테이블 인덱스 정의
- [ ] FK 정합성 유지

### 2.4 테스트
- [ ] JUnit 5 + MockMvc + @DataJpaTest
- [ ] Service 커버리지 80% 이상
- [ ] Repository 커버리지 70% 이상

### 2.5 배포/운영
- [ ] Docker Compose 배포 스크립트
- [ ] dev / prod 프로필 분리
- [ ] 로그 / 백업 전략 문서화

### 2.6 문서
- [ ] 프로젝트 계획서 / ERD / API 명세서 / 아키텍처 / 화면 기능 정의서
- [ ] Git / 코드리뷰 / 테스트 / 배포 문서

---

## 3. 추가 학습 권장 항목 (자기주도 / 가점)
- [ ] Actuator + Prometheus + Grafana
- [ ] OAuth2 / JWT 병행
- [ ] CI/CD (GitHub Actions)
- [ ] API 문서 자동화 (Springdoc OpenAPI)
- [ ] 부하 테스트 (k6 / Gatling)

---

## 4. 구현 시 주의사항 & 팁
- {{도메인 특화 주의점 1}}
- {{도메인 특화 주의점 2}}
- **공통:** 외부 API 키는 환경변수로만 관리, 커밋 절대 금지
