# Agent team

Summary of the Project Pulse agent team and where definitions live.

- Orchestrator — model: Claude Opus 4.7 — coordinates Planner, Coder, Designer; file: .github/agents/orchestrator.agent.md
- Planner — model: Claude Opus 4.7 — researches the codebase and produces ordered implementation plans, file assignments, dependencies, and validation criteria; file: .github/agents/planner.agent.md
- Coder — model: GPT-5.5 — implements code, runnable app support, and validation for Project Pulse; file: .github/agents/coder.agent.md
- Designer — model: Gemini 3.1 Pro — responsible for UI/UX, accessibility, and visual design for the dashboard; file: .github/agents/designer.agent.md

Work will be coordinated using GitHub Copilot CLI running in a Codespace to orchestrate tasks and agent interactions.

README-style notes

Roles

- Orchestrator: Breaks work into phases, assigns file scopes, and verifies integration. Use for coordination and sequencing.
- Planner: Produces a prioritized, testable plan with file-level assignments and validation steps. Use first to define scope and dependencies.
- Coder: Implements code and runnable app support within assigned files; includes validation and minimal launch configs when needed.
- Designer: Provides UI/UX direction, accessible styles, and component-level CSS hooks for predictable integration.

Workflow

1. Request a plan from the Planner describing the desired outcome and constraints.
2. Orchestrator converts the plan into phases and assigns files to Coder and Designer (non-overlapping scopes run in parallel).
3. Coder and Designer implement assigned files, report changes, and provide validation notes.
4. Orchestrator integrates outputs, runs final validation, and reports completion or blockers.

Delegation examples

- Small UI polish: Orchestrator assigns only CSS/HTML files to Designer; Coder is not involved.
- New backend endpoint + UI: Planner outlines steps; Orchestrator assigns API routes and tests to Coder and dashboard views to Designer; work runs sequentially if schema changes are involved.
- Runnable demo: Orchestrator assigns app preview support to Coder (create .vscode/launch.json and open index.html) and asks Designer to prepare the initial responsive layout.

Notes

- Specialists must stay within assigned files unless the Orchestrator reassigns scope.
- All agents follow the repository patterns and report validation steps and remaining risks.
- Git operations (stage/commit/push) are controlled by the learner via Copilot CLI prompts.