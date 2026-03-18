# Vortex 微架构 (Microarchitecture)

### Vortex GPGPU 执行模型 (Execution Model)

Vortex 使用 SIMT (单指令多线程) 执行模型，每个周期发射一个 warp (线程束)。

- **线程 (Threads)**
  - 最小计算单元 (Smallest unit of computation)
  - 每个线程有自己的寄存器文件 (32 int + 32 fp registers)
  - 线程并行执行 (Threads execute in parallel)
- **Warps (线程束)**
  - 线程的逻辑簇 (A logical cluster of threads)
  - warp 中的每个线程执行相同的指令 (Each thread in a warp execute the same instruction)
    - PC 是共享的；维护由于写回的线程掩码 (The PC is shared; maintain thread mask for Writeback)
  - Warp 的执行在对数步骤按时间复用 (Warp's execution is time-multiplexed at log steps)
    - 例如：warp 0 在周期 0 执行，warp 1 在周期 1 执行 (Ex. warp 0 executes at cycle 0, warp 1 executes at cycle 1)

### Vortex RISC-V ISA 扩展 (Extension)

- **线程掩码控制 (Thread Mask Control)**
  - 控制执行期间激活的 warp 数量 (Control the number of warps to activate during execution)
  - `TMC` *count*: 激活 count 个线程 (activate count threads)
- **Warp 调度 (Warp Scheduling)**
  - 控制执行期间激活的 warp 数量 (Control the number of warps to activate during execution)
  - `WSPAWN` *count, addr*: 激活 count 个 warps 并跳转到 addr 位置 (activate count warps and jump to addr location)
- **控制流分歧 (Control-Flow Divergence)**
  - 当分支分歧时控制线程激活 (Control threads activation when a branch diverges)
    - `SPLIT` *taken, predicate*: 应用谓词线程掩码并将当前状态保存到 IPDOM 栈 (apply predicate thread mask and save current state into IPDOM stack)
    - `JOIN`: 弹出 IPDOM 栈以恢复线程掩码 (pop IPDOM stack to restore thread mask)
    - `PRED` *predicate, restore_mask*: 线程谓词指令 (thread predicate instruction)
- **Warp 同步 (Synchronization)**
  - `BAR` *id, count*: 阻塞进入屏障 *id* 的 warps 直到达到 count (stall warps entering barrier *id* until count is reached)

### Vortex 流水线/数据通路 (Pipeline/Datapath)

![Image of Vortex Microarchitecture](../assets/img/vortex_microarchitecture.png)

Vortex 有一个 6 级流水线 (6-stage pipeline)：

- **调度 (Schedule)**
  - Warp 调度器 (Warp Scheduler)
    - 调度下一个 PC 进入流水线 (Schedule the next PC into the pipeline)
    - 跟踪停顿、活跃的 warps (Track stalled, active warps)
  - IPDOM 栈 (IPDOM Stack)
    - 保存分歧线程的 split/join 状态 (Save split/join states for divergent threads)
  - 在飞指令跟踪器 (Inflight Tracker)
    - 跟踪在飞指令 (Track in-flight instructions)

- **取值 (Fetch)**
  - 从内存检索指令 (Retrieve instructions from memory)
  - 处理 I-cache 请求/响应 (Handle I-cache requests/responses)
- **解码 (Decode)**
  - 解码获取的指令 (Decode fetched instructions)
  - 在控制指令上通知 warp 调度器 (Notify warp scheduler on control instructions)
- **发射 (Issue)**
  - IBuffer
    - 将解码的指令存储在单独的每 warp 队列中 (Store decoded instructions in separate per-warp queues)
  - 记分板 (Scoreboard)
    - 跟踪正在使用的寄存器 (Track in-use registers)
    - 检查解码指令的寄存器使用情况 (Check register use for decoded instructions)
  - 操作数收集器 (Operands Collector)
    - 从寄存器文件中获取发射指令的操作数 (Fetch the operands for issued instructions from the register file)
- **执行 (Execute)**
  - ALU 单元 (ALU Unit)
    - 处理算术和分支操作 (Handle arithmetic and branch operations)
  - FPU 单元 (FPU Unit)
    - 处理浮点操作 (Handle floating-point operations)
  - LSU 单元 (LSU Unit)
    - 处理加载/存储操作 (Handle load/store operations)
  - SFU 单元 (SFU Unit)
    - 处理 warp 控制操作 (Handle warp control operations)
    - 处理控制状态寄存器 (CSRs) 操作 (Handle Control Status Registers (CSRs) operations)
- **提交 (Commit)**
  - 将结果写回寄存器文件并更新记分板 (Write result back to the register file and update the Scoreboard.)

### Vortex 聚类架构 (clustering architecture)
- 插槽 (Sockets)
  - 共享 L1 缓存的多个核心的分组 (Grouping multiple cores sharing L1 cache)
- 簇 (Clusters)
  - 共享 L2 缓存的插槽的分组 (Grouping of sockets sharing L2 cache)

### Vortex 缓存子系统 (Cache Subsystem)
有关缓存子系统的更多详细信息，请参阅[此处](cache_subsystem.md)。