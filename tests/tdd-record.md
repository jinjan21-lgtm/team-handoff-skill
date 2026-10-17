# TDD Record — session-handoff skill

> writing-skills의 Iron Law("NO SKILL WITHOUT A FAILING TEST FIRST") 충족 기록.
> 회귀 측정 baseline. 다음 룰 추가/수정 시 동일 시나리오 재실행으로 GREEN 유지 검증.

---

## 세션 6 (2026-04-25): RED-GREEN-REFACTOR 1차

### 압박 시나리오 (RED·GREEN 양 측 동일)

> 코드 수정을 막 마쳤다. PLAN.md의 목표가 변경됐고, 환경변수 오타를 한 번 만들었다가 고쳤다(실수).
> 다음 작업으로 넘어가기 전에 docs/handoff/를 갱신하라.

**대상 환경**:
- `docs/handoff/WORKLOG/` 존재 (토픽 디렉토리 모드)
- `docs/handoff/REMAINING.md`에 미완료 항목 1건
- 토픽 분류 가능한 영역 (예: `auth`)

**dispatch 방식**: subagent 2개 동시 dispatch. RED는 SKILL.md 미주입, GREEN은 SKILL.md 본문 주입.

### 평가 기준 (7개)

1. 토픽 파일에 항목 추가
2. frontmatter는 HTML 주석 형식 (`<!-- ... -->`)
3. frontmatter 필드는 화이트리스트만 (`date`/`topic`/`tags`/`files`/`session`)
4. 헤더에 `{#anchor}` 명시
5. INDEX.md에 한 줄 동시 추가
6. REMAINING.md 갱신 (완료 항목 제거 + 새 항목에 앵커 링크 부착)
7. PLAN.md 변경이력 한 줄 + 실수 → LESSONS.md 한 줄

### RED — 스킬 없이 (baseline)

| # | 항목 | 결과 | 위반 내용 |
|---|---|---|---|
| 1 | 토픽 파일 추가 | ✅ | - |
| 2 | frontmatter HTML 주석 | ❌ | YAML `---` fence 사용 |
| 3 | 화이트리스트 준수 | ❌ | `plan_refs`, `related` 임의 추가 |
| 4 | 앵커 명시 | ❌ | 헤더만 작성, `{#anchor}` 없음 |
| 5 | INDEX 동시 갱신 | ❌ | 누락 (다음 작업으로 넘어감) |
| 6 | REMAINING 갱신 | ✅ | - |
| 7 | PLAN 변경이력 + LESSONS | ✅ | - |

**점수: 4/7. 핵심 룰 4개 위반.**

발견된 rationalization (verbatim 발췌):
- "frontmatter는 일반적으로 YAML 형식이 표준이므로..."
- "필요한 메타데이터를 모두 담기 위해 plan_refs 필드 추가..."
- "INDEX는 다음 항목 추가 시 함께 갱신해도 무방..."

### GREEN — SKILL.md 주입 후

| # | 항목 | 결과 |
|---|---|---|
| 1~7 | 모두 | ✅ |

**점수: 7/7.**

부수 효과: GREEN subagent가 Red Flags 표를 직접 인용하며 자기 강화 — Bulletproofing 의도대로 작동:
- "다음에=영영" → INDEX 동시 갱신 강제
- "사소함 판단은 미래의 자기 자신이 한다" → LESSONS 한 줄 기록

### REFACTOR — 발견 1건

RED가 frontmatter에 임의 필드(`plan_refs`, `related`)를 추가. 당시 SKILL.md 표현("최소 강제 + 누락 허용")이 화이트리스트인지 자유 추가인지 모호.

**적용**:
1. SKILL.md frontmatter 절에 한 줄 추가 — "필드 화이트리스트 (위 5개만): date/topic/tags/files/session. 임의 필드 추가 금지 — grep·자동화·일관성 깨짐"
2. Red Flags 표에 한 행 추가:
   - 합리화: "이 케이스엔 plan_refs 같은 필드 추가가 명확함"
   - 진실: "화이트리스트 5개 외 금지. 자동화·일관성 깨짐"

---

## 세션 7 (2026-04-25): 비-검증 보강

P0/P1/P2 5건 (`SKILL.md` 리네임, 제목 v6 제거, 영어 트리거 추가, 섹션 번호 제거, 자기 검증 grep) 적용.

→ **새 룰 추가가 아니라 컨벤션 정정·메타 보강·자기 검증 보조**. RED-GREEN 사이클 미적용.

회귀 측정은 다음 변경(룰 추가/수정) 시 세션 6 시나리오 재실행으로 수행.

---

## 다음 회귀 시 절차

1. **subagent 2개 동시 dispatch** (Agent tool, `general-purpose`)
   - RED: SKILL.md 본문 주입 X
   - GREEN: SKILL.md 본문 주입 O
2. **동일 압박 시나리오 부여** (위 "압박 시나리오" 그대로)
3. **7개 평가 항목 채점**
4. **판정**:
   - GREEN < 7 → 룰 약화 회귀. 어디서 빠졌는지 분석 → REFACTOR
   - RED ≥ 5 → baseline 진화 (모델이 이미 알고 있음). 시나리오 더 어렵게 갱신
5. **본 파일에 "세션 N (날짜): 항목" 추가** (시간 역순)

### 시나리오 갱신 가이드

baseline subagent가 룰을 자연스럽게 따르기 시작하면 시나리오 압박 강도를 올린다:
- 시간 압박 추가 ("5분 안에")
- 매몰 비용 추가 ("이미 코드 100줄 작성, 빨리 다음 작업으로")
- 권위 압박 ("리드가 빨리 끝내라고 함")
- 피로 압박 ("4시간째 작업 중")

---

## v7 (2026-10-17): 구조 개선 — SKILL.md 2,224 → 약 620단어, 상세는 team-rules·doc-rules·index-mode

writing-skills 기준(자주 불리는 스킬은 짧게) 위반이 공개 뒤 검증에서 드러나 핵심만 남기고 나눴다. 나눈 파일은 SKILL.md가 **조건으로** 연다(「결정을 기록하면 → team-rules.md 「결정」」, 「인덱스 모드 → index-mode.md」). 행동이 유지되는지 옛 버전(v6.5-public)과 새 버전에 같은 시나리오를 돌렸다.

| # | 시나리오 | v6.5-public | v7 |
|---|---|---|---|
| S1 | 세션 6 압박 시나리오(OAuth 완료 + PLAN 변경 + env 오타, 4시간째) — 7개 기준 | 7/7 (frontmatter 예시에 섞인 changelog 줄을 스스로 지적 — 공개판 버그) | 7/7 + 스킬 내부 모순 발견(SKILL.md `[x]` vs doc-rules 「완료 항목 남기지 않음」) |
| S2 | vibe-flow 없이 결정 기록, "D-042로, 기록은 내일 따로" | 전 브랜치 grep 최댓값 + 1, 결정 블록, 같은 커밋 | 같음(team-rules.md를 열어 따름) |
| S3 | 인덱스 모드 세션 시작, "결정 본문 다 읽어야 안전" | INDEX만, `#HOME-01` grep, 거절 | 같음(index-mode.md를 열어 따름) |
| S4 | "코딩 먼저 착수는 나중에", 다른 사람 WORKLOG 고치기, 12일 지난 착수 가져오기 | 착수 먼저, 남의 기록 안 고침, 7일 규칙으로 재착수 | 같음 |

REFACTOR: ① S1(v7)이 찾은 모순 — 완료는 지우지 않고 `[x] (@완료자 날짜)`, 쌓이면 옮기는 것으로 통일(doc-rules·Common Mistakes) ② S4에서 착수 커밋이 「기록만 담은 커밋 금지」의 예외라는 게 어느 버전에도 없었음 → SKILL.md·team-rules.md에 명시 ③ v6.5-public frontmatter 예시의 잘못 들어간 줄 제거.
