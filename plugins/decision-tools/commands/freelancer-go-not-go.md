---
description: 프리랜서 관점에서 클라이언트 요구사항 평가 및 진행 가능성 판단
argument-hint: [프로젝트 요구사항]
---

## SIMULATION GOAL

Simulate how an elite consulting team with deep expertise in software development estimation, market pricing analysis, modern web/app frameworks, and project scoping would analyze the given IT client requirements from their respective perspectives.

**Perspectives to activate:**
- **Tech Lead**: 기술 스택 적합성, 구현 복잡도, 바이브 코딩 적합성 평가
- **Project Estimator**: 개발 공수 산정, 숨겨진 요구사항 식별
- **Market Analyst**: 시장 단가 대비 요청 금액의 적정성 분석
- **Risk Assessor**: 프로젝트 리스크, 기술적 함정, 스코프 크립 가능성 평가

---

## INPUT FORMAT

클라이언트 요구사항을 다음 형식으로 입력:
```
[프로젝트명]: (있다면)
[요구사항]: 
(클라이언트가 제시한 기능/서비스 설명)

[제시 금액]: (있다면)
[기한]: (있다면)
[추가 정보]: (있다면)
```

---

## ANALYSIS FRAMEWORK

### 1. 요구사항 분해 (Requirement Decomposition)

| 구분 | 내용 |
|------|------|
| **핵심 기능** | 필수 구현 기능 목록 |
| **숨겨진 요구사항** | 명시되지 않았지만 필요한 기능 (인증, 관리자 페이지 등) |
| **기술적 복잡도** | Low / Medium / High / Very High |

### 2. 바이브 코딩 적합성 판정

**바이브 코딩 정의**: AI 도구(Cursor, Copilot 등)를 활용해 빠르게 프로토타입/MVP 수준으로 개발하는 방식

**적합성 기준:**
- ✅ 적합: 표준 CRUD, 일반적인 웹/앱, 템플릿 기반 구현 가능
- ⚠️ 부분 적합: 일부 커스텀 로직 필요, 외부 API 연동 다수
- ❌ 부적합: 고도의 도메인 전문성 필요, 복잡한 알고리즘, 레거시 연동

**판정 결과:**
```
[적합성]: ✅/⚠️/❌
[예상 난이도]: 1-10점
[근거]: (구체적 이유)
```

### 3. 기술 스택 제안
```
[Frontend]: 
[Backend]: 
[Database]: 
[Infra/Deployment]: 
[추가 서비스]: (결제, 인증, 스토리지 등)
```

### 4. 금액 적정성 분석

**시장 기준 산정 방식:**
- 예상 개발 기간 × 일일 단가 (주니어: 20-30만원, 미드: 40-60만원, 시니어: 80-150만원)
- 유사 프로젝트 시장 시세 참조
```
[예상 적정 금액 범위]: ₩OOO ~ ₩OOO
[클라이언트 제시 금액]: ₩OOO
[판정]: 적정 / 저가 / 고가
[분석]: (근거 및 협상 포인트)
```

### 5. 리스크 & 권고사항
```
[주요 리스크]:
1. ...
2. ...

[권고사항]:
- 수락 전 확인해야 할 사항
- 계약 시 명시해야 할 조건
- 스코프 제한 제안
```

---

## OUTPUT FORMAT
```
## 📋 프로젝트 분석 리포트

### 1️⃣ 요구사항 요약
(핵심 기능 + 숨겨진 요구사항)

### 2️⃣ 바이브 코딩 적합성
[판정]: ✅/⚠️/❌ | 난이도: O/10
(상세 근거)

### 3️⃣ 추천 기술 스택
(테이블 또는 목록)

### 4️⃣ 금액 분석
- 시장 적정가: ₩OOO ~ ₩OOO
- 제시 금액 판정: (적정/저가/고가)
- 협상 포인트: ...

### 5️⃣ 최종 권고
[수락 권고 여부]: 🟢 권장 / 🟡 조건부 / 🔴 비권장
[핵심 조언]: (한 줄 요약)
```

---

## CONSTRAINTS

- 분석은 한국 IT 외주 시장 기준으로 수행
- 금액 산정 시 2024-2025년 시세 반영
- 바이브 코딩 도구는 Cursor, v0, Bolt, Lovable 등 가정
- 불확실한 요소는 보수적으로 산정
