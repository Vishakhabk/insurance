# DemoIntern

> Auto-generated project context for AI-assisted development.
> Last updated: 2026-11-13

**Organization:** Test27112025

## Development Methodology

This project follows **Spec-Driven Development (SDD)**.

Every feature has:
- `specs.md` — Full technical specification
- `requirements.md` — Implementation acceptance checklist
- `prompt.md` — Ready-to-use implementation prompt

## Features (3)

- **As a Service Agent, I want to search by a single-record identifier so that I can retrieve the correct customer and account summary quickly** (1 user stories)
- **Fraud Detection As a Compliance Officer, I want DemoIntern to retain and audit account management planning records and AI interaction evidence so that regulated banking reviews can verify who did what, when, and under which policy** (1 user stories)
- **Edited As a Bug Management User, I want to record a bug for the DemoIntern project so that defects are captured with consistent and traceable information** (0 user stories)

## Getting Started

1. Read this file for project context
2. Check `specs/.devx/workflow.md` for the development workflow
3. Review `specs/.devx/instruction.md` for architecture and multi-repo rules
4. Pick a feature from `specs/.devx/features.json`
5. Open the feature's `prompt.md` and use it with your AI assistant
6. Follow the spec and requirements to implement

## Project Structure

```
specs/
  .devx/
    project.md          ← You are here
    workflow.md          ← Development workflow
    features.json        ← Feature index (machine-readable)
    tracker.json         ← Code-generation execution status
    generation.json      ← Last generation metadata
    architecture.md      ← System architecture
    init.sh              ← Setup AI tool configs
  <feature-slug>/
    specs.md             ← Technical specification
    requirements.md      ← Implementation acceptance checklist
    prompt.md            ← Implementation prompt
```

## AI Tool Setup

Run the init script to configure your AI tools automatically:

```bash
bash ./specs/.devx/init.sh
```

If you want execute permissions as well:

```bash
  chmod +x ./specs/.devx/init.sh && ./specs/.devx/init.sh
```

The script lists supported AI tools, lets you choose one, and creates only that tool's config files.
