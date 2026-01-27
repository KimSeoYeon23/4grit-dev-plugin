# Git 커밋 with Jira 이슈번호

## 작업 순서

1. **현재 브랜치에서 이슈번호 추출**:
   - `git branch --show-current` 로 현재 브랜치명 확인
   - 브랜치명에서 이슈번호 추출 (예: `BEUS-11237_add_login` → `BEUS-11237`)

2. **변경사항 확인**:
   - `git status` 로 변경된 파일 확인
   - `git diff --staged` 또는 `git diff` 로 변경 내용 확인

3. **커밋 메시지 작성**:
   - 형식: `{이슈번호} {작업내용 영어로 간단히}`
   - 첫 글자 대문자, 동사 원형으로 시작
   - 50자 이내 권장
   - 예: `BEUS-11237 Add login validation`

4. **커밋 실행**:
   ```bash
   git add -A
   git commit -m "{커밋메시지}"
   ```

## 커밋 메시지 규칙
- 동사 원형으로 시작: Add, Fix, Update, Remove, Refactor, Improve
- 불필요한 단어 제거
- 예시:
  - `BEUS-123 Add user authentication`
  - `GEO-456 Fix button color on mobile`
  - `MKT-789 Update landing page layout`
