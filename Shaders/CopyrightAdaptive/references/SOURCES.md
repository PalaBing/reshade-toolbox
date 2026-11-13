# CopyrightAdaptive 来源与许可

使用入口见[项目说明](../README.md)，脚本与样式生成方式见[技术说明](../docs/TECHNICAL.md)。

## CopyrightAdaptive.fx 与样式头文件

实际来源为 AuroraShade / ReShade-CN2 安装提供的 `reshade-shaders/Shaders/Copyright.fx` 及 `CopyrightTex_XIV_AUR.fxh`、`CopyrightTex_XIV.fxh`、`CopyrightTex_Custom.fxh`。参考 `Copyright.fx` 的 SHA-256 为 `4F6BE3A65E68EB3F2CC6D635D01FBAF3DF83F00D1AEC1B0AD92AD57834E70765`。

原主文件标注 `Copyright (based on Layer)`、`License: MIT`，署名 CeeJay.dk、seri14、Marot Satil、uchu suzume、prod80、originalnicodr；中文翻译与 ReShade 适配署名为 BarricadeMKXX。原历史、作者及翻译说明保留在 `CopyrightAdaptive.fx` 中。私有样式头文件继续保留原作者说明，其中 AuroraShade 水印列表记录路障 MKXX、白玉为何物的素材署名。

三个参考索引文件的文件头提供作者说明，没有单独的许可条目；本仓库保留这些来源与署名。

VintageVelvet 新增部分为独立效果与预处理器命名、按画面高度缩放、自定义尺寸轴处理、缺图筛选、紧凑菜单与原款式 ID 映射，以及预设迁移和同步工具。这些新增代码及文档采用仓库根目录的 MIT 许可；原代码继续保留来源署名与 MIT 条款，不重新归为维护者原创。模块完整许可随附于 [CopyrightAdaptive/LICENSE.txt](../CopyrightAdaptive/LICENSE.txt)。

仓库分发着色器代码、样式元数据和工具。PNG 图片、字体、`ReShade.fxh`、`Blending.fxh` 由现有安装提供，未纳入仓库；代码中的 MIT 标注不扩大这些素材的许可范围。来源记录以实际参考文件及哈希为准。
