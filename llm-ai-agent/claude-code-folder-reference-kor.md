# Claude Code `.claude/` Folder Reference
updated: 2026-06-14

## 전체 구조 요약

```
프로젝트/.claude/          ← 팀 공유, git commit
├── CLAUDE.md              # 프로젝트 영구 메모리 (매 세션 로드)
├── settings.json          # 공유 설정 (권한, hooks, MCP)
├── settings.local.json    # 로컬 전용 오버라이드 (gitignore)
├── skills/                # 컨텍스트 기반 자동 발동 워크플로우
├── commands/              # 수동 슬래시 커맨드 (레거시 → skills 통합 중)
├── agents/                # 컨텍스트 격리 서브에이전트
├── rules/                 # 경로별 추가 규칙 파일
├── hooks/                 # 라이프사이클 이벤트 스크립트
└── plans/                 # Plan Mode 결과물

~/.claude/                 ← 개인 전용, 절대 git commit 금지
├── CLAUDE.md              # 전역 개인 규칙
├── settings.json          # 개인 전역 설정
├── commands/              # 개인 전용 슬래시 커맨드 (전 프로젝트 공유)
├── skills/                # 개인 전용 skills
├── agents/                # 개인 전용 서브에이전트
└── projects/              # 세션 기록 & auto-memory
    └── -Users-me-myproject/
        ├── sessions/
        │   ├── abc123.jsonl        # 완전한 세션 transcript
        │   └── sessions-index.json # 메타데이터 (요약, git 브랜치, 타임스탬프)
        └── memory/
            └── MEMORY.md           # Claude가 자동 작성하는 메모리
```

### 폴더별 한 눈에 비교

| 폴더 | 로드 시점 | 호출 주체 | git 커밋 |
|------|-----------|-----------|----------|
| `skills/` | 관련 task 감지 시 자동 | Claude 자동 또는 `/name` | o |
| `commands/` | 사용자가 `/name` 호출 시 | 사용자 | o |
| `agents/` | 부모가 Task tool로 spawn | Claude 자동 또는 `@name` | o |
| `rules/` | 세션 시작 시 (경로 스코프 시 해당 파일 작업 시만) | 자동 | o |
| `hooks/` | 라이프사이클 이벤트 발생 시 | 자동 (결정론적) | o |
| `plans/` | Plan Mode 완료 후 저장 | Plan Mode 실행 시 | x 권장 |
| `projects/` | — (저장소) | — | x |

---

## 1. `skills/`

관련 task가 감지될 때 Claude가 자동으로 로드하는 재사용 가능한 워크플로우. CLAUDE.md가 매 세션 항상 로드되는 것과 달리, skill은 **필요할 때만** 로드되어 컨텍스트를 절약한다.

### 파일 구조

```
.claude/skills/
└── security-review/
    ├── SKILL.md         ← 필수 (frontmatter + 지시사항)
    ├── checklist.md     ← 선택 지원 파일 (@참조 가능)
    └── scan.sh          ← 선택 스크립트
```

### SKILL.md 포맷

```yaml
---
name: security-review
description: >
  Run a security review on code changes. Use when reviewing auth,
  API endpoints, or data handling code. Triggers on: "security check",
  "review for vulnerabilities", "auth code review".
allowed-tools: Bash, Read, Grep
disable-model-invocation: false   # true면 사용자만 /name으로 수동 호출 가능
---

# Security Review 절차

1. `git diff`로 변경사항 확인
2. 인증/인가 로직 검토
3. SQL Injection, XSS 가능 여부 스캔
4. 의존성 취약점 확인

@checklist.md  ← 지원 파일 인라인 참조
```

### 핵심 포인트

- **description = 트리거**: 단순 설명이 아니라 Claude가 fuzzy matching으로 발동 여부를 결정하는 문자열. 모호하면 조용히 발동하지 않음
- **시작 시 name + description만 스캔** (약 100토큰/skill), 매칭 시 전체 로드
- `disable-model-invocation: true`: `/deploy`, `/commit` 처럼 부작용 있는 워크플로우에서 Claude의 자동 실행 방지
- `user-invocable: false`: Claude만 내부적으로 호출 가능 (배경 지식용)
- **오픈 스탠다드**: Claude Code, OpenAI Codex CLI, Cursor, Gemini CLI 등 공통 포맷

### skills vs commands 선택 기준

| | skills | commands |
|-|--------|----------|
| 발동 | 컨텍스트 기반 자동 | 사용자가 `/name` 명시 |
| 용도 | "how we do XYZ" 절차적 지식 | 정해진 타이밍에 실행할 액션 |
| 복잡도 | 지원 파일, 스크립트 포함 가능 | 단순 프롬프트 템플릿 |

---

## 2. `commands/`

`/command-name`으로 호출하는 프롬프트 템플릿. v2.1.101부터 skills와 통합되어 같은 `.md` 파일 형식을 공유하며, 신규 작성은 skills 방식 권장.

### 파일 구조

```
.claude/commands/
├── fix-issue.md              → /fix-issue
└── frontend/
    └── component.md          → /frontend:component  (네임스페이싱)
```

### 커맨드 파일 포맷

```yaml
---
description: Fix a GitHub issue by number
argument-hint: <issue-number>
allowed-tools: Bash, Read, Edit
model: claude-sonnet-4-6
---

Fix GitHub issue #$ARGUMENTS following our coding standards.

1. Read the issue description
2. Understand the requirements
3. Implement the fix
4. Write tests
5. Create a commit
```

### 인자 처리

```
/fix-issue 1234
           └─ $ARGUMENTS = "1234"

/deploy main production
        └─ $ARGUMENTS = "main production"
           $1 = "main"
           $2 = "production"
```

### frontmatter 주요 필드

| 필드 | 설명 |
|------|------|
| `description` | 슬래시 커맨드 picker에 표시되는 한 줄 설명 |
| `argument-hint` | 입력창에 표시되는 placeholder (코스메틱) |
| `allowed-tools` | 이 커맨드에서 사용 가능한 tool 목록 |
| `model` | 사용할 모델 고정 (버전 drift 방지 시 유용) |
| `disable-model-invocation` | `true`이면 Claude 자동 실행 불가, 사용자만 호출 가능 |

---

## 3. `agents/`

독립 컨텍스트 윈도우를 가진 전문화된 Claude 인스턴스. 부모 세션의 컨텍스트가 커질 때 사이드 작업을 격리해서 처리하고 요약만 반환한다.

### 파일 구조

```
.claude/agents/
├── code-reviewer.md
├── test-writer.md
└── docs-generator.md
```

### 에이전트 파일 포맷

```yaml
---
name: code-reviewer
description: >
  Expert code review specialist. Use after writing or modifying code
  to check quality, security, and maintainability.
  Trigger: "review this code", "check for security issues".
tools: Read, Grep, Glob, Bash
model: claude-haiku-4-5   # 빠른 작업엔 haiku, 복잡한 추론엔 opus
---

You are a senior code reviewer. When invoked:

1. Run `git diff` to see recent changes
2. Focus on modified files
3. Check for security vulnerabilities
4. Review code quality and maintainability
5. Return a structured summary with findings
```

### 핵심 포인트

- **description = 트리거**: 부모 에이전트가 어느 서브에이전트를 spawn할지 결정. "Use this agent when X" 형태로 명확하게 작성
- **독립 컨텍스트**: 각 서브에이전트가 자신만의 컨텍스트 윈도우를 가져 부모 세션 오염 방지
- **권한 제한 중요**: 서브에이전트는 사용자에게 권한 프롬프트를 표시할 수 없음. `ask` 룰 매칭 시 자동 거부되므로 Read-only tool 세트 권장. Edit/Write는 부모에게 위임
- **모델 오버라이드**: `CLAUDE_CODE_SUBAGENT_MODEL` 환경변수로 전체 서브에이전트 모델 강제 지정 가능
- **병렬 실행**: 부모가 여러 서브에이전트를 동시에 spawn해서 독립적 작업을 병렬 처리 가능

### skills vs agents 선택 기준

| | skills | agents |
|-|--------|--------|
| 용도 | 절차적 지식 (how) | 전문화된 역할 |
| 컨텍스트 | 부모 세션 공유 | 완전히 격리된 독립 윈도우 |
| 적합한 경우 | 반복 워크플로우 자동화 | 대규모 탐색, 병렬 작업 |

---

## 4. `rules/`

CLAUDE.md가 너무 길어질 때 관심사별로 분리하는 규칙 파일. 경로 스코프를 지정하면 해당 파일 작업 시에만 로드되어 컨텍스트를 절약한다.

### 파일 구조

```
.claude/rules/
├── tests.md            ← 항상 로드 (경로 스코프 없음)
├── python-types.md     ← 항상 로드
└── migrations.md       ← .sql 파일 작업 시에만 로드
```

### 규칙 파일 포맷

```yaml
# migrations.md (경로 스코프 예시)
---
paths: ["**/*.sql", "**/migrations/**"]
---

# Migration 규칙

- 항상 롤백 가능한 migration 작성 (DOWN migration 포함)
- 대용량 테이블 변경 시 batch 처리 사용
- 인덱스는 CONCURRENTLY 옵션으로 생성
- Zero-downtime 배포를 고려한 다단계 migration 설계
```

```yaml
# tests.md (항상 로드, 경로 스코프 없음)
---
# frontmatter 없어도 됨
---

# 테스트 규칙

- 새 함수마다 단위 테스트 필수
- 외부 의존성은 반드시 mock 처리
- 테스트 파일명: {module}.test.ts
```

### CLAUDE.md vs rules 비교

| | CLAUDE.md | rules/ |
|-|-----------|--------|
| 로드 시점 | 항상 (매 세션) | 항상 또는 경로 매칭 시 |
| 컨텍스트 절약 | ❌ 항상 소비 | ✅ 경로 스코프 시 절약 |
| 용도 | 전역 아키텍처/컨벤션 | 도메인별 세부 규칙 |
| 권장 크기 | 200줄 이하 | 파일당 짧게 |

> ⚠️ `@import`로 파일을 가져오는 것은 컨텍스트를 절약하지 않는다. 어차피 시작 시 전부 로드되기 때문. 실제 컨텍스트 절약은 경로 스코핑된 rules만 가능.

---

## 5. `hooks/`

Claude Code 라이프사이클 이벤트에 자동으로 실행되는 스크립트. CLAUDE.md의 "요청"과 달리 **보장된 실행** — Claude의 판단과 무관하게 반드시 동작한다.

### settings.json에서 설정

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write",
        "hooks": [{
          "type": "command",
          "command": "pnpm lint --fix",
          "timeout": 30
        }]
      }
    ],
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [{
          "type": "command",
          "command": "bash .claude/hooks/block-secrets.sh"
        }]
      }
    ],
    "Stop": [
      {
        "matcher": "*",
        "hooks": [{
          "type": "command",
          "command": "node .claude/hooks/notify-slack.js",
          "async": true
        }]
      }
    ]
  }
}
```

### 주요 이벤트

| 이벤트 | 타이밍 | 주요 용도 |
|--------|--------|-----------|
| `PreToolUse` | 도구 실행 직전 | 차단/허용 결정, 보안 게이트 |
| `PostToolUse` | 도구 실행 직후 | 자동 포맷팅, 검증 |
| `Stop` | Claude 응답 완료 시 | 알림, 완료 후 검사 |
| `SessionStart` | 세션 시작/재개/clear/compact 시 | 환경 초기화 |
| `SessionEnd` | 세션 종료 시 | 백업, 로깅 |
| `UserPromptSubmit` | 사용자 메시지 제출 시 | 입력 전처리 |
| `SubagentStop` | 서브에이전트 완료 시 | 서브에이전트 결과 검증 |

### exit code 시멘틱 (PreToolUse)

```
exit 0  → 허용 (계속 진행)
exit 2  → 차단 (stderr의 메시지를 Claude에게 전달)
```

### handler 타입

| 타입 | 설명 |
|------|------|
| `command` | 셸 스크립트 실행 |
| `http` | 엔드포인트에 POST (2026년 2월 추가) |
| `prompt` | 단일 턴 LLM 평가 |
| `agent` | 도구 접근 가능한 서브에이전트 |

`async: true` 추가 시 비차단 백그라운드 실행 (알림, 로깅용).

### CLAUDE.md vs hooks 선택 기준

- **판단이 필요한 것** → CLAUDE.md ("ESLint 에러가 있으면 고쳐줘")
- **예외 없이 반드시 실행** → hooks (`PostToolUse`로 항상 lint 자동 실행)
- 둘 다 넣으면 충돌 신호 — hook이 자동 실행되는데 CLAUDE.md에도 같은 지시가 있으면 Claude가 중복으로 시도함

---

## 6. `plans/`

Plan Mode에서 Claude가 작성하는 마크다운 실행 계획 파일.

### 기본 동작

- Plan Mode 진입 시 Claude가 변경 전에 수행할 작업을 마크다운으로 작성
- 기본 저장 위치: `~/.claude/plans/` (전역, 모든 프로젝트 혼합)
- 파일명: `jaunty-petting-nebula.md` 같은 자동 생성 이름

### 프로젝트별 분리 설정 (권장)

```json
// .claude/settings.json
{
  "plansDirectory": ".claude/plans"
}
```

```
프로젝트/.claude/plans/
├── refactor-auth-2026-06.md
└── add-payment-module.md
```

### plan 파일 예시

```markdown
# Refactor Authentication Module

## 목표
JWT 기반 인증으로 전환, 세션 스토어 제거

## 단계
1. [ ] `auth/session.ts` 읽기 — 현재 구조 파악
2. [ ] JWT 유틸리티 함수 작성 (`auth/jwt.ts`)
3. [ ] 미들웨어 교체 (`middleware/auth.ts`)
4. [ ] 기존 세션 관련 테스트 업데이트
5. [ ] 통합 테스트 실행

## 영향받는 파일
- `src/auth/session.ts`
- `src/middleware/auth.ts`
- `tests/auth.test.ts`
```

### VS Code에서 Plan Mode

Plan Mode 전환 시 Claude가 plan을 전체 마크다운 문서로 자동으로 열어줌. 인라인 코멘트로 피드백을 추가하고 승인하면 실행 시작.

---

## 7. `projects/` (`~/.claude/projects/`)

모든 세션의 완전한 대화 기록과 Claude가 자동 작성하는 메모리. 개인 전용으로 git에 절대 커밋하지 않는다.

### 구조

```
~/.claude/projects/
└── -Users-me-myproject/          ← 절대 경로를 - 로 변환
    ├── sessions/
    │   ├── abc123.jsonl          ← 완전한 세션 transcript (JSONL)
    │   └── sessions-index.json   ← 세션 메타데이터
    └── memory/
        └── MEMORY.md             ← auto-memory (Claude 자동 작성)
```

### sessions/*.jsonl 내용

단순 로그가 아닌 완전한 기록:
- tool 호출의 정확한 input/output
- extended thinking 블록
- 서브에이전트 spawn 이벤트
- 턴별 토큰 사용량
- 모델 선택, 작업 디렉토리, git 상태 스냅샷

### sessions-index.json 내용

```json
{
  "sessions": [
    {
      "id": "abc123",
      "summary": "JWT 인증 모듈 리팩토링",
      "messageCount": 42,
      "gitBranch": "feat/jwt-auth",
      "createdAt": "2026-06-10T09:00:00Z",
      "modifiedAt": "2026-06-10T11:30:00Z"
    }
  ]
}
```

### auto-memory (MEMORY.md)

Claude가 세션 중 자동으로 기록:
- 발견한 커맨드 (`pnpm test` 등)
- 관찰한 패턴 및 아키텍처 인사이트
- 교정된 선호도

`/memory` 커맨드로 조회·편집 가능. 지우고 싶으면 파일 삭제 후 Claude Code가 재생성.

### 세션 재개 커맨드

```bash
claude -c                  # 현재 프로젝트의 가장 최근 세션 재개
claude -r SESSION_ID       # 특정 세션 ID로 재개
/history                   # 세션 내에서 최근 세션 목록 조회
```

---

## 실전 권장 설정 예시

```
myproject/
├── CLAUDE.md              ← 아키텍처, 주요 컨벤션 (200줄 이하)
└── .claude/
    ├── settings.json      ← hooks, 권한, plansDirectory 설정
    ├── rules/
    │   ├── tests.md       ← 테스트 규칙 (항상 로드)
    │   └── migrations.md  ← DB 규칙 (*.sql 작업 시만 로드)
    ├── skills/
    │   ├── security-review/SKILL.md
    │   └── pr-checklist/SKILL.md
    ├── agents/
    │   ├── code-reviewer.md
    │   └── test-writer.md
    ├── commands/
    │   └── fix-issue.md   ← /fix-issue (레거시 또는 수동 호출 전용)
    └── plans/             ← plansDirectory 설정 시 여기에 저장
```

```json
// .claude/settings.json
{
  "plansDirectory": ".claude/plans",
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write",
        "hooks": [{ "type": "command", "command": "pnpm lint --fix", "timeout": 30 }]
      }
    ]
  }
}
```

---

## References

- [Claude Code Official Memory Guide](https://code.claude.com/docs/en/memory)
- [Claude Code Official Slash Commands Documentation](https://code.claude.com/docs/en/slash-commands)
- [Claude Code Features & Settings Reference 2026](https://hidekazu-konishi.com/entry/claude_code_features_settings_reference_2026.html)
- [Claude Code Hooks Complete Guide](https://claudefa.st/blog/tools/hooks/hooks-guide)
- [Claude Code Subagents Guide](https://www.tembo.io/blog/claude-code-subagents)
- [Anatomy of the .claude Folder (codewithmukesh)](https://codewithmukesh.com/blog/anatomy-of-the-claude-folder/)
- [SKILL.md Spec: Every Field](https://www.agensi.io/learn/skill-md-format-reference)