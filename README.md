# Claude Code 전격 탐구 — 목차

> 기초부터 고급까지 단계별로 Claude Code를 탐구합니다.

---

## 1단계: 시작하기 (Getting Started)

| # | 주제 | 설명 |
|---|------|------|
| 1-1 | 설치 및 인증 | Claude Code 설치, 로그인, 계정 연결 |
| 1-2 | 첫 대화 | 기본 인터페이스, 프롬프트 입력, 세션 관리 |
| 1-3 | 기본 명령어 | `claude`, `-p`, `-c`, `--help` 등 CLI 플래그 |

---

## 2단계: 파일 & 코드 작업 (File & Code Operations)

| # | 주제 | 설명 |
|---|------|------|
| 2-1 | 파일 읽기 (Read) | 파일 열기, 라인 범위 지정, 이미지/PDF 지원 |
| 2-2 | 파일 쓰기 (Write) | 새 파일 생성, 전체 덮어쓰기 |
| 2-3 | 파일 편집 (Edit) | 특정 부분만 수정, diff 방식 |
| 2-4 | 파일 검색 (Glob) | 패턴 매칭으로 파일 찾기 |
| 2-5 | 내용 검색 (Grep) | 정규표현식으로 코드 검색 |

---

## 3단계: Git 통합 (Git Integration)

| # | 주제 | 설명 |
|---|------|------|
| 3-1 | 기본 Git 명령 | status, diff, add, commit 자연어로 실행 |
| 3-2 | 브랜치 & PR | 브랜치 생성, PR 초안 작성 |
| 3-3 | Worktree | 여러 브랜치 병렬 작업 |

---

## 4단계: 슬래시 커맨드 (Slash Commands)

| # | 주제 | 설명 |
|---|------|------|
| 4-1 | 내장 커맨드 | `/help`, `/clear`, `/compact`, `/memory`, `/model` 등 |
| 4-2 | 커스텀 스킬 | `.claude/skills/`에 Markdown으로 나만의 커맨드 만들기 |
| 4-3 | 스킬 호출 방식 | 명시적 호출 vs 자동 위임 |

---

## 5단계: CLAUDE.md & 메모리 (Memory System)

| # | 주제 | 설명 |
|---|------|------|
| 5-1 | CLAUDE.md 작성 | 프로젝트 지침 파일, 임포트(`@`), 경로별 규칙 |
| 5-2 | 자동 메모리 | Claude가 학습한 내용을 자동 저장하는 방식 |
| 5-3 | 메모리 관리 | `/memory` 커맨드, 편집 및 삭제 |

---

## 6단계: 설정 파일 (Settings)

| # | 주제 | 설명 |
|---|------|------|
| 6-1 | 설정 계층 구조 | User → Project → Local → Managed 우선순위 |
| 6-2 | 권한 설정 | `allow`, `deny`, `ask` 규칙 작성 |
| 6-3 | 환경 변수 | `env` 블록으로 변수 주입 |
| 6-4 | 권한 모드 | `default`, `acceptEdits`, `plan`, `bypassPermissions` |

---

## 7단계: 훅 시스템 (Hooks)

| # | 주제 | 설명 |
|---|------|------|
| 7-1 | 훅 개념 | 라이프사이클 이벤트와 결정적 자동화 |
| 7-2 | 주요 이벤트 | `PreToolUse`, `PostToolUse`, `Stop`, `SessionStart` 등 |
| 7-3 | 훅 유형 | `command`, `prompt`, `agent`, `http` |
| 7-4 | 실전 훅 | 포맷터 자동 실행, 파일 보호, 알림 보내기 |

---

## 8단계: MCP 서버 (Model Context Protocol)

| # | 주제 | 설명 |
|---|------|------|
| 8-1 | MCP 개념 | Claude와 외부 서비스를 연결하는 표준 프로토콜 |
| 8-2 | 서버 설정 | `.mcp.json` 작성, `stdio` / `http` / `sse` 유형 |
| 8-3 | 설정 범위 | local, project, user, managed |
| 8-4 | 실전 활용 | GitHub, Slack, DB 연동 예시 |

---

## 9단계: 서브에이전트 (Sub-Agents)

| # | 주제 | 설명 |
|---|------|------|
| 9-1 | 내장 에이전트 | `Explore`, `Plan`, `general-purpose` |
| 9-2 | 커스텀 에이전트 | `.claude/agents/`에 에이전트 정의 파일 작성 |
| 9-3 | 에이전트 옵션 | 도구 제한, 권한 모드, 최대 턴, 메모리 |
| 9-4 | 에이전트 호출 | 자동 위임, `@mention`, 명시적 호출 |

---

## 10단계: IDE 통합 (IDE Integration)

| # | 주제 | 설명 |
|---|------|------|
| 10-1 | VS Code 확장 | 설치, 인라인 diff, 파일 @-mention |
| 10-2 | JetBrains 플러그인 | IntelliJ, PyCharm, WebStorm 연동 |
| 10-3 | 상태바 & 단축키 | 커스텀 키바인딩, 상태바 설정 |

---

## 11단계: 고급 기능 (Advanced)

| # | 주제 | 설명 |
|---|------|------|
| 11-1 | Plan Mode | 읽기 전용 계획 수립 후 승인 → 실행 |
| 11-2 | Extended Thinking | 복잡한 문제 깊은 사고, 노력 수준 조정 |
| 11-3 | Worktree & 병렬 작업 | 독립적인 브랜치에서 동시 진행 |
| 11-4 | 원격 제어 | 웹/모바일에서 로컬 세션 제어 |

---

## 12단계: Claude API & Anthropic SDK

| # | 주제 | 설명 |
|---|------|------|
| 12-1 | SDK 기본 | `@anthropic-ai/sdk` / `anthropic` 패키지 |
| 12-2 | Tool Use | 함수 호출(Function Calling) |
| 12-3 | Streaming | 실시간 응답 스트리밍 |
| 12-4 | 구조화된 출력 | JSON Schema 기반 응답 |
| 12-5 | 에이전트 앱 만들기 | Claude API로 나만의 AI 앱 구축 |

---

## 부록

| 항목 | 내용 |
|------|------|
| A | 주요 CLI 플래그 치트시트 |
| B | 환경 변수 목록 |
| C | 자주 쓰는 훅 예제 모음 |
| D | 권장 프로젝트 구조 |
| E | 공식 문서 및 참고 링크 |

---

> **진행 방식**: 각 단계를 순서대로 실습하며, 단계별 예제 파일을 함께 작성합니다.
