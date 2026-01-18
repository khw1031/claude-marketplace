---
name: vite-react-project-initializer
description: "Vite + React 프로젝트를 실제로 생성하고 초기화하는 실행 단계"
---

# Vite + React 프로젝트 초기화 실행

## 자동 활성화 시점
- "Vite React 프로젝트 만들어줘"
- "프로젝트 초기화해줘"

---

## 실행 프로세스

### Phase 1: 환경 준비

```bash
node --version
fnm install <version>
fnm use <version>
npm install -g pnpm
```

### Phase 2: 프로젝트 생성

```bash
pnpm create vite@latest <project-name> --template react-ts
```

### Phase 3: 디렉토리 구조 설정

```bash
cd <project-name>

mkdir -p src/features
mkdir -p src/shared/{components/{ui,design-system},hooks,lib,types,styles,utils}

# 테마 파일
touch src/shared/styles/theme.ts
touch src/shared/styles/variants.ts
```

### Phase 4: 추가 도구 설치

#### 1. React Router

```bash
pnpm add react-router-dom
```

#### 2. Tailwind CSS + shadcn/ui

```bash
pnpm add -D tailwindcss postcss autoprefixer
pnpm dlx tailwindcss init -p

# shadcn/ui
pnpm dlx shadcn@latest init
```

shadcn/ui 설정:
- Components path: `src/shared/components/ui`

#### 3. 필수 패키지

```bash
pnpm add zod
pnpm add -D vitest @vitest/ui @testing-library/react @testing-library/jest-dom jsdom
```

#### 4. 코드 품질 도구 (선택)

**Biome**:
```bash
pnpm add -D @biomejs/biome
pnpm biome init
```

**ESLint+Prettier**:
```bash
pnpm add -D prettier eslint-config-prettier
echo '{"semi": true, "singleQuote": true}' > .prettierrc
```

#### 5. Vitest 설정

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

#### 6. package.json 스크립트

```json
{
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "lint": "biome check .",
    "lint:fix": "biome check --write .",
    "format": "biome format --write .",
    "format:check": "biome format .",
    "test": "vitest",
    "test:run": "vitest run"
  }
}
```

#### 7. Node.js 버전 고정

```bash
echo "20.11.0" > .nvmrc
```

#### 8. CLAUDE.md 생성

**commands/claude-md-generator 사용**:

```markdown
1. commands/rules-extractor/command.md 읽기
2. 규칙 추출
3. commands/claude-md-generator/command.md 읽기
4. 템플릿 렌더링
5. CLAUDE.md 생성
```

### Phase 5: 검증

```bash
pnpm lint
pnpm format:check
pnpm build
pnpm dev
```

---

## 체크리스트

- [ ] Phase 1: 환경 준비
- [ ] Phase 2: 프로젝트 생성
- [ ] Phase 3: 디렉토리 구조
- [ ] Phase 4: 도구 설치
- [ ] Phase 5: 검증

---

## 참고사항

초기화 완료 후 검증이 필요하면 자연스럽게 대화를 이어가세요.
