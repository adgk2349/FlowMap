# 변리사 상담용 1페이지 요약

## 상담 목적

GitHub 및 VS Code Marketplace에 공개된 FlowMap 기술에 대해 특허 출원 가능성, 공개예외 적용 가능성, 국내/해외 출원 전략을 검토하고 싶습니다.

## 제품/기술 개요

FlowMap은 Swift 프로젝트를 파일/타입/함수/호출 관계 그래프로 변환하고, AI 코딩 도구가 생성한 코드 변경 전후의 구조적 차이를 시각화하는 VS Code extension 및 로컬 분석 엔진입니다.

핵심 기능:

- SwiftSyntax 기반 AST parsing
- workspace-level semantic graph 생성
- Git 변경 파일 탐지
- HEAD 버전과 현재 작업트리 버전의 graph fragment 비교
- context-aware cross-file call resolution
- added/removed/changed node 및 edge 산출
- call edge 역방향 전파 기반 impacted node 산출
- IDE에서 diff/impact 상태 시각화

## 특허 후보 발명

후보 명칭:

> AI 생성 코드 변경의 구조적 환각 검출을 위한 그래프 기반 분석 방법 및 시스템

핵심 발명 포인트:

1. 변경 파일의 기준 버전과 현재 버전을 각각 AST 기반 의미 그래프로 변환
2. 변경되지 않은 workspace context를 이용해 cross-file call을 보수적으로 재해석
3. old/new graph fragment diff로 added/removed/changed node 및 edge를 산출
4. 호출 엣지 역방향 탐색으로 간접 영향 노드를 산출
5. AI 생성 코드 변경 검토를 위해 직접 변경과 간접 영향을 IDE에서 구분 표시

## 공개 상태

이미 GitHub 및 VS Code Marketplace에 공개된 상태입니다. 공개예외 및 국가별 신규성 리스크 검토가 필요합니다.

주요 커밋:

- `8e85a6a` (2026-03-05): AI patch diff + graph impact analysis
- `84f8ada` (2026-03-07): diff impact visualization + replay validation tooling
- `309662d` (2026-03-08): public beta validation and README refresh
- `c3c509a` (2026-05-29): Marketplace listing + hallucination guard branding
- `906da22` (2026-08-21): Marketplace 0.1.3 preparation

정확한 public 전환일과 Marketplace 게시일은 추가 확인이 필요합니다.

## 검증 자료

공개 베타 검증:

- Total commit pairs: 209
- Swift / Non-Swift pairs: 112 / 97
- TP / TN / FP / FN: 106 / 97 / 0 / 6
- Non-Swift FP Rate: 0%
- Swift Detection Rate: 94.64%
- Overall Match Rate: 97.13%
- Sample scenario regression: 60 / 60 passed

자료 위치:

- `reports/replay-validation-bundle.md`
- `reports/public-beta-validation-detail.md`

## 상담 질문

1. 공개 이후 한국 출원 시 공개예외 적용 가능성이 있는지
2. 공개예외를 위해 필요한 증빙과 출원 절차
3. 미국 출원 또는 PCT 가능성
4. "AI hallucination" 표현을 청구항에 넣는 것이 적절한지
5. Swift/VS Code에 한정하지 않는 넓은 청구항 가능성
6. 일반 call graph/diff tool 대비 진보성 포인트
7. 현재 공개 코드와 README가 신규성에 미치는 영향

## 전달 문서

- `docs/patent/invention-disclosure.kr.md`
- `docs/patent/specification-draft.kr.md`
- `docs/patent/claims-draft.kr.md`
- `docs/patent/filing-checklist.kr.md`
- `docs/patent/public-disclosure-timeline.kr.md`

