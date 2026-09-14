---
name: lesson-step-analysis
description: 오늘 진도를 느린학습자가 따라갈 수 있도록 3~6개의 작은 순서형 스텝으로 나누는 스킬
---

# Skill: 스몰스텝 과제분석

## 1. 스킬 정의

### 이름
스몰스텝 과제분석

### 역할
오늘 진도를 **느린학습자가 따라갈 수 있도록 3~6개의 작은 순서형 스텝으로 분해**하고, **START_LEVEL에 맞게 현재 시작점을 결정**하는 스킬

### 활용 시점
- 수준진단 (START_LEVEL 결정) 완료 후
- 첫 번째 개념 설명을 하기 전

### 입력값
- LESSON (진도명)
- START_LEVEL (1, 2, 3 - 수준진단에서 전달)
- SCHOOL_LEVEL, GRADE, SUBJECT (진도입력에서 전달)

### 출력값
- STEPS (스몰스텝 배열)
- CURRENT_STEP (현재 시작 스텝)
- 학습 경로 맵

---

## 2. 핵심 원칙

| 원칙 | 설명 | 예시 |
|------|------|------|
| **데이터 기반** | lesson-units.json에서 조회 | 임의로 스텝 만들지 않음 |
| **START_LEVEL 연동** | 수준에 맞게 스텝 수 조정 | Level 1: 5~6개, Level 2: 3~4개, Level 3: 2~3개 |
| **1개 개념** | 각 스텝은 한 개념만 | "분수 개념"과 "분모"를 같은 스텝에 안 함 |
| **순서형** | 선수개념 우선 | 기본 → 심화 순서 유지 |
| **선수개념 참조** | 학생이 막힐 때 돌아갈 수 있게 | Step 1 우선, 필요하면 선수개념까지 역추적 가능 |
| **명확한 맵핑** | 각 스텝과 개념ID 명시 | {"step": 1, "concept_id": "FRAC_001", ...} |

---

## 3. 절차 (Step-by-Step)

### Step 1: lesson-units에서 해당 진도를 찾는다

**목표**: 이미 설계된 커리큘럼 구조 활용

**데이터 출처**: `lesson-units.json`

**조회 조건**:
```json
{
  "school_level": "중등",
  "grade": "1학년",
  "subject": "수학",
  "lesson": "분수의 덧셈"
}
```

**데이터 형식** (예시):
```json
{
  "lesson_id": "LESSON_FRAC_ADD_001",
  "lesson": "분수의 덧셈",
  "school_level": "중등",
  "grade": "1학년",
  "subject": "수학",
  "prerequisite_concepts": ["CONCEPT_FRAC_001", "CONCEPT_FRAC_002"],
  "steps": [
    {
      "step_id": 1,
      "step_name": "분수 개념 이해",
      "concept_id": "CONCEPT_FRAC_001",
      "learning_objective": "전체를 같은 크기로 나눈 것이 분수임을 이해"
    },
    {
      "step_id": 2,
      "step_name": "분자와 분모",
      "concept_id": "CONCEPT_FRAC_002",
      "learning_objective": "분자(위)는 취한 부분, 분모(아래)는 전체 개수"
    },
    ...
  ]
}
```

**데이터 없는 경우**:
```
진도명이 lesson-units에 없으면?
→ 이전 단계(진도입력)에서 data_available: false로 처리
→ 과제분석 스킬까지 올 수 없음
```

---

### Step 2: 스텝 목록을 가져온다

**목표**: 진도에 속한 모든 스텝을 배열로 정리

**데이터 추출**:
```json
[
  {"step": 1, "concept_id": "CONCEPT_FRAC_001", "name": "분수 개념"},
  {"step": 2, "concept_id": "CONCEPT_FRAC_002", "name": "분자/분모"},
  {"step": 3, "concept_id": "CONCEPT_FRAC_003", "name": "같은 분모 분수"},
  {"step": 4, "concept_id": "CONCEPT_FRAC_004", "name": "분수 덧셈 규칙"},
  {"step": 5, "concept_id": "CONCEPT_FRAC_005", "name": "연습 문제"}
]
```

---

### Step 3: START_LEVEL에 맞춰 CURRENT_STEP을 정한다

**목표**: 학생 수준에 맞게 시작점 결정

#### START_LEVEL = 3 (거의 다 알고 있음)

**특징**:
- 선수개념을 잘 알고 있음
- 핵심 개념만 정리 필요

**적용 방법**:
- 스텝 수: 2~3개 (핵심만)
- CURRENT_STEP: 중간~끝 스텝부터 시작 (예: Step 3~4)

**예시** (원본 5개 스텝):
```
원본: [Step 1, Step 2, Step 3, Step 4, Step 5]
      ↓
조정: [Step 3, Step 4] 또는 [Step 3, Step 4, Step 5]
      ↓
CURRENT_STEP: 3 (Step 3부터 시작)
```

**학생 메시지**:
```
"분수는 꽤 이해하고 있으니까, 
 분수 덧셈의 규칙만 정리해보자."
```

---

#### START_LEVEL = 2 (일부만 알고 있음)

**특징**:
- 기본 개념은 이해하지만 일부 부족
- 전체적인 정리 필요

**적용 방법**:
- 스텝 수: 3~4개 (기본부터)
- CURRENT_STEP: Step 1 또는 Step 2부터 시작

**예시** (원본 5개 스텝):
```
원본: [Step 1, Step 2, Step 3, Step 4, Step 5]
      ↓
조정: [Step 1, Step 2, Step 3, Step 4]
      ↓
CURRENT_STEP: 1 (Step 1부터 시작)
```

**학생 메시지**:
```
"분수의 기본부터 천천히 정리해보자. 
 먼저 분수가 뭔지부터 봐보자."
```

---

#### START_LEVEL = 1 (거의 모름)

**특징**:
- 선수개념이 부족하거나 개념이 낯설음
- 아주 천천히, 작은 단계씩 필요

**적용 방법**:
- 스텝 수: 5~6개 (더 세분화)
- CURRENT_STEP: Step 0 (선수개념) 또는 Step 1부터 시작
- 필요하면 선수개념까지 역추적

**예시 1** (원본 5개 스텝, 선수개념 충분):
```
원본: [Step 1, Step 2, Step 3, Step 4, Step 5]
      ↓
조정: [Step 0: 분수 기본, Step 1, Step 2, Step 3, Step 4, Step 5]
      ↓
CURRENT_STEP: 0 (선수개념부터 시작)
```

**예시 2** (원본 5개 스텝, 선수개념도 필요):
```
prerequisite_concepts에서 더 작은 개념 추출
원본: [Step 1, Step 2, Step 3, Step 4, Step 5]
      ↓
조정: [선수1: 분수란?, 선수2: 나누기, Step 1, Step 2, Step 3, Step 4]
      ↓
CURRENT_STEP: 선수1 (가장 기초부터)
```

**학생 메시지**:
```
"분수가 처음이거나 아직 어렵구나. 
 그럼 가장 작은 조각부터 천천히 함께 해보자."
```

---

### Step 4: 각 스텝은 한 번에 하나의 개념만 다룬다

**목표**: 마이크로러닝 원칙 유지

**검증**:
```
❌ Step 1: "분수 개념과 분자/분모"
   (2개 개념)

✅ Step 1: "분수 개념"
   Step 2: "분자와 분모"
   (각 1개 개념)
```

**적용**:
- lesson-units에서 가져온 스텝이 이미 1개 개념씩 설계되어 있으면 그대로 사용
- 만약 한 스텝에 여러 개념이 있으면, 더 세분화

**예시** (설계가 나쁜 경우):
```
원본 Step 2: {
  "concepts": ["분자", "분모", "분수 크기 비교"]
}

개선: Step 2-1: "분자 개념"
     Step 2-2: "분모 개념"
     Step 2-3: "분수 크기 비교"
```

---

### Step 5: 필요하면 이전 선수개념으로 돌아갈 수 있게 저장한다

**목표**: 학생이 막혔을 때 역추적 가능하게 설계

**저장 구조**:
```json
{
  "steps": [
    {
      "step": 1,
      "concept_id": "CONCEPT_FRAC_001",
      "name": "분수 개념",
      "previous_concept": null,
      "next_concept": "CONCEPT_FRAC_002"
    },
    {
      "step": 2,
      "concept_id": "CONCEPT_FRAC_002",
      "name": "분자/분모",
      "previous_concept": "CONCEPT_FRAC_001",
      "next_concept": "CONCEPT_FRAC_003"
    },
    ...
  ]
}
```

**역추적 예시**:
```
학생이 Step 3에서 계속 틀림
→ Step 2로 돌아가기
→ Step 2도 못하면 Step 1로 돌아가기
→ 그래도 안 되면 선수개념 추출해서 역진행

선수개념 참조:
prerequisite_concepts: ["CONCEPT_기초1", "CONCEPT_기초2"]
→ 필요하면 "분수 나누기 기본" 같은 선수개념으로 먼저 진행
```

---

## 4. 스텝 설계 원칙

### 스몰스텝의 특징

| 특징 | 설명 |
|------|------|
| **진행도 30%** | 한 스텝은 전체 진도의 약 20~30% 수준 |
| **인지 부하 최소** | 한 스텝에서 배운 게 다음 스텝의 선수개념 |
| **피드백 루프** | 각 스텝 후 미니퀴즈로 즉시 확인 |
| **실패 가능성 낮음** | 잘 설계되면 학생이 성공 경험 많음 |

---

## 5. 출력 형식

```
[학생에게 보이는 학습 경로 안내]
[자연스러운 대화 톤]

---
STEPS: [스텝 배열]
CURRENT_STEP: [현재 시작 스텝 번호]
STATUS: 과제분석 완료
NEXT: 첫 번째 스텝 개념설명으로 이동
STATE_UPDATE:
{
  "lesson": "진도명",
  "lesson_id": "ID",
  "start_level": 1|2|3,
  "total_steps_in_lesson": 정수 (원본),
  "assigned_steps": 정수 (START_LEVEL 맞춰 조정),
  "current_step": 정수,
  "steps": [
    {
      "step": 1,
      "concept_id": "ID",
      "concept_name": "개념명",
      "learning_objective": "학습목표",
      "previous_step": null|정수,
      "next_step": 정수|null
    },
    ...
  ],
  "prerequisite_concepts_used": boolean,
  "learning_path": ["Step1", "Step2", "Step3", ...],
  "next_state": "스텝설명"
}
```

---

## 6. 필드 상세 설명

| 필드 | 설명 | 예시 |
|------|------|------|
| **STEPS** | 이번 세션에서 다룰 스텝 배열 | [1, 2, 3, 4] |
| **CURRENT_STEP** | 현재 시작 스텝 | 1 또는 3 |
| **STATUS** | 현재 상태 | "과제분석 완료" |
| **NEXT** | 다음 액션 | "첫 번째 스텝 개념설명" |
| **total_steps_in_lesson** | 원본 lesson-units의 스텝 수 | 5 |
| **assigned_steps** | START_LEVEL에 맞춰 조정된 스텝 수 | 3 또는 4 |
| **current_step** | 현재 시작점 | 1 |
| **steps** | 상세 스텝 정보 배열 | [{step: 1, concept_id: "...", ...}, ...] |
| **prerequisite_concepts_used** | 선수개념 추가 여부 | true/false |
| **learning_path** | 전체 학습 경로 (시각화용) | ["분수 개념", "분자/분모", ...] |

---

## 7. 진도별 스텝 분해 예시

### 예시 1: 중1 수학 - 분수의 덧셈 (START_LEVEL = 2)

**원본 스텝** (lesson-units.json):
```json
{
  "lesson": "분수의 덧셈",
  "steps": [
    {"step": 1, "concept_id": "FRAC_001", "name": "분수 개념"},
    {"step": 2, "concept_id": "FRAC_002", "name": "분자와 분모"},
    {"step": 3, "concept_id": "FRAC_003", "name": "같은 분모"},
    {"step": 4, "concept_id": "FRAC_004", "name": "덧셈 규칙"},
    {"step": 5, "concept_id": "FRAC_005", "name": "연습"}
  ]
}
```

**START_LEVEL = 2 적용**:
- 원본: 5개 스텝
- 조정: 3~4개 스텝 사용
- CURRENT_STEP: 1 (처음부터 시작)

**출력**:
```
좋아, 분수는 대체로 이해하는데 덧셈이 조금 어렵구나.
그럼 다음 과정으로 배워보자:
1단계: 분자와 분모 다시 정리
2단계: 같은 분모를 가진 분수
3단계: 분수 덧셈 규칙

자, 1단계부터 시작해보자.

---
STEPS: [1, 2, 3, 4]
CURRENT_STEP: 1
STATUS: 과제분석 완료
NEXT: 첫 번째 스텝 개념설명 (분자와 분모)
STATE_UPDATE:
{
  "lesson": "분수의 덧셈",
  "start_level": 2,
  "total_steps_in_lesson": 5,
  "assigned_steps": 4,
  "current_step": 1,
  "steps": [
    {"step": 1, "concept_id": "FRAC_002", "concept_name": "분자와 분모"},
    {"step": 2, "concept_id": "FRAC_003", "concept_name": "같은 분모"},
    {"step": 3, "concept_id": "FRAC_004", "concept_name": "덧셈 규칙"},
    {"step": 4, "concept_id": "FRAC_005", "concept_name": "연습"}
  ],
  "learning_path": ["분자와 분모", "같은 분모", "덧셈 규칙", "연습"],
  "next_state": "스텝설명"
}
```

---

### 예시 2: 중1 수학 - 분수의 덧셈 (START_LEVEL = 1)

**원본 스텝**:
```
[Step 1, Step 2, Step 3, Step 4, Step 5]
```

**START_LEVEL = 1 적용**:
- 원본: 5개 스텝
- 조정: 5~6개 스텝 (선수개념 추가)
- CURRENT_STEP: 선수1 (기초부터)

**출력**:
```
분수가 처음이거나 아직 어렵구나. 
그럼 가장 작은 조각부터 천천히 해보자:
사전 1단계: 나누기 기본 (6÷3은?)
사전 2단계: 전체와 부분 개념
1단계: 분수 개념
2단계: 분자와 분모
3단계: 같은 분모
4단계: 덧셈 규칙

자, 사전 1단계부터 시작해보자.

---
STEPS: [pre1, pre2, 1, 2, 3, 4]
CURRENT_STEP: pre1
STATUS: 과제분석 완료
NEXT: 사전 1단계 개념설명
STATE_UPDATE:
{
  "lesson": "분수의 덧셈",
  "start_level": 1,
  "total_steps_in_lesson": 5,
  "assigned_steps": 6,
  "current_step": 0 (pre1로 표현),
  "prerequisite_concepts_used": true,
  "steps": [
    {"step": 0, "concept_id": "PREREQ_001", "concept_name": "나누기 기본"},
    {"step": -1, "concept_id": "PREREQ_002", "concept_name": "전체와 부분"},
    {"step": 1, "concept_id": "FRAC_001", "concept_name": "분수 개념"},
    ...
  ],
  "learning_path": ["나누기 기본", "전체와 부분", "분수 개념", ...],
  "next_state": "스텝설명"
}
```

---

### 예시 3: 중1 국어 - 부사절 접속사 (START_LEVEL = 3)

**원본 스텝**:
```
[Step 1: 접속사란, Step 2: 접속사 ~서, Step 3: 접속사 ~는데, 
 Step 4: 접속사 ~면, Step 5: 복합 연습]
```

**START_LEVEL = 3 적용**:
- 원본: 5개 스텝
- 조정: 2~3개 스텝 (핵심만)
- CURRENT_STEP: 2 (중간부터 시작)

**출력**:
```
접속사 개념은 이미 알고 있으니까, 
각 접속사의 역할을 정확히 정리해보자:
1단계: 접속사 ~서 (이유)
2단계: 접속사 ~는데 (대조/배경)
3단계: 복합 연습

자, 1단계부터 시작해보자.

---
STEPS: [2, 3, 5]
CURRENT_STEP: 2
STATUS: 과제분석 완료
```

---

### 예시 4: 중1 영어 - 과거형 (START_LEVEL = 2)

**원본 스텝**:
```
[Step 1: 동사 기본형, Step 2: 규칙동사 과거형, Step 3: 불규칙동사,
 Step 4: 과거형 문장 만들기, Step 5: 연습]
```

**START_LEVEL = 2 적용**:
- 원본: 5개 스텝
- 조정: 4개 스텝
- CURRENT_STEP: 1

**출력**:
```
동사의 기본형은 알고 있으니까, 
과거형으로 변하는 방법을 배워보자:
1단계: 규칙동사 과거형 (-ed)
2단계: 불규칙동사 과거형 (went, ate...)
3단계: 과거형 문장 만들기
4단계: 연습

자, 1단계부터 시작해보자.

---
STEPS: [2, 3, 4, 5]
CURRENT_STEP: 2
```

---

## 8. START_LEVEL별 스텝 수 결정 테이블

| START_LEVEL | 특징 | 스텝 수 | 시간 | CURRENT_STEP |
|------------|------|--------|------|-------------|
| **3** | 거의 다 알음 | 2~3개 | 5~10분 | 중간~끝 |
| **2** | 일부 이해 | 3~4개 | 10~15분 | 처음 또는 중간 |
| **1** | 거의 모름 | 5~6개 + 선수개념 | 20~25분 | 선수개념 또는 처음 |

---

## 9. 체크리스트

### 스몰스텝 과제분석 Skill 실행 체크

- [ ] lesson-units.json에서 해당 진도를 찾았는가?
- [ ] 원본 스텝 수를 확인했는가?
- [ ] START_LEVEL에 따라 스텝 수를 조정했는가?
  - [ ] START_LEVEL = 1: 5~6개 (또는 + 선수개념)?
  - [ ] START_LEVEL = 2: 3~4개?
  - [ ] START_LEVEL = 3: 2~3개?
- [ ] 각 스텝이 1개 개념만 다루는가?
- [ ] CURRENT_STEP을 정확히 설정했는가?
- [ ] 필요하면 선수개념을 추가했는가?
- [ ] 각 스텝의 연결 관계(previous/next)를 저장했는가?
- [ ] learning_path를 생성했는가?
- [ ] 학생에게는 친근한 말로 경로를 설명했는가?
- [ ] STATUS를 "과제분석 완료"로 설정했는가?
- [ ] NEXT를 "첫 번째 스텝 개념설명"으로 설정했는가?

---

## 10. 주의사항

### 하면 안 되는 것

| 금지 사항 | 이유 |
|---------|------|
| lesson-units에 없는 스텝 추가 | 데이터 일관성 위반 |
| START_LEVEL 무시 | 학생 수준 맞춤 실패 |
| 한 스텝에 여러 개념 포함 | 마이크로러닝 위배 |
| 학생에게 "단계 1/5"라고 말하기 | 내부 상태 노출 |
| 스텝 순서 뒤바꾸기 | 선수개념 위반 |
| 이전/다음 스텝 참조 없이 설계 | 역추적 불가능 |

### 해야 하는 것

| 권장 사항 | 효과 |
|---------|------|
| lesson-units 데이터 기반 | 일관성, 신뢰성 |
| START_LEVEL 정확히 반영 | 학생 맞춤 경로 |
| 1개 개념 고집 | 이해도 향상 |
| 선수개념 참조 구조 유지 | 막히면 돌아갈 수 있음 |
| 스텝 연결 관계 명시 | 세션 간 연속성 |

---

## 11. 데이터 구조 예시

### lesson-units.json 형식

```json
{
  "lessons": [
    {
      "lesson_id": "LESSON_FRAC_ADD",
      "lesson": "분수의 덧셈",
      "school_level": "중등",
      "grade": "1학년",
      "subject": "수학",
      "prerequisite_concepts": ["CONCEPT_FRAC_001"],
      "steps": [
        {
          "step_id": 1,
          "step_number": 1,
          "concept_id": "CONCEPT_FRAC_BASIC",
          "concept_name": "분수 개념",
          "learning_objective": "분수란 전체를 같은 크기로 나눈 것",
          "keywords": ["전체", "나누기", "부분"]
        },
        {
          "step_id": 2,
          "step_number": 2,
          "concept_id": "CONCEPT_FRAC_PARTS",
          "concept_name": "분자와 분모",
          "learning_objective": "분자는 취한 부분, 분모는 전체 개수",
          "keywords": ["분자", "분모", "위", "아래"]
        },
        ...
      ]
    }
  ]
}
```

---

## 12. 다음 Skill 연결

과제분석 완료 후 → **개념설명 Skill**으로 연결

```
과제분석 (현재)
    ↓
[STEPS, CURRENT_STEP 전달]
    ↓
개념설명 (반복, 각 스텝마다)
[현재 스텝의 개념을 3줄 이하로 설명]
    ↓
자기말설명
    ↓
이해판단
    ↓
미니퀴즈
    ↓
(다음 스텝으로 또는 선수개념으로 분해)
```

---

## 13. 문서 변경 이력

| 날짜 | 버전 | 내용 |
|------|------|------|
| 2026-07-02 | 1.0 | 초판 작성 |

