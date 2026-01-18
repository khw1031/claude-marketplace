---
name: nextjs-project-initializer
description: "Next.js 프로젝트를 실제로 생성하고 초기화하는 실행 단계. '프로젝트 만들어줘', '초기화해줘' 같은 실행 요청 시 자동 활성화"
---

# Next.js 프로젝트 초기화 실행

## 자동 활성화 시점
- "Next.js 프로젝트 만들어줘"
- "프로젝트 초기화해줘"
- "새 프로젝트 설정해줘"
- 실제 프로젝트 생성 실행 요청 시

---

## 다중 전문가 시뮬레이션

다음 전문가들의 관점에서 프로젝트 초기화를 수행합니다:
- **Frontend Architect**: 아키텍처 설계 및 구조 결정
- **DevOps Engineer**: 빌드 도구 및 배포 설정
- **DX Specialist**: 개발자 경험 최적화

---

## 사전 탐색 단계 (필수)

프로젝트 설정 전 **웹 검색**으로 다음 항목의 최신 정보를 확인:

1. ✅ **Next.js 최신 stable 버전** 및 주요 변경사항
2. ✅ **선택한 도구들의 최신 버전** 및 Next.js 호환성
3. ✅ **현재 권장되는 Node.js LTS 버전**

---

## 실행 프로세스

### Phase 1: 환경 준비

```bash
# 1. Node.js 버전 확인
node --version

# 2. 필요 시 Node.js 버전 관리자로 적절한 버전 설치
# fnm 사용 예시:
fnm install <version>
fnm use <version>

# 3. pnpm 설치 확인 (없으면 설치)
npm install -g pnpm
```

### Phase 2: 프로젝트 생성

```bash
# create-next-app으로 프로젝트 scaffolding
pnpm create next-app@latest <project-name>
```

**필수 선택 옵션:**
- ✅ TypeScript
- ✅ Tailwind CSS
- ✅ ESLint
- ✅ App Router
- ✅ src 디렉토리
- ✅ Turbopack

### Phase 3: 디렉토리 구조 설정

Feature-based 구조로 필요한 디렉토리 생성:

```bash
cd <project-name>

# 기본 구조 생성
mkdir -p src/features
mkdir -p src/shared/{components/{ui,design-system},hooks,lib,types,styles,config}
mkdir -p src/__tests__

# 예시 feature 생성 (auth)
mkdir -p src/features/auth/{components,hooks,api,types,utils}
echo "export {};" > src/features/auth/index.ts

# 테마 파일 생성
touch src/shared/styles/theme.ts
touch src/shared/styles/variants.ts
```

**테마 파일 초기 내용:**

`src/shared/styles/theme.ts`:
```typescript
// 디자인 토큰 정의
export const theme = {
  colors: {
    primary: 'hsl(var(--primary))',
    secondary: 'hsl(var(--secondary))',
    // ... 추가 색상
  },
  spacing: {
    xs: '0.25rem',
    sm: '0.5rem',
    md: '1rem',
    lg: '1.5rem',
    xl: '2rem',
  },
  // ... 추가 토큰
} as const;

export type Theme = typeof theme;
```

`src/shared/styles/variants.ts`:
```typescript
import { cva } from 'class-variance-authority';

// 공통 variant 패턴 예시
export const buttonVariants = cva(
  'inline-flex items-center justify-center rounded-md font-medium transition-colors',
  {
    variants: {
      variant: {
        primary: 'bg-primary text-primary-foreground hover:bg-primary/90',
        secondary: 'bg-secondary text-secondary-foreground hover:bg-secondary/80',
      },
      size: {
        sm: 'h-9 px-3 text-sm',
        md: 'h-10 px-4',
        lg: 'h-11 px-8 text-lg',
      },
    },
    defaultVariants: {
      variant: 'primary',
      size: 'md',
    },
  }
);
```

### Phase 4: 추가 도구 설치 및 구성

#### 1. shadcn/ui 초기화

```bash
pnpm dlx shadcn@latest init
```

**설정 시 주의:**
- Components path: `src/shared/components/ui`로 설정
- `class-variance-authority`가 자동 설치됨

#### 2. 필수 패키지 설치

```bash
# 런타임 의존성
pnpm add zod

# 개발 의존성
pnpm add -D vitest @vitest/ui @testing-library/react @testing-library/jest-dom jsdom
```

#### 3. 코드 품질 도구 설치 (사용자에게 선택 요청)

**옵션 A: Biome (권장)**
```bash
pnpm add -D @biomejs/biome

# biome.json 생성
pnpm biome init
```

`biome.json` 설정:
```json
{
  "$schema": "https://biomejs.dev/schemas/1.9.4/schema.json",
  "vcs": {
    "enabled": true,
    "clientKind": "git",
    "useIgnoreFile": true
  },
  "files": {
    "ignoreUnknown": false,
    "ignore": []
  },
  "formatter": {
    "enabled": true,
    "indentStyle": "space"
  },
  "linter": {
    "enabled": true,
    "rules": {
      "recommended": true
    }
  },
  "javascript": {
    "formatter": {
      "quoteStyle": "single"
    }
  }
}
```

**옵션 B: ESLint + Prettier**
```bash
pnpm add -D prettier eslint-config-prettier

# .prettierrc 생성
echo '{"semi": true, "singleQuote": true, "trailingComma": "es5"}' > .prettierrc
```

#### 4. Vitest 설정

`vitest.config.ts`:
```typescript
import { defineConfig } from 'vitest/config';
import react from '@vitejs/plugin-react';
import path from 'path';

export default defineConfig({
  plugins: [react()],
  test: {
    environment: 'jsdom',
    setupFiles: ['./vitest.setup.ts'],
  },
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
  },
});
```

`vitest.setup.ts`:
```typescript
import { expect, afterEach } from 'vitest';
import { cleanup } from '@testing-library/react';
import * as matchers from '@testing-library/jest-dom/matchers';

expect.extend(matchers);

afterEach(() => {
  cleanup();
});
```

#### 5. package.json 스크립트 업데이트

```json
{
  "scripts": {
    "dev": "next dev --turbopack",
    "build": "next build",
    "start": "next start",
    "lint": "biome check .",
    "lint:fix": "biome check --write .",
    "format": "biome format --write .",
    "format:check": "biome format .",
    "test": "vitest",
    "test:run": "vitest run",
    "test:coverage": "vitest run --coverage"
  }
}
```

#### 6. Node.js 버전 고정

`.nvmrc` (또는 `.node-version`):
```
20.11.0
```

`package.json`에 engines 추가:
```json
{
  "engines": {
    "node": ">=20.11.0",
    "pnpm": ">=8.0.0"
  }
}
```

#### 7. CLAUDE.md 생성

프로젝트 루트에 `CLAUDE.md` 생성 (nextjs-setup-guide skill의 템플릿 사용)

### Phase 5: 검증

모든 설정 완료 후 다음 명령어로 정상 작동 확인:

```bash
# 1. 린트 검사
pnpm lint

# 2. 포맷 검사
pnpm format:check

# 3. 빌드 테스트
pnpm build

# 4. 개발 서버 실행
pnpm dev
```

---

## 실행 체크리스트

각 Phase 완료 시 확인:

- [ ] Phase 1: Node.js 및 pnpm 버전 확인 완료
- [ ] Phase 2: Next.js 프로젝트 생성 완료
- [ ] Phase 3: Feature-based 디렉토리 구조 생성 완료
- [ ] Phase 4-1: shadcn/ui 초기화 완료 (`src/shared/components/ui` 경로)
- [ ] Phase 4-2: 필수 패키지 설치 완료 (zod, vitest, testing-library)
- [ ] Phase 4-3: 코드 품질 도구 설치 및 설정 완료
- [ ] Phase 4-4: Vitest 설정 파일 생성 완료
- [ ] Phase 4-5: package.json 스크립트 업데이트 완료
- [ ] Phase 4-6: Node.js 버전 고정 파일 생성 완료
- [ ] Phase 4-7: CLAUDE.md 생성 완료
- [ ] Phase 5: 모든 검증 명령어 실행 성공

---

## 에러 처리

- 각 단계에서 에러 발생 시 즉시 해결 후 진행
- 패키지 버전 충돌 발견 시 최신 호환 버전 확인
- 사용자에게 선택이 필요한 부분은 명확하게 질문

---

## 최종 산출물

프로젝트 초기화 완료 후 사용자에게 다음 정보 제공:

1. ✅ 생성된 디렉토리 구조 요약
2. ✅ 설치된 패키지 목록 및 버전
3. ✅ 실행 가능한 스크립트 목록
4. ✅ 다음 단계 권장사항

---

## 참고사항

초기화 완료 후 검증이 필요하면 자연스럽게 대화를 이어가세요. 별도의 검증 skill이 자동으로 활성화될 수 있습니다.
