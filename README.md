# TCC 组工程实验记录

本仓库记录时序异常与 WCET 方向的工程实践，方便组内同学了解实验目标、复查数据、复用操作流程。目前围绕三条工作线展开：Ariane/MINOTAuR 仿真对比、LLVM-TA+ 工具学习，以及时间可预测性相关文献阅读。这里整理的是个人复现与学习记录，原项目代码以各自上游仓库为准。

## 从哪里开始

| 方向            | 目录说明                                                     | 当前进度                                                     |
| --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Ariane/MINOTAuR | [实验说明](minotaur/README.md)、[bsort 单例与波形](minotaur/bsort.md)、[批量结果](minotaur/results.md) | 第6周：50个有效测例双分支均 OK，29个 Ariane LSU inversion 检测计数非零，MINOTAuR 的50个测例均为0。排除 mpeg2、susan；CSV 的51个不同测例中 susan 为 TIMEOUT/MISSING，不计入有效测例成功率，151条历史记录不是测例数。各测例执行周期增幅等权平均约9.72%、中位数约8.35%。 |
| LLVM-TA+        | [学习与运行记录](llvmta/README.md) | 此前已验证 my_loop10、my_loop100；3360/3361 差异复核后已消失，my_loop1000 尚未运行，尚未建立匹配 MINOTAuR 的硬件分析模型 |
| 相关文献阅读    | [阅读索引](papers/README.md) | 围绕 MINOTAuR、LLVM-TA/Lavinium、A POP⋆ is Born、ScalarEvolution 缓存分析、可预测 SIMD GEMM 与 S3 六个主题推进精读整理；具体状态按已有笔记区分，pWCET 等历史初读保留 |

## 周报

- [第6周：批量结果、bsort 波形与六个阅读主题](weekly/2026-week06.md)
- [第4周：长期重跑与论文阅读](weekly/2026-week04.md)
- [第3周：批量实验、WCET工具学习与文献阅读](weekly/2026-week03.md)

批量实验中的 inversion 检测计数变化表明本次测试中相关检测现象受到抑制，不等同于严格意义上的 timing anomaly 证据，不能宣称消除了所有时序异常。bsort 的 `lsu=2` 对应同一连续检测区间内的两个计数周期。最新统计及其适用范围见[批量结果](minotaur/results.md)。本次更新环境无法访问服务器 CSV，采用已确认汇总，完整最新配对表待补；此前“47个双分支成功、28个非零计数”的当前进展已过时，历史周报与结果快照保留当时统计。

## 记录原则

每项实验尽量写明代码版本、输入、运行环境、命令、原始日志、输出指标和结论范围。`total_time` 的**执行周期**与主机上的**实际运行耗时（秒）**分别记录，执行周期不能直接称为 WCET。批量测试保留初跑和重跑记录，不把超时项的空字段当作零。

`bsort` 中 MINOTAuR 的 `lsu` 计数由 2 降为 0，但这一单例对比不能单独证明出现了时序异常。LLVM-TA+ 的当前示例用于学习静态 WCET 分析流程，其结果不是 Ariane/MINOTAuR 或 FPGA 原型的 WCET。

## 目录

```text
README.md
minotaur/
  README.md       # 工程和复现入口
  bsort.md        # 已确认的单例数据
  results.md      # 当前汇总与历史快照
llvmta/
  README.md       # 示例、配置、输出和待解决问题
papers/
  README.md       # 关于RTSS/RTAS论文阅读
weekly/
  2026-week03.md  # 第三周周报
  2026-week04.md  # 第四周周报
  2026-week06.md  # 第六周周报
```
