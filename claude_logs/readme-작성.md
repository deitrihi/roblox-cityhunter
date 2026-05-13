# readme-작성
날짜: 2026-05-13

## 세션 개요
CityHunter 프로젝트의 README.md 파일을 PROJECT.md 기반으로 작성한다.

## 작업 로그

### README.md 작성
- 요청: PROJECT.md를 참고해서 README.md 파일을 작성해 달라
- 행동: PROJECT.md 전체 내용 열람 후 README.md 신규 작성
- 산출물: `README.md` (프로젝트 루트)
- 결정: 게임 개요, 게임 흐름, 조작 방법, 핵심 수치(GunServer/StaminaSystem/TPSCamera), 더미 NPC, 랭킹 시스템, 서비스 구조, 저장소 구성, 개발 환경 섹션으로 구성
- 참고: 기존 README.md에 `roblox-cityhunter` 한 줄만 있어 전면 교체

### 세션 로그 생성 (사후)
- 요청: CLAUDE.md 세션 로그 지침이 확인되는지 질문
- 확인: 전역 CLAUDE.md 섹션 11에 로그 지침 존재 확인
- 행동: 누락된 세션 로그를 소급 생성

### PROJECT.md → README.md 자동 동기화 훅 설정
- 요청: PROJECT.md 수정 시 README.md도 자동으로 반영되게 해달라
- 행동: `.claude/settings.json` 신규 생성, PostToolUse 훅 추가
- 결정: `agent` 훅 타입 사용 — 셸 명령으로는 내용 차이를 지능적으로 반영할 수 없으므로 Claude 에이전트가 두 파일을 비교해 업데이트
- 매처: `Edit|Write`, if 필터: `Edit(*PROJECT.md*)|Write(*PROJECT.md*)`
- 타임아웃: 120초
