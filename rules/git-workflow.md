# Git 워크플로우

## 브랜치 전략

```
main (또는 master)
  └── develop
       ├── feature/기능명
       ├── fix/버그명
       └── refactor/대상
```

## 커밋 메시지 형식

```
[타입] 제목 (50자 이내)

본문 (선택, 72자 줄바꿈)
- 무엇을 변경했는지
- 왜 변경했는지

Co-Authored-By: Claude <noreply@anthropic.com>
```

### 타입
- `feat`: 새 기능
- `fix`: 버그 수정
- `docs`: 문서 변경
- `style`: 포맷팅 (코드 변경 없음)
- `refactor`: 리팩토링
- `test`: 테스트 추가/수정
- `chore`: 빌드, 설정 변경

## PR 체크리스트

- [ ] 테스트 통과
- [ ] 린트 통과
- [ ] 타입 체크 통과
- [ ] 리뷰어 지정
- [ ] 관련 이슈 연결

## 금지 사항

- main/master에 직접 push 금지
- force push 금지 (특별한 경우 제외)
- 대용량 바이너리 커밋 금지
- .env 파일 커밋 금지

## 병렬 작업 워크플로우 (git worktree)

여러 작업을 동시에 진행할 때 git worktree 사용:

### 권장 시나리오
- 긴급 버그 수정이 필요한데 현재 작업 중단 불가
- 여러 feature를 동시에 개발
- PR 리뷰하면서 내 작업 계속 진행

### 설정 방법
```bash
# 새 worktree 생성
git worktree add ../project-기능명 -b feature/기능명

# 각 worktree에서 별도 Claude 세션 실행
cd ../project-기능명 && claude
```

### 주의사항
- 작업 완료 후 반드시 worktree 정리 (`git worktree remove`)
- 같은 파일 동시 수정 시 충돌 주의
- worktree별 독립적인 컨텍스트 유지

### 자동화
`/worktree` 스킬 사용
