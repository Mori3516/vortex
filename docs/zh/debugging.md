# 调试 Vortex GPU (Debugging Vortex GPU)

## 测试对 RTL 或仿真器 GPU 驱动程序的更改 (Testing changes to the RTL or simulator GPU driver)

如果 h/w 配置与上次运行相同，Blackbox 实用程序脚本将不会获取你的更改。
要强制实用程序构建驱动程序，运行测试时需要传递 --rebuild=1 选项。
使用 --rebuild=0 将阻止重建，即使 h/w 配置与上次运行不同。

    $ ./ci/blackbox.sh --driver=simx --app=demo --rebuild=1

## SimX 调试 (SimX Debugging)

SimX 周期近似仿真器允许加快 Vortex 内核执行的调试。
启用调试的推荐方法是在运行程序时向 `blackbox` 工具传递 `--debug=<level>` 标志。

    // 在调试模式下在 SimX 上运行演示程序
    $ ./ci/blackbox.sh --driver=simx --app=demo --debug=1

程序执行期间会在当前目录中生成调试跟踪 `run.log`。该跟踪包括仿真处理器的重要状态（解码指令、寄存器状态、流水线状态等）。你可以通过更改调试级别来增加跟踪的详细程度。

    // 以详细级别 3 在调试模式下使用 SimX
    $ ./ci/blackbox.sh --driver=simx --app=demo --debug=3

## RTL 调试 (RTL Debugging)

要调试处理器 RTL，你需要使用 VLSIM 或 RTLSIM 驱动程序。VLSIM 仿真整个处理器，包括 AFU 命令处理器（使用 `/rtl/afu/opae/vortex_afu.sv` 作为顶层模块）。RTLSIM 仅仿真 Vortex 处理器（使用 `/rtl/Vortex.v` 作为顶层模块）。

启用调试的推荐方法是在运行程序时向 `blackbox` 工具传递 `--debug` 标志。

    // 在调试模式下在 opae 仿真器上运行演示程序
    $ TARGET=opaesim ./ci/blackbox.sh --driver=opae --app=demo --debug=1

    // 在调试模式下在 rtlsim 上运行演示程序
    $ ./ci/blackbox.sh --driver=rtlsim --app=demo --debug=1

程序执行期间会在当前目录中生成调试跟踪 `run.log`。该跟踪包括仿真处理器的重要状态（内存、缓存、流水线、停顿等）。程序执行期间还会在当前目录中生成波形跟踪 `trace.vcd`。
默认情况下，/libs/ 文件夹下所有库模块均从跟踪中排除以减小波形文件大小，你可以通过在 lib 模块源文件 (例如 VX_stream_buffer.sv) 中显式注释掉 `TRACING_OFF`/`TRACING_ON` 来更改此行为，或者简单地通过定义 TRACING_ALL 来启用完整跟踪，如下所示。

    // 在完全跟踪模式下使用 rtlsim 调试演示程序
    $ CONFIGS="-DTRACING_ALL" ./ci/blackbox.sh --driver=rtlsim --app=demo --debug=1

你可以使用任何可以打开 VCD 文件的工具（Modelsim, Quartus, Vivado 等）可视化波形跟踪。[GTKwave] (http://gtkwave.sourceforge.net) 是一个很好的开源范围分析器，也可用于 VCD 文件。

## FPGA 调试 (FPGA Debugging)

可能需要直接调试 FPGA 以调查 RTL 仿真无法捕获的运行时错误。我们为 Vortex 实施了一个内部范围分析器 (in-house scope analyzer)，该分析器在 FPGA 运行时工作。要启用 FPGA 范围分析器，应使用 `SCOPE=1` 标志构建 FPGA 比特流

    $ cd /hw/syn/opae
    $ CONFIGS="-DSCOPE=1" TARGET=fpga make