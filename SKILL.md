---
name: team-handoff
description: (team, shared docs/handoff/) Use when resuming a prior session's project context (PLAN/WORKLOG/REMAINING), when completing tasks that need cross-session progress tracking, or when user mentions handoff/continue/resume/where-did-we-leave-off/인수인계/이어서/어디까지/지난 세션
user-invocable: true
---

# 세션 인수인계 프로토콜 (팀 공용)

## Overview

세션 간 프로젝트 컨텍스트 유지 프로토콜. `docs/handoff/`에 PLAN(기획)·WORKLOG(작업 이력)·REMAINING(미완료)·LESSONS(교훈)를 두고 세션을 껐다 켜도 맥락을 즉시 복원한다.

**핵심 원칙:**
1. **AI 인수인계 효율 우선** — 평가축은 "구조 미학"이 아니라 "맥락 회수 비용 절감"
2. **문서는 항상 최신 상태** — 태스크 완료마다 즉시 반영
3. **변경 추적** — 기획 변경 시 이력 필수
4. **3계층 검색 강제** — 긴 md 참조 시 INDEX → 앵커 → 본문. **본문 통째 로드 금지**
5. **Letter = Spirit** — 룰의 letter를 위반하면 spirit도 위반한 것이다

## When to Use

- **사용**: 세션 시작 시, 코드 작성·수정·기능 구현 완료 후, 사용자 명시 호출(`/team-handoff`)
- **사용 X**: 단발성 질문 응답, 읽기 전용 탐색·조회, 사용자가 "기록하지 마" 명시

## Quick Reference

| 문서 | 역할 | 업데이트 시점 |
|------|------|-------------|
| `PLAN.md` | 프로젝트 기획 | 기획 변경 시 |
| `WORKLOG.md` 또는 `WORKLOG/INDEX.md` | 작업 이력 진입점 | 태스크 완료 시 |
| `WORKLOG/<topic>.md` (디렉토리 모드) | 토픽별 상세 | 태스크 완료 시 |
| `REMAINING.md` | 미완료 태스크 | 태스크 완료/추가/변경 시 |
| `LESSONS.md` | 실수·교훈 | 실수 발생 시 (삭제 금지) |
| `INDEX.md` (인덱스 모드) | 진입 인덱스 — 문서마다 INDEX 경로·언제 본문을 여나 | 문서 구조가 바뀔 때 |
| `DECISIONS.md` | 결정 INDEX(인덱스 모드) — 본문은 결정 날짜의 달마다 `DECISIONS-YYYY-MM.md`(넘치면 `-2`) | 결정할 때 (본문 + INDEX 한 줄) |

## 문서 저장 위치

`docs/handoff/` 디렉토리 (`.claude/` 내부 X — 보호 영역).

- 여러 사람이 쓰는 레포는 **토픽 디렉토리 모드 이상**을 쓴다 (`docs/handoff/WORKLOG/` — 같은 파일 한 곳에 몰리는 충돌을 줄인다).
- **단일 파일 모드 (작은 프로젝트)**: `docs/handoff/{PLAN, WORKLOG, REMAINING, LESSONS}.md`
- **토픽 디렉토리 모드 (대형 프로젝트, 권장)**:
  ```
  docs/handoff/
  ├── {PLAN, REMAINING, LESSONS}.md
  ├── WORKLOG/{INDEX.md, <topic>.md, ...}
  └── specs/<feature>.md
  ```

- **인덱스 모드 (장문·대형 프로젝트)**: 진입 인덱스 → 문서별 INDEX → 본문. 옛 경로(`PLAN.md`·`DECISIONS.md`·`LESSONS.md`·`NORTHSTAR.md`)가 INDEX가 되고, 본문은 같은 폴더의 `PLAN-body.md`·`DECISIONS-YYYY-MM.md`…·`LESSONS-body.md`·`NORTHSTAR-body.md`로 옮겨져 있다(텍스트는 그대로). 완료 항목은 `REMAINING-done-YYYY-MM.md`, 지난 WORKLOG INDEX 줄은 `WORKLOG/INDEX-YYYY-MM.md`. 형식·임계값의 원천은 `.claude/skills/vibe-flow/docs/structure.md` 「인덱스 계층」.
  ```
  docs/handoff/
  ├── INDEX.md                         진입 인덱스
  ├── {PLAN, DECISIONS, LESSONS, NORTHSTAR}.md   문서별 INDEX
  ├── PLAN-body.md · DECISIONS-YYYY-MM*.md · LESSONS-body.md · NORTHSTAR-body.md
  ├── REMAINING.md (활성만) · REMAINING-done-YYYY-MM.md
  ├── goals/{INDEX.md, INDEX-done.md, G-###-*.md}
  └── WORKLOG/{INDEX.md (최근 30줄), INDEX-YYYY-MM.md, <topic>.md}
  ```

전환은 사용자 명시 명령으로만. 절차: [migration.md](migration.md). 인덱스 모드 전환은 `python .claude/skills/vibe-flow/scripts/handoff.py migrate` → `verify-migration`(무손실 검증).

---

## 다인(N명) 협업 규칙

이 스킬은 레포 안(`.claude/skills/team-handoff/`)에 두는 **팀 공용 사본**이다. **N명의 참여자가 각자의 AI 세션**으로 같은 `docs/handoff/` 1세트를 쓴다. 참여자 수는 정해져 있지 않고, **영역별 담당·책임을 나누지 않는다**. 누구든 어떤 항목이든 집을 수 있다.

1. **세션 시작 전 `git pull`** — 다른 참여자가 남긴 기록을 먼저 받는다. 그다음 아래 「세션 시작 시」 순서대로 읽는다.
2. **작성자 표기 (필수)** — 작성자 = `git config user.name`.
   - WORKLOG 항목 제목: `## YYYY-MM-DD G-### 한 줄 제목 @작성자 #tag {#YYYY-MM-DD-slug-작성자}` — **anchor 끝에 항상 `-작성자`** (N명이 같은 날 같은 slug를 써도 충돌하지 않게). vibe-flow 골 작업이면 골 ID를 넣고, 골과 무관한 기록(토의·결정 정리 등)은 `G-###` 자리를 비운다
   - INDEX 한 줄: `- YYYY-MM-DD [topic] @작성자 G-### 한 줄 요약 → [topic.md#anchor](topic.md#anchor)`
   - LESSONS 항목 끝: `(@작성자, 날짜)`
3. **REMAINING은 담당 없이 쓴다**
   - 할 일: `- [ ] 할 일 → 링크` (담당 표기 없음. 누구나 집을 수 있음)
   - **착수 표시(claim):** 집을 때 `- [~] G-### 할 일 (착수 @작성자 YYYY-MM-DD) → <브랜치>`로 바꾸고 **바로 커밋·푸시**한다. 다른 AI 세션의 중복 작업을 막기 위해서다. 골 작업은 `python .claude/skills/vibe-flow/scripts/handoff.py claim G-### "<할 일>" <브랜치>`가 중복 검사 → 브랜치 → 착수 줄 커밋 → push까지 한 번에 하고, push가 확인된 뒤에만 탐색을 시작한다
   - 착수 표시가 **7일** 넘게 그대로면 누구든 다시 집을 수 있다(착수 표시를 자기 것으로 교체하고 WORKLOG에 사유 한 줄)
   - 완료: `- [x] G-### 할 일 (@완료자 YYYY-MM-DD)` (골과 무관하면 `G-###` 없이)
   - 결정이 필요한 항목은 `- [ ] 결정: …`으로 적는다. 참여자는 동등하며 누구나(AI 포함) 결정할 수 있다. 결정하면 `docs/handoff/DECISIONS.md`에 D-nnn으로 기록하고 이 항목을 `[x]`로 바꾼다. 결정자 = 세션 사용자 git user.name, 방식(사용자 / AI 위임), 근거 1~2줄 필수
8. **DECISIONS** — 결정 기록은 삭제·수정 금지, 뒤집을 때는 새 D-nnn + 이전 항목 상태만 「대체됨 → D-nnn」으로 바꾼다. 인덱스 모드에서는:
   - **세션 시작 때 읽지 않는다.** 탐색할 때 작업이 닿는 화면·모듈 태그로 찾는다: `grep "#HOME-01" docs/handoff/DECISIONS.md` → 유효한 줄(대체됨 아님)의 본문만 연다
   - **번호는 `python .claude/skills/vibe-flow/scripts/handoff.py next-id D`로 받는다**(전 브랜치 + 원격 예약). 손으로 「마지막 번호 + 1」을 쓰지 않는다 — 다른 브랜치의 같은 번호가 union으로 조용히 두 줄이 된다
   - 본문은 안내된 `DECISIONS-YYYY-MM*.md` 끝에 `## D-nnn 제목` 블록, **같은 커밋에** `DECISIONS.md` 끝에 INDEX 한 줄(`- D-nnn · 제목 · 날짜 · @결정자 · 유효 · #태그 → [본문](…)`, 뒤집으면 `대체 D-kkk`). INDEX 줄은 추가만 하고 고치지 않는다
4. **같은 커밋** — 핸드오프 기록은 그 작업의 코드·문서 변경과 **같은 커밋**에 넣는다. vibe-flow 골 작업은 QA 결과를 보고 기록하므로, QA 뒤 기록을 **마지막 작업 커밋에 합친다**(`git commit --amend`, 이미 push했으면 자기 브랜치에 `--force-with-lease`). 기록만 담은 커밋은 만들지 않는다.
5. **충돌 처리** — `INDEX.md`·`REMAINING.md`·`LESSONS.md`·`DECISIONS.md`·WORKLOG 토픽 파일과 인덱스 모드의 INDEX·본문 파일(`DECISIONS-YYYY-MM*.md`·`LESSONS-body*.md`·`goals/INDEX.md`·`REMAINING-done-*.md`·`WORKLOG/INDEX-*.md`)은 `.gitattributes`의 `merge=union`으로 양쪽 줄이 자동으로 남는다. union이 남긴 같은 번호·같은 줄 두 개는 `handoff.py lint`가 잡는다. **PR을 머지하기 전에 로컬에서 `git pull --rebase origin main`**으로 브랜치를 최신화한다(union은 로컬 병합에서 동작). 남은 충돌은 양쪽 줄을 모두 남기고 날짜순 정렬. 다른 참여자의 항목 내용은 지우거나 고치지 않는다(완료 체크·착수 표시 변경만 허용).
6. **다른 참여자 작업 인지** — 세션 시작 보고에 「마지막으로 내가 기록한 이후 다른 참여자들이 남긴 WORKLOG·REMAINING 변경」을 작성자별로 한 줄씩 요약한다. 확인은 `git log --since=<내 마지막 기록 날짜> -- docs/handoff/`로 한다.
7. **AI 종류 무관** — Claude가 아닌 에이전트도 같은 형식을 쓴다(`AGENTS.md` → `CLAUDE.md`).
9. **vibe-flow 연결** — 코드·화면 작업 흐름은 프로젝트 스킬 `vibe-flow`(`.claude/skills/vibe-flow/`)가 맡고, 이 스킬은 기록을 맡는다. 둘이 만나는 형식(골 ID·착수 표시·노스스타·골 위치)은 `.claude/skills/vibe-flow/docs/structure.md`가 원천이다.
   - 세션 시작: `git pull` 다음 `python .claude/skills/vibe-flow/scripts/handoff.py precheck` — 읽을 순서, 다른 참여자 변경, 7일 이내 착수 표시, 결정 대기, 열린 골 후보를 한 번에 보여 준다. 그 뒤 아래 순서대로 읽는다
   - 노스스타 `docs/handoff/NORTHSTAR.md`, 골 `docs/handoff/goals/G-###-*.md`. 노스스타·골 파일은 작업 브랜치에서 고치지 않는다
   - 태스크 완료(골 작업): QA 뒤 WORKLOG 토픽 항목 + INDEX 한 줄 + REMAINING `[x]`를 마지막 작업 커밋에 합치고(amend) `python .claude/skills/vibe-flow/scripts/handoff.py check-record`로 확인한다

---

## 모드 판단 (강제)

```
docs/handoff/INDEX.md 첫머리 <!-- vibe-index: ENTRY -->  → 인덱스 모드 (토픽 디렉토리 모드 + 문서별 INDEX)
docs/handoff/WORKLOG/ 존재  → 토픽 디렉토리 모드
       없음     → docs/handoff/WORKLOG.md 존재 → 단일 파일 모드
                                  없음 → 새 프로젝트 (초기화)
```

## 세션 시작 시

### 기존 문서가 있는 경우

**사전체크**: `git pull` → `python .claude/skills/vibe-flow/scripts/handoff.py precheck`

**인덱스 모드 — 읽을 파일 = precheck가 출력한 「읽을 순서」만** (진입 INDEX → REMAINING → goals/INDEX → WORKLOG/INDEX → LESSONS → NORTHSTAR → PLAN, 모두 INDEX). precheck가 총 자 수를 함께 보여 준다(목표 12,000자, 넘으면 `handoff.py archive`). **본문 파일(`*-body.md`·`DECISIONS-YYYY-MM*.md`·토픽 파일)은 열지 않는다** — 골이 정해진 뒤 INDEX에서 고른 것만.

**토픽 디렉토리 모드 — 읽을 파일 (강제 순서)**:
1. `docs/handoff/PLAN.md`
2. `docs/handoff/REMAINING.md`
3. `docs/handoff/DECISIONS.md` (최근 결정 위주)
4. `docs/handoff/LESSONS.md` (있으면)
5. **WORKLOG**:
   - 디렉토리 모드: `WORKLOG/INDEX.md`만. **토픽 파일 본문 X**
   - 단일 파일 모드: `WORKLOG.md` 그대로

**보고**: 현재 상태 / 최신 1~3개 / 남은 업무 / 교훈(2회+ 반복은 **볼드**).

마무리: "이전 세션에서 이어서 작업합니다. 남은 업무를 확인해주세요."

### 새 프로젝트
1. `docs/handoff/` 생성
2. 사용자 질문으로 기획 파악
3. [templates.md](templates.md) 참고하여 PLAN/WORKLOG/REMAINING/LESSONS 초기화

---

## 태스크 완료 시 (생략 금지)

태스크 완료 즉시, **다음 작업 전**:

1. **WORKLOG 기록**
   - 단일 파일: `WORKLOG.md`에 날짜 항목 추가
   - 디렉토리: 적절한 토픽 파일에 항목 추가 (모호하면 `misc.md`) + HTML 주석 frontmatter + `{#anchor}` + **`WORKLOG/INDEX.md`에 한 줄 동시 추가** (누락 시 stale)
2. `REMAINING.md` 갱신 (앵커 링크 부착 권장)
3. PLAN 변경 있었으면 변경이력 테이블 + WORKLOG에도 기록
4. 실수 있었으면 LESSONS 기록/횟수 증가
5. **인덱스 모드**: 본문에 새로 쓴 항목마다 그 INDEX 한 줄을 **같은 커밋**에 — 결정 `DECISIONS.md`, 교훈 `LESSONS.md`(번호 `next-id L`, 본문 줄에 `L-nnn`), PLAN·NORTHSTAR 새 `##` 섹션은 그 INDEX, 새 골은 `goals/INDEX.md`(judge가 붙인다)

**자기 검증 (필수, 다음 작업 전)**:
- 디렉토리 모드: `grep "$(date +%Y-%m-%d)" docs/handoff/WORKLOG/INDEX.md` → 결과 없으면 **INDEX 동시 갱신 누락**. 즉시 보강
- REMAINING 갱신 시: 방금 완료한 항목이 `[ ]` 상태로 잔존하지 않는지 grep으로 재확인
- PLAN 변경 시: 변경이력 테이블 마지막 행 날짜가 오늘인지 확인
- 커밋 후(골 작업): `python .claude/skills/vibe-flow/scripts/handoff.py check-record` — 작업과 기록이 같은 커밋에 있는지, 작성자·골 ID 표기, 착수 `[~]` 잔존 여부, (인덱스 모드) 새 본문 항목의 INDEX 줄을 검사
- 인덱스 모드: `python .claude/skills/vibe-flow/scripts/handoff.py lint` — INDEX↔본문 불일치·끊긴 링크·union 중복은 오류(빠진 INDEX 줄을 붙일 형태로 출력), 임계값·완료 잔존은 경고 → 다음 작업 커밋에서 기록을 쓰기 전에 `handoff.py archive`(이동만 담은 커밋은 만들지 않는다)

---

## 문서별 규칙

### PLAN.md
양식: [templates.md](templates.md). 기획 변경 시 변경이력 테이블 + WORKLOG에도 기록 + 양방향 앵커 유지.

### WORKLOG (단일 파일)
날짜별 역순. 양식: [templates.md](templates.md).

### WORKLOG (토픽 디렉토리)

**INDEX.md** — 한 줄/항목, 시간순 그룹화. 옛 분기는 `<details>` 또는 `INDEX-archive/<quarter>.md`:
```markdown
## YYYY-MM
- YYYY-MM-DD [topic] 한 줄 요약 → [topic.md#anchor](topic.md#anchor)
```

**토픽 파일** — frontmatter는 **HTML 주석 강제** (마크다운 비표시 + grep 친화. YAML은 렌더링·grep 동시 깨짐):
```markdown
<!--
date: YYYY-MM-DD
topic: <topic-slug>
tags: [tag1, tag2]
files: [path1]
session: N
v6.5-public (2026-10-17): 공개용 일반화 — 특정 레포 전용 표현 제거
-->

## YYYY-MM-DD 한 줄 제목 #tag1 #tag2 {#YYYY-MM-DD-slug}

### 완료한 태스크
- [x] 태스크명
  - 상세 / 파일 / 비고
```

**필드 화이트리스트** (위 5개만): `date`/`topic`/`tags`/`files`/`session`. 임의 필드(`plan_refs`, `related` 등) **추가 금지** — grep·자동화·일관성 깨짐.

**최소 강제**: `date` + `topic` + 한 줄 요약 + 앵커. 나머지(tags/files/session) 누락 허용.

### REMAINING.md
미완료 태스크. 양식: [templates.md](templates.md). 항목에 WORKLOG 앵커 링크 부착 권장. 인덱스 모드에서는 활성 항목만 두는 INDEX다 — `[x]` 완료 줄은 `handoff.py archive`가 하위 줄째 `REMAINING-done-YYYY-MM.md`로 옮긴다(원래 소제목과 함께, 원문 그대로).

### LESSONS.md
1회는 한 줄, 2회+는 상세+횟수. 비슷한 항목 합침. **항목 삭제 금지**. 인덱스 모드: 본문은 `LESSONS-body.md`, 새 교훈은 본문 끝에 `- … (L-nnn, @작성자, 날짜)` + `LESSONS.md` INDEX 한 줄. 합치기·반영 표시는 INDEX 줄에서(`· 반영: <문서>`) — 본문 텍스트는 고치지 않는다.

### PLAN.md · NORTHSTAR.md (인덱스 모드)
INDEX는 본문(`PLAN-body.md`·`NORTHSTAR-body.md`)의 `##` 섹션 목차다. 기획 변경은 본문의 변경이력 테이블에. 노스스타 상태 주석(`status: draft|confirmed`)은 본문 첫머리에 그대로 있다.

---

## AI 참조 룰 (긴 md)

긴 md 참조 시 다음 순서 강제:

0. 인덱스 모드면 모든 문서에 같은 규칙: **진입 `INDEX.md` → 문서별 INDEX → 본문**. 결정은 태그 grep, 교훈·기획·노스스타는 INDEX 줄에서 고른 섹션만
1. **`WORKLOG/INDEX.md`만 먼저 읽음**
2. INDEX에서 토픽·태그·날짜 찾기 → 해당 토픽 파일 frontmatter + `##` 헤더만 스캔
3. 본문은 **정말 필요할 때만**
4. **본문 통째 로드 금지**

PLAN ↔ WORKLOG ↔ REMAINING은 앵커 링크로 점프. 다른 파일 통째 읽기 금지. 태그 검색은 grep: `#auth`, `#migration` 등.

> **Letter = Spirit:** "이번엔 짧으니" / "한 번만" / "파일 다 봐도 되겠지" 같은 합리화는 spirit 위반이다.

---

## Red Flags — 보이면 STOP (사고·합리화 차단)

> 이 섹션은 **행동 직전에 떠오르는 생각·말**을 잡는다. "Common Mistakes"(아래)는 사후 결과·증상.

| 합리화 | 진실 |
|---|---|
| "이 토픽 파일 짧으니 통째 로드" | 길이 무관, 룰 항상 적용. INDEX부터 |
| "INDEX 갱신은 다음 작업에서" | 다음 = 영영. 동시 갱신 강제 |
| "토픽 분류 모호하니 INDEX에만 적자" | 토픽 파일 강제. 모호하면 `misc.md` |
| "frontmatter는 YAML이 더 익숙하니까" | HTML 주석 강제. YAML은 렌더링·grep 동시 깨짐 |
| "이 케이스엔 plan_refs 같은 필드 추가가 명확함" | 화이트리스트 5개 외 금지. 자동화·일관성 깨짐 |
| "이번 실수는 사소해서 LESSONS 안 적어도 됨" | 사소함 판단은 미래의 자기 자신이 한다 |
| "PLAN 변경이력 한 번 정도 안 적어도" | 추적 끊김. 무조건 적기 |
| "spirit은 따르니 letter는 가벼워도" | letter 위반 = spirit 위반 |
| "착수 표시는 좀 살펴본 뒤에" | 착수 push가 먼저다. 그 전에 한 작업은 다른 세션과 겹칠 수 있다 |
| "기록은 다음 커밋에 따로" | 작업과 같은 커밋이다. `check-record`가 기록만 담은 커밋을 거부한다 |
| "DECISIONS를 한 번 다 읽어 두면 편하다" | 인덱스 모드에서는 태그 grep → 유효 줄의 본문만. 통째 로드는 맥락만 잡아먹는다 |
| "마지막 결정이 D-106이니 D-107" | `next-id D`. 다른 브랜치가 이미 썼을 수 있다 |
| "INDEX 줄은 나중에 한꺼번에" | 본문과 같은 커밋. `check-record`·`lint`가 잡는다 |
| "옛 결정 문구를 이참에 다듬자" | 본문은 이동만. 정리는 INDEX 상태 표시로 |

**모두 같은 결론: 룰 그대로 적용. 예외 없음.**

---

## Common Mistakes (사후 결과·증상)

> 이 섹션은 **이미 발생한 결과**를 잡는다. "Red Flags"(위)는 행동 직전의 합리화. 같은 룰이라도 잡히는 시점이 다르다.

- REMAINING 완료 항목 제거 잊음 → 다음 세션 중복 작업
- WORKLOG에 수정 파일 목록 누락 → 변경 범위 파악 불가
- **디렉토리 모드에서 INDEX 동시 갱신 누락** → INDEX stale, 검색 실패
- PLAN 변경 시 변경이력 미갱신 → 사유 추적 불가
- 태스크 완료 후 기록 미루고 다음 작업 진행 → 인수인계 실패
- 실수 발생 시 LESSONS 기록 안 함 → 같은 실수 반복
- AI가 INDEX 무시하고 토픽 파일 통째 로드 → 검색 효율 무의미
- frontmatter를 YAML로 작성 → 마크다운 렌더링 깨짐

---

## 부록

- **좋은 엔트리 vs 나쁜 엔트리 대비**: [examples.md](examples.md) — Before/After 4쌍 (토픽 파일·REMAINING·PLAN 변경이력·자기 검증)
- **회귀 검증 baseline**: [tests/tdd-record.md](tests/tdd-record.md) — 세션 6 RED 4/7 → GREEN 7/7 + REFACTOR 1건. 다음 룰 변경 시 동일 시나리오 재실행
- **빈 양식 템플릿**: [templates.md](templates.md) — PLAN/WORKLOG/REMAINING/LESSONS 초기화용
- **v0 → 토픽 디렉토리 모드 전환**: [migration.md](migration.md) — 자동 트리거 없음, 사용자 명시 명령으로만

---

<!-- Changelog
v6 (2026-04-25): writing-skills 평가 기반 재설계 — 토픽 디렉토리 모드, INDEX 강제, frontmatter 화이트리스트
v6.1 (2026-04-25): writing-skills 재평가 반영 — SKILL.md 리네임, 제목 버전 라벨 제거, 영어 트리거 추가, 섹션 번호 제거, 자기 검증 명령 추가
v6.2 (2026-04-25): P3 분리 — examples.md (Before/After 4쌍) + tests/tdd-record.md (RED-GREEN baseline) 신설, 부록 섹션으로 통합
v6.3 (2026-04-25): Common Mistakes / Red Flags 의도 부제 추가 — "사고·합리화 차단" vs "사후 결과·증상" 구분 명시
v6.4 (2026-10-17, @jinjan21): vibe-flow 연결 — 골 ID 표기(WORKLOG·INDEX·착수·완료), handoff.py precheck/claim/check-record, 토픽 파일 union, Red Flag 2개
v6.5 (2026-10-17, @jinjan21): 인덱스 모드 — 진입 INDEX → 문서별 INDEX → 본문, DECISIONS 세션 시작 제외·태그 검색, next-id 번호 발급, INDEX 같은 커밋(check-record), lint·archive, Red Flag 4개
v6.5-public (2026-10-17): 공개용 일반화 — 특정 레포 전용 표현 제거
-->