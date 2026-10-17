# Examples — 좋은 엔트리 vs 나쁜 엔트리

> SKILL.md 룰의 구체 적용 사례. Before(위반) ↔ After(준수) 대비.

---

## 예제 1: WORKLOG 토픽 파일 엔트리

### ❌ Before — frontmatter YAML, 앵커 누락, INDEX 미갱신, 임의 필드

`docs/handoff/WORKLOG/auth.md`:

```markdown
---
date: 2026-04-25
topic: auth
plan_refs: [feat-login, feat-oauth]
related: feat-mobile
---

## 2026-04-25 OAuth 구현
- [x] 콜백 URL 설정
```

위반:
- YAML `---` frontmatter — 마크다운 렌더러가 메타로 인식 시도, grep `^date:` 패턴이 `---` 경계에서 막힘
- `plan_refs`/`related` — 화이트리스트(date/topic/tags/files/session) 외 필드
- 헤더에 `{#anchor}` 없음 — INDEX에서 점프 불가
- 인라인 태그 `#tag` 없음 — grep 검색 안 됨
- INDEX.md 동시 갱신 누락

### ✅ After

`docs/handoff/WORKLOG/auth.md`:

```markdown
<!--
date: 2026-04-25
topic: auth
tags: [oauth, login]
files: [src/auth/oauth.ts]
session: 12
-->

## 2026-04-25 OAuth 콜백 URL 설정 #oauth #login {#2026-04-25-oauth}

### 완료한 태스크
- [x] 콜백 URL 설정
  - 파일: src/auth/oauth.ts
```

`docs/handoff/WORKLOG/INDEX.md` (동시 추가):

```markdown
## 2026-04
- 2026-04-25 [auth] OAuth 콜백 URL 설정 → [auth.md#2026-04-25-oauth](auth.md#2026-04-25-oauth)
```

---

## 예제 2: REMAINING 항목

### ❌ Before — 완료 항목 잔존, 앵커 링크 누락

```markdown
## 높음 (이번 주)
- [x] OAuth 콜백 URL 설정
- [ ] 로그인 폼 검증 추가
```

위반:
- 완료된 `[x]` 항목이 REMAINING에 잔존 → 다음 세션 중복 작업
- 새 항목에 WORKLOG 앵커 링크 없음 → 어디서 논의됐는지 추적 불가

### ✅ After

```markdown
## 높음 (이번 주)
- [ ] 로그인 폼 검증 추가 ([WORKLOG](WORKLOG/auth.md#2026-04-25-oauth))
```

완료 항목은 즉시 제거. 새 항목은 관련 WORKLOG 앵커 부착.

---

## 예제 3: PLAN 변경이력

### ❌ Before — 목표 수정 후 변경이력 미갱신

`PLAN.md`:

```markdown
## 목표
- [x] 기본 로그인
- [ ] OAuth 지원   ← 새로 추가됨

## 변경 이력
| 날짜 | 변경 내용 | 사유 |
|------|----------|------|
| 2026-04-01 | 최초 작성 | - |
```

위반: 목표가 추가됐는데 변경이력은 그대로 → 사유·시점 추적 끊김.

### ✅ After

```markdown
## 변경 이력
| 날짜 | 변경 내용 | 사유 |
|------|----------|------|
| 2026-04-01 | 최초 작성 | - |
| 2026-04-25 | 목표에 OAuth 지원 추가 | 외부 협업 요청 (Slack #product) |
```

WORKLOG에도 동일 변경 기록 — 양방향 추적.

---

## 예제 4: 자기 검증 (태스크 완료 직후)

### ❌ Before — 갱신했다고 믿고 다음 작업 진행

```bash
# WORKLOG 갱신 끝 → 바로 다음 태스크
```

위반: INDEX 동시 갱신을 잊어도 모름. 다음 세션이 stale INDEX를 읽음.

### ✅ After

```bash
# 갱신 후 즉시
grep "$(date +%Y-%m-%d)" docs/handoff/WORKLOG/INDEX.md
# → 결과 없으면 INDEX 누락. 즉시 보강.
```

자기 검증 룰을 매 태스크 완료마다 실행 — 실수가 다음 세션까지 살아남지 않음.
