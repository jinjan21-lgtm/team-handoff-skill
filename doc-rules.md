# 문서별 규칙 · AI 참조 룰 · 흔한 실수

SKILL.md의 상세. 빈 양식은 [templates.md](templates.md), 좋은 기록 vs 나쁜 기록은 [examples.md](examples.md).

## 문서별 규칙

### PLAN.md
양식: templates.md. 기획 변경 시 변경이력 테이블에 한 행 + WORKLOG에도 기록 + 양방향 앵커 유지.

### WORKLOG (단일 파일)
날짜별 역순. 양식: templates.md.

### WORKLOG (토픽 디렉토리)

**INDEX.md** — 한 줄/항목, 시간순 그룹화. 옛 분기는 `<details>` 또는 `INDEX-archive/<quarter>.md`:
```markdown
## YYYY-MM
- YYYY-MM-DD [topic] @작성자 한 줄 요약 → [topic.md#anchor](topic.md#anchor)
```

**토픽 파일** — 항목 양식은 SKILL.md 「WORKLOG 항목 양식」. frontmatter는 HTML 주석(YAML은 렌더링·grep을 동시에 깨뜨린다), 필드는 `date`/`topic`/`tags`/`files`/`session` 5개만. 최소 강제: `date` + `topic` + 한 줄 요약 + 앵커. 분류가 모호하면 `misc.md`.

### REMAINING.md
미완료 태스크. 양식: templates.md. 항목에 WORKLOG 앵커 링크 부착 권장. 끝난 항목은 지우지 않고 `[x] (@완료자 날짜)`로 바꾼다 — 다른 참여자가 누가 언제 끝냈는지 본다. `[x]`가 쌓이면 인덱스 모드는 archive가 완료 월 파일로 옮기고([index-mode.md](index-mode.md)), 그 밖의 모드는 다음 작업 커밋에서 `## 완료` 소제목 아래로 옮긴다(원문 그대로).

### LESSONS.md
1회는 한 줄, 2회+는 상세 + 횟수. 비슷한 항목은 합친다. **항목 삭제 금지**.

## AI 참조 룰 (긴 md)

1. 인덱스 모드면 모든 문서에: **진입 `INDEX.md` → 문서별 INDEX → 본문**. 결정은 태그 grep
2. **`WORKLOG/INDEX.md`만 먼저** 읽는다
3. INDEX에서 토픽·태그·날짜를 찾아 해당 토픽 파일의 frontmatter + `##` 헤더만 훑는다
4. 본문은 **정말 필요할 때만**. 통째 로드 금지

PLAN ↔ WORKLOG ↔ REMAINING은 앵커 링크로 점프. 태그 검색은 grep: `#auth`, `#migration`.

## Common Mistakes (사후 결과·증상)

- 끝낸 항목을 `[ ]`·`[~]`로 남겨 둠 → 다음 세션·다른 참여자 중복 작업
- WORKLOG에 수정 파일 목록 누락 → 변경 범위 파악 불가
- **토픽 디렉토리 모드에서 INDEX 동시 갱신 누락** → INDEX stale, 검색 실패
- PLAN 변경 시 변경이력 미갱신 → 사유 추적 불가
- 태스크 완료 후 기록을 미루고 다음 작업 → 인수인계 실패
- 실수했는데 LESSONS 안 씀 → 같은 실수 반복
- INDEX 무시하고 토픽 파일 통째 로드 → 검색 효율 무의미
- frontmatter를 YAML로 → 마크다운 렌더링 깨짐
- 결정 번호를 「마지막 + 1」로 → 다른 브랜치와 같은 번호가 union으로 두 줄
