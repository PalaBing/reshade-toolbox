# 照片制作案例

P01—P04 是四次 FF14 角色照片制作留下的独立案例。保留可编辑实现、视觉预览与素材记录，供后续新需求参考处理方法；各案例可以单独打开和使用。

## 案例与技术入口

用 Chrome 或 Edge 打开对应的 `index.html`，无需安装或运行服务器。

| 案例 | 可参考的处理方式 | 默认输出 | 入口 |
|---|---|---|---|
| P01 · JOURNEY | 人物与大标题前后层次；图片导入、位置调整、方案保存恢复和 PNG 导出 | 3840 × 2160 | [编辑器](templates/p01-journey/index.html) · [说明](templates/p01-journey/README.md) · [预览](previews/p01.svg) |
| P02 · 橙夜魔女 | 节日装饰布局、整体镜像，同时保持文字正常方向 | 2880 × 3840 | [编辑器](templates/p02-halloween/index.html) · [说明](templates/p02-halloween/README.md) · [预览](previews/p02.svg) |
| P03 · 绯红夜宴 | 将方向稿拆成可编辑文字与矢量装饰；人物越过拱窗 | 2880 × 3840 | [编辑器](templates/p03-crimson/index.html) · [说明](templates/p03-crimson/README.md) · [预览](previews/p03.svg) |
| P04 · 鸦羽之夜 | 生成位图、人物、矢量眼睛与独立羽毛前景的分层控制 | 3840 × 2160 | [编辑器](templates/p04-raven/index.html) · [说明](templates/p04-raven/README.md) · [预览](previews/p04.svg) |

各案例支持 PNG 导入、文字编辑、方案保存与 PNG 导出，独有功能见对应说明。方案 JSON 含完整人物图片，需自行保存；当前没有自动保存。模板仅在浏览器中处理选中的文件，没有上传接口。

## 参考与素材

- [previews/](previews/) 保存实际默认版式；P01/P02 使用几何占位人物，P03/P04 默认留空。
- [P03 方向稿](concepts/p03-direction.png)、[P04 方向稿](concepts/p04-direction.png) 保存设计构思，与可编辑实现分别查阅。
- [P04 素材](templates/p04-raven/assets/) 保存法阵背景、透明羽毛及[生成记录](templates/p04-raven/assets/generation-notes.md)；HTML 内嵌同样素材，便于独立打开。
- [NOTES.md](NOTES.md) 保存设计来源、历史检查范围，以及原始归档清单的查阅方式。

4K 指输出画布尺寸；P04 法阵原图为 1672 × 941，放大不会补出原图没有的细节。不同电脑字体可能略有差异。

代码与文档适用仓库根目录许可，生成位图的来源单独记录。目录内不包含个人角色 PNG、角色方案、测试成片或第三方参考原图；游戏素材及参考作品的权利不归入代码许可。
