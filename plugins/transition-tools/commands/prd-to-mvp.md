---
description: PRD를 분석하여 MVP 범위 정의 및 단계별 구현 계획서 생성
argument-hint: [PRD 내용 또는 링크]
---

## PRD → MVP & Phased Implementation Document Generator

### LANGUAGE PROTOCOL

- **Internal Reasoning**: All analysis in `<thinking>` blocks MUST be conducted in **English** to maximize logical precision and reasoning depth.
- **Final Output**: All user-facing documents and responses MUST be delivered in **Korean** with natural, professional tone.
- **Exception**: Technical terms, proper nouns, and industry-standard terminology should remain in their original language (e.g., MVP, API, MoSCoW, User Story).

---

### SIMULATION FRAMEWORK

When analyzing a given PRD, simulate how the following expert group would cross-review it based on their respective expertise:

- **Product Strategist**: Core value proposition and market fit assessment
- **Technical Architect**: Implementation complexity and technical dependency analysis
- **Startup Advisor**: Resource efficiency and validation priority decisions
- **UX Designer**: User journey and critical interaction identification

This simulation process is performed only within the `<thinking>` block for internal reasoning. Only the integrated results are presented to the user.

---

### PHASE 1: PRD Analysis (Internal Reasoning)

Perform within `<thinking>` block:

**1.1 Core Element Extraction**
- What is the Problem Statement being addressed?
- Who is the Target User?
- What is the Core Value Proposition?
- What are the Success Metrics?

**1.2 Feature Classification**
Classify all features in the PRD using the following criteria:

| Category | Definition | Decision Criteria |
|----------|------------|-------------------|
| **Must-Have** | Essential to deliver core value proposition | Does the product make sense without this? |
| **Should-Have** | Significantly improves user experience | Can early users be satisfied without this? |
| **Could-Have** | Competitive advantage or scalability | Can this be added after validation without issues? |
| **Won't-Have (Now)** | Out of current scope | Is this unrelated to core hypothesis validation? |

**1.3 Assumptions Identification**
- Explicitly state assumptions implicitly embedded in the PRD
- Evaluate risk level for each assumption
- Prioritize assumptions requiring validation

---

### PHASE 2: MVP Scope Definition

**2.1 MVP Principles Application**
```
MVP = Minimum feature set required to validate the core hypothesis
```

Review Questions:
- [ ] Can this MVP validate "Do users want to solve this problem?"
- [ ] Can this MVP validate "Does our solution solve the problem?"
- [ ] Is the core user journey complete without additional features?

**2.2 Technical MVP Boundaries**
- Distinguish essential infrastructure vs. optimized infrastructure
- Minimize external service/API dependencies
- Identify automation features that can be replaced with manual processes

---

### PHASE 3: Phased Implementation Planning

Define each phase using the following structure:
```
## Phase N: [Phase Name]

### Objective
- Hypothesis to validate or goal to achieve in this phase

### Scope
- Included feature list
- Explicit exclusions (deferred to next phase)

### Technical Requirements
- Required tech stack
- Infrastructure requirements
- External dependencies

### Success Criteria
- Quantitative metrics (e.g., DAU, conversion rate, completion rate)
- Qualitative metrics (e.g., user feedback themes)

### Estimated Duration
- Development period (provide as range)
- Key milestones

### Risks & Mitigation
- Identified risks
- Response strategies

### Gate Conditions for Next Phase
- Criteria required to proceed to Phase N+1
```

---

### PHASE 4: Output Document Generation

**Output Structure:**
```markdown
# [제품명] MVP 및 단계별 구현 계획서

## Executive Summary
> 3문장 이내로 제품, MVP 범위, 단계별 로드맵 요약

## 1. PRD 핵심 분석

### 1.1 문제 정의
[원본 PRD에서 추출 및 정제]

### 1.2 핵심 가치 제안
[명확하게 재정의]

### 1.3 목표 사용자
[페르소나 또는 세그먼트 명시]

### 1.4 핵심 가정 및 리스크
| 가정 | 리스크 레벨 | 검증 방법 |
|------|-------------|-----------|
| ... | High/Med/Low | ... |

---

## 2. MVP 정의

### 2.1 MVP 범위
**포함 기능:**
- [기능 1]: [포함 이유]
- [기능 2]: [포함 이유]

**명시적 제외:**
- [기능 A]: [제외 이유, 예상 단계]

### 2.2 MVP 사용자 여정
[핵심 플로우를 단계별로 기술]

### 2.3 MVP 성공 기준
- [지표 1]: [목표값]
- [지표 2]: [목표값]

---

## 3. 단계별 구현 계획

### Phase 1: MVP (Core Validation)
[위 구조에 따라 상세 기술]

### Phase 2: [단계명]
[위 구조에 따라 상세 기술]

### Phase 3: [단계명]
[위 구조에 따라 상세 기술]

---

## 4. 기술 아키텍처 개요

### 4.1 MVP 기술 스택
[선택 이유와 함께 기술]

### 4.2 확장 고려사항
[Phase 2-3에서 필요한 기술적 준비 사항]

---

## 5. 타임라인 및 마일스톤

[간트 차트 또는 타임라인 다이어그램]

---

## Appendix

### A. 기능 우선순위 매트릭스
[전체 기능의 MoSCoW 분류표]

### B. 기술적 의존성 맵
[컴포넌트 간 의존관계]

### C. 원본 PRD 참조
[필요 시 원본 섹션 참조]
```

---

### VALIDATION CHECKLIST

Self-validate after document generation:

- [ ] Is the MVP truly "minimum"? Are there features that can be further reduced?
- [ ] Are gate conditions for each phase measurable?
- [ ] Does tech stack selection align with team capabilities?
- [ ] Is the assumption-validation method connection logical?
- [ ] Does each phase deliver value from the user's perspective?

---

### USAGE

When a PRD is provided, generate MVP and phased implementation documents following the above process.

**Input Format:**
```
다음 PRD를 분석하여 MVP 및 단계별 구현 계획서를 작성해주세요:

[PRD 내용]
```

**Additional Context (Optional):**
- 팀 규모: 
- 예상 기간:
- 기술 스택 제약:
- 특별히 중요한 제약 조건: