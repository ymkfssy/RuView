# ruview — WiFi 感知的 Claude Code + Codex 插件

**RuView**（WiFi-DensePose）的端到端工具包：入门引导、ESP32 硬件设置、配置、感知应用、模型训练、高级多站感知和见证验证——从实用到高级。

属于 **`ruview` 市场**的一部分——清单位于仓库根目录：`.claude-plugin/marketplace.json`（此插件的 `source` 为 `./plugins/ruview`）。

## 安装/测试

```bash
# 在 Claude Code 中——将此仓库添加为插件市场，然后安装：
/plugin marketplace add ruvnet/RuView
/plugin install ruview@ruview

# 或在本地试用而不安装（从仓库克隆）：
claude --plugin-dir ./plugins/ruview
```

对于 Codex（OpenAI CLI），参见 [`codex/`](codex/)——所有七个 `/ruview-*` 命令镜像为 Codex 提示，以及 `AGENTS.md` 和安装说明在 [`codex/README.md`](codex/README.md) 中。

## 内容

### 技能（从 `skills/` 自动发现）

| 技能 | 功能 |
|------|------|
| `ruview-quickstart` | 入门引导与首次运行——Docker 演示、仓库构建、最快路径到实时仪表板 |
| `ruview-hardware-setup` | ESP32-S3 / C6 固件构建、烧录、WiFi 配置、串口监控 |
| `ruview-configure` | sdkconfig 变体、NVS 配置、信道/MAC 覆盖（ADR-060）、边缘模块（ADR-041）、感知服务器标志、网状网络、Cognitum Seed |
| `ruview-applications` | 运行存在检测、生命体征、姿态（WiFlow）、睡眠、环境映射、MAT、点云融合、新型 RF 应用 |
| `ruview-model-training` | 无摄像头姿态、摄像头监督姿态（92.9% PCK@20，ADR-079）、RuVector 嵌入（AETHER）、领域泛化（MERIDIAN）、本地 SNN、GCloud GPU、HF 发布 |
| `ruview-advanced-sensing` | RuvSense 多站、交叉视角融合、RF 层析成像、持久场模型、意图信号、对抗检测、网状网络安全 |
| `ruview-cli-api` | `wifi-densepose` CLI 二进制文件（包括 MAT 子命令）、REST API（`wifi-densepose-api`）、浏览器/WASM（`wifi-densepose-wasm`、`wifi-densepose-wasm-edge`） |
| `ruview-mmwave` | 毫米波 / FMCW 雷达——ESP32-C6 + MR60BHA2（60 GHz 心率/呼吸频率/存在检测）、HLK-LD2410（24 GHz）、毫米波↔CSI 融合（48 字节融合生命体征） |
| `ruview-verify` | Rust 测试、确定性 Python 证明、固件哈希、ADR-028 见证包 + 自验证、预合并检查清单 |

### 命令（`commands/`）

| 命令 | 用途 |
|------|------|
| `/ruview-start` | 开始使用——选择 Docker / 构建 / 硬件并逐步操作 |
| `/ruview-flash` | 构建 + 烧录 ESP32 固件（8MB / 4MB），确认 CSI 流 |
| `/ruview-provision` | 配置 WiFi 凭据、接收端 IP、信道 / MAC 过滤到节点 |
| `/ruview-app` | 运行感知应用 |
| `/ruview-train` | 训练 / 评估 / 发布模型（包括 GPU） |
| `/ruview-advanced` | 使用多站 / 层析成像 / 交叉视角 / 网状网络安全功能 |
| `/ruview-verify` | 运行信任流水线 + 预合并检查清单 |

### 代理（`agents/`）

| 代理 | 角色 |
|------|------|
| `ruview-onboarding-guide` | 引导新手从零到工作设置 |
| `ruview-config-engineer` | 设置/调优部署（固件、NVS、边缘模块、网状网络、Seed） |
| `ruview-training-engineer` | 训练、评估和发布模型 |

## 兼容性

- **Claude Code** — 技能、命令和代理自动发现；无需 `claude-flow` MCP 服务器（技能驱动 RuView 自己的工具：`cargo`、`python`、`idf.py`、`docker`、`node`）。可选：安全变更时参考 `npx @claude-flow/cli@latest security scan`。
- **Codex（OpenAI CLI）** — 工作流程镜像在 `codex/prompts/` 下；放入 `~/.codex/prompts/`（或将 Codex 指向 `codex/`）。`codex/AGENTS.md` 包含项目规则。
- **目标仓库** — 假设 [`ruvnet/RuView`](https://github.com/ruvnet/RuView) / `wifi-densepose` 布局：`v2/crates/`、`firmware/esp32-csi-node/`、`archive/v1/`、`scripts/`、`docs/adr/`。在 Windows 上，ESP-IDF 构建通过 `CLAUDE.local.md` 中的 Python 子进程模式进行。

## 命名空间协调

此插件为其技能、命令和代理声明了 kebab-case 的 `ruview-*` 命名空间（技能：`ruview-quickstart`、`ruview-hardware-setup`、`ruview-configure`、`ruview-applications`、`ruview-model-training`、`ruview-advanced-sensing`、`ruview-cli-api`、`ruview-mmwave`、`ruview-verify`；命令：`/ruview-start`、`/ruview-flash`、`/ruview-provision`、`/ruview-app`、`/ruview-train`、`/ruview-advanced`、`/ruview-verify`；代理：`ruview-onboarding-guide`、`ruview-config-engineer`、`ruview-training-engineer`）。它不会写入任何 `claude-flow` 内存命名空间。如果与 `ruflo` 市场结合，请参考 `ruflo-agentdb` ADR-0001 §"命名空间约定"——两者无重叠（`ruview-*` vs. `ruflo-*`）。

## 验证

```bash
bash plugins/ruview/scripts/smoke.sh
```

结构契约：plugin.json 包含 `version` + `keywords`，**不**枚举技能/命令/代理；每个技能/命令/代理文件存在且具有有效的前置元数据；README 包含兼容性部分和命名空间协调块；ADR-0001 存在且状态为 `Proposed`；技能中无通配符工具；Codex 镜像存在 **且** 对等——每个 `commands/<name>.md` 都有对应的 `codex/prompts/<name>.md`。

## 架构决策

- [`docs/adrs/0001-ruview-plugin-contract.md`](docs/adrs/0001-ruview-plugin-contract.md) — 插件契约（已提议）：结构、命名空间、兼容性接口、烟雾测试范围、Codex 镜像策略。

## 硬件说明

`COM8` 是此插件文档中的默认 ESP32 串口——已针对连接的 **ESP32-S3**（USB VID:PID `303A:1001`，Espressif）确认，运行 RuView CSI 固件（串口监视器上显示实时 `adaptive_ctrl` 滴答 + `csi_collector: CSI cb #… len=128 …`）。仓库的 `CLAUDE.local.md` 历史上引用 `COM7`；一些 README 片段引用 `COM9`。烧录前务必确认实际端口（`python -c "import serial.tools.list_ports as l; print([p.device for p in l.comports()])"`，或设备管理器）。在 Windows 上，`provision.py --help` 需要 `PYTHONUTF8=1` 才能打印（帮助文本中的非 ASCII 字符）；构建/烧录路径通过 `CLAUDE.local.md` 中的 Python 子进程模式进行（ESP-IDF v5.4 ≠ Git Bash）。
