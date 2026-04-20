# Procedure: voice-journal

> 帮 Daniel 把口述内容写进 Obsidian daniel-journal vault 的 `Journal/`。
> **上游规则：vault 内 `99-agent/WIKI_WORKFLOW_SOP.md §6` 为准。**

---

## 何时触发

用户（Daniel）通过语音或文字表达："记一下"、"写日记"、"今天的日志"、"帮我记录一下刚才说的" 等意图，内容是**个人视角的叙述 / 感受 / 观察 / 想法**。

**不触发**的情况：
- 纯技术讨论 / 调试日志（走文章归档或项目日志）
- 短期备忘提醒（走 reminders / today view）
- 与其他人的对话记录（不是 Daniel 自己的"日记"）

---

## 执行步骤

### 1. 进入 vault 并同步

```bash
cd /root/.openclaw/workspace/obsidian/daniel-journal
git pull --ff-only
```

非 fast-forward → **立即停**，通知 Daniel。

### 2. 确定目标文件

- 路径：`Journal/YYYY-MM-DD.md`（按 Asia/Shanghai 时区）
- **已存在** → 追加，不改历史段落
- **不存在** → 新建

### 3. 新建文件模板

```markdown
---
title: "YYYY-MM-DD"
created: YYYY-MM-DD
voice: assistant-ghost
source: telegram:<session-id> | voice | cli
tags: [journal]
---

# YYYY-MM-DD

## HH:MM

> [!quote] Daniel
> <Daniel 的原话 / 转录文本>

<基于口述整理的正文，保持 Daniel 的第一人称视角，不加入 agent 的解读或延伸>
```

### 4. 追加文件模板（已存在时）

在文件末尾追加：

```markdown

---

## HH:MM

> [!quote] Daniel
> <Daniel 的原话>

<整理稿>
```

**严禁**：
- 改写或删除已有段落
- 合并/重排已有段落
- 润色已有历史内容

### 5. 作者权标注铁律

- Daniel 原话（或近乎原话的语音转录）：用 `> [!quote] Daniel` 块包裹
- 基于原话整理的正文：正文无标记，但 frontmatter `voice: assistant-ghost`
- **推断 / 延伸 / 你的观察**：不进日记。若 Daniel 明确让你"帮我想想"，另起段落用 `> [!note] 由 <agent> 延伸` 块，**绝不**伪装成 Daniel 原话

### 6. Commit & Push

```bash
git add Journal/YYYY-MM-DD.md
git commit -m "assistant(journal): YYYY-MM-DD voice dictation"
git push origin main
```

Push 失败 → 停 + 保存 diff 到 `04-logs/conflicts/`，通知 Daniel。

### 7. 留痕

追加到 `04-logs/assistant-runs/YYYY-MM-DD.md`：

```markdown
## HH:MM — journal dictation
- trigger: telegram:<chat-id> | voice | cli
- session: <session-id>
- user_intent: 口述日记
- files: Journal/YYYY-MM-DD.md
- commit: <hash>
- outcome: ok
```

### 8. 回报

告诉 Daniel：commit hash + 文件路径。可附一句"已落盘"或简短确认。

---

## 铁律（汇总）

1. 只写当天 `Journal/YYYY-MM-DD.md`，跨天应分多个文件
2. 代笔 ≠ 改写：不改历史段落
3. Daniel 原话必须可识别（`> [!quote] Daniel`）
4. 你的推断不混进 Daniel 的叙述
5. 写入前 `git pull --ff-only`，失败即停
6. `git add <精确文件>`，禁用 `git add .`
7. Push 失败不强推、不 rebase
8. 一次口述一个 commit
9. 必须留痕到 `04-logs/assistant-runs/`

---

## 参考

- `99-agent/AGENTS.md` §8 作者权标注规范
- `99-agent/WIKI_WORKFLOW_SOP.md` §6 语音口述日记的标准流
