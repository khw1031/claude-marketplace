# Commands 사용 가이드

이 디렉토리는 재사용 가능한 command들을 포함합니다. 각 command는 순수 문서 기반으로 작성되어 있으며, Claude가 읽고 해석하여 실행합니다.

## Command 구조

각 command는 다음 구조를 가집니다:

```
commands/
└── <command-name>/
    ├── command.md       # Command 정의 및 실행 지침
    └── templates/       # (선택) 템플릿 파일들
```

## 사용 가능한 Commands

### 1. rules-extractor

**목적**: 프로젝트 디렉토리를 분석하여 실제 설정과 구조에서 규칙을 추출

**입력**:
- `projectPath`: 분석할 프로젝트의 루트 경로
- `framework`: 프레임워크 타입 (`nextjs` | `nestjs` | `vite-react`)

**출력**: `ExtractedRules` 객체 (JSON 형태)

**사용 예시**:
```markdown
프로젝트 규칙을 추출합니다:

1. commands/rules-extractor/command.md를 읽습니다
2. projectPath: /path/to/project
3. framework: nextjs
4. command.md의 "실행 프로세스" 섹션을 순차적으로 실행
5. 결과를 ExtractedRules 형태로 구성
```

### 2. claude-md-generator

**목적**: 프로젝트 규칙을 추출하고 프레임워크별 템플릿과 조합하여 CLAUDE.md 생성

**입력**:
- `projectPath`: 프로젝트 루트 경로
- `framework`: 프레임워크 타입 (`nextjs` | `nestjs` | `vite-react`)

**출력**: 프로젝트 루트에 `CLAUDE.md` 파일 생성

**사용 예시**:
```markdown
CLAUDE.md를 생성합니다:

1. commands/claude-md-generator/command.md를 읽습니다
2. projectPath: /path/to/project
3. framework: nextjs
4. command.md의 "실행 프로세스" 섹션을 순차적으로 실행:
   - Step 1: rules-extractor 호출
   - Step 2: 템플릿 선택
   - Step 3: 변수 매핑
   - Step 4: 템플릿 렌더링
   - Step 5: 파일 생성
```

## Command 실행 방법 (Claude용)

### 기본 실행 흐름

1. **Command 문서 읽기**
   ```markdown
   commands/<command-name>/command.md를 읽습니다.
   ```

2. **입력 파라미터 준비**
   ```markdown
   필요한 입력 파라미터:
   - projectPath: /actual/path
   - framework: nextjs
   ```

3. **실행 프로세스 따르기**
   ```markdown
   command.md의 "실행 프로세스" 섹션을 단계별로 실행:
   
   Step 1: ...
   [bash 명령어 실행]
   
   Step 2: ...
   [결과 처리]
   ```

4. **출력 반환**
   ```markdown
   command.md에 정의된 출력 형식으로 결과 반환
   ```

### 템플릿 렌더링 (Mustache-style)

Claude가 직접 수행하는 간단한 문자열 치환 방법:

#### 1. 변수 삽입 `{{VARIABLE}}`

**입력**:
```markdown
- **프레임워크**: {{FRAMEWORK_NAME}}
```

**변수**:
```json
{ "FRAMEWORK_NAME": "Next.js" }
```

**출력**:
```markdown
- **프레임워크**: Next.js
```

**구현**: `{{VARIABLE}}`를 찾아서 해당 변수 값으로 치환

#### 2. 조건부 블록 `{{#CONDITION}}...{{/CONDITION}}`

**입력**:
```markdown
{{#HAS_NODE_VERSION}}
- **Node.js 버전**: {{NODE_VERSION}}
{{/HAS_NODE_VERSION}}
```

**변수**:
```json
{
  "HAS_NODE_VERSION": true,
  "NODE_VERSION": "20.11.0"
}
```

**출력**:
```markdown
- **Node.js 버전**: 20.11.0
```

**구현**:
- `HAS_NODE_VERSION`이 truthy면 블록 내용 렌더링
- `HAS_NODE_VERSION`이 falsy면 블록 전체 제거

#### 3. 배열 반복 `{{#ARRAY}}...{{/ARRAY}}`

**입력**:
```markdown
Features:
{{#FEATURES}}
- {{FEATURE_NAME}}
{{/FEATURES}}
```

**변수**:
```json
{
  "FEATURES": [
    { "FEATURE_NAME": "auth" },
    { "FEATURE_NAME": "dashboard" }
  ]
}
```

**출력**:
```markdown
Features:
- auth
- dashboard
```

**구현**:
- 배열의 각 요소에 대해 블록 내용 반복
- 각 반복에서 요소의 속성을 변수로 사용

### 렌더링 단계별 가이드

1. **변수 객체 준비**
   ```json
   {
     "FRAMEWORK_NAME": "Next.js",
     "NODE_VERSION": "20.11.0",
     "HAS_NODE_VERSION": true,
     "FEATURES": [...]
   }
   ```

2. **템플릿 읽기**
   ```markdown
   commands/claude-md-generator/templates/nextjs.md를 읽습니다.
   ```

3. **조건부 블록 처리**
   - `{{#CONDITION}}...{{/CONDITION}}` 패턴을 찾습니다
   - CONDITION 변수가 truthy면 블록 유지, falsy면 제거
   - 중첩된 블록은 외부부터 처리

4. **배열 반복 처리**
   - `{{#ARRAY}}...{{/ARRAY}}` 패턴을 찾습니다
   - ARRAY가 배열이면 각 요소로 블록 반복
   - 빈 배열이면 블록 제거

5. **변수 치환**
   - 남은 `{{VARIABLE}}` 패턴을 찾습니다
   - 해당 변수 값으로 치환

6. **최종 출력**
   - 렌더링된 결과를 반환

## 새로운 Command 추가 방법

1. `commands/` 아래에 새 디렉토리 생성
2. `command.md` 작성:
   - 역할
   - 입력/출력
   - 실행 프로세스 (단계별 명령어)
3. 필요시 `templates/` 디렉토리 추가
4. 이 README에 command 추가

## 주의사항

- Command는 순수 문서 기반이므로 명령어는 명확하고 구체적으로 작성
- Bash 명령어는 실제로 실행 가능해야 함
- 템플릿 변수명은 명확하고 충돌하지 않도록 대문자_언더스코어 사용
- 복잡한 로직은 단계별로 나누어 설명

## 예시: rules-extractor 실행

```markdown
규칙을 추출합니다:

1. 📖 commands/rules-extractor/command.md 읽기
2. 🔍 프레임워크 감지
   $ cat package.json | grep "next"
   → Next.js 감지
   
3. 🏗️ 아키텍처 패턴 분석
   $ ls -d src/features/*/ 2>/dev/null
   → Feature-based 아키텍처
   
4. 🎨 디자인 시스템 분석
   $ cat components.json | grep "ui"
   → shadcn/ui 사용
   
5. 🔧 코드 품질 도구 분석
   $ ls biome.json
   → Biome 사용
   
6. 📦 패키지 분석
   $ cat package.json | jq '.dependencies'
   → 의존성 목록 추출
   
7. ✅ ExtractedRules 객체 구성 완료
```

## 예시: claude-md-generator 실행

```markdown
CLAUDE.md를 생성합니다:

1. 📖 commands/claude-md-generator/command.md 읽기

2. 🔍 규칙 추출 (rules-extractor 호출)
   → ExtractedRules 객체 획득

3. 📄 템플릿 선택
   framework: nextjs
   → commands/claude-md-generator/templates/nextjs.md

4. 🗺️ 변수 매핑
   {
     "ARCHITECTURE_PATTERN": "Feature-based",
     "DESIGN_SYSTEM_ENABLED": true,
     ...
   }

5. 🎨 템플릿 렌더링
   - 조건부 블록 처리
   - 배열 반복 처리
   - 변수 치환

6. 💾 파일 생성
   $ cat > CLAUDE.md << 'EOF'
   [렌더링된 내용]
   EOF

7. ✅ CLAUDE.md 생성 완료
```
