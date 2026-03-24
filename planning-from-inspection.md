You are working in a local repository.

You have already performed a read-only inspection of the project and have a summary of the current state.

Your task is to produce a **small, concrete execution plan** for the given objective.

Do NOT implement anything.
Do NOT modify any files.
Focus only on planning.

---

## Inputs

- Objective: {{OBJECTIVE}}
- Inspection summary:
{{INSPECTION_SUMMARY}}

---

## Requirements

1. Use the inspection summary as the source of truth
   - Do not invent new architecture
   - Do not assume files or systems that were not observed

2. Produce a **minimal plan**
   - Prefer the smallest set of changes that achieve the objective
   - Avoid unrelated refactors or improvements

3. Be explicit and concrete
   - Name exact file paths to modify
   - Describe what will change in each file
   - Keep changes scoped and localized

4. Respect constraints
   - Do not break patterns identified in the inspection
   - Call out any risky areas explicitly

---

## Output Format

### Objective
Restate the objective in one or two sentences.

### Approach
Describe the chosen approach briefly.
Explain why it is the minimal viable path.

### Steps
List ordered steps. Each step must include:
- step name
- files to modify (with paths)
- exact intent of the change

Example:

1. Update global metadata handling  
   - Files: frontend/app/layout.tsx, frontend/app/lib/seo.ts  
   - Change: adjust metadata generation to include canonical and openGraph defaults

### Verification
Define how to verify correctness:
- build commands
- tests (if applicable)
- manual checks (if necessary)

### Risks and Constraints
List any risks or fragile areas based on the inspection.

### Out of Scope
Explicitly state what you are NOT changing.

---

## Rules

- Do not propose multiple alternative approaches
- Do not expand scope beyond the objective
- Do not include implementation code
- Prefer clarity over completeness

