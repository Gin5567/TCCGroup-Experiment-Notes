# Ariane / MINOTAuR 仿真记录

## 目的与范围

在相同测例输入下分别运行 Ariane 和 MINOTAuR，记录 `[UART]` 中的计数器及周期数，观察 `lsu` inversion 计数和执行时间的变化。当前实验主要是工程复现和现象整理；要验证严格意义上的时序异常，还需要控制初始微架构状态并比较相应执行情形。

## 本次实验的代码与环境

| 项目 | 记录 |
| --- | --- |
| 服务器系统 | Ubuntu 22.04.5 LTS |
| 服务器 CPU | Intel Xeon Platinum 8488C |
| 仿真工具 | Questa；本次记录尚未附 `vsim -version` 原始输出 |
| Ariane 分支 / HEAD | `ariane` / `59a05d401f02cb9129e5e2ac35bb9e4afa6548d7` |
| MINOTAuR 分支 / HEAD | `minotaur` / `04a4e12b90ada078a9e97cf9856a6a5f7e0b0109` |
| 测例来源 | 服务器上的 `~/research_ariane/from-vm/` 两份工程 |

检查时，Ariane 工作树中的 `src/verifier.sv` 有未提交修改。实验执行者确认该修改不影响本次分析结果；本记录仍保留这一信息，避免将当前工作树误写成完全等同于 HEAD。两个分支的 `bsort.mem` SHA-256 相同，详见 [bsort](bsort.md)。

## 服务器位置与数据读取

工程目录：

```text
~/research_ariane/from-vm/MINOTAuR-ariane
~/research_ariane/from-vm/MINOTAuR-minotaur
```

批量运行脚本及结果：

```text
~/research_ariane/run_both_tacle_51.py
~/research_ariane/rerun_timeout_parallel.py
~/research_ariane/server_batch/results.csv
~/research_ariane/server_batch/progress.log
```

已知的 Ariane 单例命令是在工程根目录执行 `make sim batch-mode=1 APP=bsort`。批量运行及 MINOTAuR 的具体调用参数应以服务器上的脚本为准；不要只凭本页命令重建整批实验。

在服务器上检查原始数据，可使用：

```bash
grep -nF '[UART]: brpending:' ~/research_ariane/server_batch/ariane/bsort.log
grep -nF '[UART]: brpending:' ~/research_ariane/server_batch/minotaur/bsort.log
head -n 3 ~/research_ariane/server_batch/results.csv
```

`results.csv` 中的 `elapsed_seconds` 是主机运行耗时，`total_time` 是程序报告的仿真周期。两者单位和用途不同。

## 结果入口

- [bsort：完整对比和结论](bsort.md)
- [批量结果：初跑与重跑快照](results.md)

仓库可以加入经过检查的自编脚本和小型汇总表；原工程源码、许可证文件及大体积仿真日志应按各自项目的使用条件处理。
