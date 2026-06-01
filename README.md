# Claude Code 配置切换器

> ⚠️ **本文件中 API Key 已替换为占位符。使用前请先用记事本打开，将占位符替换为你自己的 Key。切勿将含真实 Key 的版本上传到公开仓库。**

> 一键在 Claude Opus 4.8 与 DeepSeek V4 PRO 之间无缝切换 Claude Code 后端模型。

**双击即用，无需安装任何运行环境。仅限 Windows。**

---

## 它能做什么

如果你同时拥有两套 AI 模型接入方案（例如一边是 b.ai 中转站的 Claude、一边是 DeepSeek 官方的 Anthropic 兼容接口），每次手动改 `settings.json` 又繁琐又易出错。

这个工具帮你把切换过程变成 **点一下按钮**：

| | 方案 A（示例） | 方案 B（示例） |
|---|---|---|
| 模型 | Claude Opus 4.8 | DeepSeek V4 PRO |
| 线路 | https://api.b.ai | https://api.deepseek.com/anthropic |
| 鉴权方式 | API_KEY | AUTH_TOKEN |

> 上面的线路 / 模型 / 鉴权方式都可以在源码里自由修改，适配你自己的中转站。

---

## 快速使用（3 步）

### 1. 下载

从 [Releases](https://github.com/QQROCK112233/-claude-code-/releases) 下载 `Claude配置切换器.hta`，放到桌面或任意目录。

### 2. 填入你的 API Key

右键 `.hta` 文件 →「打开方式」→「记事本」，找到以下两处：

```javascript
// 方案 A（第 201 行）
ANTHROPIC_API_KEY: "在此填入你的b.ai_API_Key",

// 方案 B（第 213 行）
ANTHROPIC_AUTH_TOKEN: "在此填入你的DeepSeek密钥",
```

把引号内的占位符替换成你自己的 Key，保存关闭。

### 3. 双击运行

双击 `.hta` → 界面会自动检测当前使用的方案 → 点击另一张卡片上的按钮即可切换。

**切换后必须完全退出并重启 Claude Code 才能生效。**

---

## 运行原理

| 步骤 | 操作 | 说明 |
|------|------|------|
| 1 | 备份 | `settings.json` → `settings.json.bak.20260601_143022`（带时间戳，不覆盖历史备份） |
| 2 | 写入 | 新内容 → `settings.json.tmp`（临时文件） |
| 3 | 移动 | `settings.json.tmp` → `settings.json`（替换） |
| 4 | 校验 | 重新读取，将整个 env 对象序列化后整体比对（覆盖全部 7+ 字段） |
| 5 | 回滚 | 校验失败时自动从最近一次备份恢复 |

切换的是 `%USERPROFILE%\.claude\settings.json` 中的 `env` 对象——**整体替换**，不会出现两套配置的变量互相残留的问题。

---

## 修改配置说明

打开 `.hta` 文件（用记事本），找到 `CFG = { A: {...}, B: {...} }` 区域（第 196 行起），每个方案的字段说明：

| 字段 | 含义 | 示例 |
|------|------|------|
| `ANTHROPIC_BASE_URL` | API 地址 | `https://api.b.ai` |
| `ANTHROPIC_API_KEY` | API 密钥（部分中转站用这个） | `sk-xxx...` |
| `ANTHROPIC_AUTH_TOKEN` | 认证 Token（部分中转站用这个） | `sk-xxx...` |
| `ANTHROPIC_MODEL` | 主模型名称 | `claude-opus-4.8` |
| `ANTHROPIC_DEFAULT_HAIKU_MODEL` | 子代理轻量模型 | `deepseek-v4-flash` |
| `MAX_THINKING_TOKENS` | 思考 token 上限（0 = 禁用思考） | `0` |
| `CLAUDE_CODE_EFFORT_LEVEL` | 执行努力级别 | `max` |

---

## 环境要求

- **操作系统**：Windows 10 / 11（需支持 HTA，系统自带，无需额外安装）
- **已安装 Claude Code**：确保 `%USERPROFILE%\.claude\settings.json` 存在

---

## 安全提醒

1. **Key 是敏感信息**——请勿将含有真实 Key 的 `.hta` 文件上传到公开仓库或分享给不信任的人。
2. 本项目代码中 **不含任何作者的 API Key**（已替换为占位符），每次公开发布前请确认。
3. 代码仅操作 `%USERPROFILE%\.claude\settings.json` 这一个文件，不会读写其他位置。

---

## 常见问题

**Q: 双击后弹出安全警告？**
A: 系统提示「来自 Internet 的文件」，点击「允许」或解除文件锁定：右键 → 属性 → 勾选「解除锁定」→ 确定。

**Q: 切换后还是旧的模型？**
A: 需要**完全退出 Claude Code**（包括终端里的后台进程），再重新启动。

**Q: 能不能改成三套 / 更多套配置？**
A: 可以。仿照 `CFG.A` / `CFG.B` 的结构新增 `CFG.C`，在 HTML 里加一张卡片，`detectActive()` 和 `onSwitch()` 加上对应逻辑即可。
