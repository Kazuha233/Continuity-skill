# 组合使用 Continuity-skill + Hermes Guard

两个项目互补——同时使用能覆盖 AI Agent 最常见的六种失败模式。

## 职责分工

| 项目 | 许可证 | 防什么 | 方式 |
|------|--------|--------|------|
| **Continuity-skill** | PolyForm Noncommercial | 漂移、失忆、幽灵连续性 | Agent 自律（心跳 + 快照 + RELAY） |
| **Hermes Guard** | AGPL-3.0 | 吹牛、编造数字、空头承诺 | 外部审查层（每句话扫描证据） |

## 同时安装

```bash
# 1. 安装 Continuity-skill
hermes skills install https://raw.githubusercontent.com/Kazuha233/Continuity-skill/master/skill/SKILL.md

# 2. 安装 Hermes Guard
git clone https://github.com/phamhien4278n-hub/hermes-guard.git
cd hermes-guard
npm install

# 3. 配置 Hermes hooks（在 ~/.hermes/config.yaml 中添加）
```

```yaml
# ~/.hermes/config.yaml
hooks:
  pre_llm_call:
    - command: "node /path/to/hermes-guard/hermes-hook-bridge.cmd"
      timeout: 20
  transform_llm_output:
    - command: "node /path/to/hermes-guard/hermes-hook-bridge.cmd"
      timeout: 20
```

## 效果

```
Continuity-skill 保证:
  ✅ Agent 知道自己当前在做什么（心跳）
  ✅ 下次会话记得进度（快照）
  ✅ 每项声明有来源（RELAY）

Hermes Guard 保证:
  ✅ Agent 说"测试通过"→ 必须真跑过测试
  ✅ Agent 说"99.7%准确率"→ 必须有网页证据
  ✅ Agent 说"已保存"→ 必须有文件写操作记录
```

## 上游链接

- [Hermes Guard](https://github.com/phamhien4278n-hub/hermes-guard) — AGPL-3.0, by Qianhui
- 更多配置详见 Hermes Guard 的 [README](https://github.com/phamhien4278n-hub/hermes-guard#hermes-hook-integration--接入-hermes)
