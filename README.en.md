<!-- Logo -->
<p align="center">
  <a href="https://tashan.ac.cn" target="_blank" rel="noopener noreferrer">
    <img src="docs/assets/tashan.svg" alt="Tashan Logo" width="280" />
  </a>
</p>

<!-- Title -->
<p align="center">
  <strong>Tashan Content Writing System</strong><br>
  <em>他山宣传写作体系</em>
</p>

<!-- Navigation -->
<p align="center">
  <a href="#overview">Overview</a> •
  <a href="#features">Features</a> •
  <a href="#quick-start">Quick Start</a> •
  <a href="#architecture">Architecture</a> •
  <a href="#directory-structure">Directory</a> •
  <a href="#ecosystem">Ecosystem</a> •
  <a href="#contributing">Contributing</a> •
  <a href="README.md">中文</a>
</p>

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

> A complete AI-powered writing workflow for Tashan cross-discipline content: WeChat articles, long-form writing, research reports, image generation, and proofreading — running on Cursor AI Agent.

---

## Overview

The core proposition of the "Human-Agent Hybrid Digital World" is that AI is not merely a tool, but a partner in cognitive externalization and knowledge production. Content writing is the most direct application of this proposition — every article is a projection of the cognitive structure outward.

This project corresponds to the **content creation layer**, solving the problem of "how to make AI produce high-quality, reproducible, and aesthetically sound articles from ideation to publication." Without this project, AI writing remains one-off conversational generation with unstable quality; with it, writing becomes a standardized, callable, and iteratable pipeline.

**Core Ideas**

- **Cognitive root first**: Every article must be traceable to a specific view in the cognitive structure (L1/L1.5 docs), rejecting "floating writing"
- **Pipeline over single generation**: 8 Stages run in sequence (Research → Draft → Logic Check → Fact Check → Image Planning → Image Generation → Proofreading → Publish), each with clear I/O and failure handling
- **Three-layer quality assurance**: Content self-check (challenger reflection) + Independent proofreading (5 rounds) + Format validation (15 automated checks)

**Use Cases & Audience**

- Academic WeChat operators: Write high-quality science communication articles with consistent style
- AI tooling researchers: Reference a complete Cursor Skill orchestration for content production workflows
- Tashan ecosystem developers: Extend to new content types (academic outreach, event recaps, etc.)

---

## Features

- **Unified writing entry**: `document-pipeline` handles all writing tasks; enter at any Stage — start from proofreading if you have a draft, start from image generation if you only need images
- **Full WeChat pipeline**: Topic selection → HTML formatting → Footer template → Publish, covering the complete WeChat official account publishing workflow
- **Multi-perspective images**: 2-4 independent logical angles per image, bilingual (qwen-image + DALL-E 3), with Mermaid skeleton preserved
- **Built-in proofreading standards**: 5-round proofreading (AI tone / 4 title errors / absolute expressions / structural soundness / conclusion completeness), based on Boyuan Zheng's annotation history
- **Review tracking**: Structured recording and execution of user feedback, with trackable status
- **Style library**: Validated image styles can be saved as templates for reuse across articles, maintaining visual consistency
- **WeChat history scraping**: `wechat-scraper` subagent supports batch article scraping as writing reference

---

## Quick Start

### 1. Clone the repository

```bash
git clone https://github.com/TashanGKD/tashan-writing-system.git
cd tashan-writing-system
```

### 2. Install into your Cursor workspace

```bash
# Skills → .cursor/skills/
cp -r skills/* /your-workspace/.cursor/skills/

# Subagents → .cursor/agents/
cp subagents/* /your-workspace/.cursor/agents/

# Rules → .cursor/rules/
cp rules/* /your-workspace/.cursor/rules/

# Reference docs → recommended knowledge base directory
cp -r docs/writing-guides/ /your-workspace/_内部总控/认知结构/L1_系统性文档/待建维度/
```

### 3. Configure API Keys

| File | Placeholder | Service |
|---|---|---|
| `skills/ai-image-generator/SKILL.md` | `YOUR_DASHSCOPE_API_KEY` | [Alibaba Dashscope](https://dashscope.aliyun.com) (qwen-image-2.0-pro / wan2.6-t2i) |
| `skills/ai-image-generator/SKILL.md` | `YOUR_DMXAPI_KEY` | [DMXAPI](https://www.dmxapi.cn) (DALL-E 3 proxy, fallback) |
| `skills/document-pipeline/SKILL.md` | `YOUR_DMXAPI_KEY` | Same as above |

```bash
# Batch replace (macOS/Linux)
sed -i '' 's/YOUR_DASHSCOPE_API_KEY/your-actual-key/g' skills/ai-image-generator/SKILL.md
sed -i '' 's/YOUR_DMXAPI_KEY/your-actual-key/g' skills/ai-image-generator/SKILL.md skills/document-pipeline/SKILL.md
```

### 4. Start writing

In Cursor chat, tell the AI:

```
Write a WeChat article about "XXX"
```

The AI will automatically load `wechat-article-writer` Skill, route to `document-pipeline`, and proceed through Stage 0-8.

---

## Architecture

### Skill Call Graph

```
User input
    │
    ├─ "Write WeChat article" → wechat-article-writer (html)    ─┐
    ├─ "Write long article"   → general-article-writer (md)      ├──→ document-pipeline (Stage 0-8)
    └─ "Research and write"   → research-output (research)       ─┘
                                            │
                         ┌──────────────────┼──────────────────┐
                         ↓                  ↓                   ↓
               article-proofreading   ai-image-generator   Stage 8 format
               (Stage 7 · 5 rounds)  (Stage 5 · multi-view)  (HTML/MD/LaTeX)
                         ↑                  ↑
               article-review-tracker  article-image-angles
               (user feedback tracker) (angle analysis)
                                            ↑
                                    article-image-styles
                                    (style library)
```

### Pipeline Stages

| Stage | Name | Description |
|---|---|---|
| 0 | Entry Detection | Determine target format (html/md/latex), entry stage, doc type |
| 1 | Research + Draft | Read style guide, web search, write draft, multi-topic logic check |
| 2 | Logic Check | Scan argument-evidence completeness, concept consistency |
| 3 | Fact Check | Verify sources, annotate `[unverified]`, never delete content |
| 4 | Image Planning | Identify image positions and types (Mermaid/qwen-image) |
| 5 | Batch Images | Mermaid + Chinese image + English image, bilingual triplet |
| 6 | Image Review | Check image-text consistency, regenerate if severely mismatched |
| 7 | Proofreading | Trigger `article-proofreading`, fix P0 directly, suggest P1/P2 |
| 8 | Format Convert | HTML (WeChat) / MD (knowledge base) / LaTeX (academic) |

---

## Directory Structure

```
tashan-writing-system/
├── README.md                           # Chinese documentation (this file)
├── README.en.md                        # English documentation
├── LICENSE                             # MIT License
├── CONTRIBUTING.md                     # Contribution guide
├── CHANGELOG.md                        # Changelog
│
├── docs/
│   ├── assets/
│   │   └── tashan.svg                  # Tashan Logo
│   ├── writing-guides/                 # Writing standard documents
│   │   ├── 写作习惯与风格手册.md        # ← "D0 cognitive root" for all Skills
│   │   ├── 微信公众号发布手册.md        # WeChat formatting & publishing guide
│   │   ├── 全场景绘图指南.md            # Content type → image type mapping
│   │   ├── 配图风格库.md               # Validated image style templates
│   │   ├── 图片风格库.md               # Image style reference (S01-S10)
│   │   └── 他山公众号运营技术全景规划.md # WeChat account operation tech plan
│   └── 宣发/                           # Brand promotion strategy docs
│       ├── 宣发管理README.md
│       ├── 他山世界_全维度宣发方案与文案矩阵.md
│       ├── 他山世界_宣发文案框架_v1.md
│       ├── 他山世界_科研圈专项宣发矩阵.md
│       └── 宣传点与人群画像.md
│
├── skills/                             # Cursor Skill files (AI capability modules)
│   ├── document-pipeline/              # ★ Core: unified writing pipeline (Stage 0-8)
│   ├── wechat-article-writer/          # WeChat article entry (thin wrapper)
│   │   └── templates/
│   │       ├── 配图索引模板.md          # Image index template
│   │       └── 审稿意见模板.md          # Review feedback template
│   ├── general-article-writer/         # Long-form article entry (thin wrapper)
│   ├── research-output/                # Research report entry (thin wrapper)
│   ├── article-proofreading/           # Proofreading (5 independent rounds)
│   ├── article-review-tracker/         # User review feedback tracker
│   ├── article-image-angles/           # Multi-perspective image analysis
│   ├── article-image-styles/           # Image style library management
│   └── ai-image-generator/             # Capability layer: image generation
│
├── subagents/                          # Cursor Subagent files
│   └── wechat-scraper.md               # WeChat article batch scraper
│
└── rules/                              # Cursor Rule files (global constraints)
    ├── full-node-audit.mdc             # F-022: challenger reflection at writing nodes
    └── knowledge-integrity-rules.mdc   # R2/R3/R6/R8: knowledge integrity constraints
```

---

## Ecosystem

This project is part of the Tashan cross-discipline content operation system, working in coordination with the Tashan AI product ecosystem.

```
Tashan AI Ecosystem
├── tashan-openbrain        Digital twin · cognitive injection & agent conversation
├── Tashan-TopicLab         Academic topic aggregation platform
├── tashan-profile-helper   Researcher profile building
├── tashan-writing-system   ★ This repo: content creation & writing system
└── tashan-forum            Tashan cross-discipline forum
```

### Related Repositories

| Repository | Role | Link |
|---|---|---|
| tashan-writing-system | This repo · content writing system | Current page |
| tashan-openbrain | Cognitive injection platform (source of cognitive roots for writing) | [TashanGKD/tashan-openbrain](https://github.com/TashanGKD) |
| Tashan-TopicLab | Content distribution platform | [TashanGKD/Tashan-TopicLab](https://github.com/TashanGKD/Tashan-TopicLab) |
| tashan-forum | Cross-discipline forum (another distribution channel) | [TashanGKD/tashan-forum](https://github.com/TashanGKD/tashan-forum) |

---

## Contributing

Contributions welcome! See [CONTRIBUTING.md](CONTRIBUTING.md).

- **Skill improvements**: Optimize any Skill's workflow or add new scenario coverage
- **Documentation**: Improve guides in `docs/writing-guides/`
- **Bug reports**: Describe the trigger scenario and expected/actual behavior in Issues

---

## Changelog

See [CHANGELOG.md](CHANGELOG.md) for version history.

---

## License

MIT License. See [LICENSE](LICENSE) for details.
