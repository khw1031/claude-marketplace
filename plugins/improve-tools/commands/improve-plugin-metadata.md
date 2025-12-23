---
description: Claude Code 플러그인의 메타데이터 개선 및 최적화
argument-hint: [plugin-directory-path]
---

## 목표

Claude Code 플러그인의 메타정보를 분석하고 공식 가이드라인에 맞춰 개선합니다.

## 작업 프로세스

### 1단계: 플러그인 구조 분석

주어진 플러그인 경로에서 다음 파일들을 읽어서 현재 상태를 파악합니다:

1. `.claude-plugin/plugin.json` - 플러그인 전체 메타데이터
2. `commands/*.md` - 개별 명령어 파일들의 frontmatter

### 2단계: plugin.json 개선

다음 기준으로 plugin.json의 메타데이터를 개선합니다:

#### description 필드 개선 기준

- 길이: 30-60자 범위의 간결한 한 줄 설명
- 구조: 동사로 시작하는 명확한 기능 설명
- 내용: 플러그인의 핵심 가치를 직관적으로 전달
- 스타일: 구어체 피하고 표준 영어 표현 사용

개선 예시:
```
❌ 나쁜 예: "A plugin" / "Do things" / "Various features..."
✅ 좋은 예: "Evaluate freelance project ideas and opportunities"
```

#### keywords 필드 개선 기준

- 관련성: description과 명령어 기능에 직접 관련된 키워드만 포함
- 검색성: 사용자가 실제로 검색할 만한 용어 선택
- 개수: 3-7개 정도의 핵심 키워드
- 일관성: description의 주요 단어와 일치

개선 예시:
```json
{
  "description": "Evaluate freelance project ideas and opportunities",
  "keywords": ["freelance", "project", "evaluation", "analysis", "viability"]
}
```

### 3단계: 명령어 frontmatter 개선

각 명령어 파일(`commands/*.md`)의 frontmatter를 다음 기준으로 개선합니다:

#### 공식 필드 확인

Claude Code가 공식적으로 지원하는 필드만 사용:

| 필드 | 용도 | 필수 여부 |
|------|------|---------|
| `description` | 명령어 설명 (/help에 표시) | 필수 |
| `argument-hint` | 인자 힌트 | 선택 (권장) |
| `allowed-tools` | 허용 도구 제한 | 선택 |
| `model` | 특정 모델 지정 | 선택 |
| `disable-model-invocation` | 도구 호출 방지 | 선택 |

#### 제거해야 할 비공식 필드

다음 필드들은 plugin.json에서 관리하므로 명령어 frontmatter에서 제거:
- `name` (파일명에서 자동 추출)
- `author` (plugin.json에서 관리)
- `author-url` (plugin.json에서 관리)
- `version` (plugin.json에서 관리)
- 기타 비공식 필드

#### description 개선 기준

- 길이: 한 줄로 간결하게 (도움말 표시용)
- 명확성: 명령어가 무엇을 하는지 직접적으로 설명
- 사용자 관점: "~를 한다" 형태로 작성

개선 예시:
```yaml
# 개선 전
---
name: freelancer-go-not-go
description: 클라이언트의 요구사항을 받으면 바이브 코딩 프리랜서 입장에서 해당 요구사항을 평가해줘
author: Someone
version: 1.0.0
---

# 개선 후
---
description: 프리랜서 관점에서 클라이언트 요구사항 평가 및 진행 가능성 판단
argument-hint: [프로젝트 요구사항]
---
```

#### argument-hint 추가

사용자가 명령어 입력 시 어떤 인자를 전달해야 하는지 힌트 제공:

```yaml
---
description: 코드 리뷰 수행 및 개선사항 제안
argument-hint: [file-path] [review-type]
---
```

### 4단계: 개선사항 적용

분석이 완료되면 다음 순서로 파일을 수정합니다:

1. `plugin.json` 개선
   - description 최적화
   - keywords 개선

2. 각 명령어 파일의 frontmatter 개선
   - 비공식 필드 제거
   - description 간결화
   - argument-hint 추가

### 5단계: 개선 보고서 작성

개선 작업 완료 후 다음 내용을 포함한 보고서를 제공합니다:

```markdown
## 플러그인 메타데이터 개선 보고서

### plugin.json 변경사항
- description: [이전] → [이후]
- keywords: [이전] → [이후]

### 명령어 파일 변경사항

#### [명령어명].md
- 제거된 필드: name, author, version 등
- description: [이전] → [이후]
- 추가된 필드: argument-hint

### 개선 효과
1. 검색 최적화: keywords와 description 일관성으로 발견 가능성 향상
2. 사용자 경험: argument-hint 추가로 명령어 사용법 명확화
3. 표준 준수: 공식 필드만 사용하여 호환성 보장
4. 유지보수성: 메타데이터 중복 제거로 관리 용이

### 추가 권장사항
[필요시 추가 개선사항 제안]
```

## 실행 방법

```bash
# 플러그인 경로를 인자로 전달
/improve-plugin-metadata plugins/your-plugin-name
```

## 주의사항

- 파일 수정 전 반드시 현재 내용을 읽어서 확인
- 명령어의 실제 기능과 메타데이터가 일치하는지 검증
- description 변경 시 명령어의 본문 내용도 함께 고려
- 기존 기능을 변경하지 않고 메타데이터만 개선

## 베스트 프랙티스

### description 작성 팁
- 동사로 시작: "Evaluate", "Generate", "Analyze"
- 핵심 기능 먼저: 부가 기능은 생략
- 기술 용어 최소화: 일반 사용자도 이해 가능하게
- 수동태 피하기: 능동태로 명확하게

### keywords 선택 팁
- 사용자 검색 의도 반영
- 유사 플러그인과 차별화
- 일반 키워드 + 특화 키워드 조합
- description의 핵심 단어 포함

### argument-hint 작성 팁
- 대괄호 사용: `[required-arg]`
- 선택적 인자: `[optional-arg?]`
- 복수 인자: `[arg1] [arg2]`
- 의미 명확히: `[file-path]` > `[path]`
