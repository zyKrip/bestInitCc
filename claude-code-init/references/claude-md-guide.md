# CLAUDE.md 记忆系统完整指南

## 概述

CLAUDE.md 是 Claude Code 启动时自动读取的记忆文件，可解决 90% 的使用问题。它是 Claude Code 作为"操作系统"的核心配置层。

## 六层记忆体系

| 层级 | 文件路径 | 作用 | 优先级 |
|------|---------|------|--------|
| 企业级 | 企业托管设置 | 全组织策略（合规、安全底线） | 最高 |
| 项目级 | `项目根目录/CLAUDE.md` | 团队共享规范（进 Git） | 高 |
| 目录级 | `src/子目录/CLAUDE.md` | 模块特定规则 | 中 |
| 用户级 | `~/.claude/CLAUDE.md` | 个人全局偏好（跨项目） | 中低 |
| 私有本地 | `CLAUDE.local.md` | 本地特殊配置（不进 Git） | 低 |
| 自动记忆 | `~/.claude/projects/...` | Claude 自动记录的项目特征 | 最低 |

规则冲突时，高层级覆盖低层级。

### 各层详解

**项目级（最常用）**：放在项目根目录，所有团队成员共享，提交到 Git。包含：技术栈说明、常用命令、代码规范、项目结构。超过 200 行时拆分到 `.claude/rules/`。

**用户级**：`~/.claude/CLAUDE.md`，跨所有项目生效。适合个人偏好，如"回复用简体中文"、"使用 2 空格缩进"、"Git commit message 用英文"。

**私有本地**：`CLAUDE.local.md`，不进 Git。适合本地特殊配置，如"我的数据库在 localhost:5433"。

**自动记忆层**：Claude 会在 `~/.claude/projects/` 对应的 memory 目录自动记笔记——项目构建命令、代码风格偏好、它自己发现的项目特征。跨会话自动加载，可手动查看和编辑。

## 创建方法（5 种）

### 方法一：自动生成
```bash
claude
# 在对话中说："帮我生成 CLAUDE.md"
```

### 方法二：编辑器打开
```bash
claude /memory
# 或在对话中输入 /memory
```

### 方法三：快速追加
```
# 在对话中直接说
"Update CLAUDE.md: always use bun instead of npm"
```

### 方法四：@import 语法
在对话中使用 `@` 前缀引用文件，Claude 会自动加载到上下文：
```
@README.md         # 加载指定文件
@src/lib/          # 加载整个目录（注意：目录加载消耗更多 token）
```
建议用 `@file.ts` 替代 `@dir/`（节省 30-50% token）。

### 方法五：# 前缀快速记忆
在对话中用 `#` 前缀可以立即追加规则到 CLAUDE.md：
```
# always use bun instead of npm
```
这会直接将规则写入 CLAUDE.md，无需手动编辑。

## 自进化 CLAUDE.md

让 Claude 在被纠正后自动更新规则，形成自学习机制：

当你纠正 Claude 的行为（如"不要用 var，用 const"），可以要求它同时更新 CLAUDE.md：
```
"以后都用 const 而非 var。把这条写入 CLAUDE.md。"
```

Claude 会自动在 CLAUDE.md 中添加规则，下次对话自动遵守。这是一种"纠正一次、永久生效"的机制。

## CLAUDE.md 核心模板

```markdown
# 项目名称

## 项目概述
- 技术栈: [框架] + [语言] + [ORM/数据库]
- 项目类型: [Web应用/CLI工具/库/API服务]
- 包管理器: [npm/bun/pnpm/yarn]

## 项目结构
src/
├── components/    # UI组件
├── lib/           # 工具函数
├── api/           # API路由
├── types/         # TypeScript类型
└── styles/        # 样式文件

## 常用命令
- 开发: `npm run dev`
- 构建: `npm run build`
- 测试: `npm test`
- Lint: `npm run lint`
- 格式化: `npm run format`

## 代码规范
- 使用 TypeScript strict mode
- 组件使用 PascalCase，函数使用 camelCase
- 文件使用 kebab-case
- 提交遵循 Conventional Commits 规范
- 禁止使用 any 类型

## 架构决策
- [日期] 选择 [技术A] 而非 [技术B]，因为 [原因]

## 重要注意事项
- 不要修改 node_modules、dist、.git 目录
- 环境变量在 .env 中，不要提交到版本控制
- 数据库迁移使用 [工具名] 管理
```

## 规则拆分最佳实践

### 何时拆分
当 CLAUDE.md 超过 200 行时，规则权重会被稀释，Claude 的遵从率下降。此时需要拆分。

### 拆分结构
```
.claude/rules/
├── coding-standards.md    # 编码规范：命名、格式、文件组织
├── testing-rules.md       # 测试规范：TDD 流程、覆盖率要求
├── security-policy.md     # 安全规则：密钥管理、输入校验、XSS 防护
├── git-workflow.md        # Git 工作流：分支策略、commit 格式
├── performance.md         # 性能要求：懒加载、缓存策略
└── agents.md              # Agent 配置：何时委派给子代理
```

每个文件都是独立的规则模块，Claude 启动时自动加载全部。

### 路径范围限定（关键进阶技巧）

使用 `paths:` frontmatter 限定规则生效范围，让规则更精准：

```yaml
---
paths:
  - "src/api/**"
---
API 路由必须使用 Zod 校验所有输入参数。
所有端点必须返回统一的 { data, error, message } 结构。
错误响应必须包含机器可读的 error code。
```

好处：规则不会干扰无关代码。Claude 处理前端组件时不会被 API 校验规则干扰，处理 API 时又能严格执行校验。

### 动态上下文注入

在 CLAUDE.md 中引用外部文档，Claude 会按需加载：
```markdown
## API 文档
详细 API 规范见 `docs/api-spec.md`，需要时请先阅读。
```

高级用法：通过 `--system-prompt` 注入不同角色上下文，同一项目支持多种工作模式：
```bash
# 开发模式
claude --system-prompt "你是高级开发者，专注于代码质量和测试覆盖率"

# 审查模式
claude --system-prompt "你是安全审计专家，专注于发现漏洞和性能问题"

# 调研模式
claude --system-prompt "你是技术调研员，专注于评估技术方案的可行性"
```

## 核心原则

1. **保持精简**: CLAUDE.md 控制在 5000 token 以内（超过严重影响性能）
2. **具体明确**: 用具体命令而非模糊描述（"运行 `npm test`" 而非 "跑测试"）
3. **及时更新**: 项目变化时同步更新
4. **分层管理**: 全局配置放用户级，项目配置放项目级
5. **避免冗余**: 不重复框架文档中已有的信息
6. **命令优先**: 让 Claude 知道怎么做，而非只知道是什么

## 常见错误

- 过于冗长（超过 5000 token 严重影响性能）
- 包含过时信息（命令已变但 CLAUDE.md 未更新）
- 与框架默认行为矛盾
- 缺少实际可执行的命令（只有描述没有命令）
- 重复已有框架文档内容（浪费 token）
