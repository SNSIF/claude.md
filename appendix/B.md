# 부록 B — 환경 변수 목록

Claude Code의 동작을 제어하는 환경 변수를 정리한 참고 자료입니다. 변수는 쉘에서 직접 설정하거나 `settings.json`의 `env` 블록에 등록해 영구 적용할 수 있습니다.

---

## 핵심 변수

| 변수명 | 기본값 | 설명 |
|--------|--------|------|
| `ANTHROPIC_API_KEY` | — | Anthropic API 키 **(필수)**. 이 값이 없으면 Claude Code가 동작하지 않음 |
| `ANTHROPIC_BASE_URL` | `https://api.anthropic.com` | API 엔드포인트 오버라이드. 프록시나 사내 게이트웨이 사용 시 변경 |
| `NODE_ENV` | — | `development` / `production` / `test` 환경 구분. 일부 내부 동작에 영향 |

---

## 디버그 & 개발

| 변수명 | 기본값 | 설명 |
|--------|--------|------|
| `CLAUDE_CODE_DEBUG` | `false` | `true`로 설정 시 상세 디버그 로그 출력. 문제 진단 시 활성화 |
| `CLAUDE_CODE_SIMPLE` | `false` | `true`로 설정 시 간단 모드 활성화. 초기화 속도가 빨라지며 고급 기능 일부 비활성화 |

---

## 메모리 & 컨텍스트

| 변수명 | 기본값 | 설명 |
|--------|--------|------|
| `CLAUDE_CODE_DISABLE_AUTO_MEMORY` | `false` | `true`로 설정 시 대화 내용 자동 저장 비활성화. 민감한 작업 환경에서 유용 |
| `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` | `95` | 컨텍스트 윈도우가 이 비율(%)을 초과하면 자동 압축(compaction) 시작. 값을 낮출수록 더 일찍 압축 |
| `MAX_THINKING_TOKENS` | — | 확장 사고(extended thinking) 기능에서 사용할 최대 토큰 수 지정 |

---

## 작업 & 성능

| 변수명 | 기본값 | 설명 |
|--------|--------|------|
| `CLAUDE_CODE_DISABLE_BACKGROUND_TASKS` | `false` | `true`로 설정 시 백그라운드 작업(파일 인덱싱 등) 비활성화. 저사양 환경에서 리소스 절약 |

---

## AWS Bedrock 사용 시 추가 변수

AWS Bedrock을 통해 Claude API를 사용하는 경우 아래 변수를 설정합니다.

| 변수명 | 설명 |
|--------|------|
| `AWS_ACCESS_KEY_ID` | AWS 액세스 키 ID |
| `AWS_SECRET_ACCESS_KEY` | AWS 시크릿 액세스 키 |
| `AWS_SESSION_TOKEN` | 임시 세션 토큰 (STS 사용 시) |
| `AWS_REGION` | AWS 리전 (예: `us-east-1`, `ap-northeast-2`) |
| `ANTHROPIC_BEDROCK_BASE_URL` | Bedrock 엔드포인트 오버라이드 (선택) |

**설정 예시:**
```bash
export AWS_ACCESS_KEY_ID="AKIA..."
export AWS_SECRET_ACCESS_KEY="wJalr..."
export AWS_REGION="us-east-1"
```

---

## Google Vertex AI 사용 시 추가 변수

Google Cloud Vertex AI를 통해 Claude를 사용하는 경우 아래 변수를 설정합니다.

| 변수명 | 설명 |
|--------|------|
| `GOOGLE_APPLICATION_CREDENTIALS` | 서비스 계정 키 JSON 파일 경로 |
| `GOOGLE_CLOUD_PROJECT` | GCP 프로젝트 ID |
| `GOOGLE_CLOUD_REGION` | Vertex AI 리전 (예: `us-central1`) |
| `ANTHROPIC_VERTEX_BASE_URL` | Vertex AI 엔드포인트 오버라이드 (선택) |
| `CLOUD_ML_REGION` | `GOOGLE_CLOUD_REGION` 대체 변수 |

**설정 예시:**
```bash
export GOOGLE_APPLICATION_CREDENTIALS="/path/to/service-account.json"
export GOOGLE_CLOUD_PROJECT="my-gcp-project"
export GOOGLE_CLOUD_REGION="us-central1"
```

---

## 설정 방법

### 방법 1: 임시 설정 (현재 셸 세션에만 적용)

```bash
# 단일 변수 설정
export CLAUDE_CODE_DEBUG=true

# 여러 변수 한꺼번에 설정
export ANTHROPIC_API_KEY="sk-ant-..."
export CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=80
export MAX_THINKING_TOKENS=10000
```

### 방법 2: 영구 설정 — 셸 프로파일에 추가

```bash
# ~/.bashrc 또는 ~/.zshrc에 추가
echo 'export ANTHROPIC_API_KEY="sk-ant-..."' >> ~/.bashrc
echo 'export CLAUDE_CODE_DEBUG=false' >> ~/.bashrc
source ~/.bashrc
```

### 방법 3: settings.json으로 영구 설정 (권장)

프로젝트별로 관리하거나 전역 설정을 원할 때 `settings.json`의 `env` 블록을 사용합니다.

**전역 설정** (`~/.claude/settings.json`):
```json
{
  "env": {
    "CLAUDE_CODE_DEBUG": "false",
    "CLAUDE_AUTOCOMPACT_PCT_OVERRIDE": "85",
    "MAX_THINKING_TOKENS": "8000"
  }
}
```

**프로젝트별 설정** (`.claude/settings.json`):
```json
{
  "env": {
    "NODE_ENV": "development",
    "CLAUDE_CODE_DISABLE_BACKGROUND_TASKS": "false"
  }
}
```

**로컬 전용 설정** (`.claude/settings.local.json` — git에 커밋하지 않음):
```json
{
  "env": {
    "ANTHROPIC_API_KEY": "sk-ant-개인키-여기에",
    "CLAUDE_CODE_DEBUG": "true"
  }
}
```

### 방법 4: 명령 실행 시 일회성 적용

```bash
# 특정 명령에만 디버그 모드 적용
CLAUDE_CODE_DEBUG=true claude "왜 이 훅이 동작 안 하지?"

# 자동 압축 임계값 낮춰서 1회 실행
CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=70 claude "긴 문서 분석해줘"
```

---

## 우선순위 (낮음 → 높음)

환경 변수가 여러 곳에 정의된 경우 아래 순서로 우선 적용됩니다:

1. 전역 `settings.json` (`~/.claude/settings.json`)
2. 프로젝트 `settings.json` (`.claude/settings.json`)
3. 로컬 `settings.local.json` (`.claude/settings.local.json`)
4. 셸 환경 변수 (`export` 또는 명령 앞에 붙인 값)

---

## 보안 주의사항

- `ANTHROPIC_API_KEY`는 절대 코드에 하드코딩하거나 Git에 커밋하지 않습니다.
- `.claude/settings.local.json`은 반드시 `.gitignore`에 추가합니다.
- CI/CD 환경에서는 플랫폼의 시크릿 관리 기능(GitHub Secrets, GitLab CI Variables 등)을 사용합니다.

```bash
# .gitignore에 추가 권장
.claude/settings.local.json
.env
.env.local
```
