# AMD逐核限制频率

运行 `Juxic Per-Core Limit.exe`。适用于 Windows 10/11 x64，需要管理员权限和 [PawnIO 2.2.0](https://github.com/namazso/PawnIO.Setup/releases/tag/2.2.0)。程序按已知 Zen 4 / Zen 5 CPUID 型号进行检测，并在每个逻辑 CPU 上验证 CPPC。发布文件为单个 EXE，首次运行时自动部署 C++20 Windows 服务。

## 主要用途

主要面向非华硕主板，提供逐核心频率限制，用于降低高负载下的处理器温度，或在超频调试时限制峰值频率。

## 功能

- 选择物理核心并调整 MaxPerf；选中核心的全部 SMT 线程会同步处理。
- 支持逐核心数值、批量应用、硬件上限 `A`、读取和保存预设。
- 预设保存到 `C:\Program Files\Juxic Per Core Limit\CppcPreset.txt`。
- 显示核心频率和有效频率，单位为 MHz。
- 「禁用闲置」用于调试时观察施加限制后的频率。建议临时开启，配合 HWiNFO 对照读数，观察结束后重新启用闲置。
- 程序独立采样频率，日常运行无需接入 HWiNFO，减少游戏期间的额外后台采样开销。
- 支持中英文界面、亮色/暗色主题、开机自启、静默自启和系统托盘。
- 关闭程序或从托盘停止限制时，恢复本次运行修改过的设置。
- 前端使用 Rust 和 Win32 GDI 自绘；后端使用 C++20 Windows 服务；两者通过本地命名管道通信。

## 支持范围

已加入 Zen 4 / Zen 5 Ryzen、Threadripper 和移动平台的已知型号检测，包括 Family 19h 与 Family 1Ah 的相关型号。检测通过后，程序还会逐个逻辑 CPU 验证 CPPC 状态、性能范围和读回结果。

已验证型号：Ryzen 9 7950X3D、Ryzen 9 9900X3D、Ryzen 9 9950X3D（16 核 / 32 线程）。其它 CPU 仍需在对应平台上实际验证。

## 构建

默认使用 Cargo、CMake 和 Visual Studio 2022 MSVC x64 构建：

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File native/build.ps1
```

构建前需安装 Rust stable MSVC target、Visual Studio 2022 C++ Build Tools、Windows SDK 和 CMake 3.21+。从 [PawnIO.Modules 0.2.12](https://github.com/namazso/PawnIO.Modules/releases/tag/0.2.12) 下载 `release_0_2_12.zip`，将其中的 `AMDFamily17.bin` 解压到 `cppc-deps/modules/AMDFamily17.bin`。

发布文件位于：

`bin/Release/juxic-single/Juxic Per-Core Limit.exe`

也可以使用 LLVM-MinGW 构建：

```powershell
native/build.ps1 -LlvmBin <llvm-mingw/bin>
```

LLVM-MinGW 构建还需要 Ninja。