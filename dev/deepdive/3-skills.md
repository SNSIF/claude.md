# Deepdive 3 — 커스텀 스킬 실전

## 스킬이란?

`.claude/skills/` 폴더에 Markdown 파일로 만드는 나만의 슬래시 커맨드입니다.
반복되는 작업 절차를 한 번 정의해두면 `/스킬명` 한 번으로 실행됩니다.

---

## 파일 구조

```
.claude/
└── skills/
    ├── commit.md         # /commit
    ├── review.md         # /review
    ├── pr.md             # /pr
    └── deploy-check.md   # /deploy-check
```

---

## 스킬 파일 작성 규칙

```markdown
# 스킬명

[Claude에게 줄 지침. 이 파일이 곧 프롬프트입니다.]

$ARGUMENTS — 호출 시 전달된 인자 (없으면 생략)
```

- 파일명 = 커맨드명 (예: `commit.md` → `/commit`)
- 마크다운으로 자유롭게 작성
- `$ARGUMENTS`로 인자를 받을 수 있음

---

---

# 완전체 예시

---

## `/review` — 코드 리뷰

```markdown
# review

$ARGUMENTS 에 지정한 파일을 코드 리뷰합니다.
파일을 지정하지 않으면 최근 변경된 파일(`git diff HEAD`)을 리뷰합니다.

## 리뷰 항목
- 버그 및 엣지 케이스
- 타입 안전성 (any 사용, 누락된 null 체크 등)
- 성능 이슈 (불필요한 리렌더링, N+1 등)
- 보안 취약점 (XSS, 민감 정보 노출 등)
- 코드 중복 및 리팩터링 기회
- 네이밍 일관성

## 출력 형식
심각도를 아래 기준으로 표시한다:
- 🔴 critical — 버그, 보안 문제. 반드시 수정 필요
- 🟡 warning  — 잠재적 문제. 수정 권장
- 🟢 suggestion — 개선 아이디어. 선택 사항

이슈가 없으면 "이슈 없음"으로 간단히 마무리한다.
```

**사용 예:**
```
/review
/review src/api/auth.ts
/review src/components/LoginForm.tsx src/hooks/useLogin.ts
```

---

## `/pr` — PR 초안 생성

```markdown
# pr

현재 브랜치와 main 브랜치의 diff를 분석해서 PR 초안을 작성합니다.

## 순서
1. `git log main..HEAD --oneline` 으로 커밋 목록 확인
2. `git diff main...HEAD` 로 전체 변경 내용 확인
3. PR 초안 작성

## PR 형식
\`\`\`
## 요약
- [변경 내용 bullet 2~4개]

## 변경 이유
[왜 이 작업이 필요했는지]

## 테스트 체크리스트
- [ ] [테스트 항목]
- [ ] [테스트 항목]
\`\`\`

## 주의
- 제목은 70자 이내, 한국어
- 스크린샷이 필요한 UI 변경은 체크리스트에 추가
- gh 명령이 사용 가능하면 PR을 직접 생성할지 물어본다
```

---

## `/deploy-check` — 배포 전 체크

```markdown
# deploy-check

배포 전 체크리스트를 순서대로 실행하고 결과를 보고합니다.

## 체크 항목
1. **디버그 코드 확인**: `console.log`, `debugger`, `TODO` Grep
2. **타입 체크**: `npx tsc --noEmit`
3. **린트**: `npm run lint`
4. **테스트**: `npm test`
5. **빌드**: `npm run build`
6. **환경변수 확인**: `.env.example` 과 실제 `.env` 키 비교

## 출력 형식
각 항목을 ✅ / ❌ 로 표시하고, 실패한 항목은 이유와 함께 정리한다.
모든 항목 통과 시 "배포 준비 완료"로 마무리한다.
```

---

## `/new-component` — 컴포넌트 생성

```markdown
# new-component

$ARGUMENTS 이름의 React 컴포넌트를 생성합니다.

## 생성 파일
- `src/component/[Name]/[Name].tsx` — 컴포넌트
- `src/component/[Name]/index.ts`   — 재export

## 컴포넌트 템플릿
프로젝트 컨벤션에 맞게 생성한다:
- interface로 Props 정의
- 화살표 함수 컴포넌트
- return 안에 함수 선언 금지, 핸들러는 위에서 정의
- Tailwind CSS 사용

## 주의
- 컴포넌트 이름이 없으면 물어보고 진행한다
- 이미 같은 이름의 파일이 있으면 덮어쓰기 전에 확인한다
```

**사용 예:**
```
/new-component UserCard
/new-component LoginForm
```
