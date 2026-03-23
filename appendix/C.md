# 부록 C — 자주 쓰는 훅 예제 모음

Claude Code 훅(Hook)은 특정 이벤트 발생 시 자동으로 실행되는 셸 명령입니다. 아래 예제들은 `.claude/settings.json`에 복붙해서 바로 사용할 수 있습니다.

---

## 훅 이벤트 종류

| 이벤트 | 발생 시점 |
|--------|-----------|
| `PreToolUse` | Claude가 도구를 실행하기 직전 |
| `PostToolUse` | Claude가 도구를 실행한 직후 |
| `Stop` | Claude가 응답을 완료했을 때 |
| `SessionStart` | 새 세션이 시작될 때 |
| `SessionEnd` | 세션이 종료될 때 |

> `PreToolUse` 훅에서 `exit 2`를 반환하면 해당 도구 실행을 **차단**할 수 있습니다.

---

## 1. 코드 포맷팅 — Prettier (JavaScript/TypeScript)

파일을 편집하거나 새로 작성할 때마다 Prettier로 자동 포맷합니다.

```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Edit|Write",
      "hooks": [{
        "type": "command",
        "command": "npx prettier --write \"$FILE_PATH\" 2>/dev/null || true"
      }]
    }]
  }
}
```

**적용 범위 좁히기 — JS/TS 파일만:**
```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Edit|Write",
      "hooks": [{
        "type": "command",
        "command": "if echo \"$FILE_PATH\" | grep -qE '\\.(js|ts|jsx|tsx|css|json)$'; then npx prettier --write \"$FILE_PATH\" 2>/dev/null || true; fi"
      }]
    }]
  }
}
```

---

## 2. 코드 포맷팅 — Python (black + isort)

Python 파일 저장 시 black으로 스타일을 정리하고 isort로 import 순서를 정렬합니다.

```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Edit|Write",
      "hooks": [{
        "type": "command",
        "command": "python -m black \"$FILE_PATH\" 2>/dev/null; python -m isort \"$FILE_PATH\" 2>/dev/null; true"
      }]
    }]
  }
}
```

**Python 파일만 처리:**
```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Edit|Write",
      "hooks": [{
        "type": "command",
        "command": "if echo \"$FILE_PATH\" | grep -q '\\.py$'; then python -m black \"$FILE_PATH\" 2>/dev/null && python -m isort \"$FILE_PATH\" 2>/dev/null; fi; true"
      }]
    }]
  }
}
```

---

## 3. 민감 파일 보호 — .env 파일 수정 차단

`.env` 관련 파일을 Claude가 실수로 수정하지 못하도록 PreToolUse에서 차단합니다.

```json
{
  "hooks": {
    "PreToolUse": [{
      "matcher": "Write|Edit",
      "hooks": [{
        "type": "command",
        "command": "if echo \"$FILE_PATH\" | grep -q '\\.env'; then echo '.env 파일은 수정할 수 없습니다'; exit 2; fi"
      }]
    }]
  }
}
```

**확장 버전 — 여러 민감 파일 보호:**
```json
{
  "hooks": {
    "PreToolUse": [{
      "matcher": "Write|Edit",
      "hooks": [{
        "type": "command",
        "command": "if echo \"$FILE_PATH\" | grep -qE '(\\.env|\\.pem|\\.key|credentials|secrets)'; then echo '보안 파일은 수정할 수 없습니다: '$FILE_PATH; exit 2; fi"
      }]
    }]
  }
}
```

---

## 4. 작업 완료 알림 — macOS

Claude가 응답을 완료하면 macOS 시스템 알림을 표시합니다.

```json
{
  "hooks": {
    "Stop": [{
      "hooks": [{
        "type": "command",
        "command": "osascript -e 'display notification \"Claude가 작업을 완료했습니다\" with title \"Claude Code\"'"
      }]
    }]
  }
}
```

**소리 포함 버전:**
```json
{
  "hooks": {
    "Stop": [{
      "hooks": [{
        "type": "command",
        "command": "osascript -e 'display notification \"작업 완료!\" with title \"Claude Code\" sound name \"Glass\"'"
      }]
    }]
  }
}
```

---

## 5. 작업 완료 알림 — Windows

Windows 환경에서 PowerShell을 사용해 알림 메시지를 표시합니다.

```json
{
  "hooks": {
    "Stop": [{
      "hooks": [{
        "type": "command",
        "command": "powershell -Command \"Add-Type -AssemblyName System.Windows.Forms; [System.Windows.Forms.MessageBox]::Show('Claude 작업 완료!')\""
      }]
    }]
  }
}
```

**Windows 토스트 알림 (더 자연스러운 방식):**
```json
{
  "hooks": {
    "Stop": [{
      "hooks": [{
        "type": "command",
        "command": "powershell -Command \"[Windows.UI.Notifications.ToastNotificationManager, Windows.UI.Notifications, ContentType=WindowsRuntime] | Out-Null; $template = [Windows.UI.Notifications.ToastTemplateType]::ToastText01; $xml = [Windows.UI.Notifications.ToastNotificationManager]::GetTemplateContent($template); $xml.GetElementsByTagName('text')[0].AppendChild($xml.CreateTextNode('Claude Code 작업 완료!')) | Out-Null; $toast = [Windows.UI.Notifications.ToastNotification]::new($xml); [Windows.UI.Notifications.ToastNotificationManager]::CreateToastNotifier('Claude Code').Show($toast)\" 2>/dev/null || true"
      }]
    }]
  }
}
```

---

## 6. 세션 로깅

세션 시작 및 종료 시각을 로그 파일에 기록합니다. 작업 시간 추적에 유용합니다.

```json
{
  "hooks": {
    "SessionStart": [{
      "hooks": [{
        "type": "command",
        "command": "echo \"$(date '+%Y-%m-%d %H:%M:%S'): 세션 시작\" >> ~/.claude/session.log"
      }]
    }],
    "SessionEnd": [{
      "hooks": [{
        "type": "command",
        "command": "echo \"$(date '+%Y-%m-%d %H:%M:%S'): 세션 종료\" >> ~/.claude/session.log"
      }]
    }]
  }
}
```

**프로젝트 경로 포함 상세 로깅:**
```json
{
  "hooks": {
    "SessionStart": [{
      "hooks": [{
        "type": "command",
        "command": "echo \"$(date '+%Y-%m-%d %H:%M:%S') [시작] 디렉토리: $(pwd)\" >> ~/.claude/session.log"
      }]
    }],
    "SessionEnd": [{
      "hooks": [{
        "type": "command",
        "command": "echo \"$(date '+%Y-%m-%d %H:%M:%S') [종료] 디렉토리: $(pwd)\" >> ~/.claude/session.log"
      }]
    }]
  }
}
```

---

## 7. 자동 git add

파일을 편집하거나 생성할 때마다 자동으로 `git add`를 실행합니다.

```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Edit|Write",
      "hooks": [{
        "type": "command",
        "command": "git add \"$FILE_PATH\" 2>/dev/null || true"
      }]
    }]
  }
}
```

> **주의:** 민감한 파일이 실수로 스테이징될 수 있습니다. `.gitignore`를 꼼꼼히 관리하거나 아래 안전 버전을 사용하세요.

**안전 버전 — .env 제외:**
```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Edit|Write",
      "hooks": [{
        "type": "command",
        "command": "if ! echo \"$FILE_PATH\" | grep -qE '(\\.env|secrets|credentials)'; then git add \"$FILE_PATH\" 2>/dev/null || true; fi"
      }]
    }]
  }
}
```

---

## 8. ESLint 자동 수정

JavaScript/TypeScript 파일 편집 후 ESLint를 실행해 자동 수정 가능한 오류를 처리합니다.

```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Edit|Write",
      "hooks": [{
        "type": "command",
        "command": "if echo \"$FILE_PATH\" | grep -qE '\\.(js|ts|jsx|tsx)$'; then npx eslint --fix \"$FILE_PATH\" 2>/dev/null || true; fi"
      }]
    }]
  }
}
```

---

## 9. 파일 변경 감사 로그

어떤 파일이 언제 변경되었는지 감사 로그를 남깁니다. 팀 환경이나 중요 프로젝트에서 추적용으로 활용합니다.

```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Edit|Write|Bash",
      "hooks": [{
        "type": "command",
        "command": "echo \"$(date '+%Y-%m-%d %H:%M:%S') [$TOOL_NAME] $FILE_PATH\" >> ~/.claude/audit.log 2>/dev/null || true"
      }]
    }]
  }
}
```

---

## 10. 테스트 자동 실행 (경량)

특정 소스 파일이 수정될 때 관련 테스트를 자동으로 실행합니다.

```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Edit|Write",
      "hooks": [{
        "type": "command",
        "command": "if echo \"$FILE_PATH\" | grep -qE 'src/.*\\.(ts|js)$'; then npm test -- --testPathPattern=\"$(basename $FILE_PATH .ts)\" --passWithNoTests 2>/dev/null || true; fi"
      }]
    }]
  }
}
```

---

## 여러 훅 조합하기

실무에서는 여러 훅을 하나의 설정에 조합해서 사용합니다.

```json
{
  "hooks": {
    "PreToolUse": [{
      "matcher": "Write|Edit",
      "hooks": [{
        "type": "command",
        "command": "if echo \"$FILE_PATH\" | grep -qE '(\\.env|\\.pem|\\.key)'; then echo '보안 파일 수정 차단: '$FILE_PATH; exit 2; fi"
      }]
    }],
    "PostToolUse": [{
      "matcher": "Edit|Write",
      "hooks": [
        {
          "type": "command",
          "command": "if echo \"$FILE_PATH\" | grep -qE '\\.(js|ts|jsx|tsx|json|css)$'; then npx prettier --write \"$FILE_PATH\" 2>/dev/null || true; fi"
        },
        {
          "type": "command",
          "command": "if echo \"$FILE_PATH\" | grep -q '\\.py$'; then python -m black \"$FILE_PATH\" 2>/dev/null || true; fi"
        },
        {
          "type": "command",
          "command": "git add \"$FILE_PATH\" 2>/dev/null || true"
        }
      ]
    }],
    "Stop": [{
      "hooks": [{
        "type": "command",
        "command": "echo \"$(date '+%Y-%m-%d %H:%M:%S'): 작업 완료\" >> ~/.claude/session.log"
      }]
    }]
  }
}
```

---

## 훅 적용 위치

| 위치 | 파일 경로 | 적용 범위 |
|------|-----------|-----------|
| 전역 | `~/.claude/settings.json` | 모든 프로젝트 |
| 프로젝트 | `.claude/settings.json` | 해당 프로젝트 |
| 로컬 | `.claude/settings.local.json` | 로컬 환경 전용 (Git 제외) |
