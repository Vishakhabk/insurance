1. **Technology Stack Summary**
- The only code-level evidence is a Node.js ES module MCP server in `specs/.devx/mcp/`. It uses `"type": "module"` and `"main": "server.js"`, and it starts with `node server.js` (source: specs/.devx/mcp/package.json).
- The sole declared dependency is `@modelcontextprotocol/sdk` `^1.12.1` (source: specs/.devx/mcp/package.json).
- The MCP server runs over stdio (source: specs/.devx/mcp/README.md).
- The application stack for the insurance product is **not evidenced** in the sampled files. The tree includes `golden-stack-options.json/.env`, but their contents were not sampled (source: specs/.devx/guidance/versions/8c546c37a750/golden-stack-options.json).
- The domain is vehicle claim intake and validation, covering FNOL capture, validation, India localization/timezone and accessibility (source: specs/.devx/architecture.md).
- Shell scripts exist for tooling: `init.sh`, `discover-workspace.sh`, `devx-command.sh`, `validate-tracking.sh` (source: README in specs/.devx/mcp/README.md, tree listing).

2. **Project Structure Overview**
- The root `README.md` is an unfilled template with TODO placeholders (source: README.md).
- `specs/.devx/` is a generated spec-driven workflow toolkit. It contains:
  - `architecture.md`, `project.md`, `workflow.md`, `current-state.md`, `features.json`, `tracker.json`
  - `commands/`, `skills/` (autopilot, implement, next, ui-design, validate), `guidance/versions/<hash>/`, `change-maps/`, `mcp/`
  (source: tree listing)
- `specs/claim-intake-validation-localization-accessibility/` holds the feature spec set: `prompt.md`, `requirements.md`, `specs.md`, `tasks.md` (source: tree listing).
- The MCP server sits in `specs/.devx/mcp/` with `server.js`, `package.json` and `README.md`. Its `node_modules` is ignored via generated `.gitignore` files (source: specs/.devx/mcp/README.md).
- No application source directories (`src/`, etc.) appear in the tree. The 44 discovered files are almost entirely specs and tooling (source: tree listing).

3. **Naming Conventions**
- Feature folders use kebab-case slugs, e.g. `claim-intake-validation-localization-accessibility` (source: tree listing).
- Change-map files share the feature slug with `.json` and `.md` extensions (source: specs/.devx/change-maps/).
- Per-feature spec files use fixed names: `prompt.md`, `requirements.md`, `specs.md`, `tasks.md` (source: tree listing).
- The generated project identifier is `qa051126`, used in the package name `qa051126-mcp-server` (source: specs/.devx/mcp/package.json, specs/.devx/architecture.md).
- The MCP server key is `devx-specs`, and tools use snake_case, e.g. `list_features`, `get_feature_specs` (source: specs/.devx/mcp/README.md).
- Guidance versions are stored under a hash-named directory, `8c546c37a750` (source: tree listing).

4. **Coding Style Guidelines**
- Not detected for application code. No linter or formatter configuration was observed in the tree.
- The only stated guidance is generic: follow the specs in each feature folder and the project's established patterns, and extract shared logic into common modules (source: specs/.devx/architecture.md).
- `golden-repo-guidelines.md` likely holds more detail, but it was not sampled (source: specs/.devx/guidance/versions/8c546c37a750/golden-repo-guidelines.md).

5. **Common Design Patterns**
- **Spec-driven development workflow:** each feature has prompt, requirements, specs and tasks files. A feature registry (`features.json`) and a `tracker.json` track status (source: tree listing, specs/.devx/mcp/README.md).
- **MCP tool exposure:** specs are exposed to AI tools through tools such as `list_features`, `get_next_feature` and `mark_feature_done` (source: specs/.devx/mcp/README.md).
- **Multi-agent integration:** the same stdio server config is used for Claude Code, Cursor, VS Code and Kiro. `init.sh` generates `AGENTS.md` for Codex and Kiro steering context (source: specs/.devx/mcp/README.md).
- The architecture guidance says features should be independently deployable where possible (source: specs/.devx/architecture.md).

6. **Framework and Library Usage**
- `@modelcontextprotocol/sdk` is used for the MCP server (source: specs/.devx/mcp/package.json).
- No application frameworks, UI libraries or ORMs are evidenced.
- `server.js` exists but its contents were not sampled, so the exact SDK usage is unverified (source: tree listing).

7. **Testing Approach**
- Not detected. No test files, test scripts or CI config were observed. `package.json` has only a `start` script (source: specs/.devx/mcp/package.json).
- The MCP server mentions an optional `get_tdd_tests` tool that reads `tdd-tests.md` "if TDD enabled", but no such file is in the tree (source: specs/.devx/mcp/README.md).
- A `validate_implementation` tool returns an acceptance checklist, and `validate-feature.md` and `validate-tracking.sh` exist, but their contents were not reviewed (source: specs/.devx/mcp/README.md, tree listing).

8. **Build and Deployment Standards**
- Setup runs `bash specs/.devx/init.sh`, which installs MCP dependencies, or manually runs `npm install` in `specs/.devx/mcp` (source: specs/.devx/mcp/README.md).
- The README recommends invoking scripts via `bash` because execute permissions may not survive cloning (source: specs/.devx/mcp/README.md).
- The server is started with `node server.js` (source: specs/.devx/mcp/package.json).
- No application build, CI/CD pipeline or deployment configuration was evidenced.

9. **Notable Gaps / Unknowns**
- The product's runtime technology stack (language, framework, database) is unknown. The contents of the golden stack options and guidelines files were not sampled.
- The root `README.md` is an unfilled template, so there is no project description, build or test instructions (source: README.md).
- The Data Models, API Contracts and Integration Points sections in `architecture.md` are placeholders (source: specs/.devx/architecture.md).
- Only 4 of 44 files were sampled. The contents of `requirements.md`, `specs.md`, `tasks.md`, `features.json`, `tracker.json`, `server.js` and the shell scripts were not reviewed, so feature status and detailed acceptance criteria are unknown.
- No application source code, tests or CI configuration are visible in the tree.