# CLAUDE.md Generator Command

프로젝트 규칙을 추출하고 프레임워크별 템플릿과 조합하여 CLAUDE.md를 생성하는 command입니다.

## 역할

1. `rules-extractor` command를 호출하여 프로젝트 규칙 추출
2. 프레임워크에 맞는 템플릿 선택
3. 템플릿에 추출된 규칙을 삽입 (Mustache-style)
4. 프로젝트 루트에 `CLAUDE.md` 파일 생성

## 입력

- **projectPath**: 프로젝트 루트 경로
- **framework**: 프레임워크 타입 (`nextjs` | `nestjs` | `vite-react`)

## 출력

- 프로젝트 루트에 `CLAUDE.md` 파일 생성
- 생성 성공 여부 반환

## 실행 프로세스

### Step 1: 규칙 추출

```markdown
규칙을 추출하는 중...
```

`rules-extractor` command를 호출하여 `ExtractedRules` 객체를 얻습니다.

### Step 2: 템플릿 선택

프레임워크에 따라 템플릿 선택:

- `nextjs` → `templates/nextjs.md`
- `nestjs` → `templates/nestjs.md`
- `vite-react` → `templates/vite-react.md`

### Step 3: 템플릿 변수 매핑

추출된 규칙을 템플릿 변수로 매핑:

```typescript
interface TemplateVariables {
  // 기본 정보
  ARCHITECTURE_PATTERN: string;          // 'Feature-based' | 'Layered' | 'Modular'
  STYLING_FRAMEWORK: string;             // 'Tailwind CSS' | 'styled-components'
  NODE_VERSION: string | null;
  PACKAGE_MANAGER: string;               // 'pnpm' | 'npm' | 'yarn'

  // Feature-based 아키텍처
  FEATURE_BASED: boolean;
  FEATURES: Array<{ FEATURE_NAME: string }>;

  // 디자인 시스템
  DESIGN_SYSTEM_ENABLED: boolean;
  COMPONENT_LIBRARY: string;             // 'shadcn/ui' | ''
  UI_COMPONENTS_PATH: string;            // '@/shared/components/ui'
  DESIGN_SYSTEM_PATH: string;            // '@/shared/components/design-system'
  HAS_THEME_FILES: boolean;
  THEME_FILES: Array<{ THEME_FILE: string }>;
  HAS_THEME_FILE: boolean;               // theme.ts 존재 여부
  THEME_FILE_PATH: string;               // 'shared/styles/theme.ts'
  HAS_VARIANTS: boolean;                 // variants.ts 존재 여부

  // TypeScript
  TYPESCRIPT_ENABLED: boolean;
  STRICT_MODE: string;                   // 'enabled' | 'disabled'
  PATH_ALIASES: boolean;
  ALIASES: Array<{
    ALIAS: string;                       // '@'
    PATH: string;                        // './src'
  }>;

  // 코드 품질
  LINTER: string | null;                 // 'Biome' | 'ESLint' | null
  FORMATTER: string | null;              // 'Biome' | 'Prettier' | null
  TEST_RUNNER: string | null;            // 'Vitest' | 'Jest' | null

  // 스타일링
  TAILWIND: boolean;

  // 의존성
  RUNTIME_DEPS: Array<{
    DEP_NAME: string;
    DEP_VERSION: string;
  }>;
  DEV_DEPS: Array<{
    DEP_NAME: string;
    DEP_VERSION: string;
  }>;
}
```

### Step 4: 템플릿 렌더링

Mustache-style 템플릿 문법:

- `{{VARIABLE}}`: 변수 삽입
- `{{#CONDITION}}...{{/CONDITION}}`: 조건부 블록 (값이 truthy면 렌더링)
- `{{^CONDITION}}...{{/CONDITION}}`: 부정 조건부 블록
- `{{#ARRAY}}...{{/ARRAY}}`: 배열 반복

**예시:**

```markdown
## 프로젝트 정보

- **프레임워크**: Next.js
- **아키텍처**: {{ARCHITECTURE_PATTERN}}
{{#NODE_VERSION}}
- **Node.js 버전**: {{NODE_VERSION}}
{{/NODE_VERSION}}
```

추출된 규칙이 `{ ARCHITECTURE_PATTERN: 'Feature-based', NODE_VERSION: '20.11.0' }`이면:

```markdown
## 프로젝트 정보

- **프레임워크**: Next.js
- **아키텍처**: Feature-based
- **Node.js 버전**: 20.11.0
```

### Step 5: 파일 생성

렌더링된 내용을 `CLAUDE.md`에 작성:

```bash
echo "렌더링된 내용" > CLAUDE.md
```

## 규칙 매핑 로직

### 아키텍처 패턴 결정

```typescript
function determineArchitecturePattern(rules: ExtractedRules): string {
  if (rules.architecture.pattern === 'feature-based') {
    return 'Feature-based';
  } else if (rules.architecture.pattern === 'layered') {
    return 'Layered';
  } else if (rules.architecture.pattern === 'modular') {
    return 'Modular';
  }
  return 'Custom';
}
```

### 디자인 시스템 관련

```typescript
function mapDesignSystem(rules: ExtractedRules): Partial<TemplateVariables> {
  return {
    DESIGN_SYSTEM_ENABLED: rules.designSystem.enabled,
    COMPONENT_LIBRARY: rules.designSystem.componentLibrary || '',
    UI_COMPONENTS_PATH: rules.designSystem.uiComponentsPath || '',
    DESIGN_SYSTEM_PATH: rules.designSystem.designSystemPath || '',
    HAS_THEME_FILES: rules.designSystem.themeFiles.length > 0,
    THEME_FILES: rules.designSystem.themeFiles.map(f => ({ THEME_FILE: f })),
    HAS_THEME_FILE: rules.designSystem.themeFiles.includes('theme.ts'),
    THEME_FILE_PATH: rules.designSystem.themeFiles.find(f => f.includes('theme'))
      ? `shared/styles/theme.ts`
      : '',
    HAS_VARIANTS: rules.designSystem.hasVariants,
  };
}
```

### Features 목록

```typescript
function mapFeatures(rules: ExtractedRules): Array<{ FEATURE_NAME: string }> {
  return rules.architecture.features.map(name => ({
    FEATURE_NAME: name,
  }));
}
```

### 의존성 목록

```typescript
function mapDependencies(rules: ExtractedRules): {
  RUNTIME_DEPS: Array<{ DEP_NAME: string; DEP_VERSION: string }>;
  DEV_DEPS: Array<{ DEP_NAME: string; DEP_VERSION: string }>;
} {
  return {
    RUNTIME_DEPS: Object.entries(rules.dependencies.runtime).map(
      ([name, version]) => ({ DEP_NAME: name, DEP_VERSION: version })
    ),
    DEV_DEPS: Object.entries(rules.dependencies.dev).map(
      ([name, version]) => ({ DEP_NAME: name, DEP_VERSION: version })
    ),
  };
}
```

## 사용 예시

```markdown
CLAUDE.md를 생성하는 중...

1. ✅ 프로젝트 규칙 추출 완료
2. ✅ 템플릿 선택: nextjs.md
3. ✅ 변수 매핑 완료
   - 아키텍처: Feature-based
   - 디자인 시스템: shadcn/ui 사용
   - 코드 품질: Biome + Vitest
4. ✅ 템플릿 렌더링 완료
5. ✅ CLAUDE.md 파일 생성 완료

생성된 파일: /project-root/CLAUDE.md
```

## 구현 방식

이 command는 실제로는 Claude가 다음과 같이 실행합니다:

1. `rules-extractor` command 실행 결과를 받음
2. 프레임워크에 맞는 템플릿 파일 읽기
3. 템플릿 변수 매핑 로직 실행
4. Mustache-style 렌더링 수행 (간단한 문자열 치환)
5. 결과를 `CLAUDE.md`에 작성

## 템플릿 문법 (Mustache-style)

### 변수 삽입

```markdown
{{VARIABLE_NAME}}
```

### 조건부 블록

```markdown
{{#CONDITION}}
이 블록은 CONDITION이 truthy일 때만 렌더링됩니다.
{{/CONDITION}}
```

### 부정 조건부 블록

```markdown
{{^CONDITION}}
이 블록은 CONDITION이 falsy일 때만 렌더링됩니다.
{{/CONDITION}}
```

### 배열 반복

```markdown
{{#ARRAY}}
- {{ITEM_PROPERTY}}
{{/ARRAY}}
```

**예시:**

```markdown
Features:
{{#FEATURES}}
- {{FEATURE_NAME}}
{{/FEATURES}}
```

Input: `{ FEATURES: [{ FEATURE_NAME: 'auth' }, { FEATURE_NAME: 'dashboard' }] }`

Output:
```markdown
Features:
- auth
- dashboard
```

## 에러 처리

- 규칙 추출 실패 시 에러 반환
- 템플릿 파일이 없으면 에러 반환
- 파일 쓰기 권한이 없으면 에러 반환

## 확장 가능성

### 새로운 프레임워크 추가

1. `templates/` 디렉토리에 새 템플릿 추가
2. 템플릿에 필요한 변수 정의
3. 매핑 로직에 프레임워크별 처리 추가

### 템플릿 커스터마이징

사용자가 자신만의 템플릿을 제공할 수 있도록 확장 가능:

```markdown
# 커스텀 템플릿 사용
customTemplatePath를 입력으로 받아 해당 템플릿 사용
```

## 참고사항

- 템플릿 렌더링은 서버가 아닌 Claude가 직접 수행 (문자열 치환)
- 복잡한 로직은 매핑 단계에서 처리
- 템플릿은 가독성을 위해 Markdown 형식 유지
