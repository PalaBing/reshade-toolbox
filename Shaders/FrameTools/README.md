# 取景工具

本目录包含两款独立的 ReShade 取景着色器，用于设置成片画板、内部留边和构图辅助。两款效果均保留场景原位置的颜色采样，人物大小和位置通过游戏镜头调整。

| 工具 | 用途 | 使用说明 |
|---|---|---|
| [AlbumFrame.fx](AlbumFrame.fx) | 专辑封面、普通留边与拍立得布局，提供 12 种构图辅助 | [专辑取景框](docs/AlbumFrame.md) |
| [MagicFrame.fx](MagicFrame.fx) | 利用场景深度保留近景，让人物越过内部窗口覆盖留边，支持纯色背景 | [魔法取景框](docs/MagicFrame.md) |

## 安装与拍摄

将需要使用的 `.fx` 文件复制到 ReShade 的着色器搜索目录，确保标准 `ReShade.fxh` 可被找到，再在游戏内重新加载效果。两款文件可同时安装；拍摄时选择其中一款主效果，避免遮罩叠加。MagicFrame 的穿框和纯色背景需要可用的场景深度。

效果顺序为调色、景深等画面效果 → 选定的取景框 → 同文件中的构图辅助。构图辅助分别名为 `AlbumFrame_Guides` 和 `MagicFrame_Guides`；支持该功能的 ReShade 5.2+ 自身截图会隐藏辅助线，游戏截图、系统截图和录屏仍会包含当前可见的辅助线。

截图保存完整游戏分辨率，成片需要沿外层画板手动裁剪，保留内部留边和穿框人物。两款工具共用的裁剪步骤、坐标表和计算规则见[取景器裁剪简易说明](docs/Cropping.md)。

## 文档与维护

- `docs/` 保存使用指南、共用裁剪规则、[扩展需求](docs/FrameToolsRequirements.md)和[验证记录](docs/VALIDATION.md)。自定义继承固定模板与按画板自动另存裁图仍属于后续需求。
- `references/` 保存[外部来源与许可说明](references/SOURCES.md)。AlbumFrame 的构图几何来自 LandscapeComposition，相关上游声明保留在源码中。
- 仓库级版本记录见 [CHANGELOG.md](../../docs/CHANGELOG.md)；可分享预设的存放约定见 [Presets/README.md](../../Presets/README.md)。
