# team-handoff

여러 사람이 **각자의 AI 세션**으로 같은 레포에서 일할 때, 세션을 껐다 켜도 맥락을 바로 되살리게 하는 Claude Code 인수인계 스킬입니다. 레포의 `docs/handoff/` 한 세트(PLAN·REMAINING·WORKLOG·LESSONS·DECISIONS)를 모든 참여자와 에이전트가 같은 형식으로 씁니다. 영역별 담당을 나누지 않는 팀을 전제로 합니다.

```
/team-handoff        세션 시작(읽기·보고) 또는 태스크 완료(기록)
```

## 원칙

- **맥락 회수 비용이 평가 기준.** 긴 문서는 INDEX → 앵커 → 본문 순서로만 읽고, 본문을 통째로 읽지 않습니다.
- **기록은 작업과 같은 커밋.** 기록만 담은 커밋은 만들지 않습니다.
- **작성자 표기 필수.** `@git user.name`, WORKLOG 앵커는 `-작성자`로 끝나 같은 날 같은 제목이어도 충돌하지 않습니다.
- **착수 표시(claim)를 먼저 push.** `- [~] 할 일 (착수 @나 날짜)` — 다른 세션의 중복 작업을 막습니다. 7일 넘은 착수는 누구나 다시 집을 수 있습니다.
- **누구나 결정, 근거 1~2줄.** 결정은 `D-nnn`으로 남기고, 뒤집을 때는 새 결정으로(이전 기록은 지우지 않음).
- **병합은 union.** 핸드오프 문서는 `.gitattributes`의 `merge=union`이라 여러 사람이 같은 파일 끝에 줄을 붙여도 충돌하지 않습니다.

## 모드

| 모드 | 언제 | 구조 |
|---|---|---|
| 단일 파일 | 작은 프로젝트 | `docs/handoff/{PLAN, WORKLOG, REMAINING, LESSONS}.md` |
| 토픽 디렉토리 | 여러 사람 (권장) | `WORKLOG/INDEX.md` + `WORKLOG/<topic>.md` |
| 인덱스 | 기록이 커진 장문·대형 프로젝트 | 진입 `INDEX.md` → 문서별 INDEX → 본문(옮기기만, 텍스트 그대로). 세션 시작 읽기 목표 12,000자, 결정은 태그로 찾음 |

## 구성

| 파일 | 내용 |
|---|---|
| `SKILL.md` | 모드 판단, 세션 시작·태스크 완료 절차, 다인 협업 규칙, 문서별 규칙, Red Flags |
| `templates.md` | PLAN·WORKLOG·REMAINING·LESSONS 빈 양식 |
| `examples.md` | 좋은 기록 vs 나쁜 기록 4쌍 |
| `migration.md` | 단일 파일 → 토픽 디렉토리 전환 |
| `tests/tdd-record.md` | writing-skills RED → GREEN 기록 |

## 설치

팀 레포 안에 사본을 두어 모든 참여자가 같은 버전을 씁니다.

```bash
git clone https://github.com/jinjan21-lgtm/team-handoff-skill .claude/skills/team-handoff
rm -rf .claude/skills/team-handoff/.git
```

`.gitattributes`에 union 병합을 넣습니다.

```
docs/handoff/REMAINING.md merge=union
docs/handoff/LESSONS.md merge=union
docs/handoff/DECISIONS.md merge=union
docs/handoff/WORKLOG/*.md merge=union
```

`CLAUDE.md`(다른 에이전트는 `AGENTS.md`)에 "세션 시작과 작업 완료 때 team-handoff를 실행한다"를 적습니다.

## vibe-flow와 함께 쓰기

스킬 안의 `handoff.py` 명령(사전체크 `precheck`, 선점 `claim`, 같은 커밋 검사 `check-record`, 인덱스 이전 `migrate`, 점검 `lint`, 보관 `archive`, 번호 발급 `next-id`)은 **[vibe-flow-skill](https://github.com/jinjan21-lgtm/vibe-flow-skill)** 의 스크립트입니다(`.claude/skills/vibe-flow/scripts/`). vibe-flow가 작업 흐름(골·설계 확인·리뷰·QA)을 맡고, team-handoff가 기록을 맡습니다. 둘이 만나는 형식은 vibe-flow의 [docs/structure.md](https://github.com/jinjan21-lgtm/vibe-flow-skill/blob/main/docs/structure.md)입니다.

vibe-flow 없이 쓸 때는 스크립트 단계를 손으로 합니다. 착수 줄을 커밋·push하고, 기록 뒤에는 `grep "$(date +%Y-%m-%d)" docs/handoff/WORKLOG/INDEX.md`로 INDEX 동시 갱신을 확인합니다. 인덱스 모드는 vibe-flow 스크립트가 필요합니다.

## License

MIT
