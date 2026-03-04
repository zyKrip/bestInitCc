# [项目名称]

## 项目概述
- 技术栈: Node.js + Express/Fastify + TypeScript
- 数据库: PostgreSQL + Redis
- ORM: Drizzle/Prisma
- 包管理器: pnpm

## 项目结构
```
src/
├── routes/           # API 路由定义
├── controllers/      # 请求处理逻辑
├── services/         # 业务逻辑层
├── models/           # 数据模型
├── middleware/        # 中间件
├── utils/            # 工具函数
├── types/            # TypeScript 类型
├── config/           # 配置文件
└── __tests__/        # 测试文件
```

## 常用命令
- 开发: `pnpm dev`
- 构建: `pnpm build`
- 启动: `pnpm start`
- 测试: `pnpm test`
- 测试覆盖率: `pnpm test:coverage`
- Lint: `pnpm lint`
- 数据库迁移: `pnpm db:migrate`

## 代码规范
- TypeScript strict mode
- 函数/变量: camelCase
- 类/接口: PascalCase
- 常量: UPPER_SNAKE_CASE
- 文件: kebab-case
- API 路由: RESTful, 复数名词 (/api/v1/users)
- 错误处理: 统一错误中间件
- 响应格式: { success, data, error, meta }

## API 约定
- 认证: Bearer Token (JWT)
- 版本: URL路径 (/api/v1/)
- 分页: ?page=1&limit=20
- 排序: ?sort=createdAt&order=desc
- 输入验证: Zod schemas
- 错误码: HTTP 标准状态码

## 测试策略
- 单元测试: Vitest
- 集成测试: Supertest
- 测试数据库: 独立测试库

## 重要注意
- 不修改: node_modules/, dist/, .git/
- 敏感配置通过环境变量注入
- 所有 API 端点需要输入验证
- 数据库查询使用参数化，防止 SQL 注入
