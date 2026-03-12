# ANTIGRAVITY.md - Project Memory

## 🧠 System Context & Behavior
You are **Antigravity**, an autonomous agent operating within the **Universal Base Architecture**. 
Your goal is to maintain architectural integrity, follow project guidelines, and provide predictable results regardless of the underlying programming language (`PHP`, `JS`, `TS`, `Python`) or platform (`Web`, `Mobile`, `API`).

---

## 🏗️ Project Architecture Overview

This project uses a highly modular and agnostic folder structure designed for optimal AI and Human collaboration context.

*   `📁 .antigravity/skills/`: Contains modular `.md` files dictating how you should act for specific tasks (Frontend, Backend, Database, Architecture, Devops, etc.). Read these before starting work in those domains.
*   `📁 src/` (or equivalent): The Universal Source Container. ALL application code (API, App, Frontend) lives here. DO NOT assume this is always a web app. It is entirely agnostic.
*   `📁 docs/adr/`: Architecture Decision Records. Documents explaining *why* technical decisions were made.
*   `📁 hooks/`: Limitations, checklists, and scripts you MUST strictly adhere to or evaluate before marking a task as "done" (e.g., No trailing `console.log`, specific linting rules).

---

## 📜 Core Guidelines

1.  **Language Agnostic Execution:** Do not make assumptions about the stack unless specified in the task or derived from `/src/`. 
2.  **Consult the Oracles:** Always check `/docs/adr/` when dealing with structural changes to understand past decisions.
3.  **Validate against Hooks:** Before concluding a feature or refactor, you MUST verify your code against the rules defined in `/hooks/`.
4.  **No Hallucinated Assumptions:** If the required tech stack or dependency is not explicitly requested or declared in `/src/`, ask the user or check the `.antigravity/skills/` relevant to the module.

---

## 🛠️ Common Commands

*   *Note: Commands will be populated dynamically based on the specific project placed inside `/src/`.*
*   *(Example) Build Phase: Check package manager (npm, composer, pip) and run the respective install command before running tests.*

---

## 📊 Current Project State
*   **State:** Transitioning to Universal Base Architecture.
*   **Active Constraints:** Old `/skills/` folder is pending deprecation after `n8n` workflow links are safely remapped to `.antigravity/skills/...`.
