I need a clear plan and any clarifying questions you have. Do not make any file changes. Only respond with a plan and questions.

Goal
Remove the two prompt files and have the Refinement Agent own the entire workflow on its own. Optionally extract long output templates into separate files if it improves maintainability.

Files Involved
.github/agents/refinement.agent.md — keep, will own all workflow logic
.github/prompts/refine-ticket.prompt.md — to be deleted
.github/prompts/generate-notes.prompt.md — to be deleted
Workflow That Must Be Preserved
The agent must continue to handle this end-to-end in a single smooth conversation:

Fetch Jira ticket via Atlassian MCP
Detect target repo using .github/config/repos.yaml and ticket signals (linked PRs, components, labels, keywords, parent epic)
Confirm repo with user when confidence is below HIGH
Decide analysis mode: workspace (if open) or GitHub MCP (otherwise)
Completeness Gate: check AC, scope, parent epic, required artifacts (Figma for UI, API spec for integrations)
If anything blocking is missing → output "Cannot Proceed" with best-guess (clearly speculative) and stop
If complete → analyze code in target repo, produce structured analysis with categorized open questions and an answer template
Wait for user answers in chat
When user signals readiness (or invokes notes generation), validate Critical questions are answered, then generate concise technical notes
The agent should handle this as one continuous conversation flow, not formal phases.

Key Concern To Address In Your Plan
Right now, the agent file likely contains references to slash commands like /refine-ticket and /generate-notes in its internal narrative. These slash commands historically came from the prompt files.

After we delete the prompt files, I need clarity on:

Will the slash commands /refine-ticket and /generate-notes still work? Or will they break because they were defined by the prompt files?

Should the agent stop referencing slash commands in its internal logic and instead just behave correctly based on the conversation flow (i.e., when invoked, it starts the refinement; when answers are provided, it knows to move toward notes)?

How will the user trigger the notes step if there's no /generate-notes slash command? Options:

User just says "generate the notes" in natural language
User says "ready for notes" or similar
Agent automatically transitions when it detects Critical answers are filled in
A new slash command defined elsewhere
I want your recommendation here.

What I Want From You
Two things only:

1. A Proposed Plan
Cover:

What changes to refinement.agent.md (slim it, remove slash command framing, add clear invocation/trigger behavior)
Whether to extract output templates to .github/agents/lib/templates/ (your recommendation: yes or no, with reasoning)
Order of operations (e.g., update agent → test → delete prompt files)
How the user will interact with the agent after slash commands are gone (the trigger mechanism for notes generation)
2. Clarifying Questions
List every question you have for me before you can execute confidently. Examples might include:

Should /refine-ticket still work as the invocation entry point, or only @refinement mention?
When the user says something like "looks good, generate notes" — how strictly should the agent require explicit phrasing?
Should the agent auto-detect that answers are filled and offer to generate notes, or wait for explicit user request?
Should templates be in separate files even if the agent file is only ~250 lines?
Surface every uncertainty as a question. I will answer them, then you can produce a final plan I approve.

Output Format
Section 1: Proposed Plan (concise, scannable)
Section 2: Clarifying Questions (numbered list)
Do not start making changes. Do not produce code. Just plan + questions.
