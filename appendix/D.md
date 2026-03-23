# 부록 D — 권장 프로젝트 구조

Claude Code를 최대한 효과적으로 활용하기 위한 프로젝트 구성 방법을 설명합니다. 올바른 구조와 설정 파일을 갖춰두면 Claude가 프로젝트 맥락을 빠르게 파악하고 더 정확한 작업을 수행할 수 있습니다.

---

## 전체 구조

```
프로젝트 루트/
├── CLAUDE.md                        # 프로젝트 지침 (필수) — Claude가 가장 먼저 읽음
├── agent-progress.md                # 작업 진행 상황 추적
├── .gitignore                       # settings.local.json, .mcp.json 등 포함 권장
│
├── .claude/
│   ├── settings.json                # 프로젝트 공유 설정 (Git에 커밋)
│   ├── settings.local.json          # 로컬 전용 설정 (Git에서 제외 — API 키 등)
│   │
│   ├── skills/                      # 커스텀 슬래시 커맨드 정의
│   │   ├── commit.md                # /commit — 커밋 메시지 자동 생성
│   │   ├── review.md                # /review — 코드 리뷰
│   │   └── deploy.md                # /deploy — 배포 체크리스트
│   │
│   └── agents/                      # 커스텀 서브에이전트 정의
│       ├── code-reviewer.md         # 코드 리뷰 전담 에이전트
│       └── test-writer.md           # 테스트 작성 전담 에이전트
│
└── .mcp.json                        # MCP 서버 설정 (API 키 포함 시 gitignore 권장)
```

---

## 각 파일의 역할

### `CLAUDE.md` — 프로젝트 지침 (가장 중요)

Claude Code가 프로젝트를 열 때 가장 먼저 읽는 파일입니다. 빌드 명령어, 코드 컨벤션, 프로젝트 구조, 주의사항 등을 명시합니다. 이 파일이 잘 작성되어 있을수록 Claude의 작업 품질이 높아집니다.

**최소 포함 내용:**
- 빌드/실행/테스트 명령어
- 프로젝트 디렉토리 구조 설명
- 코드 컨벤션 및 네이밍 규칙
- 절대 하면 안 되는 것 (`.env` 커밋 금지 등)

---

### `agent-progress.md` — 진행 상황 추적

장기 프로젝트에서 Claude와 여러 세션에 걸쳐 작업할 때 현재 진행 단계를 기록합니다. 새 세션을 시작할 때 Claude가 이전 작업 상태를 즉시 파악할 수 있습니다.

---

### `.claude/settings.json` — 프로젝트 설정

권한 규칙, 훅, 환경 변수 등 프로젝트 전체에 적용될 설정을 정의합니다. Git에 커밋해서 팀원 전체가 동일한 환경을 사용합니다.

---

### `.claude/settings.local.json` — 로컬 전용 설정

개인 API 키, 로컬 경로, 개발자별 환경 변수 등 공유하면 안 되는 설정을 저장합니다. 반드시 `.gitignore`에 추가해야 합니다.

---

### `.claude/skills/` — 커스텀 슬래시 커맨드

자주 사용하는 작업 패턴을 슬래시 커맨드로 정의합니다. 각 `.md` 파일의 이름이 커맨드 이름이 됩니다.

예: `.claude/skills/commit.md` → `/commit` 으로 호출

---

### `.claude/agents/` — 커스텀 서브에이전트

특정 역할에 특화된 에이전트를 정의합니다. 코드 리뷰어, 테스트 작성자, 문서화 담당자 등 역할을 분리해 품질을 높일 수 있습니다.

---

### `.mcp.json` — MCP 서버 설정

Model Context Protocol 서버 연결 정보를 담습니다. API 키가 포함될 경우 `.gitignore`에 추가하거나 환경 변수로 키를 분리합니다.

---

## CLAUDE.md 템플릿

```markdown
# [프로젝트명]

## 빌드 명령어
- 개발 서버: `npm run dev`
- 빌드: `npm run build`
- 테스트: `npm test`
- 테스트 (단일 파일): `npm test -- --testPathPattern="파일명"`
- 린트: `npm run lint`
- 린트 자동 수정: `npm run lint:fix`
- 타입 체크: `npm run type-check`

## 프로젝트 구조
- `src/` — 소스 코드
  - `src/components/` — 재사용 UI 컴포넌트
  - `src/pages/` — 페이지 단위 컴포넌트
  - `src/hooks/` — 커스텀 훅
  - `src/utils/` — 유틸 함수
  - `src/types/` — TypeScript 타입 정의
- `tests/` — 테스트 파일 (소스와 동일한 구조 유지)
- `docs/` — 문서
- `public/` — 정적 파일

## 기술 스택
- Frontend: React 18 + TypeScript + Tailwind CSS
- 상태 관리: TanStack Query (서버 상태), Zustand (클라이언트 상태)
- 테스트: Vitest + React Testing Library
- 빌드: Vite

## 코드 컨벤션
- TypeScript 우선, `any` 타입 사용 금지
- 들여쓰기: 2칸 스페이스
- 함수명: camelCase, 컴포넌트명: PascalCase
- 파일명: kebab-case (컴포넌트는 PascalCase)
- 한 파일에 하나의 컴포넌트/함수만 export
- 주석은 "왜"를 설명 (무엇은 코드가 설명)

## 중요 사항 (반드시 지켜야 함)
- `.env` 파일 절대 커밋 금지
- `console.log` 는 디버그 후 반드시 제거
- PR 전 반드시 `npm test` 와 `npm run type-check` 통과 확인
- `node_modules/`, `dist/`, `.env*` 파일 수정 금지
```

---

## settings.json 템플릿

```json
{
  "permissions": {
    "defaultMode": "default",
    "allow": [
      "Bash(npm run *)",
      "Bash(npx *)",
      "Bash(git status)",
      "Bash(git diff *)",
      "Bash(git log *)",
      "Bash(git add *)",
      "Bash(git commit *)"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push --force*)",
      "Edit(.env)",
      "Edit(.env.*)",
      "Write(.env)",
      "Write(.env.*)"
    ]
  },
  "hooks": {
    "PreToolUse": [{
      "matcher": "Write|Edit",
      "hooks": [{
        "type": "command",
        "command": "if echo \"$FILE_PATH\" | grep -qE '(\\.env|\\.pem|\\.key)'; then echo '보안 파일 수정 불가: '$FILE_PATH; exit 2; fi"
      }]
    }],
    "PostToolUse": [{
      "matcher": "Edit|Write",
      "hooks": [{
        "type": "command",
        "command": "if echo \"$FILE_PATH\" | grep -qE '\\.(js|ts|jsx|tsx|css|json|md)$'; then npx prettier --write \"$FILE_PATH\" 2>/dev/null || true; fi"
      }]
    }]
  },
  "env": {
    "NODE_ENV": "development"
  }
}
```

---

## skills/ 파일 예시

### `.claude/skills/commit.md`

```markdown
현재 스테이징된 변경사항을 분석해 커밋 메시지를 작성해줘.

1. `git diff --staged` 로 변경 내용 확인
2. 변경의 목적과 영향을 파악
3. 아래 형식으로 커밋 메시지 작성:
   - 제목: 50자 이내, 현재형 동사로 시작 (feat/fix/refactor/docs/test/chore)
   - 본문: 변경 이유와 영향 설명 (필요 시)
4. 메시지 확인 후 `git commit -m "..."` 실행
```

### `.claude/skills/review.md`

```markdown
현재 변경사항을 코드 리뷰해줘.

아래 항목을 체크해서 마크다운으로 보고해줘:
- [ ] 버그 및 로직 오류
- [ ] 보안 취약점 (SQL 인젝션, XSS, 인증 누락 등)
- [ ] 성능 문제 (불필요한 반복, N+1 쿼리 등)
- [ ] 코드 가독성 및 네이밍
- [ ] 테스트 커버리지 누락
- [ ] 타입 안전성
```

---

## agents/ 파일 예시

### `.claude/agents/code-reviewer.md`

```markdown
---
name: code-reviewer
description: 코드 품질과 보안에 집중하는 리뷰 전담 에이전트
---

당신은 시니어 개발자이자 보안 전문가입니다.
코드 리뷰 시 다음 우선순위로 검토합니다:

1. 보안 취약점 (최우선)
2. 버그 및 로직 오류
3. 성능 문제
4. 코드 품질 및 유지보수성

항상 구체적인 수정 방법과 예시 코드를 제시합니다.
칭찬보다 개선점에 집중합니다.
```

---

## .gitignore 추가 권장 항목

```gitignore
# Claude Code 로컬 설정 (개인 API 키, 로컬 경로 포함 가능)
.claude/settings.local.json

# MCP 설정 (API 키 포함 시)
.mcp.json

# Claude 세션/로그 (선택)
.claude/session.log
.claude/audit.log
```

---

## 빠른 시작 체크리스트

새 프로젝트에서 Claude Code 환경 설정 시 아래 순서로 진행합니다:

- [ ] `CLAUDE.md` 작성 (빌드 명령어, 구조, 컨벤션, 주의사항)
- [ ] `.claude/settings.json` 생성 (권한 규칙, 훅 설정)
- [ ] `.claude/settings.local.json` 생성 (개인 API 키 등)
- [ ] `.gitignore`에 `settings.local.json` 추가
- [ ] 자주 쓸 커스텀 커맨드 `skills/` 폴더에 추가
- [ ] `agent-progress.md` 초기화 (장기 프로젝트의 경우)
- [ ] MCP 서버 필요 시 `.mcp.json` 설정
