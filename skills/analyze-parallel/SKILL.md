---
name: analyze-parallel
description: 코드베이스를 병렬 에이전트로 동시 분석. 분야별(보안, 품질, 아키텍처) 또는 구역별(폴더/모듈) 분석 지원.
allowed-tools: Read, Grep, Glob, Task
---

# 병렬 에이전트 코드 분석

코드베이스를 여러 관점에서 동시에 분석합니다.

## 사용법

```
/analyze-parallel                    # 전체 코드베이스 분야별 분석
/analyze-parallel src/               # 특정 폴더 분석
/analyze-parallel --mode=zone        # 구역별 분석 모드
/analyze-parallel --focus=security   # 특정 분야만 분석
```

## 분석 모드

### 분야별 분석 (기본)

여러 전문 에이전트가 동시에 각자 분야를 분석:

| 에이전트 | 담당 분야 | 검사 내용 |
|----------|-----------|-----------|
| `architect` | 전체 구조 | 아키텍처 패턴, 의존성, 설계 |
| `security-reviewer` | 보안 | XSS, SQL 인젝션, 인증 취약점 |
| `code-reviewer` | 코드 품질 | 가독성, 중복, 에러 처리 |
| `frontend-developer` | UI/UX | 컴포넌트 구조, 성능, 접근성 |

### 구역별 분석 (--mode=zone)

대규모 프로젝트에서 폴더별 병렬 분석:

```
src/
├── api/        → 에이전트 1
├── components/ → 에이전트 2
├── utils/      → 에이전트 3
└── services/   → 에이전트 4
```

## 실행 절차

1. **범위 파악**
   - 분석 대상 디렉토리 확인
   - 파일 구조 탐색

2. **모드 결정**
   - 분야별: 전문 에이전트별 분석
   - 구역별: 폴더별 분석

3. **병렬 실행**
   - Task 도구로 서브에이전트 동시 실행
   - 각 에이전트에 명확한 범위 지정

4. **결과 통합**
   - 각 분석 결과 수집
   - 우선순위별 정리

5. **리포트 생성**
   - ANALYSIS-REPORT.md 파일 생성

## 결과물

분석 완료 후 `ANALYSIS-REPORT.md` 파일 생성:

```markdown
# 병렬 분석 리포트

## 분석 개요
- 분석 범위: [전체/특정 폴더]
- 분석 모드: [분야별/구역별]

## 분야별 분석 결과

### 🏛️ 아키텍처
[분석 결과]

### 🔒 보안
[분석 결과]

### 📋 코드 품질
[분석 결과]

## 종합 권장사항
1. 🔴 [우선순위 높음]
2. 🟡 [우선순위 중간]
3. 🟢 [우선순위 낮음]
```

## 옵션

| 옵션 | 설명 | 예시 |
|------|------|------|
| `--mode=zone` | 구역별 분석 모드 | `/analyze-parallel --mode=zone` |
| `--focus=X` | 특정 분야만 분석 | `--focus=security,quality` |
| `--depth=N` | 분석 깊이 (1-3) | `--depth=2` |
| `--output=FILE` | 출력 파일명 지정 | `--output=REPORT.md` |

## 사용 예시

### 전체 분석
```
/analyze-parallel
```
→ 아키텍처, 보안, 코드 품질 동시 분석

### 보안 집중 분석
```
/analyze-parallel --focus=security
```
→ 보안 취약점만 심층 분석

### 대규모 프로젝트 구역별 분석
```
/analyze-parallel src/ --mode=zone
```
→ src 하위 폴더별 병렬 분석

### PR 전 빠른 검토
```
/analyze-parallel --depth=1
```
→ 가벼운 수준의 빠른 분석

## 왜 빠를까요?

| 방식 | 3개 영역 분석 |
|------|---------------|
| 순차 분석 | ⏱️⏱️⏱️ (3배) |
| 병렬 분석 | ⏱️ (1배) |

각 에이전트가 **독립적으로 동시 실행**되기 때문에 가장 오래 걸리는 작업 시간만큼만 소요됩니다!
