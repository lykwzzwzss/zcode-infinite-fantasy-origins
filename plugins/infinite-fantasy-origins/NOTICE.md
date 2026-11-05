# 来源与许可（NOTICE）

## 来源

**infinite-fantasy-origins**（ZCode 插件形态）是 MIT 开源项目
**dsh-infinite-gen-2（无限二代 / Infinite Generation Two）** 的衍生作品。

| 项目 | 值 |
| --- | --- |
| 上游仓库 | https://github.com/xuanmingguo/dsh-infinite-gen-2 |
| 上游版本 | v0.2.3 |
| 上游版权 | `Copyright (c) 2026 0x5477` |
| 上游许可 | MIT License |

## 许可合规

MIT 许可要求在所有副本或实质性部分中保留原始版权声明与许可声明。本包据此执行：

1. `LICENSE` 与上游逐字一致（sha256 `4b908cad2e4721a92bdb0b71e76380bede4ee9a5345468d7398ff977d1ae8964`）；
2. 本 NOTICE 标明衍生关系、上游地址与上游版权人；
3. 提示词正文公开可对照：
   - `prompts/infinite-fantasy-origins.md`，15,202 字节，sha256 前 16 位 `0fd8e765487c7046`；
   - `prompts/obedience-core.md`，1,753 字节，sha256 前 16 位 `8465ec2ba0495664`。

## 本包与 DSH 版插件的关系

本包包含两段提示词、元数据与一个轻量 hook 脚本（`inject-hook.mjs`），不含 DSH 插件的运行时代码。
两段提示词与 DSH 侧 `dsh-infinite-fantasy-origins` **0.5.0 逐字节相同**；DSH 侧额外提供热更、
启动自检、会话投影徽标、回归评测等能力，那些能力在 ZCode 侧由宿主自己的 `systemPrompts` 机制承担。

## 提示词相对上游的改动

| 方面 | 上游 `dsh-infinite-gen-2` v0.2.3 | 本包 0.5.x |
| --- | --- | --- |
| 禁用词表 | 两份几乎重复的中英词表 | 合并为一份权威表 |
| 示例 | 无 | 新增 5 组最小对（学形态，不学字面） |
| 边界词触发 | 字面匹配 | 追加语义触发（由模糊代称推断同类场景） |
| 行为层 | 无独立行为层 | 新增 `obedience-core.md`（order 980） |
| 输出形态 | 不约束 | 首行即结论，过程不占正文 |
| 强度标定 | 无标定概念 | 与 v4 同级（100），稳定只约束输出形态 |
| 篇幅 | 10,522 字节 | 主提示词 15,202 字节 + 行为层 1,753 字节 |

## 沿用的部分

- 十层提示词组织结构（任务优先级 / 硬性输出规则 / 边界场景处理 / 任务分类 / 占位符归一化 /
  双语路由 / 边界词归一化 / 预置流程槽位 / 判定与稳定锚点）；
- 预置流程槽位（网络与 Web 检查类命令模板）的文案与触发词；
- MIT `LICENSE` 全文。

上游与本包都不提供任何明示或默示担保，详见 `LICENSE` 正文。
