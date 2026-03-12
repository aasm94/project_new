# ADR 0001: Adopt Universal Base Architecture (Claude Code Model)

**Date:** 2026-03-11  
**Status:** Accepted

## Context
The project was previously operating under a loose system of agent `.md` skill files inside a `/skills/` folder at the root. As the system scales and is connected to automation workflows (like `n8n`), maintaining a clean context for AI agents (and humans) became challenging. Additionally, there was a risk of the AI making incorrect assumptions about the environment (e.g., assuming it's a web app when it's just a backend API).

## Decision
We decided to adopt a **Universal Base Architecture** inspired by the Claude Code Model to optimize AI and developer context.

1.  **Categorized Skills:** The flat `/skills/` folder was deprecated and moved to `.antigravity/skills/*` with explicit categories (Frontend, Backend, Devops, etc.).
2.  **Agnostic Source Container:** We established the `/src/` directory to house all application code, ensuring the architecture remains agnostic to language or framework.
3.  **Project Memory:** Introduced `ANTIGRAVITY.md` to act as the short-term memory (guidelines, frequency commands, state) that agents parse on initialization.
4.  **ADR Tracking:** We created the `/docs/adr/` directory (where this file lives) to document structural "whys".
5.  **Hooks Constraints:** Added the `/hooks/` folder to create rigid boundaries and checklists (like `pre_commit_checks.md`) before finishing tasks.

## Transition Strategy (n8n Integration)
Because existing `n8n` workflows read static `.md` files directly from the `/skills/` folder, **we did not immediately delete the old directory.** Instead, files were duplicated into `.antigravity/skills/` and a **deprecation warning** was injected into the original files. This creates a safe buffer for users to manually update `n8n` before the final cleanup of the legacy folder.

## Consequences
*   **Positive:** Massively improved context window efficiency for the agent. Easier onboarding for humans. Clear separation of AI instructions, memory, decisions, and source code.
*   **Negative:** Added a brief transition period where skills are duplicated across the repository until `n8n` links are migrated.
