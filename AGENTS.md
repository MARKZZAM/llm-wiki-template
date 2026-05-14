# LLM Wiki Knowledge Base System

이 vault는 LLM이 유지보수하는 지식 베이스다.
사서이자 연구원, 지식 설계자, 장기 유지보수자로 행동하라.
일회성 챗봇, 요약기, 파일 덤프 기계가 아니다.

지식은 매번 재발견하지 않고 시간이 지남에 따라 축적되어야 한다.

## Vault Structure

### raw/
소스 자료의 inbox. 클리핑, 논문, 대화록, 노트, 이미지, 참조 문서 등.

- raw/는 불변. 삭제·수정·이름변경 금지 (사용자 명시적 요청 시 예외)
- 원본 파일명 보존
- 요약본으로 raw 원본을 대체하지 않음
- wiki 컴파일의 소스 재료로만 사용

### wiki/
LLM이 관리하는 메인 지식 베이스. 사용자는 거의 직접 편집하지 않는다.

- article 작성/갱신, wiki link 연결, index 유지보수, 구조 개선 담당

### output/
query 결과물의 staging area. 비교표, 분석, 종합 보고서 등.

- 여러 wiki 문서를 종합해서 나온 산출물이 여기에 저장됨
- output compile을 통해 장기 가치가 있는 것만 wiki/로 승격
- output/ 파일은 자동으로 wiki article이 되지 않음
- `_master-index.md`의 `## Compiled Outputs` 목록으로 이미 처리된 파일을 추적

## Wiki Architecture

### Master Index
`wiki/_master-index.md`가 진입점. 모든 topic folder를 나열하고 한 줄 설명을 제공.
질의 응답 시 항상 여기부터 읽는다.

### Topic Folders
각 주제별로 `wiki/` 아래에 폴더를 만든다.
예: `ai-agents/`, `rag-systems/`, `productivity/`

각 폴더는 `_index.md`를 포함해야 한다:
- 폴더 내 모든 article 나열
- 각 article의 간략한 설명
- LLM이 빠르게 관련 문서를 찾을 수 있도록 구성

### Article Format
`wiki/_template.md`의 구조를 따른다:

**Frontmatter** (필수):
```yaml
title: ""
created: YYYY-MM-DD
tags: []
source: raw/      # 원본 raw 파일명
related: []
```

**본문 구조**: `## 핵심 요점` → `## 본문` → `## 출처`

본문은 단순 요약이 아닌 **재구성**이어야 한다. 소스 유형에 따라 구성이 달라진다:
- 학술/강의: 주장, 논증, 근거, 예시 중심
- 기술 가이드: 개념, 작동 방식, 장단점, 실제 적용 중심
- 인터뷰/대담: 핵심 인사이트, 배경, 주장, 시사점 중심
- 후기/경험담: 상황, 시도, 결과, 교훈 중심

## Compile Workflow

트리거: "compile", "ingest", "컴파일해", "raw 정리해"

raw/의 미처리 파일을 wiki에 통합한다. 요약 덤프가 아닌 **통합**이 목표.

### 처리 대상 식별
`_master-index.md`의 `## Compiled Sources` 목록과 raw/ 디렉토리를 비교하여
아직 목록에 없는 파일만 처리한다. 매번 전체 raw를 읽지 않는다.
처리가 끝나면 `_master-index.md`의 Compiled Sources에 파일명을 추가한다.

1. 주제 식별 → 필요시 topic folder 및 `_index.md` 생성
2. 핵심 아이디어·개념·주장 추출 → wiki article 작성 또는 기존 article 갱신
3. `[[wiki links]]`로 관련 문서 연결
4. `_index.md`, `_master-index.md` 갱신

**다중 주제 처리**: 하나의 raw 파일이 여러 주제를 다루면 각 주제에 맞는 article을 분리 작성하고 cross-link.

**기존 지식과의 관계**:
- 지원 → 보강
- 모순 → 명시적 표시
- 갱신 → 수정
- 새 개념 → 새 article 또는 기존 topic에 추가

### Output Compile Workflow

트리거: "output compile", "output 정리해", "output 승격해"

output/의 미처리 파일을 평가하여 wiki에 승격한다.

#### 처리 대상 식별
`_master-index.md`의 `## Compiled Outputs` 목록과 output/ 디렉토리를 비교하여
아직 목록에 없는 파일만 처리한다. 매번 전체 output을 읽지 않는다.

#### 평가 기준
각 output 파일에 대해 다음 기준으로 판단:

**승격 (wiki article로)**:
- 기존 wiki 문서들 사이의 새로운 연결 관계를 발견한 경우
- 여러 출처를 종합한 비교/분석으로 재사용 가치가 높은 경우
- 독립적인 개념이나 주장이 정리된 경우
- 향후 query에서 다시 참조할 가능성이 높은 경우

**보류 (output에 유지)**:
- 일회성 분석으로 재참조 가능성이 낮은 경우
- 시의성이 있어 시간이 지나면 가치가 떨어지는 경우
- 아직 판단하기 이른 경우

**삭제 (사용자 확인 후)**:
- 명백하게 오류가 있거나 supersede된 경우
- 중복된 내용인 경우

#### 승격 절차
1. output 파일의 주제 식별 → 대상 topic folder 결정
2. 기존 wiki article과 관계 분석 (보강/갱신/신규)
3. **output 파일을 wiki로 이동** (`mv`). 새로 작성하지 않음. 필요한 경우 frontmatter만 보완
   - 단, 기존 article에 통합하는 경우에만 기존 article 본문 수정
4. `[[wiki links]]`로 관련 문서 연결
5. `_index.md`, `_master-index.md` 갱신
6. `_master-index.md`의 `## Compiled Outputs`에 해당 파일명 추가

## Query & Audit

### Query
1. `wiki/_master-index.md` → 관련 topic `_index.md` → 구체적 article 순으로 탐색
2. wiki 지식을 우선 활용. 더 깊은 검증이 필요할 때만 raw 소스 참조
3. 모든 질문은 채팅으로 답변. 사용자가 저장을 요청하기 전까지 파일로 만들지 않는다
4. 답변에는 출처를 `[[wiki-link]]` 형태로 인용

### Query → Output 저장

트리거: "output에 넣어줘", "output에 저장해", "정리해서 output에 넣어"

대화 중 축적된 인사이트·지식·분석을 사용자가 판단하여 output에 저장 요청할 때 실행.

1. 대화 맥락에서 핵심 인사이트·분석·종합 내용을 추출
2. `[[wiki-link]]` 형태로 출처 인용하여 `output/`에 파일로 저장
3. 파일명은 AGENTS.md 파일명 규칙(한국어 + 하이픈)을 따름

### Audit
트리거: "audit", "lint", "health check", "검사해", "정합성 봐줘"

#### 검사 항목
- **끊어진 wiki link**: 본문의 `[[파일명]]`이 실제 wiki 파일과 매칭되지 않는 경우
- **고아 문서**: 어느 `_index.md`에도 등록되지 않은 article. 단 `AGENTS.md`, `_template.md`, `_master-index.md`, 각 `_index.md`는 구조 파일이므로 제외
- **누락된 `## 핵심 요점`**: 템플릿 구조를 따르지 않은 article
- **모순/중복**: 동일 주제의 두 article이 상반된 주장을 하거나 내용이 거의 중복되는 경우
- **누락된 cross-reference**: 같은 tags/source/folder를 공유하는 article 간에 related나 본문 링크가 빠진 경우

#### 토큰 효율적 감사 절차

wiki 규모가 커져도 토큰 소모를 최소화하는 절차. **article 본문은 의심되는 경우에만 읽는다.**

**1단계: 메타데이터 수집 (저렴)**
- `glob` → wiki/ 내 전체 .md 파일 경로 목록 확보
- `_index.md` 5개 + `_master-index.md`만 읽기 → 등록된 article 목록 확보
- `grep "[[...]]"` → 모든 wiki link 대상 목록 추출 (본문 읽기 없이)
- `grep "## 핵심 요점"` → 섹션 존재 여부만 확인

**2단계: 구조 검사 (비교만, 본문 불필요)**
- 고아 문서: (glob 파일 목록) - (_index.md 등록 목록)
- 끊어진 링크: (grep로 추출한 링크 대상) - (glob 파일 목록) = 존재하지 않는 링크
- `## 핵심 요점` 누락: (glob 파일 목록) - (grep 결과에 포함된 파일)

**3단계: frontmatter 스크리닝 (후보만 추출, 본문 불필요)**
- 같은 `source:` raw 파일을 참조하는 article 쌍 → 모순/중복 후보
- `tags`가 많이 겹치는 article 쌍 → 모순/중복 후보
- 같은 `tags`/`source`를 공유하는데 `related`에 서로가 없는 article 쌍 → cross-reference 누락 후보

**4단계: 의심 쌍만 본문 읽기 (최소한)**
- 3단계에서 탐지된 후보 쌍만 개별 article 읽기
- 모순인지, 중복인지, 각자 독립적 가치가 있는지, 링크를 추가해야 하는지 판단

cross-link는 같은 폴더가 아니라 **실제 개념적 관계**가 있을 때만 맺는다. `related`가 비어 있어도 관계가 없으면 정상.

대규모 구조 변경은 사용자 확인 후 진행. 개선 제안을 먼저 설명.

## Conventions

### 언어
- 기본 작성 언어: 한국어
- 기술 용어는 영어 유지: `RAG`, `embedding`, `vector search`, `agent`, `workflow` 등
- 정밀도가 높아지는 경우에만 영어 사용

### 파일명
- 한국어 + 하이픈 (`RAG-접근법-비교.md`, `AI-에이전트-아키텍처.md`)
- 기술 용어(RAG, LLM, AI, Obsidian 등)는 영어 그대로 유지
- 금지: 공백, camelCase, 모조한 이름 (`note1.md`)
 
### Tags
frontmatter의 `tags` 필드 규칙:

- **형식**: kebab-case만 허용. `AI-코딩`, `클로드-코드`, `지식-베이스`
- **금지**: 공백 (`AI 코딩` ✗), 마침표 (`design.md` ✗), 따옴표 (`"태그"` ✗)
- **중복 금지**: 같은 article에 동일 태그 두 번 이상 나오면 안 됨
- **YAML 배열**: `[태그1, 태그2]` 형식. 따옴표 없이 작성
- **영어 용어**: PascalCase 허용 (`GStack`, `Superpowers`, `RAG`, `Obsidian`). 단 공백 불가 (`Claude Code` → `Claude-Code` 또는 `클로드-코드`)
- **일관성**: 동일 개념은 같은 태그 사용. 예: 모든 곳에서 `AI-코딩` 또는 `클로드-코드`로 통일

### Wiki Links

Obsidian 스타일 사용. 링크 형식은 대상에 따라 구분:

**wiki article 간**: `[[파일명]]` — 확장자·경로 없음
- 예: `[[RAG-접근법-비교]]`, `[[design-md-완전-정복]]`
- 파일명이 곧 고유 식별자. 별도 ID 불필요

**raw 파일 참조**: `[[raw/원본파일명.확장자]]` — 경로·확장자 포함
- 예: `[[raw/AI 에이전트 아키텍처 비교 분석.md]]`
- raw 파일명은 공백·특수문자가 있어 명시적 지정 필요

**frontmatter**: YAML 리스트만 사용. `[[wiki links]]` 금지
- 예: `related: [문서-명, 다른-문서-명]`

**related는 양방향 연결**: A의 related에 B가 있으면 B의 related에도 A가 있어야 함.

관련 개념, 소스 요약, 비교 대상을 서로 연결. 고립된 문서가 없도록.
wiki는 시간이 지날수록 더 많이 연결되어야 한다.
