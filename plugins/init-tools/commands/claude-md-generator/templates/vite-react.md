# 프로젝트 개발 가이드

이 문서는 AI 어시스턴트가 프로젝트의 구조와 규칙을 이해하도록 돕습니다.

## 프로젝트 정보

- **프레임워크**: Vite + React
- **아키텍처**: {{ARCHITECTURE_PATTERN}}
- **스타일링**: {{STYLING_FRAMEWORK}}
{{#NODE_VERSION}}
- **Node.js 버전**: {{NODE_VERSION}}
{{/NODE_VERSION}}
- **패키지 매니저**: {{PACKAGE_MANAGER}}

## 아키텍처

{{#FEATURE_BASED}}
### Feature-based 구조

프로젝트는 Feature-based 아키텍처를 사용합니다:

```
src/
├── main.tsx                # 애플리케이션 진입점
├── App.tsx                 # 루트 컴포넌트
├── features/               # 기능별 모듈
{{#FEATURES}}
│   ├── {{FEATURE_NAME}}/
{{/FEATURES}}
└── shared/                 # 공유 리소스
    ├── components/
{{#DESIGN_SYSTEM_ENABLED}}
    │   ├── ui/            # {{COMPONENT_LIBRARY}} (수정 금지)
    │   └── design-system/ # 자체 디자인 시스템
{{/DESIGN_SYSTEM_ENABLED}}
    ├── hooks/
    ├── lib/
    ├── types/
{{#HAS_THEME_FILES}}
    ├── styles/            # 테마 및 스타일
{{#THEME_FILES}}
    │   ├── {{THEME_FILE}}
{{/THEME_FILES}}
{{/HAS_THEME_FILES}}
    └── utils/
```

### Feature 규칙

1. **높은 응집도, 낮은 결합도**
   - 각 feature는 자체 완결성을 유지합니다
   - feature 간 직접 import 금지

2. **명시적 API 노출**
   - `index.ts`를 통해 외부에 노출할 항목만 export
   - 내부 구현 세부사항은 캡슐화

3. **공유 규칙**
   - feature 간 공유는 `shared/`를 통해서만
   - 3회 이상 재사용되는 코드는 `shared/`로 이동 고려
{{/FEATURE_BASED}}

{{#DESIGN_SYSTEM_ENABLED}}
## 디자인 시스템 규칙

프로젝트는 **자체 디자인 시스템**을 구축하고 **theme 기반**으로 개발합니다.

### 절대 금지 ❌

1. **{{COMPONENT_LIBRARY}} 원본 파일 직접 수정 금지**
   - `{{UI_COMPONENTS_PATH}}/` 내 파일 수정 불가
   
2. **{{COMPONENT_LIBRARY}} 컴포넌트 직접 사용 금지**
   - 애플리케이션 코드에서 `{{UI_COMPONENTS_PATH}}/` import 금지
   
3. **하드코딩된 스타일 값 사용 금지**
   - ❌ `text-blue-500`, `p-4`, `#3b82f6` 등
   - ✅ theme 기반 값 사용

### 필수 준수 ✅

#### 새로운 UI 컴포넌트 추가 시

1. `{{UI_COMPONENTS_PATH}}/`에서 적절한 {{COMPONENT_LIBRARY}} 컴포넌트 선택
2. `{{DESIGN_SYSTEM_PATH}}/`에 래퍼 컴포넌트 생성
{{#HAS_THEME_FILE}}
3. `{{THEME_FILE_PATH}}`의 디자인 토큰 활용
{{/HAS_THEME_FILE}}
{{#HAS_VARIANTS}}
4. variant가 필요하면 `class-variance-authority` (cva) 사용
{{/HAS_VARIANTS}}

#### 예시

```typescript
// ❌ 잘못된 사용
import { Button } from '@/shared/components/ui/button';

function MyComponent() {
  return <Button className="bg-blue-500 px-4">Click</Button>;
}

// ✅ 올바른 사용
import { PrimaryButton } from '@/shared/components/design-system';

function MyComponent() {
  return <PrimaryButton>Click</PrimaryButton>;
}
```
{{/DESIGN_SYSTEM_ENABLED}}

{{#ROUTING}}
## 라우팅

### React Router 규칙

1. **라우트 정의**
   ```typescript
   // src/routes/index.tsx
   import { createBrowserRouter } from 'react-router-dom';
   
   export const router = createBrowserRouter([
     {
       path: '/',
       element: <RootLayout />,
       children: [
         { path: '', element: <Home /> },
         { path: 'about', element: <About /> },
       ],
     },
   ]);
   ```

2. **네비게이션**
   - `<Link>` 컴포넌트 사용
   - `useNavigate` hook으로 프로그래매틱 네비게이션
{{/ROUTING}}

{{#STATE_MANAGEMENT}}
## 상태 관리

### {{STATE_LIBRARY}}

{{#ZUSTAND}}
#### Zustand 규칙

1. **Store 정의**
   ```typescript
   import { create } from 'zustand';
   
   interface UserStore {
     user: User | null;
     setUser: (user: User) => void;
   }
   
   export const useUserStore = create<UserStore>((set) => ({
     user: null,
     setUser: (user) => set({ user }),
   }));
   ```

2. **사용**
   ```typescript
   function Profile() {
     const user = useUserStore((state) => state.user);
     return <div>{user?.name}</div>;
   }
   ```
{{/ZUSTAND}}

{{#REDUX}}
#### Redux Toolkit 규칙

1. **Slice 정의**
   ```typescript
   import { createSlice } from '@reduxjs/toolkit';
   
   const userSlice = createSlice({
     name: 'user',
     initialState: { user: null },
     reducers: {
       setUser: (state, action) => {
         state.user = action.payload;
       },
     },
   });
   ```
{{/REDUX}}
{{/STATE_MANAGEMENT}}

## TypeScript

{{#TYPESCRIPT_ENABLED}}
- **Strict mode**: {{STRICT_MODE}}
{{#PATH_ALIASES}}
- **Path aliases**: 
{{#ALIASES}}
  - `{{ALIAS}}` → `{{PATH}}`
{{/ALIASES}}
{{/PATH_ALIASES}}

### 타입 안전성

- 모든 컴포넌트에 명시적 타입 지정
- `any` 사용 금지 (불가피한 경우 `unknown` 사용 후 타입 가드)
- Props interface는 component 파일 내에 정의
{{/TYPESCRIPT_ENABLED}}

## 코드 품질

{{#LINTER}}
### Linting

- **도구**: {{LINTER}}
- 커밋 전 반드시 lint 검사 통과
{{/LINTER}}

{{#FORMATTER}}
### Formatting

- **도구**: {{FORMATTER}}
- 일관된 코드 스타일 유지
{{/FORMATTER}}

{{#TEST_RUNNER}}
### 테스팅

- **도구**: {{TEST_RUNNER}}
- 새로운 컴포넌트/기능 추가 시 테스트 작성
- 테스트 파일은 colocated 방식 권장 (`*.test.tsx`)
{{/TEST_RUNNER}}

## 스타일링

{{#TAILWIND}}
### Tailwind CSS

- utility-first 방식 사용
{{#HAS_THEME_FILE}}
- 하드코딩된 색상 대신 theme의 CSS 변수 사용
- `theme.ts`에 정의된 디자인 토큰 활용
{{/HAS_THEME_FILE}}
{{/TAILWIND}}

## 개발 워크플로우

### 필수 스크립트

```bash
# 개발 서버 실행
{{PACKAGE_MANAGER}} dev

# 프로덕션 빌드
{{PACKAGE_MANAGER}} build

# 빌드 미리보기
{{PACKAGE_MANAGER}} preview

{{#LINTER}}
# 린트 검사
{{PACKAGE_MANAGER}} lint

# 린트 자동 수정
{{PACKAGE_MANAGER}} lint:fix
{{/LINTER}}

{{#FORMATTER}}
# 포맷 검사
{{PACKAGE_MANAGER}} format:check

# 포맷 적용
{{PACKAGE_MANAGER}} format
{{/FORMATTER}}

{{#TEST_RUNNER}}
# 테스트 실행
{{PACKAGE_MANAGER}} test

# 테스트 실행 (watch 모드 없음)
{{PACKAGE_MANAGER}} test:run
{{/TEST_RUNNER}}
```

### 커밋 전 체크리스트

- [ ] Lint 검사 통과
- [ ] Format 검사 통과
{{#TEST_RUNNER}}
- [ ] 테스트 통과
{{/TEST_RUNNER}}
- [ ] 빌드 성공

## 주요 의존성

### Runtime

{{#RUNTIME_DEPS}}
- `{{DEP_NAME}}`: {{DEP_VERSION}}
{{/RUNTIME_DEPS}}

### Development

{{#DEV_DEPS}}
- `{{DEP_NAME}}`: {{DEP_VERSION}}
{{/DEV_DEPS}}

## 성능 최적화

### 권장사항

1. **Code Splitting**
   - 라우트 단위로 lazy loading
   ```typescript
   const Home = lazy(() => import('./features/home'));
   ```

2. **Memoization**
   - 비용이 큰 계산은 `useMemo` 사용
   - 콜백 함수는 `useCallback` 사용

3. **번들 최적화**
   - Vite의 자동 code splitting 활용
   - 큰 라이브러리는 dynamic import

## 참고사항

- 이 문서는 프로젝트 초기화 시 자동 생성되었습니다
- 프로젝트 구조나 규칙이 변경되면 이 문서도 함께 업데이트하세요
- AI 어시스턴트는 이 문서를 기반으로 프로젝트를 이해하고 코드를 생성합니다
