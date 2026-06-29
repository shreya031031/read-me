
I need you to do a careful analysis and propose a cleanup plan for my Backlog Refinement Agent setup. Do not make any changes yet. Your job in this turn is to read the files, analyze them, surface issues, and propose a plan. I will approve the plan before you execute.

Files to Analyze
Read these three files in full:

.github/agents/refinement.agent.md
.github/prompts/refine-ticket.prompt.md
.github/prompts/generate-notes.prompt.md
Also note: .github/copilot-instructions.md and .github/config/repos.yaml exist but are not in scope for this cleanup.

Workflow That Must Be Preserved (Source of Truth)
The following workflow logic is the canonical source of truth. As you analyze the three files, compare them against this and flag any drift:

Refine a ticket flow:

Fetch ticket details using Atlassian MCP (title, description, AC, components, labels, parent epic, linked PRs, attachments, comments, type, priority).

Detect target repo by reading .github/config/repos.yaml and matching signals in priority order:

Linked PRs in ticket (HIGH confidence)
Components field (HIGH confidence)
Labels (MEDIUM confidence)
Keywords in title/description (MEDIUM confidence)
Parent epic component (LOW-MEDIUM confidence)
Confirm repo with user:

HIGH confidence: state the detected repo, proceed (allow user override)
MEDIUM confidence: propose 2-3 candidates, ask user, wait for response
LOW confidence: list all repos, ask user, wait for response
Decide analysis mode:

If user's current workspace matches the target repo's GitHub identifier → use workspace/codebase search
Otherwise → use GitHub MCP for code search and file reads
State clearly which mode you are using
Completeness Gate — check if these BLOCKING items are present (look in both Jira fields AND description text):

Acceptance Criteria clearly defined (not vague)
Scope clearly defined
Parent epic / parent component linked
Required artifacts (heuristic-based):
UI keywords ("UI", "screen", "page", "design", "frontend") → Figma link expected
Integration keywords ("integrate", "external API", "third-party", "webhook") → API spec link expected
Branch:

BLOCKED (any blocking item missing): output "Cannot Proceed" with clear list of missing items, 1-2 sentence understanding from description, best-guess implementation path with strong speculative disclaimers, list of required items before re-running. STOP.
PROCEED (all blocking items present): perform code analysis in the target repo, identify relevant classes/methods/endpoints, verify file paths exist, produce structured analysis output with: ticket summary, current system understanding, implementation plan, categorized open questions (Critical / Important / Optional), and an answer template the user can fill in.
Wait for user to provide answers in the same chat.

Generate notes flow:

Use existing chat context first. Do NOT re-fetch Jira details unless something specific is missing AND needed. State clearly when you're using chat context vs re-fetching.

Validate that all Critical questions are answered. Important and Optional do NOT block. If Critical answers are missing, list which ones and STOP.

Use the same analysis mode (workspace or GitHub MCP) determined earlier.

Generate concise technical notes:

One file = one section
State what changes and why
Include code snippets ONLY for: new endpoint signatures, new bean/config declarations, or non-obvious business logic (max 2-3 lines)
Skip snippets for: obvious changes, simple field additions, configuration tweaks
Verify all file paths exist before referencing
Tone & Language Constraints (Important)
The current agent file has clunky phrasing that needs to be cleaned up. Specifically:

Remove all mentions of "two phases" — the workflow is one continuous conversation, not formally phased
Remove references like "Phase A" / "Phase B" — flatten the structure
Remove or minimize explicit slash command framing like "When user runs /refine-ticket..." inside the agent's own narrative — the agent already knows what command was invoked; it doesn't need to keep referencing the command name in its own internal logic
Make the flow feel like a single, smooth conversation: fetch → detect repo → confirm → check completeness → either block or analyze → wait for answers → generate notes when ready
Slash commands should be invocation entry points only, not narrative scaffolding throughout the file
The agent should read more like "here is how I behave when invoked" rather than "here are two separate phases I run".

Templates Should Move to Separate Files
The current agent file likely contains large Markdown output templates inline (BLOCKED template, ANALYSIS template, NOTES template). Move these to separate files:



.github/agents/lib/templates/blocked.md
.github/agents/lib/templates/analysis.md
.github/agents/lib/templates/notes.md
The agent file should reference these templates by path rather than embed them inline. When the agent needs to produce output, it should read the relevant template file and fill it in.

What I Want You To Do Right Now
Do these steps in order:

Step 1 — Read all three files in full. Do not summarize prematurely. Actually load the content.

Step 2 — Compare logic across the three files. Identify:

Where the three files agree
Where any two files contradict each other
Where one file has logic the others are missing
Where the agent file uses awkward phrasing ("two phases", explicit /refine-ticket framing inside its own logic, etc.)
Where output templates exist inline that should be moved out
Step 3 — Verify against the canonical workflow above. For each step of the canonical workflow, confirm:

Is it present in refinement.agent.md?
Is it correctly described?
Does it match the canonical version?
Step 4 — Surface every discrepancy as a question to me. For each conflict or ambiguity, ask explicitly: "File X says this; file Y says that; the canonical version says the third thing. Which should win?"

Do not silently resolve discrepancies. I want to see every conflict.

Step 5 — Propose a written plan with these sections:

a. Discrepancies Found — bulleted list of every disagreement between files, with which version you recommend keeping and why

b. Phrasing Issues to Clean Up — specific examples of awkward "two phases" / slash command references you found, with proposed cleaner wording

c. Files to Create — list of new template files under .github/agents/lib/templates/ with what each will contain

d. Files to Modify — what changes to refinement.agent.md (slim down, reference templates, remove phasing language)

e. Files to Delete — .github/prompts/refine-ticket.prompt.md and .github/prompts/generate-notes.prompt.md, only after agent is verified to contain all logic

f. Execution Order — the exact sequence of changes you'll make if I approve, with each step labeled. Include a verification checkpoint before deletion of prompt files.

g. Risks — anything that could go wrong (e.g., agent fails to load template file at runtime)

Step 6 — STOP and wait for my approval. Do NOT make any file changes until I explicitly say "approved" or give specific feedback.

Output Format
Use clear Markdown with headers. Use tables where comparing things across files. Keep the plan tight and scannable — I should be able to approve or reject it in one read.
