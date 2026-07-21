---
description: "Run mutation testing against production code to find test gaps. Use when user says /mutation-test."
allowed-tools:
  - Bash
  - Read
  - Edit
---

# Mutation Testing

Evaluate test quality by introducing deliberate defects (mutations) into production code and checking whether the test suite catches them. A surviving mutant = a test gap.

## Arguments

- `$1` — path to the production source file to mutate
- `$2` — (optional) test filter or command. If omitted, infer from the project type and source file name.

## Rules

- **Only mutate production code, never test code.**
- Apply exactly one mutation at a time.
- Run the relevant tests after each mutation.
- Revert the mutation before applying the next one (use `git checkout` on the file).
- Never commit mutations.
- **Exhaustive coverage** — apply every meaningful mutation operator to every non-trivial line. Do not sample or skip candidates. The goal is a complete report, not a quick one.

## Mutation Operators

Apply these operators, picking candidates that exercise meaningful logic (skip trivial/unreachable code):

| Operator               | Example                                                          |
|------------------------|------------------------------------------------------------------|
| **Negate conditional** | `<` → `>=`, `==` → `!=`, `&&` → `\|\|`                           |
| **Boundary shift**     | `<` → `<=`, `>` → `>=`                                           |
| **Remove guard**       | `if (cond && extra)` → `if (cond)` (drop one conjunct)           |
| **Statement deletion** | Remove a statement entirely (e.g. an assignment, a method call)  |
| **Return value**       | `return null` → `return new ...`, `return true` → `return false` |
| **Constant change**    | `0` → `1`, `1` → `0`, `""` → `"mutant"`                          |
| **Arithmetic**         | `+` → `-`, `*` → `/`, `++` → `--`                                |

## Procedure

1. **Read** the production source file (`$1`).

2. **Identify** mutation candidates — list them with line number, operator, and what the mutation does. Skip trivial ones (e.g. imports, type declarations).

3. **Present** the candidate list to the user and wait for approval before proceeding.

4. **Execute** each mutation:
   a. Apply the mutation via the Edit tool.
   b. Run the test command with appropriate filtering. Capture pass/fail.
   c. Record result: **killed** (test failed = good) or **survived** (tests still passed = gap).
   d. Revert the file: `git checkout <file>`.

5. **Report** results in a summary table:

```
## Mutation Testing Report — <filename>

| # | Line | Operator | Mutation | Result |
|---|------|----------|----------|--------|
| 1 | 42   | Negate   | `<` → `>=` | Killed |
| 2 | 55   | Delete   | Remove `cursor.Index++` | **SURVIVED** |

**Score: X/Y killed (Z%)**

### Surviving Mutants (test gaps)
- **#2** (line 55): Removing `cursor.Index++` — tests don't verify ...
```

6. For each surviving mutant, briefly explain what test is missing and suggest a test name.
