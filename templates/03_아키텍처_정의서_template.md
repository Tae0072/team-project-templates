# {{EMOJI}} {{PROJECT_NAME}} — 프로젝트 아키텍처 정의서 v1.0

> **문서 버전:** v1.0
> **작성일:** YYYY-MM-DD
> **연관 문서:** 프로젝트 계획서 / ERD / API 명세서

---

## 목차
1. 전체 시스템 레이어 구조
2. 기술 스택
3. Spring Boot 패키지 구조 & 소유자 맵핑
4. 담당자별 소유 영역 전체 지도
5. 공유 레이어 — 책임개발자 단독 소유
6. 기능 모듈별 경계 정의
7. 세션 저장소 설정
8. 서버 사이드 유효성 검증 패턴
9. CSRF 토큰 + AJAX 처리
10. 페이징 구현 패턴

---

## 1. 전체 시스템 레이어 구조

```
Browser
  │ HTTPS
  ▼
Nginx (80/443) ── SSL 종단
  │
  ▼
Spring Boot App (8080)
  ├─ Filter: Security, CSRF
  ├─ Interceptor: LayoutModelInterceptor
  ├─ Controller (SSR / @RestController)
  ├─ Service
  ├─ Repository (Spring Data JPA)
  ├─ Session ─── Redis
  └─ External ── {{외부 API (LLM / 결제 / 메시지)}}
            │
            ▼
         MySQL 8.0
```

---

## 2. 기술 스택

| 영역 | 기술 | 버전 | 비고 |
| --- | --- | --- | --- |
| Runtime | JDK | 17 / 21 | |
| Framework | Spring Boot | 3.x | |
| View | Mustache | | SSR |
| DB | MySQL | 8.0 | |
| ORM | Spring Data JPA | | |
| Session | Redis | 7.0 | Spring Session Data Redis |
| HTTP Client | Spring WebClient | | 외부 API 호출 |
| Build | Gradle | | |
| Test | JUnit 5 + MockMvc | | |
| Deploy | Docker Compose + Nginx | | |

---

## 3. Spring Boot 패키지 구조 & 소유자 맵핑

```
com.example.{{project}}
├── {{project}}Application.java       [Lead]
├── config/                           [Lead]
│   ├── SecurityConfig.java
│   ├── RedisSessionConfig.java
│   └── WebMvcConfig.java
├── common/                           [Lead]
│   ├── interceptor/LayoutModelInterceptor.java
│   ├── exception/GlobalExceptionHandler.java
│   └── dto/ApiResponse.java
├── domain/                           [Lead — 변경 금지]
│   ├── {{entity1}}/{{Entity1}}.java
│   └── {{entity2}}/{{Entity2}}.java
├── feature/
│   ├── {{moduleA}}/                  [Dev A]
│   │   ├── {{ModuleA}}Controller.java
│   │   ├── {{ModuleA}}Service.java
│   │   └── {{ModuleA}}Repository.java
│   ├── {{moduleB}}/                  [Dev B]
│   │   └── ...
│   └── {{moduleC}}/                  [Dev C]
│       └── ...
└── llm/                              [Lead — 공유 레이어]
    ├── LlmService.java
    └── ClaudeClient.java
```

---

## 4. 담당자별 소유 영역 전체 지도

### 4.1 책임개발자 (Tech Lead)
- `config/`, `common/`, `domain/` 전체
- 공유 서비스: SlotService, LayoutModelInterceptor, LlmService, SecurityFilter
- 배포, CI, 문서 정합 관리

### 4.2 개발자 A — {{MODULE_A}}
- `feature/{{moduleA}}/**` 전체
- 관련 Mustache 템플릿: `templates/{{moduleA}}/**`
- 관련 테스트: `test/**/{{moduleA}}/**`

### 4.3 개발자 B — {{MODULE_B}}
- `feature/{{moduleB}}/**`
- ...

### 4.4 개발자 C — {{MODULE_C}}
- `feature/{{moduleC}}/**`
- ...

---

## 5. 공유 레이어 — 책임개발자 단독 소유

### 5.1 SlotService — 예약/슬롯 중복 방지 (해당 시)
### 5.2 LayoutModelInterceptor — 공통 레이아웃 Model 자동 주입
### 5.3 Entity 클래스 — 변경 금지 (schema 변경은 PR 리뷰 필수)

---

## 6. 기능 모듈별 경계 정의

### 모듈 A — {{MODULE_A_NAME}} (개발자 A)
- **의존:** {{ENTITY_1}}, {{ENTITY_2}}
- **제공:** 외부 사용자 진입 화면, {{기능}}
- **인터페이스(서비스 메서드 시그니처):**
  - `{{MODULE_A}}Service.createX(request): Result`

### 모듈 B — {{MODULE_B_NAME}} (개발자 B)
- ...

---

## 7. Redis 세션 저장소 설정

**의존성 (build.gradle)**
```gradle
implementation 'org.springframework.boot:spring-boot-starter-data-redis'
implementation 'org.springframework.session:spring-session-data-redis'
```

**설정 (application.properties)**
```properties
spring.session.store-type=redis
spring.session.redis.namespace=spring:session:{{project}}
spring.data.redis.host=localhost
spring.data.redis.port=6379
server.servlet.session.timeout=30m
```

---

## 8. 서버 사이드 유효성 검증 패턴

**원칙:** 모든 외부 입력은 `@Valid` 또는 `@Validated`로 검증.

**SSR 폼 처리 (PRG 패턴)**
```java
@PostMapping("/{{resource}}")
public String create(@Valid @ModelAttribute {{Resource}}Form form,
                     BindingResult bindingResult,
                     RedirectAttributes ra) {
    if (bindingResult.hasErrors()) {
        return "{{resource}}/form";  // 같은 페이지 재렌더
    }
    service.create(form);
    ra.addFlashAttribute("message", "등록되었습니다");
    return "redirect:/{{resource}}";
}
```

**REST API**
```java
@PostMapping("/api/{{resource}}")
public ResponseEntity<ApiResponse<{{Resource}}Dto>> create(
        @Valid @RequestBody {{Resource}}Request req) {
    return ResponseEntity.ok(ApiResponse.success(service.create(req)));
}
```

---

## 9. CSRF 토큰 + AJAX 처리

**Mustache 메타 태그 방식 (권장)**
```html
<meta name="_csrf" content="{{_csrf.token}}">
<meta name="_csrf_header" content="{{_csrf.headerName}}">
```

```javascript
const token = document.querySelector('meta[name="_csrf"]').content;
const header = document.querySelector('meta[name="_csrf_header"]').content;
fetch('/api/...', { headers: { [header]: token, 'Content-Type': 'application/json' }, ... });
```

---

## 10. 페이징 구현 패턴

```java
@GetMapping("/{{resource}}")
public String list(@PageableDefault(size = 20) Pageable pageable, Model model) {
    Page<{{Resource}}Dto> page = service.findAll(pageable);
    model.addAttribute("page", page);
    return "{{resource}}/list";
}
```

Mustache에서 페이지 번호/이전·다음 처리 파셜(`partials/pagination.mustache`)을 공유 컴포넌트로 둔다.
