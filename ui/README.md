# WiFi DensePose UI

一个模块化、现代化的 Web 界面，用于 WiFi DensePose 人体追踪系统。提供实时监控、WiFi 感知可视化和基于 CSI（信道状态信息）的姿态估计。

## 架构

UI 采用模块化架构，关注点清晰分离：

```
ui/
├── app.js                    # 主应用入口点
├── index.html                # 带标签结构的 HTML 外壳
├── style.css                 # 完整 CSS 设计系统
├── config/
│   └── api.config.js         # API 端点和配置
├── services/
│   ├── api.service.js        # HTTP API 客户端
│   ├── websocket.service.js  # WebSocket 连接管理器
│   ├── websocket-client.js   # 底层 WebSocket 客户端
│   ├── pose.service.js       # 姿态估计 API 包装器
│   ├── sensing.service.js    # WiFi 感知数据服务（实时 + 模拟回退）
│   ├── health.service.js     # 健康监控 API 包装器
│   ├── stream.service.js     # 流式 API 包装器
│   └── data-processor.js     # 信号数据处理工具
├── components/
│   ├── TabManager.js         # 标签导航组件
│   ├── DashboardTab.js       # 带实时系统指标的仪表板
│   ├── SensingTab.js         # WiFi 感知可视化（3D 信号场、指标）
│   ├── LiveDemoTab.js        # 实时姿态检测与设置指南
│   ├── HardwareTab.js        # 硬件配置
│   ├── SettingsPanel.js      # 设置面板
│   ├── PoseDetectionCanvas.js # 基于画布的姿态骨架渲染器
│   ├── gaussian-splats.js    # 3D 高斯溅射信号场渲染器（Three.js）
│   ├── body-model.js         # 3D 身体模型
│   ├── scene.js              # Three.js 场景管理
│   ├── signal-viz.js         # 信号可视化工具
│   ├── environment.js        # 环境/房间可视化
│   └── dashboard-hud.js      # 仪表板平视显示器
├── utils/
│   ├── backend-detector.js   # 自动检测后端可用性
│   ├── mock-server.js        # 用于测试的模拟服务器
│   └── pose-renderer.js      # 姿态渲染工具
└── tests/
    ├── test-runner.html       # 测试运行器 UI
    ├── test-runner.js         # 测试框架和用例
    └── integration-test.html  # 集成测试页面
```

## 功能

### WiFi 感知标签
- 3D 高斯溅射信号场可视化（Three.js）
- 实时 RSSI、方差、运动频带、呼吸频带指标
- 存在/运动分类及置信度评分
- **数据源横幅**：绿色"实时 - ESP32"、黄色"重新连接中..."或红色"模拟数据"
- Sparkline RSSI 历史图
- "关于此数据"卡片，解释每个传感器数量的 CSI 能力

### 实时演示标签
- 基于 WebSocket 的实时姿态骨架渲染
- **估计模式徽章**：绿色"信号推导"或蓝色"模型推理"
- **设置指南面板**显示每个 ESP32 数量提供的功能：
  - 1 个 ESP32：存在检测、呼吸、粗略运动
  - 2-3 个 ESP32：身体定位、运动方向
  - 4+ 个 ESP32 + 训练模型：个体肢体追踪、完整姿态
- 调试模式及日志导出
- 区域选择和强制重新连接控制
- 性能指标侧边栏（帧数、运行时间、错误数）

### 仪表板
- 实时系统健康监控
- 实时姿态检测统计
- 区域占用追踪
- 系统指标（CPU、内存、磁盘）
- API 状态指示器

### 硬件配置
- 交互式天线阵列可视化
- 实时 CSI 数据显示
- 配置面板
- 硬件状态监控

## 数据源

感知服务（`sensing.service.js`）支持三种连接状态：

| 状态 | 横幅颜色 | 说明 |
|------|---------|------|
| **实时 - ESP32** | 绿色 | 已连接到接收真实 CSI 数据的 Rust 感知服务器 |
| **重新连接中** | 黄色（脉冲） | WebSocket 断开，正在重试（最多 20 次） |
| **模拟数据** | 红色 | 5 次以上重连失败后回退到客户端模拟 |

模拟帧包含 `_simulated: true` 标记，以便代码检测合成数据。

## 后端

### Rust 感知服务器（主要）
基于 Rust 的 `wifi-densepose-sensing-server` 提供 UI 和以下功能：
- `GET /health` — 服务器健康状态
- `GET /api/v1/sensing/latest` — 最新感知特征
- `GET /api/v1/vital-signs` — 生命体征估计（心率/呼吸频率）
- `GET /api/v1/model/info` — RVF 模型容器信息
- `WS /ws/sensing` — 实时感知数据流
- `WS /api/v1/stream/pose` — 实时姿态关键点流

### Python FastAPI（旧版）
仍支持端口 8000 上的原始 Python 后端。UI 通过 `backend-detector.js` 自动检测哪个后端可用。

## 快速开始

### 使用 Docker（推荐）
```bash
cd docker/

# 默认：自动检测 UDP 5005 上的 ESP32，回退到模拟
docker-compose up

# 强制使用真实 ESP32 数据
CSI_SOURCE=esp32 docker-compose up

# 强制模拟（无需硬件）
CSI_SOURCE=simulated docker-compose up
```
打开 http://localhost:3000/ui/index.html

### 使用本地 Rust 二进制文件
```bash
cd v2
cargo build -p wifi-densepose-sensing-server --no-default-features

# 使用模拟数据运行
../../target/debug/sensing-server --source simulated --tick-ms 100 --ui-path ../../ui --http-port 3000

# 使用真实 ESP32 运行
../../target/debug/sensing-server --source esp32 --tick-ms 100 --ui-path ../../ui --http-port 3000
```
打开 http://localhost:3000/ui/index.html

### 使用 Python HTTP 服务器（旧版）
```bash
# 在端口 8000 上启动 FastAPI 后端
wifi-densepose start

# 在端口 3000 上提供 UI
cd ui/
python -m http.server 3000
```
打开 http://localhost:3000

## 姿态估计模式

| 模式 | 徽章 | 要求 | 准确率 |
|------|------|------|--------|
| **信号推导** | 绿色 | 1+ 个 ESP32，无需模型 | 存在检测、呼吸、粗略运动 |
| **模型推理** | 蓝色 | 4+ 个 ESP32 + 训练的 `.rvf` 模型 | 完整 17 关键点 COCO 姿态 |

要使用模型推理，请使用训练好的模型启动服务器：
```bash
sensing-server --source esp32 --model path/to/model.rvf --ui-path ./ui
```

## 配置

### API 配置
编辑 `config/api.config.js`：

```javascript
export const API_CONFIG = {
  BASE_URL: window.location.origin,
  API_VERSION: '/api/v1',
  WS_CONFIG: {
    RECONNECT_DELAY: 5000,
    MAX_RECONNECT_ATTEMPTS: 20,
    PING_INTERVAL: 30000
  }
};
```

## 测试

打开 `tests/test-runner.html` 运行测试套件：

```bash
cd ui/
python -m http.server 3000
# 打开 http://localhost:3000/tests/test-runner.html
```

测试类别：API 配置、API 服务、WebSocket、姿态服务、健康服务、UI 组件、集成测试。

## 样式

使用 CSS 设计系统，包含自定义属性、暗色/亮色模式、响应式布局和基于组件的样式。关键变量在 `style.css` 的 `:root` 中。

## 许可证

属于 WiFi-DensePose 系统的一部分。请参见主项目 LICENSE 文件。
