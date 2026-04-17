# 프로젝트 작업 규칙 (Claude Code용)

## 언어
- 응답 및 커밋 메시지는 한국어
- 코드(Arduino C++) 주석·변수명 한국어 우선

## Git 워크플로우 — main 자동 반영
작업 완료 시 아래 순서로 동작하며, 사용자에게 머지 여부를 되묻지 않는다.

1. 지정된 `claude/*` feature 브랜치에 커밋하고 origin에 push 한다.
2. `mcp__github__create_pull_request` 로 **base=main**, head=feature 브랜치 PR을 즉시 생성한다.
3. PR은 GitHub Actions 워크플로우 `.github/workflows/auto-merge-claude.yml` 가 자동 squash 머지 한다.
4. 결과를 사용자에게 요약 보고한다 (PR 번호·머지 여부).

PR 본문에는 변경 요약과 테스트 체크리스트만 간결하게 적는다. 리뷰 요청·라벨 추가 불필요.

## 기타 규칙
- 이전 결정 사항은 `docs/context.md` 와 `docs/decisions.md` 참조.
- 설계 변경 시 `docs/wbs.md` 의 일정·상태도 함께 갱신.
- 추정 금지. 불확실하면 먼저 질문.
- 기초 설명 생략. 실무 핵심·트레이드오프 중심.
