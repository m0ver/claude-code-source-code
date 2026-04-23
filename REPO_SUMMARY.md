# Repository & Architecture Summary

## Overview

**claude-code-source-code** contains the decompiled source for Anthropic’s `@anthropic-ai/claude-code` npm package (v2.1.88). This repository is strictly for technical research, study and educational exchange; commercial use is strictly prohibited. All code is owned by Anthropic/Claude.

### Composition
- **Languages:** TypeScript (99.9%), JavaScript (0.1%)
- **Original npm package:** Only ships a bundled `cli.js`; this repo provides the unbundled TypeScript source in `src/`
- **Docs:** Deep analysis and code review available in `/docs/` in 4 languages
- **108 modules** are missing (feature gated in npm bundle; their absence is documented)


## Architecture Overview

**Flow:** Entry Point → Query Engine → Tools/Services/State

### Description
- **Entry Point**: CLI or programmatic API entry for user commands/interactions
- **Query Engine**: Core logic; manages user input, coordinates execution of tasks, orchestrates tool invocations
- **Tools/Services/State**: Set of 40+ built-in tools for code operations (editing, file IO, AI actions, telemetry, etc), all with explicit permissions and sometimes sub-agent (plug-in) support

### Supplementary Mechanisms
- **Tool system & permissions**: Permissions are managed to control what tools can do, ensuring security and transparency
- **Progressive harnesses**: Internal agent-loop mechanisms allow gradual roll-in of complex production features


## Key Documentation (`/docs/`)
- Telemetry & privacy: What data is collected/how it’s used
- Hidden features, codenames, feature flags
- Undercover mode: Hiding AI authorship
- Remote control and killswitches: Central settings, off switches
- Roadmap: Unreleased features (e.g., Numbat, KAIROS, voice mode)

See `README.md` for full language navigation and deeper technical drilldown.

---

*This file was auto-generated to provide a summary of the repository and its software architecture.*