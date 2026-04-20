# Procedure: article-archive

> 把 Daniel 发来的文章 / 链接 / 文档归档进 Obsidian daniel-journal vault 的 `01-raw-articles/`。
> **上游规则：vault 内 `99-agent/WIKI_WORKFLOW_SOP.md §7` 为准。**

---

## 何时触发

Daniel 发来以下任意形式，并暗示"归档 / 保存 / 收藏"：
- URL 链接（公众号文章、Twitter/X thread、博客、论文等）
- 直接粘贴的文章正文 / 截图
- 转发的文档文件

**不触发**：
- 纯提问 / 讨论（不归档，直接回答）
- 让你"总结一下这篇文章"但没有说"存下来"（可以先总结，但**不自动写文件**，除非明确要求）

---

## 执行步骤

### 1. 进入 vault 并同步

```bash
cd /root/.openclaw/workspace/obsidian/daniel-journal
git pull --ff-only
```

非 fast-forward → **立即停**，通知 Daniel。

### 2. 确定来源子目录

| 来源 | 子目录 | 示例 |
|------|--------|------|
| 微信公众号 | `01-raw-articles/wechat/` | `a16z-ai-application-layer.md` |
| Twitter/X | `01-raw-articles/twitter/` | `elon-musk-ai-vision-2026.md` |
| 博客 / 网站 | `01-raw-articles/web/` | `obsidian-workflow-guide.md` |
| 论文 / arXiv | `01-raw-articles/papers/` | `attention-is-all-you-need.md` |
| 其他 | `01-raw-articles/other/` | `<slug>.md` |

**不确定来源** → 默认放 `web/`。

### 3. 生成文件名

- 格式：`<english-slug>.md`
- 规则：英文 kebab-case，从文章标题翻译/音译，**禁止中文文件名**
- 示例：
  - "深度访谈｜a16z合伙人：AI最大机会在应用层" → `a16z-ai-application-layer-interview.md`
  - "跟OpenClaw作者学Agentic Engineering" → `learn-agentic-engineering-with-openclaw-author.md`
  - "Gemini 3预训练负责人揭秘..." → `gemini-3-pretraining-lead-interview.md`
- **冲突处理**：同名文件已存在 → 追加 `-2`、`-3` 后缀

### 4. 获取文章内容

优先级：
1. 用 `web_fetch` 抓取正文（支持大多数网页）
2. 微信文章可能被反爬 → 记录标题 + URL，正文标 `status: needs-retrieval`
3. Daniel 直接粘贴正文 → 直接使用
4. 文件 / 截图 → 用 image tool 提取文字

### 5. 写入文件

模板：

```markdown
---
title: "<原始标题>"
created: YYYY-MM-DD
source: wechat | twitter | web | papers | other
origin_url: "<原文 URL>"
captured_at: YYYY-MM-DDTHH:MM:SS+08:00
voice: external
status: raw | needs-retrieval
tags: [<来源标签>, <主题标签>]
---

# <原始标题>

> 来源：[<来源名>](<URL>)
> 抓取时间：YYYY-MM-DD HH:MM

---

<文章正文>

---

## Agent Notes

<!-- agent 在此可追加简短备注，如抓取状态、补充链接等 -->
<!-- 禁止在此写总结 / 评论 / 解读，那是 knowledge 层的事 -->
```

### 6. 可选：小摘要到 _drafts

**仅当 Daniel 明确要求**，或文章明显高价值且 Daniel 历史偏好会看摘要时：

- 路径：`02-knowledge/_drafts/<same-slug>-summary.md`
- frontmatter：`voice: assistant-derived`, `derived_from: "[[01-raw-articles/<source>/<slug>]]"`
- 内容：3-5 行要点摘要
- **不自动做**。没说就不要擅自动 `_drafts/`

### 7. Commit & Push

```bash
git add 01-raw-articles/<source>/<slug>.md
git commit -m "assistant(raw): ingest <source>/<slug>"
git push origin main
```

Push 失败 → 停 + 保存 diff 到 `04-logs/conflicts/`，通知 Daniel。

### 8. 留痕

追加到 `04-logs/assistant-runs/YYYY-MM-DD.md`：

```markdown
## HH:MM — article archive
- trigger: telegram:<chat-id> | cli
- session: <session-id>
- user_intent: 归档文章
- source_url: <URL>
- files: 01-raw-articles/<source>/<slug>.md
- commit: <hash>
- outcome: ok | partial (正文未抓到，待补)
```

### 9. 回报

告诉 Daniel：文件路径 + commit hash + 正文是否完整抓取。如正文缺失，提示"需要手动补充"。

---

## 铁律（汇总）

1. 原文只新增不改动，**绝不覆盖或删除已有 raw 文件**
2. 文件名一律英文 kebab-case，**禁止中文文件名**
3. 微信反爬抓不到时如实标注 `status: needs-retrieval`，不伪造正文
4. 摘要落 `_drafts/` 须 Daniel 明确要求或历史偏好支持，**不自动生成**
5. 写入前 `git pull --ff-only`，失败即停
6. `git add <精确文件>`，禁用 `git add .`
7. Push 失败不强推、不 rebase
8. 一篇文章一个 commit
9. 必须留痕到 `04-logs/assistant-runs/`

---

## 参考

- `99-agent/AGENTS.md` §8 作者权标注规范
- `99-agent/WIKI_WORKFLOW_SOP.md` §7 文章归档流
