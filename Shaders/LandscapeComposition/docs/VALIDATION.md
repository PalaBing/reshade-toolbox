# LandscapeComposition 验证记录

历史记录保留当时的日期、输入哈希与路径；2026-11-13 按项目从仓库公共验证文档迁入。旧路径对应当时的源码布局，当前操作入口见项目说明。后续修改另增日期条目。

使用入口：[项目说明](../README.md)。

## LandscapeComposition 0.1 — 2026-09-27

2026-09-28 许可补齐：仅在 `.fx` 的 `#include` 前添加许可注释，已与修改前提交比对，`#include` 起的全部实现代码未改变；未重复运行编译。该次许可补齐后的文件 SHA-256 为 `058C031FE719B40A589077BB367DA3D074979B1C1A98725663A32B9443D55395`。下文原始编译输入哈希保留，用于对应当时的验证记录。

### 工具与来源

使用 [ReShade Testing Initiative v6.8.0.5](https://github.com/CeeJayDK/ReShade-Testing-Initiative/releases/tag/v6.8.0.5) 的 Windows x64 `reshadefx_cli.exe`。版本输出为 `ReShade 6.8.0 (ReShade Testing Initiative build)`。

该构建使用官方 ReShade FX 编译器源码，二进制由社区项目发布。该项目将普通 `reshadefx_cli` 标为未修改的官方 `tools/fxc.cpp`；记录中的编译结果使用该普通版本，带补丁的 `reshadefx_cli_fixed` 仅用于列出入口名称。DXBC 模式通过 Windows 的 Microsoft D3DCompiler 编译实际顶点、像素着色器。

- 官方源码：[crosire/reshade](https://github.com/crosire/reshade)
- 社区构建说明：[ReShade Testing Initiative README](https://github.com/CeeJayDK/ReShade-Testing-Initiative#readme)
- 编译器 SHA-256：`986FFD4ABE4BBDDAA504D2A68044F0B676AEFA958B8BCE8B90B468EB02DC408A`
- 被验证的 `LandscapeComposition.fx` SHA-256：`E8E69C4C6777A8DA456BC60EFA76E2A39E7C2C1BF15325C863BAF9B9BB03C819`
- 验证环境中的 `ReShade.fxh` 来自 AuroraShade 发行包，SHA-256：`2FFFB6009B9593BC43473861E5B189A6FAEE1BD7465913FA8799B85D77DF066F`

此记录未提供 AuroraShade 发行包的公开下载链接。复现相同输入需要准备上述哈希对应的 `ReShade.fxh`；使用其他版本时，应另行记录依赖哈希和编译结果。

2026-09-27 验证时，该编译器在包含中文的 include 路径下异常退出。以下复现命令使用 ASCII 路径，并要求复制输入时保持文件字节不变。

### 已完成检查

| 检查 | 分辨率 | 结果 |
| --- | --- | --- |
| DXBC Shader Model 5.0，顶点及像素入口分别编译 | 1920 × 1080，16:9 | 两入口均退出码 0，生成非空字节码 |
| DXBC Shader Model 5.0，顶点及像素入口分别编译 | 2560 × 1080，常见 21:9 显示器规格 | 两入口均退出码 0，生成非空字节码 |
| DXBC Shader Model 5.0，顶点及像素入口分别编译 | 5120 × 1440，32:9 | 两入口均退出码 0，生成非空字节码 |
| OpenGL GLSL 源码生成 | 1920 × 1080 | 退出码 0，生成非空 GLSL |
| Vulkan SPIR-V，顶点及像素入口分别生成 | 1920 × 1080 | 两入口均退出码 0，生成非空 SPIR-V |

编译时保留运行时 uniform，所有构图分支均参与编译；没有仅使用默认选项进行常量折叠。没有收到编译错误或警告。

### 复现命令

下面的路径是复现示例：将编译器、源文件和 `ReShade.fxh` 分别放入对应目录，再从这些目录的上级目录执行。`-I` 指向含 `ReShade.fxh` 的目录；编译器及验证用复制文件不纳入仓库。

```powershell
$compiler = '.\work\reshade-validation\reshadefx_cli.exe'
$inputFile = '.\work\compiler-input\LandscapeComposition.fx'
$includeDir = '.\work\compiler-input'

# 对 1920 × 1080、2560 × 1080、5120 × 1440 分别执行：
foreach ($size in @(@(1920,1080), @(2560,1080), @(5120,1440))) {
    foreach ($entry in @('F__PostProcessVS', 'F__LandscapeComposition__PS_Composition')) {
        & $compiler --dxbc --shader-model 50 --width $size[0] --height $size[1] `
            -D __RENDERER__=0xb000 -I $includeDir -E $entry `
            -Fo ".\work\composition-$($size[0])x$($size[1])-$entry.dxbc" $inputFile
        if ($LASTEXITCODE -ne 0) { throw "DXBC compilation failed: $entry" }
    }
}

& $compiler --glsl --width 1920 --height 1080 -D __RENDERER__=0x10000 `
    -I $includeDir -Fo '.\work\composition.glsl' $inputFile

foreach ($entry in @('E__PostProcessVS', 'E__LandscapeComposition__PS_Composition')) {
    & $compiler --spirv --vulkan-semantics --width 1920 --height 1080 `
        -D __RENDERER__=0x20000 -I $includeDir -E $entry `
        -Fo ".\work\composition-$entry.spv" $inputFile
}
```

为独立编译器显式提供 `__RENDERER__`，使 include 中的后端条件与被测试后端一致。DXBC 与 SPIR-V 使用具体入口，避免独立工具在未指定入口时不生成实际字节码。

### 验证范围

上述结果覆盖所列输入哈希对应的 ReShade FX 前端与 DX11 SM5 编译。GLSL 检查仅覆盖代码生成，生成结果未交给实际 OpenGL 驱动编译。该记录未包含游戏内加载、运行时图像或截图检查；线条观感、效果排序和截图流程仍需在实际游戏环境中检查。超宽分辨率的检查范围也是编译，独立安全框、裁切与宽屏专属构图设计未在此条目中验证。

`enabled_in_screenshot = false` 是 [ReShade 5.2 官方加入的技术注解](https://www.reshade.me/releases/8046-5-2)，并非 AuroraShade 私有扩展。它控制 ReShade 自身截图流程，不能使游戏截图、系统截图或录屏自动隐藏构图线。
