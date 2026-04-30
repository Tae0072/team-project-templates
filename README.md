# 팀 프로젝트 문서 템플릿

팀 프로젝트를 시작할 때 바로 복사해서 쓸 수 있는 문서 템플릿 모음입니다. 기획, ERD, API, 화면 정의, Git 규칙, 테스트, 코드 리뷰, 배포까지 프로젝트 초반에 합의해야 할 내용을 한 저장소에 정리했습니다.

## 빠른 시작

1. `프로젝트_문서_가이드.md`를 먼저 읽고 문서 작성 순서를 정합니다.
2. `templates/`에서 필요한 템플릿을 프로젝트 저장소로 복사합니다.
3. `{{PROJECT_NAME}}`, `{{DOMAIN}}`, `{{ENTITY_1}}` 같은 자리표시자를 팀 프로젝트에 맞게 치환합니다.
4. 01 계획서, 02 ERD, 07 요구사항을 먼저 작성한 뒤 개발 규칙과 환경 설정 문서를 확정합니다.

## 구성

| 경로 | 용도 |
| --- | --- |
| [`프로젝트_문서_가이드.md`](./프로젝트_문서_가이드.md) | 전체 문서 체계, 작성 순서, 체크리스트 |
| [`templates/00_개발_일정_총괄표_template.md`](./templates/00_개발_일정_총괄표_template.md) | 일정, 마일스톤, Critical Path |
| [`templates/01_프로젝트_계획서_template.md`](./templates/01_프로젝트_계획서_template.md) | 목표, 범위, WBS, 리스크 |
| [`templates/02_ERD_문서_template.md`](./templates/02_ERD_문서_template.md) | ERD, 테이블 정의, 관계, 상태 코드 |
| [`templates/03_아키텍처_정의서_template.md`](./templates/03_아키텍처_정의서_template.md) | 패키지 구조, 레이어, 모듈 책임 |
| [`templates/04_API_명세서_template.md`](./templates/04_API_명세서_template.md) | API 공통 규칙, 엔드포인트 명세 |
| [`templates/05_시퀀스_다이어그램_template.md`](./templates/05_시퀀스_다이어그램_template.md) | 주요 흐름 Mermaid 시퀀스 |
| [`templates/06_화면_기능_정의서_template.md`](./templates/06_화면_기능_정의서_template.md) | 화면별 기능, 입력값, 검증 규칙 |
| [`templates/07_요구사항_정의서_template.md`](./templates/07_요구사항_정의서_template.md) | 필수 요구사항, 우선순위, 수용 기준 |
| [`templates/09_Git_규칙_template.md`](./templates/09_Git_규칙_template.md) | 브랜치, 커밋, PR 운영 규칙 |
| [`templates/10_환경_설정_템플릿/`](./templates/10_환경_설정_템플릿/) | Spring 환경 설정 예시 |
| [`templates/11_테스트_전략서_template.md`](./templates/11_테스트_전략서_template.md) | 테스트 범위, 담당, CI 기준 |
| [`templates/12_코드_리뷰_규칙_template.md`](./templates/12_코드_리뷰_규칙_template.md) | PR 작성법, 리뷰 체크리스트, merge 조건 |
| [`templates/14_배포_가이드_template.md`](./templates/14_배포_가이드_template.md) | Docker Compose, Nginx, SSL 배포 절차 |
| [`templates/AGENTS.md.example`](./templates/AGENTS.md.example) | Codex, Cursor, Claude Code 등 AI 에이전트 협업 규칙 예시 |

## 권장 작성 순서

1. 킥오프: `01_프로젝트_계획서`, `07_요구사항_정의서`
2. 설계: `02_ERD_문서`, `03_아키텍처_정의서`, `06_화면_기능_정의서`
3. 개발 준비: `09_Git_규칙`, `10_환경_설정_템플릿`, `12_코드_리뷰_규칙`
4. 구현 직전: `04_API_명세서`, `05_시퀀스_다이어그램`, `11_테스트_전략서`
5. 배포 전: `14_배포_가이드`

## 자리표시자 치환

템플릿 안의 `{{...}}` 값은 프로젝트에 맞게 바꿔 사용합니다.

| 토큰 | 예시 |
| --- | --- |
| `{{PROJECT_NAME}}` | 병원 예약 시스템 |
| `{{EMOJI}}` | 🏥 |
| `{{DOMAIN}}` | hospital-reservation |
| `{{DURATION}}` | 3주 |
| `{{N}}` | 6 |
| `{{PROJECT_ENV}}` | HOSPITAL |
| `{{project}}` | hospital |
| `{{ENTITY_1}}`, `{{ENTITY_2}}` | MEMBER, RESERVATION |
| `{{ROLE_A}}`, `{{ROLE_B}}` | USER, ADMIN |

## AI 에이전트 협업

AI 도구를 함께 사용할 때는 `templates/AGENTS.md.example`을 실제 프로젝트 루트의 `AGENTS.md`로 복사한 뒤 팀 규칙에 맞게 수정하세요. 담당 영역, 금지 작업, 테스트 기준, 커밋 규칙을 명확히 적어두면 여러 도구를 함께 써도 작업 충돌을 줄일 수 있습니다.

## 활용 팁

- 문서는 한 번에 완성하려고 하기보다 개발 진행에 맞춰 계속 갱신합니다.
- PR을 만들 때 관련 문서 링크를 함께 적으면 리뷰 속도가 빨라집니다.
- ERD, API, 화면 정의가 바뀌면 테스트 전략과 배포 가이드도 함께 확인합니다.
- 팀 프로젝트 종료 후에는 작성한 문서를 포트폴리오 설명 자료로 재사용할 수 있습니다.
