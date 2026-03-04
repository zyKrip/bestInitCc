# Claude Code Init Skill

> 基于 Anthropic 黑客松冠军最佳实践，一键初始化 Claude Code 项目配置。

## 简介

Claude Code Init 是一个 Claude Code Skill，将 Claude Code 从基础聊天工具转变为完整配置的开发操作系统。基于 Claude Code 终极指南和 Anthropic 黑客松冠军（Affaan Mustafa 50K+ star 仓库）的实战经验打造。

## 核心理念

**"Claude Code is not a chat tool — it is an operating system."** — Boris Cherny

CLAUDE.md 解决 90% 的使用问题，Hooks 在系统层强制执行规则（100% 可靠性 vs 仅靠 CLAUDE.md 的 ~80%）。

## 项目结构

```
claude-code-init/
├── SKILL.md                          # Skill 主文件（8 阶段工作流）
├── assets/                           # 项目模板
│   ├── claude-md-template-web.md     # Web/全栈模板 (Next.js + TS + Tailwind)
│   ├── claude-md-template-api.md     # 后端 API 模板 (Express/Fastify + PostgreSQL)
│   ├── claude-md-template-python.md  # Python 模板 (FastAPI/Django + SQLAlchemy)
│   └── settings-template.json        # 权限与 Hooks 配置模板
└── references/                       # 参考文档
    ├── claude-md-guide.md            # CLAUDE.md 六层记忆系统指南
    └── advanced-features.md          # 高级特性参考（多智能体、安全、Hooks 等）
```

## 8 阶段初始化工作流

| 阶段 | 内容 | 说明 |
|------|------|------|
| 1 | 项目分析 | 检测技术栈、目录结构、依赖、CI/CD |
| 2 | CLAUDE.md 记忆系统 | 创建项目上下文文件（技术栈、命令、规则） |
| 3 | 规则拆分 | 大型项目按路径拆分到 `.claude/rules/` |
| 4 | 权限与安全配置 | 创建 `.claude/settings.json`（允许/拒绝规则） |
| 5 | Hooks 自动化 | 配置 7 种 Hook 事件（格式化、类型检查等） |
| 6 | Skills 设置 | 创建 `/verify`、`/code-review` 等项目技能 |
| 7 | MCP & 插件集成 | 配置 Model Context Protocol 服务器 |
| 8 | 验证 | 校验所有配置是否正确生效 |

## 内置模板

- **Web/全栈**: Next.js 14 + TypeScript + Tailwind CSS + Prisma + PostgreSQL
- **后端 API**: Node.js + Express/Fastify + TypeScript + Drizzle/Prisma + Redis
- **Python**: Python 3.12+ + FastAPI/Django + SQLAlchemy + uv/poetry

## 核心特性

- **六层记忆体系**: Enterprise → Project → Directory → User → Local → Auto
- **三级权限策略**: 低风险 → 允许 | 有副作用 → 询问 | 高风险 → 拒绝
- **7 种 Hook 事件**: PreToolUse / PostToolUse / Stop / SessionStart / PreCompact / UserPromptSubmit / Notification
- **多智能体架构**: 子代理、Git Worktree、管道编排、级联方法
- **Token 优化**: 策略性压缩，节省 30-50% 上下文开销
- **安全加固**: 5 大攻击面防护，敏感文件保护，沙箱隔离

## 使用方式

在 Claude Code 中触发：

```
> init
> initialize
> setup claude code
> configure project
```

Skill 将引导你完成全部 8 个阶段，将项目转变为完整优化的 Claude Code 开发环境。

## 安装

将 `claude-code-init/` 目录复制到你的 Claude Code Skills 目录中，或在项目的 `.claude/settings.json` 中配置 skill 路径。

## 参考资料

- Claude Code 终极指南（上篇 + 下篇）
- Anthropic 官方 Claude Code 文档
- Affaan Mustafa 黑客松冠军仓库最佳实践

## License

MIT
