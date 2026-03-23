# Deepdive 2 — settings.json 실전 설정

## 파일 위치 & 역할

```
~/.claude/settings.json                  # 전역 — 모든 프로젝트 공통
[project]/.claude/settings.json         # 프로젝트 — 팀 공유 (git 포함)
[project]/.claude/settings.local.json   # 로컬 — 나만의 오버라이드 (git 제외)
```

우선순위: `Managed < User(전역) < Project < Local`
충돌 시 Local이 이깁니다.

> `.gitignore`에 `.claude/settings.local.json` 추가 권장

---

## 전체 구조

```json
{
  "defaultMode": "acceptEdits",
  "permissions": {
    "allow": [],
    "deny": []
  },
  "env": {},
  "hooks": {}
}
```

---

## defaultMode

| 값 | 동작 |
|----|------|
| `"default"` | 모든 작업 승인 요청 |
| `"acceptEdits"` | 파일 작업 자동 승인, Bash는 승인 요청 |
| `"plan"` | 계획만 수립, 실행 안 함 |
| `"bypassPermissions"` | 전부 자동 승인 (CI 전용) |

일반 개발 시 `"acceptEdits"` 권장. 파일 수정 승인 피로를 없애면서 Bash는 통제 유지.

---

## permissions

### allow 패턴 문법

| 패턴 | 의미 |
|------|------|
| `"Edit"` | Edit 도구 전체 허용 |
| `"Bash(npm run *)"` | npm run 으로 시작하는 명령 허용 |
| `"Bash(git commit *)"` | git commit 허용 |

### deny 우선순위

`allow`와 `deny`가 겹치면 **deny가 우선**입니다.

```json
"allow": ["Bash(git *)"],
"deny":  ["Bash(git push --force *)"]
// → git push --force 만 차단, 나머지 git 명령은 허용
```

---

## env

Claude가 Bash 명령을 실행할 때 주입되는 환경변수입니다.

```json
{
  "env": {
    "NODE_ENV": "development",
    "API_BASE_URL": "http://localhost:8000"
  }
}
```

> DB URL, API 키 같은 민감한 값은 `settings.local.json`에만 넣습니다.

---

## hooks

파일 수정 후 자동으로 실행할 명령을 등록합니다.

```json
"hooks": {
  "PostToolUse": [
    {
      "matcher": "Edit|Write",
      "hooks": [{ "type": "command", "command": "..." }]
    }
  ]
}
```

| 이벤트 | 시점 |
|--------|------|
| `PreToolUse` | 도구 실행 직전 |
| `PostToolUse` | 도구 실행 직후 |
| `Stop` | Claude 응답 완료 후 |
| `SessionStart` | 세션 시작 시 |

---

---

# 완전체 예시

---

## `~/.claude/settings.json` — 전역

모든 프로젝트에 공통 적용. 기본 안전망 역할.

```json
{
  "defaultMode": "acceptEdits",
  "permissions": {
    "allow": [
      "Edit",
      "Write",
      "Read",
      "Glob",
      "Grep"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push --force *)",
      "Bash(git reset --hard *)"
    ]
  }
}
```

---

## `[project]/.claude/settings.json` — 프로젝트 공유 (git 포함)

팀 전체가 쓰는 허용 범위 + 포맷터 자동화 훅.

```json
{
  "defaultMode": "acceptEdits",
  "permissions": {
    "allow": [
      "Edit",
      "Write",
      "Read",
      "Glob",
      "Grep",
      "Bash(npm run *)",
      "Bash(npm install *)",
      "Bash(npx tsc *)",
      "Bash(npx eslint *)",
      "Bash(npx prettier *)",
      "Bash(pip install *)",
      "Bash(python *)",
      "Bash(pytest *)",
      "Bash(git status)",
      "Bash(git diff *)",
      "Bash(git log *)",
      "Bash(git add *)",
      "Bash(git commit *)",
      "Bash(git checkout *)",
      "Bash(git branch *)",
      "Bash(git stash *)",
      "Bash(git fetch *)",
      "Bash(git pull *)"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push --force *)",
      "Bash(git reset --hard *)",
      "Bash(DROP *)",
      "Bash(TRUNCATE *)"
    ]
  },
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "FILE=\"$CLAUDE_TOOL_INPUT_FILE_PATH\"; case \"$FILE\" in *.ts|*.tsx|*.js|*.jsx) npx prettier --write \"$FILE\" 2>/dev/null || true; npx eslint --fix \"$FILE\" 2>/dev/null || true ;; *.py) isort \"$FILE\" 2>/dev/null || true; black \"$FILE\" 2>/dev/null || true; flake8 \"$FILE\" 2>/dev/null || true ;; esac"
          }
        ]
      }
    ]
  }
}
```

훅 로직을 풀어보면:

```bash
FILE="$CLAUDE_TOOL_INPUT_FILE_PATH"

case "$FILE" in
  # JS / TS
  *.ts|*.tsx|*.js|*.jsx)
    npx prettier --write "$FILE" 2>/dev/null || true  # 포맷터
    npx eslint --fix "$FILE"    2>/dev/null || true  # 린터 자동 수정
    ;;

  # Python
  *.py)
    isort "$FILE"  2>/dev/null || true  # import 정렬
    black "$FILE"  2>/dev/null || true  # 포맷터
    flake8 "$FILE" 2>/dev/null || true  # 린터 (출력만, 수정 없음)
    ;;
esac
```

> `|| true` 는 포맷터 오류가 있어도 Claude 작업이 중단되지 않게 합니다.

---

## `[project]/.claude/settings.local.json` — 개인 로컬 (git 제외)

개인 환경변수, 로컬에서만 쓸 권한 오버라이드.

```json
{
  "defaultMode": "bypassPermissions",
  "env": {
    "DATABASE_URL": "postgresql://localhost:5432/mydb_dev",
    "OPENAI_API_KEY": "sk-..."
  }
}
```

---

## `.gitignore`

```gitignore
# Claude Code 개인 설정
.claude/settings.local.json
```
