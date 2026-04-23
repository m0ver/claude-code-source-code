# Repository & Architecture Deep Dive

## Main Modules and Responsibilities

- **src/commands/**: Implements high-level user commands (e.g., security review, privacy settings).
- **src/services/**: Handles analytics (telemetry), remote managed settings, and integration logic.
- **src/tools/**: Home to “tools” available to agents, with modules for Bash, FileRead/Write, Agent memory, etc.
- **src/types/**: Common type definitions, especially for permissions, tool use, etc.
- **docs/**: Extensive reports and deep dives into security, telemetry, and hidden flag systems (`docs/en/01-telemetry-and-privacy.md`, `docs/en/02-hidden-features-and-codenames.md`).

---

## Core Design Patterns & Agents/Tools System

- **Agent/Tool Abstraction**: The code employs an agent-centric architecture where "agents" invoke tools to accomplish user and automated tasks (see `src/tools/AgentTool/built-in/claudeCodeGuideAgent.ts`).
- **Permissions System**: Fine-grained permissions—before tools run, explicit allow/ask/deny decisions are calculated, with reasons, classifier hooks, and (optionally) user prompts (`src/types/permissions.ts`).
    - Permissions respect tool, context, input, and higher-level security policies (`src/tools/BashTool/bashPermissions.ts`).

---

## The Query Engine

- **Flow**: Entry → Query Engine → Tools/Services/State
- The query engine orchestrates user inputs, workflow management, and tool invocations, ensuring all permission/security checks are passed.
- Specialized hooks for MFA, user input confirmation, and agent-sandbox isolation are present.

---

## Feature Highlights

- **Telemetry & Privacy**:
    - Two-tier analytics: Core telemetry goes to Anthropic (OpenTelemetry Protobuf), selective events to Datadog.  
    - Data includes platform, environment, user/session hashes, tool invocation details.  
    - "Opt-out" is not supported for core logging (see `src/services/analytics/firstPartyEventLogger.ts`).  
    - Deep-dive in `docs/en/01-telemetry-and-privacy.md`.

- **Security/Permission Mechanisms**:
    - Tool actions are sandboxed; compounding bash commands with path/generic constraints.
    - Bash tool permission logic ensures dangerous command patterns (multiple cd, cd+git, path traversal) require explicit user approval (`src/tools/BashTool/bashPermissions.ts`).
    - Security review commands and methodologies are present (`src/commands/security-review.ts`).

- **Remote Management & Killswitches**:
    - Supports remote management of settings. Security dialogs block dangerous settings unless the user approves (`src/services/remoteManagedSettings/securityCheck.tsx`).

- **Agent/Tool Memory**:
    - Agents track state/memory in structured, project-scoped directories (e.g. `.claude/agent-memory/`).  
    - Remote memory dirs supported for session migration (`src/tools/AgentTool/agentMemory.ts`).

- **Codenames & Feature Flags**:
    - Uses “animal” codenames for models and all feature flags—`tengu_`, `capybara`, `numbat`, etc.—to obfuscate functionality.
    - See `docs/en/02-hidden-features-and-codenames.md` for examples.

---

## Missing Modules & Build Process

- **108 feature-gated modules** are absent: any attempt to use them results in stub fallback (see `scripts/stub-modules.mjs`).
- **Build Notes**: Direct compilation is not possible without stubbing. A build script generates stub modules for all missing features, iteratively compiling until stubs are complete.  
    - Example from build script output:
        ```
        ✅ Created X stubs for Y missing modules
        🔨 Attempting esbuild bundle...
        ```

---

## Security & Privacy Measures

- **Telemetry cannot be fully disabled** (except in test environments).
- **Critical tool operations** (deleting, moving, shell, etc) undergo path checks, multi-command validation, and sometimes require real-time user authorization.
- **Remote killswitch and settings management** allow Anthropic to push emergency config changes, with interactive security confirmation dialogs enforced on the user side.
- **Agent memory and cached data** are restricted to project or user scope, sanitized to prevent path traversal attacks (`src/tools/AgentTool/agentMemory.ts`).

---

## Notable Capabilities and Limitations

### Capabilities
- Robust agent-based automation with plugin support
- Fine-grained tool permissions
- Advanced telemetry and auditing
- Security review tools and defensive design patterns
- Multi-language documentation and code comments

### Limitations
- Not directly runnable (must stub 100+ modules for build)
- Commercial and production use strictly disallowed
- Some key modules are feature-gated and unavailable for local testing
- Privacy opt-out is not fully supported

---

## References

- [Telemetry & Privacy](https://github.com/m0ver/claude-code-source-code/blob/main/docs/en/01-telemetry-and-privacy.md)
- [Hidden Features, Codenames & Flags](https://github.com/m0ver/claude-code-source-code/blob/main/docs/en/02-hidden-features-and-codenames.md)
- File: `src/types/permissions.ts`, `src/tools/BashTool/bashPermissions.ts`, `src/services/remoteManagedSettings/securityCheck.tsx`, `scripts/stub-modules.mjs`, and others.

---

*This file was auto-updated to provide a deeper technical summary of the repository and its software architecture.*