---
title: Scrum敏捷开发基本思想
date: 2023-10-29 09:21:04
categories: 
- 学习
tags:
- 程序架构
---

### 敏捷开发基本思想

#### 和传统项目对比

传统项目管理：瞄准，瞄准，瞄准，射击，命中
敏捷项目管理：瞄准，射击，调整弹道，命中

- not command and control, but learn as we go
- not plan in details, but change happens
- not enforce the plan, but embrace change
- not "control" change, but inspect and adapt
![](agile1.png)

#### 敏捷宣言

- **Individuals and interactions** over processes and tools. 个体和互动 高于 流程和工具
- **Working software** over comprehensive documentation. 工作的软件 高于 详尽的文档
- **Customer collaboration** over contract negotiation. 客户合作 高于 合同谈判
- **Responding to change** over following a project plan. 响应变化 高于 遵循计划

<!-- more -->

#### 12条原则

- 我们最重要的目标，是通过持续不断地及早交付有价值的软件使客户满意。
- 欣然面对需求变化，即使在开发后期也一样。为了客户的竞争优势，敏捷过程掌控变化。
- 经常地交付可工作的软件，相隔几星期或一两个月，倾向于采取较短的周期。
- 业务人员和开发人员必须相互合作，项目中的每一天都不例外。
- 激发个体的斗志，以他们为核心搭建项目。提供所需的环境和支援，辅以信任，从而达成目标。
- 不论团队内外，传递信息效果最好效率也最高的方式是面对面的交谈。
- 可工作的软件是进度的首要度量标准。
- 敏捷过程倡导可持续开发。责任人、开发人员和用户要能够共同维持其步调稳定延续。
- 坚持不懈地追求技术卓越和良好设计，敏捷能力由此增强。
- 以简洁为本，它是极力减少不必要工作量的艺术。
- 最好的架构、需求和设计出自自组织团队。
- 团队定期地反思如何能提高成效，并依此调整自身的举止表现。

#### 敏捷是一种状态

从产出来看：频繁地交付可见的、可用的成果
从团队来看：主动、持续地学习和改进

### Agile敏捷流派

- Scrum：3355
- Xp：结对，重构，CI
- TDD：测试驱动开发
- FDD
- kanban
- 水晶方法
- DSDM

### Scrum敏捷流派

#### 角色
- Product Owner
  - 产品负责人（Product Owner）：代表利益相关者，负责定义产品的需求、优先级和发布计划。产品负责人与开发团队紧密合作，确保产品的需求得到满足。
- Development Team
  - Scrum团队（Scrum Team）：由开发人员组成的自组织团队，负责实现产品的需求。团队通常包括开发人员、测试人员和其他相关角色。团队通过迭代式的开发来交付软件，并在每个迭代中进行持续改进。
- ScrumMaster
  - Scrum主管（Scrum Master）：负责促进和支持Scrum团队的实践和过程。Scrum主管帮助团队遵循Scrum框架，解决障碍和问题，并促进团队的自我组织和持续改进。

#### 资产
- Product Backlog
- Scprint Backlog
- Increment

#### 事件
- Sprint Planning
  - 冲刺计划会议（Sprint Planning Meeting）：团队和产品负责人共同决定下一个冲刺中要完成的工作，并制定一个可执行的计划。
- Daily Scrum
  - 每日站会（Daily Scrum）：每天团队成员在15分钟内汇报工作进展、讨论遇到的问题和计划当天的工作。
- Sprint Review
  - 冲刺评审会议（Sprint Review）：在每个冲刺结束时，团队向利益相关者展示已完成的工作，并接受反馈和建议。
- Sprint Retrospective
  - 冲刺回顾会议（Sprint Retrospective）：团队在每个冲刺结束时进行回顾，评估团队的工作方式，识别改进点，并制定下一个冲刺的改进计划。
 
#### 价值观
- Courage
- Openness
- Focus
- Commitment
- Respect

#### 开发团队特征

- 5-9人
- 跨职能
  - 具备不同领域的必备技能
  - 不同层面的成熟度
  - T模型人才
- 全职：Sprints（迭代）之间方可换人
- 自组织
  - 没有管理者的头衔
  - **团队决定如何去工作**
  - 全员责任制
  - 团队承担大部分围观管理工作

#### 产品负责人（Product Owner）特征

- “一”个人
- 被授予产品（“做什么”）决策权
- 驱动产品走向成功
- 提供产品领导力
- 面向干系人代表团队
- 面向团队代表干系人
- 和所有合作

#### Scrum主管（Scrum Master）

- 面向管理层代表团队
- 面向团队代表管理层
- 不是一个项目经理或团队经理
- 没有管理头衔，不代表团队做出决定
- 是一个仆人式的领导者
- 。。。

#### 产品Backlog

- 一份动态的列表，包含了产品可能具备的功能（最好能做开关）
- 以及其他为用户带来价值的工作
- 一个健康的product backlog应当具备DEEP原则：
  - Detailed appropriately，恰当的详细程度
  - Estimated，已估算
  - Emergent，涌现式
  - Prioritized，优先级排序
- 对所有人开放但最终由PO（Product Owner）维护
- 关注于“什么”带给用户最大的价值
- 最优秀的PO从“为什么”开始

#### 用户故事（user stories）

As a [a user role], I want to [accomplish a result], So that [Business value].
作为[用户角色]，我想[实现一个结果]，因为[商业价值]。
![](agile2.png)



