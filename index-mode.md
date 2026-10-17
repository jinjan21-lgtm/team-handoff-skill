# 인덱스 모드 (장문·대형 프로젝트)

판별: `docs/handoff/INDEX.md` 첫머리에 `<!-- vibe-index: ENTRY -->`. 형식·임계값의 원천은 vibe-flow `docs/structure.md` 「인덱스 계층」이고, 명령은 vibe-flow 스크립트(`.claude/skills/vibe-flow/scripts/handoff.py`)다 — vibe-flow 없이는 쓸 수 없다.

## 구조

진입 인덱스 → 문서별 INDEX → 본문. 옛 경로(`PLAN.md`·`DECISIONS.md`·`LESSONS.md`·`NORTHSTAR.md`)가 INDEX가 되고, 본문은 같은 폴더로 옮겨져 있다(텍스트는 그대로).

```
docs/handoff/
├── INDEX.md                                    진입 인덱스
├── {PLAN, DECISIONS, LESSONS, NORTHSTAR}.md    문서별 INDEX
├── PLAN-body.md · DECISIONS-YYYY-MM*.md · LESSONS-body.md · NORTHSTAR-body.md
├── REMAINING.md (활성만) · REMAINING-done-YYYY-MM.md
├── goals/{INDEX.md, INDEX-done.md, G-###-*.md}
└── WORKLOG/{INDEX.md (최근 30줄), INDEX-YYYY-MM.md, <topic>.md}
```

전환은 사용자 명시 명령으로만: `handoff.py migrate` → `verify-migration <스냅숏>`(무손실 검증). 세션 시작 읽기량이 목표(12,000자)를 넘을 때만 — 작은 레포는 오히려 는다.

## 세션 시작

`handoff.py precheck`가 출력한 「읽을 순서」만 읽는다(진입 INDEX → REMAINING → goals/INDEX → WORKLOG/INDEX → LESSONS → NORTHSTAR → PLAN, 모두 INDEX). 총 자 수가 함께 나온다. **본문 파일(`*-body.md`·`DECISIONS-YYYY-MM*.md`·토픽 파일)은 열지 않는다** — 골이 정해진 뒤 INDEX에서 고른 것만.

## 결정

- **세션 시작 때 읽지 않는다.** 탐색할 때 작업이 닿는 화면·모듈 태그로: `grep "#HOME-01" docs/handoff/DECISIONS.md` → 유효한 줄(대체됨 아님)의 본문만
- 번호: `handoff.py next-id D` — 붙일 본문 파일(이번 달 `DECISIONS-YYYY-MM.md`, 넘치면 `-2`)도 안내한다
- 본문 파일 끝에 `## D-nnn 제목` 블록, **같은 커밋에** `DECISIONS.md` 끝에 INDEX 한 줄:
  `- D-nnn · 제목 · 날짜 · @결정자 · 유효 · #태그 → [본문](DECISIONS-YYYY-MM.md#앵커)` — 뒤집으면 `대체 D-kkk`. INDEX 줄은 추가만 하고 고치지 않는다

## 기록할 때 INDEX도 같은 커밋에

본문에 새로 쓴 항목마다 그 INDEX 한 줄:

| 본문 | INDEX |
|---|---|
| 결정 `## D-nnn` | `DECISIONS.md` |
| 교훈 — `LESSONS-body.md` 끝에 `- … (L-nnn, @작성자, 날짜)`, 번호는 `next-id L` | `LESSONS.md` 한 줄 |
| PLAN·NORTHSTAR 새 `##` 섹션 | 그 INDEX |
| 새 골 | `goals/INDEX.md`(judge가 붙인다) |

LESSONS 합치기·반영 표시는 INDEX 줄에서(`· 반영: <문서>`) — 본문 텍스트는 고치지 않는다. PLAN·NORTHSTAR INDEX는 본문의 `##` 섹션 목차, 노스스타 상태 주석(`status: draft|confirmed`)은 본문 첫머리에 있다.

## 점검·보관

- 커밋 뒤 `handoff.py check-record`(같은 커밋·INDEX 줄), `handoff.py lint`(INDEX↔본문 불일치·끊긴 링크·union 중복은 오류 — 빠진 INDEX 줄을 붙일 형태로 출력, 임계값·완료 잔존은 경고)
- 경고가 나오면 **다음 작업 커밋에서 기록을 쓰기 전에** `handoff.py archive` — REMAINING `[x]`는 하위 줄째 `REMAINING-done-YYYY-MM.md`로(원래 소제목과 함께, 원문 그대로), 지난 WORKLOG INDEX 줄은 `WORKLOG/INDEX-YYYY-MM.md`로. 이동만 담은 커밋은 만들지 않는다
- 크기는 직접 재지 않는다(`wc -c`는 바이트, 임계값은 문자 수) — lint·`archive --dry` 출력을 따른다
