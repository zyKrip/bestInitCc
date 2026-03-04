---
name: claude-code-init
description: This skill should be used when initializing Claude Code for a new project or optimizing an existing project's Claude Code configuration. It provides a complete workflow for setting up the six-layer CLAUDE.md memory system, rule splitting with path-scoped frontmatter, permission policies, hooks (with all 7 event types), skills, MCP servers, Plugins/LSP, multi-agent architecture, and security hardening. Based on the Claude Code终极指南 (上篇+下篇) and Anthropic hackathon champion best practices. Trigger when users say "init", "initialize", "setup claude code", "configure project", or want to optimize their Claude Code workflow.
---

# Claude Code Project Initializer

## Overview

Transform Claude Code from a basic chat tool into a fully configured development operating system.

Core philosophy: "Claude Code is not a chat tool — it is an operating system." (Boris Cherny, Claude Code founder)

Sources: Claude Code终极指南 (上篇: 记忆系统到多代理架构, 下篇: 自动化、生态与安全工程), Anthropic hackathon champion Affaan Mustafa's everything-claude-code repository (50,000+ GitHub Stars), official documentation.

## Workflow

### Phase 1: Project Analysis

Analyze the current project before making any configuration:

1. Read `package.json`, `pyproject.toml`, `Cargo.toml`, `go.mod`, or equivalent to identify tech stack
2. Scan directory structure to understand project organization (`ls -la`, `ls src/`)
3. Check for existing `.claude/` directory, `CLAUDE.md`, `CLAUDE.local.md`, or `.mcp.json`
4. Identify package manager (npm/pnpm/bun/yarn/uv/poetry/cargo)
5. Detect testing framework, build tools, and formatter (prettier/ruff/rustfmt/gofmt)
6. Check for existing CI/CD configuration (`.github/workflows/`, etc.)

### Phase 2: CLAUDE.md Memory System Setup

The CLAUDE.md memory system is the foundation — it solves 90% of usage problems. For detailed guidance, refer to `references/claude-md-guide.md`.

**Step 1: Select template** based on project type from `assets/`:
- Web frontend/fullstack: `assets/claude-md-template-web.md`
- API/Backend service: `assets/claude-md-template-api.md`
- Python project: `assets/claude-md-template-python.md`
- Other: use `references/claude-md-guide.md` core template as starting point

**Step 2: Customize** with actual project details:
1. Replace all placeholders with real project name, tech stack, commands
2. Document actual project structure (verify with `ls`)
3. Verify all commands work (run them to confirm)
4. Add project-specific architecture decisions
5. Keep total content under 5000 tokens (critical for performance)

**Step 3: Create the file** at project root as `CLAUDE.md`.

### Phase 3: Rule Splitting (For Large Projects)

When CLAUDE.md exceeds 200 lines, split rules into `.claude/rules/` directory. Claude loads all files in this directory automatically at startup.

Recommended split structure:
```
.claude/rules/
├── coding-standards.md    # Code style, naming, formatting
├── testing-rules.md       # TDD flow, coverage requirements
├── security-policy.md     # Key management, input validation, XSS protection
├── git-workflow.md        # Branch strategy, commit format
├── performance.md         # Lazy loading, caching strategy
└── agents.md              # When to delegate to subagents
```

**Key advanced technique**: Use `paths:` frontmatter to scope rules to specific directories:
```yaml
---
paths:
  - "src/api/**"
---
API routes must use Zod to validate all input parameters.
All endpoints must return unified { data, error, message } structure.
```

This ensures API rules only apply when Claude works on API code, not when editing frontend components.

### Phase 4: Permission & Security Configuration

Create `.claude/settings.json` using `assets/settings-template.json` as base. Refer to `references/advanced-features.md` for security details.

Three-tier permission strategy:
- **Low risk → allow**: npm test, git status, read files
- **Side effects → ask (default)**: deploy, data migration, send messages
- **High risk → deny**: git push --force, rm -rf, curl | bash, ssh, scp, read ~/.ssh/, read ~/.aws/

Customize rules:
1. Adjust `allow.Bash` to match the project's actual commands
2. Set `allow.Write` to cover source and test directories only
3. Ensure `deny.Write` protects generated files, dependencies, and secrets
4. Ensure `deny.Read` protects credentials and sensitive config files
5. Add project-specific dangerous commands to `deny.Bash`

Security principles:
- deny rules always take priority over allow rules
- Start restrictive, loosen as needed
- Never allow write access to `.env*` files
- Never use `--auto-approve` for new projects

### Phase 5: Hooks Setup

Configure hooks in `.claude/settings.json` for automated quality enforcement. Hooks are 100% reliable vs ~80% compliance rate for CLAUDE.md instructions alone.

**"把'希望 Claude 记住'变成'系统层面强制执行'。"**

**Essential hooks to configure:**

1. **Block sensitive file writes (PreToolUse)** — exit code 2 blocks the operation:
   - Prevent writes to `.env`, `.key`, `.pem` files
   - Prevent `rm -rf` on critical directories

2. **Auto-format after file changes (PostToolUse)**:
   - JavaScript/TypeScript: `npx prettier --write $CLAUDE_FILE_PATH`
   - Python: `ruff format $CLAUDE_FILE_PATH`
   - Rust: `rustfmt $CLAUDE_FILE_PATH`
   - Go: `gofmt -w $CLAUDE_FILE_PATH`

3. **Smart task completion check (Stop)** — use prompt-type hooks with a small model (Haiku) to verify tasks are truly complete before Claude stops. If not complete, force continue.

**Memory persistence hooks (持续学习系统):**

4. **Session state loading (SessionStart)** — load previous session's key findings from `.claude/memory/session-state.md`

5. **Pre-compression state save (PreCompact)** — save critical state before context compression to prevent information loss

6. **Session learning save (Stop)** — use Haiku to summarize key findings, architecture decisions, problems and solutions, then append to `.claude/memory/session-state.md`

Create `.claude/memory/` directory during initialization to support the learning system.

Hook exit codes: 0 = pass, 1 = warn but continue, 2 = block operation.

All 7 hook events: PreToolUse, PostToolUse, UserPromptSubmit, Stop, SessionStart, PreCompact, Notification. See `references/advanced-features.md` for complete reference.

### Phase 6: Skills Setup (Optional)

Create project-specific skills in `.claude/skills/` for repeated workflows:

```
.claude/skills/
├── code-review/
│   └── SKILL.md       # /code-review triggers full code review
├── verify/
│   └── SKILL.md       # /verify runs six-stage verification
└── tdd-workflow/
    └── SKILL.md       # /tdd triggers test-driven development
```

Consider the hackathon champion's key skills:
- **/verify** — Six-stage verification: build → type check → lint → tests → security scan → diff review
- **/search-first** — Search npm/PyPI/GitHub before writing code
- **/code-review** — Comprehensive review with severity levels (CRITICAL/WARNING/INFO)
- **/cost-aware-llm-pipeline** — Route tasks to appropriate model, with budget tracking

Use `context: fork` for analysis-heavy skills to avoid polluting the main context window.

### Phase 7: MCP & Plugins Integration (Optional)

Create `.mcp.json` at project root only for services the project actively needs.

**MCP vs CLI selection rule**: If a CLI tool already does the job well (e.g., `gh` for GitHub, `vercel` for deploy), use CLI + Skill instead of MCP. MCP tool descriptions consume context tokens.

| Scenario | Recommended | Reason |
|----------|-------------|--------|
| GitHub ops | `gh` CLI + Skill | gh CLI sufficient |
| Database query | MCP | Needs persistent connection |
| Slack/Email | MCP | Saves context switching |
| Search engines | MCP (Tavily etc) | Real-time results needed |
| Deploy | CLI + Skill | CLI sufficient, saves context |
| Browser control | MCP | Needs real-time interaction |

**MCP cost awareness**: With 20 MCP + 100+ tools, descriptions alone can consume 30-40% of 200K context.

Guidelines:
- Configure no more than 20-30 MCP servers total
- Keep simultaneously enabled servers under 10
- Active tools under 80
- Use `disabledMcpServers` per-project to disable unneeded ones
- Consider replacing simple MCP with Skills + CLI (saves ~50% tokens)

**Plugins**: Consider installing LSP plugins for real-time code intelligence:
```bash
claude plugin install typescript-lsp --scope project  # TypeScript
claude plugin install pyright-lsp --scope project      # Python
claude plugin install rust-lsp --scope project         # Rust
```
LSP is the most valuable Plugin type — provides immediate type error feedback, significantly reducing edit→build→error→fix cycles.

### Phase 8: Verification

After setup, verify everything works:

1. Run `/context` to check token usage is reasonable
2. Run a test command to verify permissions work
3. Run `/memory` to review CLAUDE.md content
4. Edit a file to confirm hooks trigger correctly (auto-format, sensitive file blocking)
5. Run `/compact "init complete"` to start fresh

## Quick Reference

### Key Commands & Shortcuts
- `/context` — Check token usage
- `/compact "summary"` — Compress history preserving key info
- `/clear` — Clear conversation for new tasks
- `/model` — Switch between Sonnet/Opus/Haiku
- `/memory` — Edit CLAUDE.md
- `/cost` — Check current spend
- `/statusline` — Configure status bar
- `/rename "name"` — Name the session for later resume
- `Shift+Tab` / `Alt+M` — Toggle Plan Mode
- `Cmd+T` / `Alt+T` — Toggle extended thinking
- `Cmd+P` / `Alt+P` — Quick model switch
- `Esc+Esc` — Undo to last checkpoint (code + conversation)
- `Ctrl+G` — Edit plan in external editor

### Token Optimization
- Use `@file.ts` instead of `@dir/` (saves 30-50%)
- Run `/compact` periodically (saves 40-60%)
- Use subagents for parallel tasks (saves 50-70%)
- Replace simple MCP with CLI + Skills (saves ~50%)
- Daily: Sonnet; complex/5+ files/architecture: Opus; simple queries: Haiku

### Model Cost Reference
| Model | Relative Cost |
|-------|--------------|
| Haiku 4.5 | 1x (baseline) |
| Sonnet 4.6 | ~4x |
| Opus 4.5 | ~19x |

### Plan Mode
Enter Plan Mode for any task involving 3+ files. Workflow: Explore → Plan → Confirm → Execute. Separates thinking from doing.

### Headless Mode (CI/CD)
```bash
claude -p "task" --allowedTools "Read,Edit,Bash" --output-format json
claude -p "task" --max-budget-usd 2           # Budget limit
claude -p "task" --output-format stream-json   # Streaming
cat error.log | claude -p "analyze errors"     # Unix pipes
```

### Multi-Agent Architecture
- Custom agents in `.claude/agents/` with custom model, tools, and instructions
- `claude --worktree feature-name` for parallel branch development
- Pipeline: RESEARCH → PLAN → IMPLEMENT → REVIEW → VERIFY (use `/clear` between stages)
- Cascade Method: Sonnet first → fail → Opus → fail → human intervention
- Agent Teams (experimental): `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS="1"`

### Prompt Template
```
目标: [1-2 sentences: what you want]
范围: [which files, or "don't touch X"]
约束: [language/framework/style/performance requirements]
验证: [what command to run to verify]
交付物: [change list + key diff]
```

## Resources

### references/
- `claude-md-guide.md` — Complete CLAUDE.md memory system guide: six-layer architecture, creation methods (5 ways including @import and # quick memory), self-evolving CLAUDE.md, core templates, rule splitting with path-scoped frontmatter, dynamic context injection with role modes, and best practices
- `advanced-features.md` — Comprehensive reference: context engineering (token optimization, strategic compression timing, session management), Plan Mode (EDD, six-stage verification), Skills (advanced config, champion's recommendations), all 7 Hook events with exit codes and 7 practical examples (including memory persistence system), multi-agent architecture (custom agents, iterative retrieval, pipeline orchestration, Git Worktree, two-instance startup, Agent Teams, Cascade Method), headless mode (batch processing, Unix pipes, Interview Me, llms.txt), MCP/Plugins/LSP ecosystem, security engineering (5 attack surfaces, 3 real incidents, sandbox isolation, hidden text detection, AgentShield), and complete command cheatsheet

### assets/
- `claude-md-template-web.md` — CLAUDE.md template for web/fullstack projects (Next.js + TypeScript + Tailwind + Prisma)
- `claude-md-template-api.md` — CLAUDE.md template for API/backend services (Express/Fastify + TypeScript + PostgreSQL)
- `claude-md-template-python.md` — CLAUDE.md template for Python projects (FastAPI/Django + SQLAlchemy + uv)
- `settings-template.json` — Complete security permissions (allow/deny for Bash/Read/Write) and hooks configuration template (PreToolUse blocking, PostToolUse auto-format, SessionStart/PreCompact/Stop memory persistence system)
