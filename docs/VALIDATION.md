# 验证索引

各项目的验证记录按日期保留工具、输入哈希、检查结果与覆盖范围。历史验证对应当时的输入文件。

| 项目 | 验证记录 |
|---|---|
| LandscapeComposition | [编译、源码与截图注解记录](../Shaders/LandscapeComposition/docs/VALIDATION.md) |
| FrameTools | [AlbumFrame 与 MagicFrame 验证记录](../Shaders/FrameTools/docs/VALIDATION.md) |
| CopyrightAdaptive | [编译、样式、脚本与安装包验证记录](../Shaders/CopyrightAdaptive/docs/VALIDATION.md) |
| DR-DLSS | [模块构建、隔离测试与游戏反馈](../Modules/DR-DLSS/docs/VALIDATION.md) |

2026-11-13 的目录迁移保留各条记录原有的日期、版本和哈希；旧路径的对应关系见[仓库结构](STRUCTURE.md#2026-11-13-路径迁移)。

## 仓库目录整理 — 2026-11-13

- 34 份公开 Markdown 中的 160 个本地链接和 11 个标题锚点检查通过，当前使用命令已指向项目内的新位置。
- 八份 PowerShell 脚本语法解析通过；四个主 FX 的 Git 内容标识与迁移前一致，DR-DLSS 的七个 C# 文件未改动。
- 公共着色器验证文档原有的 11 个记录章节、24 个不同 SHA-256 和来源文档中的许可正文均已保留，按项目归档并补齐上下文链接。
- 根目录及 24 个内容子目录均有本地 `AGENTS.md`，25 份文件全部命中现有 Git 本地排除规则，未加入 Git 跟踪。
- DR-DLSS 的 148 项隔离检查通过；CopyrightAdaptive 在 PowerShell 7.6.9 与 Windows PowerShell 5.1.26100.9818 下各通过 56 项隔离断言，详见各项目记录。
- 本次验证范围为仓库布局、脚本与文档迁移，未进行游戏内操作。

## 照片模板归位 — 2026-11-13

- PhotoTemplateStudies 从 Modules 整体移至 assets/templates，保留档案内部目录结构，首页、资产导航和路径迁移表同步更新。
- 原有 23 个文件迁移前后字节数与 SHA-256 一致，MANIFEST.json 中的 22 项基准文件全部匹配；四个 catalog 模板目录均有效。历史清单与归档日期保持原样。
- 41 份公开 Markdown 中的 179 个本地链接与 11 个标题锚点检查通过；档案内五个 HTML 的 20 处本地文件引用有效，没有仍指向旧档案目录的导航链接。
- 档案根目录与八个子目录新增九份本地 AGENTS.md。仓库内容目录共 34 份维护约定全部命中 Git 本地忽略规则，Git 跟踪数量为零；构建产物目录按现有规则排除。
- 本次检查覆盖文件完整性、目录归属和引用关系，未重新执行浏览器中的导入、方案恢复与导出流程。

## 整理提交检查 — 2026-11-13

- PhotoTemplateStudies 的文件使用局部 Git 属性 `-text` 保留原始字节，避免平台换行转换改变归档哈希。
- 在 `core.autocrlf=true` 的环境中从暂存区检出到独立临时目录，MANIFEST.json 的 22 项字节数和 SHA-256 全部匹配。
- 归档原文件 `previews/p01.svg:49` 与 `templates/p04-raven/index.html:35` 各有一处行尾空白，保留历史基准字节。其余暂存内容的空白检查通过。
- 暂存范围核对未包含 AGENTS.md、本地参考目录、构建产物或临时检查文件。
