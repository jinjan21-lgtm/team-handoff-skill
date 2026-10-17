# 다인(N명) 협업 규칙 — 상세

SKILL.md 「다인 협업 핵심」의 상세. 이 스킬은 레포 안(`.claude/skills/team-handoff/`)에 두는 **팀 공용 사본**이다. N명의 참여자가 각자의 AI 세션으로 같은 `docs/handoff/` 1세트를 쓴다. 영역별 담당·책임을 나누지 않는다 — 누구든 어떤 항목이든 집을 수 있다. Claude가 아닌 에이전트도 같은 형식을 쓴다(`AGENTS.md` → `CLAUDE.md`).

## 작성자 표기 (필수)

작성자 = `git config user.name`.

- WORKLOG 항목 제목: `## YYYY-MM-DD G-### 한 줄 제목 @작성자 #tag {#YYYY-MM-DD-slug-작성자}` — **앵커 끝에 항상 `-작성자`**(N명이 같은 날 같은 slug를 써도 충돌하지 않게). 골(vibe-flow) 작업이 아니면 `G-###` 자리를 비운다
- INDEX 한 줄: `- YYYY-MM-DD [topic] @작성자 G-### 한 줄 요약 → [topic.md#anchor](topic.md#anchor)`
- LESSONS 항목 끝: `(@작성자, 날짜)`

## REMAINING — 담당 없이, 착수 표시로

- 할 일: `- [ ] 할 일 → 링크` (담당 표기 없음)
- **착수 표시(claim):** 집을 때 `- [~] G-### 할 일 (착수 @작성자 YYYY-MM-DD) → <브랜치>`로 바꾸고 **바로 커밋·push**한다. push가 확인된 뒤에만 탐색을 시작한다. vibe-flow가 있으면 `python .claude/skills/vibe-flow/scripts/handoff.py claim G-### "<할 일>" <브랜치>`가 중복 검사 → 브랜치 → 착수 줄 커밋 → push를 한 번에 한다
- 착수 표시가 **7일** 넘게 그대로면 누구든 다시 집을 수 있다 — 착수 줄을 자기 것으로 교체하고 WORKLOG에 사유 한 줄
- 완료: `- [x] G-### 할 일 (@완료자 YYYY-MM-DD)`
- 결정이 필요한 항목: `- [ ] 결정: …` — 참여자는 동등하며 누구나(AI 포함) 결정할 수 있다. 결정하면 아래 「결정」대로 기록하고 이 항목을 `[x]`로

## 결정 (DECISIONS)

- 결정자 = 세션 사용자의 git user.name(AI가 정했어도), 방식 = 사용자 / AI 위임, **근거 1~2줄 필수**
- 기록은 삭제·수정 금지. 뒤집을 때는 새 D-nnn을 쓰고 이전 항목은 상태만 「대체됨 → D-nnn」으로
- 형식:
  ```markdown
  ## D-nnn 제목
  - 날짜 YYYY-MM-DD · 결정자 @작성자 · 방식 사용자|AI 위임 · 상태 유효
  - 결정: …
  - 근거: …(1~2줄)
  - 영향: …(화면·모듈 ID — 나중에 태그로 찾는다)
  ```
- **번호는 손으로 「마지막 번호 + 1」을 쓰지 않는다** — 다른 브랜치가 같은 번호를 쓰면 union 병합이 두 줄을 조용히 남긴다
  - vibe-flow가 있으면: `python .claude/skills/vibe-flow/scripts/handoff.py next-id D`(전 브랜치 + 원격 예약)
  - 없으면: `git fetch --all` → `git grep -ohE "D-[0-9]{3,}" $(git for-each-ref --format='%(refname)' refs/remotes refs/heads) -- 'docs/handoff/DECISIONS*'`의 최댓값 + 1. 머지 전 `git pull --rebase` 뒤 같은 번호가 두 번 나오는지 다시 grep(아직 push 안 된 복제본 번호는 못 막는다 — 겹치면 뒤에 온 쪽이 새 번호로)
- 인덱스 모드의 결정 위치·INDEX 줄은 [index-mode.md](index-mode.md)

## 같은 커밋

핸드오프 기록은 그 작업의 코드·문서 변경과 **같은 커밋**에 넣는다. 기록만 담은 커밋은 만들지 않는다. 예외는 착수 표시 커밋 하나다(작업 전에 push해야 중복을 막으므로). 7일 넘은 착수를 가져올 때의 착수 줄 교체도 이 착수 커밋에 넣고, 사유 한 줄은 그 일의 WORKLOG 항목에 쓴다. vibe-flow 골 작업은 QA 결과를 보고 기록하므로 QA 뒤 기록을 **마지막 작업 커밋에 합친다**(`git commit --amend`, 이미 push했으면 자기 브랜치에 `--force-with-lease`).

## 충돌 처리

- `REMAINING.md`·`LESSONS.md`·`DECISIONS.md`·`WORKLOG/INDEX.md`·WORKLOG 토픽 파일(인덱스 모드면 INDEX·본문 파일까지)은 `.gitattributes`의 `merge=union` — 양쪽 줄이 자동으로 남는다
- **PR을 머지하기 전에 로컬에서 `git pull --rebase origin main`**(union은 로컬 병합에서 동작)
- 남은 충돌은 양쪽 줄을 모두 남기고 날짜순. 다른 참여자의 항목 내용은 지우거나 고치지 않는다(완료 체크·착수 표시 변경만)
- union이 남긴 같은 번호·같은 줄 두 개는 vibe-flow `handoff.py lint`가 잡는다(없으면 grep)

## 다른 참여자 작업 인지

세션 시작 보고에 「내가 마지막으로 기록한 이후 다른 참여자가 남긴 WORKLOG·REMAINING 변경」을 작성자별로 한 줄씩 요약한다. vibe-flow가 있으면 `handoff.py precheck`가 보여 준다. 없으면 `git log --since=<내 마지막 기록 시각> --format='%an %s' -- docs/handoff/` — 날짜만 주면 git은 '그날 지금 시각'으로 해석하므로 시각까지 준다.

## vibe-flow 연결

작업 흐름(골·선점·설계 확인·리뷰·QA)은 vibe-flow가, 기록은 이 스킬이 맡는다. 둘이 만나는 형식의 원천은 `.claude/skills/vibe-flow/docs/structure.md`.

- 세션 시작: `git pull` → `python .claude/skills/vibe-flow/scripts/handoff.py precheck` → 그 출력의 「읽을 순서」대로
- 노스스타 `docs/handoff/NORTHSTAR.md`, 골 `docs/handoff/goals/G-###-*.md` — 작업 브랜치에서 고치지 않는다
- 골 작업 완료: QA 뒤 WORKLOG 토픽 항목 + INDEX 한 줄 + REMAINING `[x]`를 마지막 작업 커밋에 합치고 `handoff.py check-record`
