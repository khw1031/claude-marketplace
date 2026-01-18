---
name: nestjs-project-initializer
description: "NestJS 프로젝트를 실제로 생성하고 초기화하는 실행 단계. '프로젝트 만들어줘', '초기화해줘' 같은 실행 요청 시 자동 활성화"
---

# NestJS 프로젝트 초기화 실행

## 자동 활성화 시점
- "NestJS 프로젝트 만들어줘"
- "프로젝트 초기화해줘"
- "새 프로젝트 설정해줘"
- 실제 프로젝트 생성 실행 요청 시

---

## 다중 전문가 시뮬레이션

다음 전문가들의 관점에서 프로젝트 초기화를 수행합니다:
- **Backend Architect**: 아키텍처 설계 및 모듈 구조 결정
- **DevOps Engineer**: 빌드 도구 및 배포 설정
- **DX Specialist**: 개발자 경험 최적화

---

## 사전 탐색 단계 (필수)

프로젝트 설정 전 **웹 검색**으로 다음 항목의 최신 정보를 확인:

1. ✅ **NestJS 최신 stable 버전** 및 주요 변경사항
2. ✅ **선택한 도구들의 최신 버전** 및 NestJS 호환성
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

# 4. NestJS CLI 설치 확인
npm install -g @nestjs/cli
```

### Phase 2: 프로젝트 생성

```bash
# NestJS CLI로 프로젝트 scaffolding
nest new <project-name>
```

**필수 선택 옵션:**
- 패키지 매니저: **pnpm** 선택

### Phase 3: 디렉토리 구조 설정

Modular 구조로 필요한 디렉토리 생성:

```bash
cd <project-name>

# 기본 구조 생성
mkdir -p src/modules
mkdir -p src/shared/{decorators,filters,interceptors,pipes,guards,utils,types}
mkdir -p src/config
mkdir -p test

# 예시 module 생성 (auth)
nest generate module modules/auth
nest generate controller modules/auth
nest generate service modules/auth
mkdir -p src/modules/auth/{dto,entities,guards,strategies}
```

### Phase 4: 추가 도구 설치 및 구성

#### 1. 필수 패키지 설치

```bash
# 런타임 의존성
pnpm add @nestjs/config class-validator class-transformer

# 개발 의존성 (테스트 도구는 기본 포함)
# Jest는 NestJS 기본 설치에 포함
```

#### 2. 데이터베이스 및 ORM 설치 (사용자에게 선택 요청)

**옵션 A: TypeORM + PostgreSQL**
```bash
pnpm add @nestjs/typeorm typeorm pg
```

**옵션 B: Prisma**
```bash
pnpm add @prisma/client
pnpm add -D prisma

# Prisma 초기화
npx prisma init
```

**옵션 C: Mongoose (MongoDB)**
```bash
pnpm add @nestjs/mongoose mongoose
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
    "ignore": ["dist", "node_modules"]
  },
  "formatter": {
    "enabled": true,
    "indentStyle": "space",
    "indentWidth": 2
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

**옵션 B: ESLint + Prettier (이미 설치됨)**
```bash
# NestJS는 기본적으로 ESLint 포함
# Prettier 추가
pnpm add -D prettier eslint-config-prettier

# .prettierrc 생성
echo '{"semi": true, "singleQuote": true, "trailingComma": "all"}' > .prettierrc
```

#### 4. 환경 설정

`.env` 파일 생성:
```bash
cat > .env << 'EOF'
# Application
NODE_ENV=development
PORT=3000

# Database (TypeORM 예시)
DB_HOST=localhost
DB_PORT=5432
DB_USERNAME=postgres
DB_PASSWORD=postgres
DB_NAME=myapp

# JWT (인증이 필요한 경우)
JWT_SECRET=your-secret-key
JWT_EXPIRES_IN=1d
EOF
```

`.env.example` 생성 (git에 커밋용):
```bash
cat > .env.example << 'EOF'
NODE_ENV=development
PORT=3000
DB_HOST=localhost
DB_PORT=5432
DB_USERNAME=
DB_PASSWORD=
DB_NAME=
JWT_SECRET=
JWT_EXPIRES_IN=1d
EOF
```

#### 5. ConfigModule 설정

`src/app.module.ts` 수정:
```typescript
import { Module } from '@nestjs/common';
import { ConfigModule } from '@nestjs/config';

@Module({
  imports: [
    ConfigModule.forRoot({
      isGlobal: true,
      envFilePath: `.env.${process.env.NODE_ENV || 'development'}`,
    }),
  ],
})
export class AppModule {}
```

#### 6. ValidationPipe 전역 설정

`src/main.ts` 수정:
```typescript
import { NestFactory } from '@nestjs/core';
import { ValidationPipe } from '@nestjs/common';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  
  // Global ValidationPipe
  app.useGlobalPipes(
    new ValidationPipe({
      whitelist: true,
      forbidNonWhitelisted: true,
      transform: true,
    }),
  );

  await app.listen(process.env.PORT || 3000);
}
bootstrap();
```

#### 7. package.json 스크립트 업데이트

```json
{
  "scripts": {
    "start:dev": "nest start --watch",
    "start:debug": "nest start --debug --watch",
    "start:prod": "node dist/main",
    "build": "nest build",
    "lint": "biome check .",
    "lint:fix": "biome check --write .",
    "format": "biome format --write .",
    "format:check": "biome format .",
    "test": "jest",
    "test:watch": "jest --watch",
    "test:cov": "jest --coverage",
    "test:e2e": "jest --config ./test/jest-e2e.json"
  }
}
```

**ESLint+Prettier 사용 시:**
```json
{
  "scripts": {
    "lint": "eslint \"{src,apps,libs,test}/**/*.ts\"",
    "lint:fix": "eslint \"{src,apps,libs,test}/**/*.ts\" --fix",
    "format": "prettier --write \"src/**/*.ts\" \"test/**/*.ts\"",
    "format:check": "prettier --check \"src/**/*.ts\" \"test/**/*.ts\""
  }
}
```

#### 8. Node.js 버전 고정

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

#### 9. .gitignore 업데이트

```bash
cat >> .gitignore << 'EOF'

# Environment
.env
.env.local
.env.*.local

# Database
*.db
*.sqlite

# Prisma (if using)
prisma/migrations/
EOF
```

#### 10. CLAUDE.md 생성

**commands/claude-md-generator 사용**:

```markdown
CLAUDE.md를 생성하는 중...

이 단계에서는 `claude-md-generator` command를 실행합니다:

1. 프로젝트 규칙 추출 (`rules-extractor` 자동 호출)
2. NestJS 템플릿 적용
3. 프로젝트 루트에 CLAUDE.md 생성

실행 방법:
- commands/claude-md-generator/command.md의 지침 따름
- framework: 'nestjs'
- projectPath: 현재 프로젝트 경로
```

**실행 흐름**:

1. `commands/rules-extractor/command.md` 읽기
2. 규칙 추출 프로세스 실행 (bash 명령어들)
3. `commands/claude-md-generator/command.md` 읽기
4. 템플릿 렌더링 및 CLAUDE.md 생성

### Phase 5: 검증

모든 설정 완료 후 다음 명령어로 정상 작동 확인:

```bash
# 1. 린트 검사
pnpm lint

# 2. 포맷 검사
pnpm format:check

# 3. 테스트 실행
pnpm test

# 4. 빌드 테스트
pnpm build

# 5. 개발 서버 실행
pnpm start:dev
```

---

## 실행 체크리스트

각 Phase 완료 시 확인:

- [ ] Phase 1: Node.js, pnpm, NestJS CLI 버전 확인 완료
- [ ] Phase 2: NestJS 프로젝트 생성 완료
- [ ] Phase 3: Modular 디렉토리 구조 생성 완료
- [ ] Phase 4-1: 필수 패키지 설치 완료 (config, class-validator)
- [ ] Phase 4-2: 데이터베이스 및 ORM 설치 완료
- [ ] Phase 4-3: 코드 품질 도구 설치 및 설정 완료
- [ ] Phase 4-4: 환경 설정 파일 생성 완료 (.env, .env.example)
- [ ] Phase 4-5: ConfigModule 설정 완료
- [ ] Phase 4-6: ValidationPipe 전역 설정 완료
- [ ] Phase 4-7: package.json 스크립트 업데이트 완료
- [ ] Phase 4-8: Node.js 버전 고정 파일 생성 완료
- [ ] Phase 4-9: .gitignore 업데이트 완료
- [ ] Phase 4-10: CLAUDE.md 생성 완료
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
4. ✅ 다음 단계 권장사항 (예: 인증 모듈 추가, API 문서 설정 등)

---

## 참고사항

초기화 완료 후 검증이 필요하면 자연스럽게 대화를 이어가세요. 별도의 검증 skill이 자동으로 활성화될 수 있습니다.
