# TCC 组工程实验记录

本仓库记录时序异常与 WCET 方向的工程实践，方便组内同学了解实验目标、复查数据、复用操作流程。目前围绕三条工作线展开：Ariane/MINOTAuR 仿真对比、LLVM-TA+ 工具学习，以及时间可预测性相关文献阅读。这里整理的是个人复现与学习记录，原项目代码以各自上游仓库为准。

## 从哪里开始

| 方向            | 先读什么                                                     | 当前进度                                                     |
| --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Ariane/MINOTAuR | [实验说明](https://chatgpt.com/c/minotaur/README.md)、[bsort 单例](https://chatgpt.com/c/minotaur/bsort.md)、[批量结果](https://chatgpt.com/c/minotaur/results.md) | 截至2026-09-21 02:26，已开展51个测例的测试；46个获得同输入双分支成功结果，其中27个观测到 LSU inversion 从 Ariane 非零降至 MINOTAuR 0 |
| LLVM-TA+        | [学习与运行记录](https://chatgpt.com/c/llvmta/README.md)     | 已跑通循环示例，继续学习 LLVM IR、SSA、phi、基本块及时间分析流程 |
| 相关文献阅读    | [阅读记录](https://chatgpt.com/c/papers/README.md)           | 围绕 pWCET、可预测矩阵运算和缓存分析开展初步学习，逐步整理与当前课题的联系 |

## 周报

- [第3周：批量实验、WCET工具学习与文献阅读](https://chatgpt.com/c/weekly/2026-week03.md)

批量实验中的 inversion 计数变化不等同于严格意义上的 timing anomaly 证据。最新统计及其适用范围见[批量结果](https://chatgpt.com/c/minotaur/results.md)。

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
papers/
  README.md       # 关于RTSS/RTAS论文阅读
weekly/
  2026-week03.md  # 第三周周报
```
