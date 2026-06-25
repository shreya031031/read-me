eate a new file .github/prompts/generate-notes.prompt.md with the following structure.

1. Frontmatter:



---
mode: agent
description: Generate concise technical notes after open questions are answered
---
2. Instructions to Copilot:

Step 1 — Context check: Before doing anything, verify the chat already contains:

A previous /refine-ticket analysis output for some ticket
User-provided answers to the Critical questions
If either is missing:

State clearly what's missing
Ask the user to either run /refine-ticket first, or provide the missing answers
STOP
Step 2 — Re-fetch decision: Decide if you need to re-fetch ticket details from Atlassian MCP:

If chat already has full ticket context → DO NOT re-fetch
If chat is missing key details (e.g., AC text) → fetch only what's needed
Step 3 — Validate all Critical questions are answered:

Review the answer template in the chat
If any Critical question is still unanswered or has placeholder text → list which ones and STOP
Important and Optional questions do NOT block notes generation
Step 4 — Generate Technical Notes: Use this exact template:

markdown


# Technical Notes: {TICKET_ID}

## 📌 Summary
{1-2 sentences: what we're building, based on ticket + answers}

## 🔧 File Changes

### 1. `path/to/File1.java`
**Purpose:** {what role this file plays in the change}
**Changes:**
- {Specific change 1}
- {Specific change 2}

{Include code snippet ONLY if:
  - It's a new endpoint (show method signature with annotations)
  - It's non-obvious business logic (show 2-3 key lines)
  - It's a new bean/config (show declaration)
Otherwise SKIP the snippet.}

```java
// 2-3 lines max, only the essential part
2. path/to/File2.java
Purpose: ... Changes:

...
N. {one section per file}
🧪 Test Considerations
{Specific test cases needed, as bullets}
⚠️ Assumptions & Dependencies
{Any assumptions made based on answers}
{Any external dependencies or coordination needed}
Generated from refinement analysis. Verify against ticket and code before implementation.




**3. Strict rules to enforce:**
- One file = one section
- Use **actual file paths** verified in the workspace
- Concise: each section's "Changes" should be 2-5 bullets max
- Code snippets: maximum 2-3 lines, only when they add real value
- Skip snippets for: simple field additions, configuration tweaks, obvious DTO changes
- Include snippets for: new endpoints (controller method signature), key business logic, new beans
- Don't repeat information from earlier in the chat (be a notes generator, not a re-analyzer)
- Don't invent file paths

**4. Example to include in the prompt (for Copilot's reference):**

For "add a new GET endpoint for fetching user preferences":
```markdown
### 1. `src/main/java/com/.../UserPreferencesController.java`
**Purpose:** Expose new GET endpoint
**Changes:**
- Add new `@GetMapping("/users/{id}/preferences")` method
- Inject `UserPreferencesService`
- Return `UserPreferencesDTO`

```java
@GetMapping("/{id}/preferences")
public UserPreferencesDTO getPreferences(@PathVariable Long id) { ... }
2. src/main/java/com/.../UserPreferencesService.java
Purpose: Business logic for fetching preferences Changes:

Add getPreferences(Long userId) method
Load from UserPreferencesRepository
Map entity to DTO
3. src/main/java/com/.../UserPreferencesRepository.java
Purpose: Data access Changes:

Add findByUserId(Long userId) query method



Notice: only the controller has a snippet (new endpoint signature). Service and repository describe changes in bullets without snippets — that's the right balance.

**5. End the prompt with:** "Generate technical notes based on the current ch
