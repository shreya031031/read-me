place the entire content of .github/copilot-instructions.md with new instructions that define a conditional refinement workflow.

This file must instruct Copilot about:

1. Project context:

This is a Spring Boot Java repository (api-layer) — backend API layer
Standard layered architecture: Controller → Service → Repository → Entity
When working with Jira tickets, use the Atlassian MCP tools to fetch details
2. Backlog Refinement Workflow (two-phase, conditional):

Phase A — Analysis (/refine-ticket):

Step 1: Fetch ticket details (title, description, AC, components, labels, type, priority, parent epic, comments, attachments)
Step 2: Run the Completeness Gate. Check if the following BLOCKING items are present (look in BOTH the dedicated Jira fields AND the description text — information may exist in either):
Acceptance Criteria clearly defined
Scope clearly defined
Parent epic / parent component linked
Required artifacts present (use keyword heuristics):
If ticket mentions "UI", "screen", "page", "design", "frontend" → Figma link expected
If ticket mentions "integrate", "external API", "third-party", "webhook" → API spec link expected
Step 3: Branch based on completeness:
BLOCKED PATH (any blocking item missing): Output a "Cannot Proceed" response with:
Clear list of what's missing
1-2 sentence understanding inferred from the description
Best-Guess Implementation Path clearly labeled as speculative, with strong disclaimers
List of required items before re-running
STOP — do NOT proceed to code analysis or open questions
ANALYSIS PATH (all blocking items present):
Search workspace for relevant Java classes, methods, endpoints based on ticket keywords
Produce: Ticket Summary, Current System Understanding, Implementation Plan, Open Questions (categorized), Structured Answer Template
STOP — wait for user to answer questions in chat
Phase B — Technical Notes (/generate-notes):

Use the existing chat context first (don't re-fetch Jira details unless absolutely necessary)
If critical context is missing from the chat, re-fetch only what's needed
Generate concise technical notes as bullet points:
One point per file
State what changes in that file
Include small code snippets (2-3 lines) ONLY for: new endpoints, key business logic, non-obvious changes
Skip snippets for: obvious changes, configuration tweaks, simple field additions
Notes must be concise but complete — no missing changes
3. Open Question Categorization:

Critical (blocking): Without an answer, developer cannot start OR risks building wrong thing
Important: Developer could start but would make assumptions needing rework
Optional: Quality improvements (observability, polish)
4. Core Rules (strict):

NEVER invent classes, methods, files, or endpoints that don't exist in the workspace
If unsure, say "❓ no specific match found"
Mark assumptions explicitly with "⚠️ assumption"
Output must be in clean Markdown, suitable for Jira comments
In BLOCKED path, the best-guess recommendation MUST carry strong disclaimers ("speculative", "pending confirmation", "best-guess only")
Write the file in clear, instructive second-person ("you should..."). Keep total length under 100 lines. Use clear section headers.
