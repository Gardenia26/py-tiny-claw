# py-tiny-claw

AI Agent 工具注册与极简工具集实验项目，使用 Python 3.10+ 实现动态 Tool Registry 和 read、write、edit、bash 四种工具。

## 环境

- Python 3.10+
- Git Bash（Windows 上用于 `bash` 工具）
- 联网运行模型入口时需要 DeepSeek API Key；离线测试不需要密钥，也不消耗 token。

## 在 VS Code 中运行

1. 使用 VS Code 打开本项目目录。
2. 在集成终端执行 `python -m pip install -r requirements.txt`。
3. 运行离线验证：`python test_tools.py`。五个用例均通过后再考虑联网任务。
4. 如需运行模型任务，在 PowerShell 当前终端设置 `$env:DEEPSEEK_API_KEY="你的密钥"`，然后运行 `python -m cmd.claw.main`。

密钥只从环境变量读取。不要将密钥写入代码、提交记录或 `.env` 文件；`.gitignore` 已忽略 `.env` 和 `workspace/`。

## 项目结构

```text
cmd/claw/main.py          联网综合任务入口
internal/engine/loop.py   Agent 主循环
internal/provider/        模型接口与 OpenAI 兼容 Provider
internal/schema/          消息和工具调用数据结构
internal/tools/           Registry 与 read/write/edit/bash 工具
test_tools.py             离线验证（不调用模型）
```

## 工具边界

- `read_file`、`write_file`、`edit_file` 将文件路径映射到工具工作目录。
- `bash` 使用工作目录作为当前目录，30 秒超时并限制返回输出长度。
- `edit_file` 逐级放宽格式匹配，但多处命中时拒绝修改。

> 注意：bash 可以执行任意命令；工作目录作为当前目录并不构成操作系统级沙箱。仅在可信环境中运行，并避免对不可信模型暴露敏感权限。
