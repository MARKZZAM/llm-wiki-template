# LLM Wiki Template

LLM 에이전트가 유지보수하는 지식 베이스 스켈레톤.

Obsidian과 함께 쓰도록 설계되었으며, AI 코딩 에이전트(Codex, Claude Code, OpenCode 등)가 `AGENTS.md`를 읽고 지식 베이스를 자동으로 관리합니다.

## 구조

```
├── AGENTS.md              # 에이전트 지시사항 (핵심)
├── raw/                   # 소스 자료 inbox (불변)
├── output/                # 쿼리 결과물 staging
└── wiki/                  # LLM이 관리하는 지식 베이스
    ├── _master-index.md   # 전체 진입점
    ├── _template.md       # article 템플릿
    └── sample-topic/      # 주제 폴더 예시
        └── _index.md      # 폴더 내 article 목록
```

## 시작하기

### 1. 레포 복제

```bash
git clone https://github.com/MARKZZAM/llm-wiki-template.git my-wiki
cd my-wiki
rm -rf .git && git init   # 히스토리 초기화
```

### 2. sample-topic 정리

`wiki/sample-topic/`를 참고로 삼고, 본인 주제에 맞게 폴더를 만드세요.

```bash
# 예시: ai-agents 주제 추가
mkdir wiki/ai-agents
```

각 폴더에 `_index.md`를 만듭니다:

```markdown
# 주제명

한 줄 설명.

## Articles

<!-- article이 추가되면 여기에 자동으로 등록됩니다 -->
```

### 3. raw/에 소스 넣기

정리할 자료를 `raw/`에 넣습니다.

- 웹 클리핑, 논문, 대화록, 노트, 이미지 등 무엇이든
- **raw/는 불변** — 삭제·수정·이름변경 금지

### 4. 에이전트에게 컴파일 요청

AI 코딩 에이전트에게 아래 명령을 전달하세요:

```
컴파일해줘
```

에이전트가 `AGENTS.md`를 읽고:
- `raw/`의 미처리 파일을 식별
- 주제별 wiki article 작성
- `_index.md`, `_master-index.md` 갱신
- 관련 문서 간 `[[wiki links]]` 연결

## 사용법

### 컴파일 (raw → wiki)

```
컴파일해줘
```

`raw/`에 새 파일이 있으면 wiki article로 통합합니다.

### 질의

```
창세기 1장 창조의 의미는?
```

에이전트가 `_master-index.md` → `_index.md` → article 순으로 탐색하여 답변합니다. 답변에는 `[[wiki-link]]` 형태로 출처가 인용됩니다.

### 결과물 저장 (대화 → output)

```
output에 저장해
```

대화 중 나온 인사이트나 분석을 `output/`에 저장합니다.

### Output 승격 (output → wiki)

```
output compile해줘
```

`output/`의 파일을 평가하여 재사용 가치가 있는 것을 wiki로 승격합니다.

### 정합성 검사

```
audit해줘
```

끊어진 링크, 고아 문서, 누락된 섹션, 단방향 참조 등을 검사합니다.

## 규칙 요약

| 항목 | 규칙 |
|------|------|
| 언어 | 한국어 기본, 기술 용어는 영어 |
| 파일명 | 한국어 + 하이픈 (`창세기-1장-주석.md`) |
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
