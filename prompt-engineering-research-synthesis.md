# Optimal Prompt Engineering: A Research Synthesis

## Executive Summary

This document synthesizes research findings from 150+ sources on optimal prompt format, structure, and design to establish evidence-based guidelines for constructing effective prompts for state-of-the-art large language models (LLMs).

### Core Findings

**Format selection impacts performance by up to 40%** on specific tasks, but no single format universally outperforms others. Optimal format depends on task type, target LLM, and operational priorities (accuracy, speed, or cost).

**Structure and component ordering significantly impact performance.** Research demonstrates that placing examples first and instructions last optimizes token-by-token prediction mechanisms inherent to LLM architectures.

**Longer prompts with substantive background knowledge consistently outperform shorter variants** across domain-specific tasks, improving F1-scores by 5-10%. However, this must be balanced against the "context window paradox"—performance degrades as input length approaches context limits.

**Simple persona prompting provides minimal performance gains.** Comprehensive evaluations reveal effect sizes too small to justify routine use unless personas are highly specific, detailed, and domain-matched.

**Any consistent structure outperforms unstructured text.** The specific format (JSON vs XML vs Markdown) matters less than using structure appropriately for the task.

### Key Metrics

| Factor | Impact |
|--------|--------|
| Format selection | Up to 40% performance variation |
| XML vs JSON for reasoning | XML achieves 23% higher accuracy |
| Background knowledge inclusion | +5-10% F1-score improvement |
| Structured generation | Up to 60% boost on math benchmarks |
| YAML vs JSON token efficiency | 48% token savings with YAML |
| Constrained decoding reliability | 98.7% valid output rate vs 82.3% baseline |

---

## Decision Flow: Designing Effective Prompts

This section guides you through the sequential decisions required to design an optimal prompt, from initial task analysis through final implementation.

---

### Step 1: Classify Your Task

Before selecting formats or components, classify your task to determine which optimization strategies apply.

#### Task Categories

| Category | Characteristics | Examples |
|----------|----------------|----------|
| **Complex Reasoning** | Multi-step logic, mathematical deduction, causal analysis | Financial modeling, legal analysis, scientific reasoning |
| **Data Extraction** | Structured information from unstructured sources | Resume parsing, invoice processing, entity extraction |
| **Code Generation** | Software development, scripts, technical implementation | Feature implementation, bug fixes, code translation |
| **Creative Content** | Writing, storytelling, marketing copy | Blog posts, marketing materials, narrative content |
| **Q&A / Explanation** | Information retrieval, explanations, natural language reasoning | Customer support, documentation queries, educational content |

#### Complexity Assessment

**Simple Tasks** (1-2 steps, clear output):
- Summaries, brief explanations, standard queries, simple classifications
- Estimated prompt length: 50-100 words

**Moderate Tasks** (3-5 steps, some nuance):
- Analysis, outlines, creative briefs, moderate data extraction
- Estimated prompt length: 150-300 words

**Complex Tasks** (6+ steps, multiple constraints):
- Intricate specifications, technical documentation, comprehensive reports
- Estimated prompt length: 300-500 words

> **Decision Point**: If your task is simple and doesn't require structured output, a well-crafted plain text prompt with clear instructions may be sufficient. Proceed to Step 2 only if format optimization is warranted.

---

### Step 2: Select Optimal Format

Format selection should be driven by task requirements, not personal preference. Each format has measurable strengths and weaknesses.

#### Format Comparison Matrix

| Format | Best For | Strengths | Weaknesses | Token Efficiency |
|--------|----------|-----------|------------|------------------|
| **XML** | Complex reasoning, Claude models | Semantic tags guide attention, hierarchical structure, clear boundaries | Most verbose, higher token cost | Lowest |
| **JSON** | Data extraction, structured output | Explicit key-value structure, easy parsing, type constraints | Field name sensitivity (50% more variation than tool calling), verbose | Low |
| **YAML** | High-volume structured tasks | Comparable accuracy to JSON, 6-8% fewer tokens | Less common parsing support | Medium |
| **Markdown** | Creative content, GPT models | Lightweight, training-data prevalent, natural prose flow | Less suitable for strict schemas | High |
| **Plain Text** | Q&A, simple tasks, explanations | Minimal overhead, flexible | No structural guarantees | Highest |

#### Task-to-Format Recommendations

```
Complex Reasoning     → XML with semantic tags
Data Extraction       → JSON or YAML with explicit schema
Code Generation       → Plain text (simple) or XML→JSON hybrid (complex)
Creative Content      → Markdown
Q&A / Explanation     → Plain text with format instructions
```

#### Model-Specific Format Preferences

| Model Family | Preferred Format | Notes |
|--------------|------------------|-------|
| **Claude (Anthropic)** | XML | Documented preference from training data; 85% accuracy with hierarchical formats |
| **GPT-4/4o (OpenAI)** | Markdown | Lowest format sensitivity; Markdown preferred for reasoning |
| **GPT-3.5-turbo** | JSON | Higher format sensitivity; benefits from explicit structure |
| **O1 Reasoning Models** | Clear problem statement | Avoid explicit CoT instructions; model reasons internally |
| **Gemini (Google)** | Pure JSON or Markdown | Requires format consistency; struggles with hybrid formats |
| **DeepSeek-V3** | JSON or XML | Supports strict JSON mode; follows XML "flawlessly" |
| **DeepSeek-R1** | User prompt only | All context should be in user prompt, not system prompt |
| **Llama / Open-source** | Markdown | Higher format sensitivity than frontier models; test extensively |

> **Decision Point**: Select your primary format based on task category and target model. If cost is a major concern and you need structured output, request YAML and convert to JSON in your application layer.

---

### Step 3: Select Prompt Components

Not all components are required for every prompt. Select based on task requirements.

#### Component Reference

| Component | Required? | Purpose | When to Include |
|-----------|-----------|---------|-----------------|
| **Task Directive** | **Always** | Specifies the action to perform | Every prompt |
| **Context** | Usually | Background information for informed responses | Domain-specific tasks, complex analysis |
| **Constraints** | Often | Rules, limits, and boundaries | When output must meet specific criteria |
| **Output Format** | Often | Structure for the response | When parsing output or specific presentation needed |
| **Examples (Few-shot)** | Sometimes | Demonstrate desired patterns | Complex formatting, classification tasks, subtle distinctions |
| **Role/Persona** | Rarely | Assign expert identity | Only when highly specific expertise is critical AND tested |

#### Component Guidelines

**Task Directive** (Required)
- Use specific, actionable verbs: "analyze," "summarize," "synthesize," "calculate"
- Quantify requirements: word counts, number of items, time ranges
- Define ambiguous terms explicitly
- Example weak: "Write about climate change"
- Example strong: "Write a 300-word summary of the three primary anthropogenic causes of climate change for a high school audience, using one concrete example for each cause"

**Context**
- Include only directly relevant information
- Structure hierarchically (most critical first)
- Use explicit labels: `Background:`, `Relevant Facts:`, `Prior Context:`
- For long documents: summarize before inclusion rather than wholesale insertion
- Stay below 70-80% of context window capacity

**Constraints and Requirements**
- Organize as bulleted or numbered lists
- Use strong directive language: "must," "required," "never," "always"
- Limit to 7-10 constraints maximum (compliance degrades beyond this)
- Frame positively: state what to include, not just what to avoid
- Example: "Focus exclusively on product features" rather than "Do not mention price"

**Output Format**
- For structured data: provide explicit schema with field types
- For natural language: specify organizational elements (headers, sections)
- Consider enforcement method (JSON mode, tool calling, constrained decoding) based on reliability requirements

**Few-Shot Examples**
- Optimal quantity: 2-5 examples (diminishing returns beyond this)
- Quality supersedes quantity
- Ensure diversity: examples should span expected input range
- Maintain identical formatting across all examples
- Order matters: place strongest, most representative example last

**Role/Persona** (Use Sparingly)
- Default to no persona for general tasks
- Research shows simple personas ("You are an expert") provide negligible benefit
- If used: make detailed, domain-specific, and test for actual improvement
- LLM-generated personas often outperform human-written ones
- Avoid demographics/social constructs unless testing for bias

> **Decision Point**: Start with the minimum necessary components. Add complexity only when testing shows improvement.

---

### Step 4: Order Components Optimally

Component ordering significantly impacts performance due to LLMs' autoregressive (left-to-right) token prediction.

#### Research-Validated Optimal Sequence

```
1. Few-shot examples (if using)
2. Context / background information
3. Role / persona (if using)
4. Task directive / instruction
5. Output format specification
```

#### Rationale for This Ordering

| Position | Component | Rationale |
|----------|-----------|-----------|
| First | Examples | Establishes patterns before task-specific details; prevents interpretation as prompt continuation |
| Second | Context | Model processes background before attempting task; mirrors human problem-solving |
| Third | Role | Persona operates with full awareness of situational background |
| Fourth | Instruction | Prevents model from "continuing" the prompt; signals "now perform this action" |
| Fifth | Output Format | Remains fresh in attention window as generation begins; maximizes compliance |

#### Alternative Framework Orderings

Different frameworks propose variations, but all converge on key principles:

| Framework | Order | Best For |
|-----------|-------|----------|
| **CARE** | Context → Ask → Rules → Examples | Conversational interfaces |
| **KERNEL** | Context → Task → Constraints → Format | Programming-style specifications |
| **CRAFT** | Capability → Role → Action → Format → Tone | Non-technical documentation |

> **Key Principle**: Context before instructions, examples early if used, format specifications near the end.

---

### Step 5: Determine Optimal Length

Prompt length has a non-monotonic relationship with performance—both too short and too long degrade results.

#### Length Guidelines by Complexity

| Task Complexity | Recommended Length | Key Consideration |
|-----------------|-------------------|-------------------|
| Simple | 50-100 words | Minimal context needed |
| Moderate | 150-300 words | Optimal range for most tasks |
| Complex | 300-500 words | Include substantial background |
| Upper boundary | Avoid >500-1000 words | Diminishing returns; "prompt bloat" |

#### The Context Window Paradox

Research reveals competing effects:

**Longer prompts with background knowledge improve domain-specific performance:**
- Query Intent Classification: +10% recall, +8% F1-score
- Financial Sentiment Analysis: +8% recall, +5% F1-score
- Technical System Behavior: +9% recall, +7% F1-score

**BUT: Performance degrades as input approaches context limits:**
- Focused prompts (~300 tokens) dramatically outperform long-context variants
- Even frontier models with million-token windows show accuracy drops
- Degradation occurs non-uniformly and unpredictably

#### Resolution: Optimize for Informative Density

| Do | Don't |
|----|-------|
| Include necessary background knowledge | Add redundant explanations |
| Eliminate repetitive content | Include tangentially related details |
| Structure for scannability (headers, bullets) | Write dense paragraphs |
| Stay well below 80% context utilization | Approach context window limits |
| Summarize long source documents | Insert documents wholesale |

> **Decision Point**: Include enough context to provide necessary background, but optimize every word for relevance. When in doubt, test a compressed version against your original.

---

### Step 6: Apply Effective Writing Style

The linguistic style and structural organization of prompt text significantly impacts model comprehension.

#### Core Principle: Explicit Over Implicit

LLMs cannot reliably infer unstated requirements. State everything explicitly.

| Implicit (Weak) | Explicit (Strong) |
|-----------------|-------------------|
| "Analyze this company" | "Analyze this company's financial performance over the past three years, focusing on revenue growth, profit margins, and cash flow trends. Compare metrics to industry averages." |
| "Summarize the article" | "Write a 150-word summary of this article's main argument and supporting evidence, written for readers with no background in this topic." |
| "Create a report" | "Create a 5-section report: (1) Executive Summary (150 words), (2) Methodology (200 words), (3) Findings (400 words), (4) Recommendations (200 words), (5) Limitations (150 words). Use markdown with ## for sections." |

#### Techniques for Explicitness

**Replace vague verbs:**
- "Write something" → "Draft a persuasive email"
- "Analyze" → "Calculate year-over-year growth rates and identify the three largest contributors"
- "Explain" → "Provide a step-by-step explanation for someone with no prior knowledge"

**Quantify requirements:**
- "Short summary" → "100-150 word summary"
- "Several examples" → "Exactly three examples"
- "Recent data" → "Data from 2023-2025"

**Define ambiguous terms:**
- "User-friendly design" → "Design following WCAG 2.1 AA standards with task completion times under 60 seconds"

#### Structural Organization

**Use distinct sections with headers:**
```
## Context
[Background information]

## Task
[Specific instruction]

## Constraints
- [Constraint 1]
- [Constraint 2]

## Output Format
[Format specification]
```

**Use numbered/bulleted lists for multiple items:**
```
Your response must:
1. Address all three stakeholder perspectives
2. Include at least two data points per recommendation
3. Identify potential implementation risks
4. Provide a timeline with specific milestones
```

**Group related content together** and present in logical order (general → specific, background → action).

#### Delimiter Selection

Delimiters mark boundaries between sections and provide semantic signaling.

| Delimiter Type | Format | Best For |
|----------------|--------|----------|
| Special characters | `###`, `---`, `===` | Simple prompts, quick separation |
| XML tags | `<context>`, `<task>` | Complex prompts, semantic clarity |
| Markdown headers | `## Section` | Natural language tasks, readability |

**XML tags offer advantages for complex prompts:**
- Semantic information at both opening and closing boundaries
- "Context collapse" signals conceptual unit completion
- Natural support for hierarchical nesting
- Reduced ambiguity for nested structures

#### Constraint Specification

| Practice | Example |
|----------|---------|
| Strong directive language | "Must include..." not "Should include..." |
| Specific limits | "Exactly 5 examples" not "Around 5 examples" |
| Positive framing | "Focus on features" not "Don't mention price" |
| Bulleted format | Organized lists, not embedded in paragraphs |

#### Imperative vs. Declarative Style

| Style | Use When | Example |
|-------|----------|---------|
| **Imperative** | Specific sequence matters | "Do X. Then do Y. Then do Z." |
| **Declarative** | Outcome matters more than process | "The output should include X, Y, and Z properties" |

> **Recommendation**: Use declarative for high-level goals and analytical tasks; reserve imperative for procedural tasks with required step ordering.

---

### Step 7: Consider Advanced Techniques

For complex tasks where single-prompt optimization is insufficient, consider these advanced approaches.

#### Prompt Chaining

**What it is**: Breaking complex workflows into sequential prompts where each step's output feeds the next.

**When to use**:
- Tasks naturally decompose into discrete steps
- Intermediate validation or human review is valuable
- Single prompts consistently fail but subtasks succeed
- Token limits constrain single-prompt approaches

**Benefits**:
- Reduces cognitive load on the model
- Enables error correction at intermediate steps
- Supports diverse decomposition structures (hierarchical, recursive, sequential)

#### Chain-of-Thought (CoT) Prompting

**What it is**: Instructing models to generate step-by-step reasoning before final answers.

**Zero-shot CoT**: Add "Let's think step by step" to trigger reasoning without examples.

**Few-shot CoT**: Provide examples demonstrating step-by-step reasoning patterns.

**When to use**:
- Multi-step logic, mathematical reasoning, symbolic manipulation
- Tasks where intermediate steps can be validated

**Limitations**:
- Susceptible to fact hallucination when reasoning relies on incorrect knowledge
- Error propagation when early mistakes compound

#### ReAct (Reasoning + Acting)

**What it is**: Interleaving reasoning traces with actions that retrieve external information.

**Pattern**:
```
Thought: [Reasoning about what information is needed]
Action: [Search query, API call, tool invocation]
Observation: [Result from external source]
Thought: [Reasoning incorporating the observation]
...
Answer: [Final response grounded in retrieved information]
```

**When to use**:
- Tasks require factual information not in training data
- Answers depend on current/dynamic information
- Verification against authoritative sources is critical

**Benefit**: Reduces hallucination by grounding reasoning in factual information.

#### Automated Prompt Optimization

For high-stakes applications where manual optimization plateaus:
- Foundation model-based optimization (LLMs refining prompts based on feedback)
- Evolutionary methods (genetic algorithms mutating prompt components)
- Gradient-based optimization (treating prompts as continuous embeddings)

**When to consider**:
- High-stakes applications with measurable performance metrics
- Large-scale deployments where small improvements yield significant ROI
- Scenarios requiring adaptation across multiple models or domains

---

### Step 8: Implement Structured Output (If Required)

When outputs must conform to specific schemas, choose an enforcement method based on reliability requirements.

#### Method Comparison

| Method | Reliability | Use Case | Trade-offs |
|--------|-------------|----------|------------|
| **Prompt-based JSON mode** | 82.3% valid output | Flexible applications, rapid iteration | 50% performance variation with field name changes |
| **Tool/Function Calling** | Higher consistency | Production applications | Requires function definitions, model support |
| **Constrained Decoding** | 98.7% valid output | Mission-critical, zero tolerance for errors | Implementation complexity, potential speed impact |

#### Schema Design Best Practices

- Use descriptive, semantic field names that help the model understand purpose
- Provide field descriptions for complex schemas
- Test schema variations—minor naming changes can impact performance by 30%
- For JSON mode: include "JSON" explicitly in prompt text and response format parameter

#### Validation Requirements

**Never assume LLM outputs are correct.** Regardless of format or enforcement method:
- Implement programmatic validation (JSON schema validators, type checking)
- Add fallback handling for malformed responses
- Monitor schema compliance rates in production

---

## Task-Specific Reference Guide

This section provides quick-reference templates for common task categories.

### Complex Reasoning Tasks

**Optimal format**: XML with semantic tags

**Structure template**:
```xml
<context>
[Background and relevant information]
</context>

<problem>
[Specific question or challenge]
</problem>

<constraints>
[Limitations, assumptions, boundaries]
</constraints>

<reasoning_framework>
<step_1>Identify key variables and relationships</step_1>
<step_2>Analyze causal connections</step_2>
<step_3>Evaluate alternative explanations</step_3>
<step_4>Synthesize conclusion with supporting evidence</step_4>
</reasoning_framework>

<output_requirements>
[Format and content specifications]
</output_requirements>
```

**Length**: 300-500 words including context and framework

**Consider**: Chain-of-Thought or ReAct for multi-step reasoning

---

### Data Extraction Tasks

**Optimal format**: JSON or YAML with explicit schema

**Structure template**:
```
Task: Extract the following information from the provided document.

Required Fields:
- field_1: [description and expected type]
- field_2: [description and expected type]
- field_3: [description and expected type]

Constraints:
- Use null for missing information; do not invent data
- Classify field_2 as one of: [option_a, option_b, option_c]
- Format dates as YYYY-MM-DD

Example:
Input: [sample document]
Output: {
  "field_1": "value",
  "field_2": "option_a",
  "field_3": "2025-01-18"
}
```

**Length**: 150-300 words including schema and 1-2 examples

**Token efficiency**: Use YAML for high-volume applications (6-8% savings vs JSON)

---

### Code Generation Tasks

**Simple tasks** (straightforward implementation):
```
Task: Implement [specific functionality] in [language/framework]

Requirements:
1. [Requirement 1]
2. [Requirement 2]
3. [Requirement 3]

Consider:
- Error handling for [specific edge cases]
- Performance implications for [usage context]
- Code style following [standard/convention]
```

**Complex tasks** (architectural decisions required):
```xml
<code_requirements>
  <architecture>[Design constraints and patterns]</architecture>
  <integration>[How this connects to existing systems]</integration>
  <performance>[Speed, memory, scalability requirements]</performance>
</code_requirements>

<implementation_guidance>
[Step-by-step approach]
</implementation_guidance>

<output_format>
{
  "reasoning": "Explain architectural approach",
  "code": "Implementation here",
  "tests": "Test cases here",
  "considerations": ["Trade-offs", "Alternatives"]
}
</output_format>
```

**Length**: 100-200 words (simple), 300-500 words (complex)

**Note**: Hybrid XML→JSON reduces bugs by 31% for complex tasks

---

### Creative Content Tasks

**Optimal format**: Markdown with declarative specifications

**Structure template**:
```
Task: [Type of content] for [audience] with [tone/style]

Content Requirements:
- Length: [word count or section breakdown]
- Structure: [organizational scheme]
- Key points: [main topics to address]
- Style: [voice, formality, perspective]

Example of desired voice:
[Short sample demonstrating tone]

---

Begin content:
```

**Length**: 100-250 words (avoid over-constraining creativity)

**Note**: Markdown outperforms JSON/XML by 18% on creative tasks

---

### Q&A / Explanation Tasks

**Optimal format**: Plain text with explicit format instructions

**Structure template**:
```
Question: [Specific question with necessary context]

Respond in this format:
REASONING: Explain your step-by-step logic
ANSWER: Provide your direct response
CONFIDENCE: [High/Medium/Low] with brief justification
SOURCES: [If applicable, cite knowledge sources or note limitations]
```

**Length**: 50-150 words depending on question complexity

---

## Token Efficiency Reference

When cost optimization matters, format selection directly impacts API expenses.

### Token Efficiency Ranking (Most to Least Efficient)

1. **Markdown**: Baseline (11,612 tokens for test content)
2. **YAML**: +6% vs Markdown
3. **TOML**: +8% vs Markdown
4. **JSON**: +19% vs Markdown
5. **XML**: Most verbose (variable overhead)

### Cost Impact at Scale

For 1 million GPT-4 API requests per month:
- Switching from JSON to YAML saves ~190 tokens per request
- Estimated monthly savings: ~$11,400 (at 2023 pricing)

### Strategy: Request YAML, Convert to JSON

If downstream systems require JSON:
1. Request YAML format from the LLM
2. Convert to JSON in your application layer
3. Capture token savings while maintaining parsing compatibility

---

## Evaluation Criteria Summary

Use these criteria to evaluate prompt quality and alignment with research-backed best practices.

### Format Selection Criteria

| Criterion | Evaluation Question |
|-----------|-------------------|
| Task alignment | Is the format appropriate for the task category? |
| Model alignment | Does the format match the target model's preferences? |
| Token efficiency | Is the format optimized for cost given volume requirements? |
| Schema appropriateness | Is the level of structure appropriate for output requirements? |

### Component Criteria

| Criterion | Evaluation Question |
|-----------|-------------------|
| Task directive clarity | Is the instruction specific, actionable, and unambiguous? |
| Context relevance | Is all included context directly relevant? Is necessary context present? |
| Constraint quality | Are constraints specific, limited in number, positively framed? |
| Example effectiveness | Are examples representative, diverse, and consistently formatted? |
| Persona justification | If used, is the persona detailed, domain-specific, and tested? |

### Structure Criteria

| Criterion | Evaluation Question |
|-----------|-------------------|
| Component ordering | Does ordering follow research-validated sequence? |
| Length appropriateness | Is length appropriate for task complexity? |
| Visual hierarchy | Are sections clearly delineated with headers/delimiters? |
| Delimiter effectiveness | Do delimiters provide semantic clarity? |

### Style Criteria

| Criterion | Evaluation Question |
|-----------|-------------------|
| Explicitness | Are requirements stated explicitly rather than implied? |
| Quantification | Are measurable requirements quantified? |
| Term definition | Are potentially ambiguous terms defined? |
| Positive framing | Are constraints stated positively (what to do vs. what not to do)? |

### Advanced Technique Criteria

| Criterion | Evaluation Question |
|-----------|-------------------|
| Technique appropriateness | Is the advanced technique (CoT, chaining, etc.) warranted by task complexity? |
| Implementation correctness | Is the technique implemented according to best practices? |
| Output enforcement | Is the structured output method appropriate for reliability requirements? |

---

## Implementation Checklist

### Phase 1: Task Analysis
- [ ] Define core task in one sentence
- [ ] Classify complexity (simple/moderate/complex)
- [ ] Identify task category (reasoning/extraction/code/creative/Q&A)
- [ ] Determine if structured output is required
- [ ] Assess if examples would clarify patterns

### Phase 2: Component Selection
- [ ] Write specific, quantified task directive
- [ ] Compile relevant context (only directly relevant information)
- [ ] Define constraints as bulleted list with strong language
- [ ] Specify output format if structured output needed
- [ ] Select 2-5 high-quality examples if using few-shot
- [ ] Consider persona only if domain expertise critical (and plan to test)

### Phase 3: Structure & Format
- [ ] Select format based on task category and target model
- [ ] Order components: examples → context → role → task → format
- [ ] Apply appropriate length for complexity level
- [ ] Add section headers and delimiters
- [ ] Use XML tags for complex prompts requiring semantic clarity

### Phase 4: Style Refinement
- [ ] Replace vague verbs with specific alternatives
- [ ] Quantify all measurable requirements
- [ ] Define ambiguous terms
- [ ] Eliminate redundancy
- [ ] Frame constraints positively

### Phase 5: Validation
- [ ] Test on 3-5 representative inputs
- [ ] Evaluate against success criteria
- [ ] Identify and address failure modes
- [ ] Consider compression if prompt is >300 words
- [ ] A/B test critical variations

### Phase 6: Production Considerations
- [ ] Implement output validation
- [ ] Add fallback handling for malformed responses
- [ ] Consider constrained decoding for zero-tolerance scenarios
- [ ] Monitor performance metrics
- [ ] Plan for periodic re-evaluation as models update

---

## Appendix: Key Research Insights

### On Format Selection
- Format choice can impact performance by up to 40%, but no single format is universally superior
- XML provides 23% higher accuracy than JSON on mathematical reasoning benchmarks
- Structured formats improve performance by clarifying syntax and structure, not semantic content
- Claude has documented preference for XML; GPT-4 shows Markdown preference for reasoning

### On Prompt Structure
- Component ordering aligns with LLM token prediction mechanisms—sequence matters
- Examples first establishes patterns before task-specific details
- Instructions positioned late prevent the model from "continuing" rather than executing

### On Length
- Longer prompts with background knowledge improve domain-specific F1-scores by 5-10%
- Short prompts (<50% baseline) universally degrade performance
- Performance degrades non-uniformly as input approaches context limits
- Optimal: sufficient context while staying well below 80% context utilization

### On Personas
- Simple persona prompting shows negligible effect sizes
- Prompt-question semantic similarity predicts gains better than persona characteristics
- Detailed, domain-matched, LLM-generated personas outperform simple human-written ones

### On Few-Shot Learning
- 2-5 examples optimal; diminishing returns beyond this
- Example quality and ordering matter more than quantity
- Place strongest example last in sequence

### On Structured Output
- JSON mode: 82.3% valid output rate, high field name sensitivity
- Tool calling: more robust to schema variations
- Constrained decoding: 98.7% valid output rate, guaranteed compliance
