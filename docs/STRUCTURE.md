# 仓库结构

ReShade Toolbox 使用一个 Git 仓库汇总多个内容项目。每个项目集中维护自己的源码或主产物、使用入口、专用脚本和参考资料。

## 内容归属

| 目录 | 职责 |
|---|---|
| `Shaders/` | ReShade 着色器开发，按功能分为 LandscapeComposition、FrameTools、CopyrightAdaptive |
| `Presets/` | 可分享的滤镜预设，按风格或用途建立系列 |
| `Modules/` | DR 本地模块，每个模块独立维护依赖、构建和验证 |
| `assets/templates/` | 可继续编辑的共用或独立制作模板，当前收录 PhotoTemplateStudies |
| `assets/resources/` | 图片、纹理、LUT 等共用或独立素材 |
| `references/` | 跨项目外部资料索引与引用 |
| `docs/` | 仓库结构、变更、验证索引和公共维护说明 |

项目专用内容放在所属项目内。顶层 `scripts/` 留给仓库级或跨项目脚本，目前没有此类脚本，因此尚未建立。

## 子项目结构

每个项目提供 `README.md`，说明用途、使用条件和入口。源码或主产物可直接放在小项目根目录；其他目录按内容需要增加：

- `docs/`：使用细节、技术设计、需求和实际验证记录。
- `references/`：外部文档、来源版本、引用与许可记录。
- `scripts/`：本项目的构建、安装、转换和检查脚本。
- `assets/`：项目专用模板、素材和展示内容。
- `tests/`：可重复运行的检查及其必要输入。

外部资料记录原始地址、版本或提交和引用用途。第三方许可继续随需要分发的源码或资产保留。生成文件说明生成入口；缓存、构建产物和本机诊断资料通过相应忽略规则管理。

各内容目录的本地 `AGENTS.md` 补充该目录的工作约定，由仓库的 Git 本地排除配置忽略。公开使用方式和维护入口以已提交的 README 与文档为准。

## 2026-11-13 路径迁移

| 原位置 | 当前位置 |
|---|---|
| `Shaders/LandscapeComposition.fx` | [LandscapeComposition/LandscapeComposition.fx](../Shaders/LandscapeComposition/LandscapeComposition.fx) |
| `Shaders/AlbumFrame.fx`、`Shaders/MagicFrame.fx` | [FrameTools](../Shaders/FrameTools/README.md) |
| `Shaders/CopyrightAdaptive.fx` 与 `Shaders/CopyrightAdaptive/*.fxh` | [CopyrightAdaptive](../Shaders/CopyrightAdaptive/README.md)，主 FX 与其 `CopyrightAdaptive/` 样式目录相邻 |
| `tools/*CopyrightAdaptive*.ps1` | [CopyrightAdaptive/scripts](../Shaders/CopyrightAdaptive/scripts/) |
| `docs/LandscapeComposition.md` | [LandscapeComposition/README.md](../Shaders/LandscapeComposition/README.md) |
| `docs/AlbumFrame.md`、`MagicFrame.md`、`Cropping.md`、`FrameToolsRequirements.md` | [FrameTools/docs](../Shaders/FrameTools/docs/) |
| `docs/CopyrightAdaptive.md`、`CopyrightAdaptive-Technical.md` | [水印使用说明](../Shaders/CopyrightAdaptive/README.md)、[技术说明](../Shaders/CopyrightAdaptive/docs/TECHNICAL.md) |
| `docs/VALIDATION.md` 中各着色器记录 | [各项目验证记录](VALIDATION.md) |
| `docs/THIRD_PARTY_NOTICES.md` 中各项目来源正文 | [各项目来源记录](THIRD_PARTY_NOTICES.md) |
| `Modules/DR-DLSS/Build.ps1` | [DR-DLSS/scripts/Build.ps1](../Modules/DR-DLSS/scripts/Build.ps1) |
| DR-DLSS 根目录的使用、技术与验证文档 | [DR-DLSS/docs](../Modules/DR-DLSS/docs/) |
| DR-DLSS 根目录的上游与来源文档 | [DR-DLSS/references](../Modules/DR-DLSS/references/) |
| `Modules/PhotoTemplateStudies/` | [assets/templates/PhotoTemplateStudies](../assets/templates/PhotoTemplateStudies/README.md) |

历史验证记录保留当时的输入哈希和路径。当前操作请使用项目入口中的命令；CopyrightAdaptive 的独立安装包继续采用顶层主 FX 与相邻样式目录的复制安装结构。
