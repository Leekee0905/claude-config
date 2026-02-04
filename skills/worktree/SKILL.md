---
name: worktree
description: git worktree를 사용한 병렬 작업 설정 자동화. 여러 브랜치를 동시에 작업할 수 있도록 워킹 디렉토리 생성/관리.
allowed-tools: Bash, Read
---

# Git Worktree 병렬 작업

여러 브랜치를 동시에 작업할 수 있도록 git worktree를 설정합니다.

## 사용법

```
/worktree                           # 현재 worktree 목록 확인
/worktree create feat-login         # feature/feat-login 브랜치로 worktree 생성
/worktree create fix-bug hotfix     # hotfix/fix-bug 브랜치로 worktree 생성
/worktree cleanup                   # 모든 worktree 정리
```

## 명령어

### 목록 확인
```bash
git worktree list
```

### 새 worktree 생성
```bash
# 기본 (feature 브랜치)
git worktree add ../프로젝트명-기능명 -b feature/기능명

# hotfix 브랜치
git worktree add ../프로젝트명-fix명 -b hotfix/fix명

# 기존 브랜치 체크아웃
git worktree add ../프로젝트명-브랜치명 브랜치명
```

### worktree 삭제
```bash
git worktree remove ../프로젝트명-기능명
```

### 전체 정리
```bash
git worktree prune
```

## 병렬 작업 패턴

### 패턴 1: Feature + Hotfix 동시 작업
```bash
# 메인 디렉토리: 현재 작업
# worktree 1: 긴급 버그 수정
git worktree add ../project-hotfix -b hotfix/urgent-bug

# 각 터미널에서 별도 Claude 세션 실행
cd ../project-hotfix && claude
```

### 패턴 2: 여러 Feature 동시 개발
```bash
git worktree add ../project-auth -b feature/auth
git worktree add ../project-dashboard -b feature/dashboard
git worktree add ../project-api -b feature/api-refactor

# 각각 별도 터미널에서 claude 실행
```

### 패턴 3: 리뷰 + 개발 동시 진행
```bash
# 메인: 내 개발 작업
# worktree: 동료 PR 리뷰
git worktree add ../project-review colleague/feature-branch
```

## 주의사항

1. **같은 브랜치 중복 불가**: 이미 체크아웃된 브랜치는 다른 worktree에서 사용 불가
2. **cleanup 필수**: 작업 완료 후 `git worktree remove` 또는 `git worktree prune` 실행
3. **경로 주의**: worktree는 프로젝트 상위 디렉토리에 생성 권장 (../project-xxx)
4. **충돌 방지**: 같은 파일을 여러 worktree에서 수정 시 merge conflict 발생 가능

## 실행 절차

1. 현재 worktree 목록 확인
2. 새 worktree 생성 (브랜치 타입에 따라 prefix 자동 설정)
3. 생성된 디렉토리 경로 안내
4. Claude 세션 시작 명령어 제공

## 브랜치 타입별 prefix

| 인자 | 브랜치 prefix | 예시 |
|------|--------------|------|
| (기본) | feature/ | `create login` → `feature/login` |
| hotfix | hotfix/ | `create bug hotfix` → `hotfix/bug` |
| fix | fix/ | `create typo fix` → `fix/typo` |
| refactor | refactor/ | `create api refactor` → `refactor/api` |

## 예시 시나리오

### 긴급 버그 수정이 들어왔을 때
```bash
# 현재 feature 작업 중...
# 긴급 버그 수정 요청!

# 1. hotfix worktree 생성
git worktree add ../project-hotfix -b hotfix/critical-bug

# 2. 새 터미널에서 hotfix 작업
cd ../project-hotfix
claude  # 새 Claude 세션

# 3. 버그 수정 완료 후 정리
git worktree remove ../project-hotfix
```

### 여러 팀원과 동시 작업
```bash
# 각자 담당 feature를 worktree로 분리
git worktree add ../project-auth -b feature/auth        # 인증
git worktree add ../project-payment -b feature/payment  # 결제
git worktree add ../project-ui -b feature/ui-redesign   # UI

# 각 worktree에서 독립적으로 Claude 실행
```
