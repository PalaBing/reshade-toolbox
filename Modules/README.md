# DR 模块

本目录用于开发通过 Daily Routines 本地模块机制加载的功能模块。各模块自行说明宿主依赖、安装方式、构建入口和验证范围。

| 模块 | 用途 | 维护入口 |
|---|---|---|
| [DR-DLSS](DR-DLSS/README.md) | DLSS 模型与挡位选择、OptiScaler 配置保存、状态读取和手动刷新 | [技术说明](DR-DLSS/docs/TECHNICAL.md)、[依赖来源](DR-DLSS/references/UPSTREAM.md) |

模块源码、构建脚本、隔离测试和维护资料集中在模块目录。发布产物和宿主 DLL 的分发范围见各模块的来源与许可说明。
