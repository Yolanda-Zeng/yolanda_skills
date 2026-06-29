---
name: non-tech-code-reader-skill
description: "Use when a PM, QA, or operations stakeholder pastes code and wants to understand what it does in business language. Covers 8 output modes: business logic summary, requirement coverage check, change impact analysis, shareable doc generation, API interface decoding, code vs PRD gap analysis, tracking event interpretation, and config/feature flag explanation. Trigger when someone asks 'what does this code do', 'does our code support X', 'what would break if we change Y', 'explain this API', 'find the gap between code and PRD', 'what does this tracking event send', or 'what does this feature flag control'. Output language always follows the user's language."
---

# Code Reader — For Product, QA & Operations

Translate code into business-readable understanding for non-engineering stakeholders.
Covers 8 output modes mapped to the most common PM, QA, and operations scenarios.

Output language always follows the user's language — respond in whatever language the user writes in.

Every output mode ends with a visual diagram. Generate the diagram after the structured text output.
Choose the diagram type specified per mode. Keep diagrams simple and scannable — they should
complement the text, not repeat it.

---

## When to Use

- A PM or business stakeholder pastes code and wants to know what it does
- Someone asks "does our current code support requirement X?"
- Someone suspects code behavior does not match the PRD or design
- Someone wants to understand an API interface or field meanings
- Someone needs to understand tracking or analytics events
- Someone needs to understand a config, feature flag, or gradual rollout switch
- Someone wants to generate a shareable doc from code (for Notion, Confluence, PRD)
- Anyone asks "what would break if we changed X?"

---

## How It Works

### Overview

```mermaid
flowchart TD
    A([User pastes code]) --> B[Phase 1: Identify role\nPM · QA · Ops · Designer]
    B --> C[Phase 2: Identify goal\nWhat do they need to walk away with?]
    C --> D1[A — Business summary]
    C --> D2[B — Requirement check]
    C --> D3[C — Change impact]
    C --> D4[D — Shareable doc]
    C --> D5[E — Interface decoder]
    C --> D6[F — Code vs PRD gap]
    C --> D7[G — Tracking events]
    C --> D8[H — Config & switches]
    D1 & D2 & D3 & D4 & D5 & D6 & D7 & D8 --> OUT([Structured output + diagram\nin user's language])
```

---

### Phase 1: Identify Role and Goal

Before analyzing, infer or confirm two things from context:

**Role** (default to PM if unclear)

- Product Manager / Product Owner
- QA / Test Engineer
- Operations / Customer Success
- Designer or non-technical manager

**Goal** — route to the matching output mode:

| Goal                                       | Output Mode                    |
| ------------------------------------------ | ------------------------------ |
| Understand what the code does              | A — Business Logic Summary     |
| Check if a requirement is supported        | B — Requirement Coverage Check |
| Assess impact of a proposed change         | C — Change Impact Analysis     |
| Generate a shareable doc for the team      | D — Shareable Summary Doc      |
| Understand API interface or field meanings | E — Interface Field Decoder    |
| Find gaps between code and PRD             | F — Code vs PRD Gap Analysis   |
| Understand tracking or analytics events    | G — Tracking Code Interpreter  |
| Understand a config or feature flag        | H — Config & Switch Explainer  |

If the goal is ambiguous, ask one question: **"What are you trying to figure out from this code?"**

---

### Phase 2: Analyze the Code

Before generating output, identify:

- **Entry point**: what user action or system event triggers this code
- **Happy path**: what happens under normal conditions
- **Branch logic**: what conditions change the behavior (if/else, switch)
- **Error handling**: what happens when things go wrong
- **Hardcoded rules**: magic numbers, fixed strings, hidden business constraints
- **Scope**: is this a complete feature or a fragment? If a fragment, ask for context before proceeding

---

### Phase 3: Generate Output

Run the matching output mode. Each mode has two parts:

1. **Structured text** — tables, bullet points, findings
2. **Diagram** — a visual that makes the key insight immediately scannable

Always lead with business meaning — never with technical mechanism.

---

## Output Modes

### Mode A — Business Logic Summary

**Use when**: User wants to understand what a piece of code does in plain language.

**Text output**:

```
## What This Code Does

**Summary**
[One sentence in business language — no technical jargon]

**What triggers it**
[What user action or system event causes this code to run]

**Normal flow**
[Step-by-step: "When the user does X, the system does Y"]

**Business rules embedded in the code**
[Hardcoded rules, fixed values, or special cases — in plain language]

**Error handling**
| Situation | What the user sees |
|---|---|
| [case] | [message or behavior] |

**⚠️ PM should know**
[Hidden assumptions, product constraints, or technical debt that could affect decisions]
```

---

**Diagrams — Multi-Perspective Flow Suite**

Mode A always outputs the following diagrams in sequence. Each diagram must be preceded by a one-line label stating its perspective and what it reveals that the others do not.

---

**Diagram 1 — Happy Path + Branch Flowchart (主流程图)**

The primary flow diagram. Shows trigger → happy path → branches → error outcomes.
Always generated first. This is the anchor that the other diagrams reference.

```mermaid
flowchart TD
    T([User submits login]) --> V{Phone number\nregistered?}
    V -- No --> E1([Show: user not found])
    V -- Yes --> M{Login method?}
    M -- SMS code --> S{Code valid?}
    M -- Password --> P{Password correct?}
    S -- No --> E2([Show: code expired])
    S -- Yes --> OK
    P -- No --> F{Fail count ≥ 5?}
    P -- Yes --> OK
    F -- Yes --> E3([Lock account 24h])
    F -- No --> E4([Show: wrong password])
    OK([Issue 7-day token ✓])
```

---

**Diagram 2 — System Swimlane (系统维度泳道图)**

Shows which system owns each step. Reveals system boundaries, handoff points, and where cross-system dependencies exist.

Generate only if the code involves 2+ distinct systems, services, or modules. If not applicable, write: `> ℹ️ System swimlane not applicable — this code runs within a single system.`

Each swimlane = one system or service. Handoffs between swimlanes = integration points.

```mermaid
flowchart LR
    subgraph 采购系统
        A1[建立采购订单] --> A2[审核通过] --> A3[接收付款结果] --> A4[接收收货结果]
    end
    subgraph 财务系统
        B1[生成付款单据] --> B2[财务付款]
    end
    subgraph WMS系统
        C1[仓库接收采购单] --> C2[收货入库]
    end
    subgraph 库存系统
        D1[库存增加]
    end
    A2 --> B1
    B2 --> A3
    A3 --> C1
    C2 --> D1
    D1 --> A4
```

---

**Diagram 3 — Role Swimlane (角色维度泳道图)**

Shows who does what. Reveals accountability, handoffs between people, and steps that require cross-role coordination.

Generate only if the code involves actions by 2+ distinct roles or user types. If not applicable, write: `> ℹ️ Role swimlane not applicable — this code involves a single actor.`

Each swimlane = one role. Steps inside = that role's responsibility. Arrows crossing swimlanes = handoffs.

```mermaid
flowchart LR
    subgraph 采购专员
        R1[建立采购单据] --> R5[通知供应商发货]
    end
    subgraph 采购经理
        R2[审核采购单]
    end
    subgraph 财务人员
        R3[对供应商付款]
    end
    subgraph 仓库人员
        R4[收货并质检]
    end
    subgraph 供应商
        R6[发货]
    end
    R1 --> R2
    R2 -- 审核通过 --> R3
    R3 --> R5
    R5 --> R6
    R6 --> R4
```

---

**Diagram 4 — Timeline / Phase View (时间线阶段图)**

Shows the flow as ordered phases or stages. Reveals how long the process takes conceptually, where waiting/blocking happens, and what must complete before the next phase starts.

Generate only if the flow has meaningful sequential phases (e.g., approval → payment → fulfillment). If the code is a single atomic operation, write: `> ℹ️ Timeline view not applicable — this is a single-phase operation.`

```mermaid
flowchart LR
    PH1["📋 Phase 1\n订单创建\n采购专员建单\n经理审核"] --> PH2["💳 Phase 2\n财务付款\n生成付款单\n财务执行付款"]
    PH2 --> PH3["📦 Phase 3\n收货入库\n供应商发货\n仓库质检入库"]
    PH3 --> PH4["✅ Phase 4\n结果同步\n库存更新\n采购单关闭"]
    style PH1 fill:#dbeafe,stroke:#3b82f6
    style PH2 fill:#fef3c7,stroke:#d97706
    style PH3 fill:#d1fae5,stroke:#059669
    style PH4 fill:#ede9fe,stroke:#7c3aed
```

---

**Diagram 5 — AI-Identified Perspective (AI 自选补充视角)**

After generating Diagrams 1–4, assess whether there is an additional structural insight that none of the above diagrams captured. Generate this diagram only if a genuinely different perspective exists.

Candidates (pick the one most revealing for this specific code):
- **Data flow view**: what data objects are created, read, or modified at each step
- **Exception / error path map**: all error branches and fallback behaviors in one view
- **State machine**: how the core entity (order, user, ticket) transitions between states
- **Dependency map**: what this code depends on, and what depends on it

If no additional perspective adds meaningful insight beyond what Diagrams 1–4 already show, write:
`> ℹ️ No additional perspective needed — the four diagrams above cover the key structural views of this flow.`

Label the chosen perspective explicitly, e.g.:
`> 📊 Diagram 5 — State Machine View: shows how the purchase order status transitions throughout the process`

---

**Diagram generation rules for Mode A**:
- Always generate Diagram 1 (main flow). Never skip it.
- Generate Diagrams 2, 3, 4 unless explicitly inapplicable (use the ℹ️ note when skipping).
- Generate Diagram 5 only when it adds something the other four do not.
- Each diagram must be preceded by a one-line label: what perspective it represents and what unique insight it provides.
- Max 10 nodes per diagram. If the flow is more complex, show the critical path and note simplification.
- Never generate a diagram that merely repeats another diagram's content in a different layout.

---

### Mode B — Requirement Coverage Check

**Use when**: User has a product requirement and wants to know if the code already supports it.

If the requirement is not clearly stated, ask first: "Could you describe the expected user behavior or paste the requirement?"

**Text output**:

```
## Requirement Coverage Check

**Your requirement**
[Restate the requirement to confirm understanding]

**Verdict**
✅ Supported / ⚠️ Partially supported / ❌ Not supported

**What the current code supports**
[List]

**Gaps — what would need to be added**
[List, or "None" if fully supported]

**Potential risks**
[Side effects if the current code is forced to meet this requirement]
```

**Diagram — coverage bar broken into supported / partial / missing segments**:
Generate a Mermaid pie or a simple visual block showing the proportion of the requirement
that is already covered vs what is missing. If the requirement has multiple sub-items,
show each as a row with a ✅ / ⚠️ / ❌ status. Use a quadrant or checklist style:

```mermaid
graph LR
    subgraph Requirement coverage
    A[✅ Phone + password login] 
    B[✅ SMS verification]
    C[⚠️ Account lockout — no unlock flow]
    D[❌ 30-day remember me]
    end
```

---

### Mode C — Change Impact Analysis

**Use when**: User wants to assess the risk and scope of a proposed change.

**Text output**:

```
## Change Impact Analysis

**The change you want to make**
[Restate the proposed change]

**Direct impact**
[How this code's behavior would change]

**Downstream impact**
[Other features or modules that might be affected]

**Complexity estimate**
Low / Medium / High (with brief explanation)

**Questions to confirm with engineering**
[A list the PM can take directly to the developer]
```

**Diagram — ripple impact map showing direct vs downstream blast radius**:
Generate a Mermaid flowchart with the changed component at the center,
direct impacts in the first ring, and downstream impacts in the outer ring.
Use node styles to encode risk level (normal = low risk, highlighted = medium, bold border = high).

```mermaid
flowchart LR
    CHG[/"Change: token\nexpiry 7→30 days"/]
    CHG --> D1[generate_token\nfunction]
    CHG --> D2[Login response\npayload]
    D1 --> DS1[Token refresh logic]
    D1 --> DS2[Session management]
    D2 --> DS3[Frontend storage]
    D2 --> DS4[Logout flow]
    style CHG fill:#fef3c7,stroke:#d97706
    style DS1 fill:#fee2e2,stroke:#dc2626
    style DS4 fill:#fee2e2,stroke:#dc2626
```

---

### Mode D — Shareable Summary Doc

**Use when**: User wants a formatted document to paste into Notion, Confluence, or a PRD.
Trigger signals: "generate a doc", "write a summary", "share with the team", "send to QA / ops", "put this in Confluence".

**Text output** — clean Markdown document including:

- A note that this is AI-assisted and should be verified with engineering
- Code source (leave blank for user to fill in)
- Business logic summary (Mode A structure)
- Key assumptions and constraints
- Open questions — things the PM should confirm with the developer before treating this as final

**Diagram — embed the full Mode A Multi-Perspective Flow Suite inside the doc**:
Include all applicable diagrams from Mode A (Diagrams 1–5) directly in the Markdown doc.
Place them after the normal flow section, grouped under a heading:
`## 📊 Flow Diagrams (AI-generated — verify with engineering)`
Label each sub-diagram clearly so recipients who receive the document understand which perspective each diagram represents.

---

### Mode E — Interface Field Decoder

**Use when**: User pastes an API definition, JSON response, or interface schema and wants to
understand field meanings, which fields are required, and what the values represent.
Trigger signals: "what does this field mean", "explain this interface", "what does it return", "which fields are required", "explain this JSON".

**Text output**:

```
## Interface Field Decoder

**What this interface does**
[One sentence: what business action this API performs]

**Request fields**
| Field | Type | Required | Business meaning | Notes |
|---|---|---|---|---|
| [field] | [type] | Yes / No | [meaning] | [notes] |

**Response fields**
| Field | Business meaning | Possible values / enums | Notes |
|---|---|---|---|
| [field] | [meaning] | [enums] | [notes] |

**Fields to confirm with engineering**
[Fields with ambiguous names, unclear purpose, or unexpected values]

**⚠️ PM should know**
[e.g. hardcoded enums that limit extensibility; required fields the user cannot control]
```

**Diagram — request → response data flow showing key fields at each stage**:
Generate a Mermaid sequence diagram or left-to-right flowchart showing:

- What the caller sends (key request fields)
- What the system does in the middle (one-line description)
- What comes back (key response fields, including enum values where relevant)

```mermaid
sequenceDiagram
    participant U as Caller
    participant S as System
    U->>S: POST /login\nphone, password|sms_code
    S->>S: Verify identity\nCheck lockout rules
    S-->>U: 200 OK\ntoken (7d), user_id
    S-->>U: 400 Error\nerror_code, message
```

---

### Mode F — Code vs PRD Gap Analysis

**Use when**: User suspects the code behavior does not match the PRD, design doc, or stated product intent.
Trigger signals: "code doesn't match the PRD", "the live behavior is different from the design", "this isn't what we specified", "find the gap".

Always require both the code AND the PRD before proceeding. If the PRD is missing, ask:
"Could you paste the relevant PRD section, or describe what the intended behavior was?"
If the PRD is vague, note it explicitly: "The PRD wording here is ambiguous — the analysis below is based on reasonable interpretation. Please confirm with the team."

Decision flow for Mode F:

```mermaid
flowchart TD
    F0([User suspects code vs PRD mismatch]) --> F1{PRD provided?}
    F1 -- No --> F2[Ask: paste PRD section\nor describe intended behavior]
    F2 --> F1
    F1 -- Yes --> F3{PRD clear enough\nto compare?}
    F3 -- No --> F4[Note ambiguity explicitly\nProceed with caveats]
    F3 -- Yes --> F5[Compare systematically:\nhappy path · edge cases · errors]
    F4 --> F5
    F5 --> F6[Classify each gap:\nMissing · Extra · Different · Unclear]
    F6 --> F7([Output gap table +\nrecommended next steps])
```

**Text output**:

```
## Code vs PRD Gap Analysis

**Scope**
[Which feature or flow this code corresponds to in the PRD]

**Aligned** ✅
[Where the code behavior matches the PRD]

**Gaps** ❌
| Dimension | PRD requirement | Actual code behavior | Gap type |
|---|---|---|---|
| [dimension] | [PRD says] | [code does] | Missing / Extra / Different / Unclear |

Gap type key:
- **Missing**: PRD requires it, code does not implement it
- **Extra**: Code does something the PRD did not specify (may be intentional — confirm)
- **Different**: Implementation differs from the PRD description
- **Unclear**: Cannot determine from code alone — needs engineering clarification

**Recommended next steps**
[Which gaps need a sync meeting / which need a PRD update / which need an engineering fix]
```

**Diagram — side-by-side PRD vs code comparison with gap markers**:
Generate a Mermaid flowchart with two parallel swim lanes — PRD intent on the left,
code behavior on the right. Connect matching steps with a solid line (✅ aligned)
and mismatched steps with a dashed line and gap label (❌ Missing / ⚠️ Different).

```mermaid
flowchart LR
    subgraph PRD ["📋 PRD intent"]
        P1[Apply discount\nif order > $100]
        P2[Show savings\namount to user]
        P3[Log discount\nevent]
    end
    subgraph Code ["💻 Code behavior"]
        C1[Apply discount\nif order > $100]
        C2[❌ Not implemented]
        C3[⚠️ Logs order total\nnot discount amount]
    end
    P1 --- C1
    P2 -. Missing .-> C2
    P3 -. Different .-> C3
```

---

### Mode G — Tracking Code Interpreter

**Use when**: User pastes tracking or analytics code and wants to understand when events fire,
what data gets sent, and whether it supports their analytics or reporting needs.
Trigger signals: "tracking", "analytics event", "what does this event send", "when does this fire", "埋点".

**Text output**:

```
## Tracking Code Interpreter

**Event inventory**
| Event name | When it fires | Properties sent | Business meaning |
|---|---|---|---|
| [event] | [trigger action] | [property list] | [meaning] |

**⚠️ Data quality risks**
[Fields that may be null in certain scenarios; events that may fire multiple times; enums that are not exhaustive]

**Coverage against your analytics needs**
[If the user stated an analytics goal, assess whether the current tracking supports it.
Otherwise: "If you want to measure X, the current tracking is / is not sufficient because..."]
```

**Diagram — event timeline showing trigger sequence and data payload**:
Generate a Mermaid sequence diagram showing the user actions down the left,
each tracking event firing as a message to an "Analytics" receiver,
with the key properties shown inline. Highlight any data quality risks with a note.

```mermaid
sequenceDiagram
    participant U as User
    participant A as App
    participant T as Analytics

    U->>A: Opens product page
    A->>T: page_view\n{page_id, user_id, referrer}

    U->>A: Clicks "Add to cart"
    A->>T: add_to_cart\n{item_id, price, quantity}

    U->>A: Reaches checkout
    A->>T: begin_checkout\n{cart_total, item_count}
    Note over T: ⚠️ user_id may be null\nif not logged in
```

---

### Mode H — Config & Switch Explainer

**Use when**: User pastes config files, feature flag logic, or gradual rollout switch code and
wants to understand what it controls, who it affects, and what changing it would do.
Trigger signals: "what does this config do", "what does this flag control", "what happens if I change this", "which users are affected", "feature flag", "gradual rollout".

**Text output**:

```
## Config & Switch Explainer

**Config inventory**
| Key | Current value | What it controls | Affected user scope |
|---|---|---|---|
| [key] | [value] | [description] | [all users / % rollout / specific segment] |

**What happens if you change it**
[For each config the user is asking about, describe the business effect of changing it]

**⚠️ High-risk configs**
[Configs where a mistake would have large blast radius, or where rollback is difficult]

**Before you change it — confirm**
[Who to align with / what to test / whether a staged rollout is recommended]
```

**Diagram — user population breakdown showing who each config affects**:
Generate a Mermaid pie chart or flowchart showing the current rollout scope
for each key config — what percentage or segment of users sees each variant,
and what changes for them under each setting. For multi-config scenarios,
show a decision tree of how configs combine to determine user experience.

```mermaid
flowchart TD
    ALL([All users]) --> FF{new_checkout_flow\n= true?}
    FF -- Yes 30% --> NF[New checkout UI\n+ streamlined flow]
    FF -- No 70% --> OF[Legacy checkout UI]
    NF --> PF{express_pay\nenabled?}
    PF -- Yes --> EP[One-tap payment\navailable]
    PF -- No --> SP[Standard payment\nflow]
    style NF fill:#d1fae5,stroke:#059669
    style EP fill:#d1fae5,stroke:#059669
```

---

## Best Practices

1. **Business meaning first** — always explain what the code means for the product before explaining how it works technically
2. **Multi-perspective diagrams in Mode A** — always generate the full diagram suite (Diagrams 1–4 by default, Diagram 5 when it adds genuine insight); never produce only one diagram when multiple perspectives exist
3. **Diagrams complement, not repeat** — each diagram must show something the others cannot: flow, system boundary, role accountability, time sequence, or state. Never generate two diagrams that convey the same structural insight
4. **Surface hidden constraints proactively** — hardcoded business rules, fixed enums, configs without rollback: these are the highest-value insights for PMs and ops; do not wait to be asked
5. **Flag uncertainty explicitly** — when code is ambiguous or incomplete, say "My interpretation is X — recommend confirming with engineering" rather than stating it as fact
6. **Mode F requires both inputs** — never attempt a gap analysis without the PRD; inferring product intent from code alone produces unreliable results
7. **Keep diagrams simple** — max 10 nodes per diagram; if the code is more complex, show the most important path and note that detail was simplified

---

## Anti-Patterns to Avoid

- Using unexplained technical terms (API, async, null, schema, ORM, middleware) in output without definition
- Leaving the "⚠️ PM should know" section empty or superficial — this is often the most actionable part
- Running Mode F without a PRD and guessing the intended behavior instead
- Returning a large code block as the primary answer
- Expressing higher confidence than the code actually supports ("this definitely does X")
- **Generating only one diagram in Mode A** — a single main flow diagram is insufficient; always produce the full multi-perspective suite
- Generating a diagram that just mirrors the text as boxes — diagrams must add visual insight not present in the text
- Skipping the system or role swimlane without an explicit ℹ️ note explaining why it is not applicable

---

## Examples

### Example 1: PM wants to understand a feature

**User**: "Here's our checkout flow code — can you tell me what it does?"
**Action**: Mode A — business logic summary + full multi-perspective diagram suite
**Output**: Trigger + normal flow + error table + ⚠️ PM should know + Diagram 1 (main flow) + Diagram 2 (system swimlane) + Diagram 3 (role swimlane) + Diagram 4 (phase timeline) + Diagram 5 if applicable

### Example 2: Checking if a requirement is already supported

**User**: "Does our current login code support phone number + SMS verification?"
**Action**: Mode B — coverage check + per-item status visual
**Output**: Verdict + supported parts + gaps + coverage checklist diagram

### Example 3: Assessing the impact of a change

**User**: "We want to extend the login token from 7 days to 30 days — how big a change is that?"
**Action**: Mode C — impact analysis + ripple map diagram
**Output**: Direct + downstream impact + complexity + questions for engineering + impact map

### Example 4: Reading an API interface

**User**: "The developer sent over an API spec — can you explain what these fields mean?"
**Action**: Mode E — field decoder + request/response sequence diagram
**Output**: Field tables + fields to confirm + sequence diagram

### Example 5: Finding gaps between code and PRD

**User**: "My PRD says discount applies above $100, but the live behavior seems different — can you find the gap?"
**Action**: Mode F — gap analysis + PRD vs code swim lane diagram
**Output**: Aligned sections + gap table + side-by-side comparison diagram

### Example 6: Generating a team-facing document

**User**: "This is our payment flow code — generate a summary I can send to QA and operations"
**Action**: Mode D — full Markdown doc with embedded multi-perspective diagram suite
**Output**: Complete shareable doc including all applicable flow diagrams from the Mode A suite

### Example 7: Understanding tracking events

**User**: "What does our purchase tracking code actually send?"
**Action**: Mode G — event inventory + analytics sequence diagram
**Output**: Event table + data quality risks + trigger sequence diagram

### Example 8: Understanding a feature flag

**User**: "What does this feature flag control and who does it affect right now?"
**Action**: Mode H — config explainer + user population breakdown diagram
**Output**: Config table + change impact + rollout scope diagram
