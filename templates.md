# 문서 생성 템플릿

새 프로젝트 시 아래 양식을 참고하여 docs/handoff/ 문서를 생성한다.

---

## PLAN.md

```markdown
# 프로젝트명

## 개요
프로젝트 목적과 배경

## 목표
- [ ] 목표 1
- [ ] 목표 2

## 기술 스택
- 언어/프레임워크

## 아키텍처
주요 구조 설명

## 주요 기능
### 기능 1
- 설명
- 요구사항

### 기능 2
- 설명
- 요구사항

## 일정/마일스톤
- Phase 1: ...
- Phase 2: ...

## 작업 이력
- 진입점: [docs/handoff/WORKLOG/INDEX.md](WORKLOG/INDEX.md) (또는 [WORKLOG.md](WORKLOG.md))
- 토픽: <필요 시 토픽 파일 링크>

## 변경 이력
| 날짜 | 변경 내용 | 사유 |
|------|----------|------|
| YYYY-MM-DD | 최초 작성 | - |
```

---

## WORKLOG — 단일 파일 모드 (작은 프로젝트)

`docs/handoff/WORKLOG.md`:

```markdown
# 작업 일지

## YYYY-MM-DD

### 완료한 태스크
- [x] 태스크명
  - 상세: 무엇을 했는지
  - 파일: 수정한 주요 파일 목록
  - 비고: 특이사항

---

## 이전 날짜...
```

---

## WORKLOG — 토픽 디렉토리 모드 (대형 프로젝트, 권장)

### `docs/handoff/WORKLOG/INDEX.md`

```markdown
# WORKLOG INDEX

> AI 진입점. 본문 읽기 전 여기 먼저 확인. 항목별 한 줄 요약 + 앵커 링크.

## YYYY-MM (활성)
- YYYY-MM-DD [topic] 한 줄 요약 → [topic.md#YYYY-MM-DD](topic.md#YYYY-MM-DD)
- ...

## YYYY-MM (이전)
- ...

<details>
<summary>이전 분기</summary>

## YYYY-Q?
- archive/YYYY-Q?.md
</details>

---

> 토픽 디렉토리:
> - [topic-1.md](topic-1.md) — 한 줄 설명
> - [topic-2.md](topic-2.md) — 한 줄 설명
```

### `docs/handoff/WORKLOG/<topic>.md`

```markdown
# WORKLOG — <topic>

> 한 줄 토픽 설명. 관련 spec/PLAN 링크.

---

<!--
date: YYYY-MM-DD
topic: <topic-slug>
tags: [tag1, tag2]
files: [path1, path2]
session: N
-->

## YYYY-MM-DD 한 줄 제목 #tag1 #tag2 {#YYYY-MM-DD}

### 완료한 태스크
- [x] 태스크명
  - 상세: 무엇을 했는지
  - 파일: 수정한 주요 파일 목록
  - 비고: 특이사항

---

(다음 항목, 시간 역순)
```

**최소 강제**: `date` + `topic` + 한 줄 요약 + 앵커. 나머지(tags/files/session/4섹션) 누락 허용.

---

## REMAINING.md

```markdown
# 남은 업무

> 마지막 업데이트: YYYY-MM-DD

## 긴급 (즉시 처리)
- [ ] 태스크 설명 (관련: [WORKLOG 앵커](WORKLOG/topic.md#anchor))

## 높음 (이번 주)
- [ ] 태스크 설명

## 보통
- [ ] 태스크 설명

## 낮음 (여유 시)
- [ ] 태스크 설명

## 보류 (블로커 있음)
- [ ] 태스크 설명
  - 블로커: 이유
```

---

## LESSONS.md

```markdown
# 교훈

> 1회: 한 줄 요약 | 2회+: 상세 설명 — 방지법 (N회)

(아직 기록된 교훈 없음)
```
