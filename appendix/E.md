# 부록 E — 공식 문서 및 참고 링크

Claude Code를 더 깊이 활용하고 싶을 때 참고할 수 있는 공식 문서, 도구, 커뮤니티 링크를 카테고리별로 정리했습니다.

---

## 공식 문서

| 이름 | URL | 설명 |
|------|-----|------|
| Claude Code 문서 | https://docs.anthropic.com/claude-code | CLI 사용법, 설정, 훅, MCP 등 전체 문서 |
| Claude API 문서 | https://docs.anthropic.com/en/api/ | API 레퍼런스, 모델 목록, 요청/응답 형식 |
| Claude 모델 개요 | https://docs.anthropic.com/en/docs/about-claude/models/ | 사용 가능한 모델과 성능 비교 |
| Anthropic 콘솔 | https://console.anthropic.com | API 키 관리, 사용량 및 비용 모니터링 |
| Anthropic 요금제 | https://www.anthropic.com/pricing | 모델별 입출력 토큰 단가 |

---

## GitHub 저장소

| 이름 | URL | 설명 |
|------|-----|------|
| Claude Code 이슈 트래커 | https://github.com/anthropics/claude-code/issues | 버그 신고, 기능 요청, 피드백 |
| Anthropic 공식 GitHub | https://github.com/anthropics | 공식 SDK, 예제 코드 |
| MCP 공식 저장소 | https://github.com/modelcontextprotocol | MCP 스펙 및 레퍼런스 구현 |
| MCP 서버 모음 | https://github.com/modelcontextprotocol/servers | 공식 MCP 서버 예제 (파일시스템, Git, DB 등) |

---

## MCP (Model Context Protocol) 관련

MCP는 Claude가 외부 도구 및 데이터 소스와 연결되는 표준 프로토콜입니다.

| 이름 | URL | 설명 |
|------|-----|------|
| MCP 공식 사이트 | https://modelcontextprotocol.io | MCP 스펙 문서, 시작 가이드 |
| MCP 서버 레지스트리 | https://modelcontextprotocol.io/servers | 공식 및 커뮤니티 서버 목록 |
| MCP 서버 GitHub 모음 | https://github.com/modelcontextprotocol/servers | 브라우저, 파일, DB, 슬랙 등 서버 |
| Awesome MCP Servers | https://github.com/punkpeye/awesome-mcp-servers | 커뮤니티 MCP 서버 큐레이션 목록 |

**자주 쓰이는 MCP 서버:**

| 서버 | 기능 |
|------|------|
| `@modelcontextprotocol/server-filesystem` | 로컬 파일시스템 접근 |
| `@modelcontextprotocol/server-github` | GitHub API 연동 |
| `@modelcontextprotocol/server-postgres` | PostgreSQL 데이터베이스 연결 |
| `@modelcontextprotocol/server-sqlite` | SQLite 데이터베이스 연결 |
| `@modelcontextprotocol/server-brave-search` | Brave Search 웹 검색 |
| `@modelcontextprotocol/server-puppeteer` | 브라우저 자동화 |
| `@modelcontextprotocol/server-slack` | Slack 메시지 연동 |

---

## SDK

### Python SDK
| 항목 | 정보 |
|------|------|
| PyPI 패키지 | https://pypi.org/project/anthropic/ |
| GitHub | https://github.com/anthropics/anthropic-sdk-python |
| 설치 | `pip install anthropic` |

```python
import anthropic

client = anthropic.Anthropic(api_key="sk-ant-...")
message = client.messages.create(
    model="claude-opus-4-5",
    max_tokens=1024,
    messages=[{"role": "user", "content": "안녕하세요!"}]
)
print(message.content[0].text)
```

### Node.js / TypeScript SDK
| 항목 | 정보 |
|------|------|
| npm 패키지 | https://www.npmjs.com/package/@anthropic-ai/sdk |
| GitHub | https://github.com/anthropics/anthropic-sdk-node |
| 설치 | `npm install @anthropic-ai/sdk` |

```typescript
import Anthropic from "@anthropic-ai/sdk";

const client = new Anthropic({ apiKey: "sk-ant-..." });
const message = await client.messages.create({
  model: "claude-opus-4-5",
  max_tokens: 1024,
  messages: [{ role: "user", content: "안녕하세요!" }],
});
console.log(message.content[0].text);
```

### Claude Code SDK (에이전트 자동화용)
| 항목 | 정보 |
|------|------|
| npm 패키지 | https://www.npmjs.com/package/@anthropic-ai/claude-code |
| 설명 | Claude Code를 프로그래밍 방식으로 제어하는 SDK |

---

## 커뮤니티

| 이름 | URL | 설명 |
|------|-----|------|
| Anthropic Discord | https://discord.gg/anthropic | 개발자 커뮤니티, 질문/답변, 최신 소식 |
| Anthropic 공식 블로그 | https://www.anthropic.com/news | 모델 업데이트, 연구 발표, 제품 뉴스 |
| Anthropic X (Twitter) | https://x.com/AnthropicAI | 짧은 업데이트 및 공지 |
| Claude Reddit | https://www.reddit.com/r/ClaudeAI/ | 사용자 경험 공유, 팁, 활용 사례 |

---

## 관련 도구

| 도구 | URL | 설명 |
|------|-----|------|
| Claude.ai | https://claude.ai | 웹 인터페이스 (Claude Code와 세션 공유 가능) |
| Anthropic Workbench | https://console.anthropic.com/workbench | 프롬프트 테스트 및 API 실험 환경 |
| OpenRouter | https://openrouter.ai | Claude 포함 다양한 모델 통합 라우터 |

---

## 학습 자료

| 자료 | URL | 설명 |
|------|-----|------|
| 프롬프트 엔지니어링 가이드 | https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/ | 효과적인 프롬프트 작성법 |
| Claude 활용 쿡북 | https://github.com/anthropics/anthropic-cookbook | 실용적인 코드 예제 모음 |
| 컨텍스트 윈도우 관리 | https://docs.anthropic.com/en/docs/build-with-claude/context-windows | 대용량 컨텍스트 처리 전략 |

---

## 마무리

Claude Code는 단순한 AI 채팅 도구를 넘어, 실제 개발 워크플로에 깊게 통합되는 프로그래머블 에이전트 플랫폼입니다. 이 문서를 통해 살펴본 것처럼, CLI 플래그로 모델과 비용을 정밀하게 제어하고, 환경 변수로 동작 방식을 커스터마이징하며, 훅으로 포맷팅·린팅·알림·로깅 같은 반복 작업을 자동화할 수 있습니다. 잘 작성된 `CLAUDE.md`와 `settings.json`은 Claude가 프로젝트 맥락을 즉시 파악하고 안전한 범위 내에서 자율적으로 작업하도록 안내하는 지휘 체계가 됩니다. MCP를 통해 데이터베이스, GitHub, 브라우저 같은 외부 시스템과 연결하면 Claude의 활용 범위는 코드 편집기를 훨씬 넘어서게 됩니다. 결국 Claude Code를 잘 쓴다는 것은 AI에게 무엇을 시킬지 아는 것이 아니라, AI가 스스로 잘 작동할 수 있는 환경과 규칙을 설계하는 것입니다.
