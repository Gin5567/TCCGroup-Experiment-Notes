# TCC 组工程实验记录

本仓库记录时序异常与 WCET 方向的工程实践，方便组内同学了解实验目标、复查数据、复用操作流程。目前包含两部分：Ariane/MINOTAuR 仿真对比，以及 LLVM-TA+ 工具学习。这里整理的是个人复现记录，原项目代码以各自上游仓库为准。

## 从哪里开始

| 方向 | 先读什么 | 当前进度 |
| --- | --- | --- |
| Ariane/MINOTAuR | [实验说明](minotaur/README.md)、[bsort 单例](minotaur/bsort.md)、[批量进度](minotaur/results.md) | `bsort` 两分支运行成功；批量测试及超时重跑仍在进行 |
| LLVM-TA+ | [学习与运行记录](llvmta/README.md) | 跑通 `my_loop10` 示例，正在理解分析流程和输出 |

## 记录原则

每项实验尽量写明代码版本、输入、运行环境、命令、原始日志、输出指标和结论范围。**仿真周期**与主机上的**实际运行耗时**分别记录。批量测试保留初跑和重跑记录，不把超时项的空字段当作零。

`bsort` 中 MINOTAuR 的 `lsu` 计数由 2 降为 0，但这一单例对比不能单独证明出现了时序异常。LLVM-TA+ 的当前示例用于学习静态 WCET 分析流程，其结果不是 Ariane/MINOTAuR 或 FPGA 原型的 WCET。

## 目录

```text
README.md
minotaur/
  README.md       # 工程和复现入口
  bsort.md        # 已确认的单例数据
  results.md      # 批量测试快照
llvmta/
  README.md       # 示例、配置、输出和待解决问题
```
