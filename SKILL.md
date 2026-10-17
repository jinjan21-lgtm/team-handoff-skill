---
name: team-handoff
description: Use when the repo has a docs/handoff/ folder shared by several people or AI sessions — at session start, when resuming prior context (PLAN/WORKLOG/REMAINING/DECISIONS), after finishing a task that must be logged, or when the user mentions handoff/continue/resume/where-did-we-leave-off/인수인계/이어서/어디까지/지난 세션/오늘 한 일 기록. If there is no docs/handoff/, the personal session-handoff applies instead.
user-invocable: true
---

# 팀 인수인계 (team-handoff)

## Overview

N명이 각자의 AI 세션으로 같은 `docs/handoff/`를 쓴다. 평가 기준은 **맥락 회수 비용** — 세션을 껐다 켜도 INDEX만 보고 바로 이어 간다. 그래서 기록은 **작업과 같은 커밋에, 정해진 형식으로, INDEX와 함께** 남긴다. 룰의 letter를 어기면 spirit도 어긴 것이다.

사용하지 않을 때: 단발성 질문, 읽기 전용 조회, 사용자가 "기록하지 마"라고 할 때.

## 모드 판단

```
docs/handoff/INDEX.md 첫머리 <!-- vibe-index: ENTRY --> → 인덱스 모드   (규칙: index-mode.md)
docs/handoff/WORKLOG/ 있음                               → 토픽 디렉토리 모드 (여러 사람이면 이것 이상)
docs/handoff/WORKLOG.md만 있음                           → 단일 파일 모드
없음                                                      → 새 프로젝트: templates.md로 초기화
```

## 세션 시작

1. `git pull` (vibe-flow가 있으면 이어서 `python .claude/skills/vibe-flow/scripts/handoff.py precheck`)
2. 읽기 — **INDEX만, 본문 통째 로드 금지**
   - 인덱스 모드: precheck의 「읽을 순서」만 → [index-mode.md](index-mode.md)
   - 토픽 디렉토리: PLAN → REMAINING → DECISIONS(최근 결정만) → LESSONS → `WORKLOG/INDEX.md`(토픽 파일 본문 X)
   - 단일 파일: PLAN → REMAINING → LESSONS → WORKLOG
3. 보고: 현재 상태 / 최신 1~3개 / 남은 업무 / 교훈(2회+는 **굵게**) / 내 마지막 기록 이후 다른 참여자 변경(작성자별 한 줄). 마무리: "이전 세션에서 이어서 작업합니다. 남은 업무를 확인해주세요."

## 태스크 완료 (생략 금지, 다음 작업 전)

1. **WORKLOG** — 토픽 파일(모호하면 `misc.md`)에 아래 양식으로 항목 + **`WORKLOG/INDEX.md`에 한 줄 동시 추가**
2. **REMAINING** — 내 `[~]`를 `[x] (@나 날짜)`로, 새 할 일은 `[ ]` + WORKLOG 앵커
3. **PLAN**이 바뀌었으면 변경이력 표에 한 행 + WORKLOG에도
4. **실수**가 있었으면 LESSONS에 한 줄(2회+는 상세 + 횟수, 삭제 금지)
5. **결정**을 기록하면 → [team-rules.md](team-rules.md) 「결정」의 형식과 번호 정하는 법(손으로 「마지막 + 1」 금지)
6. 전부 **그 작업과 같은 커밋**에. 기록만 담은 커밋은 만들지 않는다(예외: 일을 집을 때의 착수 표시 커밋 — 작업 전에 push해야 하므로)

**WORKLOG 항목 양식** (frontmatter는 HTML 주석, 필드는 `date`/`topic`/`tags`/`files`/`session` 5개만):

```markdown
<!--
date: YYYY-MM-DD
topic: <topic-slug>
tags: [tag1]
files: [path1]
-->

## YYYY-MM-DD 한 줄 제목 @작성자 #tag1 {#YYYY-MM-DD-slug-작성자}

### 완료한 태스크
- [x] 태스크명
  - 상세 / 파일 / 비고
```

INDEX 한 줄: `- YYYY-MM-DD [topic] @작성자 한 줄 요약 → [topic.md#앵커](topic.md#앵커)`. 골(vibe-flow) 작업이면 제목·INDEX 줄에 `G-###`.

**자기 검증:** `grep "$(date +%Y-%m-%d)" docs/handoff/WORKLOG/INDEX.md`(없으면 INDEX 누락), 완료 항목이 `[ ]`로 남지 않았는지 grep, PLAN 변경이력 마지막 행이 오늘인지. vibe-flow가 있으면 `handoff.py check-record`(인덱스 모드는 `lint`도).

## 다인 협업 핵심 (상세: [team-rules.md](team-rules.md))

- 작성자 = `git config user.name` — `@작성자`, 앵커 끝 `-작성자`, LESSONS 끝 `(@작성자, 날짜)`
- 일을 집기 전에 REMAINING에 `- [~] 할 일 (착수 @나 날짜) → <브랜치>`를 **커밋·push한 뒤에** 탐색. 7일 넘은 착수는 다시 집을 수 있다
- 머지 전 `git pull --rebase origin main`. 핸드오프 문서는 `merge=union` — 남은 충돌은 양쪽 줄 유지
- 다른 참여자 항목은 내용을 고치지 않는다(완료 체크·착수 표시만)
- 누구나(AI 포함) 결정할 수 있다. 결정자·방식·근거 1~2줄 필수

## Red Flags — 이 생각이 들면 멈춘다

| 합리화 | 진실 |
|---|---|
| "짧은 파일이니 통째 로드" / "DECISIONS 한 번 다 읽어 두자" | 길이 무관. INDEX부터, 결정은 태그 grep |
| "INDEX 갱신은 다음 작업에서" / "INDEX 줄은 나중에 한꺼번에" | 다음 = 영영. 같은 커밋 |
| "기록은 다음 커밋에 따로" | 작업과 같은 커밋 |
| "토픽 분류가 모호하니 INDEX에만" | 토픽 파일 강제, 모호하면 `misc.md` |
| "frontmatter는 YAML이 익숙하다" / "plan_refs 같은 필드가 필요하다" | HTML 주석, 필드 5개만 |
| "착수 표시는 좀 살펴본 뒤에" | 착수 push가 먼저 |
| "마지막 결정이 D-106이니 D-107" | team-rules.md 「결정」의 번호 정하는 법 |
| "사소한 실수라 LESSONS 안 적어도" / "PLAN 변경이력 한 번쯤" | 무조건 적는다 |
| "옛 기록 문구를 이참에 다듬자" | 다른 사람·과거 기록은 고치지 않는다 |
| "spirit은 지켰으니 letter는 가볍게" | letter 위반 = spirit 위반 |

## 부록

[team-rules.md](team-rules.md) 다인 협업 상세(착수·결정·충돌·vibe-flow 연결) · [doc-rules.md](doc-rules.md) 문서별 규칙·AI 참조 룰·흔한 실수 · [index-mode.md](index-mode.md) 인덱스 모드 · [templates.md](templates.md) 빈 양식 · [examples.md](examples.md) 좋은/나쁜 기록 · [migration.md](migration.md) 단일 → 토픽 전환 · [tests/tdd-record.md](tests/tdd-record.md) 검증 기록

<!-- Changelog
v6.4 (2026-10-17): vibe-flow 연결 — 골 ID 표기, handoff.py precheck/claim/check-record, 토픽 파일 union
v6.5 (2026-10-17): 인덱스 모드
v7 (2026-10-17): 구조 개선 — 핵심만 SKILL.md(약 2,200 → 약 700단어), 상세는 team-rules·doc-rules·index-mode로. 공개판 frontmatter 예시에 잘못 들어간 changelog 줄 제거
-->
