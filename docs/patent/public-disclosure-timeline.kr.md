# 공개 이력 및 증거 타임라인 초안

이 문서는 특허 상담 시 공개예외, 신규성, 해외 출원 가능성을 검토하기 위한 공개 이력 초안이다.

주의: Git commit 날짜는 로컬/저장소 기록 기준이며, 실제 "공중에게 공개된 날"은 GitHub visibility, push 시각, Marketplace 게시 시각, release 게시 시각으로 별도 확정해야 한다.

## 확인된 주요 커밋

| Commit | Date (KST) | 내용 |
|---|---:|---|
| `524fdac` | 2026-03-05 04:23:36 | CLI binary reading JSON from stdin |
| `4f5520d` | 2026-03-05 18:36:01 | Swift AST CLI parser 추가 |
| `9b44e7e` | 2026-03-05 18:41:29 | Swift scanner, bridge, graph builder |
| `8e85a6a` | 2026-03-05 20:10:46 | AI patch diff + graph impact analysis |
| `ae2497b` | 2026-03-05 23:43:48 | auto-analyze settings/toggle |
| `3cd11ac` | 2026-03-05 23:44:56 | auto-analyze on save debounce/concurrency |
| `c6b5785` | 2026-03-07 12:19:15 | public README set polishing |
| `84f8ada` | 2026-03-07 20:14:04 | diff impact visualization + replay validation tooling |
| `309662d` | 2026-03-08 04:32:48 | detailed public beta validation + README refresh |
| `bff9960` | 2026-03-08 04:45:42 | public beta notice moved to top |
| `f405f11` | 2026-03-08 05:11:00 | replay source artifacts for transparency |
| `c0478e1` | 2026-03-08 05:22:24 | public beta v0.1.0-beta.1 release notes |
| `a1010ef` | 2026-03-14 20:54:25 | beta constraints + canonical repo links |
| `c3c509a` | 2026-05-29 06:37:17 | Marketplace listing + hallucination guard branding |
| `906da22` | 2026-08-21 11:47:25 | VS Code Marketplace 0.1.3 preparation |

## 특허 관점의 공개 민감 포인트

다음 커밋 또는 공개물은 발명의 핵심을 외부에 설명했을 가능성이 있다.

- `8e85a6a`: AI patch diff + graph impact analysis
- `84f8ada`: diff impact visualization + replay validation
- `309662d`: public beta validation and README refresh
- `c3c509a`: hallucination guard branding
- `906da22`: Marketplace 0.1.3 상세 설명, 스크린샷, UX 개선

## 추가로 확인할 외부 공개 증거

GitHub:

- repository가 public으로 전환된 정확한 날짜
- `main` branch에 각 commit이 push된 날짜
- PR merge 날짜 및 PR 페이지 URL
- release/tag 생성 날짜

Marketplace:

- 최초 extension 게시 날짜
- `0.1.0`, `0.1.1`, `0.1.2`, `0.1.3` 버전 게시 날짜
- Marketplace 상세 페이지 snapshot 또는 screenshot

기타:

- SNS, 커뮤니티, 블로그, 포트폴리오 게시 날짜
- README badge/link가 외부 검색에 노출된 날짜
- VSIX 파일 배포 또는 공유 날짜

## 공개예외 검토 메모

한국/미국 등 일부 국가는 발명자 본인의 공개에 대해 일정 기간의 grace period를 검토할 수 있다. 그러나 국가별 요건, 절차, 증빙 방식이 다르고, 일부 국가 또는 지역에서는 공개 후 출원이 매우 불리할 수 있다.

따라서 변리사에게 다음 질문을 먼저 해야 한다.

1. 위 공개일 중 어느 날짜를 "최초 공개일"로 보아야 하는가?
2. 공개예외 주장을 위해 출원 시 어떤 서류/진술이 필요한가?
3. 한국 출원만 할지, 미국/PCT까지 고려할지?
4. 이미 공개된 구현과 다른 미공개 개선 아이디어를 별도 발명으로 분리할 수 있는가?

## 현재 레포 상태에서의 추천

1. 최대한 빨리 국내 변리사 상담을 잡는다.
2. 본 폴더의 문서와 위 commit list를 전달한다.
3. 공개일 기준 12개월 이내인지 확인한다.
4. 넓은 청구항은 "그래프 기반 AI 생성 코드 구조 검증"으로 잡고, 좁은 종속항은 Swift/VS Code/Git/색상 UI로 둔다.
5. 추후 Codex plugin/CLI 자동 리뷰 기능은 별도 continuation 또는 분할출원 후보로 관리한다.

