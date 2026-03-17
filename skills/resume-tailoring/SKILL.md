---
name: resume-tailoring
description: Use when creating tailored resumes for job applications - researches company/role, creates optimized templates, conducts branching experience discovery to surface undocumented skills, generates professional multi-format resumes from user's resume library while maintaining factual integrity, and validates output through simulated recruiter evaluation with iterative feedback loop
---

# Resume Tailoring Skill

## Overview

Generates high-quality, tailored resumes optimized for specific job descriptions while maintaining factual integrity. Builds resumes around the holistic person by surfacing undocumented experiences through conversational discovery.

**Core Principle:** Truth-preserving optimization - maximize fit while maintaining factual integrity. Never fabricate experience, but intelligently reframe and emphasize relevant aspects.

**Mission:** A person's ability to get a job should be based on their experiences and capabilities, not on their resume writing skills.

## When to Use

Use this skill when:
- User provides a job description and wants a tailored resume
- User has multiple existing resumes in markdown or LaTeX (.tex) format
- User wants to optimize their application for a specific role/company
- User needs help surfacing and articulating undocumented experiences

**DO NOT use for:**
- Generic resume writing from scratch (user needs existing resume library)
- Cover letters (different skill)
- LinkedIn profile optimization (different skill)

## Quick Start

**Required from user:**
1. Job description (text or URL)
2. Resume library location (defaults to `resumes/` in current directory)
3. Resume source format: markdown (`.md`) or LaTeX (`.tex`)

**Workflow:**
1. Recruiter intake - build evaluation rubric from JD (sealed)
2. Build library from existing resumes (supports `.md` and `.tex`)
3. Research company/role
4. Create template (with user checkpoint)
5. Optional: Branching experience discovery
6. Match content with confidence scoring
7. Polish bullets using XYZ format (bullet-writing coach)
8. Generate output:
   - If source is `.md`: Generate full MD + DOCX + Report (+ optional PDF)
   - If source is `.tex`: Generate LaTeX changes patch only (courses, experience bullets, project bullets, skills)
9. Recruiter evaluation (6-second scan + 14-second detail review)
10. If rejected: iterate with feedback (max 3 loops)
11. User review and optional library update

## Implementation

See supporting files:
- `research-prompts.md` - Structured prompts for company/role research
- `matching-strategies.md` - Content matching algorithms and scoring
- `branching-questions.md` - Experience discovery conversation patterns
- `bullet-writing-coach.md` - XYZ format rules, verb enforcement, role-type adaptation
- `recruiter-evaluation.md` - Recruiter rubric generation, scoring, feedback loop routing

## Workflow Details

### Multi-Job Detection

**Triggers when user provides:**
- Multiple JD URLs (comma or newline separated)
- Phrases: "multiple jobs", "several positions", "batch", "3 jobs"
- List of companies/roles: "Microsoft PM, Google TPM, AWS PM"

**Detection Logic:**

```python
# Pseudo-code
def detect_multi_job(user_input):
    indicators = [
        len(extract_urls(user_input)) > 1,
        any(phrase in user_input.lower() for phrase in
            ["multiple jobs", "several positions", "batch of", "3 jobs", "5 jobs"]),
        count_company_mentions(user_input) > 1
    ]
    return any(indicators)
```

**If detected:**
```
"I see you have multiple job applications. Would you like to use
multi-job mode?

BENEFITS:
- Shared experience discovery (faster - ask questions once for all jobs)
- Batch processing with progress tracking
- Incremental additions (add more jobs later)

TIME COMPARISON (3 similar jobs):
- Sequential single-job: ~45 minutes (15 min × 3)
- Multi-job mode: ~40 minutes (15 min discovery + 8 min per job)

Use multi-job mode? (Y/N)"
```

**If user confirms Y:**
- Use multi-job workflow (see multi-job-workflow.md)

**If user confirms N or single job detected:**
- Use existing single-job workflow (Phase 0 onwards)

**Enhanced Single-Job Workflow:** The single-job workflow now includes recruiter evaluation (Phase -1, Phase 6) and bullet polish (Phase 3.5) with an iterative feedback loop. All original phases (0-5) remain unchanged in behavior.

**Multi-Job Workflow:**

When multi-job mode is activated, see `multi-job-workflow.md` for complete workflow.

**High-Level Multi-Job Process:**

```
┌─────────────────────────────────────────────────────────────┐
│ PHASE -1: Recruiter Intake (per-job rubrics)                │
│ - Build evaluation rubric from each JD (sealed)             │
│ - Define knockout criteria, scan priorities, triggers       │
└─────────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────────┐
│ PHASE 0: Intake & Batch Initialization                      │
│ - Collect 3-5 job descriptions                              │
│ - Initialize batch structure                                │
│ - Run library initialization (once)                         │
└─────────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────────┐
│ PHASE 1: Aggregate Gap Analysis                             │
│ - Extract requirements from all JDs                         │
│ - Cross-reference against library                           │
│ - Build unified gap map (deduplicate)                       │
│ - Prioritize: Critical → Important → Job-specific           │
└─────────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────────┐
│ PHASE 2: Shared Experience Discovery                        │
│ - Single branching interview covering ALL gaps              │
│ - Multi-job context for each question                       │
│ - Tag experiences with job relevance                        │
│ - Enrich library with discoveries                           │
└─────────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────────┐
│ PHASE 3: Per-Job Processing (Sequential)                    │
│ For each job:                                               │
│   ├─ Research (company + role benchmarking)                 │
│   ├─ Template generation                                    │
│   ├─ Content matching (uses enriched library)               │
│   ├─ Bullet polish (XYZ format, verb quality)          NEW  │
│   ├─ Generation (MD + DOCX + Report)                        │
│   └─ Recruiter evaluation (feedback loop, max 3)       NEW  │
│ Interactive or Express mode                                 │
└─────────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────────┐
│ PHASE 4: Batch Finalization                                 │
│ - Generate batch summary (includes recruiter scores)        │
│ - User reviews all resumes together                         │
│ - Approve/revise individual or batch                        │
│ - Update library with approved resumes                      │
└─────────────────────────────────────────────────────────────┘
```

**Time Savings:**
- 3 jobs: ~40 min (vs 45 min sequential) = 11% savings
- 5 jobs: ~55 min (vs 75 min sequential) = 27% savings

**Quality:** Same depth as single-job workflow (research, matching, generation)

**See `multi-job-workflow.md` for complete implementation details.**

### Phase -1: Recruiter Intake

**Goal:** Build an independent evaluation rubric BEFORE any resume work begins

**Why this phase exists:** If the same process both builds and evaluates the resume, you get confirmation bias. By establishing the recruiter's criteria upfront and independently, the evaluation in Phase 6 has teeth.

**Inputs:**
- Job description (text or URL from user)

**Process:**

**-1.1 Activate Recruiter Persona:**
```
Persona: "You are a senior recruiter at {Company} who has been briefed
on this role. You have 15 open reqs and limited time. You need to
quickly identify candidates worth forwarding to the hiring manager."
```

**-1.2 Build Evaluation Rubric (see recruiter-evaluation.md):**

1. **Knockout criteria** - instant disqualifiers:
   - Missing required credentials
   - Wrong seniority level
   - No relevant domain experience
   - Resume formatting red flags

2. **6-second scan priorities** (what recruiter eyes hit first):
   - Title/company signal (25%)
   - Summary keyword alignment (25%)
   - Visible quantified achievements (20%)
   - Experience recency (15%)
   - Visual clarity (15%)

3. **Forward triggers** (what earns an interview):
   - Specific achievements matching role
   - Evidence of scope and scale
   - Domain depth indicators

4. **Differentiators** (good vs great):
   - Narrative coherence
   - Unique angles
   - Growth trajectory

**-1.3 Seal the Rubric:**

CRITICAL: The rubric is NOT shared with Phases 0-4. It is stored separately and accessed ONLY by Phase 6. The resume-building phases operate without knowledge of the specific evaluation criteria. On iteration 2+, only the FEEDBACK (what is wrong) flows back, not the rubric itself.

**Checkpoint:**
```
"Before we build your resume, I've analyzed the JD from a recruiter's
perspective.

KNOCKOUT CRITERIA (instant discard):
- {criterion 1}
- {criterion 2}

WHAT I SCAN FIRST (6-second test):
1. {scan_priority_1} (25%)
2. {scan_priority_2} (25%)
3. {scan_priority_3} (20%)
4. {scan_priority_4} (15%)
5. {scan_priority_5} (15%)

WHAT EARNS AN INTERVIEW:
- {trigger_1}
- {trigger_2}

Does this match your understanding? Any insider knowledge to add?"

Wait for user confirmation before proceeding.
```

**Output:** Sealed evaluation rubric (consumed only by Phase 6)

### Phase 0: Library Initialization

**Always runs first - builds fresh resume database**

**Process:**

1. **Locate resume directory:**
   ```
   User provides path OR default to ./resumes/
   Validate directory exists
   ```

2. **Detect source format and scan for resume files:**

   **Auto-detect from initial message first:**
   ```
   If the user's first message contained a file path ending in .tex
   (e.g., "here is my resume: resume.tex" or "use resume/darshan.tex"):
     source_format = "latex"
     Announce: "I see you provided a LaTeX file. I'll build your library
     from that .tex file and output a LaTeX patch at the end. ✓"
     Skip the Glob scan and load that specific file.
   Else:
     Proceed with Glob scan below.
   ```

   **Glob-based detection (when no path in initial message):**
   ```
   Step A — Scan for .tex files:
     Use Glob tool: pattern="*.tex" path={resume_directory}

   If .tex files found (N > 0):
     Say exactly:
       "I found {N} LaTeX file(s) in your resume directory:
        {list filenames}
        Should I use LaTeX mode? In LaTeX mode, I will:
        - Read your .tex source to build the content library
        - Output ONLY the changed text as ready-to-paste LaTeX snippets
          (courses line, experience bullets, project bullets, skills block)
        - Leave all formatting, fonts, and section ordering untouched

        Use LaTeX mode? (Y/N — default Y if all files are .tex)"

   If user says Y (or all files are .tex and user does not object):
     source_format = "latex"
     Announce: "Building resume library from {N} LaTeX file(s)..."

   If user says N, or no .tex files found:
     Step B — Scan for .md files:
       Use Glob tool: pattern="*.md" path={resume_directory}
       source_format = "markdown"
       Announce: "Building resume library from {N} markdown resume(s)..."

   If BOTH .tex AND .md files exist and user has not yet confirmed:
     Say exactly:
       "Your resume directory contains both .tex and .md files.
        Which format is your primary source?
        1. LaTeX (.tex) — I output a patch with only the changed sections
        2. Markdown (.md) — I output a full new resume (MD + DOCX)
        (Do not mix formats in the same session.)"
     Wait for answer before proceeding.

   If .tex Glob returns 0 AND user's message mentions a .tex file:
     Say: "I didn't find any .tex files in {resume_directory}.
     Please confirm the path or paste the file contents directly."
     Wait for user response.
   ```

   Set library.source_format = source_format
   This value controls Phase 4 output routing.

3. **Parse each resume:**
   For each resume file:
   - Use Read tool to load content
   - Detect format from file extension
   - Extract sections using the appropriate parser (see parsing rules below)
   - Identify patterns: bullet structure, length, formatting

   **Parsing rules for `.md` files (unchanged):**
   - Sections are identified by `#` or `##` headings
   - Bullets are lines starting with `-` or `•`
   - Education, Experience, Projects, Skills identified by heading text
   - Courses line identified by text starting with `**Courses:**` or `Courses:`

   **Parsing rules for `.tex` files:**
   - Sections are identified by `\section*{...}` or `\section{...}` commands
   - Bullets are `\item` lines inside `\begin{itemize}` or `\begin{enumerate}` blocks
   - Treat `\begin{itemize}` and `\begin{enumerate}` identically for content extraction; record which command was used in the source so Phase 4 can reproduce it exactly
   - The Education section is the block following `\section*{Education}` or `\section{Education}`
   - The Courses line is any line inside the Education block that begins with one of these patterns: `\textbf{Courses:}`, `\textbf{Relevant Courses:}`, `\textbf{Selected Courses:}`, `\textbf{Key Courses:}`, or `Courses:` (un-bolded). If none of these patterns is found, set `courses_line` to null and skip the Courses section in Phase 4.0 output rather than guessing.
   - Experience roles are identified by `\textbf{Job Title,}` or `\textbf{Job Title}` lines followed by a company name and date range (usually before the first `\begin{itemize}` in the Experience section)
   - If a single role contains more than one `\begin{itemize}` (or `\begin{enumerate}`) block, treat all of them together as that role's bullet list. In Phase 4.0 output, emit a single merged `\begin{itemize}...\end{itemize}` block replacing all of them, and add a note: "(Source had multiple list blocks for {Role}; merged into one in this patch.)"
   - Project names are identified by `\textbf{Project Name}` lines (optionally followed by `\href{...}{...}`) before the first `\begin{itemize}` in the Projects section
   - The Skills section is the block following `\section*{Skills}` or `\section{Skills}`, containing `\textbf{Category:} value \\` lines
   - Preserve role and project ordering exactly as they appear in the source file; NEVER reorder roles, projects, or skill categories
   - LaTeX escape sequences in plain text (e.g., `\&` for `&`, `\%` for `%`) should be stored in the database in their escaped form so they can be reproduced faithfully in output
   - To populate `section_order`, scan the source file top-to-bottom and record the normalized name (`education`, `experience`, `projects`, `skills`) of each `\section*{...}` or `\section{...}` command in the order it appears. If a section has an unexpected name (e.g., `\section*{Work History}`), map it to the closest normalized name and note the mapping. This array is read by Phase 4.0 to enforce the "Do NOT change section ordering" constraint.
   - **Section command variants:** Treat `\section`, `\section*`, `\subsection`, `\subsection*`, and `\noindent\textbf{...}` followed by `\\` as equivalent section delimiters when parsing. Record the exact command used in `user_preferences` so Phase 4.0 never outputs a different command in the patch.

   **Handling unknown LaTeX commands:**
   - If a line contains an unrecognized command (e.g., `\vspace`, `\hfill`, `\noindent`), preserve it verbatim in the surrounding context but do not attempt to parse its content as resume data
   - If the entire section uses a custom environment not listed above (e.g., `\begin{cvitems}`), note it as "non-standard environment: {name}" and ask the user how to handle it before continuing

4. **Build experience database structure:**
   ```json
   {
     "source_format": "latex",
     "roles": [
       {
         "role_id": "company_title_year",
         "company": "Company Name",
         "title": "Job Title",
         "dates": "YYYY-YYYY",
         "description": "Role summary",
         "list_env": "itemize",
         "bullets": [
           {
             "text": "Full bullet text (LaTeX-escaped if source is .tex)",
             "themes": ["leadership", "technical"],
             "metrics": ["17x improvement", "$3M revenue"],
             "keywords": ["cross-functional", "program"],
             "source_resumes": ["resume.tex"]
           }
         ]
       }
     ],
     "projects": [
       {
         "project_id": "project_name",
         "name": "Project Name",
         "url": "https://...",
         "list_env": "itemize",
         "bullets": [...]
       }
     ],
     "skills": {
       "Language": ["Java", "Python", "TypeScript"],
       "Framework": ["Spring Boot", "React"]
     },
     "education": {
       "courses_line": "\\textbf{Courses:} Data Structures \\& Algorithms, Cloud Computing \\\\",
       "institutions": [...]
     },
     "user_preferences": {
       "typical_length": "1-page|2-page",
       "section_order": ["education", "experience", "projects", "skills"],
       "bullet_style": "\\item"
     }
   }
   ```

   Note: `list_env` records whether the source used `itemize` or `enumerate` so Phase 4 can reproduce the exact environment. `section_order` records the order sections appear in the source file so Phase 4 never reorders them.

5. **Tag content automatically:**
   - Themes: Scan for keywords (leadership, technical, analytics, etc.)
   - Metrics: Extract numbers, percentages, dollar amounts (strip LaTeX markup first when scanning, e.g., treat `70\%` as `70%`)
   - Keywords: Frequent technical terms, action verbs

**Output:** In-memory database ready for matching, with `source_format` flag set

**Code pattern:**
```python
# Pseudo-code for reference
library = {
    "source_format": "markdown",  # or "latex"
    "roles": [],
    "projects": [],
    "skills": {},
    "education": {}
}

tex_files = glob("resumes/*.tex")
md_files  = glob("resumes/*.md")

if tex_files and user_confirmed_latex_mode:
    library["source_format"] = "latex"
    files = tex_files
else:
    library["source_format"] = "markdown"
    files = md_files

for resume_file in files:
    content = read(resume_file)
    if library["source_format"] == "latex":
        roles    = extract_roles_from_tex(content)
        projects = extract_projects_from_tex(content)
        skills   = extract_skills_from_tex(content)
        education = extract_education_from_tex(content)
    else:
        roles    = extract_roles(content)
        projects = extract_projects(content)
        skills   = extract_skills(content)
        education = extract_education(content)

    for role in roles:
        role["bullets"] = tag_bullets(role["bullets"])
        library["roles"].append(role)

    library["projects"].extend(projects)
    library["skills"].update(skills)
    library["education"] = education

return library
```

### Phase 1: Research Phase

**Goal:** Build comprehensive "success profile" beyond just the job description

**Inputs:**
- Job description (text or URL from user)
- Optional: Company name if not in JD

**Process:**

**1.1 Job Description Parsing:**
```
Use research-prompts.md JD parsing template
Extract: requirements, keywords, implicit preferences, red flags, role archetype
```

**1.2 Company Research:**
```
WebSearch queries:
- "{company} mission values culture"
- "{company} engineering blog"
- "{company} recent news"

Synthesize: mission, values, business model, stage
```

**1.3 Role Benchmarking:**
```
WebSearch: "site:linkedin.com {job_title} {company}"
WebFetch: Top 3-5 profiles
Analyze: common backgrounds, skills, terminology

If sparse results, try similar companies
```

**1.4 Success Profile Synthesis:**
```
Combine all research into structured profile (see research-prompts.md template)

Include:
- Core requirements (must-have)
- Valued capabilities (nice-to-have)
- Cultural fit signals
- Narrative themes
- Terminology map (user's background → their language)
- Risk factors + mitigations
```

**Checkpoint:**
```
Present success profile to user:

"Based on my research, here's what makes candidates successful for this role:

{SUCCESS_PROFILE_SUMMARY}

Key findings:
- {Finding 1}
- {Finding 2}
- {Finding 3}

Does this match your understanding? Any adjustments?"

Wait for user confirmation before proceeding.
```

**Output:** Validated success profile document

### Phase 2: Template Generation

**Goal:** Create resume structure optimized for this specific role

**Inputs:**
- Success profile (from Phase 1)
- User's resume library (from Phase 0)

**Process:**

**2.1 Analyze User's Resume Library:**
```
Extract from library:
- All roles, titles, companies, date ranges
- Role archetypes (technical contributor, manager, researcher, specialist)
- Experience clusters (what domains/skills appear frequently)
- Career progression and narrative
```

**2.2 Role Consolidation Decision:**

**When to consolidate:**
- Same company, similar responsibilities
- Target role values continuity over granular progression
- Combined narrative stronger than separate
- Page space constrained

**When to keep separate:**
- Different companies (ALWAYS separate)
- Dramatically different responsibilities that both matter
- Target role values specific progression story
- One position has significantly more relevant experience

**Decision template:**
```
For {Company} with {N} positions:

OPTION A (Consolidated):
Title: "{Combined_Title}"
Dates: "{First_Start} - {Last_End}"
Rationale: {Why consolidation makes sense}

OPTION B (Separate):
Position 1: "{Title}" ({Dates})
Position 2: "{Title}" ({Dates})
Rationale: {Why separate makes sense}

RECOMMENDED: Option {A/B} because {reasoning}
```

**2.3 Title Reframing Principles:**

**Core rule:** Stay truthful to what you did, emphasize aspect most relevant to target

**Strategies:**

1. **Emphasize different aspects:**
   - "Graduate Researcher" → "Research Software Engineer" (if coding-heavy)
   - "Data Science Lead" → "Technical Program Manager" (if leadership)

2. **Use industry-standard terminology:**
   - "Scientist III" → "Senior Research Scientist" (clearer seniority)
   - "Program Coordinator" → "Project Manager" (standard term)

3. **Add specialization when truthful:**
   - "Engineer" → "ML Engineer" (if ML work substantial)
   - "Researcher" → "Computational Ecologist" (if computational methods)

4. **Adjust seniority indicators:**
   - "Lead" vs "Senior" vs "Staff" based on scope

**Constraints:**
- NEVER claim work you didn't do
- NEVER inflate seniority beyond defensible
- Company name and dates MUST be exact
- Core responsibilities MUST be accurate

**2.4 Generate Template Structure:**

```markdown
## Professional Summary
[GUIDANCE: {X} sentences emphasizing {themes from success profile}]
[REQUIRED ELEMENTS: {keywords from JD}]

## Key Skills
[STRUCTURE: {2-4 categories based on JD structure}]
[SOURCE: Extract from library matching success profile]

## Professional Experience

### [ROLE 1 - Most Recent/Relevant]
[CONSOLIDATION: {merge X positions OR keep separate}]
[TITLE OPTIONS:
  A: {emphasize aspect 1}
  B: {emphasize aspect 2}
  Recommended: {option with rationale}]
[BULLET ALLOCATION: {N bullets based on relevance + recency}]
[GUIDANCE: Emphasize {themes}, look for {experience types}]

Bullet 1: [SEEKING: {requirement type}]
Bullet 2: [SEEKING: {requirement type}]
...

### [ROLE 2]
...

## Education
[PLACEMENT: {top if required/recent, bottom if experience-heavy}]

## [Optional Sections]
[INCLUDE IF: {criteria from success profile}]
```

**Checkpoint:**
```
Present template to user:

"Here's the optimized resume structure for this role:

STRUCTURE:
{Section order and rationale}

ROLE CONSOLIDATION:
{Decisions with options}

TITLE REFRAMING:
{Proposed titles with alternatives}

BULLET ALLOCATION:
Role 1: {N} bullets (most relevant)
Role 2: {N} bullets
...

Does this structure work? Any adjustments to:
- Role consolidation?
- Title reframing?
- Bullet allocation?"

Wait for user approval before proceeding.
```

**Output:** Approved template skeleton with guidance for each section

### Phase 2.5: Experience Discovery (OPTIONAL)

**Goal:** Surface undocumented experiences through conversational discovery

**When to trigger:**
```
After template approval, if gaps identified:

"I've identified {N} gaps or areas where we have weak matches:
- {Gap 1}: {Current confidence}
- {Gap 2}: {Current confidence}
...

Would you like to do a structured brainstorming session to surface
any experiences you haven't documented yet?

This typically takes 10-15 minutes and often uncovers valuable content."

User can accept or skip.
```

**Branching Interview Process:**

**Approach:** Conversational with follow-up questions based on answers

**For each gap, conduct branching dialogue (see branching-questions.md):**

1. **Start with open probe:**
   - Technical gap: "Have you worked with {skill}?"
   - Soft skill gap: "Tell me about times you've {demonstrated_skill}"
   - Recent work: "What have you worked on recently?"

2. **Branch based on answer:**
   - YES/Strong → Deep dive (scale, challenges, metrics)
   - INDIRECT → Explore role and transferability
   - ADJACENT → Explore related experience
   - PERSONAL → Assess recency and substance
   - NO → Try broader category or move on

3. **Follow-up systematically:**
   - Ask "what," "how," "why" to get details
   - Quantify: "Any metrics?"
   - Contextualize: "Was this production?"
   - Validate: "Does this address the gap?"

4. **Capture immediately:**
   - Document experience as shared
   - Ask clarifying questions (dates, scope, impact)
   - Help articulate as resume bullet
   - Tag which gap(s) it addresses

**Capture Structure:**
```markdown
## Newly Discovered Experiences

### Experience 1: {Brief description}
- Context: {Where/when}
- Scope: {Scale, duration, impact}
- Addresses: {Which gaps}
- Bullet draft: "{Achievement-focused bullet}"
- Confidence: {How well fills gap - percentage}

### Experience 2: ...
```

**Integration Options:**

After discovery session:
```
"Great! I captured {N} new experiences. For each one:

1. ADD TO CURRENT RESUME - Integrate now
2. ADD TO LIBRARY ONLY - Save for future, not needed here
3. REFINE FURTHER - Think more about articulation
4. DISCARD - Not relevant enough

Let me know for each experience."
```

**Important Notes:**
- Keep truthfulness bar high - help articulate, NEVER fabricate
- Focus on gaps and weak matches, not strong areas
- Time-box if needed (10-15 minutes typical)
- User can skip entirely if confident in library
- Recognize when to move on - don't exhaust user

**Output:** New experiences integrated into library, ready for matching

### Phase 3: Assembly Phase

**Goal:** Fill approved template with best-matching content, with transparent scoring

**Inputs:**
- Approved template (from Phase 2)
- Resume library + discovered experiences (from Phase 0 + 2.5)
- Success profile (from Phase 1)

**Process:**

**3.1 For Each Template Slot:**

1. **Extract all candidate bullets from library**
   - All bullets from library database
   - All newly discovered experiences
   - Include source resume for each

2. **Score each candidate** (see matching-strategies.md)
   - Direct match (40%): Keywords, domain, technology, outcome
   - Transferable (30%): Same capability, different context
   - Adjacent (20%): Related tools, methods, problem space
   - Impact (10%): Achievement type alignment

   Overall = (Direct × 0.4) + (Transfer × 0.3) + (Adjacent × 0.2) + (Impact × 0.1)

3. **Rank candidates by score**
   - Sort high to low
   - Group by confidence band:
     * 90-100%: DIRECT
     * 75-89%: TRANSFERABLE
     * 60-74%: ADJACENT
     * <60%: WEAK/GAP

4. **Present top 3 matches with analysis:**
   ```
   TEMPLATE SLOT: {Role} - Bullet {N}
   SEEKING: {Requirement description}

   MATCHES:
   [DIRECT - 95%] "{bullet_text}"
     ✓ Direct: {what matches directly}
     ✓ Transferable: {what transfers}
     ✓ Metrics: {quantified impact}
     Source: {resume_name}

   [TRANSFERABLE - 78%] "{bullet_text}"
     ✓ Transferable: {what transfers}
     ✓ Adjacent: {what's adjacent}
     ⚠ Gap: {what's missing}
     Source: {resume_name}

   [ADJACENT - 62%] "{bullet_text}"
     ✓ Adjacent: {what's related}
     ⚠ Gap: {what's missing}
     Source: {resume_name}

   RECOMMENDATION: Use DIRECT match (95%)
   ALTERNATIVE: If avoiding repetition, use TRANSFERABLE (78%) with reframing
   ```

5. **Handle gaps (confidence <60%):**
   ```
   GAP IDENTIFIED: {Requirement}

   BEST AVAILABLE: {score}% - "{bullet_text}"

   REFRAME OPPORTUNITY: {If applicable}
   Original: "{text}"
   Reframed: "{adjusted_text}" (truthful because {reason})
   New confidence: {score}%

   OPTIONS:
   1. Use reframed version ({new_score}%)
   2. Acknowledge gap in cover letter
   3. Omit bullet slot (reduce allocation)
   4. Use best available with disclosure

   RECOMMENDATION: {Most appropriate option}
   ```

**3.2 Content Reframing:**

When good match (>60%) but terminology misaligned:

**Apply strategies from matching-strategies.md:**
- Keyword alignment (preserve meaning, adjust terms)
- Emphasis shift (same facts, different focus)
- Abstraction level (adjust technical specificity)
- Scale emphasis (highlight relevant aspects)

**Show before/after for transparency:**
```
REFRAMING APPLIED:
Bullet: {template_slot}

Original: "{original_bullet}"
Source: {resume_name}

Reframed: "{reframed_bullet}"
Changes: {what changed and why}
Truthfulness: {why this is accurate}
```

**Checkpoint:**
```
"I've matched content to your template. Here's the complete mapping:

COVERAGE SUMMARY:
- Direct matches: {N} bullets ({percentage}%)
- Transferable: {N} bullets ({percentage}%)
- Adjacent: {N} bullets ({percentage}%)
- Gaps: {N} ({percentage}%)

REFRAMINGS APPLIED: {N}
- {Example 1}
- {Example 2}

GAPS IDENTIFIED:
- {Gap 1}: {Recommendation}
- {Gap 2}: {Recommendation}

OVERALL JD COVERAGE: {percentage}%

Review the detailed mapping below. Any adjustments to:
- Match selections?
- Reframings?
- Gap handling?"

[Present full detailed mapping]

Wait for user approval before generation.
```

**Output:** Complete bullet-by-bullet mapping with confidence scores and reframings

### Phase 3.5: Bullet Polish

**Goal:** Apply XYZ format rules and quality standards to every selected bullet before generation

**Why this phase exists:** Phase 3 selects WHICH content to use. Phase 3.5 ensures HOW it is written meets professional standards. These are separate concerns.

**Inputs:**
- Approved content mapping (from Phase 3)
- Success profile (from Phase 1)
- Role type classification (engineering / PM / analyst / design)
- Recruiter feedback (ONLY on iteration 2+, from Phase 6)

**Process (see bullet-writing-coach.md for full specification):**

**3.5.1 Role Type Classification:**
- Engineering: emphasize systems, architecture, technical decisions
- PM/Leadership: emphasize business outcomes, team scale, strategy
- Analyst: emphasize data volume, insight-to-action, decision impact
- Design: emphasize user research, conversion metrics, design system scale

**3.5.2 Bullet-by-Bullet Review:**

For each bullet in the approved content mapping:

1. **XYZ Format Check:**
   - Has strong past-tense action verb? (not banned/avoided)
   - Contains Z component (method/technology/approach)?
   - Contains X component (result/deliverable)?
   - Contains Y component (metric/quantified impact)?
   - Single sentence, max 2 printed lines?
   - No pronouns, no periods, no sub-bullets?

2. **Verb Check:**
   - BANNED verbs (immediate rewrite): aided, assisted, coded, collaborated, communicated, executed, helped, participated, programmed, ran, used, utilized, worked on (see bullet-writing-coach.md for full list)
   - AVOIDED verbs (flag for replacement): spearheaded, orchestrated, revolutionized, enhanced
   - PREFERRED verbs by role type (see bullet-writing-coach.md)

3. **2-of-3 Rule:**
   Each bullet must demonstrate at least 2 of:
   - Technical depth (specific technologies, methods, architectures)
   - Challenge overcome (scale, complexity, constraint, ambiguity)
   - Measurable impact (numbers, percentages, dollar amounts, time savings)

4. **Verdict Assignment:**
   - STRONG: Passes all checks, no changes needed
   - NEEDS_WORK: Passes most checks, minor adjustments
   - REWRITE: Fails XYZ format, uses banned verbs, or fails 2-of-3 rule

**3.5.3 Rewrite Process (for NEEDS_WORK and REWRITE bullets):**

1. Extract goals from original bullet
2. Identify available metrics from library metadata or discovery
3. Draft 2-3 XYZ-formatted alternatives
4. Rank by specificity, impact clarity, and role-type fit
5. Select best, trim to max 2 printed lines
6. Verify truthfulness against source material

**Truthfulness constraint:** Rewriting preserves factual accuracy. Metrics come from source resumes or discovery. Technologies must be ones the user actually used. Never fabricate.

**3.5.4 Iteration 2+ Behavior (after recruiter rejection):**

When recruiter feedback targets bullet quality:
- ONLY re-polish bullets identified in feedback
- Apply specific feedback as constraints (e.g., "add metrics to bullet 3")
- Do NOT change bullets that were not flagged
- Re-run writing process with feedback as additional input
- Log all changes for transparency

**LaTeX mode constraint (Phase 3.5):** When `source_format == "latex"`, the order of roles in the polished content mapping MUST match the order recorded in `library.user_preferences.section_order` and the role order within the Experience block as parsed in Phase 0. Do not reorder roles for relevance — bullet count per role may change, but role sequence may not. The same constraint applies to projects.

**Checkpoint (iteration 1 only):**
```
"I've polished {N} bullets for quality and impact:

REVIEW SUMMARY:
- Strong (no changes): {N} bullets
- Improved: {N} bullets
- Rewritten: {N} bullets

NOTABLE CHANGES:
- {Bullet X}: '{original}' -> '{polished}' [Reason: {why}]
- {Bullet Y}: '{original}' -> '{polished}' [Reason: {why}]

All changes preserve factual accuracy.

Review changes? Or proceed to generation?
(Y to proceed / R to review all changes / adjust specific bullets)"

Wait for user approval before proceeding.

On iteration 2+: No user checkpoint. Changes auto-apply from recruiter
feedback and proceed directly to Phase 4.
```

**Output:** Polished content mapping with all bullets meeting XYZ format standards

### Phase 4: Generation Phase

**Goal:** Create professional multi-format outputs

**Inputs:**
- Polished content mapping (from Phase 3.5)
- User's formatting preferences (from library analysis)
- Target role information (from Phase 1)
- `library.source_format` flag (set in Phase 0)

**Process:**

**Route based on source format:**

```
IF library.source_format == "latex":
    Run 4.0 (LaTeX Patch Output) ONLY.
    Skip 4.1, 4.2, 4.3.
    Still run 4.4 (Generation Summary Report).

IF library.source_format == "markdown":
    Skip 4.0.
    Run 4.1 (Markdown), 4.2 (DOCX), optionally 4.3 (PDF), and 4.4.
```

---

**4.0 LaTeX Patch Output (runs ONLY when source_format == "latex")**

**Goal:** Produce only the changed text, formatted as ready-to-paste LaTeX snippets. Do NOT output a full resume document. Do NOT change section ordering, fonts, spacing commands, or any structural LaTeX outside the four targeted content areas.

**What is changed:**
1. The `\textbf{Courses:}` line inside the Education section
2. The `\begin{itemize}...\end{itemize}` (or `\begin{enumerate}...\end{enumerate}`) block for each Experience role
3. The `\begin{itemize}...\end{itemize}` (or `\begin{enumerate}...\end{enumerate}`) block for each Project
4. The Skills section content (all `\textbf{Category:} value \\` lines as a group)

**What is NOT changed:**
- Section headings (`\section*{...}`)
- Role header lines (company name, job title, dates, location)
- Project header lines (project name, URL)
- Institution lines in Education
- Certifications lines in Education
- Contact information
- Any `\vspace`, `\hrule`, `\hfill`, font size declarations, or other layout commands
- The ordering of roles within Experience (preserve source order exactly)
- The ordering of projects within Projects (preserve source order exactly)
- The ordering of skill categories (preserve source order exactly)
- Do NOT merge adjacent `\begin{itemize}` blocks from two separate roles into one
- Do NOT split a single role's `\begin{itemize}` block into multiple blocks
- Do NOT add, remove, or relocate any `\\` line-break command outside the content areas being changed (courses line, skill category lines)
- Do NOT introduce new `\textbf{}`, `\textit{}`, or `\href{}` commands unless the original bullet already contained that command

**Pre-output ordering check:** Before printing the patch, verify:
1. Roles appear in the patch in the same sequence as they appear in the source `.tex` file (check against `section_order` and the parsed role order).
2. Projects appear in source order.
3. Skill categories appear in source order and no categories have been added or removed unless the user explicitly requested it.
If any ordering discrepancy is detected, silently correct it before output. Never emit a patch with reordered sections.

**Output format — the complete response must follow this exact structure:**

**CRITICAL: The square-bracket placeholders below are instructions to YOU (the LLM). Do NOT print any line that starts with `[` and ends with `]` literally in your output — those are meta-instructions. Only the non-bracketed lines (headers, LaTeX commands, section dividers) appear in the final patch. Separator lines are always exactly 45 dashes, regardless of header length.**

```
CHANGES TO APPLY TO YOUR LATEX FILE
=====================================
Target role: [Job Title] at [Company]

COURSES LINE
---------------------------------------------
Replace the \textbf{Courses:} line in your Education section with:

\textbf{Courses:} [Course 1], [Course 2], [Course 3], [Course 4] \\

(Keep every other line in the Education block exactly as-is.)

EXPERIENCE BULLETS — [Company Name] | [Job Title]
---------------------------------------------
Replace the \begin{itemize}...\end{itemize} block for this role with:

\begin{itemize}
  \item [Polished bullet 1]
  \item [Polished bullet 2]
  \item [Polished bullet 3]
\end{itemize}

[Output one EXPERIENCE BULLETS block per role, in source-file order. Do not print this line.]

PROJECT BULLETS — [Project Name]
---------------------------------------------
Replace the \begin{itemize}...\end{itemize} block for this project with:

\begin{itemize}
  \item [Polished bullet 1]
  \item [Polished bullet 2]
\end{itemize}

[Output one PROJECT BULLETS block per project, in source-file order. Do not print this line.]

SKILLS SECTION
---------------------------------------------
Replace the full skills content block (all lines between \section*{Skills}
and the next \section or end of document) with:

\textbf{[Category 1]:} [val1], [val2], [val3] \\
\textbf{[Category 2]:} [val1], [val2] \\
[One line per category. Preserve exact category names and order. Do not print this line.]

=====================================
END OF CHANGES
```

**Formatting rules for the LaTeX patch:**
- Every bullet line is `  \item {text}` (two-space indent, no trailing period)
- Every bullet text follows the XYZ format enforced in Phase 3.5; special characters are LaTeX-escaped: `&` becomes `\&`, `%` becomes `\%`, `#` becomes `\#`, `_` becomes `\_` when outside math mode
- Use the same list environment (`itemize` or `enumerate`) that appeared in the source for that role or project; do not change it
- The Courses line ends with ` \\` (space + two backslashes), matching the line-break convention in the source
- Each skill category line ends with ` \\` matching the source convention
- Do not add or remove skill categories; only update the values within existing categories unless the user explicitly asked for a category to be added
- If a role or project had no items selected (all bullets were gaps), omit that section from the patch and add a note: `(No changes for {Role/Project} — no improvements identified)`
- If the source used `\begin{enumerate}` for a role, use `\begin{enumerate}` and `\end{enumerate}` in the patch for that role (not `\begin{itemize}`); all other formatting rules (indent, no period, XYZ format) apply identically
- Emit exactly the number of polished bullets selected and approved in Phase 3/3.5 for that role or project. Do not pad to match the original count and do not truncate. If fewer bullets are produced than the original role had, add a LaTeX comment on the line before `\begin{itemize}`: `% Source had {N} bullets; {M} selected after optimization`

**Edge case handling in LaTeX patch mode:**
- **Nested itemize:** If the source has nested `\begin{itemize}` blocks inside a role, only replace the outermost block. Note explicitly: "The source for {Role} contains nested lists. Only the outer \begin{itemize} is replaced. Review inner bullets manually."
- **\item with optional label `\item[label]`:** Preserve the label syntax on any unchanged items; use plain `\item` for all new bullets
- **Inline formatting in bullets:** When rewriting bullets, preserve inline commands the user already uses (e.g., `\href{url}{text}`, `\textbf{}`, `\textit{}`). Only add `\href` links that were in the original bullet; do not introduce new URLs
- **Long bullets:** If a polished bullet exceeds approximately 150 characters of plain text, add a `% long bullet` comment on the same line as a visual warning, e.g., `  \item {long bullet text} % long bullet`
- **Multiple `.tex` files in library:** If the library was built from more than one `.tex` file, produce a separate patch block for each file, labeled with the filename at the top of its section

**Additional edge cases specific to LaTeX mode:**
- **Courses line with inline neighbors:** If the `\textbf{Courses:}` token appears mid-line (sharing the line with other `\textbf{...}` content like GPA), the replacement must emit only the `\textbf{Courses:} {list} \\` portion and preserve all other tokens on that line unchanged. State explicitly in the patch: "Replace only the `\textbf{Courses:} ...` portion of this line; keep all other tokens on the same line."
- **Non-line Skills layout:** If the Skills section body uses `\begin{tabular}`, `\begin{multicols}`, or any environment other than bare `\textbf{Cat:} value \\` lines, do not attempt to patch it. Instead output: "(Skills section uses a non-standard layout environment [{name}]. Manual editing required — see recommended skill values below:)" then list the recommended skills in plain text, one category per line.
- **Role with no bullet list:** If a role in the Experience section has no `\begin{itemize}` or `\begin{enumerate}` block (e.g., the user described the role in prose sentences), output a note: "(Role {Company/Title} has no itemize block in source. To add bullets, insert the following block after the role header line:)" then emit the `\begin{itemize}...\end{itemize}` block as normal.
- **Courses line is null:** If `courses_line` was set to null during Phase 0 parsing (no recognized courses pattern found), omit the COURSES LINE section from the patch entirely. Do not guess or fabricate a courses line.

**Output files:**
- Print the full patch inline in the conversation (no file write required)
- Optionally save to `{Name}_{Company}_{Role}_LaTeX_Changes.txt` if user requests a file

---

**4.1 Markdown Generation:**

**Compile mapped content into clean markdown:**

```markdown
# {User_Name}

{Contact_Info}

---

## Professional Summary

{Summary_from_template}

---

## Key Skills

**{Category_1}:**
- {Skills_from_library_matching_profile}

**{Category_2}:**
- {Skills_from_library_matching_profile}

{Repeat for all categories}

---

## Professional Experience

### {Job_Title}
**{Company} | {Location} | {Dates}**

{Role_summary_if_applicable}

• {Bullet_1_from_mapping}
• {Bullet_2_from_mapping}
...

### {Next_Role}
...

---

## Education

**{Degree}** | {Institution} ({Year})
**{Degree}** | {Institution} ({Year})
```

**Use user's preferences:**
- Formatting style from library analysis
- Bullet structure pattern
- Section ordering
- Typical length (1-page vs 2-page)

**Output:** `{Name}_{Company}_{Role}_Resume.md`

**4.2 DOCX Generation:**

**Use document-skills:docx:**

```
REQUIRED SUB-SKILL: Use document-skills:docx

Create Word document with:
- Professional fonts (Calibri 11pt body, 12pt headers)
- Proper spacing (single within sections, space between)
- Clean bullet formatting (proper numbering config, NOT unicode)
- Header with contact information
- Appropriate margins (0.5-1 inch)
- Bold/italic emphasis (company names, titles, dates)
- Page breaks if 2-page resume

See docx skill documentation for:
- Paragraph and TextRun structure
- Numbering configuration for bullets
- Heading levels and styles
- Spacing and margins
```

**Output:** `{Name}_{Company}_{Role}_Resume.docx`

**4.3 PDF Generation (Optional):**

**If user requests PDF:**

```
OPTIONAL SUB-SKILL: Use document-skills:pdf

Convert DOCX to PDF OR generate directly
Ensure formatting preservation
Professional appearance for direct submission
```

**Output:** `{Name}_{Company}_{Role}_Resume.pdf`

**4.4 Generation Summary Report:**

**Create metadata file:**

```markdown
# Resume Generation Report
**{Role} at {Company}**

**Date Generated:** {timestamp}

## Target Role Summary
- Company: {Company}
- Position: {Role}
- IC Level: {If known}
- Focus Areas: {Key areas}

## Success Profile Summary
- Key Requirements: {top 5}
- Cultural Fit Signals: {themes}
- Risk Factors Addressed: {mitigations}

## Content Mapping Summary
- Total bullets: {N}
- Direct matches: {N} ({percentage}%)
- Transferable: {N} ({percentage}%)
- Adjacent: {N} ({percentage}%)
- Gaps identified: {list}

## Reframing Applied
- {bullet}: {original} → {reframed} [Reason: {why}]
...

## Source Resumes Used
- {resume1}: {N} bullets
- {resume2}: {N} bullets
...

## Gaps Addressed

### Before Experience Discovery:
{Gap analysis showing initial state}

### After Experience Discovery:
{Gap analysis showing final state}

### Remaining Gaps:
{Any unresolved gaps with recommendations}

## Key Differentiators for This Role
{What makes user uniquely qualified}

## Recommendations for Interview Prep
- Stories to prepare
- Questions to expect
- Gaps to address
```

**Output:** `{Name}_{Company}_{Role}_Resume_Report.md`

**Present to user:**

If source_format == "latex":
```
"Your LaTeX resume changes are ready!

HOW TO APPLY:
Copy each section above and paste it into your .tex file,
replacing the indicated block. Compile with pdflatex or your
usual LaTeX build command to verify formatting.

WHAT WAS CHANGED:
- Courses line (Education section)
- Experience bullets: {list of roles updated}
- Project bullets: {list of projects updated}
- Skills section content

WHAT WAS NOT CHANGED:
- Section ordering, headings, fonts, spacing
- Role/project header lines (company, title, dates)
- Education institution lines and certifications
- Any layout commands (\vspace, \hfill, etc.)

QUALITY METRICS:
- JD Coverage: {percentage}%
- Direct Matches: {percentage}%
- Newly Discovered: {N} experiences

{If user requested file: Changes saved to {Name}_{Company}_{Role}_LaTeX_Changes.txt}

Review the changes and let me know:
1. Apply changes (you paste them into your .tex file)
2. Need revisions to specific bullets or sections
3. Save changes file for reference"
```

If source_format == "markdown":
```
"Your tailored resume has been generated!

FILES CREATED:
- {Name}_{Company}_{Role}_Resume.md
- {Name}_{Company}_{Role}_Resume.docx
- {Name}_{Company}_{Role}_Resume_Report.md
{- {Name}_{Company}_{Role}_Resume.pdf (if requested)}

QUALITY METRICS:
- JD Coverage: {percentage}%
- Direct Matches: {percentage}%
- Newly Discovered: {N} experiences

Review the files and let me know:
1. Save to library (recommended)
2. Need revisions
3. Save but don't add to library"
```

### Phase 5: Library Update (CONDITIONAL)

**Goal:** Optionally add successful resume to library for future use

**When:** After user reviews and approves generated resume

**Checkpoint Question:**
```
"Are you satisfied with this resume?

OPTIONS:
1. YES - Save to library
   → Adds resume to permanent location
   → Rebuilds library database
   → Makes new content available for future resumes

2. NO - Need revisions
   → What would you like to adjust?
   → Make changes and re-present

3. SAVE BUT DON'T ADD TO LIBRARY
   → Keep files in current location
   → Don't enrich database
   → Useful for experimental resumes

Which option?"
```

**If Option 1 (YES - Save to library):**

**Process:**

1. **Move resume to library:**
   ```
   Source: {current_directory}/{Name}_{Company}_{Role}_Resume.md
   Destination: {resume_library}/{Name}_{Company}_{Role}_Resume.md

   Also move:
   - .docx file
   - .pdf file (if exists)
   - _Report.md file
   ```

2. **Rebuild library database:**
   ```
   Re-run Phase 0 library initialization
   Parse newly created resume
   Add bullets to experience database
   Update keyword/theme indices
   Tag with metadata:
     - target_company: {Company}
     - target_role: {Role}
     - generated_date: {timestamp}
     - jd_coverage: {percentage}
     - success_profile: {reference to profile}
   ```

3. **Preserve generation metadata:**
   ```json
   {
     "resume_id": "{Name}_{Company}_{Role}",
     "generated": "{timestamp}",
     "source_resumes": ["{resume1}", "{resume2}"],
     "reframings": [
       {
         "original": "{text}",
         "reframed": "{text}",
         "reason": "{why}"
       }
     ],
     "match_scores": {
       "bullet_1": 95,
       "bullet_2": 87,
       ...
     },
     "newly_discovered": [
       {
         "experience": "{description}",
         "bullet": "{text}",
         "addresses_gap": "{gap}"
       }
     ]
   }
   ```

4. **Announce completion:**
   ```
   "Resume saved to library!

   Library updated:
   - Total resumes: {N}
   - New content variations: {N}
   - Newly discovered experiences added: {N}

   This resume and its new content are now available for future tailoring sessions."
   ```

**If Option 2 (NO - Need revisions):**

```
"What would you like to adjust?"

[Collect user feedback]
[Make requested changes]
[Re-run relevant phases]
[Re-present for approval]

[Repeat until satisfied or user cancels]
```

**If Option 3 (SAVE BUT DON'T ADD TO LIBRARY):**

```
"Resume files saved to current directory:
- {Name}_{Company}_{Role}_Resume.md
- {Name}_{Company}_{Role}_Resume.docx
- {Name}_{Company}_{Role}_Resume_Report.md

Not added to library - you can manually move later if desired."
```

**Benefits of Library Update:**
- Grows library with each successful resume
- New bullet variations become available
- Reframings that work can be reused
- Discovered experiences permanently captured
- Future sessions start with richer library
- Self-improving system over time

**Output:** Updated library database + metadata preservation (if Option 1)

### Phase 6: Recruiter Evaluation

**Goal:** Simulate a real recruiter's first-pass review of the generated resume

**Why this phase exists:** The resume-building process (Phases 0-4) optimizes for JD coverage and content quality. But real recruiter screening is a different test: does the resume survive a 6-20 second scan? This phase provides that external perspective.

**Inputs:**
- Generated resume content (from Phase 4):
  - If source_format == "markdown": the generated markdown resume text
  - If source_format == "latex": reconstruct the full resume mentally by merging the LaTeX patch (from 4.0) into the original source `.tex` content; evaluate the merged result as if it were a complete resume. Do not evaluate the patch diff in isolation.
- Sealed evaluation rubric (from Phase -1)
- Iteration count (1-based)

**Process (see recruiter-evaluation.md for full specification):**

**6.1 Knockout Check (simulated 2-second glance):**
```
For each knockout criterion in rubric:
  Check resume against criterion
  If ANY knockout found: REJECT immediately
  Feedback: "Knockout: {criterion}. Resume would be discarded
  before any detailed review."
```

**6.2 Six-Second Scan (60% of overall score):**
```
Read ONLY what a recruiter sees in 6 seconds:
- Name and contact line
- Professional summary (first 2-3 sentences)
- Most recent job title + company
- Skills section headers

Score each scan priority item (0-100), apply weights:
  scan_score = sum(item_score * item_weight for each scan_priority)

If scan_score < 60: REJECT (reason: weak first impression)
  Detailed scan never happens.
```

**6.3 Fourteen-Second Detail (40% of overall score):**
```
Only runs if scan_score >= 60

Read:
- All bullet points for top 2 roles
- Education section
- Overall structure and narrative flow

Evaluate:
- Forward trigger match (40%): How many triggers satisfied?
- Narrative coherence (25%): Does career story make sense?
- Achievement depth (20%): Are bullets specific with metrics?
- Differentiation (15%): What makes candidate stand out?

detail_score = weighted sum of above evaluations
```

**6.4 Decision:**
```
overall_score = (scan_score * 0.6) + (detail_score * 0.4)

PASS if: overall_score >= 70 AND scan_score >= 60 AND no knockouts
REJECT otherwise
```

**If PASS:**
```
"RECRUITER EVALUATION: PASS (Iteration {N})

Scan Score: {score}/100
Detail Score: {score}/100
Overall: {score}/100

WHAT WORKED:
- {strength_1}
- {strength_2}

DIFFERENTIATOR NOTES:
- {what stood out}

VERDICT: This resume would be forwarded to the hiring manager.

Proceed to library update."

→ Continue to Phase 5
```

**If REJECT:**
```
"RECRUITER EVALUATION: REJECT (Iteration {N}/{max})

Scan Score: {score}/100
Detail Score: {score}/100
Overall: {score}/100

REJECTION REASON: {primary_reason}

SPECIFIC FEEDBACK:
1. {Issue}: {What's wrong} → {What would fix it}
   Severity: CRITICAL | IMPORTANT | MINOR
   Affects: {target_phase} / {target_section}

2. {Issue}: ...

IMPROVEMENT PRIORITY (address in order):
1. {Highest impact change}
2. {Second highest}
3. {Third highest}

Feeding back into resume workflow..."

→ Route to feedback loop
```

### Feedback Loop

**Goal:** Route recruiter feedback to the appropriate phase for targeted revision

**Routing Logic:**

Feedback is classified and routed based on issue type:

```
STRUCTURAL issues → Phase 2 (Template)
  Examples: wrong section order, role consolidation incorrect,
  bullet allocation needs change
  Frequency: ~10% of rejections

CONTENT SELECTION issues → Phase 3 (Assembly)
  Examples: wrong experience highlighted, better match available,
  irrelevant bullets included
  Frequency: ~30% of rejections

BULLET QUALITY issues → Phase 3.5 (Bullet Polish)
  Examples: weak verbs, missing metrics, vague impact, poor XYZ format
  Frequency: ~60% of rejections (most common)
```

**Classification algorithm:**
```python
def determine_restart_phase(feedback_items):
    has_critical_structural = any(
        item.target_phase == "template" and item.severity == "CRITICAL"
        for item in feedback_items
    )
    content_count = sum(1 for item in feedback_items
                        if item.target_phase == "assembly")
    bullet_count = sum(1 for item in feedback_items
                       if item.target_phase == "bullet_polish")

    if has_critical_structural:
        return "Phase 2"    # Template revision
    elif content_count > bullet_count:
        return "Phase 3"    # Content re-selection
    else:
        return "Phase 3.5"  # Bullet rewrite (default)
```

**Iteration Rules:**
- Maximum 3 iterations (1 initial + 2 revisions)
- Each iteration re-runs ONLY from the routed phase forward
- Phases 0, 1, 2.5 NEVER re-run (library, research, discovery are stable)
- Phase 2 re-runs ONLY on structural feedback (rare)
- Each iteration should be progressively lighter (fewer bullets to fix)
- Recruiter feedback carries forward as additional input to the routed phase

**LaTeX mode iteration behavior:**
- When source_format == "latex", each iteration produces a new complete LaTeX patch (4.0 output) covering only the sections that changed in this iteration
- The patch header must note: "Iteration {N} — replaces iteration {N-1} patch for the following sections: {list}"
- Sections not touched in this iteration are omitted from the new patch (the user retains the previous patch for those sections)
- NEVER emit a full markdown or DOCX resume in LaTeX mode, even after multiple iterations
- If structural feedback (Phase 2) is routed in LaTeX mode, it affects only role ordering within Experience or project ordering within Projects; it does NOT change section-level ordering (Education/Experience/Projects/Skills order must stay as in the source)

**After Max Iterations (iteration 3 still REJECT):**
```
"RECRUITER EVALUATION: REJECT (Iteration 3/3 - FINAL)

After 3 iterations, the resume has not passed recruiter evaluation.

SCORE PROGRESSION:
- Iteration 1: {score}/100
- Iteration 2: {score}/100
- Iteration 3: {score}/100

REMAINING ISSUES:
- {issue_1}: This may be a fundamental content gap
- {issue_2}: ...

RECOMMENDATIONS:
1. ACCEPT CURRENT VERSION - Best achievable with current experience
   (Score: {score}/100)
2. EXPERIENCE DISCOVERY - The gaps may require discovering new
   experiences, not re-arranging existing ones
3. MANUAL REVIEW - Review yourself and provide specific direction

The root cause is likely a content gap, not a presentation issue.

Which option?"

→ If option 1: Continue to Phase 5
→ If option 2: Return to Phase 2.5 (Experience Discovery)
→ If option 3: Collect user feedback, apply changes, re-evaluate
```

## Error Handling & Edge Cases

**Edge Case 1: Insufficient Resume Library**
```
SCENARIO: User has only 1-2 resumes, limited content

HANDLING:
"⚠️ Limited resume library detected ({N} resumes).

This may result in:
- Fewer matching options
- More gaps in coverage
- Less variety in bullet phrasing

RECOMMENDATIONS:
- Proceed with available content (I'll do my best!)
- Consider adding more resumes after this generation
- Experience Discovery phase will be especially valuable

Continue? (Y/N)"
```

**Edge Case 2: No Good Matches (confidence <60% for critical requirement)**
```
SCENARIO: Template slot requires experience user doesn't have

HANDLING:
"❌ GAP: {Requirement}

No matches found with confidence >60%

OPTIONS:
1. Run Experience Discovery - might surface undocumented work
2. Reframe best available ({score}%) - I'll show you the reframing
3. Omit bullet slot - reduce template allocation
4. Note for cover letter - emphasize learning ability

Which approach?"

[Don't force matches - be transparent about gaps]
```

**Edge Case 3: Research Phase Failures**
```
SCENARIO: WebSearch fails, LinkedIn unavailable, company info sparse

HANDLING:
"⚠️ Limited company research available.

What I found:
- {Available info}

What's missing:
- {Missing areas}

Falling back to job description-only analysis.
Result: Template will be optimized for JD but may miss cultural nuances.

Do you have additional context about:
- Company culture?
- Team structure?
- Technologies used?

(Optional - you can share or skip)"

[Proceed with best-effort approach]
```

**Edge Case 4: Job Description Quality Issues**
```
SCENARIO: Vague JD, missing requirements, poorly written

HANDLING:
"⚠️ Job description lacks detail in areas:
- {Missing area 1}
- {Missing area 2}

This may limit template optimization.

Do you have additional context about:
- {Question 1}
- {Question 2}

(Optional - I'll work with what's available)"

[Extract what's possible, proceed]
```

**Edge Case 5: Ambiguous Role Consolidation**
```
SCENARIO: Unclear whether to merge roles or keep separate

HANDLING:
"🤔 Ambiguous consolidation decision for {Company}

OPTION A (Consolidated): {Rationale}
OPTION B (Separate): {Rationale}

Both are defensible. Which do you prefer?

(This becomes your preference for similar situations)"

[Remember preference for future]
```

**Edge Case 6: Resume Length Constraints**
```
SCENARIO: Too much good content, exceeds 2 pages

HANDLING:
"⚠️ Content exceeds 2 pages (current: {N} bullets, ~{pages} pages)

PRUNING SUGGESTIONS (ranked by relevance):
Remove:
- {Bullet X}: {score}% match (lowest)
- {Bullet Y}: {score}% match
...

Keep all bullets and accept >2 pages?
OR
Remove {N} bullets to fit 2 pages?

Your preference?"

[User decides priority]
```

**Edge Case 7: Recruiter Feedback Contradicts User Preferences**
```
SCENARIO: Recruiter says "remove education section" but user insisted on keeping it

HANDLING:
"The recruiter evaluation suggests changes that conflict with your
earlier preferences:

CONFLICT:
- Recruiter says: {suggestion}
- You previously chose: {preference}

OPTIONS:
1. KEEP YOUR PREFERENCE - Override recruiter feedback
2. ACCEPT RECRUITER SUGGESTION - Optimize for recruiter pass
3. COMPROMISE - {suggested middle ground}

Your choice?"

[User decides; their preference takes priority]
```

**Edge Case 8: Score Improves But Doesn't Cross Threshold**
```
SCENARIO: Score goes from 52 to 65 across iterations but never hits 70

HANDLING:
"Your resume has improved significantly across iterations:
- Iteration 1: 52/100
- Iteration 2: 61/100
- Iteration 3: 65/100

While it hasn't reached the 70/100 pass threshold, the improvement
shows the right direction. The remaining 5-point gap likely requires
new content, not better presentation.

RECOMMENDATION: Accept current version (65/100) and address remaining
gaps through experience discovery or cover letter.

Accept? (Y/N)"
```

**Error Recovery:**
- All checkpoints allow going back to previous phase
- User can request adjustments at any checkpoint
- Generation failures (DOCX/PDF) fall back to markdown-only
- Progress saved between phases (can resume if interrupted)
- Recruiter evaluation failures fall back to user review (skip Phase 6)
- Bullet polish preserves original if rewrite degrades truthfulness

**Graceful Degradation:**
- Research limited → Fall back to JD-only analysis
- Library small → Work with available + emphasize discovery
- Matches weak → Transparent gap identification
- Generation fails → Provide markdown + error details
- Recruiter loop stalls → Accept best version after max iterations
- Bullet polish conflicts → User preference overrides coach rules

## Usage Examples

**Example 1: Internal Role (Same Company)**
```
USER: "I want to apply for Principal PM role in 1ES team at Microsoft.
      Here's the JD: {paste}"

SKILL:
1. Recruiter Intake: Builds sealed evaluation rubric for 1ES PM role
2. Library Build: Finds 29 resumes
3. Research: Microsoft 1ES team, internal culture, role benchmarking
4. Template: Features PM2 Azure Eng Systems role (most relevant)
5. Discovery: Surfaces VS Code extension, Bhavana AI side project
6. Assembly: 92% JD coverage, 75% direct matches
7. Bullet Polish: 2 bullets improved (verb upgrades, added metrics)
8. Generate: MD + DOCX + Report
9. Recruiter Evaluation: PASS on first attempt (Score: 82/100)
10. User approves → Library updated with new resume + 6 discovered experiences

RESULT: Highly competitive application leveraging internal experience
```

**Example 2: Career Transition (Different Domain)**
```
USER: "I'm a TPM trying to transition to ecology PM role. JD: {paste}"

SKILL:
1. Recruiter Intake: Builds rubric focused on domain expertise + systems thinking
2. Library Build: Finds existing TPM resumes
3. Research: Ecology sector, sustainability focus, cross-domain transfers
4. Template: Reframes "Technical Program Manager" → "Program Manager,
             Environmental Systems" emphasizing systems thinking
5. Discovery: Surfaces volunteer conservation work, graduate research in
             environmental modeling
6. Assembly: 65% JD coverage - flags gaps in domain-specific knowledge
7. Bullet Polish: 5 bullets rewritten (domain terminology alignment)
8. Generate: Resume + gap analysis with cover letter recommendations
9. Recruiter Evaluation: REJECT (Score: 58/100) → feedback: weak domain signal
   Iteration 2: Bullet polish strengthens ecology keywords → PASS (Score: 71/100)

RESULT: Bridges technical skills with environmental domain
```

**Example 3: Career Gap Handling**
```
USER: "I have a 2-year gap while starting a company. JD: {paste}"

SKILL:
1. Recruiter Intake: Builds rubric; notes career gap as potential knockout
2. Library Build: Finds pre-gap resumes
3. Research: Standard analysis
4. Template: Includes startup as legitimate role
5. Discovery: Surfaces skills developed during startup (fundraising,
             product development, team building)
6. Assembly: Frames gap as entrepreneurial experience
7. Bullet Polish: 3 startup bullets enhanced with metrics and XYZ format
8. Generate: Resume presenting gap as valuable experience
9. Recruiter Evaluation: PASS (Score: 73/100) - startup framing cleared knockout

RESULT: Gap becomes strength showing initiative and diverse skills
```

**Example 4: Multi-Job Batch (3 Similar Roles)**
```
USER: "I want to apply for these 3 TPM roles:
      1. Microsoft 1ES Principal PM
      2. Google Cloud Senior TPM
      3. AWS Container Services Senior PM
      Here are the JDs: {paste 3 JDs}"

SKILL:
1. Multi-job detection: Triggered (3 JDs detected)
2. Intake: Collects all 3 JDs, initializes batch
3. Library Build: Finds 29 resumes (once)
4. Gap Analysis: Identifies 14 gaps, 8 unique after deduplication
5. Shared Discovery: 30-minute session surfaces 5 new experiences
   - Kubernetes CI/CD for nonprofits
   - Azure migration for university lab
   - Cross-functional team leadership examples
   - Recent hackathon project
   - Open source contributions
6. Per-Job Processing (×3, each with bullet polish + recruiter evaluation):
   - Job 1 (Microsoft): 85% coverage, recruiter score 79/100 (PASS)
   - Job 2 (Google): 88% coverage, recruiter score 82/100 (PASS)
   - Job 3 (AWS): 78% coverage, recruiter score 65/100 → iteration 2: 72/100 (PASS)
7. Batch Finalization: All 3 resumes reviewed, approved, added to library

RESULT: 3 high-quality resumes in 40 minutes vs 45 minutes sequential
        5 new experiences captured, available for future applications
        Average coverage: 84%, all critical gaps resolved
```

**Example 5: Incremental Batch Addition**
```
WEEK 1:
USER: "I want to apply for 3 jobs: {Microsoft, Google, AWS}"
SKILL: [Processes batch as above, completes in 40 min]

WEEK 2:
USER: "I found 2 more jobs: Stripe and Meta. Add them to my batch?"
SKILL:
1. Load existing batch (includes 5 previously discovered experiences)
2. Intake: Adds Job 4 (Stripe), Job 5 (Meta)
3. Incremental Gap Analysis: Only 3 new gaps (vs 14 original)
   - Payment systems (Stripe-specific)
   - Social networking (Meta-specific)
   - React/frontend (both)
4. Incremental Discovery: 10-minute session for new gaps only
   - Surfaces payment processing side project
   - React work from bootcamp
   - Large-scale system design course
5. Per-Job Processing (×2): Jobs 4, 5 processed with bullet polish + recruiter eval
   - Job 4 (Stripe): recruiter score 76/100 (PASS)
   - Job 5 (Meta): recruiter score 68/100 → iteration 2: 74/100 (PASS)
6. Updated Batch Summary: Now 5 jobs total, 8 experiences discovered

RESULT: 2 additional resumes in 20 minutes (vs 30 min if starting from scratch)
        Time saved by not re-asking 8 previous gaps: ~20 minutes
```

**Example 6: Recruiter Feedback Loop in Action**
```
USER: "I want to apply for Senior TPM at Google. JD: {paste}"

SKILL:
1. Recruiter Intake: Builds evaluation rubric
   - Knockouts: No distributed systems, < 5 years PM experience
   - Scan priorities: TPM title, cloud keywords, metrics visible
2. Library Build + Research + Template + Discovery + Assembly
3. Bullet Polish: 4 bullets rewritten (banned verbs, missing metrics)
4. Generation: MD + DOCX + Report
5. Recruiter Evaluation (Iteration 1): REJECT (Score: 58/100)
   - Scan: 62/100 (keywords buried in wrong section)
   - Detail: 52/100 (weak achievement depth)
   - Feedback: Move cloud keywords to summary, add metrics to top bullets
6. Bullet Polish (Iteration 2): 3 bullets improved per feedback
7. Recruiter Evaluation (Iteration 2): PASS (Score: 74/100)
   - Scan: 78/100 (keywords now prominent)
   - Detail: 68/100 (metrics now visible)

RESULT: Resume passed recruiter screen on second iteration.
        First draft would have been screened out.
```

## Testing Guidelines

**Manual Testing Checklist:**

**Test 1: Happy Path**
```
- Provide JD with clear requirements
- Library with 10+ resumes
- Run all phases without skipping
- Verify generated files
- Check library update
PASS CRITERIA:
- All files generated correctly
- JD coverage >70%
- No errors in any phase
```

**Test 2: Minimal Library**
```
- Provide only 2 resumes
- Run through workflow
- Verify gap handling
PASS CRITERIA:
- Graceful warning about limited library
- Still produces reasonable output
- Gaps clearly identified
```

**Test 3: Research Failures**
```
- Use obscure company with minimal online presence
- Verify fallback to JD-only
PASS CRITERIA:
- Warning about limited research
- Proceeds with JD analysis
- Template still reasonable
```

**Test 4: Experience Discovery Value**
```
- Run with deliberate gaps in library
- Conduct experience discovery
- Verify new experiences integrated
PASS CRITERIA:
- Discovers genuine undocumented experiences
- Integrates into final resume
- Improves JD coverage
```

**Test 5: Title Reframing**
```
- Test various role transitions
- Verify title reframing suggestions
PASS CRITERIA:
- Multiple options provided
- Truthfulness maintained
- Rationales clear
```

**Test 6: Multi-format Generation**
```
- Generate MD, DOCX, PDF, Report
- Verify formatting consistency
PASS CRITERIA:
- All formats readable
- Formatting professional
- Content identical across formats
```

**Test 7: Recruiter Feedback Loop**
```
- Generate resume with deliberately weak bullets
- Verify recruiter rejects with actionable feedback
- Verify feedback routes to correct phase
- Verify iteration improves score
- Verify max iterations reached gracefully
PASS CRITERIA:
- Feedback is specific and actionable
- Score improves across iterations
- Loop terminates at max 3 iterations
- Final output is best version across all iterations
```

**Test 8: Bullet Polish Quality**
```
- Provide bullets with banned verbs and missing metrics
- Verify bullet coach rewrites correctly
- Check XYZ format compliance
- Verify truthfulness preserved
PASS CRITERIA:
- All banned verbs replaced
- 2-of-3 rule enforced
- Metrics preserved from source material
- No fabricated content
```

**Regression Testing:**
```
After any SKILL.md changes:
1. Re-run Test 1 (happy path)
2. Verify no functionality broken
3. Commit only if passes
```
