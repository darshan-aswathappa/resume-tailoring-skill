# Recruiter Evaluation System

## Overview

The recruiter evaluation system adds a quality feedback loop around the resume building process. A simulated recruiter persona independently evaluates the generated resume, providing objective assessment that catches weaknesses before the user submits their application.

**Key Insight:** Recruiters spend 6-20 seconds on initial resume screening. If the resume does not pass that scan, nothing else matters.

**Feedback Loop:**
```
Phase -1: Recruiter builds evaluation rubric (SEALED)
    ↓
Phases 0-4: Resume building (unchanged, no access to rubric)
    ↓
Phase 6: Recruiter evaluates resume against rubric
    ↓
PASS → Phase 5 (Library Update)
REJECT → Feedback routes to Phase 2, 3, or 3.5 (max 3 iterations)
```

**Critical Design Rule:** The rubric built in Phase -1 is SEALED. It is not shared with Phases 0-4. This prevents confirmation bias -- the resume is built to match the JD and success profile, then independently evaluated by a persona that formed its own assessment of what matters. Only Phase 6 sees the rubric.

## Phase -1: Rubric Generation

### Recruiter Persona

```
You are a senior recruiter at {Company} with 15 open reqs and limited time.
You scan resumes in 6-20 seconds. You forward roughly 10-15% of resumes
you receive to the hiring manager. You have seen thousands of resumes for
roles like {Role} and have strong instincts for what makes a candidate
worth the hiring manager's time.

Your goal: Build an evaluation rubric for this specific role based on the
job description. Be concrete -- generic criteria waste everyone's time.
```

### Rubric Components

**A. Knockout Criteria (instant discard)**

Conditions where the resume is immediately rejected regardless of other strengths.

```
For each knockout, specify:
- criterion: What triggers the discard
- check: How to verify (what to look for in the resume)
- severity: HARD (no exceptions) or SOFT (context-dependent)

Examples:
- Missing required certification (e.g., PMP for PM role that says "PMP required")
- Wrong seniority signal (applying for Staff-level with 2 years experience)
- No relevant industry experience when JD specifies "X years in {domain}"
- Resume exceeds 3 pages for mid-level role
- No contact information or broken formatting
```

**B. Scan Priorities (what eyes hit in first 6 seconds)**

4-5 items with weights summing to 100. These represent what the recruiter physically reads first.

| Priority | Weight | What the Recruiter Looks For |
|----------|--------|------------------------------|
| Current/recent title + company signal | 25% | Does the most recent role signal relevant experience? Is the company recognizable or in the right domain? |
| Summary/headline keyword alignment | 25% | Does the professional summary immediately position this person for the target role? Are JD keywords present in the first 2-3 sentences? |
| Quantified achievements visible above fold | 20% | Can the recruiter see concrete numbers (revenue, users, percentage improvements) without scrolling? |
| Experience recency and relevance | 15% | Is the most relevant experience recent (last 2-3 years)? Or is it buried under unrelated work? |
| Visual clarity and structure | 15% | Can the recruiter parse the resume in seconds? Clean headers, consistent formatting, logical flow? |

**C. Forward Triggers (what makes the recruiter say "interview this person")**

3-5 specific achievements or signals derived from the JD that would make the recruiter forward the resume to the hiring manager.

```
For each trigger, specify:
- trigger: The specific achievement or signal
- evidence_type: What form this takes in a resume (bullet, skill, title)
- jd_source: Which JD requirement this maps to

Examples (for a Cloud Infrastructure PM role):
- Led migration of production systems at scale (100+ services)
- Shipped infrastructure products used by internal/external customers
- Managed cross-org technical programs with measurable business outcomes
- Evidence of cloud platform depth (AWS/Azure/GCP certifications or project scope)
```

**D. Differentiators (good vs great)**

What separates a resume that gets forwarded from one that gets forwarded with a personal note to the hiring manager.

```
- Narrative coherence: Does the career tell a story that leads to this role?
- Unique angles: Something unexpected that makes the candidate memorable
- Growth trajectory: Clear evidence of increasing scope and responsibility
- Domain depth: Indicators of deep expertise beyond surface keywords
```

### Rubric Data Structure

```json
{
  "role": "{Role}",
  "company": "{Company}",
  "knockout_criteria": [
    {
      "criterion": "Missing cloud platform experience",
      "check": "Resume must mention AWS, Azure, or GCP in experience bullets",
      "severity": "HARD"
    }
  ],
  "scan_priorities": [
    {
      "item": "Current title + company signal",
      "weight": 25,
      "look_for": "Most recent title includes PM/TPM and company is tech or infrastructure"
    }
  ],
  "forward_triggers": [
    {
      "trigger": "Led infrastructure migration at scale",
      "evidence_type": "experience_bullet",
      "jd_source": "Requirement: 5+ years infrastructure program management"
    }
  ],
  "differentiators": [
    "Career narrative progresses from IC engineer to technical leadership",
    "Evidence of both building and operating production systems"
  ],
  "pass_thresholds": {
    "overall_minimum": 85,
    "scan_score_minimum": 70,
    "knockouts_allowed": 0
  }
}
```

### Phase -1 Checkpoint

```
"Before we build your resume, I've generated a recruiter evaluation rubric
for this role. This rubric will be used AFTER resume generation to
independently assess quality.

RUBRIC SUMMARY:
- Knockout criteria: {N} items ({list brief descriptions})
- Scan priorities: {N} items (weighted scoring)
- Forward triggers: {N} specific achievements to demonstrate
- Differentiators: {N} items

PASS THRESHOLDS:
- Overall score >= 85
- Scan score >= 70
- Zero knockouts

This rubric is now SEALED and will not influence the resume building
process. It will be used in Phase 6 for independent evaluation.

Proceed to resume building? (Y/N)"
```

## Phase 6: Evaluation Protocol

### Stage 1: 6-Second Scan (60% of overall score)

Simulates what the recruiter physically reads in the first pass.

**What the recruiter reads:**
1. Name and contact info (present? professional?)
2. Professional summary (first 2-3 sentences only)
3. Most recent job title + company name
4. Skills section headers (if visible without scrolling)
5. Overall visual impression (formatting, density, length)

**Scoring:**

For each scan priority item from the rubric, score 0-100:
- 90-100: Immediately compelling, no doubt this is relevant
- 70-89: Solid signal, would continue reading
- 50-69: Ambiguous, might continue if other signals strong
- 30-49: Weak signal, leaning toward skip
- 0-29: Negative signal, actively discouraging

```
scan_score = sum(item_score * item_weight / 100 for each scan_priority)
```

**Example calculation:**
```
Title + company signal:    85 * 0.25 = 21.25
Summary keyword alignment: 70 * 0.25 = 17.50
Quantified achievements:   60 * 0.20 = 12.00
Experience recency:        90 * 0.15 = 13.50
Visual clarity:            80 * 0.15 = 12.00
                                      ------
scan_score:                            76.25
```

**Gate:** If scan_score < 70, the recruiter stops reading. Stage 2 does not run. Detail score defaults to 0.

### Stage 2: 14-Second Detail Review (40% of overall score)

Only runs if scan_score >= 70. Simulates the recruiter's second pass.

**What the recruiter reads:**
1. All bullets for the top 2 most recent/relevant roles
2. Education section
3. Overall resume structure and length
4. Any remaining sections (certifications, projects)

**Scoring dimensions:**

| Dimension | Weight | Evaluates |
|-----------|--------|-----------|
| Forward trigger match | 40% | How many forward triggers are evidenced in the resume? |
| Narrative coherence | 25% | Does the career progression make sense for this role? |
| Achievement depth | 20% | Are accomplishments specific, quantified, and impactful? |
| Differentiation | 15% | Does anything make this candidate stand out from similar applicants? |

Score each dimension 0-100, then:

```
detail_score = (trigger_match * 0.40) + (narrative * 0.25) + (achievement_depth * 0.20) + (differentiation * 0.15)
```

### Overall Score

```
overall = (scan_score * 0.6) + (detail_score * 0.4)
```

### Decision Logic

```python
def evaluate(scan_score, detail_score, knockouts_found, specialist_confidence=None):
    overall = (scan_score * 0.6) + (detail_score * 0.4)

    if knockouts_found > 0:
        return "REJECT", "knockout"
    if scan_score < 70:
        return "REJECT", "weak_first_impression"
    if overall >= 85:
        return "PASS", None  # Formula PASS — specialist_override is never logged here, even if
                              # specialist_confidence is high. This is expected: the override path
                              # only activates when the formula fails (overall < 85). A score of
                              # exactly 85 with high specialist confidence is a formula PASS, not
                              # an override.

    # Phase 6.5: Specialist Override
    # If formula score is below 85 but >= 70, a high-confidence specialist review can override
    if specialist_confidence is not None and specialist_confidence >= 85 and overall >= 70:
        return "PASS", "specialist_override"

    return "REJECT", "insufficient_evidence"
```

**Phase 6.5: Specialist Override Rules**

After computing the formula score, consult the Recruitment Specialist agent:

| Formula Score | Specialist Confidence | Result |
|---|---|---|
| >= 85 | any | PASS (formula) |
| 70-84 | >= 85% | PASS (specialist override) |
| 70-84 | < 85% | REJECT (insufficient evidence) |
| < 70 | any | REJECT (weak first impression — floor constraint, no override) |

**Limitation:** The specialist agent and the formula evaluator share the same underlying LLM. The specialist override provides a second-pass heuristic, not a fully independent evaluation.

Decision outcomes:
- **PASS**: overall >= 85 AND scan_score >= 70 AND zero knockouts
- **REJECT (knockout)**: Any knockout criterion triggered
- **REJECT (weak_first_impression)**: scan_score < 70, recruiter stopped reading
- **REJECT (insufficient_evidence)**: Passed scan but overall < 85

## Feedback Generation

When the decision is REJECT, generate structured feedback to guide revision.

### Feedback Structure

```json
{
  "decision": "REJECT",
  "iteration": 1,
  "scores": {
    "scan": 55,
    "detail": 40,
    "overall": 49
  },
  "rejection_reason": "weak_first_impression",
  "feedback_items": [
    {
      "issue": "Summary does not mention cloud infrastructure",
      "severity": "CRITICAL",
      "fix": "Lead summary with cloud/infrastructure PM positioning",
      "target_phase": "assembly",
      "target_section": "professional_summary"
    },
    {
      "issue": "Most recent title reads as generic PM, not infrastructure-specific",
      "severity": "IMPORTANT",
      "fix": "Reframe title to emphasize infrastructure/platform scope",
      "target_phase": "template",
      "target_section": "experience_role_1"
    },
    {
      "issue": "No quantified achievements visible in top 3 bullets",
      "severity": "CRITICAL",
      "fix": "Move highest-impact metrics to first bullet of most recent role",
      "target_phase": "bullet_polish",
      "target_section": "experience_role_1"
    }
  ]
}
```

**Severity Levels:**
- **CRITICAL**: Directly caused the rejection. Must fix to pass.
- **IMPORTANT**: Significantly dragged down the score. Should fix.
- **MINOR**: Would improve the resume but did not cause rejection alone.

## Feedback Classification and Routing

Feedback items route to the phase best equipped to address them.

### Routing Rules

**STRUCTURAL issues -> Phase 2 (Template):**
- Section order is wrong for this role type
- Role consolidation decision needs revisiting
- Bullet allocation between roles is imbalanced
- Missing a section the recruiter expects (e.g., certifications)
- Title reframing needs a different approach

**CONTENT SELECTION issues -> Phase 3 (Assembly):**
- Wrong experience highlighted for a template slot
- A better-matching bullet exists in the library but was not selected
- Experience from the wrong role is featured for a requirement
- Gap that could be filled by a different library entry

**BULLET QUALITY issues -> Phase 3.5 (Bullet Polish):**
- Weak action verbs (managed, helped, assisted)
- Missing quantified metrics where data exists
- Vague impact statements (improved performance vs improved latency by 40%)
- Poor XYZ format (accomplished X by doing Y, resulting in Z)
- Bullet too long or too short for scanning

**CONTENT GAP (max iterations reached) -> Phase 2.5 (Supplemental Discovery):**
- All 3 iterations exhausted and score has not reached 85
- Remaining issues suggest missing experience, not presentation problems
- Bullets from 2+ categories flagged for the same underlying gap
- Note: Phase 2.5 supplemental discovery is an escape route only — it does not re-run the full discovery session. It targets only the specific gaps that remain unresolved.

### Classification Algorithm

```python
def classify_feedback(feedback_items):
    structural = [i for i in feedback_items if i["target_phase"] == "template"]
    content = [i for i in feedback_items if i["target_phase"] == "assembly"]
    polish = [i for i in feedback_items if i["target_phase"] == "bullet_polish"]

    # Critical structural issues take priority -- they affect everything downstream
    if any(i["severity"] == "CRITICAL" for i in structural):
        return "Phase 2"

    # If more content selection issues than polish, route to assembly
    if len(content) > len(polish):
        return "Phase 3"

    # Default: bullet polish is most common (~60% of rejections)
    return "Phase 3.5"
```

### Routing Output

```
FEEDBACK ROUTING:
Target: Phase {2|3|3.5} ({Template|Assembly Phase|Bullet Polish})

ITEMS TO ADDRESS:
1. [CRITICAL] {issue} → {fix}
2. [IMPORTANT] {issue} → {fix}
3. [MINOR] {issue} → {fix}

ITEMS CARRIED FORWARD (address if time permits):
- {lower severity items not in target phase}

Proceeding to Phase {N} with {count} feedback items.
```

> **Multi-job mode note:** In multi-job batch processing, Phase 3.5 (Bullet Polish) is referred to as Phase 3C.5, and Phase 3 (Assembly Phase) is referred to as Phase 3C. The routing logic and feedback structure are identical; only the phase labels differ.

## Iteration Management

### Iteration Limits

- Maximum 3 iterations (1 initial + 2 revisions); terminate early after 2 consecutive iterations with < 2 point gain
- Each iteration must show score improvement or the loop terminates early
- Track full iteration history for transparency

### Iteration History Structure

**Example A — Successful (PASS via specialist override):**

```json
{
  "iterations": [
    {
      "attempt": 1,
      "scores": { "scan": 55, "detail": 40, "overall": 49 },
      "decision": "REJECT",
      "reason": "weak_first_impression",
      "feedback_count": { "critical": 2, "important": 1, "minor": 1 },
      "routed_to": "Phase 3.5"
    },
    {
      "attempt": 2,
      "scores": { "scan": 72, "detail": 65, "overall": 69.2 },
      "decision": "REJECT",
      "reason": "insufficient_evidence",
      "feedback_count": { "critical": 1, "important": 2, "minor": 0 },
      "routed_to": "Phase 3"
    },
    {
      "attempt": 3,
      "scores": { "scan": 78, "detail": 74, "overall": 76.4 },
      "decision": "PASS",
      "reason": "specialist_override",
      "feedback_count": null,
      "routed_to": null
    }
  ]
}
```

**Example B — Plateau termination (2 consecutive < 2 point gain → early exit):**

```json
{
  "iterations": [
    {
      "attempt": 1,
      "scores": { "scan": 76, "detail": 72, "overall": 74.4 },
      "decision": "REJECT",
      "reason": "insufficient_evidence",
      "score_gain": null,
      "consecutive_flat": 0,
      "feedback_count": { "critical": 1, "important": 2, "minor": 1 },
      "routed_to": "Phase 3.5"
    },
    {
      "attempt": 2,
      "scores": { "scan": 77, "detail": 73, "overall": 75.2 },
      "decision": "REJECT",
      "reason": "insufficient_evidence",
      "score_gain": 0.8,
      "consecutive_flat": 1,
      "feedback_count": { "critical": 0, "important": 2, "minor": 2 },
      "routed_to": "Phase 3.5"
    },
    {
      "attempt": 3,
      "scores": { "scan": 77, "detail": 74, "overall": 75.8 },
      "decision": "REJECT",
      "reason": "insufficient_evidence",
      "score_gain": 0.6,
      "consecutive_flat": 2,
      "termination_reason": "plateau_2_consecutive_flat",
      "feedback_count": { "critical": 0, "important": 1, "minor": 3 },
      "routed_to": null
    }
  ],
  "best_version": "attempt_3",
  "best_score": 75.8
}
```

> Plateau termination fires after attempt 3 completes — the 2-consecutive check runs after each iteration. The user is presented with the Max Iterations Reached template using `best_version` as the recommended output.

### Max Iterations Reached Template

```
"Score improvement stalled (2 consecutive iterations with < 2 point gain, or score plateau reached).

SCORE PROGRESSION:
  Attempt 1: {score} ({decision})
  ...
  Attempt N: {score} ({decision} — plateau)

TARGET: 85 (not yet reached)
BEST VERSION: Attempt {N} (score: {best_score})

REMAINING ISSUES:
1. {Issue}: {Why it persists}

RECOMMENDATIONS:
1. ACCEPT CURRENT VERSION - Best achievable score given available experience content.
2. DISCOVER MORE EXPERIENCES - Run Experience Discovery (Phase 2.5) to surface content addressing remaining gaps.
3. MANUAL REVIEW - Review remaining issues and make targeted edits.

Which option?"
```

## PASS Output Template

```
"RECRUITER EVALUATION: PASS

SCORES:
  Scan score:    {scan}/100 (threshold: 70)
  Detail score:  {detail}/100
  Overall score: {overall}/100 (threshold: 85)
  Knockouts:     0

STRENGTHS NOTED:
- {Strength 1}: {What impressed the recruiter}
- {Strength 2}: {What impressed the recruiter}
- {Strength 3}: {What impressed the recruiter}

DIFFERENTIATOR NOTES:
- {What makes this resume stand out from similar candidates}

VERDICT: This resume would be forwarded to the hiring manager.
{If score > 92: 'With a personal recommendation note.'}

Proceeding to Phase 5 (User Review + Optional Library Update)."
```

## REJECT Output Template

```
"RECRUITER EVALUATION: REJECT (Iteration {N})

SCORES:
  Scan score:    {scan}/100 (threshold: 70) {FAIL if < 70}
  Detail score:  {detail}/100 {N/A if scan < 70}
  Overall score: {overall}/100 (threshold: 85) {FAIL if < 85}
  Knockouts:     {count} {FAIL if > 0}

REJECTION REASON: {weak_first_impression | insufficient_evidence | knockout}

SPECIFIC FEEDBACK:
{For each item, ordered by severity:}
{N}. [{CRITICAL|IMPORTANT|MINOR}] {issue}
   Fix: {specific actionable fix}
   Section: {target section in resume}

IMPROVEMENT PRIORITY:
1. {Highest impact fix - expected score gain: +X points}
2. {Second highest impact fix - expected score gain: +X points}
3. {Third fix if applicable}

ROUTING: Feedback sent to Phase {2|3|3.5} ({Template|Assembly|Bullet Polish})"
```

## Edge Cases

### Recruiter Feedback Contradicts User Preferences

```
SCENARIO: Rubric says "lead with technical depth" but user prefers
          leadership-focused framing.

HANDLING:
"The recruiter evaluation suggests emphasizing technical depth, but
your approved template prioritizes leadership framing.

OPTIONS:
1. ADJUST - Shift emphasis toward technical (recruiter's recommendation)
2. KEEP - Maintain leadership framing (your preference)
3. BLEND - Lead with leadership but add technical depth signals

Note: The recruiter persona evaluates what typically gets forwarded
for this role. Your knowledge of the specific situation may override."

User decides. If KEEP, lower the pass threshold by 5 points for the
scan priority that conflicts (recruiter expectations adjusted).
```

### Score Improves but Does Not Cross Threshold

```
SCENARIO: Attempt 1: 52, Attempt 2: 72, Attempt 3: 83 (still < 85)

HANDLING: Apply max iterations reached template. Highlight the
improvement trajectory and note that 83 is competitive even if below
the automated threshold. Recommend accepting with the caveat that
the resume is "good but not optimized" for this specific role.
```

### Knockout Found on Iteration 2+

```
SCENARIO: A knockout criterion surfaces after a revision (e.g., title
reframing inadvertently removed a required credential signal).

HANDLING:
"WARNING: Knockout criterion triggered on iteration {N} that was
not present in iteration {N-1}.

KNOCKOUT: {criterion}
CAUSE: {What changed between iterations that caused this}

This takes priority over all other feedback. Reverting the change
that introduced the knockout and re-evaluating."

Automatically revert the specific change, then re-evaluate without
consuming an iteration (knockout introductions are treated as bugs,
not genuine failures).
```

### All Feedback Items Are MINOR

```
SCENARIO: Rejection at overall score 82, but all feedback items are
severity MINOR. No single clear fix would cross the threshold.

HANDLING:
"Score is close to threshold (82 vs 85) and all feedback items are
minor improvements. No single fix will cross the threshold.

OPTIONS:
1. ACCEPT AT CURRENT SCORE - Resume is competitive, minor polish
   unlikely to change hiring outcome.
2. APPLY ALL MINOR FIXES - Address all {N} items for cumulative
   improvement. Expected gain: +{estimate} points.

RECOMMENDATION: Option 1 unless you have time for polish. At
scores in the 82-84 range, the marginal difference from minor polish
rarely changes recruiter behavior — but do not use this reasoning to
accept scores below 80."
```

## Multi-Job Integration

### Per-Job Rubric Generation

In multi-job mode, recruiter evaluation runs independently for each job during Phase 3 processing. Each job has its own rubric because different JDs produce different evaluation criteria.

```
Batch: 3 jobs
  Job 1 (Microsoft PM): Rubric emphasizes Azure, cross-org leadership
  Job 2 (Google TPM):   Rubric emphasizes technical depth, system design
  Job 3 (AWS PM):       Rubric emphasizes customer obsession, scale metrics
```

### Express Mode

In EXPRESS mode, the evaluation loop runs without user intervention:
- Phase -1 rubric generated automatically (no checkpoint)
- Phase 6 evaluates, routes feedback, re-runs target phase
- Loop continues until PASS or score plateau (2 consecutive attempts with < 2 point gain)
- User sees only the final result with iteration history

### Interactive Mode

In INTERACTIVE mode, the user sees each iteration:
- Phase -1 rubric presented for review
- Each REJECT shows full feedback and asks user to confirm routing
- User can override routing (e.g., "skip this fix, accept as-is")
- User can inject manual feedback alongside recruiter feedback

### Batch Summary Integration

After all jobs complete evaluation:

```
BATCH EVALUATION SUMMARY:
  Job 1 (Microsoft PM):  PASS (score: 91, 1 iteration)
  Job 2 (Google TPM):    PASS (score: 87, 2 iterations)
  Job 3 (AWS PM):        PASS (score: 85, 3 iterations)

COMMON WEAKNESSES ACROSS JOBS:
- {Pattern}: Appeared in {N} evaluations
- {Pattern}: Appeared in {N} evaluations

LIBRARY UPDATE RECOMMENDATION:
These common weaknesses suggest enriching the library with:
- {Experience type that would improve multiple resumes}
```
