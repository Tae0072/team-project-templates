# {{PROJECT_NAME}} — 테스트 전략서

> **문서 버전:** v1.0
> **작성일:** YYYY-MM-DD
> **연관 문서:** API 명세서 / 아키텍처 정의서

---

## 목차
1. 테스트 목표 및 범위
2. 테스트 도구
3. 테스트 유형별 전략
4. 테스트 커버리지 목표
5. 담당자별 테스트 범위
6. 외부 연동({{LLM/결제 등}}) 테스트 전략
7. 주차별 테스트 일정
8. CI 연동
9. 테스트 작성 규칙

---

## 1. 테스트 목표 및 범위

### 1.1 테스트 목표
- 핵심 비즈니스 규칙 보장
- 회귀 방지
- 코드 리뷰 시 변경 영향도 확인 가능

### 1.2 테스트 범위

| 범위 | 포함 | 제외 |
| --- | --- | --- |
| Unit | Service 계층 | SSR 템플릿 렌더링 |
| Repository | JPA 쿼리 정합 | 외부 DB |
| Controller | MockMvc 기반 | 실제 세션/CSRF 전 과정 |
| Integration | 핵심 E2E 시나리오 | 외부 API 실제 호출 |

---

## 2. 테스트 도구

### Gradle 의존성 (build.gradle)

```gradle
dependencies {
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    testImplementation 'org.springframework.security:spring-security-test'
    testImplementation 'com.h2database:h2'
    testImplementation 'org.mockito:mockito-inline'
}

test { useJUnitPlatform() }
```

---

## 3. 테스트 유형별 전략

### 3.1 단위 테스트 — Service

```java
@ExtendWith(MockitoExtension.class)
class {{Resource}}ServiceTest {
    @Mock {{Resource}}Repository repo;
    @InjectMocks {{Resource}}Service service;

    @Test
    void 생성_성공() { ... }

    @Test
    void 중복이면_예외() { ... }
}
```

### 3.2 Repository 테스트 (`@DataJpaTest`)

```java
@DataJpaTest
class {{Resource}}RepositoryTest {
    @Autowired {{Resource}}Repository repo;

    @Test
    void 상태별_조회() { ... }
}
```

### 3.3 Controller 테스트 (MockMvc)

```java
@WebMvcTest({{Resource}}Controller.class)
class {{Resource}}ControllerTest {
    @Autowired MockMvc mvc;
    @MockBean {{Resource}}Service service;

    @Test
    @WithMockUser(roles="{{A}}")
    void 목록_조회() throws Exception {
        mvc.perform(get("/{{role_a}}/{{resource}}"))
           .andExpect(status().isOk());
    }
}
```

### 3.4 통합 테스트 (`@SpringBootTest`)
- 핵심 유스케이스 E2E (외부 API는 Mock)
- `application-test.properties`로 DB·Redis 분리

---

## 4. 테스트 커버리지 목표

| 레이어 | 커버리지 |
| --- | --- |
| Service | 80%+ |
| Repository | 70%+ |
| Controller | 주요 엔드포인트 MockMvc 통과 |

### 핵심 클래스별 커버리지 목표
- `SecurityConfig`, `SlotService`, `LlmService` — 90%+

### JaCoCo 리포트 설정
`build.gradle`
```gradle
plugins { id 'jacoco' }
jacoco { toolVersion = "0.8.11" }
jacocoTestReport { reports { html.required = true } }
```

---

## 5. 담당자별 테스트 범위

| 담당자 | 범위 |
| --- | --- |
| 책임개발자 | Security, 공유 서비스, 외부 API 연동 |
| 개발자 A | 모듈 A |
| 개발자 B | 모듈 B |
| 개발자 C | 모듈 C |

---

## 6. 외부 연동 테스트 전략

### 원칙
- **테스트에서 실제 외부 API 호출 금지**
- 반드시 `@MockBean` 또는 `WireMock` 사용

### Mock 처리 패턴
```java
@MockBean {{External}}Client client;

@Test
void 외부_성공_시나리오() {
    given(client.call(any())).willReturn(new Response(...));
    ...
}

@Test
void 외부_실패_폴백() {
    given(client.call(any())).willThrow(new TimeoutException());
    // 폴백 경로 검증
}
```

### 시나리오 목록
- 정상 응답
- 타임아웃
- 4xx / 5xx
- 잘못된 JSON
- Rate limit
- 부분 실패 → 재시도

---

## 7. 주차별 테스트 일정

| 주차 | 활동 |
| --- | --- |
| W1 | 테스트 환경 구축, Repository 테스트 착수 |
| W2 | Service 단위 테스트 집중 |
| W3 | Controller / 통합 테스트 |
| W4 | E2E 시나리오 + 버그 수정 + 커버리지 목표 달성 |

---

## 8. CI 연동

### `.github/workflows/test.yml`
```yaml
name: test
on: [pull_request, push]
jobs:
  test:
    runs-on: ubuntu-latest
    services:
      redis:
        image: redis:7
        ports: ["6379:6379"]
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with: { java-version: '17', distribution: 'temurin' }
      - run: ./gradlew test jacocoTestReport
```

### PR 머지 조건
- CI green
- Review 2인 approve
- Service/Repository 커버리지 목표 미달 시 보완 요청

---

## 9. 테스트 작성 규칙

### 코드 컨벤션
- Given-When-Then 주석 권장
- 메서드명은 한글 + 언더스코어: `void 예약_중복이면_예외()`
- 테스트 클래스명: `{Class}Test`

### 파일 구조
```
src/test/java
└── com/example/{{project}}
    ├── feature/{{moduleA}}/{{A}}ServiceTest.java
    └── ...
```

### `application-test.properties`
```properties
# 테스트 전용 설정
spring.datasource.url=jdbc:h2:mem:testdb
spring.jpa.hibernate.ddl-auto=create-drop

# Redis 비활성화 (단위 테스트 시)
spring.autoconfigure.exclude=\
  org.springframework.boot.autoconfigure.data.redis.RedisAutoConfiguration,\
  org.springframework.boot.autoconfigure.session.SessionAutoConfiguration

# LLM 테스트 키 (실제 호출하지 않음)
{{external}}.api.key=test-key
```
