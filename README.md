# OpenManus-RAG

基于 [OpenManus](https://github.com/FoundationAgents/OpenManus) 改进的通用浏览器自动化智能体，专注于解决动态网页交互与复杂推理任务。

## 项目简介

本项目在 OpenManus 基础上进行了多项优化，使其更适合实际业务场景：

- **LLM 模型优化**：集成通义千问（Qwen-Max）作为主模型，支持 function calling
- **中文搜索增强**：默认使用百度搜索，优化中文场景下的搜索效果
- **安全沙箱集成**：集成 Daytona 沙箱环境，保障代码执行安全
- **反检测机制**：添加浏览器反检测参数，提升自动化成功率

## 技术架构

```
┌─────────────────────────────────────────────────────────┐
│                    Manus Agent                          │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐    │
│  │  Planner    │→ │  Executor   │→ │  Reviewer   │    │
│  └─────────────┘  └─────────────┘  └─────────────┘    │
└──────────────────────────┬──────────────────────────────┘
                           │
        ┌──────────────────┼──────────────────┐
        ▼                  ▼                  ▼
┌───────────────┐  ┌───────────────┐  ┌───────────────┐
│ Browser Agent │  │ SWE Agent     │  │ Tool Agent    │
│ (Playwright)  │  │ (代码开发)    │  │ (MCP/搜索)    │
└───────────────┘  └───────────────┘  └───────────────┘
        │                                         
        ▼                                         
┌───────────────┐                          
│ Daytona       │ ← 安全沙箱执行 Python 代码
│ Sandbox       │                          
└───────────────┘                          
```

## 核心特性

### 1. ReAct 分层状态机
- **Planner**：任务规划与拆解
- **Executor**：执行具体操作
- **Reviewer**：结果审查与失败重试

### 2. Daytona 安全沙箱
- 网络隔离与资源限制
- 防止恶意代码执行
- 支持 Python 脚本安全运行

### 3. GUI-Plus 元素定位
- DOM 语义解析 + Vision 感知双通道
- 元素定位成功率从 60% 提升至 95%
- 解决动态网页、JS 控件定位难题

### 4. RAG 策略补充
- 构建轻量级知识库（城市标签、日期参数格式）
- 优先采用稳定路径绕过 UI 交互
- 进一步提升任务成功率

## 安装部署

### 环境要求
- Python 3.12+
- Playwright
- API Key（通义千问/DashScope）

### 安装步骤

```bash
# 1. 克隆项目
git clone https://github.com/ding-god/OpenManus-rag.git
cd OpenManus-rag

# 2. 创建虚拟环境
uv venv --python 3.12
source .venv/bin/activate  # Linux/macOS
# .venv\Scripts\activate   # Windows

# 3. 安装依赖
uv pip install -r requirements.txt

# 4. 安装浏览器
playwright install

# 5. 配置 API Key
export DASHSCOPE_API_KEY="your-api-key"
```

### 配置说明

编辑 `config/config.toml`：

```toml
[llm]
api_type = "openai"
model = "qwen-max"
base_url = "https://dashscope.aliyuncs.com/compatible-mode/v1"
api_key = "your-dashscope-api-key"  # 或通过环境变量 DASHSCOPE_API_KEY

[llm.vision]
model = "qwen-vl-plus"

[search]
engine = "baidu"  # 中文搜索推荐使用百度
lang = "zh"
country = "cn"

[daytona]
daytona_api_key = "your-daytona-api-key"
```

## 使用方法

### 命令行交互

```bash
python main.py
```

### Python API

```python
from app.agent.manus import Manus

agent = Manus()
result = agent.run("帮我查询今天北京的天气")
print(result)
```

## 项目结构

```
OpenManus-rag/
├── app/
│   ├── agent/           # Agent 实现
│   │   ├── browser.py   # 浏览器自动化 Agent
│   │   ├── manus.py     # 主 Agent
│   │   ├── mcp.py       # MCP 协议支持
│   │   └── react.py     # ReAct 模式
│   ├── tool/            # 工具集
│   │   ├── browser_use_tool.py
│   │   ├── search/      # 搜索工具
│   │   └── sandbox/    # 沙箱工具
│   └── sandbox/         # Daytona 沙箱
├── config/              # 配置文件
├── examples/            # 使用示例
└── logs/                # 运行日志
```

## 技术栈

- **语言**: Python
- **LLM**: 通义千问 (Qwen-Max/VL-Plus)
- **浏览器**: Playwright
- **框架**: LangChain, ReAct
- **沙箱**: Daytona
- **搜索**: Baidu, Google, DuckDuckGo

## 应用场景

- 机票/酒店预订自动化
- 跨平台价格比价
- 竞品分析数据采集
- 表单自动填写
- 网页内容监控

## 注意事项

1. 请遵守目标网站的服务条款
2. 敏感操作建议在沙箱环境中运行
3. 部分网站可能有反爬机制，需合理设置请求间隔

## License

MIT License
