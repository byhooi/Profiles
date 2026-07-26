# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 仓库定位

本仓库是已删除的 [DivineEngine/Profiles](https://github.com/DivineEngine/Profiles/) 的备份，现由 byhooi 维护，托管 Surge、Quantumult X、Clash 三种代理工具的配置文件与分流规则。纯配置仓库，没有构建、测试、lint 命令；修改后通过 git 提交推送即可生效（客户端按订阅链接远程拉取）。

## 关键约定：远程引用地址

配置文件中所有远程引用（RULE-SET、DOMAIN-SET、script-path、模块/重写订阅等）必须指向本仓库：

```
https://raw.githubusercontent.com/byhooi/Profiles/main/<路径>
```

原上游 `DivineEngine/Profiles/master` 的链接已全部失效，发现残留一律替换。添加或修改引用时，先确认目标文件在仓库对应路径下真实存在。允许保留的 DivineEngine 字样仅限来源署名（根 README、Quantumult 两个 .conf 的首行注释）。

## 目录结构与文件关系

- `Surge/` — 主配置为 `Outbound.conf`（出国版）和 `Inbound.conf`（回国版），引用 `Surge/Ruleset/` 下的 `.list` 规则文件。`Module/` 存放 `.sgmodule` 模块，`Rewrite/` 存放重写脚本（如 `bstar.js`，被 `Module/General.sgmodule` 和 Quantumult 的重写配置共同引用）。
- `Quantumult/` — 主配置同样分 `Outbound.conf` / `Inbound.conf`。借助资源解析器（`opt-parser=true`），Quantumult 配置直接复用 `Surge/Ruleset/` 的 `.list` 和 `.sgmodule`；`Quantumult/Filter/` 是其原生格式的规则副本，`Rewrite/` 是原生重写配置。
- `Clash/` — 仅有 `RuleSet/` 下的 `.yaml` 规则文件，没有主配置文件，不含远程引用。

同一分流规则通常存在三份（`Surge/Ruleset/*.list`、`Quantumult/Filter/*.list`、`Clash/RuleSet/*.yaml`），更新规则内容时注意三处同步。

## 规则排序约束

Surge/Quantumult 主配置中的规则引用顺序不可随意调整（详见 `Surge/README.md`）：

1. Unbreak.list（必须，修正后续规则的误杀）
2. Advertising / Privacy / Hijacking（可选，Guard 类）
3. Streaming.list → StreamingSE.list
4. Global.list → Apple.list → China.list（Apple 分流默认直连，必须放在 Global 之后）

其他要点：`StreamingMedia/` 下的独立分流文件都是从 `Streaming.list` 剥离的；不需要 Streaming 策略组时应将其策略改为 PROXY 而非删除引用。
