# 특허 상담/출원 준비 체크리스트

## 1. 먼저 확인할 결론

FlowMap은 특허 후보가 될 수 있으나, 이미 공개된 상태이므로 공개일과 공개예외 적용 가능성을 먼저 확인해야 한다.

변리사에게 처음 전달할 핵심 문장:

> Swift/AI 생성 코드 변경을 AST 기반 workspace graph로 변환하고, Git 기준 버전과 현재 작업트리의 graph fragment를 context-aware cross-file resolution으로 비교하여 graph diff 및 reverse-call impact propagation을 산출하는 기술입니다. 이미 GitHub/Marketplace에 공개되어 있어 공개예외 및 해외 출원 전략 검토가 필요합니다.

## 2. 공개일 확인

아래 날짜와 증거를 정리해야 한다.

- GitHub 저장소 최초 public 전환일
- FlowMap 관련 최초 commit 공개일
- README에서 "AI hallucination" 또는 "Hallucination Guard"를 처음 명시한 commit
- VS Code Marketplace 최초 게시일
- `0.1.0`, `0.1.1`, `0.1.2`, `0.1.3` 게시일
- 스크린샷/검증 리포트 공개일
- 블로그, SNS, 커뮤니티 홍보 여부 및 날짜

필요 증거:

- commit hash
- GitHub release/tag URL
- Marketplace extension URL
- Marketplace version history screenshot
- repository visibility history 가능 자료
- README raw URL 또는 commit permalink

## 3. 변리사에게 넘길 기술 자료

필수:

- `docs/patent/invention-disclosure.kr.md`
- `docs/patent/specification-draft.kr.md`
- `docs/patent/claims-draft.kr.md`
- `README.md`
- `editor/vscode/README.md`
- `reports/replay-validation-bundle.md`
- `reports/public-beta-validation-detail.md`

구현 근거:

- `crates/engine/src/lib.rs`
- `crates/engine/src/incremental_graph.rs`
- `crates/engine/src/graph_diff.rs`
- `crates/engine/src/impact_analysis.rs`
- `crates/engine/src/cross_file_resolver.rs`
- `parsers/swift-ast/Sources/FlowMapSwiftAST/main.swift`
- `editor/vscode/webview/graph.js`
- `editor/vscode/webview/graph.modes.js`
- `editor/vscode/webview/graph.layouts.js`

## 4. 도면 후보

도면 1: 전체 시스템 구성

```text
Swift Workspace
      |
      v
SwiftSyntax Parser -> Workspace Semantic Graph
      |
      v
Git Change Detector -> Changed Swift Files
      |
      v
HEAD Fragment + Current Fragment
      |
      v
Context-aware Cross-file Resolver
      |
      v
Graph Diff -> Impact Propagation -> IDE Visualization
```

도면 2: fragment 비교 흐름

```text
Changed File List
      |
      +-- git show HEAD:path -> Old Fragment
      |
      +-- working tree file -> New Fragment

Old Fragment + Unchanged Context -> Resolved Old Graph
New Fragment + Unchanged Context -> Resolved New Graph

Resolved Old Graph vs Resolved New Graph -> Graph Diff
```

도면 3: 영향 전파

```text
Caller A -> Caller B -> Changed Callee C

Start: C
Reverse traversal over calls:
C -> B -> A

Impacted: B, A
```

도면 4: UI 상태

```text
Green: added
Red dashed: removed
Yellow: changed
Orange border: impacted
```

도면 5: 보수적 호출 해석

```text
Unresolved call site:
NetworkManager.connect()

SymbolIndex:
(NetworkManager, connect) -> [candidate func node]

If exactly one candidate:
emit calls edge
else:
skip to avoid false positive
```

## 5. 선행기술 검색 키워드

한국어:

- 코드 변경 그래프 차분
- 정적 분석 호출 그래프 변경 영향 분석
- AI 생성 코드 검증
- 코드 환각 검출
- IDE 코드 구조 시각화
- 소스코드 의미 그래프 차분

영어:

- graph diff code review
- semantic diff call graph
- AI generated code validation
- code hallucination detection
- static analysis impact propagation
- IDE graph-based code review
- AST graph diff
- call graph impact analysis
- LLM generated code verification

## 6. 특허성 주장 포인트

기술적 문제:

- AI 생성 코드는 텍스트 diff에서 구조적 불일치가 숨겨질 수 있다.
- 단순 call graph는 변경 전후 차분과 영향 전파를 제공하지 않는다.
- 변경 파일만 보면 cross-file call relationship이 끊긴다.

기술적 해결:

- 변경 파일의 HEAD/current graph fragment를 별도로 생성한다.
- unchanged workspace context를 유지하여 fragment call resolution을 수행한다.
- graph diff와 reverse-call impact propagation을 결합한다.
- IDE에서 변경 상태와 영향 상태를 구분 표시한다.

기술적 효과:

- 구조적 환각 후보를 더 빨리 탐지한다.
- false-positive call edge를 줄인다.
- 변경 파일 외부의 영향 범위를 확인한다.
- 개발자가 AI 생성 코드의 구조적 신뢰성을 검토할 수 있다.

## 7. 출원 전략 후보

### A안: 빠른 국내 우선출원

- 장점: 공개일 리스크에 가장 빠르게 대응
- 단점: 해외 전략은 별도 검토 필요
- 문서: 이 폴더의 초안을 기반으로 변리사가 명세서 정리

### B안: 국내 출원 후 PCT 검토

- 장점: 해외 가능성 열어둠
- 단점: 공개 이후 국가별 리스크 검토 필요

### C안: 영업비밀/오픈소스 전략 유지

- 장점: 비용 낮음
- 단점: 이미 공개된 핵심 아이디어 보호 한계

## 8. 변리사에게 물어볼 질문

1. 이미 GitHub/Marketplace 공개된 상태에서 한국 공개예외 주장이 가능한가?
2. 공개예외 주장을 하려면 어떤 증빙과 절차가 필요한가?
3. 미국 provisional 또는 non-provisional 출원이 의미 있는가?
4. 유럽/일본 출원 가능성은 공개 때문에 어느 정도 제한되는가?
5. "AI hallucination"을 청구항에 넣는 것이 좋은가, 명세서에만 넣는 것이 좋은가?
6. Swift/VS Code에 한정하지 않은 넓은 청구항이 가능한가?
7. 일반 call graph/diff tool 대비 진보성 포인트를 어떻게 잡을 것인가?
8. 공개된 GitHub 코드가 실시예로 충분한가?

## 9. 다음 작업

- 최초 공개일 타임라인 작성
- 관련 commit hash 수집
- 도면을 정식 도면 형태로 변환
- 영어 명세서 초안 준비
- 선행기술 검색 결과 정리
- 변리사 상담 후 청구항 범위 조정

