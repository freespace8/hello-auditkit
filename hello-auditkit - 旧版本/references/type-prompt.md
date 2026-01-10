# Prompt Audit Rules

> **Inherits**: All rules from `rules-universal.md`
> **Execution Required**: Execute each check table below. Output evidence for each category.
> **Source**: Based on Latest GPT Prompting Guide (openai-cookbook/examples/gpt-5)

## Table of Contents

- [Overview](#overview)
- [Structure Validation](#structure-validation)
- [Content Quality](#content-quality)
- [LLM Prompting Best Practices](#llm-prompting-best-practices)
- [Prompt Compliance Checks](#prompt-compliance-checks)
- [LLM-Specific Warnings](#llm-specific-warnings)
- [Common Issues](#common-issues)

---

## Overview

**Applies to**: Standalone LLM prompts, system prompts, instruction text

**Key focus areas**:
- Verbosity constraints (explicit length limits)
- Scope boundaries (prevent feature creep)
- Output format specification (structured extraction)
- Ambiguity handling (clarification strategy)
- High-risk self-check (verification steps)

> **Dynamic Verification**: Before auditing, fetch latest GPT prompting guides per Step 0 in SKILL.md. Cross-validate built-in checks below against the latest guide and apply any new practices.

---

## Structure Validation

> **Pre-Check Required**: Before flagging any "missing" element, first verify if the intent is already conveyed through examples, context, or patterns that AI can infer from. Only flag if AI cannot reasonably infer the expected behavior.

| Check | Requirement | Severity |
|-------|-------------|----------|
| Verbosity constraints | Output length guidance present (explicit limits OR demonstrative examples OR context AI can infer from) | Severe only if AI cannot infer |
| Scope boundaries | Boundaries conveyed (explicit "do not" list OR clear context/examples showing scope) | Severe only if AI cannot infer |
| Ambiguity handling | Instructions for unclear cases | Warning |
| Output format | Specified structure/format with schema | Warning |
| Grounding | "Based on context" hedging for uncertain claims | Warning |
| Self-check steps | Verification for high-risk outputs | Info |

---

## Content Quality

### Good Prompt Patterns

```markdown
# Explicit verbosity constraints
Default: 3-6 sentences or ≤5 bullets.
Simple questions: ≤2 sentences.
Complex tasks: 1 overview + ≤5 tagged bullets (What, Where, Risk, Next, Open).

# Scope discipline
Implement EXACTLY and ONLY what requested.
Do NOT add: extra features, uncontrolled styling, new UI elements.
Forbidden: inventing elements not in requirements.

# Ambiguity handling
If unclear: provide 1-3 precise clarifying questions OR 2-3 interpretations with assumption labels.

# Output format with schema
Return JSON: { "status": "success|error", "result": {...}, "errors": [] }
Required fields: status, result. Optional: errors.

# Grounding
Based on the provided context, [conclusion].
Never fabricate specific numbers, line numbers, or external references.

# High-risk self-check
Before outputting legal/financial/security content:
- Re-scan for unstated assumptions
- Verify no ungrounded specifics
- Check for overstatements
```

### Bad Prompt Patterns

> **Fix Approach**: Before suggesting "Add X", first check if existing examples/context already convey the intent. Prefer MODIFY over ADD.

| Pattern | Problem | Fix Approach |
|---------|---------|--------------|
| "Do it well" | Vague, non-actionable | Clarify criteria (modify existing text) |
| No length guidance AND no examples | Unbounded verbosity | First check if examples imply length; if not, add minimal guidance |
| No scope indication AND no context | Feature creep risk | First check if context implies scope; if not, clarify boundaries |
| Absolute claims | No grounding | Add hedging language |
| "Be helpful" | Too generic | Specify concrete behaviors |
| "Handle errors" | Undefined | Specify error types |

---

## LLM Prompting Best Practices

> **Source**: Latest GPT Prompting Guide from [openai-cookbook/examples/gpt-5](https://github.com/openai/openai-cookbook/tree/main/examples/gpt-5)

### Core Principles

1. **Explicit over implicit**: Articulate preferences clearly, don't assume AI knows your style
2. **Constraint-driven**: Use explicit scope, verbosity, format constraints
3. **Verification-oriented**: Add self-check steps for high-risk outputs
4. **Minimal interpretation**: "If ambiguous, choose simplest valid interpretation"
5. **Scope discipline**: "Implement EXACTLY and ONLY what requested"

### Verbosity Control (Critical)

| Context | Constraint | Example |
|---------|------------|---------|
| Simple yes/no | ≤2 sentences | "Answer in ≤2 sentences" |
| Default responses | 3-6 sentences or ≤5 bullets | "Respond in 3-6 sentences" |
| Complex tasks | 1 overview + ≤5 tagged bullets | "Overview + bullets: What, Where, Risk, Next, Open" |
| Long-form content | Explicit word/section limits | "≤500 words, 3 sections max" |
| Enterprise/coding agents | Strict limits | "Default ≤5 bullets, simple ≤2 sentences" |

### Scope Discipline (Critical)

**Prevent feature creep with explicit constraints:**

```markdown
# REQUIRED scope constraints
- Implement EXACTLY and ONLY what requested
- Do NOT add: extra features, uncontrolled styling, new UI elements
- Do NOT invent elements not in requirements
- Do NOT expand problem surface area
- Respect design system constraints
```

| Check | Requirement | Severity |
|-------|-------------|----------|
| Explicit "do not" list | Forbidden actions listed | Severe |
| Scope boundaries | What IS and IS NOT in scope | Severe |
| Feature creep prevention | "Only what requested" | Warning |
| Design system respect | No uncontrolled additions | Warning |

### Long-Context Handling (>10k tokens)

| Step | Action | Purpose |
|------|--------|---------|
| 1 | Produce internal outline of key sections | Navigate large input |
| 2 | Re-state user constraints before answering | Prevent drift |
| 3 | Anchor claims to specific sections with quotes | Grounding |
| 4 | Break into phases if necessary | Manage complexity |

### Ambiguity & Hallucination Prevention

| Strategy | Implementation | Severity |
|----------|----------------|----------|
| Clarification | Ask 1-3 precise questions OR present 2-3 interpretations with assumption labels | Warning |
| Hedging | "Based on the provided context..." | Warning |
| No fabrication | Never fabricate exact figures, line numbers, external references | Severe |
| Missing data | Set missing fields to null rather than guessing | Warning |
| Grounding | Reference specific sections/quotes | Warning |
| Uncertainty | Use general terms when specifics may have changed | Info |

### High-Risk Self-Check

**For legal, financial, compliance, or security-sensitive content:**

```markdown
Before outputting, re-scan answer for:
- Unstated assumptions
- Ungrounded specific numbers
- Overstatements or absolute claims
- Missing caveats or disclaimers
```

| Check | Requirement | Severity |
|-------|-------------|----------|
| Self-check instruction | Present for high-risk domains | Warning |
| Assumption disclosure | Unstated assumptions flagged | Warning |
| Grounded specifics | No fabricated numbers | Severe |
| Appropriate hedging | No overstatements | Warning |

### Tool Usage Guidelines

| Guideline | Severity |
|-----------|----------|
| Prefer tools over internal knowledge for fresh/user-specific data | Warning |
| Parallelize independent read operations | Info |
| After writes, restate: what changed, where, validation performed | Warning |
| Explicit tool selection criteria | Info |

### Agentic Updates

| Rule | Requirement | Severity |
|------|-------------|----------|
| Brief updates | 1-2 sentences only | Warning |
| Major phases only | Not routine tool calls | Warning |
| Concrete outcomes | Each update has specific result | Warning |
| No narration | Avoid "thinking out loud" | Info |

### Structured Extraction

| Check | Requirement | Severity |
|-------|-------------|----------|
| Schema provided | Always provide JSON shape/structure | Severe |
| Field distinction | Required vs optional marked | Warning |
| Null handling | Missing → null, not guessed | Warning |
| Array bounds | Min/max items specified | Info |
| Re-scan before return | Check for missed fields | Info |
| Multi-doc extraction | Serialize by document with stable IDs | Info |

### Freedom Level Matching

| Task Type | Freedom | Constraint Style | Issue if Mismatch |
|-----------|---------|------------------|-------------------|
| Creative/flexible | High | Guidelines only | Warning if over-constrained |
| Code generation | Medium | Patterns + flexibility | Warning if too rigid |
| Data extraction | Low | Strict schema | Severe if unstructured |
| Safety-critical | Very Low | Explicit scripts | Severe if too loose |

---

## Prompt Compliance Checks

> **Execution Required**: For prompts targeting modern LLMs (GPT-5+, Claude, Gemini, etc.), verify these checks.

### Verbosity Compliance

| Check | Requirement | Severity |
|-------|-------------|----------|
| Has explicit length limit | "≤N sentences/bullets/words" | Severe |
| Default verbosity defined | What to do without specific request | Warning |
| Complex task structure | Tagged bullets for multi-part | Info |

### Scope Compliance

| Check | Requirement | Severity |
|-------|-------------|----------|
| Has "do not" constraints | Explicit forbidden actions | Severe |
| Scope boundaries clear | What IS and IS NOT in scope | Severe |
| Feature creep prevention | "Only what requested" or equivalent | Warning |

### Grounding Compliance

| Check | Requirement | Severity |
|-------|-------------|----------|
| No absolute claims | Uses hedging language | Warning |
| No fabrication instruction | "Never fabricate..." | Severe |
| Context anchoring | "Based on provided context" | Warning |

### Self-Check Compliance (High-Risk Only)

| Check | Requirement | Severity |
|-------|-------------|----------|
| Has verification step | Re-scan instruction | Warning |
| Assumption check | Flag unstated assumptions | Info |
| Overstatement check | Avoid absolute claims | Info |

### Output Format Compliance

| Check | Requirement | Severity |
|-------|-------------|----------|
| Schema provided | JSON structure or format spec | Warning |
| Required/optional marked | Field requirements clear | Info |
| Null handling defined | What to do with missing data | Info |

---

## LLM-Specific Warnings

> **Source**: Latest troubleshooting and optimization guides from [openai-cookbook/examples/gpt-5](https://github.com/openai/openai-cookbook/tree/main/examples/gpt-5)

### Official Warnings (From Troubleshooting Guide)

| Issue | Official Warning | Severity |
|-------|-----------------|----------|
| Filler phrases | Modern LLMs treat "Take a deep breath", "You are a world-class expert" as noise | Warning |
| Contradictory instructions | More damaging to reasoning models (wastes reasoning tokens reconciling) | Severe |
| No clear completion definition | Causes overthinking - model keeps exploring without stop condition | Warning |
| Maximize language | "Analyze everything", "Be thorough" causes over-tool-calling | Warning |
| Overly deferential | In agentic settings, may need persistence instructions | Warning |

### API Parameters Reference

> **Note**: When prompts include parameter recommendations, verify against official values from the latest documentation.

| Parameter | Valid Values | Purpose |
|-----------|-------------|---------|
| reasoning_effort | none \| minimal \| low \| medium \| high \| xhigh | Controls thinking depth |
| verbosity | API parameter | Controls final answer length (separate from thinking) |

---

## Common Issues

### Should Flag

> **Pre-Check**: Before flagging, verify that the issue cannot be resolved by AI inference from existing examples/context.

| Issue | Severity | Principle | Pre-Check |
|-------|----------|-----------|-----------|
| Contradictory instructions | Fatal | - | Always flag |
| Impossible constraints | Fatal | - | Always flag |
| Missing critical context | Fatal | - | Always flag |
| No verbosity guidance AND no demonstrative examples | Severe | Verbosity Control | Only if AI cannot infer from examples |
| No scope indication AND no contextual boundaries | Severe | Scope Discipline | Only if AI cannot infer from context |
| No "do not" constraints AND ambiguous scope | Severe | Scope Discipline | Only if AI cannot infer boundaries |
| Ambiguous output format for structured tasks | Severe | Structured Extraction | Check if examples show format |
| No error handling instructions | Severe | - | Check if obvious from context |
| Fabrication allowed (no grounding) | Severe | Hallucination Prevention | Always flag for factual tasks |
| Vague instructions ("do it well") | Warning | Explicit over Implicit | Check if examples clarify |
| Missing examples for complex tasks | Warning | - | Only if truly complex |
| No hedging guidance for uncertain cases | Warning | Grounding | Check context |
| Overly long without structure | Warning | Long-Context Handling | - |
| No self-check for high-risk content | Warning | High-Risk Self-Check | - |
| Absolute claims without hedging | Warning | Grounding | - |

### Should NOT Flag

| Pattern | Reason |
|---------|--------|
| Simple prompts without constraints | Not needed for simple tasks |
| Style preferences | Design choice |
| Reasonable interpretation choices | Valid |
| Missing optional elements | Optional |
| No self-check for low-risk content | Not required |
| **No explicit constraints BUT demonstrative examples exist** | AI can infer from examples |
| **No explicit rules BUT context clearly implies behavior** | AI capability - can infer from context |
| **Original design conveys intent through structure/examples** | Redundant to add explicit rules |

---

## Audit Checklist

### Fatal
- [ ] No contradictory instructions
- [ ] No impossible constraints
- [ ] Critical context present

### Severe (Critical)
- [ ] Verbosity constraints specified (explicit length limits)
- [ ] Scope boundaries defined (what IS and IS NOT in scope)
- [ ] "Do not" constraints present (forbidden actions)
- [ ] Output format clear (schema for structured tasks)
- [ ] No fabrication instruction (grounding)
- [ ] Error handling addressed

### Warnings
- [ ] Instructions are specific, not vague
- [ ] Examples provided for complex tasks
- [ ] Hedging guidance for uncertain cases
- [ ] Well-structured if long (outline for >10k tokens)
- [ ] Self-check for high-risk content
- [ ] No absolute claims without hedging

### Info
- [ ] Could benefit from more examples
- [ ] Could add self-check steps
- [ ] Could specify output more precisely
- [ ] Could add tagged bullets for complex tasks
