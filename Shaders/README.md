# ReShade 着色器

各功能项目集中维护着色器源码、使用说明、专用脚本和来源记录。

| 项目 | 源码 | 用途 |
|---|---|---|
| [LandscapeComposition](LandscapeComposition/README.md) | [LandscapeComposition.fx](LandscapeComposition/LandscapeComposition.fx) | 全画面构图辅助线 |
| [FrameTools](FrameTools/README.md) | [AlbumFrame.fx](FrameTools/AlbumFrame.fx)、[MagicFrame.fx](FrameTools/MagicFrame.fx) | 专辑与拍立得布局、深度穿框和纯色人物背景 |
| [CopyrightAdaptive](CopyrightAdaptive/README.md) | [CopyrightAdaptive.fx](CopyrightAdaptive/CopyrightAdaptive.fx) | 随分辨率缩放的版权标志 |

## 安装与效果顺序

1. 从项目目录取出所需 `.fx`，复制到 ReShade 的着色器搜索目录；CopyrightAdaptive 需要同时复制相邻的 `CopyrightAdaptive/` 样式目录，详见其[安装说明](CopyrightAdaptive/README.md)。
2. 确保搜索路径中有标准 `ReShade.fxh` 以及项目要求的其他依赖，再在游戏内重新加载效果。
3. 按项目说明启用效果并调整参数，构图辅助和边框通常放在效果顺序末尾。

LandscapeComposition 适合全屏构图。封面画板或内部照片窗口的构图使用 AlbumFrame 自带的 `AlbumFrame_Guides`，排在 AlbumFrame 遮罩之后。

MagicFrame 默认 9:16 竖版画板，让近景人物越过内部窗口覆盖留边。排序为调色、景深 → MagicFrame → MagicFrame_Guides；使用时关闭 AlbumFrame 和旧 MagicBorder 的遮罩，避免先盖掉人物颜色。拍后按[裁剪说明](FrameTools/docs/Cropping.md)保留整块画板。

CopyrightAdaptive 通常在调色完成后放到效果顺序末尾。它从相邻目录读取样式表，并复用现有纹理搜索目录中的 PNG。

## 维护入口

- 验证记录：[构图线](LandscapeComposition/docs/VALIDATION.md)、[取景工具](FrameTools/docs/VALIDATION.md)、[版权水印](CopyrightAdaptive/docs/VALIDATION.md)。
- 来源与许可：[构图线](LandscapeComposition/references/SOURCES.md)、[取景工具](FrameTools/references/SOURCES.md)、[版权水印](CopyrightAdaptive/references/SOURCES.md)。
- 可分享的滤镜组合归入 [Presets](../Presets/README.md)，专属于某个着色器的脚本与资源留在其项目内。
