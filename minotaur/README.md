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

## 批量运行与重跑

批量实验使用以下脚本：

- `~/research_ariane/run_both_tacle_51.py`：两分支批量运行。
- `~/research_ariane/rerun_timeout_parallel.py`：未完成项目的并行重跑。

首次批量运行曾采用7200秒超时，后续对未完成项目延长运行时间。具体统计见[批量结果](https://chatgpt.com/c/results.md)。

### 2026-09-21 后续重跑

以下命令来自实际启动记录；该轮运行发生在本页引用的02:26统计快照之后：

```bash
nohup python3 -u ~/research_ariane/rerun_timeout_parallel.py \
  --workers 9 \
  --timeout 2592000 \
  > ~/research_ariane/server_batch/rerun_parallel_final.progress.log \
  2>&1 < /dev/null &
```

本次设置：

| 参数        | 值        | 含义                                           |
| ----------- | --------- | ---------------------------------------------- |
| `--workers` | 9         | 配置的最大并发任务数，实际活跃数取决于剩余任务 |
| `--timeout` | 2592000秒 | 每项任务的主机运行超时上限，即30天             |

可查看进度：

```bash
tail -n 30 ~/research_ariane/server_batch/rerun_parallel_final.progress.log
```

### 结果整理方法

每次汇总记录查询时间，并保留原始CSV快照。根据分支、测例和输入哈希整理各次尝试，优先使用配置可比的成功结果，再进行双分支配对。

统计时分别报告：

- 运行尝试数量和状态分布。
- 不同测例数量及各分支可用状态。
- 同输入双分支成功的配对数量。
- LSU inversion计数变化及主体周期开销。
- 未完成项目及其处理情况。
