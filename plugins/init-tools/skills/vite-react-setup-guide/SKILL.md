---
name: vite-react-setup-guide
description: "Vite + React 프로젝트 아키텍처, 기술 스택 선택에 대한 질문이나 계획 수립 시 자동으로 가이드 제공"
---

# Vite + React 프로젝트 설계 가이드

## 자동 활성화 시점
- Vite + React 프로젝트 구조나 아키텍처 질문
- "어떤 기술 스택을 사용해야 하나요?"
- Feature-based 아키텍처 설명 필요 시

---

## 기술 스택 권장사항

### Core
- **Vite** (최신 stable)
- **React** (최신 stable)
- **TypeScript** (strict mode)

### 라우팅
- **React Router** (v6+)

### 상태 관리 (선택)
- **Zustand** (가벼운 프로젝트)
- **Redux Toolkit** (복잡한 상태 관리 필요 시)

### Styling
- **Tailwind CSS**
- **shadcn/ui** (컴포넌트 라이브러리)

### 코드 품질 도구 선택

| 옵션 | 도구 | 적합한 상황 |
|------|------|-------------|
| A | **Biome** | 신규 프로젝트, 빠른 설정 |
| B | **ESLint + Prettier** | 기존 팀 표준, React 플러그인 필요 |

### 테스팅
- **Vitest** + Testing Library

### 환경 관리
- **fnm** 또는 **nvm**
- **pnpm**

---

## Feature-based 아키텍처

### 디렉토리 구조

```
src/
├── main.tsx                # 진입점
├── App.tsx                 # 루트
├── features/               # 기능별 모듈
│   ├── auth/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── api/
│   │   ├── types/
│   │   └── index.ts
│   └── ...
└── shared/                 # 공유 리소스
    ├── components/
    │   ├── ui/            # shadcn/ui (수정 금지)
    │   └── design-system/ # 자체 컴포넌트
    ├── hooks/
    ├── lib/
    ├── types/
    ├── styles/
    │   ├── theme.ts
    │   └── variants.ts
    └── utils/
```

### 규칙

- Feature 간 직접 import 금지
- `index.ts`로 명시적 API 노출
- 공유는 `shared/`를 통해서만

---

## 디자인 시스템

### 절대 금지 ❌
- shadcn/ui 원본 파일 수정
- shadcn/ui 컴포넌트 직접 사용
- 하드코딩된 스타일 값

### 필수 준수 ✅
- shadcn/ui → 디자인 시스템 래퍼 생성
- theme 기반 스타일링
- cva로 variant 정의

---

## CLAUDE.md 템플릿

```markdown
# 프로젝트 개발 가이드

## 아키텍처
- Feature-based 구조
- Feature 간 직접 import 금지

## 디자인 시스템
- shadcn/ui 원본 수정 금지
- 디자인 시스템 래퍼 사용
- theme 기반 스타일링

## 코드 품질
- TypeScript strict mode
- 테스트 작성
```

---

## 참고사항

이 skill은 **계획 및 설계 단계**에서 활성화됩니다.
