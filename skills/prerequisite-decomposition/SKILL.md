---
name: prerequisite-decomposition
description: 현재 개념에서 계속 막히는 경우, 그 개념을 이해하기 위해 필요한 더 작은 기초 개념으로 내려가는 스킬
---

# Skill: 선수개념 분해

## 1. 스킬 정의

### 이름
선수개념 분해

### 역할
같은 개념에서 **5회 이상 반복 오답**이 발생했을 때, **그 개념을 이루는 더 작은 기초 개념으로 내려가서** 근본부터 이해하게 하는 스킬

### 활용 시점
- 무비판 오답처리에서 attempts ≥ 5회일 때
- 학생이 같은 스텝에서 계속 막힐 때
- 선수개념 부족을 감지했을 때

### 입력값
- current_concept (현재 막혀있는 개념)
- attempts (시도 횟수 ≥ 5)
- prerequisite_concepts (선수개념 목록)

### 출력값
- NEW_SUBSTEPS (더 작은 2~3개의 기초 개념)
- 학생이 이해할 수 있는 안내 메시지
- 다음 시작 위치 기록

---

## 2. 핵심 원칙

| 원칙 | 설명 | 위반 예시 |
|------|------|---------|
| **완료 처리 X** | 현재 스텝을 완료하지 않음 | ❌ 이해 미확인인데 완료로 기록 |
| **데이터 기반** | prerequisite-map에서 조회 | ✅ 임의 설계 금지 |
| **기초로 내려감** | 선수개념 찾아 더 작은 단계로 | ✅ 단계별 분해 |
| **긍정적 표현** | "난이도 낮춘다" 금지 | ❌ "쉬운 거 해" (낙인) |
| **우리 함께** | "더 작은 조각으로" 톤 | ✅ 함께 탐색하는 분위기 |
| **기록 명확** | "다음복습필요" 상태 저장 | ✅ 진도 완료 X, 선수개념 명시 |

---

## 3. 절차 (Step-by-Step)

### Step 1: 현재 스텝을 완료 처리하지 않는다

**목표**: 이해 미확인 상태 명확히 기록

**방법**:
```
현재 상태:
{
  "current_step": 2,
  "current_concept": "분자와 분모",
  "understanding_level": "incomplete" (이해 미확인),
  "status": "다음복습필요" ← 완료 X
}

금지:
{
  "status": "완료" ← 이건 절대 X
}
```

**이유**:
- 나중에 세션 리포트 생성 시 혼동 방지
- 학생이 "안 배웠던 부분"으로 표시
- 보호자/교사가 상황 인식 가능

---

### Step 2: prerequisite-map에서 선수개념을 찾는다

**목표**: 이미 설계된 선수개념 구조 활용

**데이터 출처**: `prerequisite-map.csv`

**조회 방법**:
```
현재 개념: "분자와 분모"

prerequisite-map에서 조회:
concept_id | concept_name | prerequisite_concepts
FRAC_002   | 분자와분모   | ["FRAC_001", "FRAC_BASIC_UNDERSTAND"]

필요한 선수개념:
- FRAC_001: 분수 개념 (전체와 부분)
- FRAC_BASIC: 나누기 기본
```

**데이터 형식**:
```csv
concept_id,concept_name,level,prerequisites
FRAC_002,분자와분모,level2,"FRAC_001,FRAC_BASIC"
FRAC_001,분수개념,level1,"FRAC_BASIC"
FRAC_BASIC,나누기기본,level0,"NONE"
```

---

### Step 3: 더 작은 하위스텝을 2~3개 만든다

**목표**: 현재 개념의 필수 기초를 아주 작은 단계로 분해

**방법**:

```
현재 막혀있는 개념: "분자와 분모"
필요 선수개념: ["분수 개념", "전체와 부분", "나누기"]

생성할 새 하위스텝:
Sub-Step 1: "전체와 부분 이해"
           → 호빵 한 개 = 전체, 조각 = 부분

Sub-Step 2: "나누기와 개수"
           → 호빵을 4개로 나눔 = 4등분

Sub-Step 3: "위치와 역할"
           → 1/4에서 1의 위치, 4의 위치가 뭔가?
```

**원칙**:
- 각 Sub-Step은 1개 개념만
- 3줄 이하 설명 가능 수준
- 학생이 이해할 가능성 높은 순서

---

### Step 4: 학생에게 "난이도를 낮춘다"고 말하지 않는다

**금지 표현**:
```
❌ "난이도를 낮춰볼게"
❌ "쉬운 거부터 하자"
❌ "이건 너무 어려웠나?"
❌ "더 쉬운 버전"
❌ "원래 거는 나중에"
```

**심리적 이유**:
```
"난이도 낮춤" → "내가 못한다" → 자존감 ↓
"더 작은 조각" → "배우는 과정" → 자존감 유지 ↑
```

---

### Step 5: "더 작은 조각으로 보자"라고 안내한다

**올바른 표현**:

| 상황 | 말투 |
|------|------|
| 선수개념 필요 | "이 부분이 조금 더 기초부터 필요할 것 같아. 더 작은 조각으로 봐보자" |
| 학생이 피곤할 때 | "오늘은 여기까지 하고, 다음에 더 작게 봐보자" |
| 다음 세션 준비 | "다음에는 여기서부터 차근차근 시작할게" |
| 격려 | "너는 잘했어. 지금까지의 노력이 다 도움이 될 거야" |

**이렇게 해야 하는 이유**:
- "더 작은 조각" = 학습 전략 (중립적)
- "난이도 낮춤" = 능력 평가 (부정적)

---

### Step 6: 다음 시작 위치를 저장한다

**목표**: 다음 세션에서 바로 시작할 수 있게 명확히 기록

**저장 형식**:
```json
{
  "session_end_state": {
    "current_step": 2,
    "current_concept": "분자와 분모",
    "status": "다음복습필요",
    "reason": "5회 이상 반복 오답 → 선수개념 분해",
    "prerequisites_to_study": [
      {"step": 0, "concept": "전체와 부분 이해"},
      {"step": 0.5, "concept": "나누기와 개수"},
      {"step": 1, "concept": "위치와 역할"}
    ],
    "original_target_step": 2,
    "decomposed_substeps": 3,
    "next_session_start": "Sub-Step 1: 전체와 부분"
  }
}
```

**다음 세션 시작**:
```
학생: "안녕! 오늘도 분수 해볼래?"
AI: "좋아! 지난번에 분자/분모가 조금 어려웠잖아.
    오늘은 그걸 더 잘 이해하기 위해서,
    더 기초부터 같이 해보자.
    
    먼저 '전체와 부분'부터 시작해보자!"
```

---

## 4. 선수개념 조회 예시

### 예시 1: 분수의 덧셈 체인

```
학생이 "분수의 덧셈"에서 5회 이상 틀림

현재 concept_id: FRAC_ADD (분수의 덧셈)

prerequisite-map 조회:
FRAC_ADD → [FRAC_SAME_DENOM, FRAC_PARTS, FRAC_CONCEPT]

FRAC_SAME_DENOM (같은 분모)
  → [FRAC_PARTS, FRAC_CONCEPT]

FRAC_PARTS (분자/분모)
  → [FRAC_CONCEPT, FRAC_BASIC_DIVIDE]

FRAC_CONCEPT (분수 개념)
  → [FRAC_BASIC_DIVIDE, WHOLE_PART]

따라서 선수개념 체인:
WHOLE_PART → FRAC_BASIC_DIVIDE → FRAC_CONCEPT → FRAC_PARTS → FRAC_SAME_DENOM

현재 위치: FRAC_ADD (5회 이상 틀림)
어디로 돌아갈까? FRAC_PARTS 또는 FRAC_CONCEPT부터
```

---

### 예시 2: 부사절 접속사 체인

```
학생이 "부사절 접속사 종합"에서 5회 이상 틀림

현재: CONJ_COMPREHENSIVE

prerequisite-map 조회:
CONJ_COMPREHENSIVE → [CONJ_INDIVIDUAL, CONJ_DEFINITION, SENTENCE_STRUCTURE]

CONJ_INDIVIDUAL (각 접속사별)
  → [CONJ_DEFINITION, SENTENCE_STRUCTURE]

CONJ_DEFINITION (접속사 정의)
  → [SENTENCE_STRUCTURE]

SENTENCE_STRUCTURE (문장 구조)
  → [MAIN_CLAUSE, SUB_CLAUSE]

돌아갈 선수개념:
- Sub-Step 1: 주절과 종속절 구분
- Sub-Step 2: 접속사의 역할
- Sub-Step 3: 각 접속사별 의미
```

---

## 5. 새 하위스텝 생성 예시

### 예시 1: 분자와 분모 분해

**현재 개념**: "분자와 분모" (5회 이상 틀림)

**필수 선수개념**: ["분수 개념", "전체와 부분", "위치 개념"]

**생성된 Sub-Steps**:

```
Sub-Step 1: "전체와 부분 명확히"
개념: 호빵 1개 = 전체, 조각 1개 = 부분
학습목표: 전체와 부분의 관계 이해
미니퀴즈: "호빵을 4개로 나누면, 
         조각 1개는 전체의 몇 개 중 1개?"

Sub-Step 2: "나누기와 개수"
개념: 나누기 = 분해, 개수 = 표현
학습목표: 4등분 = 4개로 나눔의 의미
미니퀴즈: "호빵을 4등분했다는 것은
         4개의 (같은 크기) 조각으로 나눴다는 뜻이야"

Sub-Step 3: "분자와 분모의 위치"
개념: 위 = 취한 개수, 아래 = 전체 개수
학습목표: 1/4에서 1(분자), 4(분모)의 의미
미니퀴즈: "1/4에서 위의 1은 뭐를 나타낼까?
         ① 내가 먹은 조각 수
         ② 전체 조각 수"
```

---

### 예시 2: 과거형 분해

**현재 개념**: "불규칙동사 과거형" (5회 이상 틀림)

**필수 선수개념**: ["규칙동사", "시제 개념", "동사 변화"]

**생성된 Sub-Steps**:

```
Sub-Step 1: "동사의 기본형과 변화"
개념: 동사는 시제에 따라 형태 변함
학습목표: walk/walked, go/went는 모두 변한 것
미니퀴즈: "Walk와 walked는 같은 동사인가? (O/X)"

Sub-Step 2: "규칙동사 패턴"
개념: 규칙동사는 -ed를 붙임
학습목표: play → played, walk → walked의 규칙
미니퀴즈: "play의 과거형은? played"

Sub-Step 3: "불규칙동사는 다름"
개념: 불규칙동사는 규칙을 안 따름
학습목표: go → went, eat → ate 등의 변화
미니퀴즈: "go의 과거형은?
         ① goed  ② went  ③ goes"
```

---

## 6. 출력 형식

```
[학생에게 보이는 긍정적 안내]
[지난 노력 격려 + 더 작은 조각 제시]

새로운 학습 경로:
1. [Sub-Step 1 명시]
2. [Sub-Step 2 명시]
3. [Sub-Step 3 명시]

자, 1번부터 시작해보자!

---
NEW_SUBSTEPS: [Sub-Step 배열]
ORIGINAL_CONCEPT: [현재 막혀있던 개념]
STATUS: 선수개념분해 완료
NEXT: Sub-Step 1 개념설명으로 이동
STATE_UPDATE:
{
  "current_step": 정수,
  "current_concept": "개념명",
  "decomposition_trigger": "5회 이상 반복",
  "original_target_step": 정수,
  "prerequisite_concepts_from_map": ["개념1", "개념2"],
  "new_substeps": [
    {
      "substep": 1,
      "substep_name": "Sub-Step 이름",
      "concept_id": "ID",
      "learning_objective": "목표",
      "expected_difficulty": "매우 쉬움"
    },
    ...
  ],
  "substeps_count": 정수,
  "session_status": "진행중_선수개념학습",
  "original_session_status": "다음복습필요",
  "next_session_plan": {
    "resume_from": "Sub-Step 1",
    "original_target": "분자와 분모",
    "estimated_steps": 3
  },
  "next_state": "스텝설명"
}
```

---

## 7. 필드 상세 설명

| 필드 | 설명 | 예시 |
|------|------|------|
| **NEW_SUBSTEPS** | 새로 생성된 Sub-Step 배열 | [1, 2, 3] |
| **ORIGINAL_CONCEPT** | 원래 막혀있던 개념 | "분자와 분모" |
| **decomposition_trigger** | 분해 이유 | "5회 이상 반복" |
| **prerequisite_concepts_from_map** | prerequisite-map에서 추출한 선수개념 | ["분수개념", "나누기"] |
| **new_substeps** | Sub-Step 상세 정보 배열 | [{substep: 1, ...}] |
| **substeps_count** | 생성된 Sub-Step 개수 | 3 |
| **session_status** | 현재 세션 상태 | "진행중_선수개념학습" |
| **original_session_status** | 선수개념 분해 전 상태 | "다음복습필요" |
| **next_session_plan** | 다음 세션 계획 | {resume_from: "Sub-Step 1", ...} |

---

## 8. 학생에게 보이는 메시지 예시

### 좋은 메시지

```
"너는 지금까지 정말 열심히 했어.
 분자/분모가 조금 어렵긴 한데,
 이게 기초부터 더 잘 이해하면 쉬워질 거야.
 
 그래서 오늘은 더 작은 조각으로 한 번 봐보자.
 먼저 '전체와 부분'부터 시작할게."

→ 긍정적, 과정 지향, 기대감
```

### 나쁜 메시지

```
❌ "난이도를 낮춰볼게"
❌ "이건 너무 어려웠나?"
❌ "5번이나 틀렸네. 기초부터 다시."
❌ "쉬운 거부터 하자"

→ 부정적, 능력 평가, 낙인 효과
```

---

## 9. 체크리스트

### 선수개념 분해 Skill 실행 체크

- [ ] 현재 스텝을 완료 처리하지 않았는가? (상태: "다음복습필요")
- [ ] prerequisite-map에서 선수개념을 조회했는가?
- [ ] 새로운 Sub-Step을 2~3개 생성했는가?
- [ ] 각 Sub-Step이 1개 개념만 다루는가?
- [ ] "난이도를 낮춘다"는 표현 안 썼는가?
- [ ] "더 작은 조각으로"라는 표현으로 안내했는가?
- [ ] 학생의 노력을 먼저 격려했는가?
- [ ] Sub-Step의 순서가 논리적인가? (쉬운 것부터)
- [ ] 다음 세션 시작 위치를 명확히 기록했는가?
- [ ] NEW_SUBSTEPS를 명시했는가?
- [ ] ORIGINAL_CONCEPT를 기록했는가?
- [ ] next_session_plan을 상세히 했는가?

---

## 10. 주의사항

### 하면 안 되는 것

| 금지 사항 | 이유 |
|---------|------|
| "난이도 낮춤" 표현 | 능력 평가 느낌 |
| 현재 스텝 완료 처리 | 이해 미확인 상태 기록 실패 |
| 선수개념 없이 임의 설계 | prerequisite-map 무시 |
| 1개 이상의 Sub-Step만 | 선택지 불충분 |
| 어려운 Sub-Step부터 | 학습 순서 오류 |
| 반복 횟수 언급 | "5회나..." 같은 부정적 표현 |

### 해야 하는 것

| 권장 사항 | 효과 |
|---------|------|
| 긍정적 표현 | 자존감 유지 |
| "다음복습필요" 기록 | 진도 현황 정확 |
| 데이터 기반 설계 | 일관성, 신뢰성 |
| 2~3개 Sub-Step | 충분한 선택지 |
| 쉬운 것부터 | 학습 동기 유지 |
| 노력 격려 | "계속 시도" 동기 |

---

## 11. 선수개념 체인 매핑

```
수학 예시:
WHOLE_PART (기초 0)
    ↓
FRAC_BASIC_DIVIDE (기초 1)
    ↓
FRAC_CONCEPT (기초 2)
    ↓
FRAC_PARTS (기초 3)
    ↓
FRAC_SAME_DENOM (기초 4)
    ↓
FRAC_ADD (현재, 5회 이상 틀림)

돌아갈 지점: FRAC_PARTS 또는 FRAC_CONCEPT부터
```

---

## 12. 세션 종료 vs 계속 진행

```
선수개념 분해 후:

학생 상태: 피곤, 반복 많았음
→ "오늘은 여기까지. 다음에 계속 하자"
→ 세션 종료 OK
→ "다음복습필요" 상태 저장

학생 상태: 신선함, "계속 할 수 있어요"
→ Sub-Step 1부터 시작
→ 세션 계속 진행
```

---

## 13. 다음 Skill 연결

선수개념 분해 후:

```
선수개념 분해 (현재)
    ↓
┌──────────────────────────────┐
│ 계속 진행할까, 종료할까?      │
└──────────────────────────────┘
    │
    ├─ 계속 진행 → Sub-Step 1 개념설명 Skill
    │             (반복: 개념설명 → 검증 → 미니퀴즈)
    │
    └─ 종료 → 세션 종료 + 리포트 생성
               (다음복습필요 상태로 기록)
```

---

## 14. 보호자/교사 리포트 표기

```
학생용 리포트:
"오늘은 분수 개념을 더 깊이 있게 이해하기 위해
 기초부터 함께 공부했어요. 전체와 부분의 관계를
 잘 이해하고 있어요!"

보호자/교사용 리포트:
"분자/분모 개념에서 반복 어려움 감지.
 선수개념 분해 실행.
 다음 복습에서 '전체와 부분'부터 시작 권고.
 원인: 나누기 기본 개념 부족 추정."
```

---

## 15. 문서 변경 이력

| 날짜 | 버전 | 내용 |
|------|------|------|
| 2026-07-02 | 1.0 | 초판 작성 |

