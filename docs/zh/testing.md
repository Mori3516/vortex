# 测试 (Testing)

## 运行 Vortex 应用程序 (Running a Vortex application)

该框架提供了一个实用程序脚本：`/ci/` 文件夹下的 `blackbox.sh`，用于在 tests 树中执行应用程序。在所有环境变量已解析的情况下，它被复制到 `build` 目录中，因此你应该从 `build` 目录如下运行它：
你可以使用以下命令查询该工具的命令行选项：

    $ ./ci/blackbox.sh --help

要在 simx 驱动程序上执行 sgemm 测试程序并传递 "-n10" 作为 sgemm 的参数：

    $ ./ci/blackbox.sh --driver=simx --app=sgemm --args="-n10"

你可以执行具有 2 个核心的 GPU 架构的相同应用程序：

    $ ./ci/blackbox.sh --core=2 --driver=simx --app=sgemm --args="-n10"

执行时，如果所需架构发生变化，Blackbox 需要重新编译驱动程序。
它跟踪当前目录 `blackbox.<driver>.cache` 下文件中的最新配置。
为避免一直重新构建驱动程序，Blackbox 会检查最新缓存的配置是否与当前匹配。

## 运行基准测试 (Running Benchmarks)

Vortex 测试套件位于 `/test/` 文件夹下。
你可以通过在根文件夹运行以下命令来执行默认回归套件：

    $ make -C tests/regression run-simx
    $ make -C tests/regression run-rtlsim

你可以通过在根文件夹运行以下命令来执行默认 opencl 套件：

    $ make -C tests/opencl run-simx
    $ make -C tests/opencl run-rtlsim

## 创建你自己的回归测试 (Creating Your Own Regression Test)

在 `tests/regression` 中，你会找到一系列根据其测试内容命名的文件夹。
你可以查看这些测试，看看哪一个与你试图创建新测试的测试相似。
找到类似的基准后，你可以复制该文件夹并将其重命名为你计划测试的名称。
回归测试通常实现以下文件：
- ***kernel.cpp*** 包含 GPU 内核代码。
- ***main.cpp*** 包含主机 CPU 代码。
- ***Makefile*** 定义了 CPU 和 GPU 二进制文件的编译器构建命令。

同步你的构建文件夹：`$ ../configure`

编译你的测试：`$ make -C tests/regression/<test-name>`

运行你的测试：`$ ./ci/blackbox.sh --driver=simx --app=<test-name> --debug`

## 将你的测试添加到 CI 流水线 (Adding Your Tests to the CI Pipeline)
如果你是贡献者，那么你需要添加集成到持续集成流水线的测试。记住，除非新代码有测试并且现有测试没有退化，否则无法合并拉取请求。此外，如果你贡献了一个新功能，建议你添加启用/禁用你添加的新功能的能力。有关更多信息，请参阅 [contributing.md](contributing.md) 和 [continuous_integration.md](continuous_integration.md)。