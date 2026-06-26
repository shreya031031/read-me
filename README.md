Automates the first pass of backlog refinement inside GitHub Copilot. Given a Jira ticket, the agent fetches details, validates completeness, analyzes the relevant codebase, surfaces categorized open questions, and generates concise technical notes — reducing refinement time and entering sprint planning with clarity.

Build prompt files (copilot-instructions.md, refine-ticket.prompt.md, generate-notes.prompt.md) with two-phase flow: completeness gate → analysis + categorized open questions → technical notes. Integrate Atlassian MCP.

Completed as planned.

Convert to Chat Mode agent. Integrate GitHub MCP. Add repo registry + repo detection from ticket signals. Dual-mode analysis: workspace if open, else GitHub MCP.

🟡 In progress. GitHub MCP integrated; chat mode and repo registry implementation started.
