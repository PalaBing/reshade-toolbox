# DR DLSS 手动模块

手动切换 DLSS 模型与挡位，保存 OptiScaler 配置，并通过桥接请求热切换。通过 DR 的本地模块管理单独加载。

当前源码为 **0.1.6.0 适配候选版**，面向 **DR 2.2.8.0 / Dalamud API 15**，尚未发布。DR 更改了模块基类的权限与名称接口，0.1.5.0 的旧声明已无法用于该版本。

2026-11-13 已使用 **DR 2.2.8.0 官方发布包**与国服 Hooks **26-11-10-01** 构建成功，无编译警告；148 项隔离检查通过。当前候选版不适用于 2.2.7.1 的基类接口，使用前应先更新 DR 至 2.2.8.0。新宿主下的加载、配置读取和热切换仍待游戏内复验，详见 [VALIDATION.md](VALIDATION.md)。

已发布的旧版本为 **0.1.5.0**，发布构建基线为 DR 2.2.2.0，后续静态检查截至 DR 2.2.5.1。以下链接保留供历史版本使用，DR 2.2.8.0 请构建当前候选源码：

[旧版 DLL](https://github.com/PalaBing/reshade-toolbox/releases/download/dr-dlss-v0.1.5.0/DR.DlssModule.dll) · [0.1.5.0 发布页](https://github.com/PalaBing/reshade-toolbox/releases/tag/dr-dlss-v0.1.5.0) · [旧版 SHA256 校验](https://github.com/PalaBing/reshade-toolbox/releases/download/dr-dlss-v0.1.5.0/SHA256SUMS.txt)

## 安装与使用

1. 安装并确认 OptiScaler 在游戏中正常加载。热切换需要带本模块桥接接口的版本；使用其他版本时，可保存配置后重启游戏应用。
2. 准备已有的 `OptiScaler.ini`，通常位于 `ffxiv_dx11.exe` 旁。FF14 和 DR 不会自带此文件；缺失时按 [OptiScaler 安装说明](https://github.com/optiscaler/OptiScaler/wiki/Manual-Installation)，从对应版本安装包取得。
3. 按上述 DR 版本选择对应的模块产物；当前候选版按 [构建说明](TECHNICAL.md#构建与隔离验证)生成 `DR.DlssModule.dll`，在 DR 本地模块管理中添加 DLL 的完整路径并启用。
4. 运行 `/pdr dlss`，展开“配置文件”，点击“自动定位”。它使用当前游戏进程目录；自定义配置位置需手动填写完整路径。
5. “配置目标”出现有效的配置模型、倍率后，选择模型和挡位，点击“写入并刷新生效”。

| 按钮 | 用途 |
|---|---|
| 写入配置 | 备份并保存 INI，供下次启动读取 |
| 刷新生效 | 对已保存配置请求桥接更新并刷新窗口 |
| 写入并刷新生效 | 保存配置，然后请求桥接更新并刷新窗口 |

桥接更新和窗口刷新完成后显示 `OptiScaler：ok applied；窗口刷新已完成`。刷新时画面可能短暂停顿，正常完成后恢复原窗口状态。如果显示“结果未确认”，说明模块没有收到完整回执，可以在 OptiScaler 菜单中查看是否生效。

## 模型与挡位

- **游戏默认**：将模型选择交还 FF14，挡位仍单独保存；当前需要重启游戏应用。
- **K**：DLAA / Quality / Balanced 的常用选择。
- **M**：面向 Performance；**L**：面向 4K Ultra Performance。

K 要求 DLSS 文件版本至少 310.2.0，L/M 至少 310.5.0。模块按检测版本过滤选项，版本未知时只提供游戏默认。

挡位：DLAA **1×**、Ultra Quality **1.3×**、Quality **1.5×**、Balanced **1.7×**、Performance **2×**、Ultra Performance **3×**。

“配置渲染分辨率”按配置倍率计算。确认游戏当前使用的倍率时，按 **Insert** 打开 OptiScaler，查看底部的输入与输出尺寸。

## 更多说明

| 文档 | 内容 |
|---|---|
| [使用说明](USAGE.md) | 配置路径、读取判断、快捷命令、备份与常见问题 |
| [技术说明](TECHNICAL.md) | 桥接协议、窗口刷新、ReShade 重建、构建与测试 |
| [验证记录](VALIDATION.md) | 游戏内验收范围与版本记录 |
| [来源与许可](PROVENANCE.md) | 来源、许可与分发范围 |

## 持续维护

模块主线位于 [`main`](https://github.com/PalaBing/reshade-toolbox/tree/main/Modules/DR-DLSS)。每次依赖适配从 `main` 创建独立分支，完成验证后通过 PR 合入；已合并且无独有工作的分支随后清理。上游与检查范围见 [UPSTREAM.md](UPSTREAM.md)。自行构建默认产物为 `out/DR.DlssModule.dll`，构建方法见 [技术说明](TECHNICAL.md#构建与隔离验证)。
