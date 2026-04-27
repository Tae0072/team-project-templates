# 📦 프로젝트 문서 템플릿 팩

> [Tae0072/documents](https://github.com/Tae0072/documents) — 병원 예약 & 내부 업무 시스템(HMS) 프로젝트 문서 체계를 **도메인 중립 템플릿**으로 재구성한 세트입니다. 다음 팀 프로젝트 시작 시 프로젝트명·엔티티·역할만 바꿔서 그대로 사용하세요.

---

## 구성

| 파일 | 용도 |
| --- | --- |
| [`프로젝트_문서_가이드.md`](./프로젝트_문서_가이드.md) | 전체 문서 체계 / 작성 순서 / 컨벤션 / 체크리스트 **← 다음 프로젝트 킥오프 때 이 파일부터 훑기** |
| [`templates/00_개발_일정_총괄표_template.md`](./templates/00_개발_일정_총괄표_template.md) | Gantt + 마일스톤 + Critical Path |
| [`templates/01_프로젝트_계획서_template.md`](./templates/01_프로젝트_계획서_template.md) | SMART 목표·WBS·리스크 |
| [`templates/02_ERD_문서_template.md`](./templates/02_ERD_문서_template.md) | 테이블·관계·상태코드 |
| [`templates/03_아키텍처_정의서_template.md`](./templates/03_아키텍처_정의서_template.md) | 패키지·소유자·공유 레이어 |
| [`templates/04_API_명세서_template.md`](./templates/04_API_명세서_template.md) | 공통 규칙·엔드포인트 템플릿 |
| [`templates/05_시퀀스_다이어그램_template.md`](./templates/05_시퀀스_다이어그램_template.md) | Mermaid 시퀀스 |
| [`templates/06_화면_기능_정의서_template.md`](./templates/06_화면_기능_정의서_template.md) | Layout·Model·화면 상세 |
| [`templates/07_요구사항_정의서_template.md`](./templates/07_요구사항_정의서_template.md) | 교과/과제 필수 요건 체크리스트 |
| [`templates/09_Git_규칙_template.md`](./templates/09_Git_규칙_template.md) | 브랜치·커밋·PR |
| [`templates/10_환경_설정_템플릿/`](./templates/10_환경_설정_템플릿/) | application 공통/dev/prod 3종 |
| [`templates/11_테스트_전략서_template.md`](./templates/11_테스트_전략서_template.md) | 커버리지·담당자·CI |
| [`templates/12_코드_리뷰_규칙_template.md`](./templates/12_코드_리뷰_규칙_template.md) | PR·리뷰 체크리스트 |
| [`templates/14_배포_가이드_template.md`](./templates/14_배포_가이드_template.md) | Docker Compose·Nginx·SSL |

---

## 치환 가이드 (Find & Replace)

템플릿에 적힌 `{{...}}` 자리표시자를 일괄 치환하세요.

| 토큰 | 치환 예 |
| --- | --- |
| `{{PROJECT_NAME}}` | 병원 예약 & 내부 업무 시스템 |
| `{{EMOJI}}` | 🏥 |
| `{{DOMAIN}}` | hospital-reservation |
| `{{DURATION}}` | 4주 |
| `{{N}}` | 3 (팀원 수) |
| `{{PROJECT_ENV}}` | HOSPITAL (환경변수 prefix) |
| `{{project}}` | hospital (패키지·DB명 등 소문자) |
| `{{ENTITY_1}}`, `{{ENTITY_2}}` | PATIENT, RESERVATION ... |
| `{{ROLE_A}}`, `{{ROLE_B}}`, `{{ROLE_C}}` | STAFF, DOCTOR, NURSE, ADMIN |
| `{{MAIN_FEATURE_1}}` | 증상 기반 예약 추천 |
| `{{EXTERNAL}}` | CLAUDE / KAKAO / TOSS 등 |

**빠른 치환 스크립트 (bash)**

```bash
find templates -type f -name "*.md" -print0 | xargs -0 sed -i \
  -e 's/{{PROJECT_NAME}}/내_프로젝트/g' \
  -e 's/{{EMOJI}}/🚀/g' \
  -e 's/{{project}}/myproject/g' \
  -e 's/{{DURATION}}/4주/g'
```

---

## 사용 순서 (권장)

1. **가이드 숙지** — `프로젝트_문서_가이드.md`를 한 번 통독
2. **폴더 구조 복제** — 새 저장소에 `00_스케줄` ~ `16_운영_메뉴얼` 폴더 생성
3. **핵심 문서 3종 먼저** — 01 계획서 / 02 ERD / 07 요구사항
4. **기반 규칙 픽스** — 03 아키텍처 / 09 Git / 10 환경설정 / 12 코드리뷰
5. **개발 시작 직전** — 04 API / 05 시퀀스 / 06 화면 / 11 테스트
6. **개발 후반** — 13 테스트 보고서 / 14 배포 / 15 사용자 / 16 운영

---

## 원본 참고

- 레포: https://github.com/Tae0072/documents
- 권장 최신본: 계획서 v4.3 / ERD v4 / 아키텍처 v2 / API v5.2 / 시퀀스 v5 / 화면 v5.1 / Git v2 / 코드리뷰 v2
