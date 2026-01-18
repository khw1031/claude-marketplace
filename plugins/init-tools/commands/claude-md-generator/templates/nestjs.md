# 프로젝트 개발 가이드

이 문서는 AI 어시스턴트가 프로젝트의 구조와 규칙을 이해하도록 돕습니다.

## 프로젝트 정보

- **프레임워크**: NestJS
- **아키텍처**: {{ARCHITECTURE_PATTERN}}
{{#NODE_VERSION}}
- **Node.js 버전**: {{NODE_VERSION}}
{{/NODE_VERSION}}
- **패키지 매니저**: {{PACKAGE_MANAGER}}
{{#DATABASE}}
- **데이터베이스**: {{DATABASE_TYPE}}
- **ORM**: {{ORM_TYPE}}
{{/DATABASE}}

## 아키텍처

{{#MODULAR}}
### Modular 구조

프로젝트는 Modular 아키텍처를 사용합니다:

```
src/
├── main.ts                 # 애플리케이션 진입점
├── app.module.ts           # 루트 모듈
├── modules/                # 기능별 모듈
{{#MODULES}}
│   ├── {{MODULE_NAME}}/
{{/MODULES}}
└── shared/                 # 공유 리소스
    ├── decorators/
    ├── filters/
    ├── interceptors/
    ├── pipes/
    ├── guards/
    ├── utils/
    └── types/
```

### 모듈 규칙

1. **모듈 중심 설계**
   - 각 기능은 독립적인 모듈로 구성
   - 모듈은 자체 완결성을 유지
   - 모듈 간 의존성은 imports를 통해 명시

2. **계층 분리**
   - **Controller**: HTTP 요청 처리만
   - **Service**: 비즈니스 로직 구현
   - **Repository/Entity**: 데이터 접근

3. **의존성 주입**
   - NestJS DI 컨테이너 활용
   - Interface 기반 설계 권장
   - Provider를 통한 느슨한 결합

4. **공유 규칙**
   - 모듈 간 공유는 `shared/`를 통해서만
   - 공통 기능은 독립 모듈로 분리
{{/MODULAR}}

## DTO 및 검증

### 필수 준수 ✅

1. **모든 외부 입력은 DTO로 검증**
   ```typescript
   import { IsString, IsEmail, MinLength } from 'class-validator';
   
   export class CreateUserDto {
     @IsEmail()
     email: string;
   
     @IsString()
     @MinLength(8)
     password: string;
   }
   ```

2. **ValidationPipe 전역 설정**
   - `whitelist: true` - DTO에 없는 속성 제거
   - `forbidNonWhitelisted: true` - 허용되지 않은 속성 있으면 에러
   - `transform: true` - 타입 자동 변환

### 절대 금지 ❌

- DTO 없이 컨트롤러 파라미터 받기
- `@Body()` 없이 request.body 직접 사용
- 검증 없이 데이터베이스에 저장

## 예외 처리

### 필수 준수 ✅

1. **NestJS 표준 예외 사용**
   ```typescript
   import { NotFoundException, BadRequestException } from '@nestjs/common';
   
   async findOne(id: string): Promise<User> {
     const user = await this.usersRepository.findOne(id);
     if (!user) {
       throw new NotFoundException(`User with ID ${id} not found`);
     }
     return user;
   }
   ```

2. **일관된 에러 응답 형식**
   ```json
   {
     "statusCode": 404,
     "timestamp": "2024-01-01T00:00:00.000Z",
     "message": "User with ID 123 not found"
   }
   ```

### 절대 금지 ❌

- 일반 Error throw (NestJS 예외 사용 필수)
- try-catch 없이 Promise 방치
- 에러 메시지에 민감한 정보 포함

{{#DATABASE}}
## 데이터베이스

### ORM: {{ORM_TYPE}}

{{#TYPEORM}}
#### TypeORM 규칙

1. **Entity 작성**
   ```typescript
   @Entity()
   export class User {
     @PrimaryGeneratedColumn('uuid')
     id: string;
   
     @Column({ unique: true })
     email: string;
   
     @CreateDateColumn()
     createdAt: Date;
   }
   ```

2. **Repository 사용**
   - `@InjectRepository()` 데코레이터 사용
   - 비즈니스 로직은 Service에서만
{{/TYPEORM}}

{{#PRISMA}}
#### Prisma 규칙

1. **Schema 작성**
   ```prisma
   model User {
     id        String   @id @default(uuid())
     email     String   @unique
     createdAt DateTime @default(now())
   }
   ```

2. **PrismaService 사용**
   - 모든 쿼리는 PrismaService를 통해서만
   - 트랜잭션이 필요한 경우 `$transaction` 사용
{{/PRISMA}}
{{/DATABASE}}

## 환경 설정

### 필수 준수 ✅

1. **@nestjs/config 사용**
   ```typescript
   @Module({
     imports: [
       ConfigModule.forRoot({
         isGlobal: true,
         envFilePath: `.env.${process.env.NODE_ENV}`,
       }),
     ],
   })
   ```

2. **환경 변수 접근**
   ```typescript
   constructor(private configService: ConfigService) {}
   
   getDatabaseHost(): string {
     return this.configService.get<string>('DB_HOST');
   }
   ```

### 절대 금지 ❌

- `process.env` 직접 사용
- 하드코딩된 설정 값
- .env 파일을 git에 커밋

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

- 모든 Service 메서드에 명시적 반환 타입 지정
- `any` 사용 금지 (불가피한 경우 `unknown` 사용 후 타입 가드)
- DTO, Entity 모두 명시적 타입 지정
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
- **단위 테스트**: 모든 Service에 대해 작성 (`*.spec.ts`)
- **E2E 테스트**: 주요 API 엔드포인트에 대해 작성 (`*.e2e-spec.ts`)

#### 테스트 작성 규칙

```typescript
// 단위 테스트 (users.service.spec.ts)
describe('UsersService', () => {
  let service: UsersService;
  let repository: Repository<User>;

  beforeEach(async () => {
    const module = await Test.createTestingModule({
      providers: [
        UsersService,
        {
          provide: getRepositoryToken(User),
          useValue: mockRepository,
        },
      ],
    }).compile();

    service = module.get<UsersService>(UsersService);
  });

  it('should find a user by id', async () => {
    // 테스트 구현
  });
});
```
{{/TEST_RUNNER}}

## 개발 워크플로우

### 필수 스크립트

```bash
# 개발 서버 실행
{{PACKAGE_MANAGER}} start:dev

# 프로덕션 빌드
{{PACKAGE_MANAGER}} build

# 프로덕션 실행
{{PACKAGE_MANAGER}} start:prod

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
# 단위 테스트 실행
{{PACKAGE_MANAGER}} test

# 테스트 watch 모드
{{PACKAGE_MANAGER}} test:watch

# E2E 테스트 실행
{{PACKAGE_MANAGER}} test:e2e

# 테스트 커버리지
{{PACKAGE_MANAGER}} test:cov
{{/TEST_RUNNER}}
```

### 커밋 전 체크리스트

- [ ] Lint 검사 통과
- [ ] Format 검사 통과
{{#TEST_RUNNER}}
- [ ] 단위 테스트 통과
- [ ] E2E 테스트 통과 (주요 변경사항이 있는 경우)
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

## API 문서

{{#SWAGGER}}
### Swagger

- **엔드포인트**: `/api`
- 모든 Controller에 `@ApiTags()` 적용
- DTO에 `@ApiProperty()` 추가

```typescript
@ApiTags('users')
@Controller('users')
export class UsersController {
  @ApiOperation({ summary: 'Get user by ID' })
  @ApiResponse({ status: 200, type: UserDto })
  @Get(':id')
  findOne(@Param('id') id: string) {
    return this.usersService.findOne(id);
  }
}
```
{{/SWAGGER}}

## 보안

### 필수 준수 ✅

1. **인증/인가**
   - Passport 전략 사용 (JWT, Local 등)
   - Guard를 통한 라우트 보호
   - Role-based access control (RBAC)

2. **데이터 검증**
   - 모든 입력 데이터 검증
   - SQL Injection 방지 (ORM 사용)
   - XSS 방지 (sanitize-html)

3. **환경 변수**
   - 민감한 정보는 환경 변수로 관리
   - .env 파일 절대 커밋 금지

## 참고사항

- 이 문서는 프로젝트 초기화 시 자동 생성되었습니다
- 프로젝트 구조나 규칙이 변경되면 이 문서도 함께 업데이트하세요
- AI 어시스턴트는 이 문서를 기반으로 프로젝트를 이해하고 코드를 생성합니다
