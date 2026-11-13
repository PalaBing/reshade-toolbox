# ReShade Toolbox

VintageVelvet 维护的摄影与游戏工具箱，汇集 ReShade 着色器、滤镜预设、DR 本地模块，以及可复用的制作资产。各子项目集中维护自己的使用说明、源码、脚本和参考资料。

## 内容入口

| 领域 | 当前内容 | 入口 |
|---|---|---|
| 着色器 | 全画面构图线、专辑与魔法取景框、自适应版权水印 | [Shaders](Shaders/README.md) |
| 滤镜预设 | 预设组织与提交说明，尚无独立预设文件 | [Presets](Presets/README.md) |
| DR 模块 | DLSS 模型与挡位管理、配置保存和手动刷新 | [Modules](Modules/README.md) |
| 制作资产 | P01—P04 照片模板及制作资源入口 | [assets](assets/README.md) |
| 外部参考 | 来源、依赖文档与参考资料索引 | [references](references/README.md) |

## 使用工具

- [LandscapeComposition](Shaders/LandscapeComposition/README.md)：为整个画面添加构图辅助线。
- [FrameTools](Shaders/FrameTools/README.md)：使用 AlbumFrame 制作专辑与拍立得布局，或使用 MagicFrame 制作人物穿框与纯色背景；[裁剪说明](Shaders/FrameTools/docs/Cropping.md)提供成片尺寸和坐标。
- [CopyrightAdaptive](Shaders/CopyrightAdaptive/README.md)：安装随分辨率缩放的版权水印，使用现有 PNG 素材。
- [DR-DLSS](Modules/DR-DLSS/README.md)：通过 DR 的本地模块管理加载，安装条件与兼容版本见模块说明。

着色器安装与效果排序见 [Shaders 使用入口](Shaders/README.md)。各功能的专用脚本随项目存放；从仓库根目录运行的命令见对应使用或技术文档。

## 照片模板试验档案

[PhotoTemplateStudies](assets/templates/PhotoTemplateStudies/index.html) 保存四个截图后使用的本地浏览器原型：人物大字横幅、万圣节装饰、绯红邀请函和法阵前景合成。包含默认版式预览、可编辑 HTML、方向稿和素材来源记录，用作设计基准。用浏览器打开即可，不需要安装为 ReShade 效果。使用方法见[档案说明](assets/templates/PhotoTemplateStudies/README.md)。

## 参与维护

新内容按所属领域建立项目，专用文档、脚本、素材和外部引用放在项目内部。目录职责、子项目结构与旧路径迁移表见[仓库结构](docs/STRUCTURE.md)。顶层 `scripts/` 在出现仓库级脚本时建立。

[公共文档](docs/README.md)汇总[版本记录](docs/CHANGELOG.md)、[验证索引](docs/VALIDATION.md)和[第三方来源与许可](docs/THIRD_PARTY_NOTICES.md)。取景工具的[模板继承与自动裁图需求](Shaders/FrameTools/docs/FrameToolsRequirements.md)仍在规划中。

## 许可与署名

本仓库新增代码和文档采用 [MIT License](LICENSE)，Copyright (c) 2026 VintageVelvet。第三方部分继续适用原有许可，具体范围与署名见[第三方说明](docs/THIRD_PARTY_NOTICES.md)及各项目来源记录。外部 PNG、标准 ReShade include、编译工具和宿主依赖按项目说明提供。
