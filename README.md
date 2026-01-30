# Idea2Paper

[![PyPI - Python Version](https://img.shields.io/badge/python-3.10%2B-blue)]()
[![License](https://img.shields.io/badge/license-MIT-green)]()
[![Stars](https://img.shields.io/github/stars/AgentAlphaAGI/Idea2Paper?style=social)](https://github.com/AgentAlphaAGI/Idea2Paper/stargazers)

中文 / English — 在下方选择你的语言（或使用展开控件切换）

<details>
<summary><strong>切换语言 / Switch language</strong></summary>

- 点击 “中文” / “English” 快速跳转：
  - 中文部分：<a href="#中文介绍">中文介绍</a>
  - English section：<a href="#english-overview">English overview</a>

</details>

---

<!-- Star history (auto-generated SVG will be displayed here if present) -->
<p align="center">
  <img alt="Star history" src="assets/star_history.svg" style="max-width:100%;height:auto;">
</p>

---

# 中文介绍

## 项目概述
把你的研究想法（Idea）自动变成“可投稿论文的 Story（论文叙事骨架）”的端到端流水线：知识图谱召回 → Pattern 选择 → Story 生成 → 可标定 Multi-Agent Review → RAG 查重与智能修正。

仓库核心路径：`Paper-KG-Pipeline/`  
运行入口（不变）：
```bash
python Paper-KG-Pipeline/scripts/idea2story_pipeline.py "your idea"
```

## 核心特性（浓缩）
- 知识图谱：从 ICLR 数据构建 Idea/Pattern/Domain/Paper 节点（当前导出规模示例：Idea 8,284 / Pattern 124 / Domain 98 / Paper 8,285）。
- 三路召回 + 两阶段加速：Idea 相似 / Domain 泛化 / Paper 相似；粗排（Jaccard）+ 精排（Embedding）。
- Idea2Story 生成链路：Pattern 选择 → Story 生成 → 评审（Anchored Multi‑Agent）→ 智能修正（含 Novelty 模式）。
- 可标定多智能体评审：使用论文图谱中的真实 review_stats 作为锚点（anchors），LLM 输出相对比较结果，由确定性算法拟合最终 1~10 分，过程可审计。
- 完整运行日志与审计：每次 run 建立独立日志目录，记录 events、LLM/embedding 调用输入输出，便于回放与审计。

## 你将得到（输出）
- `Paper-KG-Pipeline/output/final_story.json`：最终 Story（结构化字段：标题/摘要/问题/方法/贡献/实验等）
- `Paper-KG-Pipeline/output/pipeline_result.json`：完整链路结果（包含评审、修正、查重、审计信息）
- 仓库根 `log/run_.../`：每次运行的结构化运行日志

## 快速开始（端到端）
1. Python 3.10+
2. 安装依赖：
   ```bash
   pip install -r Paper-KG-Pipeline/requirements.txt
   ```
3. 配置：
   - 复制 `.env.example` -> `.env`，填写 `SILICONFLOW_API_KEY` 等敏感键（不要提交）
   - 可选：复制 `i2p_config.example.json` -> `i2p_config.json` 调整阈值/anchors 等
4. 运行：
   ```bash
   python Paper-KG-Pipeline/scripts/idea2story_pipeline.py "你的研究Idea描述"
   ```

## Multi‑Agent Review（可标定、可追溯）
核心思想：用真实论文评分分布作为锚点，LLM 做相对比较（better/tie/worse + confidence + rationale），最终分数由确定性算法拟合，使评审结果可复现、可审计。详见仓库 `MULTIAGENT_REVIEW.md`（或 `Paper-KG-Pipeline/docs` 中相应文档）。

## star 历史曲线图（说明）
README 顶部会显示 `assets/star_history.svg`。该文件可通过提供的脚本自动生成/更新（使用 GitHub API 获取 stargazers 的时间戳并绘制每日累计星数曲线）。示例脚本：`scripts/generate_star_history.py`（见仓库）。在 CI 中运行此脚本并将生成的 `assets/star_history.svg` 提交到仓库即可在 README 中展示最新曲线。

---

# English overview

## Project summary
Idea2Paper is an end-to-end pipeline that turns a research idea into a submission-ready "story" (paper narrative skeleton): Knowledge-graph retrieval → Pattern selection → Story generation → Anchored Multi‑Agent review → RAG dedup & intelligent revision.

Core code lives in `Paper-KG-Pipeline/`. Entrypoint (unchanged):
```bash
python Paper-KG-Pipeline/scripts/idea2story_pipeline.py "your idea"
```

## Key features (short)
- Knowledge graph built from ICLR data with Idea/Pattern/Domain/Paper nodes.
- Three-path retrieval + two-stage ranking: idea-similarity / domain-generalization / paper-similarity; coarse Jaccard → fine embedding ranking.
- Idea2Story: pattern selection → story generation → critic review (anchored multi-agent) → smart correction (novelty mode).
- Anchored Multi-Agent Review: uses real review_stats from the paper graph as anchors. LLMs produce relative comparisons; final 1–10 scores are fitted by a deterministic algorithm, fully auditable.
- Per-run structured logging with LLM and embedding call traces for reproducibility and audits.

## Outputs you get from a run
- `Paper-KG-Pipeline/output/final_story.json` — final structured Story (title/abstract/problem/method/contribs/experiments).
- `Paper-KG-Pipeline/output/pipeline_result.json` — full pipeline trace (round-by-round reviews, corrections, dedup audits).
- `log/run_.../` — per-run structured logs (events, llm_calls, embedding_calls).

## Quick start (end-to-end)
1. Python 3.10+
2. Install dependencies:
   ```bash
   pip install -r Paper-KG-Pipeline/requirements.txt
   ```
3. Configuration:
   - Copy `.env.example` → `.env`, fill `SILICONFLOW_API_KEY` (do not commit).
   - Optionally copy `i2p_config.example.json` → `i2p_config.json` to tweak anchors/thresholds.
4. Run:
   ```bash
   python Paper-KG-Pipeline/scripts/idea2story_pipeline.py "your research idea"
   ```

## Anchored Multi‑Agent Review (explain)
Instead of non-auditable 1–10 scores, this project uses anchored comparisons: select anchor papers (with real score10), ask LLM to compare target vs anchors (better/tie/worse + confidence + rationale referencing anchor score), then deterministically fit a final numeric score. The chain of evidence is stored in audit logs.

## Star history chart (how to update)
The README displays `assets/star_history.svg`. Use the script `scripts/generate_star_history.py` to fetch stargazer timestamps via GitHub API and render a cumulative-star SVG. Run it locally or in CI, commit the resulting `assets/star_history.svg` to update the chart in README.

---

# Files & Docs (important paths)
- Core code: `Paper-KG-Pipeline/src/idea2paper/`
- Entry scripts:
  - `Paper-KG-Pipeline/scripts/idea2story_pipeline.py`
  - `Paper-KG-Pipeline/scripts/simple_recall_demo.py`
- Docs:
  - `Paper-KG-Pipeline/docs/00_PROJECT_OVERVIEW.md`
  - `Paper-KG-Pipeline/docs/01_KG_CONSTRUCTION.md`
  - `Paper-KG-Pipeline/docs/02_RECALL_SYSTEM.md`
  - `Paper-KG-Pipeline/docs/03_IDEA2STORY_PIPELINE.md`
- Multi-Agent details: `MULTIAGENT_REVIEW.md`

---

# How to generate/update the star history SVG (example)
Prerequisites:
- A GitHub token with repo/public_repo access (to avoid rate limits)
- Python 3.10+, pip packages: PyGithub, pandas, matplotlib

Example:
```bash
pip install PyGithub pandas matplotlib
python scripts/generate_star_history.py --repo AgentAlphaAGI/Idea2Paper --token $GITHUB_TOKEN --out assets/star_history.svg
```

Add the generated `assets/star_history.svg` to the repo and push; the README will show the updated chart.

---

# Contributing / License
欢迎 PR / Issue。遵循 repo 中的贡献指南与 Code of Conduct。默认 MIT 许可（见 LICENSE）。

---

# 致谢 / Credits
- 数据来源：ICLR（见 docs 中 KG 构建说明）
- 设计灵感：以可审计的真实锚点为中心的评审流程
