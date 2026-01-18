---
name: project-setup-validator
description: "프로젝트 초기화 완료 후 설정을 검증하고 체크리스트를 확인. '검증해줘', '제대로 설정됐나?', '확인해줘' 같은 요청 시 자동 활성화"
---

# 프로젝트 설정 검증기

## 자동 활성화 시점
- "프로젝트 설정 검증해줘"
- "제대로 설정됐나?"
- "빠진 거 없나 확인해줘"
- 초기화 후 검증 요청 시

---

## 검증 프로세스

### 1. 파일 구조 검증

다음 파일과 디렉토리가 존재하는지 확인:

```bash
# 필수 파일
ls -la package.json
ls -la next.config.js
ls -la tsconfig.json
ls -la tailwind.config.ts
ls -la .nvmrc  # 또는 .node-version
ls -la CLAUDE.md

# 필수 디렉토리
ls -d src/app
ls -d src/features
ls -d src/shared/components/ui
ls -d src/shared/components/design-system
ls -d src/shared/styles
ls -d src/shared/hooks
ls -d src/shared/lib
ls -d src/shared/types
ls -d src/shared/config

# 테마 파일
ls -la src/shared/styles/theme.ts
ls -la src/shared/styles/variants.ts

# 설정 파일
ls -la vitest.config.ts
ls -la vitest.setup.ts
ls -la biome.json  # 또는 .eslintrc + .prettierrc
```

### 2. 패키지 검증

`package.json` 확인:

```bash
# 필수 의존성 확인
cat package.json | grep -E "next|react|typescript|tailwindcss|zod"

# 개발 의존성 확인
cat package.json | grep -E "vitest|@testing-library|@biomejs/biome"

# shadcn/ui 관련 확인
cat package.json | grep -E "class-variance-authority|clsx|tailwind-merge"

# engines 필드 확인
cat package.json | grep -A 3 "engines"
```

### 3. 스크립트 검증

`package.json`에 필수 스크립트가 있는지 확인:

**필수 스크립트:**
- ✅ `dev`: 개발 서버 실행
- ✅ `build`: 프로덕션 빌드
- ✅ `lint`: 코드 린팅
- ✅ `format` / `format:check`: 코드 포맷팅
- ✅ `test`: 테스트 실행
- ✅ `test:run`: 단일 테스트 실행
- ✅ `test:coverage`: 커버리지 포함 테스트

### 4. 설정 파일 검증

#### TypeScript 설정 확인
```bash
cat tsconfig.json | grep -E "strict|paths"
```

**확인 사항:**
- `strict: true` 설정 확인
- `paths` 별칭 설정 (`@/*` → `./src/*`)

#### Tailwind 설정 확인
```bash
cat tailwind.config.ts | grep -E "content|theme"
```

**확인 사항:**
- content 경로에 `./src/**/*.{js,ts,jsx,tsx,mdx}` 포함
- theme 확장 설정

#### shadcn/ui 설정 확인
```bash
cat components.json 2>/dev/null || echo "components.json not found"
```

**확인 사항:**
- `tsx: true`
- `tailwind.cssVariables: true`
- `aliases.components`: `@/shared/components/ui`

### 5. 실행 검증

각 명령어를 실제로 실행하여 정상 작동 확인:

```bash
# 1. 린트 검사
echo "Running lint check..."
pnpm lint
if [ $? -eq 0 ]; then
  echo "✅ Lint check passed"
else
  echo "❌ Lint check failed"
fi

# 2. 포맷 검사
echo "Running format check..."
pnpm format:check
if [ $? -eq 0 ]; then
  echo "✅ Format check passed"
else
  echo "❌ Format check failed"
fi

# 3. 빌드 테스트
echo "Running build..."
pnpm build
if [ $? -eq 0 ]; then
  echo "✅ Build succeeded"
else
  echo "❌ Build failed"
fi

# 4. 테스트 실행 (테스트 파일이 있는 경우)
echo "Running tests..."
pnpm test:run
if [ $? -eq 0 ]; then
  echo "✅ Tests passed"
else
  echo "⚠️ Tests failed or no tests found"
fi
```

### 6. Git 설정 확인

```bash
# .gitignore 확인
cat .gitignore | grep -E "node_modules|.next|dist|.env"

# Git 저장소 초기화 여부
if [ -d .git ]; then
  echo "✅ Git repository initialized"
else
  echo "⚠️ Git repository not initialized"
fi
```

---

## 검증 체크리스트

### 📁 파일 구조
- [ ] `src/` 디렉토리 존재
- [ ] `src/app/` (App Router)
- [ ] `src/features/` (Feature-based 구조)
- [ ] `src/shared/components/ui/` (shadcn/ui)
- [ ] `src/shared/components/design-system/` (디자인 시스템)
- [ ] `src/shared/styles/theme.ts` (테마 정의)
- [ ] `src/shared/styles/variants.ts` (variant 패턴)

### 📦 패키지
- [ ] Next.js 설치 완료
- [ ] TypeScript 설치 완료
- [ ] Tailwind CSS 설치 완료
- [ ] shadcn/ui 관련 패키지 설치 완료
- [ ] Zod 설치 완료
- [ ] Vitest + Testing Library 설치 완료
- [ ] 코드 품질 도구 설치 완료 (Biome 또는 ESLint+Prettier)

### ⚙️ 설정
- [ ] TypeScript strict mode 활성화
- [ ] Path alias 설정 (`@/*`)
- [ ] Node.js 버전 고정 (`.nvmrc` 또는 `.node-version`)
- [ ] `package.json` engines 필드 존재
- [ ] 필수 스크립트 모두 존재

### 📄 문서
- [ ] `CLAUDE.md` 존재
- [ ] `README.md` 존재 (프로젝트 설명)

### ✅ 실행 검증
- [ ] `pnpm lint` 성공
- [ ] `pnpm format:check` 성공
- [ ] `pnpm build` 성공
- [ ] `pnpm dev` 실행 가능 (포트 확인)

---

## 검증 보고서 형식

검증 완료 후 다음 형식으로 결과 보고:

```markdown
## 🔍 프로젝트 설정 검증 결과

### ✅ 통과 항목 (X/Y)
- ✅ 항목 1
- ✅ 항목 2
...

### ⚠️ 경고 항목 (있는 경우)
- ⚠️ 항목 1: 설명 및 해결 방법
...

### ❌ 실패 항목 (있는 경우)
- ❌ 항목 1: 설명 및 해결 방법
...

### 📊 통계
- 전체 체크 항목: Y개
- 통과: X개
- 경고: Z개
- 실패: W개

### 🎯 다음 단계
[프로젝트가 정상적으로 설정되었다면]
1. 첫 번째 feature 개발 시작
2. 디자인 시스템 컴포넌트 생성
3. 테스트 작성

[문제가 있다면]
1. 실패 항목 해결
2. 재검증 수행
```

---

## 자동 수정 제안

검증 중 발견된 문제에 대해 자동 수정 제안:

### 누락된 파일
```bash
# 테마 파일이 없는 경우
touch src/shared/styles/theme.ts
touch src/shared/styles/variants.ts
```

### 누락된 스크립트
사용자에게 `package.json`에 추가할 스크립트 제시

### 설정 오류
잘못된 설정 파일 내용을 올바르게 수정하는 방법 안내

---

## 참고사항

- 이 검증은 초기 설정이 완료된 후 수행합니다
- 모든 항목이 통과하지 않아도 프로젝트 개발은 가능하지만, 권장 사항을 따르는 것이 좋습니다
- 검증 실패 항목은 우선순위를 정하여 단계적으로 해결할 수 있습니다
