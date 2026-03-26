# 2장: CLAUDE.md 설계 — 컨텍스트 엔지니어링 실전

> CLAUDE.md는 설정 파일이 아닙니다. **에이전트에게 사고 방식을 가르치는 설계 문서**입니다.

## TL;DR

효과적인 에이전트의 진짜 역량은 컨텍스트 엔지니어링입니다. 이 장에서는 하나의 CLAUDE.md가 14일 동안 20회 이상 수정을 거치며 어떻게 진화했는지 추적합니다 — 175줄짜리 워크스페이스 지도에서 시작해, 모호함을 처리하고, 리스크를 관리하고, 피드백에서 학습하는 330줄짜리 운영 체제가 되기까지. 이 진화의 세 단계를 따라가면, 누구나 적용할 수 있는 패턴이 보입니다.

---

## 설정 파일의 함정

대부분의 사람들이 CLAUDE.md를 이렇게 씁니다:

```markdown
# Project: My App
- Language: TypeScript
- Framework: Next.js
- Use tabs, not spaces
- Run tests before committing
```

작동합니다. 에이전트가 지시를 따릅니다. 하지만 이 접근법에는 천장이 있습니다. 단일 리포지토리에서 코드를 쓰는 것보다 조금이라도 복잡한 일을 하는 순간 부딪히게 됩니다.

문제는 프레이밍입니다. 설정 파일은 *"여기 설정값들이 있어"*라고 말합니다. 헌법은 *"이렇게 운영해 — 모호하거나, 위험하거나, 처음 보는 상황일 때까지 포함해서"*라고 말합니다.

이 프레이밍 차이는 철학적인 게 아닙니다. 실제 업무의 80% — 설정 목록에 깔끔하게 들어맞지 않는 부분 — 를 에이전트가 처리할 수 있느냐 없느냐를 결정합니다.

## 하나의 CLAUDE.md, 세 단계, 14일

여기서 보여드리는 것은 개인 AI 에이전트 시스템(1장의 166개 자산 시스템과 동일)을 관리하는 CLAUDE.md의 실제 진화 과정입니다. 실제 git diff를 보여드리겠습니다 — 무엇이 추가되었고, 더 중요하게는 *왜* 추가되었는지.

세 단계는 1장의 조직 설계 비유와 대응됩니다:

| 단계 | 조직 비유 | CLAUDE.md 역할 | 줄 수 |
|------|---------|---------------|------|
| **V1: 지도** | 사무실 배치도 + 조직도 | "무엇이 있고 어디에 두는지" | 175 |
| **V2: 가드레일** | 안전 매뉴얼 + 컴플라이언스 정책 | "무엇을 깨뜨리지 말아야 하는지" | 206 |
| **V3: 인지** | 관리자 교육 프로그램 | "어떻게 사고하고 판단하는지" | 254 -> 330+ |

하나씩 따라가 보겠습니다.

---

## V1: 지도 (Day 1)

첫 번째 버전은 하나의 질문에 답했습니다: **"이 워크스페이스는 무엇인가?"**

```markdown
## Folder Structure (PARA)
| Folder     | Purpose                                    |
|------------|-------------------------------------------|
| 000_System | Templates, scripts, catalogs               |
| 100_Inbox  | Collection (source_type tags)               |
| 200_Projects | Short-term projects (Active->Done)         |
| ...        | ...                                        |

## Tool Roles
| Tool        | Role                                       |
|-------------|-------------------------------------------|
| Obsidian    | Single source of truth                     |
| n8n         | Pipeline -- collection -> processing         |
| Claude Code | Builder + operator -- reasoning, generation |
| Telegram    | Interface -- input, alerts, conversations   |

## Judgment Framework
- 5-axis weights: Consistency(0.34) > Feasibility(0.26) > ...
- Gates: P0 < 2.0 -> REJECT
```

**잘한 것**: 영역이 명확하게 매핑되었습니다. 에이전트는 파일이 어디에 있는지, 각 도구가 무엇을 하는지, 의사결정의 가중치가 어떻게 되는지 알게 되었습니다.

**빠진 것**: *리스크* 개념이 없었습니다. 에이전트는 파일 이름 변경과 프로덕션 워크플로우 수정을 같은 무게로 처리했습니다. *모호함* 개념도 없었습니다 — 사용자 의도가 불분명할 때, 에이전트는 추측하거나 일반적인 질문을 던지는 것밖에 할 수 없었습니다.

조직 설계로 비유하면: 배치도와 조직도는 있었지만 안전 프로토콜과 관리자 교육은 없었습니다. 모두가 책상이 어디인지는 알았습니다. 문제가 생겼을 때 어떻게 해야 하는지는 아무도 몰랐습니다.

**V2를 촉발한 사건**: 에이전트가 구조 개편 작업 중 확인 없이 파일들을 삭제했습니다. 개별 삭제는 각각 논리적이었습니다. 하지만 모아놓고 보면, 세 개의 상호 참조가 깨졌고 복구에 한 시간이 걸렸습니다. 시스템에 *되돌림 가능성*이라는 개념이 필요했습니다.

---

## V2: 가드레일 (Day 10)

V2는 31줄을 추가했습니다. 하지만 그 31줄이 에이전트의 행동을 근본적으로 바꿨습니다. 핵심 추가 사항: **Action Risk Gate**.

```markdown
## Verification Layer (Action Risk Gate)

| Level | Action                        | Gate              |
|-------|------------------------------|-------------------|
| L1-L2 | Read, local reversible change | Autonomous        |
| L3    | Delete, structural change     | Confirm first     |
| L4    | External system change (n8n)  | Backup -> Confirm  |
| L5    | External publish, cost-incurring | HITL required  |

Core question: "Can I undo this in 5 minutes?" + "Does this affect anything outside the vault?"
```

두 번째 주요 추가 사항: **피드백 루프가 내장된 도구 선택 매트릭스**.

```markdown
### Web/Browser Tool Selection Matrix

| Task                  | Primary         | Fallback        | Never Use       |
|-----------------------|-----------------|-----------------|-----------------|
| URL content extraction| firecrawl scrape| playwright-cli  | --               |
| Web search            | firecrawl search| --               | playwright-cli  |
| Login-required page   | playwright-cli  | firecrawl browser| firecrawl scrape|
| ...                   | ...             | ...             | ...             |

> Tool selection feedback loop: auto-review at session wrap
> -> accumulate in tool-selection-feedback.jsonl -> golden set at 10+ entries
```

**바뀐 것**: 에이전트에게 *영향 반경(blast radius)* 개념이 생겼습니다. 프로덕션 워크플로우를 수정하기 전에 백업을 만들게 되었습니다. 파일을 삭제하기 전에 물어보게 되었습니다. "Never Use" 열은 사일런트 장애를 일으키던 도구 미스매치를 방지했습니다.

**설계 원칙**: V2의 모든 규칙은 특정 사고로 추적 가능합니다. "L4 변경 전 백업"은 워크플로우 편집이 실행 중인 프로세스를 손상시킨 적이 있기 때문에 존재합니다. "L3 전 확인"은 앞서 말한 파일 삭제 사건 때문에 존재합니다. **좋은 가드레일은 발명하는 게 아닙니다. 포스트모템에서 추출하는 겁니다.**

조직 설계로 비유하면: 이제 안전 매뉴얼이 생겼습니다. 직원들은 어떤 행동에 상사의 결재가 필요하고 어떤 것을 자율적으로 처리할 수 있는지 알게 되었습니다. "5분 안에 되돌릴 수 있는가?"라는 테스트는 모두에게 리스크 평가를 위한 멘탈 모델을 제공했습니다.

**V3를 촉발한 사건**: 가드레일은 *명확한* 상황에서는 잘 작동했습니다. 하지만 에이전트는 여전히 *모호한* 상황에서 헤맸습니다. 제가 "정리해"라고 입력하면 — 인박스를 정리하라는 건지? 문서를 포맷하라는 건지? 오래된 프로젝트를 아카이브하라는 건지? 에이전트는 하나의 해석을 골라서 밀어붙였고, 때로는 맞았지만 때로는 재앙이었습니다. 행동 *전에* 모호함을 처리하는 방법이 필요했습니다.

---

## V3: 인지 (Day 12)

V3는 **Cognitive Twin 프로토콜**을 추가했습니다 — 에이전트에게 모호함을 해석하고 판단을 내리는 능력을 부여한 48줄입니다.

### Decoder: 모호함 해소

```markdown
### Decoder (Ambiguity Resolution)

When user input meets these conditions:
- Length <= 20 chars OR no explicit verb OR unclear target

Execute Resolution Cascade:
1. Phase A (internal graph walk): Match against Cognitive Patterns
   -> Check decisions.md for similar situations
   -> Reference Permanent Notes -> Recent conversation context
2. confidence >= 0.85 -> Auto-execute. Display "[interpretation] -- proceeding"
3. 0.60 <= confidence < 0.85 -> Soft confirm. "Did you mean X? (based on AP-NNN)"
4. confidence < 0.60 -> Ask directly
5. Phase B (HITL): If Phase A insufficient -> present candidates with reasoning
```

**왜 중요한가**: Decoder 이전에 에이전트에게는 두 가지 모드만 있었습니다 — "말한 대로 정확히 하기"와 "명확화 질문하기". Decoder가 세 번째 모드를 추가했습니다: **"축적된 맥락을 기반으로 해석하되, 확신도를 보정하라."** 0.85 임계값은 에이전트가 60%의 경우 자율적으로 행동하면서 — 진짜 모를 때는 물어보게 합니다.

### Predictor: 판단 내리기

```markdown
### Predictor (50:50 Decisions)

When two equal options are detected during work
(task order, scope, approach, tool -- no technical right answer):
1. Match against Cognitive Patterns (PR-*.md)
2. confidence >= 0.80 -> Choose + 1-line rationale.
   "Choosing [X] -- matches your usual pattern (PR-NNN)"
3. confidence < 0.80 -> Present options + mark recommendation
```

**왜 중요한가**: 복잡한 작업에는 두 가지 접근이 동등하게 유효한 순간이 반드시 있습니다. Predictor 이전에 에이전트는 멈추거나("어느 쪽을 선호하시나요?") 무작위로 골랐습니다. 이제는 37개의 문서화된 의사결정 패턴을 참조해서 사용자와 같은 선택을 하고 — 그 근거를 설명합니다.

### 피드백 루프: 교정에서 학습하기

```markdown
### Feedback Capture (automatic write-back)

- Accepted: No correction -> evidence_count += 1, confidence recalculated
- Corrected: User changes outcome -> log override + add counter-example
- Override 3+ times -> suggest rule split
```

**설계 원칙**: V3는 CLAUDE.md를 정적 문서에서 **학습하는 시스템**으로 전환했습니다. 에이전트의 해석이 맞으면 해당 패턴의 확신도가 올라갑니다. 교정을 받으면 *왜* 틀렸는지를 기록합니다. 교정이 충분히 쌓이면 패턴을 더 세분화된 규칙으로 분리하자고 제안합니다.

조직 설계로 비유하면: V1이 사무실 배치도였고, V2가 안전 매뉴얼이었다면, V3는 관리자 교육 프로그램입니다 — 에이전트에게 무엇을 할지만이 아니라, *무엇을 할지에 대해 어떻게 사고하는지*, 그리고 *실수에서 어떻게 배우는지*를 가르치는 것입니다.

---

## 계속 진화하는 것들

CLAUDE.md는 V3에서 멈추지 않았습니다. 이후 수정에서 추가된 것들:

- **Confidence Decay**: 30일 이상 사용되지 않은 패턴은 자동으로 확신도가 감소합니다 (낡은 지식이 판단을 좌우해서는 안 됩니다)
- **Devil's Advocate**: Predictor의 확신도가 너무 높을 때, "깨질 조건"에 해당하는지 확인하고 반대 의견을 제시합니다
- **Active Unlearning**: 90% 이상 적용된 패턴은 플래그됩니다 — 과적용도 실패 모드입니다
- **Techno-Reflexivity 체크**: 새 자동화를 만들기 전 세 가지 질문: *이것은 무엇을 전제하는가? 무엇을 숨기는가? 실패하면 누가 알 수 있는가?*

각 추가 사항은 특정 운영 실패에 의해 촉발되었습니다. 이 문서는 현재 330줄 이상으로 — 원래 크기의 거의 두 배 — 하지만 모든 줄이 제 몫을 합니다.

---

## CLAUDE.md를 위한 다섯 가지 설계 원칙

20회 이상의 수정에서 배운 것을 정리하면 이렇습니다:

### 1. 지도로 시작해서 헌법으로 진화시키세요

첫 버전은 이 질문에 답해야 합니다: "이 워크스페이스는 무엇인가?" 폴더 구조, 도구 역할, 파일 위치. 모든 것을 예측하려 하지 마세요. **가드레일은 경험에서 옵니다.**

### 2. 모든 규칙은 실패로 추적 가능해야 합니다

규칙이 방지하는 특정 사건을 가리킬 수 없다면, 그 규칙은 아마 불필요합니다. Action Risk Gate는 파일이 삭제되었기 때문에 존재합니다. Decoder는 모호한 입력이 잘못 해석되었기 때문에 존재합니다. 선제적 규칙은 대개 틀립니다 — 실제 실패에서 추출된 반응적 규칙이 대개 맞습니다.

### 3. 지시가 아니라 판단력을 인코딩하세요

"탭을 쓰세요"와 "모호한 요청이 오면, 추측하기 전에 37개 패턴을 확인하세요" 사이의 차이가 설정 파일과 설계 문서의 차이입니다. **지시는 에이전트에게 무엇을 할지 알려줍니다. 판단 프레임워크는 어떻게 결정할지를 알려줍니다.**

### 4. 점진적 자율성을 설계하세요

모든 행동에 같은 수준의 감독이 필요하지 않습니다. L1-L5 리스크 분류는 에이전트가 일상적 작업을 독립적으로 처리하면서 새롭거나 위험한 결정은 상위로 올리게 합니다. Cognitive Twin의 확신도 임계값도 해석에 대해 같은 역할을 합니다. **이진적 통제가 아닌, 단계적 신뢰를 설계하세요.**

### 5. 살아있는 문서로 만드세요

변하지 않는 CLAUDE.md는 완벽하거나(가능성 낮음) 낡았거나(가능성 높음) 둘 중 하나입니다. 피드백 루프를 구축하세요: 도구 선택 리뷰, 패턴 확신도 감쇠, 교정 추적. **문서는 시스템이 운영됨에 따라 더 똑똑해져야 합니다.**

---

## 스타터 템플릿

직접 CLAUDE.md를 만들어보고 싶다면, 이 리포지토리에 [스타터 템플릿](../templates/CLAUDE-md-starter.md)을 포함해 뒀습니다. V1의 핵심 요소 — 폴더 구조, 도구 역할, 기본 운영 규칙 — 을 다룹니다. 시스템을 운영하고 실패를 겪으면서, 자연스럽게 V2(가드레일)와 V3(인지)로 진화하게 될 겁니다.

템플릿은 의도적으로 최소한입니다. **최고의 CLAUDE.md는 누군가에게서 복사한 것이 아니라, 경험을 통해 직접 키운 것입니다.**

---

## 핵심 정리

설정 파일은 평면적입니다: 설정을 넣으면 동작이 나옵니다. 잘 설계된 CLAUDE.md는 *계층적*입니다:

```
Layer 1 (지도):       무엇이 있는가? 어디에 두는가?
Layer 2 (가드레일):   무엇이 위험한가? 어떻게 안전을 지키는가?
Layer 3 (인지):       어떻게 해석하는가? 어떻게 판단하는가? 어떻게 학습하는가?
```

각 레이어는 아래 레이어 위에 쌓입니다. 영역을 모르면 좋은 가드레일을 쓸 수 없습니다. 리스크를 모르면 판단력을 가르칠 수 없습니다. **지도에서 시작하세요. 나머지는 실패가 가르쳐줄 겁니다.**

---

**이전: [1장 — 왜 설계 능력인가?](ch01-why-design-ability.md)** — "에이전트를 잘 설계하는 것"이 AI 시대의 핵심 역량인 이유.

**다음: [3장 — 스킬 아키텍처](ch03-skill-architecture.md)** — 좋은 스킬과 나쁜 스킬의 차이는? 20개 프로덕션 스킬에서 추출한 패턴 비교.
