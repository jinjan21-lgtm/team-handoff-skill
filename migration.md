# v0(단일 파일) → v6(토픽 디렉토리) 전환 절차

사용자가 명시적으로 "분할" 또는 "v6 적용" 명령을 내렸을 때만 발동. **자동 트리거 없음.**

## 절차

1. **백업**: `docs/handoff/`를 `docs/handoff/_backup-<YYYY-MM-DD>/`로 통째 복사
2. **변환안 제시**: 현재 WORKLOG의 항목 → 토픽 매핑 제안. 토픽 단위 = 기능·영역 (예: `auth`, `mcp-config`, `migration`)
3. **사용자 1회 승인**
4. **AI 일괄 변환**:
   - `WORKLOG.md` → `WORKLOG/INDEX.md` + 토픽 파일들
   - 각 항목에 HTML 주석 frontmatter + 인라인 태그 + `{#anchor}` 부여
   - PLAN/REMAINING의 WORKLOG 참조를 앵커 링크로 갱신
5. **끝**. rollback·self-check 없음. 문제 시 git 또는 백업 디렉토리로 복구.

## 토픽 매핑 가이드

- 토픽은 가능한 적게 (대형 프로젝트도 5~10개 이하 권장)
- 같은 영역 작업은 한 토픽에
- 분류 모호하면 `misc.md`로 일단 분리 후 추후 재분류
- 토픽 이름은 kebab-case, 짧게 (`auth`, `db-migration`)

## 변환 후 확인 항목

- INDEX에 모든 항목이 들어갔는가?
- 앵커 ID가 토픽 파일 헤더와 일치하는가?
- PLAN/REMAINING의 기존 텍스트 참조가 앵커 링크로 갱신됐는가?

## 다운그레이드 (v6 → v0)

거의 발생 안 함. 필요하면 사용자가 직접 `WORKLOG/*.md`를 시간순으로 합쳐 단일 `WORKLOG.md`로 작성. 자동 명령 없음.
