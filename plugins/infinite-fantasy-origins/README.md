# infinite-fantasy-origins（ZCode 插件）· v0.5.1

把 **无限幻境：起源 0.5.1** 以「追加式 system 段」接入 ZCode 系统层，共两段：

| 段 | order | 文件 | 定位 |
| --- | --- | --- | --- |
| 主提示词 | 100 | `prompts/infinite-fantasy-origins.md` | 任务处理与输出约定 |
| 行为层 | 980 | `prompts/obedience-core.md` | 输出风格与执行一致性 |

不替换 identity、不动 `customSystemPrompt` 的替换语义；子代理不注入。

## v0.5.1 变更

长会话中的提示词保持：ZCode 在上下文增长后会重建消息序列，插件补入的 system 段
可能不再出现在后续请求里。v0.5.1 增加 `hooks.json` + `inject-hook.mjs`，
在三个时点把提示词续接回消息流：

| 事件 | 行为 |
| --- | --- |
| `SessionStart` | 会话开始注入两段完整提示 |
| `UserPromptSubmit` | 每次输入时重申行为层要点（约 1.1 KB） |
| `PostCompact` | 上下文压缩完成后补回完整提示（约 12.9 KB） |

## v0.5.0 变更

- 新增行为层（order 980），统一输出风格与执行一致性；
- 强度标定与 v4 对齐（100），稳定只约束输出形态；
- 主提示词与 DSH 侧 `dsh-infinite-fantasy-origins` 0.5.0 **逐字节相同**，行为层为同源新增段。

## 依赖的 ZCode 能力

需要支持插件 `systemPrompts` / `systemPrompt` / `systemPromptPath` 的 ZCode CLI 构建。

- `systemPrompts[].path` 相对插件根解析；绝对路径、`..` 越界、含 NUL 的声明会被拒绝并记诊断。
- 单插件预算 32 KiB，全部启用插件合计 64 KiB；本插件两段合计 16,955 字节（15,202 + 1,753）。
- 注入位置：稳定 `system` 段（`source: plugin_system_prompt`），排在內建 identity 之后、动态段之前，
  因此落在缓存前缀的尾部 —— 段内容一变，后面的缓存就失效，所以**改提示词要重启**。
- 0.5.1 起还依赖插件 hook：`plugin.json → "hooks": "hooks.json"`，事件为 `SessionStart` /
  `UserPromptSubmit` / `PostCompact`，用于长会话中的提示词续接。

## 安装 / 更新

本地市场 `zcode-local`（`marketplace.json` → `./plugins/infinite-fantasy-origins`）。

1. 在 ZCode 里刷新该本地市场（或移除后重新添加 `E:\vibecoding\zcode自用\zcode-local-marketplace`）；
2. 安装 / 更新本插件到 0.5.1；
3. **重启 ZCode**（缓存副本 `<zcode>/plugins/cache/...` 才会换新）。

## 验证

- 启动日志里 `bootstrap.app.startup.plugins.completed` 的 `pluginCount / enabledCount`
  应随安装递增，`diagnosticCount` 不应因本插件增加；
- 新开会话问「你现在遵循哪些输出约定」，回答应体现插件段落；
- 对话超过约 60K tokens 后，输出风格应保持一致。

## 提示词指纹

| 文件 | 大小 | sha256（前 16 位） |
| --- | --- | --- |
| `prompts/infinite-fantasy-origins.md` | 15,202 字节 | `0fd8e765487c7046` |
| `prompts/obedience-core.md` | 1,753 字节 | `8465ec2ba0495664` |

与 DSH 侧 `dsh-infinite-fantasy-origins` 0.5.0 逐字节一致；改源后需重装 + 重启，
因为 ZCode 读的是**安装缓存里的副本**（`<zcode>/plugins/cache/...`），不是市场目录。

## 许可与来源

本插件是 MIT 开源项目 **dsh-infinite-gen-2（无限二代，`Copyright (c) 2026 0x5477`）** 的衍生作品，
按 MIT 许可分发并保留原始版权声明。上游地址、改动清单见 [`NOTICE.md`](./NOTICE.md)，
许可全文见 [`LICENSE`](./LICENSE)。
