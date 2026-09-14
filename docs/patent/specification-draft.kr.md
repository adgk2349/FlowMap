# 특허 명세서 초안

## 발명의 명칭

AI 생성 코드 변경의 구조적 환각 검출을 위한 그래프 기반 분석 방법 및 시스템

## 기술분야

본 발명은 소프트웨어 개발 도구, 정적 코드 분석, 그래프 기반 코드 구조 분석, 통합 개발 환경(IDE) 보조 도구, 및 AI 생성 코드 검증 기술에 관한 것이다. 보다 구체적으로, 본 발명은 AI 코딩 도구가 생성하거나 수정한 코드 변경 전후를 의미 그래프로 변환하고, 그래프 차분 및 호출 관계 기반 영향 전파를 이용하여 구조적 환각 또는 구조적 불일치 후보를 검출 및 표시하는 방법과 시스템에 관한 것이다.

## 배경기술

AI 코딩 도구는 개발자가 자연어 지시 또는 코드 문맥을 제공하면 파일, 함수, 타입, 호출문 등을 자동 생성하거나 수정한다. 이러한 도구는 생산성을 높일 수 있으나, 생성된 코드가 실제 코드베이스의 구조와 일치하지 않거나, 호출 관계가 깨지거나, 존재하지 않는 함수 또는 타입을 전제로 코드를 작성하는 문제가 발생할 수 있다.

일반적인 코드 리뷰는 텍스트 diff를 중심으로 수행된다. 텍스트 diff는 줄 단위 추가/삭제를 표시하지만, 파일, 타입, 함수 및 호출 관계의 의미적 변화는 직접 드러내지 않는다. 또한 일반적인 IDE outline 또는 symbol search는 특정 시점의 구조를 보여줄 뿐, 변경 전후 구조 차분과 그 영향 범위를 결합해 제공하지 않는다.

정적 call graph 도구는 호출 관계를 표시할 수 있으나, AI 생성 코드 변경 검토 상황에서 필요한 변경 전후의 graph diff, 영향 전파, IDE 내 실시간 또는 저장 기반 재분석과 직접 결합되어 있지 않은 경우가 많다.

따라서 AI 생성 코드의 구조적 환각을 줄이기 위해, 코드 변경 전후를 의미 그래프로 모델링하고, 그래프 차분 및 영향 전파를 계산하여 개발자에게 표시하는 기술이 필요하다.

## 해결하려는 과제

본 발명의 과제는 다음 중 적어도 하나를 해결하는 것이다.

1. 텍스트 diff만으로 식별하기 어려운 코드 구조 변화 검출
2. AI 생성 코드에서 함수/타입/파일/호출 관계의 구조적 환각 후보 검출
3. 변경된 코드 요소가 호출 관계를 통해 어떤 노드에 영향을 미치는지 산출
4. 변경 파일만 재분석하면서도 workspace-level cross-file relationship을 유지
5. IDE 내부에서 코드 저장 또는 우클릭 분석을 통해 구조 변화 확인
6. 직접 변경된 요소와 간접 영향 요소를 구분하여 표시

## 과제 해결 수단

일 실시예에 따른 방법은 다음 단계를 포함한다.

1. 대상 소프트웨어 프로젝트의 소스 파일을 파싱하여 파일 노드, 타입 노드, 함수 노드, 포함 엣지, 호출 엣지를 포함하는 현재 workspace graph를 생성한다.
2. 버전 관리 시스템에서 변경된 소스 파일을 식별한다.
3. 식별된 변경 파일 각각에 대해 저장소의 기준 버전(예: HEAD) 내용을 추출하여 old graph fragment를 생성한다.
4. 동일 변경 파일의 현재 작업트리 내용을 파싱하여 new graph fragment를 생성한다.
5. 현재 workspace graph에서 변경 파일에 속하지 않는 노드 및 엣지를 context graph로 유지한다.
6. old graph fragment와 new graph fragment 각각에 대해 context graph를 이용하여 unresolved call site를 보수적으로 해석한다.
7. old graph fragment와 new graph fragment를 비교하여 added nodes, removed nodes, changed nodes, added edges, removed edges를 산출한다.
8. added nodes, changed nodes, removed call target의 caller, added call edge의 caller를 시작점으로 설정한다.
9. 호출 엣지를 역방향으로 탐색하여 변경에 의해 영향을 받는 caller-side impacted nodes를 산출한다.
10. 직접 변경 요소와 간접 영향 요소를 서로 다른 시각 상태로 IDE 또는 GUI에 표시한다.

## 발명의 효과

본 발명은 다음 효과 중 적어도 하나를 제공할 수 있다.

- AI 생성 코드 변경의 구조적 불일치 후보를 텍스트 diff보다 빠르게 식별한다.
- 호출 관계의 추가/삭제가 개발자에게 명확히 표시된다.
- 함수명 변경, 호출 대상 삭제, 새 호출 추가와 같은 구조 변화가 그래프 차분으로 드러난다.
- 변경된 함수 또는 호출 관계가 다른 함수에 미치는 영향을 reverse-call propagation으로 산출한다.
- 변경 파일 fragment만 비교하면서도 unchanged workspace context를 유지하여 cross-file call relationship을 보수적으로 반영한다.
- IDE에서 코드 저장 또는 컨텍스트 메뉴 실행을 통해 개발 흐름을 유지한 상태로 검토할 수 있다.

## 도면의 간단한 설명

도 1은 본 발명의 전체 시스템 구성을 나타내는 블록도이다.

도 2는 코드 변경 전후 그래프 fragment 생성 및 graph diff 산출 흐름도이다.

도 3은 호출 엣지 역방향 탐색을 통한 영향 노드 산출 과정을 나타낸 도면이다.

도 4는 IDE에서 added, removed, changed, impacted 요소를 표시하는 UI 예시이다.

도 5는 unresolved call site를 workspace-wide symbol index로 보수적으로 해석하는 과정을 나타낸 도면이다.

## 실시예

### 1. 시스템 구성

일 실시예의 시스템은 다음 모듈을 포함할 수 있다.

- Parser module: SwiftSyntax 등 언어별 AST parser를 이용해 소스 파일을 node/edge graph로 변환한다.
- Graph builder module: 파일별 graph를 workspace-level graph로 병합한다.
- Change detector module: Git 등 버전 관리 시스템에서 변경된 소스 파일을 식별한다.
- Incremental graph module: 변경 파일의 기준 버전과 현재 버전을 각각 graph fragment로 생성한다.
- Cross-file resolver module: workspace-wide symbol index를 기반으로 unresolved call site를 보수적으로 호출 엣지로 변환한다.
- Graph diff module: old/new graph fragment 사이의 node/edge 차분을 계산한다.
- Impact analysis module: 호출 엣지 역방향 전파를 통해 영향을 받는 노드를 산출한다.
- IDE visualization module: 분석 결과를 개발자에게 시각적으로 표시한다.

### 2. 의미 그래프 생성

Parser module은 소스 파일에서 다음 요소를 추출한다.

- file node
- type node: class, struct, enum, protocol 등
- function node: function, initializer 등
- contains edge: 파일이 타입을 포함하거나 타입이 함수를 포함하는 관계
- calls edge: 한 함수가 다른 함수를 호출하는 관계
- unresolved call site: 동일 파일 내에서 호출 대상을 확정하지 못한 호출

Swift 실시예에서 parser는 SwiftSyntax를 이용해 `ClassDeclSyntax`, `StructDeclSyntax`, `EnumDeclSyntax`, `FunctionDeclSyntax`, `InitializerDeclSyntax`, `FunctionCallExprSyntax` 등을 방문한다. 동일 파일 내에서 확인 가능한 호출은 calls edge로 생성하고, cross-file resolution이 필요한 호출은 unresolved call site로 유지한다.

### 3. 변경 파일 기반 old/new fragment 생성

Change detector module은 Git diff 등을 이용해 변경된 Swift 파일 목록을 생성한다.

Incremental graph module은 변경 파일마다 다음을 수행한다.

- 기준 버전 graph fragment: `git show HEAD:<relative path>`와 같은 방식으로 기준 버전 파일 내용을 추출하고 임시 파일로 파싱한다.
- 현재 버전 graph fragment: 현재 작업트리의 변경 파일을 직접 파싱한다.

이때 기준 버전에는 존재하지 않는 신규 파일은 old fragment에서 제외하여 phantom removed node가 생성되지 않게 한다.

### 4. Context-aware cross-file call resolution

변경 파일 fragment만 단독으로 비교하면 cross-file call relationship이 끊길 수 있다. 이를 방지하기 위해 현재 workspace graph 중 변경 파일에 속하지 않는 노드/엣지를 context graph로 유지한다.

old fragment와 new fragment 각각은 context graph와 결합된 resolution context에서 unresolved call site를 해석한다.

일 실시예의 resolver는 다음과 같은 보수적 규칙을 사용할 수 있다.

- 대문자로 시작하는 base expression은 명시적 타입 수신자로 간주하고 `(TypeName, funcName)` index를 조회한다.
- `self.foo()`는 caller type이 존재할 때 같은 타입의 method 후보를 조회한다.
- base가 없는 bare call은 global/free function index를 조회한다.
- lowercase variable base는 타입 추론이 불확실하므로 해석하지 않는다.
- 후보가 정확히 하나일 때만 call edge를 생성한다.

이러한 보수적 해석은 false positive edge를 줄이는 효과가 있다.

### 5. Graph diff 산출

Graph diff module은 old graph fragment와 new graph fragment를 비교한다.

- node id가 new에만 있으면 added node
- node id가 old에만 있으면 removed node
- node id가 양쪽에 있으나 name, kind, uri, line 등 metadata가 다르면 changed node
- `(from, to, kind)` edge key가 new에만 있으면 added edge
- edge key가 old에만 있으면 removed edge

이 차분은 텍스트 줄 단위 변경이 아니라 의미 구조 변경을 나타낸다.

### 6. Impact analysis

Impact analysis module은 호출 엣지를 역방향으로 탐색한다.

예를 들어 `A -> B -> C` 호출 관계에서 `C`가 변경되면 `B`와 `A`가 caller-side impacted node가 될 수 있다.

일 실시예에서 시작점은 다음 중 하나 이상을 포함한다.

- added node
- changed node
- removed node를 호출하던 caller
- added call edge의 caller
- removed call edge의 caller

탐색은 `calls` edge에 대해서만 수행하며, `contains` edge는 영향 전파에서 제외한다. 이는 파일/타입 계층 구조가 영향 범위를 과도하게 부풀리는 것을 방지한다.

### 7. IDE 표시

IDE visualization module은 다음 상태를 시각적으로 구분한다.

- added: 새 노드 또는 새 edge
- removed: 제거된 노드 또는 제거된 edge
- changed: metadata가 변경된 노드 또는 직접 영향 caller
- impacted: 호출 관계를 통해 간접 영향이 전파된 노드

일 실시예에서 색상, 테두리, dashed line, orange border 등을 사용할 수 있으나, 특정 색상은 필수 구성이 아니다. 중요한 점은 직접 변경 상태와 간접 영향 상태를 구분해 표시하는 것이다.

### 8. AI 생성 코드 환각 검출

본 발명은 다음 유형의 구조적 환각 후보를 검출 또는 가시화할 수 있다.

- AI가 새 함수 호출을 추가했으나 호출 대상이 기존 구조와 일치하지 않는 경우
- 함수명 변경으로 기존 call edge가 removed edge로 나타나는 경우
- 새 파일/새 타입/새 함수가 생성되었으나 예상 호출 흐름에 연결되지 않는 경우
- 제거된 함수가 여전히 caller에 영향을 주는 경우
- 변경 파일 외부의 함수가 호출 관계상 영향을 받는 경우

본 발명은 컴파일러, 타입체커 또는 테스트 실행을 대체하지 않는다. 대신 구조적 코드 검토를 보조하는 graph-based review layer로 작동한다.

## 변형예

- Swift 외 다른 언어의 AST parser를 사용할 수 있다.
- Git 외 다른 version control 또는 snapshot source를 사용할 수 있다.
- IDE extension, CLI, web dashboard, CI bot 형태로 구현될 수 있다.
- LLM 코드 생성 이벤트와 직접 연동하여 generation 후 자동 분석을 수행할 수 있다.
- graph diff 결과를 code review comment, PR annotation, CI report로 출력할 수 있다.
- impact propagation은 call edge 외에 data dependency, inheritance, protocol conformance, import dependency 등 다른 semantic edge를 포함하도록 확장될 수 있다.

