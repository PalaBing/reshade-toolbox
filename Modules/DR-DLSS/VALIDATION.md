# 验证记录

最近更新：2026-11-13。本文说明各版本的构建、隔离检查、游戏内测试和已知问题。当前源码为 `0.1.6.0` 适配候选版，使用 DR `2.2.8.0` / API 15 官方包完成构建；已发布预发布版仍为 `0.1.5.0`，其发布基线为 DR `2.2.2.0`。各次检查分别记录日期与范围。

## 2026-11-13 适配候选版

### 失败与修复

官方清单的 API 15 渠道为 DR 2.2.8.0。本次先用已安装的 2.2.7.1 构建 0.1.5.0，出现 `CS0246: ModulePermission could not be found`。再用 2.2.8.0 官方包构建原源码，复现相同类型缺失，同时出现 `Permission: no suitable method found to override` 和 `ModuleBase.ModuleName.get` 抽象成员未实现。

实际 Common 元数据核对表明，2.2.7.1 已将权限类型改名为 ModuleBehaviour，但属性仍为 Permission；2.2.8.0 将属性改为 Behaviour，并将 ModuleName 从 virtual 改为 abstract。本次按官方模块用法修改：

- `ModuleBehaviour Behaviour` 继续设置 `AllDefaultEnabled = true`。
- `ModuleName` 返回 `nameof(OptiScalerDlssController)`，与现有配置文件名一致，配置类与字段保留。
- 版本标识升为 0.1.6.0，以区分旧依赖构建的 0.1.5.0。

适配后的源码在 2.2.8.0 下构建成功，无警告；在 2.2.7.1 下因 Behaviour 覆写点不存在而失败。本候选以 2.2.8.0 为目标，使用前需先更新 DR。窗口刷新、桥接、INI、快照及模型帮助五个 C# 文件与上次构建的哈希一致。

### 发布包与构建身份

[DR 2.2.8.0 官方包](https://github.com/Dalamud-DailyRoutines/DailyRoutines/releases/tag/2.2.8.0)的 latest.zip 为 21,547,329 字节，SHA-256 `F17A6A189586E7C0B1B9FFE8172854D2083CC270A485D3921304C54473395B3F`，与 GitHub 发布资产 digest 一致。包解压至隔离目录，未替换安装目录。

构建环境为 PowerShell 7.6.9 / .NET 10.0.14、DR 2.2.8.0 官方包、国服 Hooks `26-11-10-01`。在模块目录可用下列命令复现：

```powershell
./Build.ps1 -PluginDirectory '<DR 2.2.8.0 发布包解压目录>' -HookDirectory '<Hooks 26-11-10-01 目录>' -OutputDirectory './out/compatibility/DR-2.2.8.0'
```

| 文件 | 完整程序集身份 | SHA-256 |
|---|---|---|
| DailyRoutines.dll | `DailyRoutines, Version=2.2.8.0, Culture=neutral, PublicKeyToken=d095e07dca186b72` | `CD49FB69E83C3784BFBE9397388ECD063AF5E3FBD61B08DB9C1A8F1CB62A0735` |
| DailyRoutines.Common.dll | `DailyRoutines.Common, Version=1.0.0.0, Culture=neutral, PublicKeyToken=d095e07dca186b72` | `26718C087F45288BC50805B5D3421B6AC8D4E40D45C46C85D3824926BFF62290` |
| OmenTools.dll | `OmenTools, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null` | `3C92DD0ED67695EDCE19D51514EFC6A57ADA381708ED66565115D8588531F7FA` |
| Dalamud.dll | `Dalamud, Version=15.0.3.9, Culture=neutral, PublicKeyToken=null` | `8BE9012701CE801E53B16E80A0035C402CAA9169E9EC94D5E5033948F9C1E672` |
| FFXIVClientStructs.dll | `FFXIVClientStructs, Version=7.57.0.9678, Culture=neutral, PublicKeyToken=null` | `3F8BA772322CDDD70A54BBE1F9F19958F5D32CCB55BC92C58B68CFA0C2B5D504` |

候选产物为 `out/compatibility/DR-2.2.8.0/DR.DlssModule.dll`，SHA-256 `EF3B19105335C083251D7E97264B5B946942C326CB88ECE242AD38974D75553B`；同目录 build-info.json 保存依赖与源码哈希。源码分支的 Dalamud 15.0.4.0、FFCS 7.58 同步记录与此处实际 Hooks 身份不同，未将源码分支等同于安装包。

### 检查结果与待验收

现有隔离脚本通过：INI 60 项、快照 42 项、桥接 46 项，共 148 项。检查使用临时配置和随机命名模拟管道，未向游戏发送 set/save。修正前的编译失败与修正后的成功构建验证了此次接口迁移；独立元数据复核确认新声明匹配目标 Common。

候选 DLL 尚未部署或发布。游戏内待验收：DR 2.2.8.0 本地模块加载与列表显示、旧配置读取、两个命令入口与通知、DLAA / Performance 双向热切换的实际尺寸、窗口恢复、取消及重载。历史 GUI 析构回调崩溃保持原状态。图形配置及原生布局的上游检查见 [UPSTREAM.md](UPSTREAM.md#2026-11-13-上游与依赖复核)。

## 2026-10-26 依赖兼容性复核

官方清单确认 API 15 渠道为 [DR 2.2.5.1](https://github.com/Dalamud-DailyRoutines/DailyRoutines/releases/tag/2.2.5.1)，发布于 2026-10-25。本次使用本机已安装的同版本依赖及国服 Hooks `26-10-21-01`，将检查产物写入独立目录；未替换已发布 DLL 或游戏安装文件。七个 C# 源文件哈希与 10 月 17 日构建记录一致。

使用 PowerShell 7 / .NET 10 重新构建成功，无编译警告。复现时在模块目录执行：

```powershell
./Build.ps1 -PluginDirectory '<DR 2.2.5.1 安装目录>' -HookDirectory '<Hooks 26-10-21-01 目录>' -OutputDirectory './out/compatibility/DR-2.2.5.1'
```

| 文件 | 完整程序集身份 | SHA-256 |
|---|---|---|
| DailyRoutines.dll | `DailyRoutines, Version=2.2.5.1, Culture=neutral, PublicKeyToken=b6b4daf5a48be2fa` | `9792A94BF524241BD25BDBADAD8E767FE1A15C736B5E47AF02D2DCC4647B9B26` |
| DailyRoutines.Common.dll | `DailyRoutines.Common, Version=1.0.0.0, Culture=neutral, PublicKeyToken=b6b4daf5a48be2fa` | `CFC3B7BA61D6991ED23827E8A6DE82930E0D95F987D604285D5B5471D5741C37` |
| OmenTools.dll | `OmenTools, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null` | `49AABD6DDE681924A33320057EFB35DF661931C1C9A4052CCA5F25B605383EBD` |
| Dalamud.dll | `Dalamud, Version=15.0.3.9, Culture=neutral, PublicKeyToken=null` | `AF15D754340C659F9F57C110C9914D8C68A068F588725CAAE50AC237B07DA1C4` |
| FFXIVClientStructs.dll | `FFXIVClientStructs, Version=7.57.0.9602, Culture=neutral, PublicKeyToken=null` | `5726E301EE5456D526F524E9F88659F38A8717CB527FE4A86D83D1ADE0AB9798` |

产物 `out/compatibility/DR-2.2.5.1/DR.DlssModule.dll` 的 SHA-256 为 `09F3E675E7FA93893EC0A3826C2F69BB60B9B394F53EED220B1258AD94DDAB54`；同目录 `build-info.json` 保存源码和依赖哈希。版本仍为 0.1.5.0，检查产物未发布。

通过 System.Reflection.Metadata 比较上次与本次隔离 DLL，127 个带方法体的方法的所属类型名、方法名、签名字节、IL 字节和局部变量签名一致；316 个成员引用的名称与签名字节一致。该比较未包含异常区域、所有元数据表或宿主实际绑定行为。程序集引用中 Common token 从 `e52b0200c8c3c744` 变为 `b6b4daf5a48be2fa`，Dalamud 从 15.0.3.7 变为 15.0.3.9，FFXIVClientStructs 从 7.57.0.9563 变为 7.57.0.9602；完整 DLL 哈希不同。

上游源码的框架接口与窗口相关结构未发现迁移要求。OmenTools 的服务初始化改为异步分帧，本模块没有调用 Init/Uninit，所用服务取得、通知和子命令接口通过当前依赖构建。源码版本与实际 Hooks 二进制版本分别记录，详见 [上游复核](UPSTREAM.md#2026-10-26-上游与依赖复核)。

本次未发现需修改模块源码或发布替换 DLL 的变化。游戏内待复验：本地模块加载、列表显示、服务就绪后的 `/pdr dlss` 与 `/pdr optidlss`、通知、DLAA / Performance 双向热切换的实际输入尺寸、窗口恢复，以及重载、取消和异常路径。本次未执行插件初始化或向游戏发送请求，历史 GUI 析构回调崩溃保持原记录。

## 2026-10-17 依赖兼容性复核

### 发布包与构建环境

从 [DR 2.2.4.1 官方 Release](https://github.com/Dalamud-DailyRoutines/DailyRoutines/releases/tag/2.2.4.1)下载 `latest.zip`，大小 17,907,988 字节，SHA-256 为 `2223BD2F0C5D5404D0F1DA02B9982E14668F1DE533C57132B86F3D9ED6A61A4C`，与 GitHub 发布资产提供的 digest 一致。检查时本机 DR 为 2.2.3.4；最新包在隔离目录解压，未替换安装目录。

0.1.5.0 的七个 C# 源文件哈希与 10 月 13 日记录一致。使用最新包、国服 Hooks `26-10-14-01`、PowerShell `7.6.5` / .NET `10.0.11` 构建成功，无编译警告。复现时在模块目录执行，并替换两个依赖路径：

```powershell
./Build.ps1 -PluginDirectory '<DR 2.2.4.1 发布包解压目录>' -HookDirectory '<Hooks 26-10-14-01 目录>' -OutputDirectory './out/compatibility/DR-2.2.4.1'
```

| 文件 | AssemblyVersion | PublicKeyToken | SHA-256 |
|---|---|---|---|
| DailyRoutines.dll | 2.2.4.1 | `e52b0200c8c3c744` | `5685329F353903E914CEA3789B8523662588F9B1EED9145B440A58935062B1A6` |
| DailyRoutines.Common.dll | 1.0.0.0 | `e52b0200c8c3c744` | `7BC2529698F57AE5A04EAF37F29BBC5988493B288656443587771C2536895550` |
| OmenTools.dll | 1.0.0.0 | `null` | `04E245875BFF7A31760EBFAE675BCC8BFDAD90F736C40D25C03C3A049C1EB8DE` |
| Dalamud.dll | 15.0.3.7 | `null` | `3B11B7CB02D2FEC8ECC59D7065732204DB2C2A8CC3CB79A0504FA1C426656FC3` |
| FFXIVClientStructs.dll | 7.57.0.9563 | `null` | `8E5F778D5D77BECD8B5F21B816F345D2D9578023AEDA867404F92C36F55825CC` |

隔离产物为 `out/compatibility/DR-2.2.4.1/DR.DlssModule.dll`，SHA-256 为 `81046C93F34139A109EE91FD944ACE1981D7B3AE66FF78510C4C78C9A122D4C7`。同目录 `build-info.json` 保存完整程序集身份、源码与依赖哈希；该产物用于兼容性检查，尚未作为新版本发布。

### 接口与依赖身份检查

通过 Mono.Cecil 比较 10 月 13 日和本次隔离产物，两者均有 127 个带方法体的方法，方法完整名称、解析后的指令文本、局部变量类型和异常处理区域一致。程序集引用另行比较：Common 引用新增 `PublicKeyToken=e52b0200c8c3c744`，Dalamud 引用升为 `15.0.3.7`，结构库引用升为 `7.57.0.9563`。这次比较不将两个 DLL 记作字节相同，也不将依赖身份变化记作控制逻辑修改。

Common 版本号未变但新增强名称，需要在后续排查中保留其完整身份。[.NET AssemblyLoadContext 设计说明](https://github.com/dotnet/runtime/blob/v10.0.0/docs/design/features/assemblyloadcontext.md)说明 .NET Core 的程序集绑定忽略强名称 token；仅此标识变化未构成本模块必须改写或重新签名的依据。DR 实际加载和模块过滤行为仍须在宿主内复验。

[国服 Dalamud 增量](https://github.com/ottercorp/Dalamud/compare/de97af586628ca3784786b65af253de565fd44d0...73e74411db3a2d190df705d377bf164180c298b5)只修改版本及结构库 pin；[结构库增量](https://github.com/ottercorp/FFXIVClientStructs/compare/431278ea821354b54485b5b15aa13f72d74c07fc...6adf065d3bc2086694629e47171b5b789ecd42a3)未涉及 Framework、GameWindow、Device 或 SwapChain，10 月 13 日记录的原生字段偏移继续适用。模型定义与配置规则的复核见 [UPSTREAM.md](UPSTREAM.md#2026-10-17-上游与依赖复核)。

### 结果与待复验项

本次未发现需要调整模块源码的接口或布局变化，发布版本保持 0.1.5.0。最新包已通过源码构建与上述静态检查；本次未执行插件初始化、未向游戏发送桥接或窗口操作请求。DR 2.2.4.0 新增模块过滤规则，因此游戏内需复验本地 DLL 加载、模块列表显示、两个命令入口，再检查 DLAA / Performance 双向热切换后的实际输入尺寸、窗口恢复，以及模块重载、取消和异常路径。历史 GUI 析构回调崩溃仍保留原状态。

## 2026-10-13 依赖兼容性复核

对 0.1.5.0 源码使用 DR `2.2.3.3` / API 15、国服 Hooks `26-10-13-01` 重新构建，结果成功，无编译警告。构建宿主为 PowerShell `7.6.5` / .NET `10.0.11`；构建仅读取依赖元数据，未向游戏发送设置或刷新请求。

复现时，在模块目录运行以下命令，将占位路径替换为对应版本的实际安装目录：

```powershell
./Build.ps1 -PluginDirectory '<DR 2.2.3.3 安装目录>' -HookDirectory '<Hooks 26-10-13-01 目录>' -OutputDirectory './out/compatibility/DR-2.2.3.3'
```

### 测试环境与依赖

| 文件 | AssemblyVersion | SHA-256 |
|---|---|---|
| DailyRoutines.dll | 2.2.3.3 | `7558F709D88416220CA4DD7E68AB4D0934F60D4CD4EA1037CAE96815C4ED5934` |
| DailyRoutines.Common.dll | 1.0.0.0 | `E4F000827A01CA48EC89A305153B70510856EC16FCE2F223D20FA19CFF9011C1` |
| OmenTools.dll | 1.0.0.0 | `724B1BBB55F0BAA731C83736D9912993D85D8B5D8DBED792C31D5535A6445976` |
| Dalamud.dll | 15.0.3.6 | `1DBCC7C1471FFEB3D7577BCFCE897428492FD6DF07D65B125E5F3537043BDD8C` |
| FFXIVClientStructs.dll | 7.57.0.9559 | `4D2DE9C55A4ABC93311F31E2A06B7105283F6F2AC6B0D55929DEF98C658C2F41` |

隔离产物为 `out/compatibility/DR-2.2.3.3/DR.DlssModule.dll`，大小 43,520 字节，SHA-256 为 `F79EA16DC73D766835B644F7D008DE12F05B44C1FD8E79E2A6B8A1C8AA9D3CB0`，源码与依赖哈希由同目录的 `build-info.json` 记录。这是本次检查产物，发布附件仍对应下文的 `0398B45D496A…` 哈希。

### 接口与原生布局检查

对照发布 DLL 与隔离产物，127 个具有 IL 的方法的所属类型名、方法名、签名字节和 IL 字节一致；316 个成员引用的名称和签名字节也一致。两个文件的完整 SHA-256 不同，此项比较只覆盖所列方法和引用数据。

原生布局检查比较国服结构库旧提交 `6e404c6089fbc2ba09c5d810cba7fe7f485f59a1` 与当前提交 `431278ea821354b54485b5b15aa13f72d74c07fc`（[源码差异](https://github.com/ottercorp/FFXIVClientStructs/compare/6e404c6089fbc2ba09c5d810cba7fe7f485f59a1...431278ea821354b54485b5b15aa13f72d74c07fc)），窗口刷新和输出尺寸读取所用字段均未变化：

| 字段 | 两次源码中的偏移 / 类型 |
|---|---|
| Framework.GameWindow | `0x07A8` / `GameWindow*` |
| GameWindow.WindowHandle | `0x18` / `nint` |
| Device.SwapChain | `0x70` / `SwapChain*` |
| Device.RequestResolutionChange | `0x7A` / `byte` |
| Device.NewWidth / NewHeight | `0x9E8` / `0x9EC`，均为 `uint` |
| SwapChain.Width / Height | `0x38` / `0x3C`，均为 `uint` |

`Framework.Instance()`、`Device.Instance()` 的单例地址签名未变。OmenTools 的通知和命令接口、Dalamud 的框架更新与 ScreenMode 调用未发现需要迁移的变化。完整上游来源与提交见 [UPSTREAM.md](UPSTREAM.md#2026-10-13-上游与依赖复核)。

### 结果与待复验项

本次检查支持继续使用现有 0.1.5.0，无需因上述上游变化修改源码或发布替换 DLL。本次未新增游戏内验收；在 DR 2.2.3.3 / 当前 Hooks 环境中，需复验模块加载、DLAA 与 Performance 双向热切换后的实际输入尺寸、窗口状态恢复，以及重载前后的取消和异常处理。

DR 2.2.3.0 的[更新说明](https://info.atmoomen.top/docs/changelog/v2.2.3.0/)记录了模块实例和程序集回收修复，以及重载时执行完整垃圾回收。0.1.4.0 的 GUI 析构回调崩溃仍保留原记录；验证原崩溃场景后再更新其状态。

## 0.1.5.0

### 构建与源码检查

编译成功，无编译警告。源码复核覆盖窗口恢复、取消和异常处理，未发现恢复路径退化。本版修改集中在重复恢复操作、固定等待和操作结果显示。

运行日志中，一次恢复 2560×1440 的 ResizeBuffers 调用与运行环境重建完成之间相隔约 15.9 秒。同期间 MotionFocus.fx 编译耗时 16.277 秒，编译结束后才继续重建，随后又发生一次同尺寸的 ResizeBuffers。ReShade 6.6.1 的重置流程会等待效果编译线程结束，和这次停顿的时间线吻合。

本版保留已成功触发热切换的临时尺寸与完整显示器尺寸请求，按实际矩形、完整 WindowPlacement 和交换链状态分别判断是否省去最终恢复操作。有待处理尺寸请求时继续执行恢复；原无边框且状态已经完全恢复的路径，固定等待可由 4.8 秒降为约 3.2 秒，不含游戏与 ReShade 内部耗时。流程结束后显示桥接回执和窗口刷新结果，失败时显示原因。

### 游戏内测试范围

2026-10-03 的使用反馈覆盖六个挡位，均可正常使用，切换速度可接受；测试期间未调整 ReShade 设置。运行日志记录加载版本为 0.1.5.0，DLL 哈希前缀为 `0398B45D496A`，与发布构建一致。

测试挡位为 DLAA（1.0）、Ultra Quality（1.3）、Quality（1.5）、Balanced（1.7）、Performance（2.0）和 Ultra Performance（3.0）。该记录来自使用反馈，没有逐项计时数据。

尚未取得所有模型与挡位组合、其他窗口模式、取消和异常恢复、长期运行及热重载场景的逐项游戏测试结果。0.1.4.0 的 GUI 析构回调崩溃根因仍未定位，相关日志见下文。

### 发布产物

0.1.5.0 按上述测试范围作为预发布版提供，0.1.4.0 保持撤回。DLL 大小 43,520 字节，SHA-256 为 `0398B45D496ABB325EDB6C378B6AABA52F0BE0F52C472D07998BCEE0EF9FCA69`。

## 0.1.4.0

### 尺寸重建修复与隔离检查

0.1.3.0 在临时窗口阶段使用客户区宽高，并在无边框后直接恢复原窗口状态。0.1.4.0 改为先按临时矩形请求尺寸变化，再在无边框模式稳定后明确设置显示器矩形并请求显示器宽高，最后恢复原模式、位置和最大化状态。最终恢复请求仍使用实际客户区宽高；所有游戏操作在框架更新线程执行，取消和异常时尝试恢复。

OptiScaler 的后端重建会复制旧实例的渲染与显示尺寸，并不调用游戏的最佳输入尺寸查询。配置倍率变化需要游戏重新查询并更新资源，才能体现到实际输入。源码依据见 [输入尺寸查询](https://github.com/optiscaler/OptiScaler/blob/v0.7.7-pre9/OptiScaler/NVNGX_Parameter.h)和[后端重建](https://github.com/optiscaler/OptiScaler/blob/v0.7.7-pre9/OptiScaler/inputs/NVNGX_DLSS_Dx11.cpp)。

本版使用 DR 2.2.2.0 / Dalamud API 15 依赖编译成功，无编译警告；46 项隔离桥接检查通过，窗口流程完成源码复核。界面显示“配置目标”和“配置倍率”，估算值与 OptiScaler 菜单中的实际输入尺寸分别查看。

### 崩溃报告与撤回

2026-10-03 的一次游戏测试中，点击“写入并刷新生效”后进程崩溃。0.1.4.0 Release 随后转为草稿，暂停公开下载。

日志记录 DR 全模块卸载及重载，随后交换链变为 2432×1368，与 2560×1440 的 0.95 倍临时尺寸吻合。之后 CLR 通过 Environment.FailFast 终止进程，原因为调用了已被垃圾回收的 `AtkComponentBase.Delegates.Dtor` 回调。崩溃堆栈没有直接指向 WindowRefresh、RuntimeBridge 或 OptiScaler，日志也没有记录模块刷新各阶段的直接调用信息。

本模块没有安装 GUI 析构 Hook。尺寸刷新可能触发了失效的 GUI 回调，但日志缺少回调所属模块和节点的信息；离线转储分析也没有完整托管堆，无法找到被回收的委托对象或具体 GUI 节点。DR 卸载重载与崩溃的关系仍待定位。

### 热切换与延迟记录

重启后，OptiScaler 菜单显示实际尺寸 `2560×1440 → 2560×1440（1.0）`，确认保存的配置在启动时生效。随后同一次游戏运行中的测试完成 `2.0 → DLAA` 和 `DLAA → 2.0` 双向热切换，记录了热切换成功反馈。涉及 DR 全模块重载的崩溃场景仍需单独排查。

同轮使用反馈还报告刷新期间长时间停顿。0.1.4.0 有六次固定等待，默认合计 4.8 秒，由框架回调推进；停顿还涉及游戏和 ReShade 的资源重建耗时。后续 0.1.5.0 对重复恢复和等待进行了精简，见上文。

## 0.1.3.0

### 桥接修复与检查

0.1.2.0 在同一次游戏运行中切到 DLAA、写入配置并刷新窗口后，INI 已显示 1×，ReShade 效果画面仍异常。源码对照发现该版本只写入 INI 和刷新窗口，遗漏了更新 OptiScaler 内存参数及请求 DLSS 后端重建的桥接步骤。

0.1.3.0 增加 K/L/M 与挡位的桥接请求，收到回执后执行窗口刷新；单独写入配置仍只修改文件。界面将估算尺寸标为“配置渲染分辨率”。旧协议无法关闭模型覆盖，游戏默认仍需保存配置后重启应用。桥接材料及自定义构建的识别信息见 [PROVENANCE.md](PROVENANCE.md)。

旧服务端回复后立即断开且未排空，可能丢失回复。客户端预先等待回复并严格核对响应；set 发送后失去回执时报告“结果未确认”，不重复发送。[Windows 的断开说明](https://learn.microsoft.com/en-us/windows/win32/api/namedpipeapi/nf-namedpipeapi-disconnectnamedpipe)说明断开时可能丢弃尚未读取的数据。

- **编译**：使用 DR 2.2.2.0 / Dalamud API 15 实际依赖构建成功，无编译警告。
- **桥接协议**：46 项隔离命名管道检查通过，覆盖命令顺序、K/L/M、save 0、文化区、进程验证、严格响应、断开、超时、取消、只读探测重试及默认模型拒绝。使用模拟服务端，没有向游戏发送 set/save。
- **真实服务连通**：只读检查确认服务端属于测试中的游戏进程。先发送后读取时出现 EOF；预先挂起读取后，三次 ping 均返回完整 `ok bridge-ready`。此项检查只发送 ping。

### 游戏内失败记录

DLAA 热切换测试未通过：桥接接受请求，OptiScaler 内存中的比例已为 1.000，实际运行尺寸仍为 `1280×720 → 2560×1440（2.0）`。重新加载 ReShade 后尺寸未变化，游戏仍沿用原来的 2× 输入尺寸。后续尺寸重建修复见 0.1.4.0 记录。

## 0.1.2.0

### 编译与隔离检查

- **编译**：Build.ps1 使用 DR `2.2.2.0`、OmenTools、Dalamud API 15 和 FFXIVClientStructs 元数据构建成功，无编译警告。合入检查时按同一兼容基线重新构建成功，详细哈希由 `out/build-info.json` 记录。
- **INI 行为**：60 项检查通过。隔离临时副本覆盖五键更新、游戏默认关闭模型覆盖且仍保存挡位、拒绝旧/弃用选项、保留无关内容与换行、原字节备份及恢复，以及重复键、缺失节、BOM、非法参数、非法 UTF-8 和临时文件清理。默认自含样本及指定外部样本各通过 60 项检查，外部原样本哈希未变。未模拟其他程序同时改写文件。
- **快照与估算**：42 项检查通过。隔离临时配置覆盖外部修改后重读、覆盖关闭/auto、默认值 0、非法倍率、读失败清值、文化区独立解析，以及 DLAA、2 倍、1.7 倍和边界取整的估算。测试无需安装或运行游戏，脚本不预设特定游戏目录。
- **模型版本门槛**：9 项检查通过，覆盖游戏默认在未知版本下可用、K 的 310.2 边界、L/M 的 310.5 边界、未知文件版本及弃用选项拒绝。
- **源码复核**：覆盖模块生命周期、配置读写、选项范围及构建方式。后台读取文件，框架线程采样输出尺寸；快照按路径和读取批次（generation）筛选，保留待选值及手动读取、写入后的新结果，卸载时注销回调。模型列表、命令和写入采用相同的选项范围。

### 游戏内测试范围

2026-10-03 的使用反馈覆盖以下操作，主要目标为保存配置后重启游戏生效：

1. 加载 0.1.2.0，作者栏为 VintageVelvet；模型、挡位及配置路径完整单行显示，选项间距充分；列表提供游戏默认/K/L/M。
2. 选择 K/Quality 并写入，配置为 Preset 11、比例 1.500000；选择游戏默认后覆盖关闭、预设值 0，备份可读取。
3. “刷新生效”和“写入并刷新生效”结束后恢复原窗口模式、位置、尺寸和最大化状态。
4. 关闭并重启游戏后，目标配置被实际读取。
5. 外部修改 INI 后，配置显示约一秒更新且不覆盖待选值；窗口分辨率变化时输出尺寸更新，估算渲染尺寸随倍率变化，例如 3840×2160、2.0 倍对应 1920×1080。

最小化、无效参数等异常场景没有逐项游戏测试结果。该版本的同次运行热切换随后发现问题，见 0.1.3.0 记录。界面每秒读取磁盘配置，实际 DLSS 输入尺寸通过 OptiScaler 菜单查看。

### 发布产物

[0.1.2.0 预发布页](https://github.com/PalaBing/reshade-toolbox/releases/tag/dr-dlss-v0.1.2.0)的标签对应源码提交 `5ceb12d2aa61c962a250f1d61566ace5ad29d0f2`。附件为 DR.DlssModule.dll 与 SHA256SUMS.txt。DLL 大小 33,792 字节，SHA-256 为 `74F21735EC13B22484BAE74D480B59B20F915854309264BD93F74D0FEA1A7DDF`；发布附件与构建记录核对一致。
