# Jira 이슈 기반 Git 브랜치 생성

Jira 이슈 번호: $ARGUMENTS

## 작업 순서

1. **Jira 이슈 조회**: 위 이슈 번호로 Jira MCP 도구를 사용해서 이슈 제목(summary) 가져오기

2. **브랜치명 생성**: 아래 규칙으로 브랜치명 생성
   - 형식: `{이슈번호}_{타이틀_영어_스네이크케이스}`
   - 타이틀은 영어로 번역 (한글인 경우)
   - 핵심 단어 3-5개만 사용 (너무 길지 않게)
   - 소문자, 언더바로 연결
   - 예: `BEUS-11237_add_user_login_validation`

3. **Git 브랜치 생성 및 이동**:
   - 현재 브랜치가 `staging-master`가 아닐 경우 `staging-master`로 이동
   - `staging-master`로 이동 후 `git pull origin staging-master`로 브랜치 최신화
   - 최신화 후 아래 shell 명령어로 브랜치 생성 및 이동
   ```bash
   git checkout -b {브랜치명}
   ```

## 브랜치명 규칙
- 최대 50자 이내 권장
- 불필요한 단어 제거 (the, a, an, for, to 등)
- 동사 + 명사 형태로 간결하게
- 예시:
  - "사용자 로그인 유효성 검사 추가" → `BEUS-123_add_login_validation`
  - "[버그] 메인 페이지 로딩 오류 수정" → `BEUS-456_fix_main_page_loading`
