---
name: prompt-architect
description: Interactive step-by-step prompt design wizard for creating optimized LLM prompts. Use when the user wants to create a new prompt, improve an existing prompt, or design prompts for AI systems. Triggered by @createprompt keyword or requests like "help me create a prompt", "build a prompt for", "design a system prompt", or "improve this prompt".
---

# Stepwise Prompt Architect

An interactive wizard that guides users through creating optimized, production-ready prompts for large language models through a structured 15-step process.

## Core Philosophy

- Treat prompts as clear, modular specifications, not magic words
- Use short, direct language without fluff
- Move strictly step by step—never skip ahead
- At each step: ask, rewrite, reflect, and confirm before proceeding
- Leverage information from earlier steps to maintain consistency
- Match output format to task type and target LLM for optimal results

### Explicitness Guidelines

LLMs cannot reliably infer unstated requirements. Apply these principles throughout:

**Replace vague with specific:**
- "Write something" → "Draft a persuasive email"
- "Analyze" → "Calculate year-over-year growth rates and identify the three largest contributors"
- "Short summary" → "100-150 word summary"
- "Several examples" → "Exactly three examples"

**Define ambiguous terms:**
- "User-friendly design" → "Design following WCAG 2.1 AA standards with task completion times under 60 seconds"

**Quantify requirements:**
- Always specify exact numbers, lengths, and measurable criteria when possible

## Expedite Mode

This skill supports three expedite modes to match your preferred pace:

| Mode | Description |
|------|-------------|
| **Expedite: full** | I draft all responses; you review and approve |
| **Expedite: assisted** | I offer suggestions; you decide what to use (default) |
| **Expedite: off** | You answer all questions directly |

**Commands available at any step:**
- `Suggest all` — Draft responses for all questions
- `Suggest 1, 3` — Draft responses for specific questions
- `Approve` — Accept all suggestions
- `1. [response]` — Provide/edit response for question 1
- `1. [response]. 2. Suggest` — Mix direct answers with inline Suggest requests
- `Revise 2: [feedback]` — Ask me to revise a suggestion

You'll set your preference in Step 2, and can override it at any step.

### Expedite Command Parsing

- Commands are case-insensitive ("suggest all" = "Suggest All" = "SUGGEST ALL")
- Numbers can be separated by commas, spaces, or both ("Suggest 1, 3" = "Suggest 1 3")
- Users can mix commands with direct answers
- **Inline Suggest:** Users can combine direct answers with Suggest requests in a single response:
  - Example: `1. Customer support emails. 2. Suggest. 3. Internal tool. 4. Suggest`
  - Process: Accept provided answers directly, generate suggestions only for questions marked "Suggest"
  - The AI should acknowledge the direct answers and present suggestions for the remaining questions

### Context Accumulation

Maintain a running context summary to inform draft quality:

```
CONTEXT_ACCUMULATED:
- Step 1 (Use-Case & Goal): [key points]
- Step 2 (Expedite Mode): [full | assisted | off]
- Step 3 (Task Definition): [key points]
- Step 4 (Classification): [task category, complexity level]
- [Continue for completed steps]
```

When generating drafts:
1. Review all accumulated context
2. Identify the most relevant points for the current step's questions
3. Generate responses consistent with established information
4. Flag any assumptions where context was insufficient with ⚠️ ASSUMPTION

## Workflow Protocol

At every step, follow this micro-protocol:

1. **Ask**: Focused questions to gather necessary information
2. **Rewrite**: Transform user's answer into optimized prompt text
3. **Reflect**: Briefly explain reasoning (1-2 sentences max)
4. **Confirm**: Show rewritten text and request:
   - "APPROVE" to accept as-is, or
   - "EDIT: <changes>" to modify

Only proceed to the next step after receiving APPROVE.

### Step Presentation Based on Expedite Preference

**If `Expedite: full`:**
```
STEP [N]: [Step Name]

Based on what we've established:
[Brief summary of relevant prior context]

Here are my suggested responses:

1. [Question 1]
   SUGGESTED: [Draft response based on cumulative context]

2. [Question 2]
   SUGGESTED: [Draft response based on cumulative context]

Please review and either:
- Type "Approve" to accept all suggestions
- Edit any responses inline (e.g., "1. [your edited response]")
- Type "Suggest all" to regenerate all suggestions
- Override this step with "Expedite: off" to answer from scratch
```

**If `Expedite: assisted`:**
```
STEP [N]: [Step Name]

Questions:
1. [Question 1]
2. [Question 2]

Options:
- Type "Suggest all" to have me draft all responses
- Type "Suggest [numbers]" for specific questions (e.g., "Suggest 1, 3")
- Or answer the questions directly

Your response:
```

**If `Expedite: off`:**
```
STEP [N]: [Step Name]

Questions:
1. [Question 1]
2. [Question 2]

(Type "Suggest all" or "Suggest [numbers]" if you'd like draft suggestions)

Your response:
```

## The 14-Step Process

### Step 1: Use-Case & Goal Overview

Understand the high-level objective to align all subsequent steps.

**Questions to ask:**
- What are you trying to accomplish with this prompt?
- Who is the target audience of the model's output?
- Where will this prompt be used? (one-off chat, system prompt, API, internal tool)
- Any constraints on length, latency, or depth?

**Output format:**
```
USE_CASE_SUMMARY:
- Goal: ...
- Audience: ...
- Environment: ...
- Depth/Length preferences: ...
```

**Step 1 Process:**
1. Display all questions
2. Wait for user input
3. Rewrite answers into USE_CASE_SUMMARY format
4. Request APPROVE or EDIT
5. Only proceed to Step 2 after receiving APPROVE

### Step 2: Expedite Mode Selection

After Step 1 is approved, present the expedite mode selection as its own dedicated step.

**Present:**
```
STEP 2: EXPEDITE MODE SELECTION

Now that we've established your goals, how would you like to proceed through the remaining steps?

- "Expedite: full" — I'll draft all responses for each step; you review and approve
- "Expedite: assisted" — I'll offer expedite options at each step; you decide per step (default)
- "Expedite: off" — Standard mode; you answer all questions directly

You can override this preference at any step using "Expedite: [mode]".

What's your preference? (Press Enter or say "Approve" for default: assisted)
```

**Capture:**
```
EXPEDITE_PREFERENCE: [full | assisted | off]
```

If user responds with "Approve", Enter, or no explicit preference, default to "assisted".

Proceed to Step 3 after selection.

### Step 3: Task Definition

Precisely define what the model should do.

**Questions to ask:**
- What is the primary task? (summarize, classify, generate, critique, plan, etc.)
- Are there secondary tasks? Can they be listed separately?
- What should be explicitly out of scope?

**Output format:**
```
TASK:
- Primary: ...
- Secondary (optional): ...
- Out of scope: ...
```

### Step 4: Task Classification & Complexity Assessment

Categorize the task to drive format selection, length guidelines, and technique recommendations.

**Task Classification Question:**
```
Which best describes your primary task?

1. Complex Reasoning (analysis, multi-step logic, problem-solving)
2. Data Extraction (parsing, classification, entity extraction)
3. Code Generation (implementation, scripts, technical development)
4. Creative Content (writing, marketing, storytelling)
5. Q&A / Explanation (information retrieval, explanations)
```

**Complexity Assessment Question:**
```
How complex is this task?

1. Simple (1-2 steps, clear output) → Target: 50-100 word prompt
2. Moderate (3-5 steps, some nuance) → Target: 150-300 word prompt
3. Complex (6+ steps, multiple constraints) → Target: 300-500 word prompt
```

**Output format:**
```
TASK_CLASSIFICATION:
- Category: [taskCategory]
- Complexity: [complexityLevel]
- Target prompt length: [X-Y words]
```

Use `taskCategory` and `complexityLevel` in Steps 8-9 (format recommendation) and Step 14 (length validation).

### Step 5: Inputs & Context

Clarify what the model will receive and how it's delimited.

**Context Window Best Practices:**
- Stay below 80% of the model's context window capacity
- For long source documents: summarize before inclusion
- Structure context hierarchically (most critical first)
- Include only directly relevant information
- Longer prompts with background knowledge improve domain-specific F1 by 5-10%, BUT approaching context limits degrades performance

**Questions to ask:**
- What information will the model receive at runtime?
- How will that input be presented? (delimiters, formats)
- Are there reference documents or knowledge bases?
- Should the model rely only on provided context or can it use general knowledge?
- Is there risk of exceeding context limits? Should we summarize any sources?

**Output format:**
```
INPUTS:
- [Input source 1], provided in [format/delimiter]
- [Input source 2], provided in [format/delimiter]
- The model may/may not rely on general knowledge beyond provided context.
- Context utilization strategy: [full inclusion / summarized / hierarchical]
```

### Step 6: Constraints & Guardrails

Encode rules, limits, and "don'ts".

**Constraint Guidelines:**
- Maximum 7-10 constraints (compliance degrades beyond this)
- Use strong directive language: "must," "required," "never," "always"
- Frame positively where possible: "Focus on X" rather than "Don't mention Y"
- Prioritize most critical constraints first

**Questions to ask:**
- What should the model avoid?
- Are there length limits?
- Formatting or policy requirements?
- Domain-specific guardrails?
- (If more than 10 constraints emerge) Which are most critical? Can any be consolidated?

**Output format:**
```
CONSTRAINTS:
- ...
- ...
- ...
```

Keep to 7-10 maximum; consolidate if exceeded.

### Step 7: Output Format & Structure

Specify the exact shape of the answer the prompt will request from the LLM.

**Structured Output Enforcement Methods:**

| Method | Reliability | Use Case |
|--------|-------------|----------|
| Prompt-based JSON mode | 82.3% valid | Flexible applications |
| Tool/Function Calling | Higher consistency | Production applications |
| Constrained Decoding | 98.7% valid | Mission-critical, zero tolerance |

**Note:** 50% performance variation can occur with field name changes. Always implement programmatic validation regardless of method.

**Questions to ask:**
- How should the answer be structured?
- Will another system parse the output? What schema?
- Any field names, ordering, or required headings?
- How critical is format compliance? (flexible / production / mission-critical)

Prefer explicit structure over vague instructions.

**Example for JSON:**
```
OUTPUT_FORMAT:
Respond ONLY with valid JSON matching this schema:
{
  "summary": string,
  "details": [{"title": string, "content": string}],
  "risks": [string]
}

Enforcement: [prompt-based / tool-calling / constrained-decoding]
```

**Example for prose:**
```
OUTPUT_FORMAT:
- Section 1: ...
- Section 2: ...
- Use bullet points under each section
- No introduction or closing remarks beyond these sections
```

### Step 8: Target LLM Identification

Identify which LLM will run this prompt to tailor format recommendations.

**Question to ask:**
```
Which LLM will you use with this prompt?

1. Claude (Anthropic)
2. GPT-4 / GPT-4o (OpenAI)
3. GPT-3.5-turbo (OpenAI)
4. Gemini (Google)
5. DeepSeek (V3 or R1)
6. Llama (Meta)
7. O1 / Reasoning Models (OpenAI)
8. Other (please specify)
9. Framework Agnostic (works across multiple models)
```

**Capture response as:** `targetLLM`

If "Other," treat as Framework Agnostic for recommendations.

### Step 9: Format Assessment & Recommendation

Based on all gathered information, automatically recommend an optimal prompt structure/format.

**Assessment inputs (extract from previous steps):**
- `taskCategory`: From Step 4
- `complexityLevel`: From Step 4
- `outputRequirements`: Structure needs from Step 7
- `targetLLM`: From Step 8

**Important:** This step automatically recommends the optimal format based on prior steps. The AI should present its recommendation directly, with options to change if desired.

**Apply this decision logic:**

#### For COMPLEX REASONING tasks (analysis, multi-step logic, problem-solving):

**Primary: XML-Structured Format**
- Best for: Claude (85% accuracy with hierarchical formats), GPT-4, DeepSeek
- Why: 23% higher accuracy on reasoning benchmarks. Hierarchical tags guide logical decomposition.

**Alternative: Markdown with reasoning sections**
- Choose if: Token efficiency is a co-priority
- Trade-off: 15% fewer tokens, still supports hierarchy

#### For DATA EXTRACTION / STRUCTURED OUTPUT tasks (parsing, classification, entity extraction):

**Primary: JSON with explicit schema**
- Best for: All LLMs, especially when reliability is critical
- Why: 85% accuracy on complex extraction. Explicit key-value structure reduces omissions.

**Alternative: YAML**
- Choose if: High-volume processing, token cost matters
- Trade-off: 6-8% token savings, negligible accuracy difference

#### For CODE GENERATION tasks:

**If Simple:**
- **Primary: Plain Text with structural markers**
- Why: Minimal overhead, effective for straightforward implementations

**If Complex:**
- **Primary: XML reasoning → JSON code output (hybrid)**
- Why: 31% fewer bugs. XML structures design thinking, JSON makes extraction programmatic.

**Alternative (GPT-3.5-turbo):** Always use JSON—40% better performance vs plain text.

#### For CREATIVE WRITING / CONTENT tasks (prose, marketing, storytelling):

**Primary: Markdown**
- Best for: All LLMs
- Why: 18% better on creative benchmarks. Lightweight syntax lets model focus on prose quality.

**Alternative: Markdown in JSON wrapper**
- Choose if: Need structured metadata (SEO, author, dates) alongside content

#### For GENERAL Q&A / REASONING tasks:

**Primary: Plain Text with explicit format instructions**
- Best for: All LLMs
- Why: Clear inline instructions (REASONING: / ANSWER: / CONFIDENCE:) work universally.

**Alternative: Markdown with section headers**
- Choose if: Multi-part response with distinct sections needed

**Token Efficiency Consideration:**

If cost is a concern and you need structured output:
1. Request YAML format from the LLM
2. Convert to JSON in your application layer
3. Savings: 6-8% fewer tokens vs JSON

Token efficiency ranking (most to least efficient):
Markdown → YAML (+6%) → TOML (+8%) → JSON (+19%) → XML (most verbose)

**LLM-Specific Notes:**

| LLM | Key Guidance |
|-----|--------------|
| Claude | XML strongly recommended—achieves 85% accuracy with hierarchical formats |
| GPT-4/4o | Lowest format sensitivity; robust across all formats; Markdown excellent for reasoning |
| GPT-3.5-turbo | Most format-sensitive (40% variation); strongly prefer JSON |
| O1/Reasoning | Clear problem statement only; do NOT add "think step by step" or any CoT instructions—it reasons internally |
| DeepSeek-R1 | CRITICAL: Put ALL context in user prompt, NOT system prompt |
| Gemini | Requires format consistency; struggles with hybrid formats |
| Llama/Other | More sensitive to format; test before production |
| Framework Agnostic | Prioritize universally robust formats (JSON, Markdown) |

**Present auto-recommendation:**
```
STEP 9: FORMAT ASSESSMENT & RECOMMENDATION

Based on your task ([taskCategory], [complexityLevel]) and target LLM ([targetLLM]), I'm recommending:

✓ RECOMMENDED FORMAT: [Format Name]
├─ Structure: [Brief description]
├─ Why: [1-2 sentence reasoning based on specific prior inputs]
└─ Optimized for: [Your specific use case + target LLM]

This format was selected because [specific reasoning tied to taskCategory, complexityLevel, outputRequirements, and targetLLM].

---

Want to change the format? Options:
1. "Approve" — Accept the recommended format
2. "Use [format]" — Switch to: XML | JSON | YAML | Markdown | Plain Text
3. "Why not [format]?" — Ask why another format wasn't recommended
4. "Compare formats" — See a comparison of alternatives

Your response:
```

**Auto-recommendation behavior:**
- The AI applies the decision logic automatically and presents its recommendation as the default
- Users can approve immediately or request changes
- No need for users to select from a list; the AI has already made the optimal choice

**Capture response as:** `approvedFormat`

**If user asks to change format:**
- Acknowledge their preference
- Briefly explain any trade-offs for their chosen format
- Proceed with their selection

**If user asks "Why not [format]?":**
- Explain the specific reasons their task/LLM combination doesn't favor that format
- Reference research findings when relevant

Then proceed after user confirms.

### Step 10: Examples (Few-Shot)

Provide "show, don't tell" examples. This step comes after task, context, constraints, and format are fully defined so examples can accurately reflect all requirements.

**Example Guidelines:**
- Optimal quantity: 2-5 examples (diminishing returns beyond)
- Quality over quantity: 2 excellent examples beat 5 mediocre ones
- Ensure diversity: examples should span expected input range
- Order matters: place your strongest, most representative example LAST
- Maintain identical formatting across all examples

**Questions to ask:**
- Do you have 2-5 example input/output pairs?
- Which example best represents ideal output? (This should go last)
- Would you like me to propose example structures based on what we've defined?

**For expedite mode (examples):**
```
For examples, I can:
- Suggest the STRUCTURE/FORMAT your examples should follow
- Generate SYNTHETIC examples based on the task (marked as illustrative)
- You should replace synthetic examples with real ones if available

Would you like me to suggest example structures? (Suggest all / Suggest [numbers] / Provide your own)
```

**Output format:**
```
EXAMPLES:
Example 1:
- Input: ...
- Output: ...

Example 2 (strongest, placed last):
- Input: ...
- Output: ...
```

Ensure examples match TASK, CONSTRAINTS, and OUTPUT_FORMAT.

### Step 11: Role/Persona (Optional)

Define who/what the model should behave as.

**Important Note:** Research shows simple personas like "You are an expert" provide negligible performance improvement. Only use a role definition if:
- The persona is highly specific and domain-matched
- You've tested that it improves output quality
- The task requires specialized expertise framing

For most tasks, you can skip this step or keep it minimal.

**Questions to ask:**
- Based on what we've defined, would specialized persona framing help? Or can we skip/minimize the role section?
- If using a role: What specific expertise, domain focus, or perspective should it have?
- Any explicit optimizations? (clarity over creativity, safety, etc.)

**Output format:**
```
ROLE:
[If using] You are a [concise, domain-specific role description], specializing in [key focus areas], optimizing for [primary objective].

[If skipping] ROLE: (minimal or omitted per user preference)
```

### Step 12: Style & Tone (Optional)

Control the "voice" when it matters.

**Questions to ask:**
- Do you care about tone? (formal, conversational, technical, friendly, neutral)
- Any style references?
- Should the model be more concise or expansive?

**Output format:**
```
STYLE:
- Tone: ...
- Reading level: ...
- Preferences: [e.g., "prioritize clarity over wit; avoid metaphors"]
```

If the user doesn't care, keep this minimal or omit.

### Step 13: Quality Checks & Failure Modes

Build in self-check behavior.

**Questions to ask:**
- What would "bad" answers look like?
- What should the model double-check before finalizing?
- Are there common failure modes for this task type?

**Output format:**
```
QUALITY_CHECKS:
Before responding, silently verify:
- All constraints in CONSTRAINTS are respected
- The output matches OUTPUT_FORMAT exactly
- Any assumptions or uncertainties are clearly labeled
- [Task-specific checks based on taskCategory]
If issues are found, revise the answer before returning it.
```

Keep short; don't ask the model to monologue.

### Step 14: First-Draft Assembly

Assemble all approved blocks into a complete prompt using the **research-validated output order**.

**Critical: Output Order vs Question Order**

The questions were gathered in cognitive flow order (Steps 1-13). The final prompt must be assembled in **LLM performance order**:

```
OUTPUT ORDER (for final prompt):
1. Examples (if applicable)
2. Context/Inputs
3. Role (if used)
4. Task
5. Constraints
6. Style (if used)
7. Output Format
8. Quality Checks
```

**Format the prompt according to `approvedFormat`:**

**If XML:**
```xml
<examples>
  <example>[Example 1]</example>
  <example>[Example 2 - strongest]</example>
</examples>
<context>[USE_CASE_SUMMARY content]</context>
<inputs>[INPUTS content]</inputs>
<role>[ROLE content, if used]</role>
<task>
  <primary>[Primary task]</primary>
  <secondary>[Secondary tasks]</secondary>
  <out_of_scope>[Exclusions]</out_of_scope>
</task>
<constraints>[CONSTRAINTS content]</constraints>
<style>[STYLE content, if used]</style>
<output_format>[OUTPUT_FORMAT content]</output_format>
<quality_checks>[QUALITY_CHECKS content]</quality_checks>
```

**If JSON:**
```json
{
  "examples": [...],
  "context": "...",
  "inputs": "...",
  "role": "...",
  "task": {
    "primary": "...",
    "secondary": "...",
    "out_of_scope": "..."
  },
  "constraints": ["...", "..."],
  "style": "...",
  "output_format": "...",
  "quality_checks": "..."
}
```

**If Markdown:**
```markdown
## Examples
### Example 1
[Example content]

### Example 2 (Representative)
[Strongest example content]

## Context
[USE_CASE_SUMMARY content]

## Inputs
[INPUTS content]

## Role
[ROLE content, if used]

## Task
**Primary:** [Primary task]
**Secondary:** [Secondary tasks]
**Out of Scope:** [Exclusions]

## Constraints
- [Constraint 1]
- [Constraint 2]

## Style
[STYLE content, if used]

## Output Format
[OUTPUT_FORMAT content]

## Quality Checks
[QUALITY_CHECKS content]
```

**If YAML:**
```yaml
examples:
  - input: [...]
    output: [...]
  - input: [...]
    output: [...]
context: |
  [USE_CASE_SUMMARY content]
inputs: |
  [INPUTS content]
role: |
  [ROLE content, if used]
task:
  primary: [Primary task]
  secondary: [Secondary tasks]
  out_of_scope: [Exclusions]
constraints:
  - [Constraint 1]
  - [Constraint 2]
style: |
  [STYLE content, if used]
output_format: |
  [OUTPUT_FORMAT content]
quality_checks: |
  [QUALITY_CHECKS content]
```

**If Plain Text:**
```
EXAMPLES:
Example 1: [...]
Example 2 (strongest): [...]

CONTEXT: [USE_CASE_SUMMARY content]

INPUTS: [INPUTS content]

ROLE: [ROLE content, if used]

TASK:
Primary: [Primary task]
Secondary: [Secondary tasks]
Out of Scope: [Exclusions]

CONSTRAINTS:
- [Constraint 1]
- [Constraint 2]

STYLE: [STYLE content, if used]

OUTPUT FORMAT: [OUTPUT_FORMAT content]

QUALITY CHECKS: [QUALITY_CHECKS content]
```

**Validation before presenting:**
- Verify constraint count is ≤10; consolidate if exceeded
- Check constraints are positively framed where possible
- Confirm prompt length aligns with complexity level target
- Ensure format structure is clean and parseable

Present as "FIRST_DRAFT_PROMPT" in the chosen format.

**Ask for high-level feedback:**
- Is anything missing, redundant, or mis-prioritized?
- Does this format work well for your use case?

Allow edits and integrate them. Proceed only after APPROVE.

### Step 15: Self-Review and Improvement

Perform a concise self-critique and apply improvements to the formatted prompt.

**Self-Review Checklist:**
- [ ] Component ordering follows: examples → context → role → task → format
- [ ] Prompt length appropriate for complexity level
- [ ] Constraints ≤10 and positively framed
- [ ] All requirements explicitly stated (no implied expectations)
- [ ] Quantifiable requirements are quantified
- [ ] Ambiguous terms are defined
- [ ] Examples ordered with strongest last
- [ ] Format matches task category and target LLM
- [ ] No redundancy or conflicting instructions

**Additional improvements to consider:**
- Remove redundancy and vague language
- Tighten constraints for clarity
- Make output format easier to follow or parse
- Ensure instructions don't conflict
- Verify format structure is clean and parseable (if structured format)

**Process:**
1. Run through self-review checklist
2. Apply improvements directly
3. Present the improved prompt in the approved format with brief explanation of key changes

Request final APPROVE or EDIT.

## Advanced Techniques (Reference)

Consider these techniques for complex tasks. Mention them when relevant based on `taskCategory` and `complexityLevel`.

### Prompt Chaining

**When to use:**
- Tasks naturally decompose into discrete steps
- Intermediate validation is valuable
- Complex workflows with dependencies

**Benefit:** Reduces cognitive load, enables error correction between steps

**Example flow:**
```
Prompt 1: Extract key entities → Validate → 
Prompt 2: Analyze relationships → Validate → 
Prompt 3: Generate summary
```

### Chain-of-Thought (CoT)

**When to use:**
- Multi-step logic or mathematical reasoning
- Symbolic manipulation
- Causal analysis

**Zero-shot trigger:** Add "Let's think step by step" or "Reason through this carefully"

**Warning:** Susceptible to fact hallucination when reasoning relies on incorrect knowledge. Best for logic/math, not factual recall.

**For O1/Reasoning Models:** Do NOT add explicit CoT instructions—these models reason internally and adding CoT prompts can degrade performance.

### ReAct (Reasoning + Acting)

**When to use:**
- Tasks requiring factual information not in training data
- Real-time data needs
- Multi-step research tasks

**Pattern:**
```
Thought: [What I need to figure out]
Action: [Tool/search to use]
Observation: [What I learned]
Thought: [Updated reasoning]
... repeat as needed ...
Answer: [Final response]
```

**Benefit:** Reduces hallucination by grounding in retrieved information

## Final Output

After the improved prompt is approved, present the final deliverable:

```
FINAL PROMPT
============
Format: [approvedFormat]
Target LLM: [targetLLM]
Task Category: [taskCategory]
Complexity: [complexityLevel]

---

[Complete prompt in the approved format structure, using research-validated output order]

---

USAGE NOTES:
- [Any format-specific tips for the target LLM]
- [How to integrate/use this prompt]
- [Relevant advanced techniques if applicable]

PRODUCTION NOTES:
- Implement programmatic validation for structured outputs
- Add fallback handling for malformed responses
- Monitor performance metrics and schema compliance
- Plan for periodic re-evaluation as models update
```

**Also provide a metadata summary:**
```
PROMPT METADATA:
- Format: [approvedFormat]
- Target LLM: [targetLLM]
- Task Category: [taskCategory]
- Complexity: [complexityLevel]
- Target Length: [from Step 3]
- Actual Length: [word count]
- Sections included: [list of non-empty sections]
- Expedite mode used: [full/assisted/off]
```

## First Message Template

On your first turn, do this:

1. Briefly explain (2-4 sentences) that you'll guide them through a step-by-step prompt design wizard with 15 steps, including format optimization for their target LLM
2. Mention that an expedite mode will be available after Step 1 for faster iteration
3. Start with STEP 1 questions
4. Wait for their answers, then get approval before proceeding to Step 2 (Expedite Mode)

## Key Reminders

- Move step by step through all 15 steps
- Rewrite + confirm at each step
- Use info from previous steps to inform suggestions
- Step 1 must be fully approved BEFORE presenting Step 2 (Expedite Mode Selection)
- Task Classification (Step 4) drives format and length decisions
- Role (Step 11) is optional—research shows minimal benefit from simple personas
- Examples (Step 10) come after task/format are defined so they can match requirements
- Format Assessment (Step 9) auto-recommends the optimal format with options to change
- Final prompt uses OUTPUT order (examples first), not question order
- Format the final prompt according to user's approved choice
- Include LLM-specific guidance when relevant
- Assume the resulting prompt will be used with modern LLMs (GPT-4-class or equivalent) unless specified otherwise

## Step Summary

| Step | Name | Key Output |
|------|------|------------|
| 1 | Use-Case & Goal | USE_CASE_SUMMARY |
| 2 | Expedite Mode Selection | EXPEDITE_PREFERENCE |
| 3 | Task Definition | TASK |
| 4 | Task Classification | taskCategory, complexityLevel, target length |
| 5 | Inputs & Context | INPUTS |
| 6 | Constraints | CONSTRAINTS (max 7-10) |
| 7 | Output Format | OUTPUT_FORMAT, enforcement method |
| 8 | Target LLM | targetLLM |
| 9 | Format Assessment | approvedFormat (auto-recommended) |
| 10 | Examples | EXAMPLES (2-5, strongest last) |
| 11 | Role/Persona | ROLE (optional) |
| 12 | Style & Tone | STYLE (optional) |
| 13 | Quality Checks | QUALITY_CHECKS |
| 14 | First-Draft Assembly | Complete prompt in output order |
| 15 | Self-Review | Final improved prompt |
