# AI Knowledge Note Cards - AI 知识卡片

<div align="center">

[![License](https://img.shields.io/badge/license-apache2.0-blue.svg)](LICENSE)
[![基于 AstronAgent](https://img.shields.io/badge/基于-AstronAgent-blue.svg)](https://github.com/iflytek/astron-agent)

</div>

## 📻 项目简介

**AI Knowledge Note Cards（AI 知识卡片）** 是一个基于讯飞星辰Agent(AstronAgent)二次开发的 AI 知识卡片应用，通过智能工作流编排，将用户输入的学习笔记/工作记录转化成通俗易懂的知识卡片。

<img width="2810" height="1326" alt="image" src="https://github.com/user-attachments/assets/c69d0036-fe89-4d17-bcf0-ea87d2e8fdbf" />


### 技术特点

- ✅ 基于企业级开源项目 [AstronAgent](https://github.com/iflytek/astron-agent) 二次开发
- ✅ 微服务架构，支持高可用部署
- ✅ 支持接入主流兼容OpenAI格式大模型
- ✅ 支持扩展自定义卡片样式
- ✅ 完整的工作流可视化调试能力

## 🚀 快速开始

### 环境要求

- Docker 20.10+
- Docker Compose 2.0+
- 8GB+ 可用内存
- 20GB+ 可用磁盘空间

### 一键部署

```bash
# 1. 克隆项目
git clone https://github.com/fanan-uyun/Knowledge-Note-Cards.git
cd Knowledge-Note-Cards/docker/astronAgent

# 2. 复制环境变量配置
cp .env.example .env

# 3. 编辑环境变量（可选，使用默认配置即可快速体验）
vim .env

# 4. 启动所有服务
docker compose up -d

# 5. 查看服务状态
docker compose ps
```

### 访问应用

启动完成后，访问以下地址：

- **应用前端**：http://localhost
- **默认账户**：admin / 123

## 📖 使用指南

### 创建 AI 知识卡片工作流
1. 配置AI大模型（星火大模型、DeepSeek、Qwen等）
2. 进入「工作流」页面，创建新工作流，配置以下节点：
   - **开始节点**：定义用户输入
   - **大模型节点**：配置模型，设置笔记重构，结构化数据提取提示词
   - **结束节点**：输出内容，卡片Html模板，结构化数据变量填充上去即可
3. 保存并运行工作流
4. 输入文本，即可将笔记转化为知识卡片

**提示词示例**：
```
# 角色
你是一个学习科学专家，擅长将零散的信息组织成系统化的知识结构。


# 任务
> “请根据提供的学习笔记，重新组织梳理，生成一个结构化知识卡片，使其更容易理解和记忆，输出格式为JSON，包含以下字段：
>
> * `title`：核心主题
> * `explanation`：用清晰易懂的方式解释核心概念
> * `knowledgeType `：知识类型（枚举：概念定义 / 方法技巧 / 原理推导 / 案例分析 / 公式定理 / 其他）
> * `difficulty `：难度级别（枚举：入门 / 基础 / 进阶 / 高级）
> * `application `：应用场景（知识的实际用途）
> * `createTime `：创建时间（ISO 格式，如 "2025-11-22T10:30:00"）
> * `tag`：标签（用于分类检索）
> * `notes `：补充备注（个人理解 / 拓展思考）
>
> 请仅输出 JSON，不要添加任何额外说明或文本。”

### ✅ 示例输入：
```text
Python 推导式是一种独特的数据处理方式，可以从一个数据序列构建另一个新的数据序列的结构体。
也可以说推导式是从一个或多个迭代器快速简洁的创建数据结构的一种办法，它可以将循环和条件判断结合，从而可以避免语法冗长的代码。
列表推导式基本格式: [表达式 for 变量 in 列表] 或者 [表达式 for 变量 in 列表 if 条件]```
### ✅ 示例输出（JSON）：
```json
{
"title": "Python列表推导式语法与应用",
"explanation": "一种独特的数据处理方式，可以从一个数据序列构建另一个新的数据序列的结构体",
"knowledgeType": "方法技巧",
"difficulty": "基础",
"application": "数据处理、爬虫结果清洗、快速生成测试数据",
"createTime": "2025-11-22T14:25:30",
"tag": "Python",
"notes": "可以嵌套使用，比如二维列表转一维：[y for x in matrix for y in x]"
}```

输入：{{input}}
```

## 🛠️ 技术架构

### 核心服务

| 服务 | 说明 | 端口 |
|------|------|------|
| console-hub | 控制台后端服务 | 8080 |
| console-frontend | 前端界面 | 1881 |
| core-workflow | 工作流引擎 | 7880 |
| core-aitools | AI 工具服务（包含超拟人合成） | 18668 |
| nginx | 反向代理 | 80 |
| postgres | PostgreSQL 数据库 | 5432 |
| mysql | MySQL 数据库 | 3306 |
| redis | Redis 缓存 | 6379 |
| minio | 对象存储 | 18998/18999 |

### 数据库说明

- **PostgreSQL**：工作流数据、用户配置
- **MySQL**：工具元数据、智能体配置
  - `astron_console.tool_box`：工具注册表
  - `spark-link.tools_schema`：工具 Schema 定义

## 🔧 常见问题

### 工作流执行失败？

检查以下配置：

1. **工具版本号**：确保 `tools_schema` 表中工具版本为 `V1.0`
2. **服务地址**：超拟人合成服务地址应为 `http://core-aitools:18668`
3. **app_id**：确保工具的 `app_id` 与工作流一致

手动修复命令：

```bash
# 修复工具版本号和服务地址
docker compose exec mysql mysql -uroot -proot123 spark-link -e "
UPDATE tools_schema 
SET version='V1.0', 
    app_id='680ab54f',
    open_api_schema = REPLACE(open_api_schema, 'https://core-aitools:18669', 'http://core-aitools:18668')
WHERE tool_id='tool@8b2262bef821000';"

# 重启相关服务
docker compose restart core-link core-workflow
```

### 容器重启后出现 502 错误？

这是因为容器 IP 地址变化导致 Nginx 无法连接到 console-hub。解决方法：

```bash
docker compose restart nginx
```

### 如何查看服务日志？

```bash
# 查看所有服务日志
docker compose logs -f

# 查看特定服务日志
docker compose logs -f console-hub
docker compose logs -f core-workflow
```

## 🤝 贡献指南

本项目基于 [AstronAgent](https://github.com/iflytek/astron-agent) 二次开发。

如果您有任何建议或发现问题，欢迎提交 Issue 或 Pull Request。

## 📄 开源协议

本项目基于 Apache 2.0 协议开源，可自由商业使用。

## 🙏 致谢

- [讯飞 AstronAgent](https://github.com/iflytek/astron-agent) - 提供强大的智能体开发平台
- [讯飞星火](https://www.xfyun.cn) - 提供大模型和语音合成能力
- [DeepSeek](https://www.deepseek.com) - 提供高性能中文大模型

---

**AI Knowledge Note Cards** - 📚
