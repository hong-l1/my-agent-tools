# my-agent-tools

个人 Agent 基础设施：积累可复用的工具、操作流程和记忆，减少在不同 Agent 中重复配置、编写和解释的工作。

当前阶段是目录骨架与约定，尚未实现工具、记忆存储或 Agent 接入。

## 目录职责

| 目录 | 内容 | 复用方式 |
| --- | --- | --- |
| [`tools/`](tools/README.md) | 可执行工具及其输入、输出和调用说明 | 通过 MCP 向不同语言实现的 Agent 提供能力 |
| [`skill/`](skill/README.md) | 任务触发条件、执行流程和验收标准 | 以 Agent Skills 格式分发，由 Agent 加载指令 |
| [`memory/`](memory/README.md) | 可复用的背景、偏好和已确认的项目决策 | 存储和读写机制待确定 |

## 跨 Agent 复用原则

1. Skill 的指令和流程独立于 Agent 的实现语言；采用 `SKILL.md` 与配套资源的形式维护。
2. Tool 通过 MCP Server 暴露，由 Agent 侧的 MCP Client 发现和调用；工具实现语言可以按实际需求选择。
3. 工具核心逻辑与 MCP 接口分开，核心逻辑不依赖某个 Agent 的消息类型、会话对象或专属 SDK。
4. 每个工具明确输入、输出、失败条件和副作用；调用凭据由运行环境提供。
5. Skill 使用相对路径和明确的工具依赖；附带脚本时说明运行环境，优先通过 MCP 调用可共享的工具能力。
6. 记忆使用可读、可导出的格式；区分已经确认的事实与待验证的推断。

兼容性关注 Agent 是否支持 Skill 加载和所选 MCP 版本、传输方式，以及所需能力是否可用。支持这些接口的 Agent 可以复用同一份 Skill 和工具服务，无需按 Agent 的实现语言重写。具体接入配置仍按宿主要求提供。

格式与协议分别参考 [Agent Skills 规范](https://agentskills.io/specification) 和 [MCP 架构说明](https://modelcontextprotocol.io/docs/learn/architecture)。

## 新增能力的顺序

1. 选一个实际会反复使用的任务，先把对应工具做成可独立调用的能力。
2. 通过 MCP 暴露工具，写清调用示例和失败情形，再接入第一个 Agent。
3. 将稳定的操作步骤整理为 Skill，声明所需的 MCP 工具，记录必要的背景和决策。
4. 在第二个支持 Skill 和 MCP 的 Agent 中加载同一份 Skill、接入同一套工具服务，验证复用效果。

工具对外调用协议确定为 MCP；实现语言、首个工具和传输方式在落地时选择。Memory 的存储与读写方式尚未确定，后续也可以将记忆读写能力通过 MCP 提供。

## 本地数据

访问凭据通过环境变量或本地配置提供，不写进代码、Skill 或记忆文件。
`memory/local/` 用于个人记忆，`memory/runtime/` 用于会话记录和临时状态，两者均默认忽略。
提交前检查新增文件；忽略规则不能识别所有敏感信息。
