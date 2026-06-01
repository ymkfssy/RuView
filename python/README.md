# wifi-densepose

[![PyPI version](https://img.shields.io/pypi/v/wifi-densepose.svg)](https://pypi.org/project/wifi-densepose/)
[![Python](https://img.shields.io/pypi/pyversions/wifi-densepose.svg)](https://pypi.org/project/wifi-densepose/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)

**检测人体存在、统计人数、读取呼吸和心率、估计骨骼姿态——仅使用家中已有的 WiFi 信号。**

无摄像头。无可穿戴设备。穿墙工作，在黑暗中也能工作。

`wifi-densepose` 是 [RuView](https://github.com/ruvnet/RuView) 感知栈的 Python 绑定：一个 Rust 核心，将普通 WiFi 芯片发出的信道状态信息（CSI）转换为环境智能信号。该 wheel 包含编译好的 DSP，用于快速离线分析，以及一个可选的 Python 客户端，用于通过 WebSocket 或 MQTT 与实时 RuView 感知服务器通信。

## 功能

- **17 关键点姿态** — 从 WiFi CSI 进行全身骨骼估计，无需摄像头
- **生命体征** — 呼吸频率（6-30 BPM）和心率（40-120 BPM），带置信度评分和临床级/退化/不可靠状态
- **存在检测、人数统计、跌倒检测、运动检测** — 来自同一 CSI 流的融合输出
- **10 个语义原语**（HA-MIND）——某人睡觉、可能的 distress、房间活跃、浴室占用、跌倒风险升高、离床……——可直接接入 Home Assistant 或 Apple Home 自动化
- **波束赋形反馈（BFLD）支持** — 在接收端 CSI 路径之上的 802.11ac/ax/be 压缩反馈矩阵
- **释放 GIL 的 DSP** — 提取循环在释放 GIL 的情况下运行，因此基于 tokio 的 Web 服务器可以调用流水线而不会阻塞其事件循环
- **小巧的 wheel** — 约 240 KB 编译大小（每个 OS/架构一个二进制文件，通过稳定 ABI 覆盖 Python 3.10+）

## 安装

```bash
pip install wifi-densepose                 # 仅核心 DSP
pip install "wifi-densepose[client]"       # + WebSocket/MQTT 客户端
```

Wheels 已发布到 Linux（x86_64、aarch64）、macOS（x86_64、arm64）和 Windows（amd64）。

## 使用

### 从 CSI 流中提取呼吸频率

```python
from wifi_densepose import BreathingExtractor

br = BreathingExtractor.esp32_default()     # 56 个子载波 @ 100 Hz，30 秒窗口

for residuals, weights in your_csi_source:  # 逐帧处理
    est = br.extract(residuals=residuals, weights=weights)
    if est is not None:
        print(f"{est.value_bpm:.1f} BPM  (confidence={est.confidence:.2f})")
```

心率的用法相同——`HeartRateExtractor.esp32_default()`，带 0.8-2.0 Hz 带通滤波和 15 秒窗口。

### 订阅实时感知服务器

```python
import asyncio
from wifi_densepose.client import SensingClient, EdgeVitalsMessage

async def main():
    async with SensingClient("ws://your-ruview-node:8765/ws/sensing") as c:
        async for msg in c.stream():
            if isinstance(msg, EdgeVitalsMessage):
                print(msg.presence, msg.breathing_rate_bpm, msg.heartrate_bpm)

asyncio.run(main())
```

### 响应 Home Assistant 语义原语

```python
from wifi_densepose.client import (
    RuViewMqttClient, SemanticPrimitive, SemanticPrimitiveListener,
)

listener = SemanticPrimitiveListener()
listener.on(SemanticPrimitive.BedExit, lambda e: print("离床:", e.node_id))
listener.on(SemanticPrimitive.PossibleDistress, lambda e: alert(e))

client = RuViewMqttClient(broker_host="homeassistant.local")
client.on_message(
    "homeassistant/+/wifi_densepose_+/+/state",
    listener.handle_mqtt_message,
)
client.start()
client.wait_connected()
```

### 解码 802.11ax 波束赋形反馈

```python
import numpy as np
from wifi_densepose import BfldFrame, BfldKind

# 将 Wireshark 捕获中的压缩 BFR 解析为 Complex64 ndarray ...
fb = np.zeros((2, 1, 996), dtype=np.complex64)  # Nr=2 Nc=1 Nsc=996 for HE80

frame = BfldFrame.from_compressed_feedback(
    timestamp_ms=ts,
    sounding_index=seq,
    sta_mac="aa:bb:cc:dd:ee:ff",
    kind=BfldKind.CompressedHE80,
    feedback_matrix=fb,
)
print(frame.n_subcarriers, frame.mean_amplitude)
```

## 硬件

适用于任何暴露 CSI 的 WiFi 芯片。参考设置（ESP-IDF 固件、构建脚本、见证验证测试包）在 [RuView 仓库](https://github.com/ruvnet/RuView)中：

| 设备 | 成本 | 角色 |
|------|------|------|
| ESP32-S3（8MB 闪存） | 约 9 美元 | WiFi CSI 感知节点 |
| ESP32-S3 SuperMini（4MB） | 约 6 美元 | WiFi CSI（紧凑型） |
| ESP32-C6 + Seeed MR60BHA2 | 约 15 美元 | 毫米波 HR/BR/存在检测附加模块 |

旧版 v1 系列（Wi-Pose 风格的 FastAPI 服务器）已停止维护；`wifi-densepose==1.99.0` 是一个墓碑版本，会引发 `ImportError` 并指向 v2 的迁移 URL。

## 链接

- **仓库** — https://github.com/ruvnet/RuView
- **现代化计划** — [ADR-117](https://github.com/ruvnet/RuView/blob/main/docs/adr/ADR-117-pip-wifi-densepose-modernization.md)
- **Home Assistant 集成** — [ADR-115](https://github.com/ruvnet/RuView/blob/main/docs/adr/ADR-115-home-assistant-integration.md)
- **Issues** — https://github.com/ruvnet/RuView/issues

## 许可证

MIT。
