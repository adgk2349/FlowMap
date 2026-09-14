# FlowMap Patent Draft Package

이 폴더는 FlowMap의 특허 상담/출원 준비를 위한 기술 초안입니다.

중요: 이 문서는 법률 의견이 아닙니다. 최종 특허성, 신규성, 진보성, 청구항 범위, 공개예외 주장 가능성은 변리사 검토가 필요합니다.

## 포함 문서

- [invention-disclosure.kr.md](invention-disclosure.kr.md)
  - 변리사에게 전달할 발명신고서 형식의 요약 문서
- [specification-draft.kr.md](specification-draft.kr.md)
  - 명세서 초안: 기술분야, 배경기술, 과제, 구성, 효과, 실시예
- [claims-draft.kr.md](claims-draft.kr.md)
  - 청구항 초안: 방법, 장치/시스템, 기록매체, 종속항 후보
- [filing-checklist.kr.md](filing-checklist.kr.md)
  - 공개일/자료/선행기술/출원 전략 체크리스트

## 권장 발명 명칭 후보

1. AI 생성 코드 변경의 구조적 환각 검출을 위한 그래프 기반 분석 방법 및 시스템
2. 코드 변경 전후의 의미 그래프 차분 및 영향 전파를 이용한 AI 생성 코드 검증 방법
3. IDE 환경에서 코드 변경의 구조적 불일치를 검출하는 그래프 차분 기반 코드 검토 시스템

## 핵심 발명 포인트

FlowMap의 특허 후보는 "그래프 시각화" 자체가 아니라 다음 조합입니다.

- Swift AST를 파일/타입/함수/호출 관계 그래프로 변환
- Git 변경 파일에 대해 HEAD 버전과 현재 작업트리 버전을 각각 파싱
- 변경 파일 그래프 fragment를 변경되지 않은 workspace context와 결합해 cross-file call을 보수적으로 재해석
- old/new semantic graph diff를 계산해 added/removed/changed node 및 edge를 산출
- 호출 edge의 역방향 전파로 impacted node를 계산
- IDE/Marketplace extension에서 AI 생성 코드 변경의 구조적 환각 후보를 색상/상태로 표시

## 현재 구현 근거

- `crates/engine/src/lib.rs`
- `crates/engine/src/incremental_graph.rs`
- `crates/engine/src/graph_diff.rs`
- `crates/engine/src/impact_analysis.rs`
- `crates/engine/src/cross_file_resolver.rs`
- `parsers/swift-ast/Sources/FlowMapSwiftAST/main.swift`
- `editor/vscode/webview/*`
- `reports/replay-validation-bundle.md`
- `reports/public-beta-validation-detail.md`

