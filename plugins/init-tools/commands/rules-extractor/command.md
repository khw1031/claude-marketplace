# Rules Extractor Command

프로젝트 디렉토리를 분석하여 실제 설정과 구조에서 규칙을 추출하는 command입니다.

## 역할

초기화된 프로젝트의 실제 파일, 디렉토리, 설정을 분석하여 CLAUDE.md에 포함될 규칙을 JSON 형태로 추출합니다.

## 입력

- **projectPath**: 분석할 프로젝트의 루트 경로
- **framework**: 프레임워크 타입 (`nextjs` | `nestjs` | `vite-react`)

## 출력

```typescript
interface ExtractedRules {
  framework: string;
  architecture: {
    pattern: string;        // 'feature-based' | 'layered' | 'modular'
    directories: string[];  // 실제 존재하는 주요 디렉토리
    features: string[];     // features/ 하위 디렉토리들
  };
  designSystem: {
    enabled: boolean;
    componentLibrary: string | null;      // 'shadcn/ui' | 'none'
    uiComponentsPath: string | null;      // 실제 경로
    designSystemPath: string | null;      // 실제 경로
    themeFiles: string[];                 // 실제 존재하는 테마 파일들
    hasVariants: boolean;                 // variants.ts 존재 여부
  };
  styling: {
    framework: string;      // 'tailwindcss' | 'styled-components' | 'css-modules'
    hasCSSVariables: boolean;
  };
  codeQuality: {
    linter: string | null;      // 'biome' | 'eslint' | null
    formatter: string | null;   // 'biome' | 'prettier' | null
    testRunner: string | null;  // 'vitest' | 'jest' | null
  };
  typescript: {
    enabled: boolean;
    strictMode: boolean;
    pathAliases: Record<string, string>;
  };
  dependencies: {
    runtime: Record<string, string>;
    dev: Record<string, string>;
  };
  nodeVersion: string | null;  // .nvmrc 또는 .node-version에서 추출
  packageManager: string;      // 'pnpm' | 'npm' | 'yarn'
}
```

## 실행 프로세스

### 1. 프레임워크 감지

```bash
# package.json에서 프레임워크 확인
cat package.json | grep -E "\"next\"|\"@nestjs/core\"|\"vite\""
```

### 2. 아키텍처 패턴 분석

```bash
# 디렉토리 구조 확인
ls -la src/

# Feature-based 패턴 확인
if [ -d "src/features" ]; then
  echo "Feature-based architecture detected"
  ls -d src/features/*/ 2>/dev/null
fi

# Layered 패턴 확인 (NestJS)
if [ -d "src/modules" ]; then
  echo "Layered architecture detected"
fi
```

### 3. 디자인 시스템 분석

```bash
# shadcn/ui 확인
if [ -f "components.json" ]; then
  echo "shadcn/ui detected"
  cat components.json | grep -E "\"ui\"|\"aliases\""
fi

# 테마 파일 확인
ls src/shared/styles/theme.ts 2>/dev/null
ls src/shared/styles/variants.ts 2>/dev/null

# class-variance-authority 확인
cat package.json | grep "class-variance-authority"
```

### 4. 코드 품질 도구 분석

```bash
# Biome 확인
if [ -f "biome.json" ]; then
  echo "Biome detected"
fi

# ESLint 확인
if [ -f ".eslintrc.json" ] || [ -f ".eslintrc.js" ]; then
  echo "ESLint detected"
fi

# Prettier 확인
if [ -f ".prettierrc" ] || [ -f ".prettierrc.json" ]; then
  echo "Prettier detected"
fi

# Vitest 확인
if [ -f "vitest.config.ts" ]; then
  echo "Vitest detected"
fi
```

### 5. TypeScript 설정 분석

```bash
# tsconfig.json 분석
if [ -f "tsconfig.json" ]; then
  cat tsconfig.json | grep -E "\"strict\"|\"paths\""
fi
```

### 6. 패키지 분석

```bash
# dependencies 추출
cat package.json | jq '.dependencies'

# devDependencies 추출
cat package.json | jq '.devDependencies'
```

### 7. Node.js 버전 추출

```bash
# .nvmrc 확인
if [ -f ".nvmrc" ]; then
  cat .nvmrc
elif [ -f ".node-version" ]; then
  cat .node-version
fi
```

### 8. 패키지 매니저 감지

```bash
# lock 파일로 패키지 매니저 확인
if [ -f "pnpm-lock.yaml" ]; then
  echo "pnpm"
elif [ -f "yarn.lock" ]; then
  echo "yarn"
elif [ -f "package-lock.json" ]; then
  echo "npm"
fi
```

## 사용 예시

```markdown
프로젝트를 분석하여 규칙을 추출합니다:

1. 아키텍처 패턴 확인 중...
   ✅ Feature-based 아키텍처 감지
   ✅ Features: auth, dashboard, profile

2. 디자인 시스템 확인 중...
   ✅ shadcn/ui 설치됨
   ✅ UI Components: src/shared/components/ui
   ✅ Design System: src/shared/components/design-system
   ✅ Theme 파일: theme.ts, variants.ts

3. 코드 품질 도구 확인 중...
   ✅ Biome 설치됨
   ✅ Vitest 설치됨

4. TypeScript 설정 확인 중...
   ✅ Strict mode 활성화
   ✅ Path alias: @ -> ./src

5. Node.js 버전: 20.11.0
6. 패키지 매니저: pnpm

규칙 추출 완료!
```

## 구현 방식

이 command는 실제로는 Claude가 다음과 같이 실행합니다:

1. 프로젝트 디렉토리로 이동
2. 위의 bash 명령어들을 순차적으로 실행
3. 결과를 파싱하여 JSON 객체 구성
4. claude-md-generator에 전달

## 에러 처리

- 파일/디렉토리가 없을 경우 null 또는 빈 배열 반환
- 필수 파일(package.json)이 없으면 에러 반환
- 프레임워크를 감지할 수 없으면 에러 반환

## 확장 가능성

새로운 프레임워크 지원 시 다음 섹션 추가:
- 프레임워크별 디렉토리 패턴
- 프레임워크별 설정 파일
- 프레임워크별 의존성 패턴
