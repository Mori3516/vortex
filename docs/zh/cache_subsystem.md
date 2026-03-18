# Vortex 缓存子系统 (Vortex Cache Subsystem)

Vortex 缓存子系统具有以下主要属性：

- 具有多 bank 并行性的高带宽传输 (High-bandwidth transfer with Multi-bank parallelism)
- 具有每 bank MSHR 的非阻塞直写缓存架构 (Non-blocking pipelined write-through cache architecture with per-bank MSHR)
- 可配置设计：Dcache, Icache, L2 cache, L3 cache (Configurable design: Dcache, Icache, L2 cache, L3 cache)

### 缓存微架构 (Cache Microarchitecture)

![Image of Cache Hierarchy](../assets/img/cache_microarchitecture.png)

Vortex 缓存由多个并行 bank 组成。它由以下模块组成：
- **Bank 请求分发交叉开关 (Bank request dispatch crossbar)**: 将 bank 分配给传入的请求并使用停顿解决冲突 (assigns a bank to incoming requests and resolve collision using stalls).
- **Bank 响应合并交叉开关 (Bank response merge crossbar)**: 合并来自 bank 的结果并转发给核心响应 (merges result from banks and forward to the core response).
- **内存请求多路复用器 (Memory request multiplexer)**: 仲裁 bank 内存请求 (arbitrates bank memory requests)
- **内存响应解复用器 (Memory response demultiplexer)**: 将内存响应转发给相应的 bank (forwards memory response to the corresponding bank).
- **刷新单元 (Flush Unit)**: 执行标记内存初始化 (performs tag memory initialization).

进入缓存的传入请求被发送到分发交叉开关，该交叉开关为每个请求选择相应的 bank，通过停顿解决 bank 冲突。每个 bank 的结果输出通过合并交叉开关合并回传出响应端口。每个 bank 集成了一个非阻塞流水线和一个本地未命中状态保持寄存器 (MSHR)，以降低未命中率。Bank 流水线由以下阶段组成：

- **调度 (Schedule)**: 从传入的核心请求、内存填充或 MSHR 条目中选择下一个进入流水线的请求，优先考虑后者 (Selects the next request into the pipeline from the incoming core request, memory fill, or the MSHR entry, with priority given to the latter).
- **标记访问 (Tag Access)**: 对标记存储的单端口读/写访问 (single-port read/write access to the tag store).
- **数据访问 (Data Access)**: 对数据存储的单端口读/写访问 (Single-port read/write access to the data store).
- **响应处理 (Response Handling)**: 给核心的核心响应 (Core response back to the core).

当 MSHR 已满并且新请求已在流水线中时，缓存内部可能会发生死锁。当内存请求队列已满并且有传入的内存响应时，也会发生这种情况。缓存通过在发出新请求之前使用早期满信号来缓解 MSHR 死锁，并通过确保其请求队列永远不会填满来类似地缓解内存死锁。