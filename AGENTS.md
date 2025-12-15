# 仓库指南（Repository Guidelines）

## 项目结构与模块组织
- `trendradar/`：TrendRadar 爬虫与通知主程序包（入口：`python -m trendradar`，见 `trendradar/__main__.py`）。读取 `config/config.yaml`，每日结果写入 `output/`。
- `mcp_server/`：FastMCP 服务端，提供 TrendRadar 数据与工具（`server.py`、`tools/`、`services/`、`utils/`）。
- `config/`：用户配置目录（`config.yaml`、`frequency_words.txt`）。
- `docker/`：生产部署相关 Docker 文件（`Dockerfile`、`Dockerfile.mcp`、`docker-compose.yml`、`manage.py`）。
- `_image/` 与 `index.html`：静态资源和简易网页查看器。
- `output/`：生成的报告/缓存目录，视为构建产物，不要手动修改。

## 构建、测试与开发命令
- 安装前置并用 uv 创建 `.venv`：
  - Mac/Linux：`./setup-mac.sh`
  - Windows：`setup-windows.bat`
- 同步依赖：`uv sync`。
- 本地运行爬虫：`uv run python -m trendradar`（使用 `config/config.yaml`）。
- 启动 MCP 服务（stdio，供 MCP 客户端使用）：`uv run python -m mcp_server.server --transport stdio`。
- 以 HTTP 模式启动 MCP 服务：`./start-http.sh`（监听 `http://localhost:3333/mcp`）。
- Docker（接近生产环境）：`docker compose -f docker/docker-compose.yml up -d`，启动 `trend-radar` 与 `trend-radar-mcp`。

## 代码风格与命名约定
- Python ≥3.10，4 空格缩进，UTF‑8 编码。遵循 PEP 8，修改尽量保持局部且最小化。
- 命名：函数/变量用 `snake_case`，类用 `PascalCase`，常量用 `UPPER_SNAKE_CASE`（参考 `trendradar/__main__.py`）。
- 鼓励使用类型注解，尤其是在 `mcp_server/` 内。
- CI 未强制格式化/静态检查；如需重排格式，请保持一致且仅限相关代码。

## 测试指南
- 当前暂无自动化测试套件。请通过运行爬虫和/或 MCP 服务进行手动验证，并检查日志及 `output/` 的生成结果。
- 如新增测试，建议使用 `pytest` 并建立 `tests/` 目录，文件命名为 `test_*.py`。

## 提交与 Pull Request 规范
- 尽量使用 Conventional Commits（`fix:`、`feat:`、`docs:`、`chore:`）。可中英双语，但首行需简短明确。
- 发布标签：爬虫用 `vX.Y.Z`，MCP 用 `mcp-vX.Y.Z`，会触发 Docker 构建流程。
- PR 需包含：清晰的变更说明、关联 issue、以及配置/环境变量变更说明；修改 `index.html` 或报告输出时请附截图。
- 禁止提交真实 Token/Webhook；用占位符，并在文档中说明必需的环境变量（`docker/.env`、`config/config.yaml`）。
