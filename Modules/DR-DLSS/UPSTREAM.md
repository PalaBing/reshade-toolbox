# 更新来源与适配基线

本文供依赖更新和故障排查使用，列出本模块所用接口、更新来源及适配检查范围。0.1.5.0 的发布构建基线为 2026-10-03；当前 0.1.6.0 为 DR 2.2.8.0 适配候选版，最近一次上游与依赖复核为 2026-11-13，结果见下文。后续适配需重新检查目标环境中的实际依赖。

## 依赖关系

本模块由 DR 加载，使用 DailyRoutines.Common 的模块基类、配置和界面容器；通过 OmenTools 获取服务、注册命令和发送通知。框架、游戏配置与界面接口来自 Dalamud，窗口句柄和图形设备结构来自 FFXIVClientStructs。配置写入的使用方是 OptiScaler，模型支持范围由使用中的 DLSS 文件决定。

`OmenTools.OmenService` 是 OmenTools 的服务命名空间。[DR 公开模块工程](https://github.com/Dalamud-DailyRoutines/DailyRoutines.ModulesPublic/blob/main/DailyRoutines.ModulesPublic.csproj)明确引用 OmenTools，并允许从 DR 的 Dev 目录取得该 DLL。适配时既要查工具库源码变化，也要核对 DR 实际携带的二进制。

## 已核实的更新来源

| 组件 | 主要来源 | 更新后检查内容 |
|---|---|---|
| DR 宿主与 Common | [官方清单 main/pluginmaster.json](https://github.com/AtmoOmen/DalamudPlugins/blob/main/pluginmaster.json)、[DR Releases](https://github.com/Dalamud-DailyRoutines/DailyRoutines/releases)、[公开模块源码](https://github.com/Dalamud-DailyRoutines/DailyRoutines.ModulesPublic) | 按 InternalName=DailyRoutines 和当前 DalamudApiLevel 选择渠道；检查实际发布包中的宿主、Common、OmenTools。重点核对 ModuleBase 生命周期、LoadConfig/SaveConfig、ModuleInfo/Permission、Overlay 及本地模块加载方式 |
| OmenTools 工具库 | [AtmoOmen/OmenTools](https://github.com/AtmoOmen/OmenTools)、[main 提交记录](https://github.com/AtmoOmen/OmenTools/commits/main/) | DService 的服务取得与初始化、CommandManager 的子命令注册/移除、NotifyHelper 的 Notification/Chat 接口。核对日期未见独立 Release，可跟踪源码提交，并检查 DR 实际携带的 DLL |
| 国服 Dalamud | [ottercorp/Dalamud](https://github.com/ottercorp/Dalamud)、[Dalamud API 版本说明](https://dalamud.dev/versions/)、[国服启动器的框架更新实现](https://github.com/ottercorp/FFXIVQuickLauncher/blob/CN/src/XIVLauncher.Common/Dalamud/DalamudUpdater.cs) | API Level、.NET 宿主、IFramework.Update/线程约束、IGameConfig/ScreenMode、CommandInfo 和 Dalamud.Bindings.ImGui。国际服 API 公告用于提前了解迁移变化，国服构建以实际安装的 Hooks 为准 |
| 国服客户端结构库 | [ottercorp/FFXIVClientStructs](https://github.com/ottercorp/FFXIVClientStructs)、[Dalamud 子模块声明](https://github.com/ottercorp/Dalamud/blob/master/.gitmodules) | Framework/GameWindow/WindowHandle、Device/SwapChain 的尺寸、NewWidth/NewHeight/RequestResolutionChange。国服游戏补丁或框架携带的结构库更新后，重新核对布局与窗口刷新；上层源码参考 [aers/FFXIVClientStructs](https://github.com/aers/FFXIVClientStructs) |
| OptiScaler | [Releases](https://github.com/optiscaler/OptiScaler/releases)、[配置定义](https://github.com/optiscaler/OptiScaler/blob/master/OptiScaler.ini) | DLSS 选择、模型覆盖开关、RenderPresetForAll 和全局比例覆盖的含义及默认值；本模块使用全局倍率覆盖，QualityOverrides 中六个挡位倍率键属于独立配置。另查启动读取与运行时配置更新方式 |
| OptiScaler 自定义桥接 | [模块桥接客户端](RuntimeBridge.cs)，参考协议记录见 [PROVENANCE.md](PROVENANCE.md) | 管道名、消息格式、响应交付、运行时 Config 与 changeBackend 行为。官方 OptiScaler 更新可能替换掉桥接版，更新后需确认自定义管道仍在；该能力由具体构建决定。当前旧协议没有关闭模型覆盖的命令 |
| NVIDIA DLSS | [SDK Releases](https://github.com/NVIDIA/DLSS/releases)、[SDK 预设定义](https://github.com/NVIDIA/DLSS/blob/main/include/nvsdk_ngx_defs.h)、[Streamline 预设定义](https://github.com/NVIDIA-RTX/Streamline/blob/main/include/sl_dlss.h) | K/L/M 的支持版本、用途、弃用和默认行为。变化影响 PresetHelp.cs、选项过滤及写入限制；以配置目录中实际 nvngx_dlss.dll 文件版本核对 |

[AtmoOmen 更新说明页](https://info.atmoomen.top/docs/changelog/v2.2.4.0/)可补充 DR 发布内容；版本与依赖接口以实际发布包核对。

## OmenTools 报错的已知原因

2026-10-02 的 OmenTools 提交[“精简聊天 API”](https://github.com/AtmoOmen/OmenTools/commit/26ef570bde7168123cc9afe152abdafa1f182b5f)修改了 [NotifyHelper.cs](https://github.com/AtmoOmen/OmenTools/blob/main/OmenService/Implementations/Helpers/Notify/NotifyHelper.cs)：实例方法 `ChatError(string, ReadOnlySeString?)` / `Chat(string, ReadOnlySeString?)` 被移除，替换为 `(ReadOnlySeString, bool useDefaultPrefix = true)`。

经 DLL 元数据核对，DR 2.2.1.0 与 2.2.2.0 携带的 OmenTools 存在同样的签名差异。调用旧签名的已编译 DLL 会出现 `Method not found: ...NotifyHelper.ChatError(System.String, Nullable<ReadOnlySeString>)`，原因是通知接口与构建时不同。INI 读写和 DLSS 是否生效需分别查看对应日志及游戏状态。当前本模块使用 NotificationInfo/Success/Warning/Error；发布构建采用 DR 2.2.2.0，后续 DR 2.2.3.3 的编译与接口复核见下文。

两份 OmenTools 的 AssemblyVersion 都是 `1.0.0.0`，而文件 SHA-256 和方法签名不同。因此每次 DR 更新，即使 OmenTools 显示版本不变，也要比较文件哈希与所调用的接口。源码 main 的提交用于提前发现接口变化，某个 DR 发布包实际采用的接口以包内 DLL 为准。

## 根据故障定位检查范围

| 表现或日志 | 优先核对 |
|---|---|
| MissingMethodException / Method not found，指向 NotifyHelper、CommandManager 或 DService | 完整方法签名、模块构建时与当前实际加载的 OmenTools DLL。接口未变化时再排查是否加载了另一份依赖或旧模块 |
| “服务 … 尚未注册或初始化” | OmenTools 的服务注册、DR 初始化顺序、模块启用/卸载时机，以及前面的首个异常；按服务生命周期问题排查 |
| ModuleBase/Common 的 TypeLoadException、加载失败或配置调用失败 | 当前 DR/Common 接口、模块加载要求和对应 API 渠道 |
| Dalamud/ImGui 接口找不到或 API Level 不匹配 | 实际 Hooks、Dalamud 与绑定 DLL、当前 .NET 宿主及 DR 渠道 |
| 更新游戏后窗口刷新异常、输出尺寸异常或访问错误 | 对应国服客户端版本、Hooks 携带的 FFXIVClientStructs 和所使用字段/函数；原生结构布局与窗口行为还需游戏实测 |
| INI 已保存，重启后倍率或模型仍不符合预期 | 游戏实际使用的 OptiScaler 路径/版本、配置键与覆盖开关、DLSS 文件支持情况，再检查其他程序是否改写配置 |
| INI 已保存，但热切换未生效或桥接回执未确认 | 实际加载的 OptiScaler 是否含桥接、管道服务端 PID、完整响应及后端重建；旧服务端立即断开可能丢失回执。界面的配置估算尺寸按倍率计算，实际渲染输入需从运行时读取 |

故障记录应包含完整异常栈、DR 版本、实际 Hooks 目录、有关 DLL 的哈希和游戏/OptiScaler/DLSS 版本。定位时查看 OmenService 下的具体类型、方法和首个异常。

## 2026-10-03 发布构建基线

- DR/API 15：`2.2.2.0`；GitHub 发布于 `2026-10-02T12:24:37Z`。
- ModulesPublic main：`d0dc979059080751953530f8d8bbf4e9ddab051c`。
- OmenTools 源码 main：`c726d53456d56b82074a3ee8dafdef6ac1831811`；聊天 API 变化提交：`26ef570bde7168123cc9afe152abdafa1f182b5f`。目前缺少 DR 包内 DLL 与 OmenTools 源码提交的精确对应记录。
- OmenTools（DR 2.2.1.0）：AssemblyVersion `1.0.0.0`，SHA-256 `61CB46F2B99A838C96C1D3B0F965CEA03A3EF53CAAA5A170DEE4D5276365FB9B`。
- OmenTools（DR 2.2.2.0）：AssemblyVersion `1.0.0.0`，SHA-256 `C2BDFF6DD126E1B254A5250EFB3657450E551DA11FC721CEB331BCEF0DACE48B`。
- DR 2.2.2.0 的 `ChatError` / `Chat` 实例方法签名为 `(ReadOnlySeString, bool)`；本模块使用 string 参数的 Notification 方法。
- 配置通过 ModuleBase 的 LoadConfig/SaveConfig 读写。
- Dalamud：`15.0.3.6`；FFXIVClientStructs：`7.56.2.9373`；Common 的 AssemblyVersion 也是 `1.0.0.0`，需同时记录哈希。
- 国服 Dalamud 的 .gitmodules 已确认客户端结构库来自 ottercorp/FFXIVClientStructs。已发布的 Hooks 内容需核对实际安装包，源码分支和子模块用于跟踪后续变化。
- 完整构建依赖和输出哈希记录在 `out/build-info.json`，通过哈希区分相同版本号下的代码修订。

## 2026-10-13 上游与依赖复核

官方清单中 `InternalName=DailyRoutines`、API 15 渠道为 [DR 2.2.3.3](https://github.com/Dalamud-DailyRoutines/DailyRoutines/releases/tag/2.2.3.3)，发布于 `2026-10-11T09:12:08Z`。本次测试环境采用该版本和国服 Hooks `26-10-13-01`，包含 Dalamud `15.0.3.6`、FFXIVClientStructs `7.57.0.9559`。依赖哈希、构建结果与检查范围见 [验证记录](VALIDATION.md#2026-10-13-依赖兼容性复核)。

以下提交用于固定本次源码检查范围；源码分支与 DR 包内 DLL 的精确对应关系仍需发布方的构建记录。

| 来源 | 本次核对的提交或正式版 | 与模块有关的结果 |
|---|---|---|
| DR 公开模块 | [`63f6ed6c64593a82033401208682130f669ccf06`](https://github.com/Dalamud-DailyRoutines/DailyRoutines.ModulesPublic/commit/63f6ed6c64593a82033401208682130f669ccf06) | 基类、配置、权限与窗口模块用法未发现需要本模块迁移的变化；实际 Common 接口通过依赖构建复核 |
| OmenTools | [`efd5780c3b2f02d05b0bf44bc243fe320f07eb94`](https://github.com/AtmoOmen/OmenTools/commit/efd5780c3b2f02d05b0bf44bc243fe320f07eb94) | 相比发布基线新增 27 个提交。NotificationInfo/Success/Warning/Error 签名保留，CommandManager 子命令接口未变；DService 增加结构库扩展的初始化/卸载，通知增加托盘空闲阈值，序列化相关修改涉及程序集回收 |
| 国服 Dalamud | [`de97af586628ca3784786b65af253de565fd44d0`](https://github.com/ottercorp/Dalamud/commit/de97af586628ca3784786b65af253de565fd44d0) | 框架更新事件、线程判断和 ScreenMode 配置调用未变；仍采用 API 15 / .NET 10 |
| 国服客户端结构库 | [`431278ea821354b54485b5b15aa13f72d74c07fc`](https://github.com/ottercorp/FFXIVClientStructs/commit/431278ea821354b54485b5b15aa13f72d74c07fc) | Framework.GameWindow、GameWindow.WindowHandle、Device 的交换链与尺寸请求字段、SwapChain.Width/Height 的偏移及单例地址签名未变；具体偏移见验证记录 |
| OptiScaler | 正式版 [v0.9.4](https://github.com/optiscaler/OptiScaler/releases/tag/v0.9.4)（2026-07-18）；开发分支 [`2a31523be4f1fab6ec67c9bb4b64bfa28feb0b0d`](https://github.com/optiscaler/OptiScaler/commit/2a31523be4f1fab6ec67c9bb4b64bfa28feb0b0d) | 五个写入键、全局倍率和 RenderPresetForAll 的覆盖规则仍适用；六个单挡位倍率键仍存在，本模块未写入。开发分支近期变化未要求调整这些调用 |
| NVIDIA DLSS | [v310.9.1](https://github.com/NVIDIA/DLSS/releases/tag/v310.9.1)，[`374959484e79a640feaba44c93ac8cfb0a03f5b5`](https://github.com/NVIDIA/DLSS/blob/374959484e79a640feaba44c93ac8cfb0a03f5b5/include/nvsdk_ngx_defs.h)（2026-09-08） | SR 的 K=11、L=12、M=13 及现有版本门槛继续适用；本版新增的 Preset F 属于 Ray Reconstruction，不纳入本模块 SR 选项 |
| Streamline | [v2.14.1](https://github.com/NVIDIA-RTX/Streamline/releases/tag/v2.14.1)，[`2122257e0fce486f91b385aa63b9a09b0a34b363`](https://github.com/NVIDIA-RTX/Streamline/blob/2122257e0fce486f91b385aa63b9a09b0a34b363/include/sl_dlss.h)（2026-09-08） | K/L/M 定义一致；发布变化集中在 Dynamic MFG 与 Vulkan 低延迟接口，未涉及本模块手动 SR 控制 |

OptiScaler、DLSS 与 Streamline 的上述正式版均早于 10 月 3 日发布构建基线。官方 OptiScaler 源码检查覆盖 [INI 定义](https://github.com/optiscaler/OptiScaler/blob/2a31523be4f1fab6ec67c9bb4b64bfa28feb0b0d/OptiScaler.ini)、[Config 读取](https://github.com/optiscaler/OptiScaler/blob/2a31523be4f1fab6ec67c9bb4b64bfa28feb0b0d/OptiScaler/Config.cpp)与 [DLSS 模型覆盖](https://github.com/optiscaler/OptiScaler/blob/2a31523be4f1fab6ec67c9bb4b64bfa28feb0b0d/OptiScaler/upscalers/dlss/DLSSFeature.cpp)。自定义桥接构建仍按 [PROVENANCE.md](PROVENANCE.md) 单独识别；更新到官方发行版时需重新确认命名管道、完整回执和运行时更新流程。

[DR 2.2.3.0 更新说明](https://info.atmoomen.top/docs/changelog/v2.2.3.0/)记录了模块实例、程序集回收修复，以及每次重载时执行完整垃圾回收。这些变化与已记录的重载崩溃场景有关，后续游戏内复验应覆盖重载前后热切换、取消与窗口恢复；原崩溃是否解决需复现原场景后判断。

本次编译、接口和原生布局复核未发现需要修改模块源码的变化。0.1.5.0 继续按原发布范围提供，当前环境的游戏内行为见验证记录中的待复验项。

## 2026-10-17 上游与依赖复核

官方 API 15 渠道更新为 [DR 2.2.4.1](https://github.com/Dalamud-DailyRoutines/DailyRoutines/releases/tag/2.2.4.1)，发布于 `2026-10-17T06:58:13Z`。检查时本机安装的是 2.2.3.4，因此另行下载并核验 2.2.4.1 官方发布包，在隔离目录配合国服 Hooks `26-10-14-01` 构建。结果无警告通过；依赖和产物身份见 [验证记录](VALIDATION.md#2026-10-17-依赖兼容性复核)。

本次相对 10 月 13 日记录检查以下增量：

| 来源 | 当前固定版本或提交 | 与模块有关的变化 |
|---|---|---|
| DR 公开模块 | [`8b48428adb0aeaeb8502052764f4707f2d318471`](https://github.com/Dalamud-DailyRoutines/DailyRoutines.ModulesPublic/commit/8b48428adb0aeaeb8502052764f4707f2d318471) | 新增 31 个提交；生命周期、权限、Overlay 和命令注册用法未发现迁移要求，本模块现有配置调用通过发布包构建复核 |
| OmenTools | [`21af6b0ddb919ea5430c7c46933521e792ae5aa4`](https://github.com/AtmoOmen/OmenTools/commit/21af6b0ddb919ea5430c7c46933521e792ae5aa4) | 新增 6 个提交；TrayNotifier 为空时直接返回、卸载时清理世界列表，并调整 Markdown 与本地化处理。模块使用的通知、服务取得和子命令接口未变 |
| 国服 Dalamud | [`73e74411db3a2d190df705d377bf164180c298b5`](https://github.com/ottercorp/Dalamud/commit/73e74411db3a2d190df705d377bf164180c298b5) | 版本升为 15.0.3.7，更新 FFCS 子模块；所用框架事件、线程判断、ScreenMode、命令和 ImGui 接口未变 |
| 国服客户端结构库 | [`6adf065d3bc2086694629e47171b5b789ecd42a3`](https://github.com/ottercorp/FFXIVClientStructs/commit/6adf065d3bc2086694629e47171b5b789ecd42a3) | 修正 Skeleton.FinishAnimation 参数和 ResolverTester 数据；本模块未调用这些成员，窗口与交换链相关结构文件未变 |
| OptiScaler | 正式版仍为 [v0.9.4](https://github.com/optiscaler/OptiScaler/releases/tag/v0.9.4)；开发分支 [`7c2b6384ce5a0f61b922ed854af565aff2de49f2`](https://github.com/optiscaler/OptiScaler/commit/7c2b6384ce5a0f61b922ed854af565aff2de49f2) | 开发分支新增 31 个提交，主要涉及帧生成、Vulkan 与菜单；模块五个配置键、倍率和模型覆盖规则未变 |
| NVIDIA DLSS | [v310.9.16](https://github.com/NVIDIA/DLSS/releases/tag/v310.9.16)，[`9981c6350dc58c0ce21f024fd65e31e851c31689`](https://github.com/NVIDIA/DLSS/blob/9981c6350dc58c0ce21f024fd65e31e851c31689/include/nvsdk_ngx_defs.h) | 北京时间 10 月 17 日发布，说明为错误修复与稳定性改进；nvsdk_ngx_defs.h 和 nvsdk_ngx_params.h 与上次核对版本全文一致 |
| Streamline | [v2.14.16](https://github.com/NVIDIA-RTX/Streamline/releases/tag/v2.14.16)，[`625db07d4bca5abc67d781c42af7e7fb0761f1b6`](https://github.com/NVIDIA-RTX/Streamline/blob/625db07d4bca5abc67d781c42af7e7fb0761f1b6/include/sl_dlss.h) | 北京时间 10 月 17 日发布，说明为错误修复与稳定性改进；sl_dlss.h 与上次核对版本全文一致 |

[DR 2.2.4.0 更新说明](https://info.atmoomen.top/docs/changelog/v2.2.4.0/)新增按客户端适用性和验证条件过滤模块的行为。2.2.4.1 Release 正文为空，本次未取得独立版本说明。实际包内 DR 和 Common 新增强名称标识 `e52b0200c8c3c744`；Common 的 AssemblyVersion 仍为 `1.0.0.0`，应同时记录完整程序集身份。上述源码提交用于跟踪变化，不视为发布包所用源码提交的精确证明。

本次未发现需要修改控制逻辑、模型选项或配置写入代码的变化，保留 0.1.5.0 发布版本。游戏内复验需重点检查本地模块加载、列表显示、`/pdr dlss` 与 `/pdr optidlss`，以及热切换和窗口恢复；隔离构建尚未验证宿主实际加载流程。官方 OptiScaler 更新仍不代表自定义桥接已随之更新。

## 2026-10-26 上游与依赖复核

官方清单的 API 15 渠道为 [DR 2.2.5.1](https://github.com/Dalamud-DailyRoutines/DailyRoutines/releases/tag/2.2.5.1)，发布于 `2026-10-25T08:24:00Z`。本次使用已安装的同版本依赖与国服 Hooks `26-10-21-01` 隔离构建，无编译警告。实际二进制为 Dalamud `15.0.3.9`、FFXIVClientStructs `7.57.0.9602`；源码分支版本与实际 Hooks 版本分别记录，构建身份见 [验证记录](VALIDATION.md#2026-10-26-依赖兼容性复核)。

| 来源 | 固定版本或提交 | 相对 10 月 17 日的结果 |
|---|---|---|
| DR 公开模块 | [`8e2ae187f3412d7767067ae19f58f7a35ef48e27`](https://github.com/Dalamud-DailyRoutines/DailyRoutines.ModulesPublic/commit/8e2ae187f3412d7767067ae19f58f7a35ef48e27) | 新增 69 个提交；当前本模块生命周期、配置、权限、Overlay 与命令调用通过实际依赖构建 |
| OmenTools | [`925446f261cc01d803d313ae965d015afdf02652`](https://github.com/AtmoOmen/OmenTools/commit/925446f261cc01d803d313ae965d015afdf02652) | 新增 38 个提交；服务容器引入依赖注入，Init/Uninit 改为异步并分帧调度。本模块由宿主初始化服务，只调用保留的 DService.Instance、通知及子命令接口，无需迁移初始化代码 |
| 国服 Dalamud | [`ecd08fafd6092c51a011b23fe322442baaecf5ce`](https://github.com/ottercorp/Dalamud/commit/ecd08fafd6092c51a011b23fe322442baaecf5ce) | 新增 25 个提交，源码版本 15.0.3.8；涉及 Addon/Agent 生命周期、UI 与导航等内部变化，本模块所用框架、配置、命令和 ImGui 接口未变 |
| 国服客户端结构库 | [`cff64c4ae999f8b2b9dae6b13da17d84b1d391a3`](https://github.com/ottercorp/FFXIVClientStructs/commit/cff64c4ae999f8b2b9dae6b13da17d84b1d391a3) | 新增 9 个提交，涉及地图结构与 IDA 数据；Framework、GameWindow、Device、SwapChain 文件未变，所用字段偏移和单例地址签名保留 |
| OptiScaler | 正式版仍为 [v0.9.4](https://github.com/optiscaler/OptiScaler/releases/tag/v0.9.4)；开发分支 [`bc69b0bf504ee606735cce5dcd403b7aa8500754`](https://github.com/optiscaler/OptiScaler/commit/bc69b0bf504ee606735cce5dcd403b7aa8500754) | 新增 78 个提交，主要涉及 FSR 4、Vulkan、帧生成与 Overlay；本模块五个写入键及全局倍率、模型覆盖规则保留 |
| NVIDIA DLSS / Streamline | 仍为 [v310.9.16](https://github.com/NVIDIA/DLSS/releases/tag/v310.9.16) / [v2.14.16](https://github.com/NVIDIA-RTX/Streamline/releases/tag/v2.14.16) | main 分别仍为 `9981c6350dc58c0ce21f024fd65e31e851c31689` / `625db07d4bca5abc67d781c42af7e7fb0761f1b6`，没有新增 SR 模型定义或版本门槛变化 |

OptiScaler 开发提交 [`c4714282b222f5103b7d2e7ee3e4bd86dda05e96`](https://github.com/optiscaler/OptiScaler/commit/c4714282b222f5103b7d2e7ee3e4bd86dda05e96) 新增 `[Menu] FpsOverlayShowRatio`，显示实际 render → output 尺寸与倍率，可辅助后续运行时验收。该功能尚未进入正式版，也不代表现有自定义桥接构建已包含它。

本次无需修改模块控制逻辑、配置键或模型选项，保留 0.1.5.0 发布版本。新产物仅用于依赖兼容性检查；游戏内需复验宿主加载、服务初始化后的命令与通知、热切换、窗口恢复和重载行为。源码分支与已安装 DLL 的精确构建对应关系未取得。

## 2026-11-13 上游与依赖复核

官方 API 15 渠道已更新至 [DR 2.2.8.0](https://github.com/Dalamud-DailyRoutines/DailyRoutines/releases/tag/2.2.8.0)，发布于 `2026-11-10T09:38:44Z`。本次下载并校验官方发布包，配合国服 Hooks `26-11-10-01` 检查。实际 Hooks 中 Dalamud 为 `15.0.3.9`、FFXIVClientStructs 为 `7.57.0.9678`；源码分支的版本与已安装二进制分别记录。

本次发现必须迁移的 Common 接口：0.1.5.0 的 `ModulePermission / Permission` 已不适用于新包，`ModuleBase.ModuleName` 新增抽象 getter。模块现按 [官方模块示例](https://github.com/Dalamud-DailyRoutines/DailyRoutines.ModulesPublic/blob/c3cefa1d83943623945182cdcc3818f18b2c06a2/Action/AutoAethericMimicry.cs)改用 `ModuleBehaviour / Behaviour`，并返回 `nameof(OptiScalerDlssController)` 作为模块名，保留 `AllDefaultEnabled = true`。适配候选版本为 0.1.6.0；在 2.2.8.0 下构建通过，在本机 2.2.7.1 下因 Behaviour 覆写点不存在而构建失败，使用候选版前需先更新 DR。

| 来源 | 本次固定版本或提交 | 相对 10 月 26 日的结果 |
|---|---|---|
| DR 公开模块 | [`c3cefa1d83943623945182cdcc3818f18b2c06a2`](https://github.com/Dalamud-DailyRoutines/DailyRoutines.ModulesPublic/commit/c3cefa1d83943623945182cdcc3818f18b2c06a2) | 新增 167 个提交，权限声明与模块名称用法已迁移；本模块按上述两处接口修正 |
| OmenTools | [`bbb9e4efcf1666ced372ca17e9545124229137c0`](https://github.com/AtmoOmen/OmenTools/commit/bbb9e4efcf1666ced372ca17e9545124229137c0) | 新增 79 个提交；结构扩展初始化增加日志，卸载增加缓存清理，服务分帧调度改为实际帧间隔的 10% 且最多 10 ms。当前模块所用 DService、通知和子命令接口通过新发布包构建 |
| 国服 Dalamud | [`8bf76fca5e32d3554dfca89132470cf67a5ac620`](https://github.com/ottercorp/Dalamud/commit/8bf76fca5e32d3554dfca89132470cf67a5ac620) | 新增 48 个提交，源码版本 15.0.4.0；所用框架、配置、命令和 ImGui 绑定接口未变。UI Hook 初始化失败清理、ReShade 回调注册等内部修复不改变历史崩溃记录的验证状态 |
| 国服客户端结构库 cn | [`a768e0e5d4edbd827d07c9cd0024593fcee89655`](https://github.com/ottercorp/FFXIVClientStructs/commit/a768e0e5d4edbd827d07c9cd0024593fcee89655) | 新增 96 个提交，同步 7.58；Framework、GameWindow、SwapChain 未改，Device 新增能力与显卡标识字段，模块现用偏移和单例签名未变 |
| OptiScaler | 正式版仍为 [v0.9.4](https://github.com/optiscaler/OptiScaler/releases/tag/v0.9.4)；master [`5f69a25d60cdd4f99d48ea54893972b227ddf583`](https://github.com/optiscaler/OptiScaler/commit/5f69a25d60cdd4f99d48ea54893972b227ddf583) | 新增 152 个提交，扩展自定义升频器和插件 API；五个配置键、dlss 值、倍率和模型覆盖规则仍适用 |
| NVIDIA DLSS / Streamline | [v310.9.16](https://github.com/NVIDIA/DLSS/releases/tag/v310.9.16) / [v2.14.16](https://github.com/NVIDIA-RTX/Streamline/releases/tag/v2.14.16) | 版本与 main 提交均未变；K/L/M 选项无需修改 |

两项后续观察方向：

- OptiScaler 开发分支的 [Plugin SDK](https://github.com/optiscaler/OptiScaler/blob/5f69a25d60cdd4f99d48ea54893972b227ddf583/plugin_sdk/optiscaler_plugin.h) 提供 `ReInitUpscalers`，但配置 API 仅操作插件自身配置节，尚未提供本模块需要的全局模型和倍率控制。`FpsOverlayShowRatio` 仍未进入 v0.9.4 正式版。开发版新增的自定义 DLL 路径也需要未来单独适配实际加载文件识别；当前模块按 INI 同目录检测 DLSS 文件版本。
- 国服结构库新增 [PostEffect.Dlss](https://github.com/ottercorp/FFXIVClientStructs/blob/a768e0e5d4edbd827d07c9cd0024593fcee89655/FFXIVClientStructs/FFXIV/Client/Graphics/PostEffect/Dlss.cs)，公开 Requested/Applied 尺寸、QueryRenderSettings 和 UpdateSettings。游戏侧记录是否与 OptiScaler 实际输入一致、能否减少窗口刷新，需要独立验证；本次适配未接入这些原生方法。

本次验证仅覆盖构建与隔离检查，0.1.6.0 尚未部署或发布。完整依赖、失败与修复证据、游戏内待验项目见 [验证记录](VALIDATION.md#2026-11-13-适配候选版)。

## 更新处理

DR 更新后，先比较官方清单、Release、实际安装包和运行环境中的 DLL，再查看 OmenTools 的相关提交与 DR 公开模块的适配用法。每次 DR 包更新均比较 Common/OmenTools 文件哈希；有差异时核对本模块实际调用的接口，即使程序集版本号没有改变。

Dalamud 或游戏更新时，补查国服框架和结构库；OptiScaler 或 DLSS 更新时，补查对应配置键和预设支持范围。是否修改本模块，取决于变化是否涉及本模块使用的接口和行为。

相关变化需要检查对应接口、修复并重新构建，运行受影响的文件写入或快照测试。构建与隔离检查通过后，还需在游戏内复验窗口行为及实际 DLSS 输入尺寸。验证结果记录在 [VALIDATION.md](VALIDATION.md)。

维护采用 `main` 主线与每次适配的独立分支。适配分支从 `main` 创建，提交应记录目标版本、受影响接口和验证结果；完成验证并合并后清理分支。版本交付方式见 [README](README.md#持续维护)。
