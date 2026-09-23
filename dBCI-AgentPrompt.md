# AGENT 提示词

如果你是AGENT，请不要修改本AGENT.MD。
任何修改都必须由用户手动进行或得到用户的直接同意后执行。

## 项目全局概览

dBCI项目是一个面向脑机接口领域数据的平台、软件、生态开发项目，它的主要产物是临枢系统，该系统由多个子系统（子组件）组成：

- 临析 Linxi：数据处理引擎
- 临析LGLDeploy Linxi-LGLDeploy：临析在LGL内部部署时所使用到的私有插件与功能封装，不对外发布
- 临析Trodes Linxi-Trodes：基于Trodes开源软件再分发的版本，提供了Trodes软件的少部分功能的python精简实现
- 临析PluginTemplate Linxi-PluginTemplate：基于临析插件系统的插件模版仓库，供开发者参考使用
- 临析ReferenceCode Linxi-ReferenceCode：用于开发临析的参考代码仓库
- 临畴 Linchou：数据共享平台
- 临策 Lince：算法竞赛
- 临枢数据契约 LinshuFormat：LinshuFile数据契约的定义与程序实现
- dBCI-Contexts：dBCI项目的上下文仓库，包含了项目的上下文信息，亦包含一些论文撰写的工作

## 开发规范

### 编程守则

- YAGNI：不为未遇到的错误 / 类型不匹配建保护。
- Fast Fail：不吞异常；CLI 层错误处理统一走 `slurm_common.validate_common_args` + Linxi 上游链路。
- 单一调用方倾向内联；重复出现再抽。
- PEP 8 + 现代类型注解；本仓 pyright 受上游 Linxi 约束（见「构建 / 检查 / 测试」）。
- 命名：语义清晰性高于精简。
- 代码风格要参考已有内容，整体保持一致。
- 自包含性 / 可脱离性：注释、docstring 里出现的每个标识（编号、名称、路径），必须在"不查阅外部工作计划、不处于编写当时的会话、未安装那个编排工具"的前提下，仍能被一个全新读者独立解析。
- 如果能使用临时脚本验证某个单元的功能，就不要为这个小单元引入单独的测试代码，防止测试代码体量过度扩张。

### 实现前调研

仓库内已有 → Python 生态（Snakemake / submitit / SLURM CLI / spikeinterface / pynwb / ...）已有 → 联网查业内做法 → 都不可行才自造。涉及 Linxi 数据接入时优先复用上游现有 processor，不要在本仓重新实现。

### 工具链与提交

- 输出代码时不需要考虑格式、换行，格式化工作完全由静态检查工具执行。不需要在输出时为了控制单句长度而断行，同一行的代码或者注释都不需要由你添加断行符。
- 静态检查由各个仓库内的 pre-commit 完成，如果仓库中没有 pre-commit，则不要进行静态检查。

### 向后可不兼容

当前开发阶段以简洁性、可读性优先，向后兼容不是硬性要求。但协同开发中若改动会引入向后不兼容，必须显式汇报给用户并请求确认后再继续。

### 运行时

请使用 conda 虚拟环境运行，参考代码：

```bash
conda run -n dbci --no-capture-output <command>
```

如果在运行过程中，你想要修改python环境中的包或者其他内容，应当显式获得用户的直接同意。

请注意运行pytest在大型仓库中是非常昂贵的，不要轻易执行全仓测试，这种测试一般只在任务接近完成时运行一次。

### git

请按需创建新分支，以免影响其他工作，必要时可以使用git worktree。
不允许在本仓库的commit中以AI Agent署名，请以git全局默认配置为准。

## LGLDeploy 相关内容

LGLDeploy通常在SLURM集群上线运行

用户要求或按照业务计划需要时，你可以通过SLURM Agent对集群进行操作。
用户的SLURM目录为`/DPC_HOME/zhangyiqin`，所有的文件修改都只能在该目录下进行，包括临时文件路径：`/DPC_HOME/zhangyiqin/tmp`。

你所做出的代码修改都是在本地运行的，需要提交到git之后，在SLURM上执行拉取。
SLURM的conda环境主要使用dbci虚拟环境，其中的python package都不是使用可编辑模式的，如果发生了代码改动，需要注意使用 `uv pip` 指令来更新环境。

SLURM集群的SSH指令响应非常慢，IO操作也很慢，因此当你需要在SLURM上执行一些测试时，需要尽可能减少总计算量。
当你运行一些端到端业务指令时，可以根据实际数据情况，调整指令参数来减少任务量（参考 `linxi_lgldeploy/motion/doc/readme.md` / `linxi_lgldeploy/memory/doc/readme.md` 的端到端示例）。
