# 현재 브랜치 Jira 이슈 조회

## 작업 순서

1. **현재 브랜치에서 이슈번호 추출**:
   - `git branch --show-current`로 현재 브랜치명 확인
   - 브랜치명에서 이슈번호 추출 (패턴: `[A-Z]+-[0-9]+`)
   - 이슈번호가 없으면 → 3번(나에게 할당된 이슈 목록 조회)으로 이동

3. **이슈번호 없을 때 — 내 이슈 목록 조회**:
   - `jira_search`로 `assignee = currentUser() AND status != Done ORDER BY updated DESC` 조회
   - 이슈 키, 제목, 상태, 우선순위를 목록으로 출력
   - 사용자가 이슈를 선택하면 해당 이슈로 4번 진행

4. **이슈 정보 조회**:
   - `jira_get_issue`로 이슈 상세 정보 가져오기 (fields: `*all`, comment_limit: 50)

5. **결과 출력**:
   - 이슈 키, 제목, 상태, 담당자, 우선순위
   - 설명 (description)
   - 댓글 목록 (작성자, 시간, 내용)
