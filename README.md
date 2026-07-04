# agentic-eng-plugin

누적형 지식 베이스(**세컨드 브레인**) 워크플로우와 코드베이스 **AI-준비도 감사**를 한데 묶은
Claude Code 플러그인. 스킬 4종을 슬래시 명령으로 제공한다.

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
  skills/
    ai-readiness-cartography/   # SKILL.md + scripts/ + assets/ + references/
    wiki-ingest/
    wiki-lint/
    wiki-query/
```

## 라이선스

MIT
