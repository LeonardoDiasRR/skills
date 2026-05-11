# 🧠 CONSTITUTION.md
## AI Coding Agent Constitution

This document defines the **non-negotiable principles, architectural standards, and behavioral rules** that MUST guide all code generation, modification, and analysis performed by the AI agent.

The agent MUST always prioritize:
> **Simplicity > Clarity > Maintainability > Performance**

---

# 1. Core Coding Principles

## 1.1 DRY (Don't Repeat Yourself)
- The agent MUST avoid code duplication at all costs.
- Any repeated logic, value, or structure MUST be abstracted into:
  - variables
  - functions
  - modules
- Rationale:
  - Reduces token usage
  - Prevents inconsistency
  - Avoids context loss within LLM window

---

## 1.2 KISS (Keep It Simple, Stupid)
- The agent MUST always choose the simplest possible implementation.
- The agent MUST NOT introduce unnecessary abstractions or complexity.
- Prefer:
  - small files (≤ 150–300 lines)
  - direct and readable logic
- The agent MUST avoid overengineering.

---

## 1.3 YAGNI (You Aren't Gonna Need It)
- The agent MUST NOT implement:
  - speculative features
  - unnecessary edge cases
  - premature optimizations
- Only implement what is explicitly required.

---

## 1.4 TDD (Test-Driven Development)
- The agent MUST:
  - write tests before OR alongside implementation
- Every feature MUST include:
  - unit tests
  - deterministic assertions
- Code WITHOUT tests is considered INCOMPLETE.

---

## 1.5 Separation of Concerns
- Each file MUST have a single responsibility.
- The agent MUST NOT mix:
  - UI logic with business logic
  - configuration with domain logic
- Violations cause:
  - Context Poisoning
  - Increased hallucination risk
  - Reduced maintainability

---

# 2. Architectural Principles

## 2.1 Mandatory Architecture: Feature-Based

The agent MUST organize the project using **Feature-Based Architecture**.

---

## 2.2 Why This Is Mandatory

Layer-based architecture:
- Forces the agent to scan many irrelevant files
- Increases token usage
- Causes inefficient reasoning

Feature-based architecture:
- Reduces context size
- Improves reasoning accuracy
- Prevents context poisoning
- Enables isolated feature development

---

## 2.3 Required Project Structure

The agent MUST follow this structure:

    src/
    ├── features/
    │   ├── <feature-name>/
    │   │   ├── components/
    │   │   ├── hooks/
    │   │   ├── services/
    │   │   ├── store/
    │   │   ├── types/
    │   │   └── tests/
    │
    └── shared/
        ├── ui/
        ├── utils/
        └── constants/

---

## 2.4 Feature Encapsulation Rule

- Each feature MUST be self-contained.
- A feature MUST include:
  - UI (components)
  - Logic (hooks/services)
  - Types
  - State (if applicable)
  - Tests

---

## 2.5 Shared Layer Constraints

- The `shared/` directory MUST contain ONLY:
  - reusable UI components
  - generic utilities
- The agent MUST NOT place business logic in `shared/`.

---

# 3. Context Optimization Rules (LLM-Specific)

## 3.1 Context Efficiency
- The agent MUST minimize the number of files required to solve a task.
- The agent SHOULD operate within a single feature whenever possible.

---

## 3.2 Context Poisoning Prevention
The agent MUST avoid introducing unrelated logic into files.

Examples of violations:
- Mixing authentication logic inside sales modules
- Adding global state unnecessarily
- Placing unrelated types together

---

## 3.3 File Size Control
- Files SHOULD remain small and focused.
- If a file exceeds reasonable size:
  - it MUST be split by responsibility

---

# 4. Behavioral Rules for the Agent

## 4.1 Decision Making
The agent MUST:
- state assumptions explicitly before implementing; stop and ask when uncertain
- present multiple interpretations when they exist — never choose silently
- prefer simple solutions over complex ones
- justify non-trivial decisions
- push back when a simpler approach exists before implementing the complex one

The agent MUST NOT:
- make assumptions not present in requirements
- proceed past confusion — name what is unclear and ask

---

## 4.2 Implementation Discipline
The agent MUST NOT:
- create unused code
- leave incomplete implementations
- ignore error handling at system boundaries (user input, external APIs)
- add error handling for scenarios that cannot happen
- introduce hidden side effects
- modify code adjacent to the change unless directly required by the task
- delete pre-existing dead code without being asked — mention it instead
- deviate from the existing code style

When the agent's own changes create orphaned imports, variables, or functions, the agent MUST remove them.

---

## 4.3 Testing Discipline
- Every feature MUST be testable
- The agent MUST transform tasks into verifiable goals before writing code:
  - "Fix the bug" → write a test that reproduces it, then make it pass
  - "Add validation" → write tests for invalid inputs, then make them pass
- For multi-step tasks, the agent MUST state a brief plan with a verify step per stage
- Tests MUST:
  - be deterministic
  - not depend on external state
  - reflect real use cases

---

# 5. Definition of Done

A task is ONLY considered complete if:

- Code follows all principles (DRY, KISS, YAGNI)
- Architecture follows Feature-Based structure
- No context poisoning is introduced
- Tests are implemented and passing
- Code is readable and maintainable

---

# 6. Enforcement Priority

If conflicts arise, the agent MUST follow this order:

1. Simplicity (KISS)
2. Correctness (TDD)
3. Separation of Concerns
4. DRY
5. Architecture Rules

---

# Final Rule

The agent MUST ALWAYS read and comply with this CONSTITUTION.md  
BEFORE generating, modifying, or reviewing any code.

Failure to comply invalidates the output.