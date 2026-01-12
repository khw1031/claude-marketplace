---
description: Next.js 프로젝트를 프로덕션 수준의 도구와 설정으로 초기화
argument-hint: [project-name?]
---

## SIMULATION GOAL

다중 전문가(Frontend Architect, DevOps Engineer, DX Specialist)의 관점에서
최신 Next.js 프론트엔드 프로젝트의 초기 설정을 수행하는 시뮬레이션을 진행한다.

---

## 목표

Next.js 기반 프론트엔드 프로젝트를 **프로덕션 수준의 기초 구성**으로 초기화한다.

---

## 사전 탐색 단계 (REQUIRED)

프로젝트 설정 전 다음 항목들의 **최신 버전과 호환성**을 웹 검색으로 확인:
1. Next.js 최신 stable 버전 및 주요 변경사항
2. 선택한 도구들의 최신 버전 및 Next.js 호환성
3. 현재 권장되는 Node.js LTS 버전

---

## 기술 스택 선택 기준

### Core Framework
- **Next.js** (App Router, Turbopack 활성화)
- **TypeScript** (strict mode)
- **React** (최신 stable)

### Styling
- **Tailwind CSS** (최신 버전)
- **shadcn/ui** (컴포넌트 라이브러리)

### 코드 품질 도구 (택 1 - 프로젝트 특성에 따라 선택)

| 옵션 | 도구 | 적합한 상황 |
|------|------|-------------|
| A | **Biome** (통합 도구) | 신규 프로젝트, 빠른 설정, 대규모 코드베이스 |
| B | **ESLint + Prettier** | 기존 팀 표준 유지, 특수 플러그인 필요, 세밀한 규칙 커스터마이징 |

### 검증/테스팅
- **Zod** (스키마 검증)
- **Vitest** + Testing Library (단위/통합 테스트)

### 환경 관리
- **fnm** 또는 **nvm** (Node.js 버전 관리)
- **pnpm** (패키지 매니저)

---

## 설정 요구사항

### 디렉토리 구조
- `src/` 디렉토리 사용
- 테스트 파일 분리 (`src/__tests__/` 또는 colocated)

### 필수 스크립트
- `dev`: 개발 서버 실행
- `build`: 프로덕션 빌드
- `lint`: 코드 린팅
- `format` / `format:check`: 코드 포맷팅
- `test`, `test:run`, `test:coverage`: 테스트 실행

### 버전 고정
- `.nvmrc` 또는 `.node-version` 파일
- `package.json`에 `engines` 필드 명시

---

## 실행 프로세스

### Phase 1: 환경 준비
1. Node.js 버전 관리자로 적절한 Node.js 버전 설치/설정
2. pnpm 전역 설치 (필요시)

### Phase 2: 프로젝트 생성
1. `create-next-app` 으로 프로젝트 scaffolding
2. 필수 옵션: TypeScript, Tailwind, ESLint, App Router, src 디렉토리, Turbopack

### Phase 3: 추가 도구 설치 및 구성
1. shadcn/ui 초기화
2. Zod 설치
3. 코드 품질 도구 설치 및 구성 (선택한 옵션에 따라)
4. Vitest + Testing Library 설치 및 구성

### Phase 4: 검증
모든 설정 완료 후 다음 명령어로 정상 작동 확인:
- 린트 검사
- 포맷 검사
- 테스트 실행
- 개발 서버 실행

---

## 산출물 체크리스트

- [ ] 프로젝트가 `pnpm dev`로 정상 실행됨
- [ ] 모든 lint/format 검사 통과
- [ ] 샘플 테스트 파일 실행 성공
- [ ] Node.js 버전 파일 존재
- [ ] 적절한 `.gitignore` 구성
- [ ] README.md에 설정 스크립트 문서화

---

## 추가 지침

- 각 단계에서 발생하는 에러는 즉시 해결 후 진행
- 설치되는 패키지의 버전 충돌 확인
- 최종 설정 파일들의 내용을 요약하여 보고
- 프로젝트 특성에 맞지 않는 기본 설정은 조정 가능함을 안내