# π RuView

<p align="center">
  <a href="https://cognitum.one/seed">
    <img src="assets/ruview-seed.png" alt="RuView - WiFi DensePose" width="100%">
  </a>
</p>
<p align="center">
  <a href="https://cognitum.one/seed">
    <img src="assets/seed.png" alt="Cognitum Seed" width="100%">
  </a>
</p>

## **用 WiFi 穿墙透视** ##

**将普通 WiFi 转变为空间智能/感知系统。** 检测人员、测量呼吸和心率、追踪运动、监控房间——穿墙、黑暗中、无需摄像头或可穿戴设备。只需物理原理。

原生支持四大智能家居生态系统：通过 HA-DISCO MQTT 发布器集成 **[Home Assistant](docs/integrations/home-assistant.md)**，作为可发现的 HAP-1.1 桥接器集成 **[Apple Home 和 HomePod](docs/user-guide-apple-homepod.md)**，通过同一 HA 桥接器或 [Matter](docs/adr/ADR-122-bfld-ruview-ha-matter-exposure.md) 端点集成 **[Google Home](docs/integrations/home-assistant.md)** 和 **[Amazon Alexa](docs/integrations/home-assistant.md)**。Siri、Google Assistant 和 Alexa 可以按房间语音查询存在状态和生命体征，无需任何自定义技能。

[![Works with Home Assistant](https://img.shields.io/badge/Works%20with-Home%20Assistant-blue?logo=home-assistant&logoColor=white&labelColor=41BDF5)](docs/integrations/home-assistant.md) [![Works with Matter](https://img.shields.io/badge/Works%20with-Matter-blue?labelColor=4285F4)](docs/adr/ADR-122-bfld-ruview-ha-matter-exposure.md) [![Works with Apple Home](https://img.shields.io/badge/Works%20with-Apple%20Home-black?logo=apple)](docs/user-guide-apple-homepod.md) [![Works with Google Home](https://img.shields.io/badge/Works%20with-Google%20Home-blue?logo=googlehome)](docs/integrations/home-assistant.md) [![Works with Alexa](https://img.shields.io/badge/Works%20with-Alexa-blue?logo=amazon&logoColor=white&labelColor=00CAFF)](docs/integrations/home-assistant.md)

> 通过一个 `--mqtt` 参数即可接入任何 **Home Assistant** 安装。或者作为 Matter 桥接器配对到 **Apple Home / Google Home / Alexa / SmartThings**。每个节点提供 21 个实体（11 个原始信号 + 10 个推断语义状态：有人睡觉、可能遇险、房间活跃、老年人活动异常、会议进行中、浴室占用、跌倒风险升高、离床、无移动、多房间切换）以及 3 个入门级 HA Blueprints。详见 [`docs/integrations/home-assistant.md`](docs/integrations/home-assistant.md) · [ADR-115](docs/adr/ADR-115-home-assistant-integration.md)。

### π RuView 是一个 WiFi 感知平台，将无线电信号转化为空间智能。

每个 WiFi 路由器都已经用无线电波充满你的空间。当人们移动、呼吸甚至静坐时，他们都会以可测量的方式扰动这些电波。RuView 通过低成本的 ESP32 传感器捕获信道状态信息（CSI）来捕捉这些扰动，并将其转化为可操作的数据：谁在那里、他们在做什么、以及他们是否安好。

**它能感知的内容：**
- **存在和占用** — 穿墙检测人员、计数、追踪进出
- **生命体征** — 呼吸频率和心率，非接触式，睡眠或静坐时均可
- **活动识别** — 行走、坐姿、手势、跌倒——来自时间 CSI 模式
- **环境映射** — 射频指纹识别房间、检测家具移动、发现新物体
- **睡眠质量** — 整夜监测，包含睡眠阶段分类和呼吸暂停筛查

基于 [RuVector](https://github.com/ruvnet/ruvector/) 和 [Cognitum Seed](https://cognitum.one) 构建，RuView 完全在边缘硬件上运行——一个 ESP32 网状网络（每个节点低至 9 美元）配合 Cognitum Seed 实现持久内存、加密认证和 AI 集成。无需云、无需摄像头、无需互联网。

该系统使用脉冲神经网络在本地学习每个环境，30 秒内即可自适应，通过 6 个 WiFi 信道的多频网状扫描，利用邻居的路由器作为免费的雷达照射源。每次测量都通过 Ed25519 见证链进行加密认证。

RuView 将普通 WiFi 转变为非接触式传感器。一块 9 美元的 ESP32 开发板读取房间内人体反射的无线电波，一个小型预训练模型——发布在 Hugging Face 的 [`ruvnet/wifi-densepose-pretrained`](https://huggingface.co/ruvnet/wifi-densepose-pretrained) 上——告诉你谁在那里、他们的呼吸情况以及心率趋势。该模型仅需 8 KB（4 位量化），在树莓派上微秒级运行。（[v2 编码器](https://huggingface.co/ruvnet/wifi-densepose-pretrained) 报告了诚实的、无标签的保留集 **时间三元组准确率 82.3%**——从 66.4% 原始值提升；旧的"100% 存在检测"数据是在单类记录上测量的，已被撤回。）无需摄像头、无需可穿戴设备、无需用户手机上的应用。

### 专为低功耗边缘应用而构建

[边缘模块](#边缘智能-adr-041) 是直接在 ESP32 传感器上运行的小程序——无需互联网、无需云费用、即时响应。

[![Rust 1.85+](https://img.shields.io/badge/rust-1.85+-orange.svg)](https://www.rust-lang.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Tests: 1463](https://img.shields.io/badge/tests-1463%20passed-brightgreen.svg)](https://github.com/ruvnet/RuView)
[![Docker: multi-arch](https://img.shields.io/badge/docker-amd64%20%2B%20arm64-blue.svg)](https://hub.docker.com/r/ruvnet/wifi-densepose)
[![Vital Signs](https://img.shields.io/badge/vital%20signs-breathing%20%2B%20heartbeat-red.svg)](#生命体征检测)
[![ESP32 Ready](https://img.shields.io/badge/ESP32--S3-CSI%20streaming-purple.svg)](#esp32-s3-硬件流水线)
[![crates.io](https://img.shields.io/crates/v/wifi-densepose-ruvector.svg)](https://crates.io/crates/wifi-densepose-ruvector)
[![Downloads](https://img.shields.io/badge/downloads-10M%2B-brightgreen.svg)](#-边缘模块目录)

 
> | 功能 | 方法 | 速度/范围 |
> |------|------|----------|
> | 🫁 **呼吸频率** | 对包裹相位进行 0.1–0.5 Hz 带通滤波、循环方差、过零 BPM（[#593](https://github.com/ruvnet/RuView/issues/593)） | 6–30 BPM，实时 |
> | 💓 **心率** | 0.8–2.0 Hz 带通滤波、过零 BPM | 40–120 BPM，实时 |
> | 👤 **存在检测** | Hugging Face 上的训练模型（[`ruvnet/wifi-densepose-pretrained`](https://huggingface.co/ruvnet/wifi-densepose-pretrained)；v2 编码器 = 82.3% 保留集时间三元组准确率，已诚实重新基准测试）+ 无需模型的相位方差回退方案 | < 1 ms，约 30 秒环境校准 |
> | 🧬 **CSI 嵌入** | 128 维对比编码器，发布在 Hugging Face 上，4 位量化版本仅 8 KB | M4 Pro 上 **164,183 嵌入/秒** |
> | 🦴 **17 关键点姿态估计** | `cog-pose-estimation` Cog v0.0.1——GCS 上签名的 aarch64 + x86_64 二进制文件，通过 Candle 加载 `pose_v1.safetensors`。使用配对数据在 RTX 5080 上 2.1 秒训练自己的模型（[ADR-101](docs/adr/ADR-101-pose-estimation-cog.md)，[基准测试](docs/benchmarks/pose-estimation-cog.md)）。**MM-Fi 上的 SOTA：** [`ruvnet/wifi-densepose-mmfi-pose`](https://huggingface.co/ruvnet/wifi-densepose-mmfi-pose) 在匹配的 MM-Fi `random_split` 协议上达到 **82.69% torso-PCK@20**（集成 83.59%），超越 MultiFormer（72.25%）和 CSI2Pose（68.41%）——可在 [AetherArena](https://huggingface.co/spaces/ruvnet/aether-arena) 上自我纠正和审计 | Pi 5 上冷启动 8.4 ms |
> | 🚶 **运动/活动** | 运动频带功率 + 相位加速度 | 实时 |
> | 🤸 **跌倒检测** | 相位加速度阈值 + 3 帧去抖 + 5 秒冷却（[#263](https://github.com/ruvnet/RuView/issues/263)） | < 200 ms |
> | 🧮 **多人计数** | 自适应 P95 归一化 + 运行时可调去重因子（`/api/v1/config/dedup-factor`，[#491](https://github.com/ruvnet/RuView/pull/491)）。六个专用学习计数器可作为 Cogs 使用：`occupancy-zones`、`elevator-count`、`queue-length`、`customer-flow`、`clean-room`、`person-matching` | 实时，自校准 |
> | 🌍 **世界模型预测** | OccWorld TransVQVAE——15 帧未来占用预测，209 ms 推理，RTX 5080 上 3.4 GB VRAM；使用 `occworld_retrain.py` 在你的空间上微调（[ADR-147](docs/adr/ADR-147-nvidia-cosmos-world-foundation-model-integration.md)） | 15 帧 × 200×200×16 体素 |
> | 🧱 **穿墙感知** | 菲涅尔区几何 + 多径建模 | 最远约 5 米，取决于信号 |
> | 🧠 **边缘智能** | **105 个 cog 目录**（[ADR-102](docs/adr/ADR-102-edge-module-registry.md)）来自 `app-registry.json`——健康、安全、建筑、零售、工业、研究、AI、群集、信号、网络和开发者模块。可选的 Cognitum Seed 添加持久向量存储 + kNN + 见证链 | 总 BOM 成本 140 美元 |
> | 🎯 **无摄像头预训练** | 自监督对比编码器，60K 帧上 12.2M 训练步，发布在 Hugging Face 上 | M4 Pro 上每轮重训练 84 秒 |
> | 📷 **摄像头监督微调** | MediaPipe + ESP32 CSI 配对训练，RTX 5080 上的端到端 Candle 流水线（[ADR-079](docs/adr/ADR-079-camera-supervised-pose-finetune.md)） | 400 轮 2.1 秒（约 5 ms/轮） |
> | 📡 **多频网状网络** | 6 个频段间信道跳频，TDM 时隙调度（[ADR-029](docs/adr/ADR-029-multifrequency-mesh.md)） | 3 倍感知带宽 |
> | 🌐 **3D 点云融合** | 摄像头深度（MiDaS）+ WiFi CSI + 毫米波雷达 → 统一空间模型 | 22 ms 流水线 · 每帧 19K+ 点 |
>
> 浏览完整的 105 模块目录（含实用描述、大小和难度），请见下方 [🧩 边缘模块目录](#-边缘模块目录)，或访问 [seed.cognitum.one/store](https://seed.cognitum.one/store)。
>
> 🤗 **预训练权重**：从 [`ruvnet/wifi-densepose-pretrained`](https://huggingface.co/ruvnet/wifi-densepose-pretrained) 下载——详见下方 [加载预训练模型](#加载预训练模型) 的一键设置。

```bash
# 选项 1：Docker（模拟数据，无需硬件）
docker pull ruvnet/wifi-densepose:latest
docker run -p 3000:3000 ruvnet/wifi-densepose:latest
# 打开 http://localhost:3000

# 选项 2a：使用 ESP32-S3 硬件进行实时感知（9 美元）
# 刷写固件、配置 WiFi 并开始感知：
python -m esptool --chip esp32s3 --port COM9 --baud 460800 \
  write_flash 0x0 bootloader.bin 0x8000 partition-table.bin \
  0xf000 ota_data_initial.bin 0x20000 esp32-csi-node.bin
python firmware/esp32-csi-node/provision.py --port COM9 \
  --ssid "你的WiFi" --password "密码" --target-ip 192.168.1.20

# 选项 2b：使用 ESP32-C6 进行 WiFi 6 + 802.15.4 研究感知（6-10 美元，ADR-110）
# 相同的 csi-node 固件为 C6 目标编译——自动选择 C6
# 覆盖配置（sdkconfig.defaults.esp32c6）。
cd firmware/esp32-csi-node
idf.py set-target esp32c6 && idf.py build
idf.py -p COM6 flash
# C6 启动额外功能（相对于 S3）：ADR-018 字节 18-19 中的 HE-LTF 子载波标记，
#   信道 15 上的 802.15.4 网状网络时间同步，AP 支持时的 TWT 设置，
#   可选的 LP-core 运动唤醒功能，适用于约 5 µA 电池种子节点。
# v0.6.7 新增：真正的 LP-core RISC-V 运动门控程序（去抖 + 运动
#   计数器）和 Wi-Fi 6 软 AP（含 TWT Responder），使两块 C6 板可以
#   在不购买 11ax 路由器的情况下基准测试真实 iTWT。两者默认关闭，
#   打开 CONFIG_C6_{LP_CORE,SOFTAP_HE}_ENABLE 即可启用。

# 选项 3：完整系统搭配 Cognitum Seed（140 美元）
# ESP32 流式传输 CSI → 桥接转发到 Seed 进行持久存储 + kNN + 见证链
node scripts/rf-scan.js --port 5006           # 实时射频房间扫描
node scripts/snn-csi-processor.js --port 5006  # SNN 实时学习
node scripts/mincut-person-counter.js --port 5006  # 正确的人员计数

# 选项 4：Python——在 PyPI 上实时可用（ADR-117）
pip install ruview                        # 或：pip install wifi-densepose
# 两者都提供相同的编译 PyO3 wheel（约 250 KB，abi3-py310，Linux/macOS/Windows）。
# 添加 [client] 以获取 asyncio WebSocket + paho-mqtt 客户端：
pip install "ruview[client]"              # 或：pip install "wifi-densepose[client]"

# from ruview import BreathingExtractor, HeartRateExtractor   # 等同于：
# from wifi_densepose import BreathingExtractor, HeartRateExtractor
# from ruview.client import SensingClient, RuViewMqttClient
```

[![PyPI ruview](https://img.shields.io/pypi/v/ruview?label=ruview)](https://pypi.org/project/ruview/) [![PyPI wifi-densepose](https://img.shields.io/pypi/v/wifi-densepose?label=wifi-densepose)](https://pypi.org/project/wifi-densepose/)

> [!NOTE]
> **建议使用支持 CSI 的硬件。** 存在检测、生命体征、穿墙感知和所有高级功能都需要来自 ESP32-S3（9 美元）或研究用网卡的信道状态信息（CSI）。Docker 镜像使用模拟数据运行以供评估。消费级 WiFi 笔记本电脑仅提供基于 RSSI 的存在检测。

> **实时 CSI 捕获的硬件选项：**
>
> | 选项 | 硬件 | 成本 | 完整 CSI | 功能 |
> |------|------|------|----------|------|
> | **ESP32 + Cognitum Seed**（推荐） | ESP32-S3 + [Cognitum Seed](https://cognitum.one) | 约 140 美元 | 是 | 存在检测、运动、呼吸、心率、跌倒检测、多人计数、17 关键点姿态（签名的 Cog 二进制文件）、105 个 cog 目录、持久向量存储、kNN 搜索、见证链、MCP 代理 |
> | **ESP32 网状网络** | 3-6 个 ESP32-S3 + WiFi 路由器 | 约 54 美元 | 是 | 与上述相同，但不含持久内存功能 |
> | **ESP32-C6 研究节点**（[ADR-110](docs/adr/ADR-110-esp32-c6-firmware-extension.md)、[见证日志](docs/WITNESS-LOG-110.md)、[评审指南](docs/ADR-110-REVIEW-GUIDE.md)、[固件 v0.7.0](https://github.com/ruvnet/RuView/releases/tag/v0.7.0-esp32)） | ESP32-C6-DevKit（6-10 美元） | 约 10 美元 | 是（支持 Wi-Fi 6） | 与 S3 相同的 CSI 流水线，使用双目标固件。**固件端 ADR-110 基础现已关闭**（v0.7.0）：ESP-NOW 跨板网状网络量化结果为 **99.56% 匹配率 / 104 µs 平滑偏移标准差 / 3.95 倍 EMA 抑制**，经过 5 分钟双板浸泡测试（见证日志 §A0.10），32 字节 UDP 同步包，操作员可调节奏（§A0.12），ADR-018 字节 19 位 4 的线修复源自工作的 ESP-NOW 路径（§A0.13）。线格式已准备好用于 ADR-018 字节 18-19 中的 HE-LTF PPDU 标记（固件编码器 + Rust + Python 解码器经过 23 个单元测试的端到端验证）。LP-core 运动门控 RISC-V 程序和带 TWT Responder 的 Wi-Fi 6 软 AP 均作为可选代码路径提供（默认关闭）。**硬件限制的测量**：HE-LTF 实时子载波捕获需要 11ax AP（IDF v5.4 不暴露 AP 端 HE 配置——§A0.6）；约 5 µA LP-core 休眠需要 INA 仪表来捕获；802.15.4 原始 RX 在 IDF v5.4 中存在问题（解决方法：ESP-NOW 传输，已提供并测量）。请参阅见证日志了解经验/声明数据的分割。 |
> | **研究用网卡** | Intel 5300 / Atheros AR9580 | 约 50-100 美元 | 是 | 完整的 3x3 MIMO CSI |
> | **任意 WiFi** | Windows、macOS 或 Linux 笔记本电脑 | 0 美元 | 否 | 仅 RSSI：粗略的存在检测和运动（参见[教程 #36](https://github.com/ruvnet/RuView/issues/36)） |
>
> 没有硬件？使用确定性参考信号验证信号处理流水线：`python archive/v1/data/proof/verify.py`
>
---


  <a href="https://ruvnet.github.io/RuView/">
    <img src="assets/v2-screen.png" alt="WiFi DensePose — Live pose detection with setup guide" width="800">
  </a>
  <br>
  <em>来自 WiFi CSI 信号的实时姿态骨架——无需摄像头、无需可穿戴设备</em>
  <br><br>
  <a href="https://ruvnet.github.io/RuView/"><strong>▶ 实时观测站演示</strong></a>
  &nbsp;|&nbsp;
  <a href="https://ruvnet.github.io/RuView/pose-fusion.html"><strong>▶ 双模态姿态融合演示</strong></a>
  &nbsp;|&nbsp;
  <a href="https://ruvnet.github.io/RuView/pointcloud/"><strong>▶ 实时 3D 点云</strong></a>
  &nbsp;|&nbsp;
  <a href="https://ruvnet.github.io/RuView/three.js/"><strong>▶ three.js 演示（5 个）</strong></a>

> [服务器](#-快速开始) 对于可视化和聚合是可选的——ESP32 [独立运行](#esp32-s3-硬件流水线) 进行存在检测、生命体征和跌倒警报。
>
> **实时 ESP32 流水线**：连接 ESP32-S3 节点 → 运行[感知服务器](#感知服务器) → 打开[姿态融合演示](https://ruvnet.github.io/RuView/pose-fusion.html)进行实时双模态姿态估计（网络摄像头 + WiFi CSI）。参见 [ADR-059](docs/adr/ADR-059-live-esp32-csi-pipeline.md)。
>
> **three.js 场景画廊**位于 [`/three.js/`](https://ruvnet.github.io/RuView/three.js/)——五个逐步丰富的 ADR-097 演示：辅助工具、电影级、GLTF 蒙皮、FBX 蒙皮，以及由 ESP32 CSI 驱动的实时 MediaPipe→Mixamo 重定向流。演示 04 和 05 需要本地 Mixamo `X Bot.fbx` 文件（许可限制——不重新分发）。


## 🤗 Hugging Face 上的预训练模型

预训练 CSI 权重位于 [`ruvnet/wifi-densepose-pretrained`](https://huggingface.co/ruvnet/wifi-densepose-pretrained)——在 60K 帧 / 610K 对比三元组上进行了 12.2M 训练步，**82.3% 保留集时间三元组准确率**（从 66.4% 原始值提升；旧的"100% 存在检测"数据是在单类记录上测量的，已被撤回），4 位量化版本仅 8 KB。该发布包含一个产生 128 维嵌入的对比 **CSI 编码器**（M4 Pro 上 164,183 嵌入/秒）和一个**存在检测头**。包含每个节点的 LoRA 适配器，用于特定环境的微调。

```bash
# 下载模型包
pip install huggingface_hub
huggingface-cli download ruvnet/wifi-densepose-pretrained --local-dir models/wifi-densepose-pretrained
```

**当前可用的功能与待接线功能：**

| 消费者 | 格式 | 状态 |
|--------|------|------|
| Python 训练/评估/嵌入提取 | `model.safetensors` | ✅ 可用——使用 `safetensors.torch.load_file` 加载 |
| 检查/重新导出包 | `model.rvf.jsonl`（逐行 JSON） | ✅ 可用——纯 JSONL |
| 感知服务器 `--model <路径>` 参数 | 二进制 RVF（`RVFS` 魔术字） | ⚠️ 加载器尚不支持 JSONL 容器 |

**已知差距：** HF 模型以 JSONL RVF 格式发布，但 `v2/crates/wifi-densepose-sensing-server/src/rvf_container.rs` 仅解析二进制 RVF 段格式。将 `--model` 指向 `model.rvf.jsonl` 当前会报错 `invalid magic at offset 0: expected 0x52564653, got 0x7974227B`，并且实时流水线会降级为空输出而非回退到启发式模式——因此对于实时感知服务器，在 JSONL 适配器就绪（或模型重新以二进制 RVF 发布）之前，请**不带** `--model` 运行。在此期间，可从 Python/训练中使用权重。

**量化选项**（均在 HF 仓库中）：`model-q2.bin`（4 KB）· `model-q4.bin` ⭐ 推荐（8 KB）· `model-q8.bin`（16 KB）· `model.safetensors` 完整版（48 KB）

独立的 **17 关键点姿态估计模型**现已发布在 [`ruvnet/wifi-densepose-mmfi-pose`](https://huggingface.co/ruvnet/wifi-densepose-mmfi-pose)——在 MM-Fi 上达到 **82.69% torso-PCK@20**（单模型）/ **83.59%**（3 模型集成 + TTA），在匹配的 `random_split` 协议上超越了先前发布的 SOTA MultiFormer（72.25%）和 CSI2Pose（68.41%）。详见下方**结果与证明**。

### 结果与证明

| 内容 | 位置 | 数据 |
|------|------|------|
| **MM-Fi 姿态模型（SOTA）** | [`ruvnet/wifi-densepose-mmfi-pose`](https://huggingface.co/ruvnet/wifi-densepose-mmfi-pose) | 82.69% torso-PCK@20（单模型）· 83.59%（集成+TTA）· 75K 参数微缩版 74.30% |
| **AetherArena 基准测试空间** | [`ruvnet/aether-arena`](https://huggingface.co/spaces/ruvnet/aether-arena) | 自我纠正、可审计的 MM-Fi 排行榜 |
| **完整 MM-Fi 研究（真实情况）** | [`docs/benchmarks/mmfi-wifi-sensing-study.md`](docs/benchmarks/mmfi-wifi-sensing-study.md) | 姿态 + 动作；零样本跨主体约 64%，+约 30 秒室内校准 → 72.2% |
| **效率前沿** | [`docs/benchmarks/wifi-pose-efficiency-frontier.md`](docs/benchmarks/wifi-pose-efficiency-frontier.md) | 20 KB int4 边缘模型中超越 SOTA 的 WiFi 姿态 |
| **预训练编码器** | [`ruvnet/wifi-densepose-pretrained`](https://huggingface.co/ruvnet/wifi-densepose-pretrained) | 82.3% 保留集时间三元组准确率，8 KB int4 |
| **可复现证明（信任终止开关）** | [`archive/v1/data/proof/verify.py`](archive/v1/data/proof/verify.py) + [`expected_features.sha256`](archive/v1/data/proof/expected_features.sha256) | 一键确定性流水线重放（输出 SHA-256 与发布哈希对比） |
| **基准测试证明 ADR** | [ADR-147](docs/adr/ADR-147-benchmark-proof.md) | 数据如何生成和验证 |
| **见证认证** | [`docs/WITNESS-LOG-028.md`](docs/WITNESS-LOG-028.md) | 33 行能力认证矩阵，每项声明附有证据 |

```bash
# 自行复现确定性流水线证明（必须输出 VERDICT: PASS）：
python archive/v1/data/proof/verify.py
```

追踪于 [#509](https://github.com/ruvnet/RuView/issues/509)；摄像头监督微调路径参见 [ADR-079](docs/adr/ADR-079-camera-supervised-pose-finetune.md) 的 P7–P9 阶段。


## 🧩 边缘模块目录

<details>
<summary><b>🧩 105 个边缘模块，可直接安装在 Cognitum 设备上</b> &mdash; 来自 <code>app-registry.json</code> v2.1.0 的实时目录（更新于 2026-05-13）。在 <a href="https://seed.cognitum.one/store">seed.cognitum.one/store</a> 或本地设备 <code>http://&lt;设备地址&gt;:9000/cogs</code> 浏览和安装。</summary>

每个模块是一个小型签名二进制文件（约 400 KB），与 WiFi-DensePose 感知栈一起在 Cognitum-V0 设备上运行。目录通过无线方式更新——你的设备通过 <code>GET /api/v1/edge/registry</code>（[ADR-102](docs/adr/ADR-102-edge-module-registry.md)）获取目录，并在安装前使用 Ed25519 签名（[ADR-100](docs/adr/ADR-100-cog-packaging-specification.md)）验证每个二进制文件。

### 🫀 健康 &mdash; <sub>14 个模块</sub>

| ID | 功能 | 大小 | 难度 |
|----|------|-----:|:----:|
| `air-quality-index` | 使用 CO2 和颗粒物传感器追踪室内空气质量 | 8 KB | 简单 |
| `baby-cry` | 持续中频能量检测器，用于育婴室/婴儿监控。仅音频，无摄像头。 | 451 KB | 简单 |
| `breathing-sync` | 检测两人何时同步呼吸 | 10 KB | 困难 |
| `cardiac-arrhythmia` | 发现不规则心跳和异常心律 | 8 KB | 困难 |
| `cough-detect` | 声学瞬态 + 频谱咳嗽检测器，带 30 秒聚类聚合。呼吸道疾病的早期预警信号。 | 451 KB | 简单 |
| `dream-stage` | 追踪你的睡眠阶段——浅睡、深睡和做梦 | 14 KB | 困难 |
| `fall-detect` | 基于环境特征流（ESP32 运动/麦克风）的两级冲击 + 静止跌倒检测器。可选的 ruview 模式用于基于 CSI 的姿态增强。 | 402 KB | 简单 |
| `gait-analysis` | 检测行走问题并评估跌倒风险 | 12 KB | 困难 |
| `health-monitor` | 非接触式心率、呼吸、睡眠和跌倒警报 | 30 KB | 中等 |
| `respiratory-distress` | 当呼吸变得费力或危险地急促时发出警报 | 10 KB | 困难 |
| `seizure-detect` | 识别癫痫发作并发送即时警报 | 10 KB | 困难 |
| `sleep-apnea` | 检测某人睡眠中是否停止呼吸 | 4 KB | 简单 |
| `snore-monitor` | 周期性低频能量追踪器，用于睡眠质量/呼吸暂停风险趋势分析。与 sleep-apnea cog 配合使用。 | 451 KB | 简单 |
| `vital-trend` | 追踪数周内的呼吸和心率趋势 | 6 KB | 中等 |

### 🔒 安全 &mdash; <sub>14 个模块</sub>

| ID | 功能 | 大小 | 难度 |
|----|------|-----:|:----:|
| `audit-logger` | 记录每个操作以符合合规要求——防篡改日志 | 8 KB | 简单 |
| `behavioral-profiler` | 学习正常行为并标记任何异常情况 | 12 KB | 困难 |
| `fleet-auth` | 管理所有 seed 的设备证书和访问权限 | 12 KB | 中等 |
| `glass-break` | 两阶段砰击 + 破碎声学检测器。区分玻璃破碎与普通脉冲噪声。 | 451 KB | 简单 |
| `gunshot-detect` | 饱和峰值 + 指数衰减声学检测器，可选的 ruview CSI 运动下降增强。 | 451 KB | 简单 |
| `intrusion` | 当未经授权的人员进入房间时发出警报 | 6 KB | 中等 |
| `intrusion-detect-ml` | 使用机器学习检测网络攻击 | 14 KB | 困难 |
| `loitering` | 当某人在一个地方逗留过久时发出警报 | 3 KB | 简单 |
| `network-firewall` | 按 cog 阻止未经授权的网络访问 | 6 KB | 简单 |
| `panic-motion` | 检测突然的恐慌或异常运动 | 6 KB | 中等 |
| `perimeter-breach` | 守卫多个区域并显示进入方向 | 10 KB | 中等 |
| `prompt-shield` | 阻止对 seed 的信号重放和注入攻击 | 10 KB | 中等 |
| `tailgating` | 捕捉尾随在持证人员身后潜入的行为 | 6 KB | 中等 |
| `weapon-detect` | 检测人员身上隐藏的金属物体 | 8 KB | 困难 |

### 🏢 建筑 &mdash; <sub>11 个模块</sub>

| ID | 功能 | 大小 | 难度 |
|----|------|-----:|:----:|
| `beehive-monitor` | 声学蜂箱状态分类器。通过嗡嗡声能量 + 混沌度 + 管道自相关检测健康/混乱/失王/分蜂/盗蜂状态。 | 451 KB | 简单 |
| `elevator-count` | 统计电梯内的人数 | 8 KB | 中等 |
| `energy-audit` | 学习你的日程安排并减少能源浪费 | 6 KB | 中等 |
| `frost-warning` | 通过温度趋势 + 露点差阈值提前 6 小时预测霜冻。适用于田地/果园农业。 | 451 KB | 简单 |
| `hvac-presence` | 当你到达时自动开启供暖和制冷 | 3 KB | 简单 |
| `lighting-zones` | 当人们在房间之间移动时自动开关灯 | 4 KB | 简单 |
| `meeting-room` | 显示会议室是否空闲或占用 | 5 KB | 简单 |
| `occupancy-zones` | 穿墙统计每个房间的人数 | 8 KB | 中等 |
| `predictive-maintenance` | 旋转设备的振动谐波分析器。追踪 F1 / 2×F1 / 高阶 / 边带能量以评估退化严重程度。 | 451 KB | 简单 |
| `smoke-fire` | 多信号烟雾和火灾检测器。融合声学爆裂声、热漂移代理和可选的 ruview CSI 烟羽特征。不能替代法规要求的 UL 认证烟雾报警器。 | 451 KB | 简单 |
| `water-leak` | 持续低振幅嘶嘶声 + 周期性滴水的声学检测器，带多分钟持续门控。两阶段：可能 → 确认。 | 451 KB | 简单 |

### 🛍️ 零售 &mdash; <sub>7 个模块</sub>

| ID | 功能 | 大小 | 难度 |
|----|------|-----:|:----:|
| `customer-flow` | 统计每个入口进出的人流量 | 8 KB | 中等 |
| `dwell-heatmap` | 显示顾客在哪些区域停留时间最长 | 6 KB | 中等 |
| `package-detect` | 持续 CSI 偏移检测器，用于门廊/装卸区包裹到达和离开。需要 ESP32 CSI ruview 输入。 | 451 KB | 简单 |
| `parking-occupancy` | 通过 ESP32 CSI 子载波幅度偏移检测各区域停车占用情况。追踪利用率和每小时周转率。需要 ruview。 | 451 KB | 简单 |
| `queue-length` | 估算排队长度和等待时间 | 6 KB | 中等 |
| `shelf-engagement` | 检测顾客何时与商品互动 | 6 KB | 中等 |
| `table-turnover` | 追踪哪些餐桌空闲或占用 | 4 KB | 简单 |

### 🏭 工业 &mdash; <sub>7 个模块</sub>

| ID | 功能 | 大小 | 难度 |
|----|------|-----:|:----:|
| `clean-room` | 在受控环境中强制执行最大人数限制 | 4 KB | 简单 |
| `confined-space` | 监控狭小空间内工人的安全 | 5 KB | 中等 |
| `forklift-proximity` | 当叉车过于靠近工人时发出警告 | 10 KB | 困难 |
| `livestock-monitor` | 监控动物的 distress、逃跑或疾病状态 | 6 KB | 中等 |
| `ppe-compliance` | Cog 组合层：当 ruview-densepose 检测到受限区域有人但缺少 PPE 摄像头 cog 确认向量时发出警报。 | 387 KB | 简单 |
| `slip-fall-zone` | 跌倒前风险检测器。当运动方差下降、溅水声和可选的谨慎步态 CSI 都指示滑倒风险升高时触发。 | 451 KB | 简单 |
| `structural-vibration` | 检测建筑物或机器中的危险振动 | 8 KB | 困难 |

### 🔬 研究 &mdash; <sub>12 个模块</sub>

| ID | 功能 | 大小 | 难度 |
|----|------|-----:|:----:|
| `emotion-detect` | 从肢体语言和呼吸中读取压力和平静状态 | 10 KB | 困难 |
| `energy-harvester` | 优化离网 seed 部署的太阳能和电池使用 | 6 KB | 中等 |
| `gesture-language` | 实时识别手语手势 | 12 KB | 困难 |
| `ghost-hunter` | 发现无法解释的环境异常——娱乐用途 | 10 KB | 困难 |
| `happiness-score` | 从运动和情绪信号估计幸福感 | 8 KB | 中等 |
| `hyperbolic-space` | 将数据映射到弯曲空间以处理树状结构 | 12 KB | 困难 |
| `music-conductor` | 读取指挥家的手势以获取节奏和力度 | 12 KB | 困难 |
| `plant-growth` | 追踪植物生长速率和昼夜周期 | 8 KB | 中等 |
| `rain-detect` | 检测何时开始下雨、停止以及雨量大小 | 6 KB | 中等 |
| `ruview-densepose` | 通过 WiFi 进行全身姿态追踪——无需摄像头 | 50 KB | 困难 |
| `sound-classifier` | 识别玻璃破碎、警报或婴儿哭声等声音 | 16 KB | 困难 |
| `time-crystal` | 重复时间模式对称性实验 | 12 KB | 困难 |

### 🤖 AI &mdash; <sub>15 个模块</sub>

| ID | 功能 | 大小 | 难度 |
|----|------|-----:|:----:|
| `anomaly-attractor` | 学习正常模式并捕捉任何异常情况 | 10 KB | 困难 |
| `cognitive-pipeline` | FastGRNN 异常门控 + SmolLM2 稀疏 LLM 推理，用于设备端 Pi Zero 2W 认知事件 | 320 KB | 困难 |
| `dtw-gesture-learn` | 通过展示示例来教授自定义手势 | 14 KB | 中等 |
| `ewc-lifelong` | 学习新知识而不忘记旧知识 | 8 KB | 困难 |
| `federated-learning` | 跨 seed 训练 AI 而无需共享原始数据 | 18 KB | 困难 |
| `goap-autonomy` | 自主规划和执行目标 | 14 KB | 困难 |
| `meta-adapt` | 自动调整自身以获得最佳性能 | 10 KB | 困难 |
| `micro-hnsw` | 快速的设备端指纹识别和分类 | 12 KB | 中等 |
| `neural-trader` | 从实时数据中发现市场模式和趋势 | 20 KB | 困难 |
| `pagerank-influence` | 找出群体中最有影响力的人 | 12 KB | 中等 |
| `pattern-sequence` | 检测日常规律和重复习惯 | 10 KB | 中等 |
| `rag-local` | 使用 AI 搜索文档——在 seed 上运行 | 14 KB | 中等 |
| `spiking-tracker` | 受大脑启发的追踪器，在微型硬件上运行 | 16 KB | 困难 |
| `temporal-logic` | 在实时事件流上强制执行安全规则 | 12 KB | 困难 |
| `time-series-forecast` | 使用历史模式预测传感器趋势 | 12 KB | 中等 |

### 🐝 群集 &mdash; <sub>11 个模块</sub>

| ID | 功能 | 大小 | 难度 |
|----|------|-----:|:----:|
| `swarm-backup-restore` | 自动备份数据到其他 seed——一键恢复 | 8 KB | 简单 |
| `swarm-cluster-monitor` | 每个 seed 健康和状态的实时仪表板 | 6 KB | 简单 |
| `swarm-consensus` | seed 在共同做出关键更改前进行投票 | 16 KB | 困难 |
| `swarm-delta-sync` | seed 之间自动同步数据——仅发送变更 | 8 KB | 中等 |
| `swarm-deploy` | 一次性在所有 seed 上安装或移除 cogs | 10 KB | 中等 |
| `swarm-distributed-store` | 跨 seed 分布数据并一次性搜索所有数据 | 14 KB | 困难 |
| `swarm-edge-orchestrator` | 从一个位置管理所有 ESP32 传感器节点 | 14 KB | 困难 |
| `swarm-load-balancer` | 跨 seed 分发查询，避免单个 seed 过载 | 10 KB | 中等 |
| `swarm-mesh-manager` | 发现、连接和监控网络上的所有 seed | 12 KB | 简单 |
| `swarm-mqtt-bridge` | 通过 MQTT 消息在 seed 之间共享事件 | 6 KB | 简单 |
| `swarm-witness-federation` | 跨 seed 共享防篡改审计追踪 | 12 KB | 困难 |

### 📡 信号 &mdash; <sub>6 个模块</sub>

| ID | 功能 | 大小 | 难度 |
|----|------|-----:|:----:|
| `coherence-gate` | 过滤噪声信号并保留干净信号 | 8 KB | 中等 |
| `flash-attention` | 将感知集中在特定区域以提高准确性 | 12 KB | 中等 |
| `optimal-transport` | 使用形状感知信号比较来测量运动 | 12 KB | 困难 |
| `person-matching` | 区分同一房间内的多人 | 18 KB | 困难 |
| `sparse-recovery` | 从部分读数中恢复缺失的信号数据 | 16 KB | 困难 |
| `temporal-compress` | 压缩旧数据以节省内存而不丢失含义 | 14 KB | 中等 |

### 🌐 网络 &mdash; <sub>1 个模块</sub>

| ID | 功能 | 大小 | 难度 |
|----|------|-----:|:----:|
| `tailscale` | 通过私有 WireGuard 网状网络（Tailscale）从任何地方访问 seed。用户态模式——无需 root。 | 700 KB | 中等 |

### 🛠️ 开发者 &mdash; <sub>7 个模块</sub>

| ID | 功能 | 大小 | 难度 |
|----|------|-----:|:----:|
| `adversarial` | 检测被篡改或伪造的传感器信号 | 4 KB | 简单 |
| `coherence` | 监控多个信道的信号质量 | 4 KB | 简单 |
| `gesture` | cog 的核心手势识别构建块 | 6 KB | 中等 |
| `interference-search` | 同时搜索多种可能性以快速获得答案 | 14 KB | 困难 |
| `psycho-symbolic` | 使用多种风格在知识图谱上进行推理 | 16 KB | 困难 |
| `quantum-coherence` | 受量子启发的先进信号状态模型 | 16 KB | 困难 |
| `self-healing-mesh` | 即使节点离线也能保持传感器网状网络运行 | 14 KB | 困难 |

> ℹ️ 构建自己的 cog：参见 [ADR-100](docs/adr/ADR-100-cog-packaging-specification.md) 了解打包规范。此仓库发布到目录的第一个 cog 位于 [v2/crates/cog-pose-estimation/](v2/crates/cog-pose-estimation/)（17 关键点 WiFi 姿态，[ADR-101](docs/adr/ADR-101-pose-estimation-cog.md)）。

</details>


## 🔬 工作原理

WiFi 路由器用无线电波充满每个房间。当人移动——甚至呼吸时——这些电波会以不同的方式散射。WiFi DensePose 读取这种散射模式并重建发生的事件：

```
WiFi 路由器 → 无线电波穿过房间 → 撞击人体 → 散射
    ↓
ESP32 网状网络（4-6 个节点）通过 TDM 协议在信道 1/6/11 上捕获 CSI
    ↓
多频融合：3 个信道 × 56 个子载波 = 每条链路 168 个虚拟子载波
    ↓
多站融合：N×(N-1) 条链路 → 注意力加权跨视角嵌入
    ↓
相干门控：接受/拒绝测量 → 无需调整即可稳定运行数天
    ↓
信号处理：Hampel、SpotFi、菲涅尔、BVP、频谱图 → 干净的特征
    ↓
AI 骨干网络（RuVector）：注意力机制、图算法、压缩、场模型
    ↓
信号线协议（CRV）：6 阶段 格式塔 → 感知 → 拓扑 → 相干 → 搜索 → 模型
    ↓
神经网络：处理后的信号 → 17 个身体关键点 + 生命体征 + 房间模型
    ↓
输出：实时姿态、呼吸、心率、房间指纹、漂移警报
```

无需训练摄像头——[自学习系统（ADR-024）](docs/adr/ADR-024-contrastive-csi-embedding-model.md)仅从原始 WiFi 数据自举。[MERIDIAN（ADR-027）](docs/adr/ADR-027-cross-environment-domain-generalization.md)确保模型在任何房间都能工作，而不仅仅是在训练过的房间。

---

## 🏢 用例与应用

WiFi 感知在任何有 WiFi 的地方都能工作。大多数情况下无需新硬件——只需在现有接入点上安装软件或添加一个 8 美元的 ESP32 模块。由于没有摄像头，部署从设计上就避免了隐私法规（GDPR 视频、HIPAA 影像）的限制。

**扩展性：** 每个 AP 可区分约 3-5 人（56 个子载波）。多 AP 线性倍增——4 个 AP 的零售网状网络可覆盖约 15-20 人。没有硬性软件限制；实际上限取决于信号物理特性。

| | WiFi 感知的优势 | 传统替代方案 |
|---|----------------|-------------|
| 🔒 | **无视频，无 GDPR/HIPAA 影像规定** | 摄像头需要同意、标识、数据保留策略 |
| 🧱 | **穿墙、穿过货架和废墟工作** | 摄像头每个房间都需要视线 |
| 🌙 | **在完全黑暗中工作** | 摄像头需要红外或可见光 |
| 💰 | **每个区域 0-8 美元**（现有 WiFi 或 ESP32） | 摄像头系统：每个区域 200-2,000 美元 |
| 🔌 | **WiFi 已无处不在** | PIR/雷达传感器每个房间都需要新布线 |

<details>
<summary><strong>🏥 日常用例</strong> — 医疗、零售、办公、酒店（普通 WiFi）</summary>

| 用例 | 功能 | 硬件 | 关键指标 | 边缘模块 |
|------|------|------|----------|----------|
| **老年护理/辅助生活** | 跌倒检测、夜间活动监测、睡眠呼吸频率——无需可穿戴设备依从性 | 每个房间 1 个 ESP32-S3（8 美元） | 跌倒警报 <2 秒 | [睡眠呼吸暂停](docs/edge-modules/medical.md)、[步态分析](docs/edge-modules/medical.md) |
| **医院患者监护** | 非重症病床的持续呼吸 + 心率监测，无需有线传感器；异常时护士警报 | 每个病房 1-2 个 AP | 呼吸：6-30 BPM | [呼吸窘迫](docs/edge-modules/medical.md)、[心律失常](docs/edge-modules/medical.md) |
| **急诊分诊** | 自动占用计数 + 等待时间估算；检测候诊区患者 distress（异常呼吸） | 现有医院 WiFi | 占用准确率 >95% | [排队长度](docs/edge-modules/retail.md)、[恐慌运动](docs/edge-modules/security.md) |
| **零售占用与客流** | 实时人流量、各区域停留时间、排队长度——无摄像头、无需选择加入、符合 GDPR | 现有商店 WiFi + 1 个 ESP32 | 停留分辨率约 1 米 | [顾客流](docs/edge-modules/retail.md)、[停留热力图](docs/edge-modules/retail.md) |
| **办公空间利用率** | 哪些办公桌/房间实际被占用、会议室未到场、基于真实存在的 HVAC 优化 | 现有企业 WiFi | 存在延迟 <1 秒 | [会议室](docs/edge-modules/building.md)、[HVAC 存在](docs/edge-modules/building.md) |
| **酒店与招待** | 无需门传感器的房间占用、迷你吧/浴室使用模式、空房节能 | 现有酒店 WiFi | 15-30% HVAC 节能 | [能源审计](docs/edge-modules/building.md)、[灯光区域](docs/edge-modules/building.md) |
| **餐厅与餐饮服务** | 餐桌翻台追踪、厨房员工存在、卫生间占用显示——用餐区无摄像头 | 现有 WiFi | 排队等待 ±30 秒 | [餐桌翻台](docs/edge-modules/retail.md)、[排队长度](docs/edge-modules/retail.md) |
| **停车场** | 摄像头盲区的楼梯间和电梯行人存在检测；有人逗留时安全警报 | 现有 WiFi | 穿透混凝土墙 | [逗留检测](docs/edge-modules/security.md)、[电梯计数](docs/edge-modules/building.md) |

</details>

<details>
<summary><strong>🏟️ 专业用例</strong> — 活动、健身、教育、市政（支持 CSI 的硬件）</summary>

| 用例 | 功能 | 硬件 | 关键指标 | 边缘模块 |
|------|------|------|----------|----------|
| **智能家居自动化** | 穿墙工作的房间级存在触发（灯光、HVAC、音乐）——无死角、无运动传感器超时 | 2-3 个 ESP32-S3 节点（24 美元） | 穿墙范围约 5 米 | [HVAC 存在](docs/edge-modules/building.md)、[灯光区域](docs/edge-modules/building.md) |
| **健身与运动** | 动作计数、姿势纠正、运动呼吸节奏——无需可穿戴设备、更衣室无摄像头 | 3+ 个 ESP32-S3 网状网络 | 姿态：17 个关键点 | [呼吸同步](docs/edge-modules/exotic.md)、[步态分析](docs/edge-modules/medical.md) |
| **托儿所与学校** | 午睡呼吸监测、操场人数统计、限制区域警报——对未成年人隐私安全 | 每个区域 2-4 个 ESP32-S3 | 呼吸：±1 BPM | [睡眠呼吸暂停](docs/edge-modules/medical.md)、[周界入侵](docs/edge-modules/security.md) |
| **活动场馆与音乐会** | 人群密度映射、通过呼吸压缩检测挤压风险、紧急疏散流追踪 | 多 AP 网状网络（4-8 个 AP） | 每平方米密度 | [顾客流](docs/edge-modules/retail.md)、[恐慌运动](docs/edge-modules/security.md) |
| **体育场与竞技场** | 分区级占用用于动态定价、特许经营人员配置、紧急出口流建模 | 企业 AP 网格 | 每个 AP 网状网络 15-20 人 | [停留热力图](docs/edge-modules/retail.md)、[排队长度](docs/edge-modules/retail.md) |
| **宗教场所** | 无需面部识别的出席人数统计——隐私敏感的会众、多房间园区追踪 | 现有 WiFi | 区域级准确率 | [电梯计数](docs/edge-modules/building.md)、[能源审计](docs/edge-modules/building.md) |
| **仓库与物流** | 工人安全区域、叉车接近警报、危险区域占用——穿过货架和托盘工作 | 工业 AP 网状网络 | 警报延迟 <500 毫秒 | [叉车接近](docs/edge-modules/industrial.md)、[狭小空间](docs/edge-modules/industrial.md) |
| **市政基础设施** | 公共卫生间占用（无法安装摄像头）、地铁站台拥挤度、紧急情况下避难所人数统计 | 市政 WiFi + ESP32 | 实时人数统计 | [顾客流](docs/edge-modules/retail.md)、[逗留检测](docs/edge-modules/security.md) |
| **博物馆与画廊** | 访客流热力图、展品停留时间、人群瓶颈警报——艺术品附近无摄像头（闪光/盗窃风险） | 现有 WiFi | 区域停留 ±5 秒 | [停留热力图](docs/edge-modules/retail.md)、[货架互动](docs/edge-modules/retail.md) |

</details>

<details>
<summary><strong>🤖 机器人及工业</strong> — 自主系统、制造业、机器人空间感知</summary>

WiFi 感知为机器人和自主系统提供了一个空间感知层，在 LIDAR 和摄像头失效的地方——穿过灰尘、烟雾、雾气以及绕过角落——仍能工作。CSI 信号场充当"第六感"，无需视线即可检测环境中的人类。

| 用例 | 功能 | 硬件 | 关键指标 | 边缘模块 |
|------|------|------|----------|----------|
| **协作机器人安全区域** | 检测协作机器人附近的人类存在——在接触前自动减速或停止，即使在障碍物后方 | 每个单元 2-3 个 ESP32-S3 | 存在延迟 <100 毫秒 | [叉车接近](docs/edge-modules/industrial.md)、[周界入侵](docs/edge-modules/security.md) |
| **仓库 AMR 导航** | 自主移动机器人感知盲角周围、穿过货架的人类——无 LIDAR 遮挡 | 沿过道的 ESP32 网状网络 | 穿货架检测 | [叉车接近](docs/edge-modules/industrial.md)、[逗留检测](docs/edge-modules/security.md) |
| **人形机器人空间感知** | 社交机器人的环境人体姿态感知——检测手势、接近方向和个人空间，无需摄像头常开 | 板载 ESP32-S3 模块 | 17 关键点姿态 | [手势语言](docs/edge-modules/exotic.md)、[情绪检测](docs/edge-modules/exotic.md) |
| **制造线监控** | 每个工位的工人存在、人体工学姿势警报、轮班合规人数统计——穿过设备工作 | 每个区域工业 AP | 姿态 + 呼吸 | [狭小空间](docs/edge-modules/industrial.md)、[步态分析](docs/edge-modules/medical.md) |
| **建筑工地安全** | 重型机械周围的禁区执行、脚手架跌倒检测、人员统计 | 加固型 ESP32 网状网络 | 警报 <2 秒，穿透灰尘 | [恐慌运动](docs/edge-modules/security.md)、[结构振动](docs/edge-modules/industrial.md) |
| **农业机器人** | 在摄像头不可靠的灰尘/雾天田间条件下检测自主收割机附近的农场工人 | 防水 ESP32 节点 | 开阔场地范围约 10 米 | [叉车接近](docs/edge-modules/industrial.md)、[降雨检测](docs/edge-modules/exotic.md) |
| **无人机降落区** | 验证降落区域无人类——WiFi 感知在雨、尘和弱光条件下工作，而下视摄像头会失效 | 地面 ESP32 节点 | 存在检测：>95% 准确率 | [周界入侵](docs/edge-modules/security.md)、[尾随检测](docs/edge-modules/security.md) |
| **洁净室监控** | 无需摄像头的人员追踪（摄像头风扇带来的颗粒污染风险）——通过姿态检测洁净服合规性 | 现有洁净室 WiFi | 无颗粒排放 | [洁净室](docs/edge-modules/industrial.md)、[牲畜监控](docs/edge-modules/industrial.md) |

</details>

<details>
<summary><strong>🔥 极端用例</strong> — 穿墙、灾难、国防、地下</summary>

这些场景利用了 WiFi 穿透固体材料——混凝土、瓦砾、泥土——的能力，这是光学或红外传感器无法达到的。WiFi-Mat 灾难模块（ADR-001）专门为此层级设计。

| 用例 | 功能 | 硬件 | 关键指标 | 边缘模块 |
|------|------|------|----------|----------|
| **搜索与救援（WiFi-Mat）** | 通过呼吸特征检测瓦砾/废墟中的幸存者、START 分诊颜色分类、3D 定位 | 便携式 ESP32 网状网络 + 笔记本电脑 | 穿透 30 厘米混凝土 | [呼吸窘迫](docs/edge-modules/medical.md)、[癫痫检测](docs/edge-modules/medical.md) |
| **消防** | 进入前通过烟雾和墙壁定位被困人员；呼吸检测远程确认生命迹象 | 消防车上的便携式网状网络 | 在零能见度下工作 | [睡眠呼吸暂停](docs/edge-modules/medical.md)、[恐慌运动](docs/edge-modules/security.md) |
| **监狱与安全设施** | 牢房占用验证、distress 检测（异常生命体征）、周界感知——无摄像头盲区 | 专用 AP 基础设施 | 24/7 生命体征监测 | [心律失常](docs/edge-modules/medical.md)、[逗留检测](docs/edge-modules/security.md) |
| **军事/战术** | 穿墙人员检测、房间清场确认、对峙距离的人质生命体征 | 定向 WiFi + 自定义固件 | 穿墙范围：5 米 | [周界入侵](docs/edge-modules/security.md)、[武器检测](docs/edge-modules/security.md) |
| **边境与周界安全** | 检测隧道内、围栏后、车辆中的人类存在——被动感知，无主动照射暴露位置 | 隐藏式 ESP32 网状网络 | 被动/隐蔽 | [周界入侵](docs/edge-modules/security.md)、[尾随检测](docs/edge-modules/security.md) |
| **采矿与地下** | GPS/摄像头失效的隧道中工人存在检测、坍塌后呼吸检测、安全点人数统计 | 加固型 ESP32 网状网络 | 穿透岩石/泥土 | [狭小空间](docs/edge-modules/industrial.md)、[呼吸窘迫](docs/edge-modules/medical.md) |
| **海事与海军** | 通过钢制舱壁的甲板下人员追踪（范围有限，需调优）、落水检测 | 船舶 WiFi + ESP32 | 穿透 1-2 个舱壁 | [结构振动](docs/edge-modules/industrial.md)、[恐慌运动](docs/edge-modules/security.md) |
| **野生动物研究** | 围栏或巢穴中非侵入性动物活动监测——无光污染、无视觉干扰 | 防水 ESP32 节点 | 零光排放 | [牲畜监控](docs/edge-modules/industrial.md)、[梦境阶段](docs/edge-modules/exotic.md) |

</details>


---

<details>
<summary><strong>🧠 自学习 WiFi AI（ADR-024）</strong> — 自适应识别、自我优化和智能异常检测</summary>

每个穿过房间的 WiFi 信号都会创建该空间的独特指纹。WiFi-DensePose 已经在读取这些指纹来追踪人员，但在此之前，它在每次读取后丢弃了内部的"理解"。自学习 WiFi AI 将这些理解捕获并保存为紧凑、可重用的向量——并持续为每个新环境进行自我优化。

**通俗解释：**
- 将任何 WiFi 信号转换为一个 128 位数字的"指纹"，唯一描述房间内正在发生的事情
- 完全从原始 WiFi 数据自主学习——无需摄像头、无需标注、无需人工监督
- 仅使用 WiFi 即可识别房间、检测入侵者、识别人物和分类活动
- 在 8 美元的 ESP32 芯片上运行（整个模型仅需 55 KB 内存）
- 在单次计算中同时产生身体姿态追踪和环境指纹

**关键能力**

| 功能 | 工作原理 | 重要性 |
|------|----------|--------|
| **自监督学习** | 模型观察 WiFi 信号并自学"相似"和"不同"的样子，无需任何人工标注数据 | 随处部署——只需插入 WiFi 传感器并等待 10 分钟 |
| **房间识别** | 每个房间产生独特的 WiFi 指纹模式 | 无需 GPS 或信标即可知道某人在哪个房间 |
| **异常检测** | 意外的人或事件会产生与之前所见都不匹配的指纹 | 自动入侵和跌倒检测作为免费副产品 |
| **人员重识别** | 每个人以略微不同的方式扰动 WiFi，创建个人签名 | 无需摄像头即可跨会话追踪个体 |
| **环境适应** | MicroLoRA 适配器（每个房间 1,792 个参数）为每个新空间微调模型 | 以最少的数据适应新房间——比从头重新训练少 93% |
| **记忆保留** | EWC++ 正则化记住预训练期间学到的知识 | 切换到新任务不会擦除先前的知识 |
| **难负样本挖掘** | 训练专注于最令人困惑的示例以更快学习 | 相同训练数据量下获得更好的准确率 |

**架构**

```
WiFi 信号 [56 信道] → Transformer + 图神经网络
                                  ├→ 128 维环境指纹（用于搜索和识别）
                                  └→ 17 关节身体姿态（用于人体追踪）
```

**快速开始**

```bash
# 步骤 1：从原始 WiFi 数据学习（无需标签）
cargo run -p wifi-densepose-sensing-server -- --pretrain --dataset data/csi/ --pretrain-epochs 50

# 步骤 2：使用姿态标签微调以获得完整能力
cargo run -p wifi-densepose-sensing-server -- --train --dataset data/mmfi/ --epochs 100 --save-rvf model.rvf

# 步骤 3：使用模型——从实时 WiFi 提取指纹
cargo run -p wifi-densepose-sensing-server -- --model model.rvf --embed

# 步骤 4：搜索——查找相似环境或检测异常
cargo run -p wifi-densepose-sensing-server -- --model model.rvf --build-index env
```

**训练模式**

| 模式 | 所需数据 | 获得结果 |
|------|----------|----------|
| 自监督 | 仅原始 WiFi 数据 | 理解 WiFi 信号结构的模型 |
| 监督 | WiFi 数据 + 身体姿态标签 | 完整姿态追踪 + 环境指纹 |
| 跨模态 | WiFi 数据 + 摄像头画面 | 与视觉理解对齐的指纹 |

**指纹索引类型**

| 索引 | 存储内容 | 实际用途 |
|------|----------|----------|
| `env_fingerprint` | 平均房间指纹 | "这是厨房还是卧室？" |
| `activity_pattern` | 活动边界 | "有人在做饭、睡觉还是锻炼？" |
| `temporal_baseline` | 正常条件 | "这个房间刚刚发生了不寻常的事情" |
| `person_track` | 个体运动特征 | "A 刚刚进入了客厅" |

**模型大小**

| 组件 | 参数 | 内存（ESP32 上） |
|------|------|-----------------|
| Transformer 骨干网络 | 约 28,000 | 28 KB |
| 嵌入投影头 | 约 25,000 | 25 KB |
| 每房间 MicroLoRA 适配器 | 约 1,800 | 2 KB |
| **总计** | **约 55,000** | **55 KB**（可用 520 KB） |

自学习系统建立在 [AI 骨干网络（RuVector）](#ai-骨干网络-ruvector) 信号处理层之上——注意力机制、图算法和压缩——并在其上添加对比学习。

完整架构详情参见 [`docs/adr/ADR-024-contrastive-csi-embedding-model.md`](docs/adr/ADR-024-contrastive-csi-embedding-model.md)。

</details>

---

## 🧩 Claude Code 与 Codex 插件

RuView 附带一个 [Claude Code](https://docs.anthropic.com/en/docs/claude-code) 插件（以及 Codex 提示镜像），将整个工作流程——入门引导、ESP32 设置、配置、感知应用、模型训练、高级多站感知、CLI/API/WASM、毫米波雷达和见证验证——封装为 9 个技能、7 个 `/ruview-*` 命令和 3 个代理。它位于 [`plugins/ruview/`](plugins/ruview/README.md)；市场清单位于仓库根目录的 [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json)。

```bash
# 在 Claude Code 中——将此仓库添加为插件市场，然后安装：
/plugin marketplace add ruvnet/RuView
/plugin install ruview@ruview

# 或在单次会话中试用而不安装（从仓库的本地克隆）：
claude --plugin-dir ./plugins/ruview

# 然后在 Claude Code 中：
#   /ruview-start      → 入门引导（Docker 演示 / 仓库构建 / 实时 ESP32）
#   /ruview-flash      → 构建 + 烧录 ESP32 固件
#   /ruview-provision  → 配置 WiFi 凭据、接收端 IP、信道/MAC、网状网络槽位
#   /ruview-app        → 运行感知应用（存在 / 生命体征 / 姿态 / 睡眠 / MAT / 点云）
#   /ruview-train      → 训练 / 评估 / 发布模型（包括 GCloud 上的 GPU）
#   /ruview-advanced   → 多站 / 层析成像 / 交叉视角 / 网状网络安全
#   /ruview-verify     → 测试 + 确定性证明 + 见证包
```

**Codex（OpenAI CLI）：** `cp plugins/ruview/codex/prompts/*.md ~/.codex/prompts/` — 七个 `/ruview-*` 命令镜像为 Codex 提示；[`plugins/ruview/codex/AGENTS.md`](plugins/ruview/codex/AGENTS.md) 包含项目规则。参见 [`plugins/ruview/codex/README.md`](plugins/ruview/codex/README.md)。

验证插件结构：`bash plugins/ruview/scripts/smoke.sh`。完整详情：[`plugins/ruview/README.md`](plugins/ruview/README.md)。

---

## 📖 文档

| 文档 | 说明 |
|------|------|
| [用户指南](docs/user-guide.md) | 分步指南：安装、首次运行、API 使用、硬件设置、训练 |
| [构建指南](docs/build-guide.md) | 从源码构建（Rust 和 Python） |
| [**Home Assistant + Matter 集成**](docs/integrations/home-assistant.md) | **与 Home Assistant 配合使用**（通过 MQTT 自动发现）+ **与 Matter 配合使用**（Apple Home / Google Home / Alexa / SmartThings）——完整实体目录、3 个入门蓝图、Lovelace 仪表板、隐私模式、阈值调优（[ADR-115](docs/adr/ADR-115-home-assistant-integration.md)）。 |
| [**BFLD — 波束赋形反馈检测层**](v2/crates/wifi-densepose-bfld/README.md) | 新的隐私保护 WiFi 感知层，测量并从结构上防止 802.11ac/ax 波束赋形反馈信息中的身份泄露。三个类型强制不变量（原始 BFI 永不离开节点、身份嵌入仅在内存中、通过每站点 BLAKE3 密钥哈希 + 每日轮换实现跨站点关联在密码学上不可能）。包含完整操作接口（`BfldPipeline`、`BfldPipelineHandle`、灵魂签名 `SoulMatchOracle` 集成）、MQTT 主题路由器 + HA-DISCO + availability + LWT、3 个操作员 HA 蓝图、两个可运行示例、eclipse-mosquitto:2 CI 服务容器。327+ 测试。[ADR-118](docs/adr/ADR-118-bfld-beamforming-feedback-layer-for-detection.md) 总括 + 子 ADR [119](docs/adr/ADR-119-bfld-frame-format-and-wire-protocol.md)/[120](docs/adr/ADR-120-bfld-privacy-class-and-hash-rotation.md)/[121](docs/adr/ADR-121-bfld-identity-risk-scoring.md)/[122](docs/adr/ADR-122-bfld-ruview-ha-matter-exposure.md)/[123](docs/adr/ADR-123-bfld-capture-path-nexmon-and-esp32.md)。研究档案：[`docs/research/BFLD/`](docs/research/BFLD/)（11 个文件，13,544 字）。 |
| [**SENSE-BRIDGE — rvagent MCP 服务器**](tools/ruview-mcp/README.md) | 双传输 MCP 服务器（`@ruvnet/rvagent`），桥接 RuView 感知栈与 AI 代理（Claude Code、Cursor、ruflo 群集）。6 个已接线工具：`ruview.presence.now`、`ruview.vitals.get_{breathing,heart_rate,all}`、`ruview.bfld.last_scan`、`ruview.bfld.subscribe`。stdio + Streamable HTTP（`POST /mcp`，来源验证、Bearer 令牌认证、`127.0.0.1` 绑定）。完整 20 工具 Zod 模式桶 + 5 个 RUVIEW-POLICY 治理工具。93 个测试。[ADR-124](docs/adr/ADR-124-rvagent-mcp-ruvector-npm-integration.md)。试用：`npx @ruvnet/rvagent stdio`。 |
| [语义原语 — 精确率/召回率](docs/integrations/semantic-primitives-metrics.md) | 在保留的配对捕获集上的每个原语 F1 分数：某人睡觉、可能的 distress、房间活跃、老年人不活动异常、会议、浴室、跌倒风险、离床、无运动、多房间。 |
| [Claude Code / Codex 插件](plugins/ruview/README.md) | `ruview` 插件 + 市场——技能、`/ruview-*` 命令、代理和 Codex 提示镜像 |
| [架构决策](docs/adr/README.md) | 96 个 ADR——每个技术选择的原因，按领域组织（硬件、信号处理、机器学习、平台、基础设施） |
| [领域模型](docs/ddd/README.md) | 8 个 DDD 模型（RuvSense、信号处理、训练流水线、硬件平台、感知服务器、WiFi-Mat、CHCI、rvCSI）——限界上下文、聚合、领域事件和通用语言 |
| [rvCSI — 边缘 RF 感知运行时](https://github.com/ruvnet/rvcsi) | Rust 优先 / TypeScript 可访问 / 硬件抽象的 CSI 运行时：多源摄入（包括来自 **Raspberry Pi 5** / Pi 4 / Pi 3B+ 的真实 nexmon_csi `.pcap`——CYW43455 / BCM43455c0）→ 验证 → DSP → 类型化事件 → RuVector RF 内存（[ADR-095](docs/adr/ADR-095-rvcsi-edge-rf-sensing-platform.md)、[ADR-096](docs/adr/ADR-096-rvcsi-ffi-crate-layout.md)、[领域模型](docs/ddd/rvcsi-domain-model.md)）。现为独立仓库——[`ruvnet/rvcsi`](https://github.com/ruvnet/rvcsi)——在此仓库的 `vendor/rvcsi` 下供应；crates.io 上 9 个 `rvcsi-*` crate，npm 上 `@ruv/rvcsi`，外加一个 Claude Code 插件。 |
| [桌面应用](v2/crates/wifi-densepose-desktop/README.md) | **开发中**——基于 Tauri v2 的桌面应用，用于节点管理、OTA 更新、WASM 部署和网状网络可视化 |
| `ruview-swarm` | 无人机群控制系统（ADR-148）——分层网状拓扑、Raft 共识、MARL、CSI 感知载荷、MAVLink/PX4/ArduPilot 兼容性、Ruflo AI 代理集成 |
| [医疗示例](examples/medical/README.md) | 通过 60 GHz 毫米波雷达实现非接触式血压、心率、呼吸频率——15 美元硬件，无需可穿戴设备 |
| [扩展文档](docs/readme-details.md) | 最新添加、关键特性、安装、快速开始、信号处理、训练、CLI、测试、部署和更新日志 |

---

## 🚧 Beta 软件

> **Beta 软件** — 正在积极开发中。API 和固件可能发生变化。已知限制：
> - 不支持 ESP32-C3 和原始 ESP32（单核，不足以处理 CSI DSP）
> - 单个 ESP32 部署的空间分辨率有限——使用 2 个以上节点或添加 [Cognitum Seed](https://cognitum.one) 以获得最佳效果
> - 无摄像头的姿态准确率有限（使用代理标签时 PCK@20 ≈ 2.5%）——[摄像头真值训练](docs/adr/ADR-079-camera-ground-truth-training.md)目标为 **35%+ PCK@20**；流水线已实现，但数据收集和评估阶段（ADR-079 P7–P9）仍在进行中。
>
> 欢迎在 [Issues](https://github.com/ruvnet/RuView/issues) 提交贡献和错误报告。

## 📄 许可证

MIT 许可证——详见 [LICENSE](LICENSE)。

## 🤝 创作者联盟计划

**面向 TikTok · Instagram · YouTube 创作者**——每推荐一笔 **Cognitum 销售，赚取 25%** 佣金。您已经在制作的 RuFlo、RuView 和 RuVector 视频已获得数百万观看量；现在为您带来的订单获得报酬。点击追踪即时激活；佣金在快速人工审核后激活（通常在 24 小时内）。

[立即申请 → cognitum.one/affiliate](https://cognitum.one/affiliate)

## 📞 支持

[GitHub Issues](https://github.com/ruvnet/RuView/issues) | [讨论区](https://github.com/ruvnet/RuView/discussions) | [PyPI](https://pypi.org/project/wifi-densepose/)

---

**WiFi DensePose** — 通过 WiFi 信号实现保护隐私的人体姿态估计。
