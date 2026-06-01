# WiFi-DensePose Rust Crates

[![License: MIT OR Apache-2.0](https://img.shields.io/badge/license-MIT%2FApache--2.0-blue.svg)](LICENSE)
[![Rust 1.85+](https://img.shields.io/badge/rust-1.85%2B-orange.svg)](https://www.rust-lang.org/)
[![Workspace](https://img.shields.io/badge/workspace-14%20crates-green.svg)](https://github.com/ruvnet/wifi-densepose)
[![RuVector v2.0.4](https://img.shields.io/badge/ruvector-v2.0.4-purple.svg)](https://crates.io/crates/ruvector-mincut)
[![Tests](https://img.shields.io/badge/tests-542%2B-brightgreen.svg)](#testing)

**用 WiFi 穿墙透视。无摄像头。无可穿戴设备。只有无线电波。**

一个模块化的 Rust 工作空间，用于基于 WiFi 的人体姿态估计、生命体征监测和灾难响应，使用信道状态信息（CSI）。基于 [RuVector](https://crates.io/crates/ruvector-mincut) 图算法和 [rUv](https://github.com/ruvnet) 的 [WiFi-DensePose](https://github.com/ruvnet/wifi-densepose) 研究平台构建。

---

## 性能

| 操作 | Python v1 | Rust v2 | 加速比 |
|------|-----------|---------|--------|
| CSI 预处理 | 约 5 ms | 5.19 us | **约 1000x** |
| 相位校正 | 约 3 ms | 3.84 us | **约 780x** |
| 特征提取 | 约 8 ms | 9.03 us | **约 890x** |
| 运动检测 | 约 1 ms | 186 ns | **约 5400x** |
| 完整流水线 | 约 15 ms | 18.47 us | **约 810x** |
| 生命体征 | 无 | 86 us（11,665 fps） | -- |

## Crate 概览

### 核心基础

| Crate | 说明 | crates.io |
|-------|------|-----------|
| [`wifi-densepose-core`](wifi-densepose-core/) | 类型、特性和工具（`CsiFrame`、`PoseEstimate`、`SignalProcessor`） | [![crates.io](https://img.shields.io/crates/v/wifi-densepose-core.svg)](https://crates.io/crates/wifi-densepose-core) |
| [`wifi-densepose-config`](wifi-densepose-config/) | 配置管理（环境变量、TOML、YAML） | [![crates.io](https://img.shields.io/crates/v/wifi-densepose-config.svg)](https://crates.io/crates/wifi-densepose-config) |
| [`wifi-densepose-db`](wifi-densepose-db/) | 数据库持久化（PostgreSQL、SQLite、Redis） | [![crates.io](https://img.shields.io/crates/v/wifi-densepose-db.svg)](https://crates.io/crates/wifi-densepose-db) |

### 信号处理与感知

| Crate | 说明 | RuVector 集成 | crates.io |
|-------|------|---------------|-----------|
| [`wifi-densepose-signal`](wifi-densepose-signal/) | SOTA CSI 信号处理（来自 SpotFi、FarSense、Widar 3.0 的 6 种算法） | `ruvector-mincut`、`ruvector-attn-mincut`、`ruvector-attention`、`ruvector-solver` | [![crates.io](https://img.shields.io/crates/v/wifi-densepose-signal.svg)](https://crates.io/crates/wifi-densepose-signal) |
| [`wifi-densepose-vitals`](wifi-densepose-vitals/) | 生命体征提取：呼吸（6-30 BPM）和心率（40-120 BPM） | -- | [![crates.io](https://img.shields.io/crates/v/wifi-densepose-vitals.svg)](https://crates.io/crates/wifi-densepose-vitals) |
| [`wifi-densepose-wifiscan`](wifi-densepose-wifiscan/) | 用于 Windows 增强感知的多 BSSID WiFi 扫描 | -- | [![crates.io](https://img.shields.io/crates/v/wifi-densepose-wifiscan.svg)](https://crates.io/crates/wifi-densepose-wifiscan) |

### 神经网络与训练

| Crate | 说明 | RuVector 集成 | crates.io |
|-------|------|---------------|-----------|
| [`wifi-densepose-nn`](wifi-densepose-nn/) | 多后端推理（ONNX、PyTorch、Candle），带 DensePose 头部（24 个身体部位） | -- | [![crates.io](https://img.shields.io/crates/v/wifi-densepose-nn.svg)](https://crates.io/crates/wifi-densepose-nn) |
| [`wifi-densepose-train`](wifi-densepose-train/) | 训练流水线，使用 MM-Fi 数据集，114->56 子载波插值 | **全部 5 个 crate** | [![crates.io](https://img.shields.io/crates/v/wifi-densepose-train.svg)](https://crates.io/crates/wifi-densepose-train) |

### 灾难响应

| Crate | 说明 | RuVector 集成 | crates.io |
|-------|------|---------------|-----------|
| [`wifi-densepose-mat`](wifi-densepose-mat/) | 大规模伤亡评估工具——幸存者检测、分诊、多 AP 定位 | `ruvector-solver`、`ruvector-temporal-tensor` | [![crates.io](https://img.shields.io/crates/v/wifi-densepose-mat.svg)](https://crates.io/crates/wifi-densepose-mat) |

### 硬件与部署

| Crate | 说明 | crates.io |
|-------|------|-----------|
| [`wifi-densepose-hardware`](wifi-densepose-hardware/) | ESP32、Intel 5300、Atheros CSI 传感器接口（纯 Rust，无 FFI） | [![crates.io](https://img.shields.io/crates/v/wifi-densepose-hardware.svg)](https://crates.io/crates/wifi-densepose-hardware) |
| [`wifi-densepose-wasm`](wifi-densepose-wasm/) | 基于浏览器的灾难仪表板的 WebAssembly 绑定 | [![crates.io](https://img.shields.io/crates/v/wifi-densepose-wasm.svg)](https://crates.io/crates/wifi-densepose-wasm) |
| [`wifi-densepose-sensing-server`](wifi-densepose-sensing-server/) | Axum 服务器：ESP32 UDP 接收、WebSocket 广播、感知 UI | [![crates.io](https://img.shields.io/crates/v/wifi-densepose-sensing-server.svg)](https://crates.io/crates/wifi-densepose-sensing-server) |

### 应用

| Crate | 说明 | crates.io |
|-------|------|-----------|
| [`wifi-densepose-api`](wifi-densepose-api/) | REST + WebSocket API 层 | [![crates.io](https://img.shields.io/crates/v/wifi-densepose-api.svg)](https://crates.io/crates/wifi-densepose-api) |
| [`wifi-densepose-cli`](wifi-densepose-cli/) | MAT 灾难扫描命令行工具 | [![crates.io](https://img.shields.io/crates/v/wifi-densepose-cli.svg)](https://crates.io/crates/wifi-densepose-cli) |

---

## 架构

```
                          wifi-densepose-core
                         (类型、特性、错误)
                                  |
              +-------------------+-------------------+
              |                   |                   |
    wifi-densepose-signal   wifi-densepose-nn   wifi-densepose-hardware
    (CSI 处理)               (推理)               (ESP32, Intel 5300)
    + ruvector-mincut       + ONNX Runtime          |
    + ruvector-attn-mincut  + PyTorch (tch)   wifi-densepose-vitals
    + ruvector-attention    + Candle          (呼吸、心率)
    + ruvector-solver            |
              |                  |             wifi-densepose-wifiscan
              +--------+---------+            (BSSID 扫描)
                       |
          +------------+------------+
          |                         |
  wifi-densepose-train    wifi-densepose-mat
  (训练流水线)             (灾难响应)
  + ALL 5 ruvector        + ruvector-solver
                          + ruvector-temporal-tensor
                                |
              +-----------------+-----------------+
              |                 |                 |
    wifi-densepose-api  wifi-densepose-wasm  wifi-densepose-cli
    (REST/WS)           (浏览器 WASM)       (CLI 工具)
              |
    wifi-densepose-sensing-server
    (Axum + WebSocket)
```

## RuVector 集成

所有 [RuVector](https://github.com/ruvnet/ruvector) crate 均为来自 crates.io 的 **v2.0.4** 版本：

| RuVector Crate | 使用位置 | 用途 |
|----------------|---------|------|
| [`ruvector-mincut`](https://crates.io/crates/ruvector-mincut) | signal, train | 用于子载波选择和人员匹配的动态最小割 |
| [`ruvector-attn-mincut`](https://crates.io/crates/ruvector-attn-mincut) | signal, train | 用于天线门控和频谱图的注意力加权最小割 |
| [`ruvector-temporal-tensor`](https://crates.io/crates/ruvector-temporal-tensor) | train, mat | 分层时间压缩（4-10 倍内存减少） |
| [`ruvector-solver`](https://crates.io/crates/ruvector-solver) | signal, train, mat | 用于插值和三角测量的稀疏 Neumann 求解器 |
| [`ruvector-attention`](https://crates.io/crates/ruvector-attention) | signal, train | 用于空间特征和 BVP 的缩放点积注意力 |

## 信号处理算法

`wifi-densepose-signal` 中实现的六种最先进算法：

| 算法 | 论文 | 年份 | 模块 |
|------|------|------|------|
| 共轭乘法 | SpotFi (SIGCOMM) | 2015 | `csi_ratio.rs` |
| Hampel 滤波器 | WiGest | 2015 | `hampel.rs` |
| 菲涅尔区模型 | FarSense (MobiCom) | 2019 | `fresnel.rs` |
| CSI 频谱图 | 标准 STFT | 2018+ | `spectrogram.rs` |
| 子载波选择 | WiDance (MobiCom) | 2017 | `subcarrier_selection.rs` |
| 身体速度剖面 | Widar 3.0 (MobiSys) | 2019 | `bvp.rs` |

## 快速开始

### 作为库使用

```rust
use wifi_densepose_core::{CsiFrame, CsiMetadata, SignalProcessor};
use wifi_densepose_signal::{CsiProcessor, CsiProcessorConfig};

// 配置 CSI 处理器
let config = CsiProcessorConfig::default();
let processor = CsiProcessor::new(config);

// 处理 CSI 帧
let frame = CsiFrame { /* ... */ };
let processed = processor.process(&frame)?;
```

### 生命体征监测

```rust
use wifi_densepose_vitals::{
    CsiVitalPreprocessor, BreathingExtractor, HeartRateExtractor,
    VitalAnomalyDetector,
};

let mut preprocessor = CsiVitalPreprocessor::new(56); // 56 个子载波
let mut breathing = BreathingExtractor::new(100.0);    // 100 Hz 采样率
let mut heartrate = HeartRateExtractor::new(100.0);

// 输入 CSI 帧并提取生命体征
for frame in csi_stream {
    let residuals = preprocessor.update(&frame.amplitudes);
    if let Some(bpm) = breathing.push_residuals(&residuals) {
        println!("呼吸：{:.1} BPM", bpm);
    }
}
```

### 灾难响应（MAT）

```rust
use wifi_densepose_mat::{DisasterResponse, DisasterConfig, DisasterType};

let config = DisasterConfig {
    disaster_type: DisasterType::Earthquake,
    max_scan_zones: 16,
    ..Default::default()
};

let mut responder = DisasterResponse::new(config);
responder.add_scan_zone(zone)?;
responder.start_continuous_scan().await?;
```

### 硬件（ESP32）

```rust
use wifi_densepose_hardware::{Esp32CsiParser, CsiFrame};

let parser = Esp32CsiParser::new();
let raw_bytes: &[u8] = /* 来自 ESP32 的 UDP 数据包 */;
let frame: CsiFrame = parser.parse(raw_bytes)?;
println!("RSSI: {} dBm, {} 个子载波", frame.metadata.rssi, frame.subcarriers.len());
```

### 训练

```bash
# 检查训练 crate（无需 GPU）
cargo check -p wifi-densepose-train --no-default-features

# 使用 GPU 运行训练（需要 tch/libtorch）
cargo run -p wifi-densepose-train --features tch-backend --bin train -- \
    --config training.toml --dataset /path/to/mmfi

# 验证确定性训练证明
cargo run -p wifi-densepose-train --features tch-backend --bin verify-training
```

## 构建

```bash
# 克隆仓库
git clone https://github.com/ruvnet/wifi-densepose.git
cd wifi-densepose/v2

# 检查工作空间（无需 GPU 依赖）
cargo check --workspace --no-default-features

# 运行所有测试
cargo test --workspace --no-default-features

# 构建发布版本
cargo build --release --workspace
```

### 特性标志

| Crate | 特性 | 说明 |
|-------|------|------|
| `wifi-densepose-nn` | `onnx`（默认） | ONNX Runtime 后端 |
| `wifi-densepose-nn` | `tch-backend` | PyTorch（libtorch）后端 |
| `wifi-densepose-nn` | `candle-backend` | Candle（纯 Rust）后端 |
| `wifi-densepose-nn` | `cuda` | CUDA GPU 加速 |
| `wifi-densepose-train` | `tch-backend` | 启用 GPU 训练模块 |
| `wifi-densepose-mat` | `ruvector`（默认） | RuVector 图算法 |
| `wifi-densepose-mat` | `api`（默认） | REST + WebSocket API |
| `wifi-densepose-mat` | `distributed` | 多节点协调 |
| `wifi-densepose-mat` | `drone` | 无人机搭载扫描 |
| `wifi-densepose-hardware` | `esp32` | ESP32 协议支持 |
| `wifi-densepose-hardware` | `intel5300` | Intel 5300 CSI 工具 |
| `wifi-densepose-hardware` | `linux-wifi` | Linux 普通 WiFi |
| `wifi-densepose-wifiscan` | `wlanapi` | Windows WLAN API 异步扫描 |
| `wifi-densepose-core` | `serde` | 序列化支持 |
| `wifi-densepose-core` | `async` | 异步特性支持 |

## 测试

```bash
# 单元测试（所有 crate）
cargo test --workspace --no-default-features

# 信号处理基准测试
cargo bench -p wifi-densepose-signal

# 训练基准测试
cargo bench -p wifi-densepose-train --no-default-features

# 检测基准测试
cargo bench -p wifi-densepose-mat
```

## 支持的硬件

| 硬件 | Crate 特性 | CSI 子载波 | 成本 |
|------|-----------|------------|------|
| ESP32-S3 网状网络（3-6 节点） | `hardware/esp32` | 52-56 | 约 54 美元 |
| Intel 5300 NIC | `hardware/intel5300` | 30 | 约 50 美元 |
| Atheros AR9580 | `hardware/linux-wifi` | 56 | 约 100 美元 |
| 任何 WiFi（Windows/Linux） | `wifiscan` | 仅 RSSI | 0 美元 |

## 架构决策记录

关键设计决策记录在 [`docs/adr/`](https://github.com/ruvnet/wifi-densepose/tree/main/docs/adr) 中：

| ADR | 标题 | 状态 |
|-----|------|------|
| [ADR-014](https://github.com/ruvnet/wifi-densepose/blob/main/docs/adr/ADR-014-sota-signal-processing.md) | SOTA 信号处理 | 已接受 |
| [ADR-015](https://github.com/ruvnet/wifi-densepose/blob/main/docs/adr/ADR-015-public-dataset-training-strategy.md) | MM-Fi + Wi-Pose 训练数据集 | 已接受 |
| [ADR-016](https://github.com/ruvnet/wifi-densepose/blob/main/docs/adr/ADR-016-ruvector-integration.md) | RuVector 训练流水线 | 已接受（完成） |
| [ADR-017](https://github.com/ruvnet/wifi-densepose/blob/main/docs/adr/ADR-017-ruvector-signal-mat-integration.md) | RuVector 信号 + MAT 集成 | 已接受 |
| [ADR-021](https://github.com/ruvnet/wifi-densepose/blob/main/docs/adr/ADR-021-vital-sign-detection.md) | 生命体征检测流水线 | 已接受 |
| [ADR-022](https://github.com/ruvnet/wifi-densepose/blob/main/docs/adr/ADR-022-windows-wifi-enhanced.md) | Windows WiFi 增强感知 | 已接受 |
| [ADR-024](https://github.com/ruvnet/wifi-densepose/blob/main/docs/adr/ADR-024-contrastive-csi-embedding.md) | 对比 CSI 嵌入模型 | 已接受 |

## 相关项目

- **[WiFi-DensePose](https://github.com/ruvnet/wifi-densepose)** -- 主仓库（Python v1 + Rust v2）
- **[RuVector](https://github.com/ruvnet/ruvector)** -- 神经网络的图算法（5 个 crate，v2.0.4）
- **[rUv](https://github.com/ruvnet)** -- 创建者和维护者

## 许可证

所有 crate 采用双许可证 [MIT](https://opensource.org/licenses/MIT) 或 [Apache-2.0](https://www.apache.org/licenses/LICENSE-2.0)。

版权所有 (c) 2024 rUv