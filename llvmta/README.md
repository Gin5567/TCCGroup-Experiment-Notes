# LLVM-TA+ 学习与示例运行记录

## 为什么学习这个工具

本方向希望理解静态 WCET 分析如何从程序和处理器配置出发，推导执行时间上界。当前记录的是工具的**使用与分析流程学习**，与 [Ariane/MINOTAuR 仿真实验](../minotaur/README.md)属于两条不同的工程线；目前没有将这里的数值当作 RISC-V 或 FPGA 原型的 WCET。

当前使用的代码位于开发容器 `/workspaces/llvmta`，入口脚本位于 `testcases/run.py`。上游项目主页：[RTS-SYSU / Timing-Analysis-Multicores](https://github.com/RTS-SYSU/Timing-Analysis-Multicores)。本地检出的具体 commit 尚未记录，因此下面的命令按当前容器中的脚本说明。

## 已跑通的最小示例

`testcases/my_loop10` 中的 C 程序由 `task0` 执行 10 次循环，循环体使用 `volatile` 输入进行累加。示例的核心配置写在 `CoreInfo.json`，记录的 `core_count` 为 2。实际分析的是所提供程序及配置对应的模型，不代表物理机上实测的执行时间。

在容器终端运行：

```bash
cd /workspaces/llvmta/testcases
python3 run.py -s my_loop10 -o my_loop10/output -t my_loop10/tmp -p
python3 run.py -s my_loop10 -o my_loop10/output -t my_loop10/tmp
```

这是当前环境中已使用过的“先执行带 `-p` 的准备流程，再运行分析”的方式。`-s`、`-o`、`-t` 分别传入源目录、输出目录和临时目录。不要将此处命令直接用于其他版本的脚本而不查看其帮助与示例。

## 如何读结果

1. 阅读输入 C 源码和 `CoreInfo.json`，确定任务、核数和微架构假设。
2. 查阅过程中生成的 LLVM IR，例如 `testcases/dirforgdb/test.ll`、`unoptimized.ll`、`optimized.ll`，观察编译后的控制流。
3. 在 `my_loop10/output` 中查看 `WCET.json` 和 `0_core_wcettask0_MicroArchAnalysis.txt`，分别记录汇总结果与微架构分析信息。

当前一次运行中，`WCET.json` 对 `task0` 给出的 WCET 为 **3360**；微架构分析文本报告的 WCET 为 **3361**、BCET 为 **2193**，并记录 L1I misses 3、L1D misses 5、L2 misses 8、stores 5、writebacks 10。**两个文件中的 WCET 相差 1 周期，原因尚未核实**；引用结果时应同时写明来源文件，不把两者改写成同一个数值。

当前示例使用的配置包含乱序模型、分离的 L1 指令/数据缓存、L2 缓存及内存延迟参数；精确参数以本次 `CoreInfo.json` 和运行脚本实际读入值为准。学习重点是“C 程序 → LLVM IR → 控制流和微架构分析 → 时间界”的输入输出关系。后续若要用于 RISC-V 原型，还需要检查目标指令集和处理器模型的支持情况、配置与硬件的一致性，并用实测数据校验；目前的 `my_loop10` 结果不能直接移用。

## 第3周学习补充：程序表示与时间分析

### 基本块

基本块是一段顺序执行的指令序列。执行从块的入口开始，通过末尾的终结指令离开；分支指令将不同基本块连接成控制流图。

例如，C程序中的条件判断通常对应一个判断块、两个分支块和一个汇合块。控制流图帮助分析工具描述程序可能经过的路径。

### SSA与phi

SSA表示静态单赋值形式，每个SSA值在程序表示中只有一个定义。例如：

```llvm
%a = add i32 %x, 1
%b = mul i32 %a, 2
```

这里 `%a` 和 `%b` 分别由一条指令定义。循环中的指令仍然可以动态执行多次，因此“静态单赋值”不表示循环只能执行一次。

不同控制流路径汇合时，可以使用phi选择对应路径传来的值：

```llvm
then:
  br label %merge

else:
  br label %merge

merge:
  %v = phi i32 [ 1, %then ], [ 2, %else ]
```

如果从 `then` 块进入 `merge`，则 `%v` 为1；如果从 `else` 块进入，则 `%v` 为2。phi依据实际前驱块选择值。

SSA主要描述这种值定义关系，不表示所有内存位置都只能写入一次。

### 与WCET分析的关系

当前从三个层次理解分析流程：

1. 程序层：任务入口、基本块、分支、循环及可能的执行路径。
2. 微体系结构层：流水线、缓存和内存等状态如何影响执行时间。
3. 时间界汇总：结合路径约束与微体系结构分析结果，获得指定模型下的执行时间界。

LLVM IR有助于理解程序结构，但不能将IR指令数量直接当作执行周期。具体工具还涉及后续代码生成及所采用的分析表示，需结合实现继续学习。

### 当前边界与待解决问题

- 已能够运行示例并定位输入、配置和输出文件，分析算法与实现细节仍在学习。
- 需要进一步定位循环次数约束如何进入分析流程。
- 需要继续理解程序控制流、处理器状态分析与最终时间界之间的具体联系。
- 当前结果属于工具所配置的处理器模型，尚未用于评估Ariane/MINOTAuR或FPGA原型。
