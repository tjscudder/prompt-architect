================================================================================
                           PROMPT ARCHITECT SKILL
                              README & Guide
================================================================================

1. OVERVIEW
-----------
Prompt Architect is an interactive 14-step wizard that guides you through 
creating optimized, production-ready prompts for large language models (LLMs).

Instead of writing prompts from scratch, this skill interviews you about your 
goals, constraints, and requirements, then assembles a well-structured prompt 
tailored to your target LLM using research-validated techniques.

TABLE OF CONTENTS
-----------------
  1.  Overview
  2.  What It Does
  3.  What It Outputs
  4.  Installation: Claude.AI
  5.  Triggering the Skill
  6.  How to Use the Skill
  7.  Using with Non-Claude LLMs
  8.  Troubleshooting
  9.  File Structure
  10. Research Basis
  11. Version History
  12. Contact & Support

================================================================================

2. WHAT IT DOES
---------------
The skill walks you through these steps:

  1. Use-Case & Goal Overview        - What you're trying to accomplish
  2. Task Definition                 - Primary and secondary tasks
  3. Task Classification             - Categorize task type and complexity
  4. Inputs & Context                - What data the model receives
  5. Constraints & Guardrails        - Rules, limits, and boundaries (max 7-10)
  6. Output Format & Structure       - How the answer should be shaped
  7. Target LLM Identification       - Which model will run your prompt
  8. Format Assessment               - Recommends optimal prompt structure
  9. Examples (Few-Shot)             - Show, don't tell demonstrations (2-5)
  10. Role/Persona (Optional)        - Who/what the model should act as
  11. Style & Tone (Optional)        - Voice and reading level
  12. Quality Checks                 - Self-verification instructions
  13. First Draft Assembly           - Combines everything in optimal order
  14. Self-Review & Improvement      - Refines and delivers final prompt

At each step, the skill asks questions, rewrites your answers into optimized 
prompt text, explains its reasoning, and asks for your approval before moving on.

EXPEDITE MODE
~~~~~~~~~~~~~
The skill supports three speed modes to match your preferred pace:

  • Expedite: full      - AI drafts all responses; you review and approve
  • Expedite: assisted  - AI offers suggestions; you decide what to use (default)
  • Expedite: off       - You answer all questions directly

Commands available at any step:
  • "Suggest all"       - Draft responses for all questions
  • "Suggest 1, 3"      - Draft responses for specific questions
  • "Approve"           - Accept all suggestions
  • "1. [response]"     - Provide/edit response for question 1

3. WHAT IT OUTPUTS
------------------
Based on your task type and target LLM, the skill recommends and outputs your 
final prompt in one of these formats:

  • XML          - Best for complex reasoning tasks (especially with Claude)
  • JSON         - Best for data extraction and structured output
  • YAML         - Token-efficient alternative to JSON
  • Markdown     - Best for creative writing and content generation
  • Plain Text   - Best for general Q&A and simple tasks

The final deliverable includes:
  - Your complete prompt in the chosen format
  - Target LLM and format metadata
  - Usage notes specific to your chosen model

================================================================================

4. INSTALLATION: CLAUDE.AI
--------------------------

OPTION 1: Global Installation (All Chats)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
To make the skill available across ALL your Claude conversations:

1. Open Claude.ai and click on your profile icon (bottom-left)

2. Select "Settings"

3. Navigate to the "Capabilities" section

4. Under Skills or Custom Instructions, click "Add" or "Upload"

5. Upload the prompt-architect.skill file
   (Claude will extract and read the SKILL.md inside)

6. Save your settings

7. The skill is now available in ALL your conversations, not just projects


OPTION 2: Project-Specific Installation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
To use this skill only within a specific Project:

1. Open Claude.ai and navigate to your Project (or create a new one)

2. In the Project Knowledge section, click "Add content"

3. Select "Upload files" and choose the prompt-architect.skill file
   (Claude will extract and read the SKILL.md inside)

4. The skill is now available in that Project's conversations only


OPTION 3: Manual Installation (Copy/Paste)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
If file upload isn't working, you can add the skill manually:

1. Unzip the .skill file (it's a ZIP archive—rename to .zip if needed)

2. Open the extracted prompt-architect/SKILL.md file

3. Copy the entire contents

4. Paste into either:
   - Settings > Capabilities (for global access), or
   - Project Knowledge (for project-specific access)

5. TRIGGERING THE SKILL
-----------------------
Start a new conversation in your Project and use one of these triggers:

  • "@createprompt"
  • "Help me create a prompt"
  • "Build a prompt for [your use case]"
  • "Design a system prompt"
  • "Improve this prompt"

Claude will recognize the skill and begin the 14-step wizard.

================================================================================

6. HOW TO USE THE SKILL
-----------------------

STARTING A SESSION
  Simply describe what you need, for example:
  
    "I need to create a prompt for extracting customer information from 
    support emails"
  
  Or just say "@createprompt" and the wizard will ask what you're building.

DURING THE INTERVIEW
  At each step, the skill will:
  
  1. Ask you focused questions
  2. Rewrite your answers into prompt text
  3. Explain why it made certain choices
  4. Show you the result and ask for approval
  
  Respond with:
  • "APPROVE" (or just "yes", "looks good", etc.) to accept and continue
  • "EDIT: [your changes]" to modify before proceeding

FORMAT SELECTION (Step 8)
  When asked about format, you can:
  
  1. Accept the PRIMARY recommendation
  2. Choose the ALTERNATIVE option
  3. Specify a CUSTOM format (XML, JSON, YAML, Markdown, Plain Text, or other)
  
  The skill explains why each format works best for your use case.

REVIEWING THE DRAFT
  In Steps 13-14, review the assembled prompt. You can:
  
  • Request changes to specific sections
  • Ask to tighten or expand certain areas
  • Adjust the structure or ordering
  
  Only approve when you're satisfied with the result.

TIPS FOR BEST RESULTS
  • Be specific about your use case and constraints
  • Provide example inputs/outputs if you have them
  • Mention if you have strict requirements (parsing, compliance, etc.)
  • Don't skip the format recommendation step—it matters for performance

================================================================================

7. USING WITH NON-CLAUDE LLMs
-----------------------------

The Prompt Architect skill itself runs inside Claude, but the prompts it 
creates are designed for ANY modern LLM. Here's how to use your generated 
prompts with other models:

STEP 1: CREATE YOUR PROMPT IN CLAUDE
  Run through the full 14-step wizard in Claude. When asked about your target 
  LLM (Step 7), select the model you'll actually use:
  
    1. Claude (Anthropic)
    2. GPT-4 / GPT-4o (OpenAI)
    3. GPT-3.5-turbo (OpenAI)
    4. Gemini (Google)
    5. DeepSeek (V3 or R1)
    6. Llama (Meta)
    7. O1 / Reasoning Models (OpenAI)
    8. Other
    9. Framework Agnostic (multi-model use)

  The skill will tailor format recommendations to your chosen model.

STEP 2: COPY YOUR FINAL PROMPT
  After approval, copy the complete prompt from Claude's output.

STEP 3: USE IN YOUR TARGET LLM
  Paste the prompt into your target platform:
  
  • OpenAI (ChatGPT/API): Paste as system prompt or user message
  • Google Gemini: Paste in the input field or API call
  • DeepSeek: Use in user prompt (R1 models prefer all context there)
  • Llama/Local models: Use as system prompt in your inference setup
  • API integrations: Include in your API request body

MODEL-SPECIFIC NOTES
--------------------

GPT-4 / GPT-4o
  • Robust across all formats
  • Markdown works excellently for reasoning tasks
  • JSON mode available for strict structured output

GPT-3.5-turbo
  • Most format-sensitive model (up to 40% performance variation)
  • Strongly prefer JSON for structured tasks
  • Explicit instructions matter more than with newer models

O1 / Reasoning Models
  • Do NOT include "think step by step" instructions
  • These models reason internally by default
  • Focus on clear problem specification, not reasoning guidance

DeepSeek
  • V3: Include "JSON" explicitly in prompt for JSON output
  • R1: Put ALL context in user prompt (not system prompt)
  • XML works well (trained on Claude-style outputs)

Gemini
  • Works well with Markdown and structured formats
  • Good at following explicit format instructions

Llama / Open-Source Models
  • More sensitive to format variations
  • Test your prompt before production use
  • Explicit, detailed instructions help more than format choice

Framework Agnostic
  If you'll use the prompt across multiple models, the skill recommends 
  universally robust formats:
  • Markdown for creative/content tasks
  • JSON for extraction/structured output
  • Plain text with explicit instructions for Q&A

================================================================================

8. TROUBLESHOOTING
------------------

"The skill isn't activating"
  Make sure the skill file is properly added to your Project Knowledge. Try 
  explicitly saying "@createprompt" or "use the prompt architect skill".

"I want to restart from a specific step"
  Say "Let's go back to Step [number]" or "I want to redo the [section name]".

"The format recommendation doesn't fit my needs"
  Select option 3 (CUSTOM) during format selection and explain your 
  requirements. The skill will adapt.

"I need to edit the final prompt"
  You can ask Claude to modify specific sections even after the final output. 
  Just say "Edit the constraints section to add..." or similar.

================================================================================

9. FILE STRUCTURE
-----------------

prompt-architect.skill    <- ZIP archive containing the skill
  └── prompt-architect/
      └── SKILL.md        <- The actual skill definition

To inspect or modify the skill:
  1. Rename .skill to .zip (or just unzip directly)
  2. Edit SKILL.md as needed
  3. Re-zip the folder and rename back to .skill

================================================================================

10. RESEARCH BASIS
------------------

This skill is built on a comprehensive synthesis of 150+ research sources on 
optimal prompt engineering. The research-backed approach delivers measurable 
improvements over intuition-based prompting.

KEY RESEARCH FINDINGS INCORPORATED
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Format Selection Impact:
  • Format choice can impact performance by up to 40% on specific tasks
  • XML achieves 23% higher accuracy than JSON for reasoning tasks
  • Markdown outperforms structured formats by 18% for creative tasks
  • YAML provides 48% token savings vs JSON with comparable accuracy

Component Ordering:
  • Research validates specific ordering: examples → context → role → task → 
    constraints → style → output format → quality checks
  • This ordering aligns with LLM token prediction mechanisms
  • Placing instructions last prevents models from "continuing" the prompt

Optimal Prompt Length:
  • Background knowledge inclusion improves F1-scores by 5-10%
  • Short prompts (<50% baseline) universally degrade performance
  • Staying below 80% context window capacity prevents degradation

Constraints & Examples:
  • Compliance degrades beyond 7-10 constraints (skill enforces this limit)
  • 2-5 examples optimal; quality and ordering matter more than quantity
  • Strongest example should be placed last in sequence

Personas & Roles:
  • Simple personas ("You are an expert") show negligible benefit
  • Detailed, domain-specific personas work better when needed
  • Role step is now optional based on this research

BENEFITS OF RESEARCH-BASED PROMPTING
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

  • Eliminates guesswork with evidence-based format selection
  • Optimizes for your specific task type and target LLM
  • Applies proven component ordering for maximum effectiveness
  • Enforces research-validated constraints and structure limits
  • Reduces trial-and-error iteration time significantly

For the complete research synthesis, see: prompt-engineering-research-synthesis.md

================================================================================

11. VERSION HISTORY
-------------------
v2.1 (January 2026)
  - Expanded from 13 to 14 steps
  - Added Task Classification & Complexity Assessment step (Step 3)
  - Added Expedite Mode for faster iteration (full/assisted/off)
  - Reordered steps: cognitive flow for gathering info, optimal flow for output
  - Made Role (Step 10) and Style (Step 11) optional based on research
  - Added research-validated constraint limits (7-10 max)
  - Added context window best practices (stay below 80% capacity)
  - Enhanced output ordering based on research (examples first, format last)
  - Added RESEARCH BASIS section documenting evidence foundation

v2.0 (January 2026)
  - Added Target LLM identification step
  - Added Format Assessment & Recommendation step
  - Prompts now output in recommended format (XML, JSON, YAML, Markdown, etc.)
  - Added LLM-specific guidance and notes
  - Expanded from 11 to 13 steps

v1.0 (Original)
  - 11-step prompt design wizard
  - JSON-only output format

================================================================================

12. CONTACT & SUPPORT
---------------------
For questions, improvements, or bug reports, refer to your organization's 
documentation or the original skill author.

================================================================================
