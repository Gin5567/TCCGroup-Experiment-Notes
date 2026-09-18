# 批量测试进度（2026-09-18 查询快照）

服务器结果文件：`~/research_ariane/server_batch/results.csv`。查询时该文件有 115 行（含表头），即 114 条**运行记录**；其中包括初跑及后续重跑，不能当作 114 个不同测例。重跑仍在进行，本页只列出已经从 CSV 原文核实的记录。

| 测例 | 分支 | 尝试 | 状态 | 主机耗时（秒） | `lsu` | `total_time`（周期） |
| --- | --- | --- | --- | ---: | ---: | ---: |
| `bsort` | Ariane | 初跑 | OK | 2,280.3 | 2 | 60,862 |
| `bsort` | MINOTAuR | 初跑 | OK | 3,147.5 | 0 | 61,610 |
| `filterbank` | Ariane | 初跑 | TIMEOUT | 7,200.3 | — | — |
| `filterbank` | MINOTAuR | 初跑 | TIMEOUT | 7,200.0 | — | — |
| `fir2dim` | Ariane | 初跑 | OK | 438.6 | 0 | 35,975 |
| `fir2dim` | MINOTAuR | 初跑 | OK | 540.7 | 0 | 38,242 |
| `fmref` | Ariane | 初跑 | TIMEOUT | 7,200.3 | — | — |
| `fmref` | MINOTAuR | 初跑 | TIMEOUT | 7,200.1 | — | — |
| `fmref` | Ariane | 重跑 | OK | 16,240.9 | 1 | 7,217,985 |

`rerun_parallel_8.progress.log` 在这次查询时显示“待重跑 30 项；排除：mpeg2, susan”，并记录了部分已完成的重跑。上表中的 Ariane `fmref` 已从初跑超时变为重跑成功；**MINOTAuR 的 `fmref` 在本快照中仍只有初跑超时记录**，不能据此判定其最终结果。TIMEOUT 行为空的 `lsu` 和周期是“未取得”，不是零。

本页是局部快照。若要统计全体测例的最终成功率或比较周期，需在重跑结束后按 `core + app + mem_sha256` 汇总各次尝试，并分别保留初跑结果与最终可用结果。`bsort` 的逐项解释见 [bsort.md](bsort.md)。
