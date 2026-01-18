---
name: nextjs-setup-guide
description: "Next.js 프로젝트 아키텍처, 기술 스택 선택, 디자인 시스템 구조에 대한 질문이나 계획 수립 시 자동으로 가이드 제공"
---

# Next.js 프로젝트 설계 가이드

## 자동 활성화 시점
- Next.js 프로젝트 구조나 아키텍처 질문
- "어떤 기술 스택을 사용해야 하나요?"
- "디자인 시스템은 어떻게 구성하나요?"
- Feature-based 아키텍처 설명 필요 시

---

## 기술 스택 권장사항

### Core Framework
- **Next.js** (App Router, Turbopack 활성화)
- **TypeScript** (strict mode)
- **React** (최신 stable)

### Styling
- **Tailwind CSS** (최신 버전)
- **shadcn/ui** (컴포넌트 라이브러리)

### 코드 품질 도구 선택 가이드

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

## Feature-based 아키텍처 패턴

### 디렉토리 구조

확장 가능하고 유지보수하기 쉬운 **Feature-based 아키텍처**를 기본으로 사용합니다.

```
src/
├── app/                    # Next.js App Router (라우팅만)
├── features/               # 기능별 모듈
│   ├── auth/              # 인증 관련
│   │   ├── components/    # 기능 전용 컴포넌트
│   │   ├── hooks/         # 기능 전용 hooks
│   │   ├── api/           # API 호출 로직
│   │   ├── types/         # 기능 전용 타입
│   │   ├── utils/         # 기능 전용 유틸
│   │   └── index.ts       # Public API (외부 노출)
│   └── ...
├── shared/                 # 공유 리소스
│   ├── components/        # 공용 UI 컴포넌트
│   │   ├── ui/           # shadcn/ui 원본 컴포넌트 (수정 금지)
│   │   └── design-system/ # 자체 디자인 시스템 컴포넌트
│   ├── hooks/            # 공용 hooks
│   ├── lib/              # 공용 유틸리티
│   ├── types/            # 공용 타입
│   ├── styles/           # 테마 및 스타일
│   │   ├── theme.ts      # 디자인 토큰 정의
│   │   └── variants.ts   # 공통 variant 패턴
│   └── config/           # 앱 설정
└── __tests__/             # 통합 테스트 (또는 colocated)
```

### Feature-based 구조 원칙

1. **높은 응집도, 낮은 결합도**
   - 각 feature는 자체 완결성을 유지합니다
   - feature 간 직접 import 금지

2. **명시적 API 노출**
   - `index.ts`를 통해 외부에 노출할 항목만 export
   - 내부 구현 세부사항은 캡슐화

3. **공유 규칙**
   - feature 간 공유는 `shared/`를 통해서만
   - 3회 이상 재사용되는 코드는 `shared/`로 이동 고려

4. **기타 원칙**
   - `src/` 디렉토리 사용 필수
   - 테스트 파일은 colocated 권장 (`*.test.tsx`)

---

## 디자인 시스템 및 테마 관리

### 핵심 원칙

프로젝트는 **자체 디자인 시스템**을 구축하고 **theme 기반**으로 개발합니다.

### 구조 및 역할

#### 1. shadcn/ui 컴포넌트 (`shared/components/ui/`)
- shadcn이 생성한 **원본 파일은 절대 직접 수정 금지**
- UI primitive로만 사용
- 낮은 레벨의 기본 컴포넌트 제공

#### 2. 디자인 시스템 컴포넌트 (`shared/components/design-system/`)
- shadcn/ui 컴포넌트를 **래핑**하여 프로젝트 전용 컴포넌트 생성
- 비즈니스 로직 및 스타일 커스터마이징 적용
- **예시**: `Button.tsx` (shadcn) → `PrimaryButton.tsx`, `SecondaryButton.tsx` (디자인 시스템)

#### 3. 테마 정의 (`shared/styles/theme.ts`)
- 디자인 토큰 중앙 관리
  - colors
  - spacing
  - typography
  - shadows
  - borders 등
- CSS 변수와 TypeScript 타입으로 관리
- 하드코딩된 값 사용 금지

#### 4. Variant 패턴 (`shared/styles/variants.ts`)
- `class-variance-authority` (cva) 사용
- 재사용 가능한 스타일 variant 정의
- 일관된 스타일 조합 보장

### 디자인 시스템 워크플로우

```
1. shadcn/ui로 기본 컴포넌트 추가
   → shared/components/ui/button.tsx

2. 테마 기반 커스텀 컴포넌트 생성
   → shared/components/design-system/primary-button.tsx
   → theme.ts의 디자인 토큰 활용
   → cva로 variant 정의

3. 애플리케이션에서는 디자인 시스템 컴포넌트만 사용
   ✅ import { PrimaryButton } from '@/shared/components/design-system'
   ❌ import { Button } from '@/shared/components/ui/button'
```

### 규칙 요약

#### ✅ 반드시 준수
- 모든 스타일은 theme 기반으로 작성
- 새 UI 컴포넌트는 반드시 디자인 시스템 레이어를 거침
- 디자인 토큰을 통한 일관된 스타일 적용

#### ❌ 절대 금지
- `shared/components/ui/` 내 shadcn/ui 원본 파일 직접 수정
- shadcn/ui 컴포넌트를 애플리케이션 코드에서 직접 사용
- 하드코딩된 색상/spacing 값 사용 (예: `text-blue-500`, `p-4` 등)

---

## CLAUDE.md 템플릿

프로젝트 루트에 `CLAUDE.md` 파일을 생성하여 AI 어시스턴트에게 프로젝트 규칙을 전달합니다:

```markdown
# 프로젝트 개발 가이드

## 아키텍처
- Feature-based 구조 사용
- Feature 간 직접 import 금지, `shared/`를 통해서만 공유
- 각 feature의 `index.ts`를 통해 명시적 API만 외부 노출

## 디자인 시스템 규칙

### 절대 금지
- `shared/components/ui/` 내 shadcn/ui 원본 파일 직접 수정 금지
- shadcn/ui 컴포넌트를 애플리케이션 코드에서 직접 사용 금지
- 하드코딩된 색상/spacing 사용 금지 (예: text-blue-500, p-4)

### 필수 준수
- 새로운 UI 컴포넌트 필요 시:
  1. `shared/components/ui/`에서 적절한 shadcn/ui 컴포넌트 선택
  2. `shared/components/design-system/`에 래퍼 컴포넌트 생성
  3. `shared/styles/theme.ts`의 디자인 토큰 활용
  4. variant가 필요하면 `class-variance-authority` (cva) 사용

- 모든 스타일은 theme 기반으로 작성

## 코드 품질
- TypeScript strict mode 준수
- 모든 컴포넌트는 테스트 작성
- Zod를 사용한 런타임 검증
```

---

## 설정 요구사항

### 버전 고정
- `.nvmrc` 또는 `.node-version` 파일 생성
- `package.json`에 `engines` 필드 명시

### 필수 스크립트
```json
{
  "scripts": {
    "dev": "개발 서버 실행",
    "build": "프로덕션 빌드",
    "lint": "코드 린팅",
    "format": "코드 포맷팅",
    "format:check": "포맷 검사",
    "test": "테스트 실행",
    "test:run": "단일 테스트 실행",
    "test:coverage": "커버리지 포함 테스트"
  }
}
```

---

## 참고사항

이 skill은 **계획 및 설계 단계**에서 활성화됩니다. 실제 프로젝트 초기화 실행이 필요하면 자연스럽게 대화를 이어가세요.
