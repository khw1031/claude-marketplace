---
description: 주제에 맞는 전문가 그룹을 동적으로 구성하여 다각적 분석 제공
argument-hint: [질문 또는 주제]
---

## Dynamic Expert Simulation System

For any text (question, sentence, or keyword) provided by the user, execute the following process.

---

### LANGUAGE PROTOCOL

- **Internal Reasoning**: All analysis in `<thinking>` blocks MUST be conducted in **English** to maximize logical precision and reasoning depth.
- **Final Output**: All user-facing responses MUST be delivered in **Korean** with natural, professional tone.

---

### Phase 1: Topic Analysis (Internal Reasoning)

Perform within `<thinking>` block:

- Identify the **core domain** of input text (technology, business, creative, academic, everyday life, etc.)
- Determine **specific subfield** (e.g., technology → frontend/backend/infrastructure/security)
- Classify the **nature of inquiry** (problem-solving, decision-making, learning, creative, analytical)
- Assess required **depth of expertise** (overview vs. deep-dive)

---

### Phase 2: Expert Group Assembly (Internal Reasoning)

Perform within `<thinking>` block:

Select **3-5 expert types** best positioned to provide the most insightful response for the identified topic.

**Selection Criteria:**
- **Core domain expert** (mandatory)
- **Adjacent field expert** (cross-perspective)
- **Practitioner/theorist balance** (applicability)
- **Critical viewpoint** (blind spot coverage)

**Example Mappings:**
| Input Type | Expert Group Composition |
|------------|--------------------------|
| Frontend Architecture | Senior Frontend Architect, UX Engineer, Performance Optimization Specialist, Tech Lead |
| Startup Idea | Venture Capitalist, Serial Entrepreneur, Market Analyst, Product Strategist |
| Health Question | Relevant Medical Specialist, Clinical Researcher, Preventive Medicine Expert, Patient Education Specialist |
| Writing/Creative | Genre-specific Author, Editor, Literary Critic, Reader Perspective Analyst |

---

### Phase 3: Multi-Perspective Simulation (Internal Reasoning)

Perform within `<thinking>` block:
```
Simulate how the assembled expert group would analyze [user's question] 
based on their respective expertise and experience.

For each expert perspective:
- What is the core point of this question?
- What are the most critical considerations from my domain?
- What perspectives might other experts overlook?
- What practically applicable suggestions can I offer?
```

---

### Phase 4: Integrated Response Generation (Present to User)

**Output Format (in Korean):**

#### 전문가 관점 요약
> 이 질문에 대해 [list assembled expert group]의 관점에서 분석했습니다.

#### 핵심 인사이트
[Naturally integrated answer synthesizing key insights from each expert perspective]

#### 관점별 심화 (Optional)
Provide detailed analysis from individual expert perspectives when needed

#### 실행 제안
Present concrete, actionable next steps

---

### Quality Standards

- **Relevance**: Is the expert group optimally suited to the topic?
- **Diversity**: Are complementary perspectives included?
- **Depth**: Does it provide expert-level insight, not surface-level answers?
- **Practicality**: Does it include actionable advice?
- **Integration**: Are multiple perspectives naturally synthesized?