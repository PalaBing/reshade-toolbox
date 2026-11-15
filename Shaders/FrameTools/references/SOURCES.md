# FrameTools 来源与实现关系

## AlbumFrame.fx 构图辅助

2026-09-29 从本仓库 LandscapeComposition.fx 复用构图模式与绘制辅助函数，来源与署名见 [LandscapeComposition 来源记录](../../LandscapeComposition/references/SOURCES.md)。将全屏坐标改为画板或内部窗口局部坐标，并以所选区域尺寸换算像素线宽。保留原几何和斜线加宽规则。文件包含原有 MIT、BSD-3-Clause 署名与完整条款；新增遮罩与区域适配部分采用仓库 MIT。

## MagicFrame 与共用取景概念

MagicFrame 的源码包含仓库 MIT 许可。两款工具共用画板与内部窗口的使用概念，分别实现矩形计算；共用的裁剪方法见[裁剪说明](../docs/Cropping.md)。修改相关计算时应同时检查裁剪坐标说明。

两款着色器的标准 `ReShade.fxh` 由使用环境提供。源码中的许可与作者声明随文件保留，仓库新增内容适用[根目录 MIT 许可](../../../LICENSE)。
