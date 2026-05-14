# LLM Wiki Template

LLM 에이전트가 유지보수하는 LLM Wiki 템플릿.

Obsidian과 함께 쓰도록 설계되었으며, AI 코딩 에이전트(Codex, Claude Code, OpenCode 등)가 `AGENTS.md`를 읽고 지식 베이스를 자동으로 관리합니다.

## 구조

```
├── AGENTS.md                  # 에이전트 지시사항 (핵심)
├── raw/                       # 소스 자료 inbox (불변)
├── output/                    # 쿼리 결과물 staging
└── wiki/                      # LLM이 관리하는 지식 베이스
    ├── _master-index.md       # 전체 진입점
    ├── _compiled-sources.md   # raw 컴파일 이력
    ├── _compiled-outputs.md   # output 컴파일 이력
    └── _template.md           # article 템플릿
```

## 시작하기

```bash
git clone https://github.com/MARKZZAM/llm-wiki-template.git my-wiki
cd my-wiki
rm -rf .git && git init   # 히스토리 초기화
```

이제 `raw/`에 소스를 넣고 에이전트에게 컴파일을 요청하세요. 아래 워크플로우를 참고.

## 워크플로우

LLM Wiki는 세 가지 영역(`raw/` → `wiki/` → `output/`)을 순환하며 지식이 축적됩니다.

```
웹 클리핑 / 노트 / 논문
        │
        ▼
    ┌───────┐   compile    ┌───────┐   query    ┌──────────┐
    │ raw/  │ ──────────▶ │ wiki/ │ ─────────▶ │  답변     │
    │       │              │       │            │  분석     │
    └───────┘              └───────┘            └────┬─────┘
       ▲                       ▲                      │
       │                       │ output compile       │ 저장
       │                  ┌────┴─────┐                ▼
       │                  │ output/  │ ◀────────────────
       │                  └──────────┘
       │
       └─── 새로운 소스 추가 ─── 계속 반복
```

### 1. 수집 → raw/

소스 자료를 `raw/`에 넣습니다. 웹 클리핑, 논문, 대화록, 노트, 이미지 등 무엇이든 가능.

**raw/는 불변** — 삭제·수정·이름변경 금지.

웹 클리핑은 [Obsidian Web Clipper](https://chromewebstore.google.com/detail/obsidian-web-clipper/cnjifjpddelmedmihgijeibhnjfabmlf) 사용 권장. 저장 폴더를 `raw/`로 설정하면 바로 inbox로 들어감.

### 2. 컴파일 → wiki/

AI 코딩 에이전트에게 컴파일을 요청합니다:

```
컴파일해줘
```

에이전트가 `AGENTS.md`를 읽고 다음을 수행합니다:

1. `_compiled-sources.md`와 `raw/`를 비교하여 미처리 파일 식별
2. 소스 내용 분석 → 주제별 topic folder 자동 생성
3. wiki article 작성 (단순 요약이 아닌 재구성)
4. 관련 문서 간 `[[wiki links]]` 연결
5. `_index.md`, `_master-index.md` 갱신
6. 처리 완료된 raw 파일을 `_compiled-sources.md`에 등록

### 3. 질의 → 답변

wiki에 축적된 지식을 자연어로 질의합니다:

```
RAG 시스템에서 벡터 DB 없이 구축하는 방법이 있나?
```

에이전트가 `_master-index.md` → `_index.md` → article 순으로 탐색하여 답변합니다. 답변에는 출처가 `[[wiki-link]]` 형태로 인용됩니다.

### 4. 대화 결과물 → output/

대화 중 축적된 인사이트나 분석을 저장합니다:

```
output에 저장해
```

### 5. output 승격 → wiki/

output 중 장기 가치가 있는 것을 wiki로 승격합니다:

```
output compile해줘
```

에이전트가 각 output 파일을 평가하여 승격/보류/삭제를 판단합니다.

### 6. 정합성 검사

```
audit해줘
```

끊어진 링크, 고아 문서, 누락된 섹션, 단방향 참조 등을 검사합니다.

## 규칙 요약

| 항목 | 규칙 |
|------|------|
| 언어 | 한국어 기본, 기술 용어는 영어 |
| 파일명 | 한국어 + 하이픈 (`AI-에이전트-비교.md`) |
| Tags | kebab-case (`AI-코딩`, `RAG`) |
| Wiki Links | `[[파일명]]` (Obsidian 스타일) |
| raw 참조 | `[[raw/원본파일명.확장자]]` |
| related | 양방향 연결 필수 |
| 핵심 구조 | `## 핵심 요점` → `## 본문` → `## 출처` |

## 지원 에이전트

| 에이전트 | 진입점 파일 |
|----------|------------|
| Codex (OpenAI) | `AGENTS.md` |
| OpenCode | `AGENTS.md` |
| Claude Code | `CLAUDE.md`로 이름 변경 필요 |
| Cursor | `.cursorrules`로 복사 후 경로 수정 |

## 라이선스

MIT
