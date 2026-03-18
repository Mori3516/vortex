# FPGA 启动和配置指南 (FPGA Startup and Configuration Guide)

## 使用 CRNCH 获得 FPGA 访问权限 (Gaining Access to FPGA's with CRNCH)
如果你与佐治亚理工学院 (或相关研讨会) 有关联，你可以使用 CRNCH 的服务器获得对 FPGA 的远程访问权限。否则，你可以跳到下面的 Xilinx 或 Intel (Altera) 综合步骤。

## 什么是 CRNCH？ (What is CRNCH?)

**CRNCH** (Center for **R**esearch into **N**ovel **C**omputing **H**ierarchies) 是**新型计算层次结构研究中心**。

## CRNCH 提供什么？ (What does CRNCH Offer?)

**Logues Gallery (RG)**: 专注于开发我们对下一代硬件理解的新概念，重点是非正统和罕见的技术。**RG** 将从供应商、研究实验室和初创公司获取新的独特硬件（即上述“*rogues*”），并将这些硬件提供给托管数据中心环境中的学生、教师和行业合作者。

## 为什么 Rouges 很重要？ (Why are the Rouges Important?)

通过让学生和研究人员接触这套独特的硬件，我们希望促进关于硬件设计的跨领域讨论，这将推动未来的 *计算性能提升，远在摩尔定律“廉价晶体管”时代结束后*。具体来说，Rogues Gallery 包含可以被综合进 Vortex 硬件的 FPGA。

## Rouges Gallery 如何资助？ (How is the Rouges Gallery Funded?)

Rogues Gallery 测试平台主要由美国国家科学基金会 (NSF) 根据 NSF 奖项编号 [#2016701](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2016701&HistoricalAwards=false) 资助。

## Rouges Gallery 文档 (Rouges Gallery Documentation)

你可以在其官方文档[页面](https://gt-crnch-rg.readthedocs.io/en/main/index.html#)上阅读关于 RG 的更多详细信息。

你可以在[此处](https://mediaspace.gatech.edu/media/Jeff%20Young%20-%20Rogues%20Gallery%20-%20CRNCH%20Summit%202021/1_lqlgr0jj)收听有关 RG 的讲座。

[CRNCH 峰会 2023](https://github.com/gt-crnch/crnch-summit-2023/tree/main)

## 请求访问 Rouges Gallery (Request Access for Rouges Gallery)

你应该使用[此表单](https://crnch-rg.cc.gatech.edu/request-rogues-gallery-access/)请求访问 RG 的可重构计算 (vortex fpga) 资源。你应该会收到一封包含你正在创建的工单项目的电子邮件。一旦处理完成，你应该会收到一封确认你的访问已被授予的电子邮件。处理可能需要一些时间。

## 如何访问 Rouges Gallery？ (How to Access Rouges Gallery?)
访问 CRNCH's Rouges Gallery 有两种方法：
1) 基于 Web 的 GUI: [rg-ood.crnch.gatech.edu](http://rg-ood.crnch.gatech.edu/)
2) SSH: `ssh <your-gt-username>@rg-login.crnch.gatech.edu`


## 我应该把文件放在哪里？ (Where should I keep my files?)
CRNCH 服务器有一个名为 `USERSCRATCH` 的文件夹，可以在你的主目录中找到：`echo $HOME`。你应该将所有文件保留在此文件夹中，因为它在所有 Rouges Gallery 节点上都可用。

## **Rogues Gallery 中有哪些机器可用？** (**What Machines are Available in the Rogues Gallery?**)

机器的完整列表可以在[此处](https://gt-crnch-rg.readthedocs.io/en/main/general/rg-hardware.html)找到。此外，你可以在[此处](https://gt-crnch-rg.readthedocs.io/en/main/reconfig/xilinx/xilinx-getting-started.html)找到有关 FPGA 硬件的详细信息。

## 分配 FPGA 节点 (Allocate an FPGA Node)
一旦你连接到 CRNCH 登录节点，你可以使用 Slurm 调度程序使用 `salloc` 请求交互式作业。此[页面](https://gt-crnch-rg.readthedocs.io/en/main/general/using-slurm.html)解释了我们为什么要使用 Slurm 请求资源。`salloc` 的文档可以在[此处](https://gt-crnch-rg.readthedocs.io/en/main/general/using-slurm-examples.html)找到。还有这里。


要在 flubber9 (一个 fpga 开发节点) 上请求 16 个核心和 64GB RAM 6 小时：
```bash
salloc -p rg-fpga --nodes=1 --ntasks-per-node=16 --mem=64G --nodelist flubber1 --time=06:00:00
```
Xilinx 板的综合 (Synthesis for Xilinx Boards)
----------------------
一旦你登录，你需要完成一些首次配置。如果你对 Intel (Altera) 综合步骤感兴趣，请向下滚动。

### 源配置脚本 (Source Configuration Scripts)
```
# From any directory
$ source /opt/xilinx/xrt/setup.sh
$ source /tools/reconfig/xilinx/Vitis/2023.1/settings64.sh
```

如果你正在使用 apptainer，
```
$ export LD_LIBRARY_PATH=/opt/boost-1.66/lib/:/opt/openssl-1.1/lib/:$LD_LIBRARY_PATH
```


### 检查已安装的 FPGA 平台 (Check Installed FPGA Platforms)
`platforminfo -l` 告诉我们安装在当前 fpga 节点上的平台的正确名称。它应用于下面的 `PLATFORM` 变量。否则，如果有错误，则是前两个命令有问题。

### 安装 Vortex 工具链 (Install Vortex Toolchain)
Xilinx 综合过程需要 verilator 来生成比特流。最终，你需要整个工具链在 FPGA 上运行比特流。因此，Vortex 工具链可以如下安装。如果你正确完成这些步骤，你应该只需要完成一次，并且可以跳到 `激活 Vortex 工具链 (Activate Vortex Toolchain)`。
```
# Make a build directory from root and configure scripts for your environment
mkdir build && cd build && ../configure --tooldir=$HOME/tools

# Install the whole prebuilt toolchain
./ci/toolchain_install.sh --all

# Add environment variables to bashrc
echo "source <full-path-to-vortex-root>/vortex/build/ci/toolchain_env.sh" >> ~/.bashrc
```

### 激活 Vortex 工具链 (Activate Vortex Toolchain)
```
# From any directory
source ~/.bashrc

# Check environment setup
verilator --version
```

### 构建 FPGA 比特流 (Build the FPGA Bitstream)
根目录包含路径 `hw/syn/xilinx/xrt`，其中包含用于生成 Vortex 比特流的 makefile。

    $ cd hw/syn/xilinx/xrt
    $ PREFIX=test1 PLATFORM=xilinx_u50_gen3x16_xdma_5_202210_1 TARGET=hw NUM_CORES=1 make > build_u250_hw_1c.log 2>&1 &
```
将在新构建目录下运行综合：BUILD_DIR := "\<PREFIX>\_\<PLATFORM>\_\<TARGET>"
生成的比特流将位于 `<BUILD_DIR>/bin/vortex_afu.xclbin` 下

对于长时间运行的作业，可以通过以下形式调用此 makefile：

`[CONFIGS=<vortex macros>] [PREFIX=<prefix directory name>] [NUM_CORES=<#>] TARGET=hw|hw_emu PLATFORM=<platform baseName> nohup make > <log filename> 2>&1 &`

例如：

```bash
CONFIGS="-DL2_ENABLE -DDCACHE_SIZE=8192" PREFIX=build_4c_u280 NUM_CORES=4 TARGET=hw PLATFORM=xilinx_u280_gen3x16_xdma_1_202310_1 nohup make > build_u250_hw_4c.log 2>&1 &
```

当比特流文件 `vortex_afu.xclbin` 存在于 `<prefix directory name><platform baseName>hw|hw_emu/bin` 中时，构建完成。

### 在 Xilinx FPGA 上运行程序 (Running a Program on Xilinx FPGA)

构建目录中的 [blackbox.sh](./simulation.md) 脚本可用于使用以下命令通过 Vortex 的 xrt 驱动程序运行测试：

`FPGA_BIN_DIR=<path to bitstream directory> TARGET=hw|hw_emu PLATFORM=<platform baseName> ./ci/blackbox.sh --driver=xrt --app=<test name>`

例如：

```FPGA_BIN_DIR=<realpath> hw/syn/xilinx/xrt/build_4c_u280_xilinx_u280_gen3x16_xdma_1_202211_1_hw/bin TARGET=hw PLATFORM=xilinx_u280_gen3x16_xdma_1_202211_1 ./ci/blackbox.sh --driver=xrt --app=demo```

Intel (Altera) 板的综合 (Synthesis for Intel (Altera) Boards)
----------------------

### OPAE 环境设置 (OPAE Environment Setup)


    $ source /opt/inteldevstack/init_env_user.sh
    $ export OPAE_HOME=/opt/opae/1.1.2
    $ export PATH=$OPAE_HOME/bin:$PATH
    $ export C_INCLUDE_PATH=$OPAE_HOME/include:$C_INCLUDE_PATH
    $ export LIBRARY_PATH=$OPAE_HOME/lib:$LIBRARY_PATH
    $ export LD_LIBRARY_PATH=$OPAE_HOME/lib:$LD_LIBRARY_PATH

### OPAE 构建 (OPAE Build)

FPGA 具有以下配置选项：
- DEVICE_FAMILY=arria10 | stratix10
- NUM_CORES=#n

命令行：

    $ cd hw/syn/altera/opae
    $ PREFIX=test1 TARGET=fpga NUM_CORES=4 make

将创建一个新文件夹（例如：`test1_xxx_4c`），构建将开始并需要约 30-480 分钟才能完成。
设置 TARGET=ase 将使用 Intel ASE 构建用于仿真的项目。


### OPAE 构建配置 (OPAE Build Configuration)

硬件配置文件 `/hw/rtl/VX_config.vh` 定义了构建处理器时可以修改的所有硬件参数。例如，具有以下可配置的参数：
- `NUM_WARPS`:   每个核心的 warps 数量 (Number of warps per cores)
- `NUM_THREADS`: 每个 warp 的线程数量 (Number of threads per warps)
- `PERF_ENABLE`: 启用所有性能计数器的使用 (enable the use of all profile counters)

你可以从命令行配置综合构建：

    $ CONFIGS="-DPERF_ENABLE -DNUM_THREADS=8" make

### OPAE 构建进度 (OPAE Build Progress)

你可以检查构建日志的最后 10 行以查找可能的错误，直到构建完成。

    $ tail -n 10 <build_dir>/build.log

通过查找 quartus_sh, quartus_syn, 或 quartus_fit 程序来检查构建是否仍在运行。

    $ ps -u <username>

如果构建失败并且你需要重新启动它，请使用以下命令清理构建文件夹：

    $ make clean

当构建完成时，文件 `vortex_afu.gbs` 应该存在：

    $ ls -lsa <build_dir>/synth/vortex_afu.gbs


### 签署比特流并编程 FPGA (Signing the bitstream and Programming the FPGA)

    $ cd <build_dir>
    $ PACSign PR -t UPDATE -H openssl_manager -i vortex_afu.gbs -o vortex_afu_unsigned_ssl.gbs
    $ fpgasupdate vortex_afu_unsigned_ssl.gbs

### FPGA 运行测试示例 (Sample FPGA Run Test)
确保你拥有针对 FPGA 目标的正确 opae 运行时

```
$ TARGET=FPGA make -C runtime/opae
```

从你的 Vortex 构建目录运行 [blackbox.sh](./simulation.md)
