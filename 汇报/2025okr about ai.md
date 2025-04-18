| 关键结果               | **关键结果权重** | **进度** |      |
| :--------------------- | :--------------- | :------- | ---- |
| 描述目标 目标权重:60%  |                  |          |      |
|                        | 30%              |          |      |
|                        | 10%              |          |      |
| 描述目标 目标权重: 30% |                  |          |      |
|                        |                  |          |      |
|                        |                  |          |      |
| 描述目标 目标权重: 10% |                  |          |      |
|                        |                  |          |      |
|                        |                  |          |      |



# OKR的分解

先建立o

然后基于o，把挑战写出来

预计挑战再写kr

比如：

o：肚子饿，想吃饱饭

错误的挑战：我没有吃饭，想吃饭

挑战：没有钱，想赚钱；

kr：如何去赚钱？



# 观摩别人的okr讲解

AI找bug，还有没有更多AI的用法：数据库，质量，性能相关的判断

知识体系的构建

投喂自己的ai知识库

发自内心愿意做的事情，认为自己正确的事情，

我的AI复现的大目标是什么？仿真复现的大目标？



### 0219 review信息

3月中旬，开CSIG总监层面的组织架构会，需要拥抱AI

ai做日志分析的KM https://km.woa.com/articles/view/621752

想o的时候，一步步往上想，往前端想。

当你要做职称评估的时候，如何用AI放大你的成果，不然凭什么可以升上去

责任与目的：把问题单的定位清晰，才能加速tapd单关闭。



ai的回复留给备份文件，然后使用更专业的prompt

我们并没有足够的时间去阅读代码，需要使用ai工蜂协助解读代码

如何追问？让ai给出更加可信的测试方案与复现方案

​	1.他的信息不足，只有公开信息，还是需要输入知识库，可是如何增加自建知识库到ds中

​	可以利用 **基于 RAG 的问答**（Retrieval-Augmented Generation）是一种结合**检索（Retrieval）**和**生成（Generation）**的混合式人工智能技术，旨在通过动态利用外部知识库生成更准确、可靠的答案。它是当前大语言模型（如 GPT）的重要增强方法，尤其适用于需要结合实时数据或专业领域知识的场景。

​	2.一切问题，都可以使用AI



1. 汇总出优秀的追问prompt

2. 当我追问了很多问题，ai的回复已经达到了我想要的结果，我该怎么把ai的这个状态保存下来



关于指标问题，以及AI分析数据库指标

​	目前已经在使用ansible收集日志信息

​	难点：波动分析怎么办？





关于日志的保留，可是就是刷的很快。



做测试的三张表

对一个问题的多方位分析？



# AI的一些分析

## o1 基于ai分析出来的代码差异，**识别未合入的社区 Bug，复现并推动代码健壮性提升。**

with 策天



### **挑战1：Bug 复现环境复杂**

通过 AI 生成测试用例（如模糊测试）。

**日志增强**：在复现流程中植入 AI 驱动的异常检测（如时序日志分析）。



具体的挑战

1. mysql orcale bug id和mysql 社区id 已经是两个分支，无法快速关联，并且mysql orcale bug id 仅仅披露最小化的bug描述和修复信息，没有详细的bug复现步骤和导致bug的原因。

   解决：mysql orcale bug 是可以在其他社区找到一些参考，需要对复现bug

   提取 MySQL Bug 报告中的核心问题描述（如错误行为、触发条件、堆栈跟踪等），在 Oracle 的 Bug 数据库（如 Oracle MOS）或社区（如 MySQL Community Bug Database）中搜索相似关键词。

   

1. 没有快速参考的复现方法，只能考虑从bug描述和代码修改中反推复现手段。

   解决：1. 将描述与代码喂给ai，结合自己的理解进行复现，重点关注借用ds版本的copilot

   需要精准测试，偶现的才需要

   缺点：无法验证反推出来的复现手段是否真的走到了被修改的代码。

   ​	解决：结合db白盒与流水线出包，对输出log进行确认？
   
   产出1：优秀的复现开源问题的prompt
   
   困难:基于开源bug信息的数据还是有点少
   
   ​	解决：开通mos账号

​		困难：海外信用卡

​			解决：找kk

流水线出包-是否可以拉个分支，











###  挑战2：**推动内部合入阻力大**

**数据驱动说服**：用 AI 统计未合入 Bug 的潜在影响（如安全风险、性能损失），生成可视化报告

 **渐进式合入**：优先合入高风险 Bug，提供 A/B 测试对比数据。





3. 





### 解决

**测试复现**：SQLsmith（自动生成 SQL 测试）、LLM 驱动的模糊测试











## O2  **基于 AI 提升数据库代码质量，推动高效性能测试，或构建自有数据库 AI。**

思想：任何遇到困难的地方，都可以结合ai



### 挑战1:**性能测试用例覆盖不足**

**AI 生成测试**：用强化学习生成边界场景测试（如高并发、大数据量），补足人工设计

**动态反馈**：基于测试结果迭代优化模型，聚焦薄弱环节（如锁竞争、IO 瓶颈）。

### 挑战2:**测试结果分析依赖人工，效率低**

ai对性能测试的结果进行快速判断，训练模型从测试日志中提取关键指标（如 QPS、锁等待时间）



### 挑战3: **缺乏持续性能监控机制**

在流水线中嵌入自动化性能测试，设置阈值触发告警（如 TPS 下降 10% 则阻断发布）

有没有机会创建出一些能放入ci/cd中的性能用例



### 挑战4:**增强我们的话语声**

**成本关联**：量化性能问题对业务的影响（如延迟导致的用户流失率、硬件成本浪费



















