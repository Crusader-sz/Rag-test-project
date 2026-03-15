# 👕 RAG 服装客服问答系统

基于 LangChain 和 Chroma 向量数据库的智能客服系统，支持多轮对话、知识库管理和流式回答。

![Python](https://img.shields.io/badge/Python-3.12+-blue.svg)
![License](https://img.shields.io/badge/License-MIT-green.svg)
![Streamlit](https://img.shields.io/badge/Streamlit-1.28.0-red.svg)
![LangChain](https://img.shields.io/badge/LangChain-1.2.9-orange.svg)

## 📖 项目简介

本项目是一个基于检索增强生成（RAG, Retrieval-Augmented Generation）技术的智能客服系统，专门针对服装行业的售前咨询服务。系统能够根据用户上传的知识库文档，结合大语言模型（LLM）的能力，为用户提供专业、准确的服装尺码推荐、洗涤养护建议和颜色选择指导。

### 核心特性

- ✅ **知识库管理**：支持 TXT 格式文档上传，自动进行文本分割和向量化存储
- ✅ **智能检索**：基于 Chroma 向量数据库，实现高效的语义检索
- ✅ **多轮对话**：支持上下文理解，能够记住历史对话内容
- ✅ **流式输出**：实时显示模型回答，提升用户体验
- ✅ **会话管理**：支持多会话切换、新建和删除，历史记录持久化保存
- ✅ **MD5 去重**：智能检测重复内容，避免知识库冗余

## 🏗️ 系统架构

```
┌─────────────────┐      ┌──────────────────┐      ┌─────────────────┐
│  Streamlit UI   │─────▶│   RAG Service    │─────▶│  ChatTongyi     │
│  (前端界面)      │      │  (业务逻辑层)     │      │  (大语言模型)    │
└─────────────────┘      └──────────────────┘      └─────────────────┘
         │                       │
         ▼                       ▼
┌─────────────────┐      ┌──────────────────┐
│ File Uploader   │      │ Vector Store     │
│ (知识库上传)     │      │ (Chroma DB)      │
└─────────────────┘      └──────────────────┘
```

## 🚀 快速开始

### 环境要求

- Python 3.12+
- 阿里云 DashScope API Key（用于访问通义千问模型和 Embedding 服务）

### 安装步骤

1. **克隆项目**
   ```bash
   git clone <your-repo-url>
   cd RAG
   ```

2. **创建虚拟环境**（推荐）
   ```bash
   python -m venv .venv
   # Windows
   .venv\Scripts\activate
   # macOS/Linux
   source .venv/bin/activate
   ```

3. **安装依赖**
   ```bash
   pip install -r requirements.txt
   ```

4. **配置环境变量**
   
   复制 `.env.example` 为 `.env`，并填写你的 API Key：
   ```bash
   cp .env.example .env
   ```
   
   编辑 `.env` 文件：
   ```env
   # 阿里云 DashScope API Key
   DASHSCOPE_API_KEY=your_dashscope_api_key_here
   ```

### 运行应用

#### 1. 启动知识库上传服务

用于上传和管理知识库文档：

```bash
streamlit run app_file_uploader.py
```

打开浏览器访问 `http://localhost:8501`，上传 TXT 格式的服装知识文档。

#### 2. 启动问答聊天服务

用于与用户进行智能对话：

```bash
streamlit run app_qa.py
```

打开浏览器访问 `http://localhost:8502`，开始体验智能客服功能。

## 📁 项目结构

```
RAG/
├── app_file_uploader.py       # 知识库上传 Web 界面
├── app_qa.py                  # 问答聊天 Web 界面
├── rag.py                     # RAG 核心业务逻辑
├── knowledge_base.py          # 知识库管理服务
├── vector_stores.py           # 向量数据库服务
├── file_history_store.py      # 会话历史存储
├── config_data.py             # 配置文件
├── requirements.txt           # Python 依赖包
├── .env.example              # 环境变量示例
├── data/                      # 示例数据目录
│   ├── 尺码推荐.txt
│   ├── 洗涤养护.txt
│   └── 颜色选择.txt
├── chroma_db/                 # Chroma 向量数据库存储目录
├── chat_history/              # 会话历史记录存储目录
└── md5.text                   # 已处理文档的 MD5 校验记录
```

## 🔧 核心组件说明

### 1. RAG 服务 ([rag.py](file://F:\project\RAG\rag.py))

核心问答引擎，负责：
- 构建 LangChain 执行链
- 从向量库检索相关文档
- 组装 Prompt 并调用大模型
- 处理多轮对话历史

### 2. 知识库服务 ([knowledge_base.py](file://F:\project\RAG\knowledge_base.py))

文档管理模块，提供：
- 文件上传和解析
- MD5 去重检测
- 文本分割（RecursiveCharacterTextSplitter）
- 向量化存储

### 3. 向量存储服务 ([vector_stores.py](file://F:\project\RAG\vector_stores.py))

封装 Chroma 向量数据库：
- 文档嵌入（使用 DashScope Embedding）
- 语义检索
- 持久化存储

### 4. 会话历史存储 ([file_history_store.py](file://F:\project\RAG\file_history_store.py))

基于文件的会话持久化：
- JSON 格式存储
- 按 session_id 分离会话
- 支持增删改查操作

## ⚙️ 配置说明

主要配置项位于 [config_data.py](file://F:\project\RAG\config_data.py)：

```python
# 向量数据库配置
collection_name = "rag"              # 集合名称
persist_directory = "./chroma_db"    # 持久化目录

# 文本分割配置
chunk_size = 1000                    # 文本块最大长度
chunk_overlap = 100                  # 文本块重叠字符数

# 检索配置
similarity_threshold = 2             # 返回最相关的 K 个文档

# 模型配置
embedding_model_name = "text-embedding-v4"  # Embedding 模型
chat_model_name = "qwen3-max-2026-01-23"    # 聊天模型
```

## 💡 使用示例

### 上传知识库文档

1. 访问 `http://localhost:8501`
2. 点击"选择文件"上传 TXT 文档
3. 预览文件内容后点击"上传到知识库"
4. 系统会自动进行文本分割和向量化存储

### 进行智能对话

1. 访问 `http://localhost:8502`
2. 在侧边栏可以管理会话（新建/切换/删除）
3. 在底部输入框提问，例如：
   - "我身高 180cm，体重 70kg，应该穿什么尺码？"
   - "这件衣服如何洗涤保养？"
   - "黑色和白色哪个更适合我？"
4. 系统会基于知识库内容给出专业回答

## 🔍 技术栈

| 组件 | 技术 | 版本                |
|------|------|-------------------|
| **编程语言** | Python | 3.12+             |
| **Web 框架** | Streamlit | >=1.28.0          |
| **RAG 框架** | LangChain | 1.2.9             |
| **向量数据库** | Chroma | 1.4.1             |
| **Embedding** | DashScope | text-embedding-v4 |
| **LLM** | ChatTongyi | qwen3-max         |
| **依赖管理** | python-dotenv | >=1.0.0           |

## 📊 性能优化建议

1. **调整文本分割参数**
   - 根据文档类型调整 `chunk_size` 和 `chunk_overlap`
   - 较长的技术文档可适当增大 chunk_size

2. **优化检索效果**
   - 调整 `similarity_threshold` 控制返回文档数量
   - 增加相关知识领域的文档可以提升回答质量

3. **会话管理**
   - 定期清理无用的会话文件
   - 使用有意义的 session_id 便于管理

## 🛠️ 常见问题

### Q: 为什么检索不到相关内容？

A: 可能原因：
- 知识库中还没有上传相关文档
- 查询词与文档内容的语义匹配度不高
- 可以尝试调整 `similarity_threshold` 参数

### Q: 如何更换 API Key？

A: 修改 `.env` 文件中的 `DASHSCOPE_API_KEY` 值，然后重启应用即可。

### Q: 会话历史保存在哪里？

A: 会话历史以 JSON 格式保存在 `./chat_history/` 目录下，每个会话一个文件，session_id 即为文件名。

### Q: 支持哪些文件格式？

A: 当前版本仅支持 UTF-8 编码的 TXT 文件。如需支持其他格式（PDF、Word 等），可以在 `app_file_uploader.py` 中扩展。

## 📝 开发计划

- [ ] 支持更多文件格式（PDF、DOCX、Markdown）
- [ ] 添加文档管理界面（查看/删除已上传文档）
- [ ] 支持多路召回（混合检索）
- [ ] 添加回答引用来源标注
- [ ] 支持本地部署的开源 LLM（如 Ollama）
- [ ] 添加用户认证和权限管理

## 🤝 贡献指南

欢迎提交 Issue 和 Pull Request！

1. Fork 本仓库
2. 创建你的特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交你的修改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 开启一个 Pull Request

## 📄 许可证

本项目采用 MIT 许可证 - 查看 [LICENSE](LICENSE) 文件了解详情。

## 🙏 致谢

- [LangChain](https://github.com/langchain-ai/langchain) - 强大的 LLM 应用开发框架
- [Chroma](https://github.com/chroma-core/chroma) - 轻量级向量数据库
- [Streamlit](https://github.com/streamlit/streamlit) - 快速构建 Web 应用
- [DashScope](https://help.aliyun.com/zh/dashscope/) - 阿里云通义千问 API

## 📧 联系方式

如有问题或建议，请通过以下方式联系：

- Email: your-email@example.com
- Issues: [GitHub Issues](https://github.com/your-username/RAG/issues)

---

**Made with ❤️ by Crusader**