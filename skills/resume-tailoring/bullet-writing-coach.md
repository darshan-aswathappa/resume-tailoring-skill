# Bullet Writing Coach

## Overview

Polish resume bullets into recruiter-ready form using the XYZ framework, strict formatting constraints, and role-appropriate language. Used by Phase 3.5 (Bullet Polish) to transform raw experience into high-impact bullets.

## XYZ Format Specification

Every bullet must address three dimensions:

- **X = What you accomplished** -- the result, deliverable, or outcome
- **Y = How it was measured** -- the metric, quantified impact, or scale
- **Z = How you did it** -- the method, technology, or approach

**Practical pattern:**
```
[Action verb] [Z: what you did], [X: what you achieved] [Y: measured by what]
```

XYZ is a content checklist, not rigid word order. Z-first ordering is preferred because it leads with the action (what you did) before stating the result. All three elements should be present, but the sentence should read naturally.

```
Z-first (preferred):
"Migrated 14 microservices from REST to gRPC, reducing inter-service latency by 40%"
 ^Z: method/action                              ^X: result         ^Y: metric

X-first (acceptable when impact is the headline):
"Reduced customer churn by 18% by building a predictive model using gradient-boosted trees"
 ^X: result            ^Y: metric  ^Z: method
```

## Bullet Constraints

1. **HARD MAX: One sentence per bullet, maximum 1-2 printed lines (~120 characters ideal, 150 characters absolute cap).** If a bullet exceeds 150 characters of plain text, it MUST be trimmed — cut qualifiers, merge clauses, or drop the least impactful detail. A 3-line bullet is NEVER acceptable.
2. Start every bullet with a strong past-tense action verb
3. No personal pronouns -- never use I, we, my, our, us, their
4. No periods at the end of bullets
5. No sub-bullets; each bullet stands alone
6. Use digits for all numbers ("3" not "three", "2nd" not "second")
7. Avoid apostrophes, ampersands, and slashes where possible ("and" not "&", "or" not "/")
8. Spell out acronyms a non-technical recruiter would not recognize on first use
9. Order bullets most impressive to least impressive within each role
10. 3-5 bullets per role for recent or relevant positions; 1-3 for older or less relevant

## Action Verb Lists

### STRONG (preferred)

analyzed, architected, automated, built, created, decreased, delivered, designed, developed, discovered, eliminated, established, expanded, grew, implemented, improved, integrated, launched, led, migrated, optimized, published, rebuilt, reduced, refactored, replaced, scaled, shipped, simplified, streamlined, unified

### BANNED (instant rewrite)

aided, assisted, coded, collaborated, communicated, executed, helped, participated, programmed, ran, used, utilized, worked on

These verbs are banned because they describe presence rather than contribution. "Collaborated on a migration" could mean you led the architecture or attended one meeting. Replace with the specific action you performed.

### AVOID (replace with what literally happened)

amplified, conceptualized, crafted, elevated, employed, engaged, engineered, enhanced, ensured, fostered, headed, hone, innovated, mastered, orchestrated, perfected, pioneered, revolutionized, spearheaded, transformed

These verbs sound impressive but communicate nothing specific. "Spearheaded a migration" tells you less than "migrated." "Engineered a solution" tells you less than "built a caching layer." Replace each with the literal action: what did your hands (or keyboard) actually do?

## The 2-of-3 Rule

Every bullet must demonstrate at least 2 of:

1. **TECHNICAL DEPTH** -- specific technologies, methods, architectures, how components integrated
2. **CHALLENGE OVERCOME** -- what was hard (constraint, scale, complexity, ambiguity, timeline)
3. **MEASURABLE IMPACT** -- quantified in business terms (revenue, users, time saved, error rates)

A bullet with only 1 of 3 reads as a job duty. A bullet with 2 of 3 reads as an accomplishment. A bullet with all 3 is exceptional.

### Quantifying Impact

- Prefer business metrics (dollars, users, revenue) over internal metrics (story points, PRs merged)
- Use percentages for improvements: "reduced build time by 40%"
- Use absolute numbers for scale: "serving 2M daily active users"
- Use time for efficiency gains: "cut deployment from 4 hours to 15 minutes"
- Use comparative framing when helpful: "3x faster than previous system"
- If exact numbers are sensitive or classified, use approximate ranges ("reduced costs by roughly 30-40%") or relative terms ("cut processing time by more than half")
- If no metric is available at all, use descriptive scale: "cross-functional team of 12", "across 8 product lines"

## Role-Type Adaptation Matrix

### Engineering
- **Emphasize:** systems and architecture decisions, technical tradeoffs, performance metrics, scale
- **Preferred verbs:** architected, built, designed, implemented, migrated, optimized, refactored, scaled, shipped
- **Metric focus:** latency, throughput, uptime, error rates, deployment frequency

### PM and Leadership
- **Emphasize:** business outcomes, team scale, strategic decisions, stakeholder alignment
- **Preferred verbs:** delivered, established, grew, launched, led, expanded, unified, streamlined
- **Metric focus:** revenue impact, user growth, team size, time-to-market, adoption rates

### Analyst
- **Emphasize:** data volume, insight-to-action pipeline, decision impact, methodology rigor
- **Preferred verbs:** analyzed, built, created, discovered, developed, improved, reduced, simplified
- **Metric focus:** dataset size, decision impact (dollars saved, risks identified), accuracy improvements

### Design
- **Emphasize:** user research methodology, conversion and engagement metrics, design system scale
- **Preferred verbs:** designed, built, created, improved, launched, reduced, simplified, streamlined
- **Metric focus:** conversion rate, task completion time, NPS or satisfaction scores, design system adoption

## Good vs Bad Examples

### Engineering

```
BAD:  "Worked on migrating the backend services to a new architecture"
      ISSUES: Banned verb (worked on), no metric, no technical depth, reads as a duty
GOOD: "Migrated 14 microservices from monolith to event-driven architecture,
       reducing deploy time from 4 hours to 12 minutes and eliminating 3 monthly outages"
      WHY: Z-first ordering, specific scale (14 services), 2 concrete metrics
```

### Engineering (metrics)

```
BAD:  "Helped improve application performance by optimizing database queries"
      ISSUES: Banned verb (helped), vague (which queries? how much faster?), no scale
GOOD: "Optimized 23 high-frequency PostgreSQL queries using index tuning and query
       plan analysis, reducing p95 API latency from 800ms to 120ms"
      WHY: Specific count, named technology, before-and-after metric
```

### PM and Leadership

```
BAD:  "Spearheaded the launch of a new product feature that customers liked"
      ISSUES: Avoid verb (spearheaded), subjective claim (liked), no metric
GOOD: "Launched real-time collaboration feature serving 50K daily users within
       6 weeks, increasing paid conversion by 12%"
      WHY: Specific feature, scale, timeline, business metric
```

### Analyst

```
BAD:  "Used SQL and Python to analyze data and create reports for stakeholders"
      ISSUES: Banned verb (used), generic (which data? what reports? what happened?)
GOOD: "Built automated anomaly detection pipeline processing 2M daily transactions,
       identifying 340K in annual fraud losses previously undetected"
      WHY: Named method, scale of data, dollar impact, clear before-and-after
```

## Review Protocol

For each bullet, assign:

```
BULLET: "{original text}"
VERDICT: Strong | Needs Work | Rewrite
ISSUES:
  - {specific problem, e.g., "banned verb: helped", "missing metric", "exceeds 2-line limit"}
REWRITE: "{improved version}"
REASONING: "{one sentence: what changed and why}"
```

**Verdict criteria:**
- **Strong**: Passes all constraints, has 2-of-3, starts with strong verb, reads clean
- **Needs Work**: Minor issues (weak verb, missing one element, slightly long)
- **Rewrite**: Banned verb, reads as job duty, no measurable element, fundamentally vague

## Writing Process

1. **EXTRACT GOALS**: What was the user trying to achieve in this role or project?
2. **IDENTIFY METRICS**: Pull out every number, percentage, dollar figure, timeline, and scale indicator from the source resume
3. **DRAFT**: Generate 2-3 bullet options per experience when workshopping interactively; 1 recommended bullet when batch processing
4. **RANK**: Order candidate bullets by impressiveness; recommend the strongest with one sentence of reasoning
5. **TRIM**: Cut adjectives and adverbs first, then compress clauses. Never sacrifice a metric for space. If a bullet is too long, split into two bullets rather than losing quantified impact

## Truthfulness Constraints

- Never fabricate experience, projects, or roles
- Metrics must come from the source resume or explicit user input during discovery
- Technologies must be ones the user actually used (not adjacent tools they did not touch)
- Scope and scale must match reality (do not inflate team size, user count, or revenue impact)
- If no metric is available and the user cannot provide one, use descriptive scale instead of inventing numbers
- When reframing, the underlying facts must remain unchanged; only emphasis and terminology shift

## Sensitive and Classified Work Guidance

When a user indicates work is under NDA, classified, or otherwise restricted:

- Describe technologies and methods generically without naming client, project, or agency
- Quantify with relative terms ("reduced processing time by 70%") instead of absolutes that could identify the project
- Focus on transferable skills and technical approach rather than domain-specific outcomes
- Use industry-standard terminology instead of proprietary internal names
- Never pressure the user to disclose more detail than they are comfortable sharing
- Frame guidance as: "Can you share the general technology area and approximate scale?"

## Integration with Phase 3.5 (Feedback Loop)

The bullet writing coach operates within a review loop alongside the recruiter persona.

### Iteration 1 (Initial Polish)
- Review ALL bullets produced by Phase 3 (Resume Assembly)
- Apply full constraint set: XYZ check, verb check, 2-of-3 rule, length limit
- Produce polished bullet set and pass to recruiter for feedback

### Iteration 2+ (Targeted Re-Polish)
- ONLY re-polish bullets flagged in recruiter feedback
- Apply specific feedback as additional constraints (e.g., "add metrics to bullet 3", "too technical for this audience")
- Do NOT modify bullets that were not flagged -- they already passed review
- Preserve the ordering established in iteration 1 unless recruiter feedback specifically requests reordering

### Feedback Constraint Application
```
FLAGGED BULLET: "{text}"
RECRUITER FEEDBACK: "{specific note}"
CONSTRAINT ADDED: {translate feedback into a writing rule}
REVISED BULLET: "{new version}"
VERIFICATION: Does revised bullet satisfy original constraints AND new feedback?
```

### Convergence
- Typically converges in 2-3 iterations
- If a bullet is flagged 3 times, surface it to the user for manual input
- The coach does not enter an infinite polish loop; after iteration 3, present the best version with a note on unresolved feedback
