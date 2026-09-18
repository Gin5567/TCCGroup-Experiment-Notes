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

在相同 `bsort.mem` 输入下，两分支成功完成仿真，主体提交指令数相同。MINOTAuR 的 `lsu` 计数由 2 降至 0，`imiss` 由 28 降至 0，而主体周期增加了 570。可报告为**本次测例中记录到的 LSU inversion 计数被消除，运行周期略增**。单次分支对比不足以证明发生了时序异常，也不能由这组主机耗时判断处理器周期性能。
