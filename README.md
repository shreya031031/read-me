Replace the entire content of .github/prompts/refine-ticket.prompt.md with a new conditional workflow prompt.

The file must have this structure:

1. Frontmatter:



---
mode: agent
description: Analyze a Jira ticket — validate completeness, then either block or produce analysis
---
2. Instruct Copilot to execute these steps in order:

Step 1 — Fetch ticket: Use Atlassian MCP tools to fetch the ticket. Extract: title, description, AC field, components, labels, type, priority, parent epic link, attachments, comments.

Step 2 — Completeness Gate: Check each of these. For each, look in BOTH the dedicated field AND the description text:

 Acceptance Criteria present and clear
 Scope clearly defined
 Parent epic / component linked
 Required artifacts (use keyword heuristic):
UI keywords ("UI", "screen", "page", "design", "frontend") → Figma link present
Integration keywords ("integrate", "external API", "third-party", "webhook") → API spec link present
Step 3 — Branch:

IF any blocking item is missing → Use BLOCKED template:

markdown


# ⚠️ Cannot Proceed — Ticket Refinement Required: {TICKET_ID}

## ❌ Blocking Items Missing
- {Specific item missing — e.g., "Acceptance Criteria not defined in AC field or description"}
- {Each missing item as a bullet}

## 📖 What We Understand (from description)
{1-2 sentences summarizing what the ticket appears to want}

## 🔮 Best-Guess Implementation Path *(speculative — pending refinement)*
> ⚠️ **This is a best-guess only.** Without complete ticket details, this recommendation may be inaccurate. Confirm with PM/author before treating as authoritative.

- **Likely scope:** {feature / bug / refactor}
- **Probable areas affected:** {1-2 high-level areas based on keywords}
- **Probable approach:** {1 sentence}

## ✅ Required Before Proceeding
1. {First missing item to add}
2. {Second missing item to add}
3. {Any other missing items}

---
*Re-run `/refine-ticket {TICKET_ID}` after the ticket is updated.*
IF all blocking items present → Use ANALYSIS template:

Continue with codebase analysis:

Identify keywords from ticket
Search workspace for relevant Java classes/methods/endpoints in src/main/java
Detect gaps in non-blocking areas (error handling, performance targets, edge cases, observability)
Output:

markdown


# Refinement Analysis: {TICKET_ID}

## 📋 Ticket Summary
**Title:** {title}
**Type:** {type}
**Priority:** {priority}
**Parent Epic:** {parent}
**Components:** {components}

**What it asks for:** {2-3 line summary}

## 🔍 Current System Understanding
{Existing functionality related to ticket}
{Key code locations — actual file paths verified in workspace}

## 🎯 Implementation Plan
**High-level approach:** {1-2 sentences}

**Likely changes:**
- {Bullet list of changes}

**Likely impacted files:**
- `path/to/File.java` — {what changes here}

## ❓ Open Questions

### 🔴 Critical (must answer before notes can be generated)
1. {Specific question referencing ticket content}
2. {Specific question}

### 🟡 Important (needed for accurate implementation)
1. {Question}

### 🟢 Optional (improves quality)
1. {Question}

## ✍️ Answer Template
Copy the section below, fill in your answers, and paste back in this chat. Then run `/generate-notes` to produce technical notes.
Answers for {TICKET_ID}
Critical
[Question 1] Answer:

[Question 2] Answer:

Important
[Question 1] Answer:
Optional
[Question 1] Answer:



---
*Once Critical questions are answered, run `/generate-notes` to produce technical notes.*
3. End the prompt with: Now process ticket: ${input:ticketId}

4. Strict rules at the top of the prompt:

Never invent files or classes that don't exist in the workspace
In BLOCKED path, do NOT proceed to code analysis or open questions
Use strong disclaimers in best-guess section
Output must follow the templates exactly
