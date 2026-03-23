# Deepdive 1 — 실전 CLAUDE.md 작성

## 어디에 넣어야 하나?

| 내용 | 위치 |
|------|------|
| Claude 응답 스타일, 언어 무관 원칙 | `~/.claude/CLAUDE.md` (전역) |
| 팀 전체 코딩 컨벤션, 금지 패턴 | `[프로젝트]/CLAUDE.md` |
| 특정 디렉터리 전용 규칙 | 해당 디렉터리 `CLAUDE.md` |

코딩 컨벤션은 코드 자체에서 읽을 수 없는 **의도**이므로 반드시 CLAUDE.md에 명시해야 합니다.

---

## 로드 우선순위

```
~/.claude/CLAUDE.md          # 전역 (항상 로드)
[프로젝트 루트]/CLAUDE.md     # 프로젝트 전체
src/CLAUDE.md                # src/ 안에서 작업할 때 추가 로드
```

하위 디렉터리의 CLAUDE.md는 그 디렉터리 안에서 작업할 때만 추가로 읽힙니다.
전역 → 프로젝트 → 디렉터리 순으로 **합쳐집니다** (덮어쓰기 아님).

---

## @ 임포트 — 공통 규칙 분리

큰 프로젝트에서 CLAUDE.md가 길어지면 파일을 나눠 임포트합니다.

```markdown
@.claude/conventions.md
@.claude/git-rules.md
```

```
.claude/
├── CLAUDE.md           # @로 아래 파일들 임포트
├── conventions.md      # 코딩 컨벤션
└── git-rules.md        # 커밋·브랜치 규칙
```

---

## 체크리스트 — CLAUDE.md에 넣을 것들

**전역 (`~/.claude/CLAUDE.md`)**
- [ ] Claude 응답 스타일 (코드 먼저, 요약 생략 등)
- [ ] 모든 프로젝트에 적용할 코딩 원칙
- [ ] Phase 단위 작업 원칙 (한 번에 하나씩, 완료 기준, 진행 파일 관리)

**프로젝트 (`[프로젝트]/CLAUDE.md`)**
- [ ] 기술 스택 (버전 포함)
- [ ] 디렉터리 구조
- [ ] 함수 스타일 (화살표 함수, return 안 함수 선언 금지 등)
- [ ] 타입 정의 방식 (interface vs type)
- [ ] 네이밍 규칙
- [ ] 컴포넌트 구조 순서
- [ ] 자주 쓰는 명령어
- [ ] 건드리면 안 되는 것들
- [ ] 커밋 규칙

---

---

# 완전체 예시

---

## ~/.claude/CLAUDE.md — 전역

```markdown
# 공통 지침

## 응답 스타일
- 설명보다 코드를 먼저 보여준다
- 작업이 끝난 후 요약하지 않는다. 결과로 말한다
- 확실하지 않으면 추측하지 말고 질문한다
- 파일을 수정하기 전에 반드시 먼저 읽는다

## 공통 코딩 원칙
- 과도한 추상화 금지. 지금 필요한 것만 구현한다
- 에러 핸들링은 실제로 발생 가능한 케이스만 처리한다
- 주석은 왜(why)를 설명할 때만 쓴다. 무엇(what)은 코드로 표현한다
- 요청하지 않은 리팩터링, 파일 정리, 의존성 추가를 하지 않는다

## 작업 원칙
- 불명확한 요구사항은 추측하지 말고 질문 후 진행한다
- 예상치 못한 파일이나 상태를 발견하면 삭제 전 반드시 확인을 요청한다
- 테스트가 실패하면 테스트를 우회하지 말고 원인을 파악해 수정한다

## Phase 관리

### 기본 원칙
- 반드시 Phase 단위로 작업한다. 한 번에 여러 Phase를 수행하지 않는다
- 매 Phase 완료 시 `agent-progress.md`를 업데이트하고 커밋한다
- 테스트를 통과해야 다음 Phase로 넘어간다
- 불확실하면 멈추고 질문한다

### Phase 작성 양식
프로젝트 CLAUDE.md에 아래 형식으로 Phase를 정의한다.

\`\`\`markdown
### Phase N: [이름]

**목표**: [이 Phase에서 달성할 것]

**수행 내용**:
- [파일/기능]: [설명]

**완료 기준**:
- [ ] [검증 항목]

**커밋**: \`N 단계: [설명]\`
\`\`\`

### agent-progress.md 형식
프로젝트 시작 시 생성하고, 매 Phase 완료 시 업데이트한다.

\`\`\`markdown
# [프로젝트명] — Agent Progress

## N 단계: [이름]
- 상태: ⬜ 대기 / 🔄 진행중 / ✅ 완료 / ❌ 실패
- 완료 시각:
- 수행 내용:
- 테스트 결과:
- 이슈/메모:
\`\`\`

### 트러블슈팅
| 상황 | 대응 |
|------|------|
| 외부 API 키 없음 | mock 데이터로 fallback, 키 확보 후 교체 |
| 테스트 실패 | 원인 파악 후 수정. 테스트 우회 금지 |
| 불명확한 요구사항 | 추측하지 말고 질문 후 진행 |
| 예상치 못한 파일/상태 발견 | 삭제 전 반드시 확인 요청 |
```

---

## [프로젝트]/CLAUDE.md — 프로젝트

```markdown
# [프로젝트명]

## 프로젝트 개요
[한두 줄로 이 프로젝트가 무엇을 하는지]

## 기술 스택
- Frontend: React 18 + TypeScript
- Styling: Tailwind CSS
- State: Zustand
- Server State: TanStack Query v5
- Backend: FastAPI
- DB: PostgreSQL + SQLAlchemy
- 인증: JWT (httpOnly cookie)

## 디렉터리 구조
\`\`\`
src/
├── container/   # 라우터가 가리키는 페이지 단위 컴포넌트
├── component/   # 재사용 UI 조각 (container에서 조립)
├── hooks/       # 전역 공통 훅
├── context/     # 전역 상태
├── types/       # 타입 정의
├── utils/       # 유틸 함수
└── api/         # API 호출 함수
\`\`\`

---

## 코딩 컨벤션 — JavaScript / TypeScript

### 함수 스타일
- 화살표 함수를 기본으로 사용한다
- `return` 안에서 함수를 직접 선언하지 않는다
- 핸들러·콜백은 return 위에서 미리 정의한다

\`\`\`tsx
// ❌ 금지
return (
  <button onClick={() => {
    doSomething();
    doMore();
  }}>
    클릭
  </button>
);

// ✅ 권장
const handleClick = () => {
  doSomething();
  doMore();
};
return <button onClick={handleClick}>클릭</button>;
\`\`\`

### 타입 정의
- `interface`를 우선 사용한다
- 유니온·튜플처럼 `interface`로 표현할 수 없는 경우에만 `type`을 쓴다

\`\`\`ts
// ✅ 권장
interface User {
  id: number;
  name: string;
  email: string;
}

// interface로 불가능한 경우만 type
type Status = 'idle' | 'loading' | 'success' | 'error';
\`\`\`

### 네이밍
- 컴포넌트: PascalCase (`UserCard`, `LoginForm`)
- 함수·변수: camelCase (`fetchUser`, `isLoading`)
- 상수: UPPER_SNAKE_CASE (`MAX_RETRY`, `API_BASE_URL`)
- 파일명: 컴포넌트는 PascalCase, 나머지는 kebab-case
- 이벤트 핸들러: `handle` 접두사 (`handleClick`, `handleSubmit`)

### 컴포넌트 구조 순서
1. import
2. interface / type 정의
3. 컴포넌트 외부 상수
4. 컴포넌트 함수
   a. 훅 호출
   b. 파생 변수 정의
   c. 핸들러 정의
   d. return

### 포맷터 & 린터
- Prettier: 2 spaces, 세미콜론 있음, 싱글 쿼트
- ESLint 규칙을 통과해야 커밋 가능
- 수동으로 포맷하지 말고 저장 시 자동 포맷에 맡긴다

---

## 자주 쓰는 명령어
- 개발 서버: `npm run dev`
- 타입 체크: `npx tsc --noEmit`
- 린트: `npm run lint`
- 테스트: `npm test`
- 빌드: `npm run build`

---

## 주의사항
- `legacy/` 폴더는 건드리지 말 것
- `.env` 파일은 직접 수정하지 말고 알려줄 것
- DB 마이그레이션은 직접 실행하지 말고 알려줄 것
- `console.log`는 커밋 전 반드시 제거
- `any` 타입 사용 금지. 불가피하면 `unknown` 후 타입 가드 사용

---

## 커밋 규칙
- 메시지는 한국어로 작성
- prefix: `feat` / `fix` / `refactor` / `docs` / `chore`
- 예시: `feat: 로그인 폼 유효성 검사 추가`

---

## Phase 정의

### Phase 0: 프로젝트 초기화

**목표**: 개발 환경 세팅 및 기본 구조 생성

**수행 내용**:
- 디렉터리 구조 생성
- 패키지 설치 및 설정 파일 작성 (tsconfig, eslint, prettier)
- 환경변수 파일 (.env.example) 생성

**완료 기준**:
- [ ] `npm run dev` 정상 실행
- [ ] `npx tsc --noEmit` 오류 없음
- [ ] `npm run lint` 통과

**커밋**: `0단계: 프로젝트 초기화`

---

### Phase 1: [기능명]

**목표**: [이 Phase에서 달성할 것]

**수행 내용**:
- [파일/기능]: [설명]

**완료 기준**:
- [ ] [검증 항목]

**커밋**: `1단계: [설명]`
```
