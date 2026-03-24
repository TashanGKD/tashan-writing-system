# tashan-writing-system

> 他山学科交叉 · 宣传写作体系
>
> 这是一套运行在 Cursor IDE 中、基于 AI Agent 的完整写作工作流——从选题构思到配图生成、从初稿到 HTML 排版发布，全流程闭环。

---

## 目录结构

```
tashan-writing-system/
├── README.md                          ← 本文件
│
├── skills/                            ← Cursor Skill 文件（AI 技能模块）
│   ├── document-pipeline/             ← 【核心】统一写作流水线（Stage 0-8）
│   ├── wechat-article-writer/         ← 公众号文章写作入口
│   │   └── templates/                 ← 配图索引模板、审稿意见模板
│   ├── general-article-writer/        ← 通用长文写作入口
│   ├── research-output/               ← 系统调研写作入口
│   ├── article-proofreading/          ← 文章审稿（5轮独立检查）
│   ├── article-review-tracker/        ← 用户审稿意见追踪
│   ├── article-image-angles/          ← 配图多视角分析
│   ├── article-image-styles/          ← 配图风格库管理
│   └── ai-image-generator/            ← 【能力层】AI配图生成
│
├── subagents/                         ← Cursor Subagent 文件
│   └── wechat-scraper.md              ← 微信公众号文章批量抓取
│
├── rules/                             ← Cursor Rule 文件（全局约束）
│   ├── full-node-audit.mdc            ← F-022：写作节点挑战者反思
│   └── knowledge-integrity-rules.mdc  ← 知识完整性约束（R2/R3/R6/R8）
│
└── docs/                              ← 参考文档
    ├── writing-guides/                ← 写作规范文档
    │   ├── 写作习惯与风格手册.md       ← 核心写作风格规范（所有 Skill 必读）
    │   ├── 微信公众号发布手册.md       ← 微信排版规范与发布流程
    │   ├── 全场景绘图指南.md           ← 内容类型 → 图类型映射
    │   ├── 配图风格库.md               ← 经验证的配图风格模板
    │   ├── 图片风格库.md               ← 图片风格参考
    │   └── 他山公众号运营技术全景规划.md
    └── 宣发/                          ← 宣传推广策略文档
        ├── 宣发管理README.md           ← 文章存档目录管理规范
        ├── 他山世界_全维度宣发方案与文案矩阵.md
        ├── 他山世界_宣发文案框架_v1.md
        ├── 他山世界_科研圈专项宣发矩阵.md
        └── 宣传点与人群画像.md
```

---

## 系统架构

### Skill 调用关系

```
用户指令
    │
    ├─ 「写公众号文章」→ wechat-article-writer (target_format=html)  ─┐
    ├─ 「写通用长文」  → general-article-writer (target_format=md)    ├─→ document-pipeline
    └─ 「调研并写文档」→ research-output       (mode=research)        ─┘
                                                      │
                                    ┌─────────────────┼──────────────────┐
                                    ↓                 ↓                  ↓
                          article-proofreading   ai-image-generator   Stage 8 格式转换
                          （Stage 7 审稿）       （Stage 5 画图）     （HTML/MD/LaTeX）
                                    ↑                 ↑
                          article-review-tracker  article-image-angles
                          （用户反馈追踪）       （配图视角分析）
                                                      ↑
                                              article-image-styles
                                              （风格库管理）
```

### 写作流水线阶段（document-pipeline Stage 0-8）

| Stage | 名称 | 说明 |
|---|---|---|
| Stage 0 | 入场检测 | 判断目标格式、入场阶段、文档性质 |
| Stage 1 | 研究 + 草稿 | 网络搜索、读取风格手册、写初稿 |
| Stage 2 | 逻辑自检 | 批判视角扫描论点-论据、概念一致性 |
| Stage 3 | 真实性校对 | 核实来源，标注无法核实内容 |
| Stage 4 | 画图规划 | 扫描草稿，确定配图位置和类型 |
| Stage 5 | 批量画图 | Mermaid + qwen-image 多视角生成 |
| Stage 6 | 图片自审 | 图文一致性检查，严重不符重生成 |
| Stage 7 | 审稿 | 触发 article-proofreading，审完才保存 |
| Stage 8 | 格式转换 | HTML（微信）/ MD（知识库）/ LaTeX（学术）|

---

## 快速上手

### 1. 安装到 Cursor 工作区

将本仓库中的文件按如下结构放入你的 Cursor 工作区：

```bash
# Skills → .cursor/skills/
cp -r skills/* .cursor/skills/

# Subagents → .cursor/agents/
cp subagents/* .cursor/agents/

# Rules → .cursor/rules/
cp rules/* .cursor/rules/

# 参考文档 → 推荐放入 _内部总控/ 或你的知识库目录
```

### 2. 配置 API Key

以下 Skill 使用了图片生成 API，需要你配置自己的 Key：

| 文件 | 占位符 | 说明 |
|---|---|---|
| `skills/ai-image-generator/SKILL.md` | `YOUR_DASHSCOPE_API_KEY` | 阿里云百炼 Dashscope，qwen-image-2.0-pro / wan2.6-t2i |
| `skills/ai-image-generator/SKILL.md` | `YOUR_DMXAPI_KEY` | DMXAPI（DALL-E 3 代理），备用模型 |
| `skills/document-pipeline/SKILL.md` | `YOUR_DMXAPI_KEY` | 同上 |

申请地址：
- Dashscope：[https://dashscope.aliyun.com](https://dashscope.aliyun.com)
- DMXAPI：[https://www.dmxapi.cn](https://www.dmxapi.cn)

### 3. 写作风格手册配置

写作风格手册（`docs/writing-guides/写作习惯与风格手册.md`）是所有 Skill 的"D0 认知根"，每次写作前会强制读取。

把它放到 Skill 引用的路径（或修改 Skill 中的引用路径）：
```
_内部总控/认知结构/L1_系统性文档/待建维度/写作习惯与风格手册.md
```

---

## 写作场景触发词

| 场景 | 触发词（对 Cursor AI 说） |
|---|---|
| 写公众号文章 | 「写一篇公众号文章/他山推文/推文」|
| 写通用长文 | 「写一篇技术文章/产品分析/对外分享/个人随笔」|
| 系统调研 | 「调研一下 XXX/帮我系统了解 XXX」|
| 审稿 | 「审稿/帮我检查这篇文章/review一下」|
| 追踪审稿意见 | 「审稿意见/修改文章/这里写得不好」|
| 配图分析 | 「帮我分析配图角度/给我几个配图方案」|
| 风格库管理 | 「调用风格库/用XX风格/存入风格库」|

---

## 文章质量保障体系

本体系包含三层质量保障，缺一不可：

```
Layer 1 【内容质检】
  ├─ Step 6.8：F-022 挑战者反思（陌生读者视角：理解障碍/标题一致/最弱段落）
  └─ Stage 7：article-proofreading（5轮：AI腔/标题4种错误/绝对表达/结构/结语）

Layer 2 【HTML格式核查】
  └─ check_html.py：15项自动验证（颜色/字体/图片/底部模板等）

Layer 3 【内容对齐核查】
  └─ diff_check.py：确认 Markdown 手稿与 HTML 内容零偏差
```

---

## 审稿标准（article-proofreading 五轮检查）

基于郑博元历次亲手修改批注的标准：

1. **第一轮 AI腔检测**：翻译感词汇 / 元评论过渡句 / 防守性修饰 / 套话
2. **第二轮 标题质量**（四种错误）：话题描述 vs 结论 / 机制描述 vs 本质 / 定性 vs 推导出的原则 / 现象描述 vs 战略含义
3. **第三轮 绝对表达软化**：「只/仅/必须/无法/永远」等软化处理
4. **第四轮 结构合理性**：坐标系污染 / 历史对比表位置 / 标注准确性
5. **第五轮 结语完整性**：涵盖全文章节 / 核心句位置 / 最后一句可独立传播

---

## 文章类型与排版模板

他山公众号支持四种文章类型，每种有对应结构模板：

| 类型 | 适用场景 | 字数目标 |
|---|---|---|
| 科教类 | AI + 科研技术解析 | 2000-4000 字 |
| 活动预告类 | 讲座/课程/活动报名 | 800-2000 字 |
| 活动回顾类 | 会议/活动总结 | 800-2000 字 |
| 动态类 | 招新/合作/项目进展 | 500-1000 字 |

**他山品牌色**：`rgb(111, 167, 170)` — 用于 H2 标题、摘要框左边框、底部署名标签

---

## 相关资源

- 他山学科交叉公众号：微信搜索「他山学科交叉」
- 他山 AI 产品官网：[tashan.chat](https://tashan.chat)
- 写作配套脚本（本地工具）：`check_html.py`（HTML核查）/ `diff_check.py`（MD-HTML对齐）

---

## 版本说明

| 版本 | 日期 | 说明 |
|---|---|---|
| v1.0 | 2026-03-24 | 初始发布，整合9个 Skill + 2个 Rule + 1个 Subagent + 参考文档 |

---

*本仓库由他山 AI 分身系统自动整理，基于 Cursor AI Agent 运行。*
