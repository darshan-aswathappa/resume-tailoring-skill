# Content Matching Strategies

## Overview

Match experiences from library to template slots with transparent confidence scoring.

## Matching Criteria (Weighted)

**1. Direct Match (40%)**
- Keywords overlap with JD/success profile
- Same domain/technology mentioned
- Same type of outcome required
- Same scale or complexity level

**Scoring:**
- 90-100%: Exact match (same skill, domain, context)
- 75-89%: Strong match (same skill, different domain)
- 60-74%: Moderate match (overlapping keywords, similar outcomes)
- <60%: Weak direct match

**2. Transferable Skills (30%)**
- Same capability in different context
- Leadership in different domain
- Technical problem-solving in different stack
- Similar scale/complexity in different industry

**Scoring:**
- 90-100%: Directly transferable (process, skill generic)
- 75-89%: Mostly transferable (some domain translation needed)
- 60-74%: Partially transferable (analogy required)
- <60%: Stretch to call transferable

**3. Adjacent Experience (20%)**
- Touched on skill as secondary responsibility
- Used related tools/methodologies
- Worked in related problem space
- Supporting role in relevant area

**Scoring:**
- 90-100%: Closely adjacent (just different framing)
- 75-89%: Clearly adjacent (related but distinct)
- 60-74%: Somewhat adjacent (requires explanation)
- <60%: Loosely adjacent

**4. Impact Alignment (10%)**
- Achievement type matches what role values
- Quantitative metrics (if JD emphasizes data-driven)
- Team outcomes (if JD emphasizes collaboration)
- Innovation (if JD emphasizes creativity)
- Scale (if JD emphasizes hyperscale)

**Scoring:**
- 90-100%: Perfect impact alignment
- 75-89%: Strong impact alignment
- 60-74%: Moderate impact alignment
- <60%: Weak impact alignment

> **Note:** Sub-dimension scoring bands use the same 75/60 breakpoints as the overall confidence bands. A sub-dimension score below 60% will contribute toward a GAP-level overall score.

## Overall Confidence Score

```
Overall = (Direct × 0.4) + (Transferable × 0.3) + (Adjacent × 0.2) + (Impact × 0.1)
```

**Confidence Bands:**
- 90-100%: DIRECT — Use with confidence
- 75-89%: TRANSFERABLE — Strong candidate, reframe terminology as needed
- 60-74%: ADJACENT — Acceptable with reframing
- <60%: GAP — Flag as unaddressed requirement

## Success Profile → Scoring Input Mapping

The Success Profile produced by research-prompts.md feeds the four scoring dimensions as follows:

| Success Profile Field | Scoring Dimension | How to Use |
|---|---|---|
| Core Requirements (Must-Have) | Direct Match (40%) | Required keywords, domain, technology — high-weight direct match inputs |
| Valued Capabilities (Nice-to-Have) | Transferable Skills (30%) | Same capability in different context — use for transferable scoring |
| Narrative Themes | Adjacent Experience (20%) | Secondary exposure patterns from similar role holders — surface adjacent skills, supporting roles, and related tools |
| Narrative Themes | Impact Alignment (10%) | What achievement types the role values — use to score impact alignment |
| Cultural Fit Signals | Impact Alignment (10%) | Collaboration, scale, innovation signals — incorporate into impact scoring |
| Terminology Map | All dimensions (reframing) | Use standard→preferred term mappings when reframing bullets |
| Risk Factors | Gap Handling | Flag risks before matching; address via reframing or cover letter guidance |

## Content Reframing Strategies

**When to reframe:** Good match (≥60%) but language doesn't align with target terminology

**Strategy 1: Keyword Alignment**
```
Preserve meaning, adjust terminology

Before: "Led experimental design and data analysis programs"
After:  "Led data science programs combining experimental design and
         statistical analysis"
Reason: Target role uses "data science" terminology
```

**Strategy 2: Emphasis Shift**
```
Same facts, different focus

Before: "Designed statistical experiments... saving millions in recall costs"
After:  "Prevented millions in potential recall costs through predictive
         risk detection using statistical modeling"
Reason: Target role values business outcomes over technical methods
```

**Strategy 3: Abstraction Level**
```
Adjust technical specificity

Before: "Built MATLAB-based automated system for evaluation"
After:  "Developed automated evaluation system"
Reason: Target role is language-agnostic, emphasize outcome

OR

After:  "Built automated evaluation system (MATLAB, Python integration)"
Reason: Target role values technical specificity
```

**Strategy 4: Scale Emphasis**
```
Highlight relevant scale aspects

Before: "Managed project with 3 stakeholders"
After:  "Led cross-functional initiative coordinating 3 organizational units"
Reason: Emphasize cross-org complexity over headcount
```

## Gap Handling

**When match confidence < 60%:**

**Option 1: Reframe Adjacent Experience**
```
Present reframing option:

TEMPLATE SLOT: {Requirement}
BEST MATCH: {Experience} (Confidence: {score}%)

REFRAME OPPORTUNITY:
Original: "{bullet_text}"
Reframed: "{adjusted_text}"
Justification: {why this is truthful}

RECOMMENDATION: Use reframed version? Y/N
```

**Option 2: Flag as Gap**
```
GAP IDENTIFIED: {Requirement}

AVAILABLE OPTIONS:
None with confidence >60%

RECOMMENDATIONS:
1. Address in cover letter - emphasize learning ability
2. Omit bullet slot - reduce template allocation
3. Include best available match ({score}%) with disclosure
4. Discover new experience through brainstorming

User decides how to proceed.
```

**Option 3: Route to Gap Resolution (truth mode only)**
```
This option applies only in `truth` mode. In `yolo` mode, skip to Option 2 (Flag as Gap) or
fabricate per Phase 2.5 rules — do not prompt the user for discovery during content matching.

If Phase 2.5 Gap Resolution has not yet run for this gap:
  Route back to Phase 2.5 for targeted supplemental discovery:
  "This gap might be addressable through supplemental discovery.
  Routing automatically to Phase 2.5 — no routing confirmation needed before the supplemental interview begins."

If Phase 2.5 has already run and this gap remains:
  Accept gap, move forward.
```
