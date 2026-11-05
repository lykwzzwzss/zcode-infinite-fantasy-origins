# ⚔️ Infinite Fantasy: Origins（无限幻境：起源）· ZCode 版

把 **无限幻境：起源 0.5.1** 以「追加式 system 段」接入 ZCode，共两段提示词，
另配一组轻量 hook，用于长会话中的提示词续接。

| 段 | order | 文件 | 定位 |
| --- | --- | --- | --- |
| 主提示词 | 100 | `prompts/infinite-fantasy-origins.md` | 任务处理与输出约定 |
| 行为层 | 980 | `prompts/obedience-core.md` | 输出风格与执行一致性 |

不替换 ZCode 的 identity，也不改写 `customSystemPrompt` 的替换语义；子代理不注入。

> 本仓库是 **ZCode 插件形态**。DSH（DeepSeek Harness）版在
> [lykwzzwzss/dsh-infinite-fantasy-origins](https://github.com/lykwzzwzss/dsh-infinite-fantasy-origins)，
> 它额外提供热更、启动自检、状态徽标与回归评测；那些能力在 ZCode 侧由宿主自己的
> `systemPrompts` 机制承担。

---

## 0.5.x 变更

- **0.5.1**：长会话中的提示词保持 —— 会话开始、用户输入、上下文压缩后都会自动续接，
  减少中途的风格漂移；
- **0.5.0**：新增行为层（order 980）；强度标定与 v4 对齐，稳定只约束输出形态。

## 文件结构

```
marketplace.json                                   # 市场清单（本仓库即市场）
plugins/infinite-fantasy-origins/
├── .zcode-plugin/plugin.json                      # 插件清单：systemPrompts + hooks
├── hooks.json                                     # 会话事件声明
├── inject-hook.mjs                                # 读取两段提示词并输出 additionalContext
├── prompts/infinite-fantasy-origins.md            # 主提示词（15,202 字节）
├── prompts/obedience-core.md                      # 行为层（1,753 字节）
├── README.md
├── NOTICE.md
└── LICENSE
SHA256SUMS                                         # 全量校验和
```

## 安装

```bash
# 1. 添加本仓库为一个市场
zcode plugins marketplace add lykwzzwzss/zcode-infinite-fantasy-origins

# 2. 安装插件
zcode plugins install infinite-fantasy-origins@zcode-infinite-fantasy

# 3. 完全退出 ZCode（含托盘）再重开
```

也可以在 ZCode 的插件页里添加市场源后点安装。安装完成后：

```bash
zcode plugins list
```

应能看到 `infinite-fantasy-origins` 且状态为 enabled。

## 验证

- 启动日志 `bootstrap.app.startup.plugins.completed` 里 `pluginCount / enabledCount`
  应随安装递增，`diagnosticCount` 不应因本插件增加；
- 新开会话，问一句「你现在遵循哪些输出约定」，回答应体现插件段落；
- 长会话（约 60K tokens 以上）之后，输出风格应保持一致。

## 兼容性

需要**支持插件 `systemPrompts` 与插件 hooks 的 ZCode 构建**。
上游官方 ZCode 不识别这两个能力，装上之后会静默不生效。

| 项 | 规则 |
| --- | --- |
| 路径解析 | `systemPrompts[].path` 相对插件根；绝对路径、`..` 越界、符号链接逃逸一律拒绝并记诊断 |
| 单段上限 | 32 KiB（本插件两段合计 16,955 字节） |
| 合计上限 | 全部启用插件 64 KiB，按 trim 后字节记账 |
| 注入位置 | 稳定 `system` 段（`source: plugin_system_prompt`），排在內建 identity 之后、动态段之前 |
| 缓存 | `cacheHint: "stable"` —— **改提示词后要重启** |
| hook 事件 | `SessionStart` / `UserPromptSubmit` / `PostCompact` |
| hook 输出 | `{"hookEventName":"…","additionalContext":"…"}` |

## 安全与信任

这个插件写入的是系统提示词层，效果上等价于调整模型的默认行为约定。请：

1. 安装前读完 `plugins/infinite-fantasy-origins/prompts/infinite-fantasy-origins.md` 全文；
2. 只安装来源可信的插件 —— 宿主会校验路径与大小，但**不会**判断提示词内容是否善意；
3. 不使用时执行 `zcode plugins disable infinite-fantasy-origins` 并重启，注入立即停止。

## 卸载

```bash
zcode plugins disable infinite-fantasy-origins
zcode plugins marketplace remove zcode-infinite-fantasy
```

## 来源与许可

MIT。衍生自 [xuanmingguo/dsh-infinite-gen-2](https://github.com/xuanmingguo/dsh-infinite-gen-2)
（`Copyright (c) 2026 0x5477`），完整衍生关系、改动清单与校验和见 [NOTICE.md](./NOTICE.md)。

- 主提示词与 DSH 版 0.5.0 逐字节相同，15,202 字节，
  sha256 `0fd8e765487c704627d6cbe26bed6bc5772d5060fb5ccf736f3d39afc4665e94`；
- 行为层为同源新增段，1,753 字节，
  sha256 `8465ec2ba0495664e499eff9581324a72bc5e4bd86064b342c89c865f3119dfa`。

> ⚠️ **免责声明**
>
> 本项目仅供学习、研究与技术交流使用，请在遵守所在地法律法规与平台服务条款的前提下使用。
