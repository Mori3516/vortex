# Vortex 仿真方法 (Simulation Methods)

### RTL 仿真 (RTL Simulation)

[Verilator](https://www.veripool.org/projects/verilator/wiki) 是一个 Verilog/SystemVerilog 设计仿真器，它将 Verilog HDL 转换为单线程或多线程的 C++/SystemC 代码以执行设计仿真 (design simulation)。Verilator 的安装指南位于[此处](https://www.veripool.org/projects/verilator/wiki/Installing)。

### 周期近似仿真 (Cycle-Approximate Simulation)

SimX 是为 Vortex 开发的内部 C++ 周期级仿真器 (cycle-level in-house simulator)。相关文件位于 `simx` 文件夹中。[自述文件](README.md) 有构建和运行 simX 的最详细说明。

- 要在你自己的系统上安装，[请遵循此文档](install_vortex.md)。
- 对于 Vortex 支持的不同佐治亚理工学院环境，[请阅读此文档](environment_setup.md)。

### FPGA 仿真 (FPGA Simulation)

具有特定配置的 fpga 构建指南位于[此处](fpga_setup.md)。你可以找到基于 Xilinx 和 Altera 的 FPGA 的说明 (instructions for both Xilinx and Altera based FPGAs).

### 如何测试 (使用 `blackbox.sh`) (How to Test)

在特定驱动程序 (rtlsim,simx,fpga) 下运行测试是使用位于 `ci` 文件夹中的名为 `blackbox.sh` 的脚本完成的。从 Vortex 根目录运行命令 `./ci/blackbox.sh --help` 将显示 `blackbox.sh` 的以下命令行参数：

- *Clusters* - 用于指定配置中的簇 (处理元件的集合) 数量 (used to specify the number of clusters (collection of processing elements) within a configuration).
- *Cores* - 用于指定配置中的核心 (包含多个 warps 的处理元件) 数量 (used to specify the number of cores (processing element containing multiple warps) within a configuration).
- *Warps* - 用于指定配置中的 warps (并发硬件线程的集合) 数量 (used to specify the number of warps (collection of concurrent hardware threads) within a configuration).
- *Threads* - 用于指定配置中的线程 (最小计算单元) 数量 (used to specify the number of threads (smallest unit of computation) within a configuration).
- *L2cache* - 用于启用 Vortex 核心之间共享的 L2 缓存 (used to enable the shared l2cache among the Vortex cores).
- *L3cache* - 用于启用 Vortex 簇之间共享的 L3 缓存 (used to enable the shared l3cache among the Vortex clusters).
- *Driver* - 用于指定运行 Vortex 仿真的驱动程序 (rtlsim, opae, xrt, simx) (used to specify which driver to run the Vortex simulation).
- *Debug* - 用于启用 Vortex 仿真的调试模式 (used to enable debug mode for the Vortex simulation).
- *Perf* - 用于启用 Vortex 仿真中的详细性能计数器 (used to enable the detailed performance counters within the Vortex simulation).
- *App* - 用于指定在 Vortex 仿真中运行哪个测试/基准测试 (used to specify which test/benchmark to run in the Vortex simulation). 主要选择是 vecadd, sgemm, basic, demo, 和 dogfood。其他测试/基准测试位于 `/benchmarks/opencl` 文件夹中，虽然并非所有都适用于当前版本的 Vortex。
- *Args* - 用于向应用程序传递附加参数 (used to pass additional arguments to the application).

命令行参数使用示例：使用 opae 驱动程序运行 sgemm 基准测试，Vortex 配置为 1 个簇，4 个核心，4 个 warps，和 4 个线程。

    $ ./ci/blackbox.sh --clusters=1 --cores=4 --warps=4 --threads=4 --driver=opae --app=sgemm

终端输出：
```
Create context
Create program from kernel source
Upload source buffers
Execute the kernel
Elapsed time: 2463 ms
Download destination buffer
Verify result
PASSED!
PERF: core0: instrs=90802, cycles=52776, IPC=1.720517
PERF: core1: instrs=90693, cycles=53108, IPC=1.707709
PERF: core2: instrs=90849, cycles=53107, IPC=1.710678
PERF: core3: instrs=90836, cycles=50347, IPC=1.804199
PERF: instrs=363180, cycles=53108, IPC=6.838518
```

## 其他快速入门场景 (Additional Quick Start Scenarios)

支持使用不同配置和驱动程序运行 Vortex 仿真器。例如：

- 使用 rtlsim 驱动程序运行基本驱动程序测试，Vortex 配置为 2 个簇，2 个核心，2 个 warps，4 个线程

    $ ./ci/blackbox.sh --driver=rtlsim --clusters=2 --cores=2 --warps=2 --threads=4  --app=basic

- 使用 opae 驱动程序运行演示驱动程序测试，Vortex 配置为 1 个簇，4 个核心，4 个 warps，2 个线程

    $ ./ci/blackbox.sh --driver=opae --clusters=1 --cores=4 --warps=4 --threads=2 --app=demo

- 使用 simx 驱动程序运行 dogfood 驱动程序测试，Vortex 配置为 4 个簇，4 个核心，8 个 warps，6 个线程

    $ ./ci/blackbox.sh --driver=simx --clusters=4 --cores=4 --warps=8 --threads=6  --app=dogfood
