<!-- Logo -->
<p align="center">
  <a href="https://tashan.ac.cn" target="_blank" rel="noopener noreferrer">
    <img src="docs/assets/tashan.svg" alt="他山 Logo" width="280" />
  </a>
</p>

<!-- 标题 -->
<p align="center">
  <strong>他山宣传写作体系</strong><br>
  <em>Tashan Content Writing System</em>
</p>

<!-- 快速导航 -->
<p align="center">
  <a href="#项目简介">项目简介</a> •
  <a href="#功能特性">功能特性</a> •
  <a href="#快速开始">快速开始</a> •
  <a href="#系统架构">系统架构</a> •
  <a href="#目录结构">目录结构</a> •
  <a href="#生态位置">生态位置</a> •
  <a href="#贡献">贡献</a> •
  <a href="README.en.md">English</a>
</p>

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

> 围绕「他山学科交叉」公众号与内容运营构建的 AI 写作工作流：支持公众号文章、通用长文、系统调研、配图生成与审稿的全流程闭环，运行于 Cursor AI Agent 之上。

---

## 项目简介

「人—智能体混合数字世界」的核心主张是：AI 不只是工具，而是与人协作完成认知外化、知识生产的共同体。内容写作是这一主张最直接的落地场景——每一篇文章，都是认知结构的对外投射。

本项目对应其中的**内容创作层**，负责解决「如何让 AI 高质量、可复现、符合人类审美地完成一篇从构思到发布的完整文章」这一问题。没有本项目，AI 写作停留在一次性对话生成，质量不稳定、风格不统一；有了本项目，写作工作流变成可调用、可迭代的标准化流水线。

**核心思想**

- **认知根先行**：每篇文章必须能追溯到认知结构（L1/L1.5 文档）中的某个观点，拒绝「漂浮写作」
- **流水线而非单次生成**：8 个 Stage 按序推进（研究 → 草稿 → 逻辑自检 → 真实性校对 → 配图 → 审稿 → 发布），每个 Stage 有明确的输入/输出和失败处理
- **三层质量保障**：内容自检（挑战者反思）+ 独立审稿（5 轮）+ 格式核查（15 项自动验证）

**适合以下场景与读者**

- 学术公众号运营者：用 AI 写高质量科普/科教文章，保持风格统一
- AI 工具研究者：参考一套完整的 Cursor Skill 编排方案，用于内容生产工作流
- 他山生态开发者：基于此体系扩展新的内容类型（如学术推广、活动回顾等）

---

## 功能特性

- **统一写作入口**：`document-pipeline` 是所有写作任务的核心，任意 Stage 均可入场——有草稿从审稿开始，只需配图从画图开始
- **公众号全流程**：从选题 → HTML 排版 → 底部模板 → 发布，完整覆盖微信公众号发布全流程
- **多视角配图**：每张图从 2-4 个独立逻辑视角生成，中英双语（qwen-image + DALL-E 3），并保留 Mermaid 骨架
- **内置审稿标准**：5 轮审稿（AI腔 / 标题4种错误 / 绝对表达 / 结构合理性 / 结语完整性），基于郑博元历次亲手批注整理
- **审稿意见追踪**：用户给出修改意见后，结构化记录并逐条落实，状态可追踪
- **风格库管理**：已验证的配图风格可沉淀为模板，后续写作直接调用，保持视觉一致性
- **公众号历史抓取**：`wechat-scraper` 子智能体支持批量抓取历史文章，供写作参考

---

## 快速开始

### 1. 克隆仓库

```bash
git clone https://github.com/TashanGKD/tashan-writing-system.git
cd tashan-writing-system
```

### 2. 安装到 Cursor 工作区

将文件按对应目录放入你的 Cursor 工作区根目录：

```bash
# Skills → .cursor/skills/
cp -r skills/* /你的工作区/.cursor/skills/

# Subagents → .cursor/agents/
cp subagents/* /你的工作区/.cursor/agents/

# Rules → .cursor/rules/
cp rules/* /你的工作区/.cursor/rules/

# 参考文档 → 建议放入知识库目录（路径可自定义）
cp -r docs/writing-guides/ /你的工作区/_内部总控/认知结构/L1_系统性文档/待建维度/
```

### 3. 配置 API Key

以下文件包含 API Key 占位符，需替换为你自己的 Key：

| 文件 | 占位符 | 对应服务 |
|---|---|---|
| `skills/ai-image-generator/SKILL.md` | `YOUR_DASHSCOPE_API_KEY` | [阿里云百炼](https://dashscope.aliyun.com)（qwen-image-2.0-pro / wan2.6-t2i）|
| `skills/ai-image-generator/SKILL.md` | `YOUR_DMXAPI_KEY` | [DMXAPI](https://www.dmxapi.cn)（DALL-E 3 代理，备用）|
| `skills/document-pipeline/SKILL.md` | `YOUR_DMXAPI_KEY` | 同上 |

```bash
# 批量替换（macOS/Linux）
sed -i '' 's/YOUR_DASHSCOPE_API_KEY/你的真实Key/g' skills/ai-image-generator/SKILL.md
sed -i '' 's/YOUR_DMXAPI_KEY/你的真实Key/g' skills/ai-image-generator/SKILL.md skills/document-pipeline/SKILL.md
```

### 4. 配置写作风格手册路径

所有 Skill 在 Step 0 会读取写作风格手册，默认路径为：

```
_内部总控/认知结构/L1_系统性文档/待建维度/写作习惯与风格手册.md
```

若你的工作区路径不同，在对应 Skill 的 `Step 0` 处修改 `Read:` 路径即可。

### 5. 开始写作

在 Cursor 对话框中，对 AI 说：

```
写一篇公众号文章，主题是「XXX」
```

AI 将自动加载 `wechat-article-writer` Skill，路由到 `document-pipeline`，按 Stage 0-8 推进。

---

## 系统架构

### Skill 调用关系

```
用户指令
    │
    ├─ 「写公众号文章」→ wechat-article-writer (html)   ─┐
    ├─ 「写通用长文」  → general-article-writer (md)     ├──→ document-pipeline (Stage 0-8)
    └─ 「调研并写文档」→ research-output (research)      ─┘
                                        │
                     ┌──────────────────┼──────────────────┐
                     ↓                  ↓                   ↓
           article-proofreading   ai-image-generator   Stage 8 格式转换
           (Stage 7 · 5轮审稿)   (Stage 5 · 多视角)   (HTML / MD / LaTeX)
                     ↑                  ↑
           article-review-tracker  article-image-angles
           (用户反馈追踪)          (配图视角分析)
                                        ↑
                                article-image-styles
                                (风格库管理)
```

### 写作流水线阶段

| Stage | 名称 | 说明 |
|---|---|---|
| 0 | 入场检测 | 判断目标格式（html/md/latex）、入场阶段、文档性质 |
| 1 | 研究 + 草稿 | 读取风格手册、网络搜索、写初稿、多主题逻辑确认 |
| 2 | 逻辑自检 | 以批判者视角扫描论点-论据完整性、概念一致性 |
| 3 | 真实性校对 | 核实来源，标注 `[待核实]`，不删除内容只标注 |
| 4 | 画图规划 | 扫描草稿，确定配图位置、类型（Mermaid/qwen-image）|
| 5 | 批量画图 | Mermaid 骨架 + 中文图 + 英文图，双语三元组规范 |
| 6 | 图片自审 | 图文一致性检查，严重不符重生成（最多 1 次）|
| 7 | 审稿 | 触发 `article-proofreading`，P0 直接修复，P1/P2 建议 |
| 8 | 格式转换 | HTML（微信）/ MD（知识库）/ LaTeX（学术）|

---

## 目录结构

```
tashan-writing-system/
├── README.md                           # 中文说明（本文件）
├── README.en.md                        # English version
├── LICENSE                             # MIT 许可证
├── CONTRIBUTING.md                     # 贡献指南
├── CHANGELOG.md                        # 更新日志
│
├── docs/
│   ├── assets/
│   │   └── tashan.svg                  # 他山 Logo
│   ├── writing-guides/                 # 写作规范文档
│   │   ├── 写作习惯与风格手册.md        # ← 所有 Skill 的「D0 认知根」，必读
│   │   ├── 微信公众号发布手册.md        # 排版规范、HTML样式、发布流程
│   │   ├── 全场景绘图指南.md            # 内容类型 → 图类型映射
│   │   ├── 配图风格库.md               # 已验证的配图风格模板（article-image-styles 管理）
│   │   ├── 图片风格库.md               # 图片风格参考（S01-S10）
│   │   └── 他山公众号运营技术全景规划.md
│   └── 宣发/                           # 宣传推广策略文档
│       ├── 宣发管理README.md            # 文章存档规范（科普/项目推广/学术推广）
│       ├── 他山世界_全维度宣发方案与文案矩阵.md
│       ├── 他山世界_宣发文案框架_v1.md
│       ├── 他山世界_科研圈专项宣发矩阵.md
│       └── 宣传点与人群画像.md
│
├── skills/                             # Cursor Skill 文件（AI 技能模块）
│   ├── document-pipeline/              # ★ 核心：统一写作流水线（Stage 0-8）
│   ├── wechat-article-writer/          # 公众号文章写作入口（thin wrapper）
│   │   └── templates/
│   │       ├── 配图索引模板.md          # 文章配图索引文件模板
│   │       └── 审稿意见模板.md          # 用户审稿意见记录模板
│   ├── general-article-writer/         # 通用长文写作入口（thin wrapper）
│   ├── research-output/                # 系统调研写作入口（thin wrapper）
│   ├── article-proofreading/           # 文章审稿（5轮独立检查）
│   ├── article-review-tracker/         # 用户审稿意见结构化追踪
│   ├── article-image-angles/           # 配图多视角分析（3-5个角度）
│   ├── article-image-styles/           # 配图风格库管理
│   └── ai-image-generator/             # 能力层：AI配图生成（qwen-image + DALL-E 3）
│
├── subagents/                          # Cursor Subagent 文件
│   └── wechat-scraper.md               # 微信公众号文章批量抓取
│
└── rules/                              # Cursor Rule 文件（全局约束）
    ├── full-node-audit.mdc             # F-022：写作节点挑战者反思
    └── knowledge-integrity-rules.mdc   # R2/R3/R6/R8：知识完整性全局约束
```

---

## 文章质量保障

三层保障，每层独立，缺一不可：

```
Layer 1  内容质检
  ├─ Stage 1 Step 6.8：F-022 挑战者反思
  │    陌生读者视角：理解障碍 / 标题-正文一致性 / 最弱段落
  └─ Stage 7：article-proofreading 独立审稿（5轮）
       第一轮：AI腔（翻译感/元评论/防守修饰/套话）
       第二轮：标题质量（话题描述→结论/机制→本质/定性→推导原则/现象→战略含义）
       第三轮：绝对表达软化（只/仅/必须/无法 → 大体/需要/难以）
       第四轮：结构合理性（坐标系污染/历史表位置/标注准确性）
       第五轮：结语完整性（涵盖全文/核心句位置/可独立传播）

Layer 2  HTML 格式核查（公众号专用）
  └─ check_html.py：15项自动验证
       颜色规范 / 字体 / 图片数量 / 绿色用量 / 底部模板完整性 / 署名等

Layer 3  内容对齐核查（公众号专用）
  └─ diff_check.py：Markdown 手稿与 HTML 内容零偏差验证
```

---

## 生态位置

本项目是「他山学科交叉」内容运营体系的组成部分，与他山 AI 产品生态协同工作。

```
他山 AI 产品生态
├── tashan-openbrain        数字分身 · 认知注入与智能体对话平台
├── Tashan-TopicLab         学术话题聚合 · 学术内容发现平台
├── tashan-profile-helper   科研人员画像构建
├── tashan-writing-system   ★ 本仓库：内容创作与宣传写作体系
└── tashan-forum            他山学科交叉论坛
```

### 相关仓库

| 仓库 | 定位 | 链接 |
|---|---|---|
| tashan-writing-system | 当前仓库 · 内容写作体系 | 当前页面 |
| tashan-openbrain | 认知注入平台（写作内容的认知根来源）| [TashanGKD/tashan-openbrain](https://github.com/TashanGKD) |
| Tashan-TopicLab | 内容分发的目标平台之一 | [TashanGKD/Tashan-TopicLab](https://github.com/TashanGKD/Tashan-TopicLab) |
| tashan-forum | 他山学科交叉论坛（另一内容分发渠道）| [TashanGKD/tashan-forum](https://github.com/TashanGKD/tashan-forum) |

---

## 贡献

欢迎贡献！详见 [CONTRIBUTING.md](CONTRIBUTING.md)。

- **Skill 改进**：欢迎优化任何 Skill 的流程步骤或增加新场景覆盖
- **文档改进**：欢迎完善 `docs/writing-guides/` 中的规范文档
- **Bug 反馈**：请在 Issues 中描述触发场景和预期/实际行为

---

## 更新日志

版本变更见 [CHANGELOG.md](CHANGELOG.md)。

---

## 许可证

MIT License. See [LICENSE](LICENSE) for details.
