# 발명신고서 초안

## 1. 발명 명칭

AI 생성 코드 변경의 구조적 환각 검출을 위한 그래프 기반 분석 방법 및 시스템

## 2. 발명자/출원인 메모

- 발명자 후보: SeungMin Lee
- 프로젝트명: FlowMap
- 공개 저장소: `FlowMap-AI_Code_Hallucination_Guard_for_Swift`
- 제품 형태: VS Code extension + Rust graph engine + SwiftSyntax parser
- 공개 상태: GitHub 및 VS Code Marketplace 공개 이력 있음
- 변리사 확인 필요: 최초 공개일, Marketplace 공개일, GitHub push/release 공개일, README/스크린샷 공개일

## 3. 해결하려는 문제

AI 코딩 도구가 생성한 코드는 텍스트 diff 상으로는 그럴듯해 보여도 다음과 같은 구조적 환각을 포함할 수 있다.

- 존재하지 않거나 잘못된 함수/타입 관계를 전제로 한 호출 추가
- 함수명 또는 타입명 변경 후 호출 관계 단절
- 새 파일/새 함수 추가 후 실제 호출 흐름에 연결되지 않는 구조
- 기존 호출 edge 제거로 인한 downstream 영향 누락
- 파일 내부 구조는 정상처럼 보이나 cross-file call 관계가 깨지는 경우

기존 텍스트 diff, 일반 IDE outline, 단순 call graph는 코드 변경 전후의 의미 구조 변화와 영향 전파를 한 화면에서 보여주지 못한다. 특히 AI 생성 코드 검토 상황에서는 "무엇이 바뀌었는가"보다 "구조적으로 믿어도 되는가"가 중요하다.

## 4. 발명의 핵심 아이디어

코드 변경 전후를 각각 의미 그래프로 변환한 뒤, 그래프 차분과 호출 관계 기반 영향 전파를 계산하여 AI 생성 코드 변경의 구조적 이상 징후를 표시한다.

핵심 조합은 다음과 같다.

1. Swift AST에서 파일, 타입, 함수, 호출 관계를 노드/엣지 그래프로 추출한다.
2. Git 변경 파일을 탐지한다.
3. 변경 파일의 HEAD 버전과 현재 작업트리 버전을 각각 파싱하여 old fragment와 new fragment를 생성한다.
4. 변경되지 않은 workspace graph context를 결합하여 cross-file call resolution을 수행한다.
5. old/new fragment 그래프를 비교하여 added node, removed node, changed node, added edge, removed edge를 계산한다.
6. 변경 노드 및 삭제된 호출 대상의 caller를 시작점으로 하여 call edge의 역방향 그래프 탐색을 수행한다.
7. 직접 변경 노드와 간접 영향 노드를 구분해 IDE UI에서 색상/선 스타일로 표시한다.

## 5. 구현 근거

현재 구현은 다음 코드에 근거한다.

- `crates/engine/src/lib.rs`
  - workspace graph 생성
  - Git 변경 파일 탐지
  - HEAD/current fragment 비교
  - diff 및 impact payload 생성
- `crates/engine/src/incremental_graph.rs`
  - `git show HEAD:<path>` 기반 old fragment 생성
  - 현재 파일 기반 new fragment 생성
- `crates/engine/src/graph_diff.rs`
  - node/edge 차분 계산
- `crates/engine/src/impact_analysis.rs`
  - call edge 역방향 BFS 기반 영향 전파
- `crates/engine/src/cross_file_resolver.rs`
  - workspace-wide symbol index 기반 보수적 cross-file call resolution
- `parsers/swift-ast/Sources/FlowMapSwiftAST/main.swift`
  - SwiftSyntax 기반 file/type/function/call-site 추출
- `editor/vscode/webview/*`
  - diff/impact 상태 시각화

## 6. 기존 기술과의 차별점

기존 기술은 일반적으로 다음 중 하나에 머문다.

- 텍스트 기반 diff
- 정적 call graph 생성
- IDE symbol outline
- 코드 품질 lint 또는 compile/type-check
- 테스트 실행 결과 기반 검증

FlowMap의 차별점은 다음의 결합이다.

- AI 생성 코드 변경 검토 상황을 전제로 한다.
- 텍스트 diff가 아니라 AST 기반 의미 그래프를 diff 대상으로 사용한다.
- 변경 파일만 old/new fragment로 재구성하면서, unchanged workspace context를 유지하여 cross-file call을 보수적으로 재해석한다.
- added/removed/changed graph element와 impacted graph element를 분리한다.
- removed call target 및 added call edge의 caller를 직접 영향 노드로 재분류한다.
- IDE 내부에서 파일 저장/우클릭 분석과 연결되어 코드 수정 직후 구조 변화가 표시된다.

## 7. 기술적 효과

- AI 생성 코드의 구조적 환각 후보를 텍스트 diff보다 빠르게 식별할 수 있다.
- 함수/타입/파일 단위 호출 관계 변화가 시각적으로 분리된다.
- 단순 변경 지점뿐 아니라 downstream 영향 범위를 확인할 수 있다.
- Non-Swift 변경에 대한 false positive를 낮추고 Swift 구조 변경 탐지율을 검증 지표로 관리할 수 있다.
- IDE 내에서 코드 작성 흐름을 유지하며 구조 검토를 수행할 수 있다.

## 8. 검증 근거

현재 공개 베타 검증 자료:

- `reports/replay-validation-bundle.md`
  - Total commit pairs: 209
  - Swift / Non-Swift pairs: 112 / 97
  - TP / TN / FP / FN: 106 / 97 / 0 / 6
  - Non-Swift FP Rate: 0%
  - Swift Detection Rate: 94.64%
  - Overall Match Rate: 97.13%
- `reports/public-beta-validation-detail.md`
  - Sample Scenario Regression: 60 / 60 passed
  - Public Beta Gate: PASS

주의: 위 수치는 제품 성능 검증 자료이지 특허성 자체를 입증하는 자료는 아니다. 다만 기술적 효과와 실시 가능성을 설명하는 보조 자료로 사용할 수 있다.

## 9. 권리화 포인트 후보

강하게 가져갈 포인트:

- HEAD/current AST graph fragment 비교
- unchanged workspace context를 이용한 변경 파일 fragment의 cross-file call 재해석
- graph diff와 reverse-call impact propagation의 결합
- 직접 변경 노드와 간접 영향 노드의 분리 표시
- AI 생성 코드 변경 검토를 위한 IDE 연동 워크플로우

좁게 제한해야 할 가능성이 있는 포인트:

- SwiftSyntax 사용
- VS Code extension UI
- 특정 색상 표현
- 특정 레이아웃 알고리즘

## 10. 공개 리스크

이미 GitHub/Marketplace 공개가 이루어진 상태이므로, 신규성 및 공개예외 적용 가능성을 변리사와 즉시 검토해야 한다.

필요 자료:

- 최초 GitHub public 공개일
- 최초 Marketplace 공개일
- README에 hallucination guard 표현이 등장한 일자
- 스크린샷/릴리즈 노트 공개일
- 관련 커밋 해시

