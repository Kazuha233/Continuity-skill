# 数据处理说明 / Privacy and Data Handling

ContinuityKit 会在本地磁盘写入会话数据。这个文件告诉你存了什么、怎么处理。

ContinuityKit writes session data to your local disk. This file explains what is stored and how to handle it safely.

---

## 存了什么数据 / What Data Is Stored

Skill 在 `$CONTINUITY_KIT_HOME/` 目录下写入以下文件：

The skill writes the following files to `$CONTINUITY_KIT_HOME/`:

| 文件 / File | 内容 / Content | 包含会话数据？/ Contains conversation data? |
|------------|----------------|:---:|
| `relay.yaml` | 会话指针（ID、时间戳、一行摘要、状态）/ Session pointers (ID, timestamp, one-line summary, status) | 极少 -- 仅一行摘要 / Minimal -- one-line summaries only |
| `snapshots/*.yaml` | 任务锚点、注意力焦点、假设、未收束推理 / Task anchor, attention focus, assumptions, unfinished reasoning | **是** -- 结构化任务上下文 / **Yes** -- structured task context |
| `heartbeat.log` | 心跳历史（可选）/ Heartbeat history (optional) | **是** -- 注意力焦点和假设 / **Yes** -- attention focus and assumptions |

---

## 发布示例前 / Before Publishing Examples

公开分享任何输出或快照之前，请移除以下内容：

Before sharing any output or snapshot publicly, remove:

- 个人姓名、用户名 / Personal names, usernames
- 私密关系用语或可识别信息 / Private relationship terms or identifying details
- 原始用户对话或逐字引用 / Raw user conversations or verbatim quotes
- 私密记忆文件或个人笔记 / Private memory files or personal notes
- API key、token、凭证、本地路径 / API keys, tokens, credentials, local file paths
- 情绪系统内部数据（除非有意发布）/ Emotional-system internals (unless intentionally released)

**推荐的安全示例 / Recommended (safe) example:**

```yaml
task_anchor: "Implementing a public repository package"
attention_focus:
  - "Repository structure"
  - "License selection"
```

**要避免的不安全示例 / Avoid (unsafe):**

```yaml
task_anchor: "Private conversation with [name] about [sensitive topic]"
attention_focus:
  - "Raw quote from private message"
```

---

## 数据保留 / Data Retention

如果在团队或生产环境中部署 ContinuityKit，请披露以下信息：

If you deploy ContinuityKit in a team or production setting, disclose:

- 记录了哪些数据（见上方表格）/ What is logged (see table above)
- 日志保留多久（默认：快照永久保留，指针 20 条后修剪）/ How long logs are retained (default: snapshots kept indefinitely, pointers pruned after 20 entries)
- 谁可以访问存储的数据 / Who can access the stored data
- 用户如何请求删除 / How users can request deletion
- 日志是否用于训练或评估 / Whether logs are used for training or evaluation

---

## .gitignore

自带的 `.gitignore` 已过滤 `snapshots/`、`relay.yaml`、`heartbeat.log` 和 `config.yaml`，防止隐私数据被意外提交。推送前请检查你的 `.gitignore` 配置。

The included `.gitignore` filters out `snapshots/`, `relay.yaml`, `heartbeat.log`, and `config.yaml` to prevent accidental commits of private data. Review your `.gitignore` before pushing to any remote repository.
