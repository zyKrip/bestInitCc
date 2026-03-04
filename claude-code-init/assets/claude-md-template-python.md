# [项目名称]

## 项目概述
- 技术栈: Python 3.12+ + FastAPI/Django
- 数据库: PostgreSQL
- ORM: SQLAlchemy / Django ORM
- 包管理: uv / poetry
- 虚拟环境: venv

## 项目结构
```
src/
├── api/              # API 路由和端点
├── core/             # 核心配置和设置
├── models/           # 数据模型
├── schemas/          # Pydantic 验证模型
├── services/         # 业务逻辑
├── repositories/     # 数据访问层
├── utils/            # 工具函数
└── tests/            # 测试文件
    ├── unit/
    ├── integration/
    └── conftest.py
```

## 常用命令
- 开发: `uv run uvicorn src.main:app --reload`
- 测试: `uv run pytest`
- 测试覆盖率: `uv run pytest --cov`
- Lint: `uv run ruff check .`
- 格式化: `uv run ruff format .`
- 类型检查: `uv run mypy src/`
- 迁移创建: `uv run alembic revision --autogenerate`
- 迁移执行: `uv run alembic upgrade head`

## 代码规范
- 遵循 PEP 8
- 类型注解: 所有函数参数和返回值
- 文档字符串: Google style
- 变量/函数: snake_case
- 类: PascalCase
- 常量: UPPER_SNAKE_CASE
- 使用 Pydantic 进行数据验证
- 异步优先 (async/await)

## 重要注意
- 不修改: .venv/, __pycache__/, .git/
- 依赖管理使用 pyproject.toml
- 环境变量通过 .env 和 pydantic-settings
- 所有 API 端点使用 Pydantic schema 验证
