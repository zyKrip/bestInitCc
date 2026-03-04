# [项目名称]

## 项目概述
- 技术栈: Next.js 14 + TypeScript + Tailwind CSS + Prisma
- 数据库: PostgreSQL
- 包管理器: pnpm
- 部署: Vercel

## 项目结构
```
src/
├── app/              # Next.js App Router 页面
├── components/       # React 组件
│   ├── ui/           # 基础UI组件
│   └── features/     # 业务组件
├── lib/              # 工具函数和配置
├── hooks/            # 自定义 React Hooks
├── types/            # TypeScript 类型定义
├── styles/           # 全局样式
└── server/           # 服务端逻辑
    ├── api/          # API 路由处理
    ├── db/           # 数据库模型和查询
    └── services/     # 业务服务层
```

## 常用命令
- 开发: `pnpm dev`
- 构建: `pnpm build`
- 测试: `pnpm test`
- Lint: `pnpm lint`
- 格式化: `pnpm format`
- 数据库迁移: `pnpm db:migrate`
- 数据库生成: `pnpm db:generate`

## 代码规范
- TypeScript strict mode，禁止 any
- 组件: PascalCase (UserProfile.tsx)
- 函数/变量: camelCase
- 文件: kebab-case
- CSS类: Tailwind utility-first
- 提交: Conventional Commits (feat/fix/docs/refactor/test)
- 导入顺序: React > 第三方 > 内部模块 > 类型

## 架构决策
- 使用 App Router 而非 Pages Router，支持 RSC 和流式渲染
- Prisma 作为 ORM，类型安全的数据库访问
- Tailwind CSS 而非 CSS Modules，保持样式一致性

## 测试策略
- 单元测试: Vitest
- 组件测试: React Testing Library
- E2E: Playwright
- 测试文件与源码同目录: `*.test.ts(x)`

## 重要注意
- 不修改: node_modules/, .next/, .git/
- 环境变量在 .env.local，不提交版本控制
- API 路由使用 Zod 验证输入
- 所有数据库操作通过 Prisma Client
