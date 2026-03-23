# 부록 A — CLI 플래그 치트시트

Claude Code 명령줄 인터페이스에서 사용할 수 있는 주요 플래그를 카테고리별로 정리한 참고 자료입니다.

---

## 세션 관련

| 플래그 | 설명 | 예시 |
|--------|------|------|
| (없음) | 대화형 세션 시작 | `claude` |
| `"task"` | 초기 프롬프트로 세션 시작 | `claude "버그 찾아줘"` |
| `-p "query"` | 1회 실행 후 종료 (print 모드) | `claude -p "이 코드 요약해줘"` |
| `-c` | 가장 최근 세션 계속 이어서 진행 | `claude -c` |
| `-r "id"` | 특정 세션 ID로 재개 | `claude -r "my-session"` |

**사용 예시:**
```bash
# 대화형 세션 시작
claude

# 특정 작업을 시작 프롬프트와 함께 세션 열기
claude "src/auth.ts 파일의 버그를 찾아줘"

# 1회 실행 후 종료 — 파이프라인이나 스크립트에 유용
claude -p "README.md를 한국어로 요약해줘"

# 어제 작업하던 세션 이어서 계속
claude -c

# 특정 세션 ID로 재개
claude -r "2024-03-20-refactor"
```

---

## 모델 & 성능

| 플래그 | 설명 | 예시 |
|--------|------|------|
| `--model` | 사용할 Claude 모델 선택 | `--model claude-opus-4-5` |
| `--effort` | 응답 노력 수준 설정 (`low` / `medium` / `high`) | `--effort high` |
| `--max-turns` | 에이전트 루프 최대 턴 수 제한 | `--max-turns 5` |
| `--max-budget-usd` | 세션당 최대 비용 제한 (USD) | `--max-budget-usd 1.00` |

**사용 예시:**
```bash
# 고성능 모델로 복잡한 리팩터링 작업
claude --model claude-opus-4-5 --effort high "전체 인증 모듈 리팩터링해줘"

# 간단한 작업에는 빠른 모델 + 낮은 노력 수준
claude --model claude-haiku-3-5 --effort low -p "이 변수명 영어로 바꿔줘: 사용자이름"

# 자동화 파이프라인에서 무한 루프 방지
claude --max-turns 10 "테스트 통과할 때까지 코드 수정해줘"

# 비용 초과 방지 (실험적 작업 시 유용)
claude --max-budget-usd 0.50 "이 프로젝트 전체 분석해줘"
```

---

## 권한 & 보안

| 플래그 | 설명 |
|--------|------|
| `--permission-mode` | 권한 모드 설정 (`default` / `acceptEdits` / `bypassPermissions` / `plan`) |
| `--sandbox` | OS 수준 격리 환경에서 실행 (파일시스템, 네트워크 제한) |
| `--allowedTools` | 허용할 도구 목록을 명시적으로 지정 |
| `--disallowedTools` | 금지할 도구 목록 지정 |

**사용 예시:**
```bash
# 파일 편집 자동 승인 모드 (신뢰하는 프로젝트에서 빠른 작업 시)
claude --permission-mode acceptEdits "전체 파일에서 console.log 제거해줘"

# 샌드박스 모드 — 인터넷 차단, 파일시스템 격리
claude --sandbox "이 스크립트 실행해서 결과 알려줘"

# 읽기 도구만 허용 (분석 전용, 수정 불가)
claude --allowedTools "Read,Glob,Grep" "이 프로젝트 아키텍처 분석해줘"

# Bash 실행 금지 (파일 편집만 허용)
claude --disallowedTools "Bash" "타입스크립트 오류 수정해줘"

# 계획만 수립, 실제 실행 없음
claude --permission-mode plan "데이터베이스 마이그레이션 전략 세워줘"
```

---

## 출력 포맷

| 플래그 | 설명 |
|--------|------|
| `--output-format text` | 텍스트 형식 출력 (기본값, 사람이 읽기 편함) |
| `--output-format json` | JSON 구조로 결과 출력 (프로그래밍 처리에 적합) |
| `--output-format stream-json` | 실시간 스트리밍 JSON 출력 (대용량 처리 시 유용) |
| `--json-schema '...'` | JSON 스키마를 지정해 구조화된 응답 강제 |

**사용 예시:**
```bash
# JSON 출력 — 다른 CLI 도구와 파이프라인 구성
claude -p "이 프로젝트의 의존성을 JSON 배열로 나열해줘" --output-format json

# 스트리밍 JSON — 장시간 작업의 실시간 처리
claude --output-format stream-json "전체 코드베이스 문서화해줘" | jq '.content'

# JSON 스키마로 구조화된 응답 강제
claude -p "버그 목록을 추출해줘" --json-schema '{
  "type": "array",
  "items": {
    "type": "object",
    "properties": {
      "file": {"type": "string"},
      "line": {"type": "number"},
      "description": {"type": "string"}
    }
  }
}'
```

---

## 에이전트

| 플래그 | 설명 |
|--------|------|
| `--agent` | 특정 커스텀 에이전트 지정 (`.claude/agents/` 내 정의된 에이전트) |
| `--worktree` | Git Worktree 환경에서 격리된 작업 실행 |
| `--remote-control` | 원격 제어 활성화 (외부 프로세스에서 세션 조작 가능) |
| `--remote` | 웹 브라우저 세션으로 실행 (claude.ai 연동) |

**사용 예시:**
```bash
# 코드 리뷰 전용 에이전트 사용
claude --agent code-reviewer "PR #42 리뷰해줘"

# Git Worktree에서 격리 작업 (메인 브랜치 영향 없음)
claude --worktree feature/new-api "새 API 엔드포인트 구현해줘"

# 원격 제어 모드로 세션 시작
claude --remote-control
```

---

## 기타

| 플래그 | 설명 |
|--------|------|
| `--version` | 현재 설치된 Claude Code 버전 확인 |
| `--help` | 사용 가능한 모든 플래그와 도움말 표시 |
| `update` | Claude Code를 최신 버전으로 업데이트 |
| `--debug` | 디버그 모드 활성화 (상세 로그 출력) |

**사용 예시:**
```bash
# 버전 확인
claude --version

# 전체 도움말
claude --help

# 업데이트
claude update

# 디버그 모드로 문제 진단
claude --debug "왜 이 파일을 못 읽지?"
```

---

## 파이프라인 활용 예시

`-p` 플래그를 활용하면 Unix 파이프라인에 Claude를 자연스럽게 통합할 수 있습니다.

```bash
# 에러 로그 분석
cat error.log | claude -p "이 에러 원인 분석해줘"

# 커밋 메시지 자동 생성
git diff | claude -p "커밋 메시지 작성해줘"

# 보안 취약 패키지 검사
cat requirements.txt | claude -p "보안 취약 패키지 있나 확인해줘"

# 코드 리뷰 자동화
git diff HEAD~1 | claude -p "이 변경사항 코드 리뷰해줘" --output-format json

# 빌드 에러 자동 해석
npm run build 2>&1 | claude -p "빌드 에러 원인과 해결 방법 알려줘"

# SQL 쿼리 최적화
cat slow_query.sql | claude -p "이 쿼리 성능 최적화해줘"

# 번역 파이프라인
cat en.json | claude -p "이 i18n JSON을 한국어로 번역해줘" --output-format json > ko.json
```

---

## 자주 쓰는 조합

```bash
# CI/CD에서 테스트 실패 시 자동 분석
npm test 2>&1 | claude -p "테스트 실패 원인 분석하고 수정 방법 제시해줘" --output-format text

# 대용량 코드베이스 빠른 분석 (비용 제한 포함)
claude --model claude-haiku-3-5 --max-budget-usd 0.10 -p "이 프로젝트의 주요 문제점 3가지만 짚어줘"

# 안전한 자동화 (쓰기 권한 없음, 최대 3턴)
claude --allowedTools "Read,Glob,Grep" --max-turns 3 "보안 취약점 스캔해줘"
```
