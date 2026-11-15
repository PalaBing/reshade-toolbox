# CopyrightAdaptive 验证记录

历史记录保留当时的日期、输入哈希与路径；2026-11-13 按项目从仓库公共验证文档迁入。旧路径对应当时的源码布局，当前操作入口见项目说明。后续修改另增日期条目。

使用入口：[项目说明](../README.md)。

## 目录迁移与脚本路径 — 2026-11-13

- 主 FX 移至项目根，三个样式头文件与许可移至相邻的 `CopyrightAdaptive/`；主 FX 的 Git 内容标识与迁移前一致，样式头文件仅更新生成脚本路径注释。
- 四份脚本移至 `scripts/`。打包器按指定 Git 提交识别新旧源码布局，安装器默认定位整个工具箱，样式同步默认写入项目内的 `CopyrightAdaptive/`。
- 在 PowerShell 7.6.9 和 Windows PowerShell 5.1.26100.9818 中各通过 56 项隔离断言。临时 Git 仓库分别包含旧布局与新布局的已提交样本，检查包内五个文件的路径、哈希、相对 include，以及未提交内容不进入安装包。
- 路径与拒绝用例覆盖默认和显式输出、从其他工作目录调用、已有输出拒绝覆盖、无效提交拒绝，以及新旧布局都使用指定提交的内容。
- 模拟 ReShade 目录检查 `-WhatIf`、默认及显式仓库定位、安装并另存迁移、更新备份、原 INI 与原预设保持、同步默认输出和独立预设转换。输入为隔离样本，未操作游戏安装。
- 使用当前历史提交 `9fc6e7e55a079e2e9c7a0a789c4a639c5a1058e0` 也生成了五文件安装包，确认旧提交仍可由新脚本打包；新布局通过临时 Git 提交验证。
- 本次检查覆盖目录、脚本与打包行为，未重新编译 FX 或验证游戏画面；临时样本与产物已清理。

## CopyrightAdaptive v2 — 2026-10-05

输入来自独立试用版，仓库主 `.fx` 与三个私有头文件按原字节整合。使用 [LandscapeComposition 工具记录](../../LandscapeComposition/docs/VALIDATION.md#工具与来源)中的 ReShade Testing Initiative 普通 `reshadefx_cli`（SHA-256 `986FFD4ABE4BBDDAA504D2A68044F0B676AEFA958B8BCE8B90B468EB02DC408A`），DX11 / Shader Model 5.0。

| 检查 | 范围 | 结果 |
|---|---|---|
| 菜单选择预处理与主像素入口 DXBC 编译 | 全部 120 个紧凑菜单项，2560 × 1440 | 120 次退出码 0，生成非空字节码 |
| Custom 47 主像素入口 DXBC 编译 | 默认画面尺寸、固定 800 × 100，各覆盖 2560 × 1440 和 3840 × 2160 | 4 次退出码 0，生成非空字节码 |
| 编号、UI、素材和倍率分支检查 | 266 条真实 FX 预处理记录，包含原编号、菜单优先级、移除项、越界与手动 Custom | 全部检查通过；原编号输出与对应已编译菜单输出作规范化比较 |
| 私有列表与素材 | 菜单数 45 / 63 / 12，保留原款式 ID；缺图 5 项被移除 | 有效菜单素材路径均存在，生成头文件无五个缺失文件名引用 |
| 同步工具 | Windows PowerShell 5.1，连续相同输入 | 三个头文件及清单 SHA-256 一致，原主文件和三个来源头文件未改变 |
| 预设迁移工具 | PowerShell 7、Windows PowerShell 5.1，各 10 个组合与拒绝用例 | 全部通过；源文件哈希不变，已有输出及新版段拒绝覆盖 |

编译输入 SHA-256：

| 文件 | SHA-256 |
|---|---|
| `Shaders/CopyrightAdaptive.fx` | `79B1193492F640CAE136CAAC46C12E9106AE73F18CBF538E4286991112C5F903` |
| `Shaders/CopyrightAdaptive/CopyrightTex_XIV_AUR.fxh` | `E9B7FF9C2BE3F25BADE9CFA8A10E84BF663E791D37B58A9881F98D027FD3C97F` |
| `Shaders/CopyrightAdaptive/CopyrightTex_XIV.fxh` | `E113919E65A057E5D7E8C916A13F1D44AD84368CF252B6A419F97DB2D6E653CF` |
| `Shaders/CopyrightAdaptive/CopyrightTex_Custom.fxh` | `79D5B2CD5C403696E9BE4ABF1048874F609EC486B06527C572292CC90670741A` |

依赖输入 SHA-256：`ReShade.fxh` 为 `2FFFB6009B9593BC43473861E5B189A6FAEE1BD7465913FA8799B85D77DF066F`，`Blending.fxh` 为 `BD88417D571B5719B8D3091DEB6ED2F19795D5967679C73CEF9156ACDF62B01B`。两者由安装环境提供，未随仓库分发。

素材审计读取原列表的 125 个选项：91 个引用文件名中有 86 个实际 PNG，全部完成签名、完整解码和 RGBA 转换；缺失为四个内置图片及 Custom 默认 `cLayerA.png`。声明绘制尺寸与 PNG 实际尺寸不同的项目保留原规则，未据此修改样式。

新版尚未在游戏内验证。安装后需查看菜单绑定、强制切换 4K 和恢复 2K 后的显示，以及预设重新加载。原始报告含本机路径，仓库保留上述摘要。

### 仓库发布格式

上表记录实际编译输入的哈希。提交时按仓库配置统一为 LF，并清理行尾空白；其余字符、字符串与实现保持一致。发布文件 SHA-256：

- Shaders/CopyrightAdaptive.fx: 54211A986FAEE9B1902301F781CC76C290D5114F68F12B8710D9FFF97637CF30
- Shaders/CopyrightAdaptive/CopyrightTex_XIV_AUR.fxh: D5EF7AD0CFD288BBF5192F750FE02449D129A5588B3DF3A4D642B1B9548A36F8
- Shaders/CopyrightAdaptive/CopyrightTex_XIV.fxh: 030C9398FFD61F58C97B5AC4E4340F6ECDEA47B5C7B55AEA02C882E2D253049F
- Shaders/CopyrightAdaptive/CopyrightTex_Custom.fxh: 34D4F129BAAA32DFC8D57FAC394D5E075A2C0B67E5BC34B382216CB02907EBE6

## CopyrightAdaptive 安装与相对路径 — 2026-10-05

新增安装入口，迁移与样式同步工具统一使用 PowerShell 当前目录解析相对输入、输出路径。样式同步支持多个纹理根，安装时仅对末尾 `/**` 的搜索范围递归。

- 使用 Windows PowerShell 5.1.26100.9444 与 PowerShell 7.6.5，各完成 11 项实际文件操作检查，全部通过。
- 输入采用参考安装的三组原头文件、86 个实际 PNG、标准 include 及原预设样本，复制到隔离目录后执行。
- 检查覆盖：切换 PowerShell 当前目录后的相对迁移、相对同步输出、多个纹理根与 INI 逗号转义、`-WhatIf`、安装并另存迁移、更新备份、已有输出拒绝、非递归目录、缺失来源组、`BasePath` 与带方括号目录、复制失败回滚、多份效果拒绝。其中安装并迁移作为一项检查。
- 预览、拒绝及复制失败用例核对安装目录文件快照；成功安装核对原 INI、原预设和原始头文件哈希。备份使用 `.bak` 扩展名，递归搜索中仍只有一份可加载的主 FX。
- 默认单纹理目录同步结果仍为 45 / 63 / 12 项；生成的三个私有头文件与前述已编译 v2 输入逐字节相同，本次未修改绘图代码。
- 维护者的 AuroraShade / ReShade-CN2 安装执行 `-WhatIf -PassThru` 预览，正确找到现有着色器、PNG 和唯一新版模块，输出 `Applied=False`。未执行该目录的安装写入或游戏内操作。
- 安装脚本的验证输入为 UTF-8 BOM，SHA-256：`889694D7A74A1031AA1F464569960F37EBC7EF4AB593D377D405F5DADDA25C3A`。

## CopyrightAdaptive 复制安装包 — 2026-10-06

- 安装包从已发布提交 `5fcae724ce064f703827d9c6b2b1cc66df1e871b` 读取主 FX、三个私有头文件和许可，共五个文件；包内去掉 `Shaders/` 前缀，顶层直接为 `CopyrightAdaptive.fx` 与 `CopyrightAdaptive/`。
- Windows PowerShell 5.1 与 PowerShell 7 分别运行打包工具，两份输出均包含且仅包含这五个文件；逐文件 SHA-256 与指定提交的 Git archive 内容一致。主 FX 的三个相对 include 在包中均能找到对应项。
- 已有输出拒绝覆盖且哈希未改变；无效提交拒绝打包且不生成输出。未提交的本地主 FX 未进入安装包。
- 只读检查参考 AuroraShade / ReShade-CN2 1207 安装：三个私有头文件各自去重后的字面 PNG 引用为 29 / 57 / 1 项，均在现有纹理目录找到。该安装已提供 `ReShade.fxh`、`Blending.fxh`，且着色器及纹理搜索根均配置递归查找，因此复制到原 `Copyright.fx` 所在目录无需另填搜索路径。
- 交付 ZIP SHA-256：`FFABF69D0675BDD0F15CC63E9DC7BB32DA3E6AF0A70E5607F3656CACE0C23F9A`。ZIP 容器的压缩字节可能随 PowerShell/.NET 版本不同；重新打包时以包内五个文件与源提交的一致性为准。
- 本次只调整交付与说明，着色器实现沿用已有版本。上述检查覆盖打包内容、相对头文件和参考素材；本次未执行游戏内安装或画面检查。
