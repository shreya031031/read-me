.github/
├── copilot-instructions.md          ← UPDATED (repo-aware logic)
├── agents/
│   └── refinement.agent.md          ← NEW (the custom agent) ⭐
├── prompts/
│   ├── refine-ticket.prompt.md      ← UPDATED
│   └── generate-notes.prompt.md     ← UPDATED
└── config/
    └── repos.yaml                   ← NEW


- name: api-layer
  github: your-org/api-layer
  description: Backend REST API layer
  stack: spring-boot
  components: ["api-layer", "backend"]
  keywords: ["api", "controller", "service", "endpoint"]

- name: web-api
  github: your-org/web-api
  description: BFF / web API gateway
  stack: node-typescript
  components: ["web-api", "bff"]
  keywords: ["bff", "gateway"]

  I need you to create a configuration file .github/config/repos.yaml that lists my team's repositories with metadata used by a Backlog Refinement Agent for repo detection.

For each repo I list below, use the GitHub MCP tools to fetch:

The repository description (from GitHub repo metadata)
The primary language / tech stack (infer from GitHub's language stats — pick the dominant one)
A list of likely Jira components (infer from repo name and description — best guess)
A list of keywords that might appear in Jira tickets pointing to this repo (infer from repo name, description, README first paragraph if accessible)
Steps to execute:

For each repo in the list at the bottom of this prompt:
Call GitHub MCP get_repository (or equivalent) to fetch repo metadata
Call GitHub MCP to fetch the README (first 500 chars is enough)
Extract: description, primary language, owner/repo identifier
Infer components and keywords from name + description + README
Create the file .github/config/repos.yaml with the following structure:
yaml


# Repository registry used by the Backlog Refinement Agent
# for detecting target repo from Jira ticket signals.
#
# Signals (in priority order) used for matching:
#   1. Linked PRs in ticket  (highest confidence)
#   2. Jira components       (high confidence)
#   3. Jira labels           (medium confidence)
#   4. Keywords in title/description (medium confidence)
#   5. Parent epic component (low-medium confidence)

repos:
  - name: <short-display-name>
    github: <owner/repo>
    description: <one-line description>
    stack: <primary language or framework>
    components:
      - <jira component name 1>
      - <jira component name 2>
    labels:
      - <likely jira label 1>
    keywords:
      - <keyword 1>
      - <keyword 2>
      - <keyword 3>
Important rules:
Use real data from GitHub MCP for github, description, and stack
For components, labels, and keywords: make reasonable guesses based on the repo name and description. Use lowercase. Mark uncertain inferences with a YAML comment # verify on that line.
If GitHub MCP can't fetch a repo (e.g., access denied, not found), still include the entry but mark the missing fields as null with a comment # could not fetch — please fill in
Keep keywords focused (3–6 per repo, not 20). Pick distinctive ones that uniquely identify the repo.
Avoid generic keywords like "code", "java", "javascript" — those don't help discriminate between repos.
After creating the file, print a summary table showing:
Repo name
GitHub identifier
Stack detected
Number of components / keywords inferred
Any fields that need manual review
Repos to process:



1. <owner>/<repo-name-1>
2. <owner>/<repo-name-2>
3. <owner>/<repo-name-3>
(Add or remove as needed. Use the format owner/repo.)
