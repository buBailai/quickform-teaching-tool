# QuickForm 交互式教学工具创建 Skill

> 一句话，从零生成完整的课堂交互教学工具 + 数据可视化大屏。

**English Summary:** A Claude Code skill that helps teachers create interactive HTML teaching tools with QuickForm data collection, automated test data submission, and real-time data visualization dashboards — all in one conversation.

---

## 目录

- [简介](#简介)
- [效果展示](#效果展示)
- [前提条件](#前提条件)
- [安装方法](#安装方法)
- [使用方法](#使用方法)
- [完整工作流](#完整工作流)
- [输出文件说明](#输出文件说明)
- [支持的 QuickForm 版本](#支持的-quickform-版本)
- [技术特性](#技术特性)
- [常见问题](#常见问题)
- [关于 QuickForm](#关于-quickform)
- [贡献与反馈](#贡献与反馈)
- [License](#license)

---

## 简介

这是一个运行在 **Claude Code** 中的 Skill（技能插件），专为中小学教师设计。

只需通过自然语言对话描述你的需求，Skill 会自动完成以下全部工作：

1. **创建 QuickForm 数据采集任务**（官网版或本地部署版）
2. **生成交互式 HTML 教学工具页面**（闯关、测验、投票、问卷等多种形式）
3. **自动提交测试数据**验证接口是否正常
4. **生成数据可视化大屏 HTML**（ECharts 图表 + 实时刷新）
5. **输出完成报告**，告知接口地址和文件位置

整个过程通过多轮对话引导完成，无需任何编程知识。

---

## 效果展示

### 教学工具页面

- 深色科技风 UI，适配手机和电脑
- 闯关游戏式交互：关卡进度条、答题计时、答错重试、答对动效
- 完成后自动将数据提交到 QuickForm，显示通关结果卡片

### 数据可视化大屏

- 顶部实时统计：参与人数、通关人数、最快通关用时、平均答题次数
- 各关卡平均答题次数柱状图（绿/黄/红三色区分掌握程度）
- 通关排行榜（按用时排序，显示🥇🥈🥉奖牌）
- 每 5 秒自动刷新，适合课堂大屏投影展示

> 以下为同一次对话生成的实际文件截图（示例：四年级信息科技「身边的数据」单元）

```
教学工具页面：输入姓名班级 → 5关单选题闯关 → 通关结果卡片 → 自动提交数据
数据大屏：  实时参与统计 → 各关难度柱状图 → 通关排行榜（5秒刷新）
```

---

## 前提条件

### 必须有
- **[Claude Code](https://claude.ai/code)**（官方 CLI 工具）已安装并登录
- **[QuickForm](https://quickform.cn) 账号**（官网免费注册），或本地部署的 QuickForm 服务

### 可选
- 本地部署 QuickForm（使用本地版功能时需要）

---

## 安装方法

### 方法一：直接克隆（推荐）

```bash
# 1. 进入 Claude Code skills 目录
cd ~/.claude/skills

# 2. 克隆本仓库
git clone https://github.com/your-username/quickform-teaching-tool.git

# 3. 完成！重新启动 Claude Code 即可生效
```

### 方法二：手动下载

1. 下载本仓库的 ZIP 文件并解压
2. 将 `quickform-teaching-tool` 文件夹整体复制到 `~/.claude/skills/` 目录下
3. 重新启动 Claude Code

### 验证安装

启动 Claude Code 后，输入：

```
帮我做一个教学工具
```

如果 Claude 开始询问 QuickForm 版本，说明安装成功。

### Skills 目录位置

| 系统 | 路径 |
|------|------|
| macOS / Linux | `~/.claude/skills/` |
| Windows | `C:\Users\你的用户名\.claude\skills\` |

---

## 使用方法

### 快速开始

在 Claude Code 中，直接用自然语言触发即可：

```
帮我做一个教学工具
```

```
用 QuickForm 做一个课堂投票工具
```

```
帮我制作一个小学数学单元测验，要有数据大屏
```

### 触发词（以下任意表达均可触发）

- 帮我做教学工具
- 创建交互式教学页面
- 做一个课堂互动工具
- 用 QuickForm 做教学
- 制作学生互动表单页面
- 做数据采集教学工具
- 制作可视化教学页面

---

## 完整工作流

Skill 会通过**多轮对话**引导你完成以下步骤：

### 步骤 1：选择 QuickForm 版本

```
请问你要使用哪个版本的 QuickForm？
A. 官网版（https://quickform.cn）
B. 本地版（局域网部署版）
```

- **官网版**：首次使用需输入账号密码，之后自动记住（保存至 `~/.claude/quickform_credentials.json`）
- **本地版**：使用默认账号 `wst` / `quickform`，需确认服务已启动

### 步骤 2：设计风格与基本信息

提供 6 种风格选项：

| 编号 | 风格 | 适合场景 |
|------|------|----------|
| 1 | 🎨 简洁现代风 | 通用场景 |
| 2 | 🌈 渐变炫彩风 | 活跃课堂 |
| 3 | 🌙 深色暗黑风 | 科技感课程 |
| 4 | 🎠 儿童友好风 | 小学低年级 |
| 5 | 📊 学术严肃风 | 高中/大学 |
| 6 | 🖌️ 自定义 | 自由描述 |

同时询问适用年级和学科。

### 步骤 3：收集具体需求

三个必填维度：

**① 交互方式**：工具类型（测验/投票/实验记录等）、交互方式、是否需要即时反馈

**② 数据字段**：每个要采集的字段名称、含义、类型

**③ 可视化需求**：图表类型、统计维度、是否实时刷新

信息充分后，Skill 会输出**需求确认摘要**供你确认，确认后才开始开发。

### 步骤 4：自动创建 QuickForm 任务

调用 QuickForm CLI 接口，以教学工具主题为名创建任务，返回数据提交/获取接口地址。

### 步骤 5：生成教学工具 HTML

单文件 HTML，所有 CSS/JS 内联，使用国内可访问的 CDN（jsdelivr.net）。

### 步骤 6：提交 3 条测试数据

自动根据字段结构生成有差异的真实感测试数据并提交，验证接口联通性。

### 步骤 7：生成数据大屏 HTML

从获取接口读取真实数据结构，生成带 ECharts 图表的可视化大屏，支持实时刷新。

### 步骤 8：输出完成报告

告知生成的文件路径、接口地址和使用提示。

---

## 输出文件说明

每次运行会在**当前工作目录**生成两个 HTML 文件：

| 文件 | 用途 |
|------|------|
| `<主题>_教学工具.html` | 分发给学生使用的交互页面 |
| `<主题>_数据大屏.html` | 教师课堂投影的实时数据展示页面 |

两个文件均为**纯静态单文件**，无需服务器，直接用浏览器打开即可。

---

## 支持的 QuickForm 版本

### 官网版（https://quickform.cn）

使用 QuickForm CLI 接口创建任务：

```
POST https://quickform.cn/cli/add
{"username":"...", "password":"...", "task_name":"...", "description":"..."}
```

成功后返回 `apiid`，数据接口为：
- 提交：`https://quickform.cn/api/<apiid>`
- 获取：`https://quickform.cn/api/<apiid>/all`

> 免费账号有任务数量上限（目前为3个），如已满需删除旧任务后再创建。

### 本地版（自建部署）

QuickForm 本地版无 CLI 接口，Skill 使用 Python urllib + session 登录方式创建任务。

本地版地址因用户局域网环境而异，Skill 会按以下三步自动处理，**无需手动修改任何配置文件**：

1. **优先读取已保存地址**：若 `~/.claude/quickform_credentials.json` 中已有 `local.base_url`，直接使用
2. **自动检测本机 IP**：若无保存地址，自动获取本机局域网 IP 并尝试 `http://<本机IP>:5001`
3. **检测失败才手动输入**：若自动地址无法访问，才提示用户手动输入，输入后自动保存供下次使用

启动本地服务（以 QuickForm 1.5 为例，实际路径以你的部署位置为准）：

```bash
bash /path/to/QuickForm/start.sh
```

服务启动后，Skill 会**自动获取本机局域网地址**，无需任何手动配置：

1. 若 `~/.claude/quickform_credentials.json` 中已保存过地址，直接复用
2. 否则自动检测本机 IP，尝试 `http://<本机IP>:5001`
3. 自动检测失败时才提示你输入地址，输入后自动保存，下次无需再输

---

## 技术特性

### 前端技术选型（均为国内可访问 CDN）

| 库 | 用途 | CDN 来源 |
|----|------|----------|
| Tailwind CSS v4 | 样式框架 | jsdelivr.net |
| ECharts v5 | 数据可视化 | jsdelivr.net |
| Font Awesome v6 | 图标库 | jsdelivr.net |
| Alpine.js v3 | 轻量响应式（按需） | jsdelivr.net |

> ⚠️ 不使用 Google Fonts、Google CDN、unpkg.com 等在中国大陆访问不稳定的资源。

### 数据流

```
学生浏览器 → POST /api/<id> → QuickForm 服务器
大屏浏览器 ← GET /api/<id>/all ← QuickForm 服务器
```

### 账号凭据存储

官网账号密码保存在本机 `~/.claude/quickform_credentials.json`，仅本地存储，不上传任何服务器。

---

## 常见问题

**Q：触发了 Skill 但没有按步骤走，Claude 直接开始写代码？**

A：描述需求时加上"用 QuickForm"或"要有数据大屏"，可以更准确地触发本 Skill。

---

**Q：官网账号提示"已达任务数量上限"怎么办？**

A：登录 [quickform.cn](https://quickform.cn)，删除不再需要的旧任务后重试。免费账号目前限制3个任务。

---

**Q：本地版 QuickForm 地址怎么配置？**

A：Skill 首次使用本地版时会自动询问你的访问地址，输入后保存到 `~/.claude/quickform_credentials.json`，下次直接复用，无需重复输入，也无需修改任何代码文件。如需更换地址，删除该文件中的 `local.base_url` 字段即可触发重新询问。

---

**Q：本地版提交数据时报错 `{"error":"任务不存在"}` 怎么办？**

A：这是本地版的一个重要特性：**每个任务的 API 接口 ID 不是数字序号**（如 `/api/10`），而是系统随机生成的字符串（如 `/api/C9VaWGMQEZY`）。Skill 会自动在创建任务后从任务详情页解析出真实 API ID，但如果你是手动操作，需要登录后打开任务页面，在页面源码中找到 `/api/` 开头的链接提取真实 ID。

---

**Q：本地版大屏读取数据报错或数据解析异常怎么办？**

A：本地版接口返回的 `data` 字段是 Python dict 字符串格式（用单引号），而非标准 JSON。大屏 HTML 中需要用如下方式转换后再解析：
```javascript
function parsePyDict(str) {
  return JSON.parse(str.replace(/'/g, '"').replace(/\bNone\b/g,'null').replace(/\bTrue\b/g,'true').replace(/\bFalse\b/g,'false'));
}
```

---

**Q：生成的 HTML 页面 CDN 资源加载很慢/加载失败？**

A：所有 CDN 均来自 jsdelivr.net，在中国大陆通常可正常访问。若遇到问题，可以将 CDN 资源下载到本地后修改 HTML 中的引用路径。

---

**Q：如何修改题目内容？**

A：直接用文本编辑器（如 VS Code）打开生成的 HTML 文件，找到 `const QUESTIONS = [...]` 数组，按格式修改题目文字、选项和答案即可。

---

**Q：大屏可以在多个设备同时展示吗？**

A：可以。大屏 HTML 文件直接从 QuickForm 接口读取数据，在任意浏览器打开都能看到实时数据，不需要共享同一设备。

---

**Q：学生用手机也能完成闯关吗？**

A：可以。教学工具页面采用响应式设计，手机和平板均可正常使用。将 HTML 文件托管到任意 Web 服务器（或通过局域网共享）后，用手机扫码/输入地址即可访问。

---

## 关于 QuickForm

QuickForm 是由**温州科技高级中学 AI 科创中心**联合温州大学开发的智能表单管理服务，专为 AI 赋能教学场景设计。教师借助 AI 大模型生成交互网页后，可以通过 QuickForm 提供的 API 接口回收和分析数据。

- 官网：[https://quickform.cn](https://quickform.cn)
- 开源地址：[https://gitee.com/wstlab/quickform](https://gitee.com/wstlab/quickform)
- 系统演示：[https://quickform.cn](https://quickform.cn)

---

## 贡献与反馈

欢迎提交 Issue 和 Pull Request！

**常见改进方向：**
- 增加更多教学工具类型模板（实验记录、作品上传、小组讨论等）
- 增加更多可视化图表类型（词云、热力图等）
- 支持多语言界面
- 支持更多 QuickForm 部署场景的地址配置

---

## License

MIT License

本项目以 MIT 协议开源，可自由使用、修改和分发。

---

*由厦门市演武小学信息中心与 Claude Code 共同构建*
