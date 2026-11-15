# FrameTools 验证记录

历史记录保留当时的日期、输入哈希与路径；2026-11-13 按项目从仓库公共验证文档迁入。旧路径对应当时的源码布局，当前操作入口见项目说明。后续修改另增日期条目。

使用入口：[项目说明](../README.md)。

## AlbumFrame 0.1 — 2026-09-28

- 使用[LandscapeComposition 工具记录](../../LandscapeComposition/docs/VALIDATION.md#工具与来源)中的同一 ReShade Testing Initiative 普通 reshadefx_cli 编译器及相同 ReShade.fxh。
- DXBC Shader Model 5.0，显式指定 __RENDERER__=0xb000，保留运行时 uniform。
- 2560 × 1440 与 3840 × 2160 均分别编译 F__PostProcessVS 和 F__AlbumFrame__DrawFrame；四次退出码均为 0，无错误或警告。
- 每个分辨率生成顶点字节码 18,656 字节、像素字节码 40,828 字节。
- 编译输入 SHA-256：249961333900E52223D395238E3B3982940DF26792BD55E43A637B5460571380。
- 发布文件只在该输入前附上仓库完整 MIT 许可注释，已逐字核对余下源码一致。
- 几何检查覆盖横屏、竖屏、超宽屏、奇数尺寸和极端自定义比例，2,205 个嵌套矩形组合均未越界。
- 未进行游戏内运行、画面或截图验证；不包含自动裁切功能。

复现时使用 [LandscapeComposition 的 DXBC 命令](../../LandscapeComposition/docs/VALIDATION.md#复现命令)，将输入替换为 AlbumFrame.fx、像素入口替换为 F__AlbumFrame__DrawFrame，分辨率使用 2560 × 1440 和 3840 × 2160。

发布文件 SHA-256：1C6BE0BEF8E3718CDA91A9A3320FD44A375027ED145AC08AD8FF3828C109BC4B

## AlbumFrame 构图辅助 — 2026-09-29

- 使用[工具记录](../../LandscapeComposition/docs/VALIDATION.md#工具与来源)中的普通 reshadefx_cli，DXBC SM5，2560 × 1440 与 3840 × 2160 各编译 F__PostProcessVS、F__AlbumFrame__DrawFrame、F__AlbumFrame__DrawGuides，六次均成功且无诊断。
- 各分辨率生成 VS 18,656 字节、遮罩 PS 40,760 字节、构图 PS 287,512 字节。
- 12 种绘图 helper 剔除新增 localPixelSize 传参差异后，与 LandscapeComposition 一致；移动/缩小窗口的局部坐标、三分和中心位置、像素线宽及区域裁切检查通过。
- 编译输入 SHA-256：177748ECD19F8B205CE941ECA57C3839EF70845788B25C10C5370B5FEECA7111。发布前只清理行尾空白；最终源码 SHA-256：789FA5FD5F53CC39B18B10C70A4EDC7119DDC791A73DF8CE0152E6DD683C4000。
- 遮罩与构图共享矩形计算，截图排除标记仅应用于 AlbumFrame_Guides。未在游戏内验证效果顺序、运行时画面或截图排除行为。

## AlbumFrame 自定义布局试用版 — 2026-09-29

- DXBC SM5：2560 × 1440、3840 × 2160 各编译 VS、DrawFrame、DrawGuides，六个入口均成功，未收到错误或警告。
- 源码 SHA-256：A2B2DE4FBA2D6B310CB741BDB0C540E3F7DEF08513A2B74FF02EEBA7C61410DB。
- 固定比例索引 0–5 与原计算保持一致；新自定义索引 6 和旧自由宽高索引 7 均走百分比宽高计算，后续裁切与构图共用矩形保持不变。
- 已核对标准 ReShade 面板的 hidden、ui_text 等为静态注解，因此采用带生效范围的标签，没有动态隐藏、实时比例读数或切换模式自动赋值。
- 未进行游戏内面板或运行时视觉验证。

## 经典拍立得布局 — 2026-09-29

- DXBC SM5：2560 × 1440、3840 × 2160、1080 × 1920 各编译 VS、DrawFrame、DrawGuides，共九次成功，无错误或警告。
- 编译输入 SHA-256：697634938BAB2948E66789E58F8A5920629758D2C809D3497EE440D128207BB1；编译后仅修正构图区域 tooltip 的普通画板限定语。
- 源码审查确认画板最大内接、成像区方形、底部宽边、内部留边开关覆盖与构图共用矩形。像素取整会带来约一像素边距差异。
- 未进行游戏内视觉或截图验证。

## 画板与留边解耦 — 2026-09-29

- DXBC SM5：2560 × 1440、3840 × 2160、1080 × 1920 各编译 VS、DrawFrame、DrawGuides，九次成功，无错误或警告。
- 源码 SHA-256：393871AEBB90637FCD1C72B3A69C06BC22AF646875FF12B01284D25B6E1D32F8。
- 独立源码审查确认 88:107 仅控制比例、留边开关总控所有布局、内外颜色分支独立、拍立得窗口的方形与位置边界约束。
- 尚未在游戏内验证画面、控件体验及截图。

## MagicFrame 0.1 — 2026-10-05

- 使用[工具记录](../../LandscapeComposition/docs/VALIDATION.md#工具与来源)中的同一普通 `reshadefx_cli.exe`（ReShade 6.8.0 Testing Initiative build）和 `ReShade.fxh`；2026-10-05 核对的两者 SHA-256 与工具记录一致。
- 将最终源码原样复制到 ASCII 临时目录，输入与仓库文件哈希相同。DXBC Shader Model 5.0，显式 `__RENDERER__=0xb000`，未启用 `--spec-constants`，保留运行时 uniform。
- 2560 × 1440、3840 × 2160、1080 × 1920 各编译 `F__PostProcessVS`、`F__MagicFrame__DrawFrame`、`F__MagicFrame__DrawGuides`，共九个入口全部成功，无错误或警告，均生成非空字节码。
- 源码 SHA-256：`02021F13C08A157FA773476853E383CA4D1CB80A32D0D561C54DB2C02EC84A2B`。
- 独立源码审查确认：外画板硬边界优先于前景恢复与两种预览；颜色、线性深度均采样原 UV；深度过渡为 0 时直接比较；原场景与纯色背景按各自区域合成；关闭越框只限制留边，纯色窗口内仍保留近景；选区预览显示候选前景。
- 辅助线审查发现整数边界上 1 像素线被绘为 2 像素，已将中心线与三等分线改为半开像素区间；修正后确认只选中一行 / 列，并对最终源码重新完成上述编译。轮廓向矩形内部绘制，宽度为 0 时所有辅助线关闭。
- 默认 9:16 裁切坐标与矩形公式核对：3840 × 2160 对应 `(1312, 0, 1215, 2160)`；2560 × 1440 对应 `(875, 0, 810, 1440)`。
- 主效果的选区 / 深度预览会进入截图；只有独立 `MagicFrame_Guides` 设置 `enabled_in_screenshot = false`。
- 该记录覆盖 FX / DXBC 编译与源码检查。游戏内深度是否完整包含人物、头发与透明材质边缘、实际效果顺序和截图排除行为，仍需在具体游戏环境中检查。

## AlbumFrame 画板外默认色 — 2026-10-05

- `OutsideColor` 默认值由 `(0, 0, 0)` 改为 `(0.18, 0.18, 0.18)`，与 MagicFrame 一致；同时更新该控件提示。矩形计算和绘制代码未修改，已有预设中的颜色不自动覆盖。
- 使用同一普通 ReShade FX 编译器与标准 include，DXBC SM5、`__RENDERER__=0xb000`、保留运行时 uniform；2560 × 1440 的 `F__PostProcessVS`、`F__AlbumFrame__DrawFrame`、`F__AlbumFrame__DrawGuides` 三个入口均退出码 0，无错误或警告，均生成非空字节码。
- 最终源码与原样复制输入 SHA-256：`F1C76F6B610BA08E234732B0B1FB3CE09CBB73AC3F77B4BA4145ADE2DF4EDBFE`。
