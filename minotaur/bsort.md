# bsort：Ariane 与 MINOTAuR 对比

## 数据来源

服务器 `~/research_ariane/server_batch/results.csv` 中，两个分支的 `bsort` 状态均为 `OK`。对应原始日志为：

| 分支 | 原始日志 | 日志文件时间 |
| --- | --- | --- |
| Ariane | `~/research_ariane/server_batch/ariane/bsort.log` | 2026-09-14 15:27（服务器本地时间） |
| MINOTAuR | `~/research_ariane/server_batch/minotaur/bsort.log` | 2026-09-14 16:19（服务器本地时间） |

代码 HEAD 分别为 `59a05d401f02cb9129e5e2ac35bb9e4afa6548d7` 和 `04a4e12b90ada078a9e97cf9856a6a5f7e0b0109`。两份 `bsort.mem` 的 SHA-256 均为：

```text
df93e0e807bc4d56b65a2a9849b7d2e4347d287efe8e0a4e256ae3397c0f3a5a
```

检查时 Ariane 的 `src/verifier.sv` 有未提交修改；实验执行者确认其不影响本次结果。`parallel_work` 中重复出现的 `bsort-ariane-server.log` 是工作目录里的同内容文件，本页不将它们计为独立重复试验。

## 原始计数

| `results.csv` 字段 | Ariane | MINOTAuR |
| --- | ---: | ---: |
| `brpending` | 5,871 | 5,871 |
| `mempending` | 57,728 | 59,521 |
| `lsu` | 2 | 0 |
| `imiss` | 28 | 0 |
| `imisscnt` | 65 | 55 |
| `total_time` | 60,862 | 61,610 |
| `base_time` | 1,182 | 1,360 |
| `total_committed` | 52,180 | 52,180 |
| `base_committed` | 298 | 298 |
| `elapsed_seconds`（主机耗时） | 2,280.3 | 3,147.5 |

## 计算与结论

按本次结果整理的口径，主体周期为 `total_time - base_time`；主体提交指令为 `total_committed - base_committed`。因此：

| 指标 | Ariane | MINOTAuR | 变化 |
| --- | ---: | ---: | ---: |
| 主体周期 | 59,680 | 60,250 | +570（约 +0.955%） |
| 主体提交指令 | 51,882 | 51,882 | 0 |
| 主体 CPI | 约 1.150 | 约 1.161 | 约 +0.011 |

在相同 `bsort.mem` 输入下，两分支成功完成仿真，总提交指令数均为 52,180，主体提交指令数均为 51,882。MINOTAuR 的 `lsu` 计数由 2 降至 0，`imiss` 由 28 降至 0，而主体周期增加了 570。可报告为**本次测例中相关 LSU 检测现象受到抑制，运行周期略增**。单次分支对比不足以证明发生了严格意义上的时序异常，也不能由这组主机耗时判断处理器周期性能。

按第6周批量统计采用的 `total_time` 口径，bsort 的执行周期从 60,862 增至 61,610，增幅约 1.23%；这与上表扣除 `base_time` 后的主体周期增幅约 0.955% 属于不同口径。两者都不能直接称为 WCET。

## 第6周波形定位记录

已使用 Questa 定位 Ariane 的 bsort 检测区间：`dcache_load_delayed` 首次拉高约在 **65,060 ns**，连续检测区间约为 **65,060–65,140 ns**。该区间内 LSU 检测计数从 0 增至 1，再增至 2。因此，`lsu=2` 对应同一连续区间内的两个计数周期，不能直接写成“两次独立 inversion”或“两次 timing anomaly”。波形时间单位 ns 与 `total_time` 的周期单位应分别记录。

两分支 `bsort.mem` 的 SHA-256 已核对一致（见上方哈希），总提交指令数也均为 52,180。这些信息支持输入与指令数对照，但不能替代微架构请求的因果分析。

当前仓库没有记录 MINOTAuR 对应波形对照或完整请求因果分析的完成证据，以下工作列为待完成：

- 对照 MINOTAuR 对应执行区间的请求、响应、提交及检测信号，补充可复查的波形材料。
- 沿 Ariane 检测区间追踪请求与响应顺序，解释延迟信号、计数增长与程序完成时间的因果关系。
- 开展受控局部延迟实验，在相同输入和可比初始状态下比较程序整体完成时间，验证严格意义上的时序异常；不能仅以两分支计数差异替代这一验证。



## 后续批量实验

bsort作为单例保留，用于说明指标含义与计算口径。后续已将实验扩展到多个TACLeBench测例，最新统计、配对方法及结论范围见[批量测试记录](results.md)。
