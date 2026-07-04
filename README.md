# agentic-eng-plugin

누적형 지식 베이스(**세컨드 브레인**) 워크플로우와 코드베이스 **AI-준비도 감사**를 한데 묶은
Claude Code 플러그인. 스킬 4종을 슬래시 명령으로 제공하고, **TDD Guard** hook으로
테스트 우선 워크플로우를 강제한다.

## 담긴 스킬

| 슬래시 명령 | 설명 |
|---|---|
| `/wiki-ingest <raw 파일경로>` | 새 원본 소스를 위키에 흡수 — 개념/에피소드/사례 페이지를 생성·갱신하고 `index.md`·`wiki/log.md`를 업데이트한다. |
| `/wiki-query <질문>` | 위키를 근거로 인용과 함께 답하고, 재사용 가치가 있는 답변은 `wiki/qa/`에 저장한다. |
| `/wiki-lint` | 모순·오래된 주장·고아 페이지·누락 링크·`index.md` 불일치를 감사해 리포트한다. |
| `/ai-readiness-cartography` | 임의 레포를 v2 AI-Ready 루브릭(100점·7 카테고리)으로 감사하고, 단일 HTML 대시보드 + ROI 액션 리스트를 만든다. |

## 설치

```bash
claude marketplace add gompangii/agentic-eng-plugin
claude plugin install agentic-eng-plugin
```

로컬에서 바로 시험하려면 (이 폴더 경로로):

```bash
claude marketplace add /path/to/agentic-eng-plugin
claude plugin install agentic-eng-plugin
```

## TDD Guard (hook)

플러그인을 설치하면 `PreToolUse[Edit|Write]` hook이 함께 켜진다. **테스트 파일이 먼저
존재하지 않는 구현 코드**를 작성/수정하려 하면 그 편집을 차단해, 테스트 우선(TDD)
사이클을 강제한다.

### 동작

`.ts` / `.tsx` / `.js` / `.jsx` 소스 파일을 Edit/Write 할 때, 아래 위치에서 대응하는
테스트 파일을 찾는다:

- 같은 폴더의 `<파일명>.test.<ext>` 또는 `<파일명>.spec.<ext>`
- 인접 또는 상위 `__tests__/<파일명>.test.<ext>`
- 프로젝트 루트 `src/__tests__/<파일명>.test.<ext>`

하나도 없으면 편집이 **거부**되고 아래 메시지가 뜬다:

```
TDD GUARD: 'sum'에 대한 테스트 파일이 존재하지 않습니다.
구현 코드를 작성하기 전에 테스트를 먼저 작성하세요. (테스트 파일 예: sum.test.ts)
```

즉 흐름은 항상 **테스트 먼저 → 구현**이 된다:

```
1. src/lib/sum.test.ts 작성    → 허용 (테스트 파일)
2. src/lib/sum.ts 작성          → 허용 (테스트가 존재하므로)
```

### 검사에서 제외되는 파일 (테스트 없이 허용)

- 테스트 파일 자체 (`*test*`, `*spec*`, `*__tests__*`)
- 설정/스타일/문서 (`*.json`, `*.css`, `*.scss`, `*.md`, `*.yml`, `*.env*`, `*.config.*`, `tsconfig`, `tailwind`, `postcss`, `next.config` 등)
- 타입 파일 (`*/types/*`, `*.d.ts`)
- Next.js 프레임워크 파일 (`layout` / `page` / `loading` / `error` / `not-found`, `globals.css`)

### 전제

hook 스크립트(`hooks/tdd-guard.sh`)는 `bash`와 `jq`를 사용한다. macOS/Linux는 기본
제공되고, Windows는 Git Bash + `jq` 설치가 필요하다.

> hook은 설치 시점에 로드된다. 이미 실행 중인 세션이라면 `/hooks`를 한 번 열거나
> 재시작해야 활성화된다.

## 전제 (wiki-* 스킬)

`wiki-ingest` / `wiki-query` / `wiki-lint`는 [카르파시 LLM Wiki 패턴](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)을
따르는 **세컨드 브레인 레이아웃**을 전제로 한다. 즉 작업 디렉터리에 다음 규약이 있어야 자연스럽게 동작한다:

- `raw/` — 불변 원본 소스 (읽기만)
- `wiki/` — 생성·수정하는 지식 레이어 (`concepts/`, `topics/`, `examples/`, `qa/`, `log.md`)
- `index.md` — 카테고리별 페이지 카탈로그
- `CLAUDE.md` — 위키 구조·규칙 스키마

이 규약을 정의하는 예시는 원본 세컨드 브레인 프로젝트의 `CLAUDE.md`를 참고.

`ai-readiness-cartography`는 전제 없이 아무 레포에서나 동작한다 (번들된 `scripts/score.py`는
Python 3.10+ 표준 라이브러리만 사용).

## 구조

```
agentic-eng-plugin/
  .claude-plugin/
    marketplace.json
    plugin.json
  hooks/
    hooks.json                  # PreToolUse[Edit|Write] → TDD Guard 등록
    tdd-guard.sh                # 테스트 우선 강제 스크립트
  skills/
    ai-readiness-cartography/   # SKILL.md + scripts/ + assets/ + references/
    wiki-ingest/
    wiki-lint/
    wiki-query/
```

## 라이선스

MIT
