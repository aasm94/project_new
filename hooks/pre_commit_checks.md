# Pre-Commit and Task Review Checks

This file acts as a mandatory validation hook before a task or commit is considered "Complete". **Antigravity** (and human developers) must ensure the following checks pass:

## 🔍 Code Cleanliness
- [ ] **No Development Output:** Remove all leftover debugging statements (`console.log`, `print()`, `dd()`, `var_dump()`, etc.) unless specifically requested as a feature requirement.
- [ ] **Clean Code Rules:** Ensure the implemented solution adheres strictly to `.antigravity/skills/architecture/clean_code_rules.md`.
- [ ] **Naming Conventions:** All variables, functions, and files must follow the patterns defined in `.antigravity/skills/architecture/naming_convention.md`.

## 🏗️ Architectural Integrity
- [ ] **No Scope Creep:** Only the required code logic has been modified. Avoid unrelated adjustments unless explicitly reported first.
- [ ] **Agnostic Constraints:** Ensure you haven't introduced platform-specific assumptions into neutral logic inside `/src/` without user confirmation.

## ✅ Verification
- [ ] **Run Tests:** If test scripts exist in `ANTIGRAVITY.md` or the `package.json`/equivalent in the `/src/` folder, they must be executed and pass successfully.
- [ ] **Comments & Docs:** Have you documented complex areas according to `.antigravity/skills/architecture/comments_doc.md`?
