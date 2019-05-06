---
title: 道德和负责任的使用 - 个性化体验创建服务
titleSuffix: Azure Cognitive Services
description: 这些指南旨在帮助你以特定方式实现个性化，而该方式有助于你培养他人对你的公司和服务的信任。 请务必抽些空出来，研究、了解并仔细考虑一下个性化对人们生活的影响。 如有疑问，请寻求指导。
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 7b1e972b5516aa79d1754e32e487e17c9e68ac1d
ms.sourcegitcommit: eea74d11a6d6ea6d187e90e368e70e46b76cd2aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2019
ms.locfileid: "65035421"
---
# <a name="guidelines-for-responsible-implementation-of-personalizer"></a>有关如何以负责任的方式实现个性化体验创建服务的指南

若要让人和社会认识到 AI 的全部潜力，需以特定方式对实现进行设计，使之赢得那些将 AI 添加到其应用程序中的人们的信任，以及使用 AI 构建的应用程序的用户的信任。 这些指南旨在帮助你以特定方式实现个性化体验创建服务，而该方式有助于你培养他人对你的公司和服务的信任。 请务必抽些空出来，研究、了解并仔细考虑一下个性化对人们生活的影响。 如有疑问，请寻求指导。

不应将这些准则当作法律建议来看待，而应单独确保应用程序符合此区域以及你所在行业快速发展的法律的要求。

另外，在使用个性化体验创建服务来设计应用程序时，应考虑在开发任何以数据为中心的 AI 系统时需承担的大量责任，包括道德、隐私、安全性、安全、包容性、透明度和问责制。 可以在[推荐阅读](#recommended-reading)部分阅读有关这些内容的详细信息。

可以使用以下内容作为入门清单，并根据自己的方案对其进行自定义和优化。 本文档有两个主要部分：第一部分专门介绍在针对个性化体验创建服务选择方案、特征和奖励时，如何才能进行负责任的使用。 第二部分介绍一系列的价值。Microsoft 认为在在构建 AI 系统时应该考虑到这些价值，并提供可行的建议和应该避免的风险，说明你在使用个性化体验创建服务时会对它们造成什么影响。 


## <a name="your-responsibility"></a>你的责任

有关负责任的实现的所有准则都建立在特定的基础之上，即，使用个性化体验创建服务的开发人员和企业有责任确保在社会中使用这些算法的效果。 如果你开发供自己的组织部署的应用程序，则应认识到自己在确保其正常运行和对人们的良好影响方面所担负的角色和责任。 如果你要设计供第三方部署的应用程序，则应与他们达成共识，明确谁应该对应用程序的行为担负最终责任，并将这种共识记录在案。

信任是建立在相信对方能够履行承诺的基础之上的 - 要考虑到你的用户、社会以及规范应用程序运行的法律框架，以便确定对方需要履行的明示和默示的承诺。

Microsoft 始终致力于优化其工具和文档，帮助你履行这些责任。 如果你相信额外的工具、产品功能和文档有助于你遵循这些有关如何使用个性化体验创建服务的准则，请[向 Microsoft 提供反馈](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D)。


## <a name="factors-for-responsibly-implementing-personalizer"></a>影响你以负责任的方式实现个性化体验创建服务的因素

实现个性化体验创建服务对你的用户和企业可能有极大的价值。 若要以负责任的方式实现个性化体验创建服务，首先要考虑以下准则：

* 选择可应用个性化的用例。
* 构建[奖励函数](https://github.com/Azure/personalization-rl/blob/master/docs/concepts-rewards.md)。
* 选择将哪些与上下文和可能的操作有关的[特征](https://github.com/Azure/personalization-rl/blob/master/docs/concepts-features.md)用于个性化。


## <a name="choosing-use-cases-for-personalizer"></a>选择个性化体验创建服务的用例

可以使用一项学习如何将内容和用户界面个性化的服务。 它也可能会被误用，例如，个性化的方式在实际生活中产生负面效果，包括用户在不了解内容个性化的情况下造成的负面影响。 

在特定情况下使用个性化体验创建服务时，会提高造成负面影响或缺乏透明性的可能性，例如，“奖励”取决于许多长期的复杂因素，如果将这些因素过度简化成一种即时奖励，可能会为个人带来不希望的结果。 通常会将这些视为“间接损害”选择或涉及损害风险的选择。 例如： 


* **财务**：基于贷款、金融和保险产品的个性化套餐，其中的风险因素取决于个人用户所不知道的、无法获取的或无法争辩的数据。 
* **教育**：针对学校课程和教育机构的个性化排名，所提供的建议可能带有偏见，导致用户不了解其他选择。
* **民主和公民参与**：为用户提供个性化内容，目的是影响用户的政治观点，这种行为具有间接损害，属于操纵性行为。
* **第三方奖励评估**：个性化项目的奖励基于第三方以后对该用户的评估，并非根据用户本身的行为进行奖励。
* **不允许探索**：对个性化体验创建服务进行探索会导致损害的任何情形。

选择个性化体验创建服务的用例时，请考虑以下因素：

* 在启动设计过程的时候，需考虑到个性化对用户的帮助情况。
* 如果因为个性化模式或探索原因而不针对用户进行某些项目的排名，要考虑到在实际生活中产生的消极后果。
* 考虑到自证预言循环。 如果通过个性化奖励训练一种模型，使之在以后进一步阻止某个人群访问相关内容，则可能会发生这种情况。 例如，低收入居民区的大多数人不会获得高级保险套餐，因此慢慢地该居民区中的所有人就根本看不到该套餐了。
* 请保存模型和学习策略的副本，因为将来可能需要重新生成个性化体验创建服务。 可以定期这样做，也可以每隔一个模型刷新周期就这样做一次。
* 考虑到探索级别要适合相应的空间，以及如何将它作为工具来使用，以减轻“回音室”效应。


## <a name="selecting-features-for-personalizer"></a>为个性化体验创建服务选择特征

个性化内容基于这样一个理念：提供与内容和用户相关的有用信息。 请注意，就某些应用程序和行业来说，某些用户特征可能会被直接或间接地视为带有歧视的特征，因此可能是非法的。

考虑使用这些特征的后果：

* **用户人口统计数据**：与性、性别、年龄、种族、宗教有关的特征。由于法规方面的原因，可能不允许在某些应用程序中使用这些特征。而且，对其进行个性化操作可能是不道德的，因为个性化会传播泛化论和偏见。 关于上面所说的传播偏见的行为，一个例子是在发布工程方面的工作招聘广告时，不向老年人或特定性别的受众显示该广告。
* **区域设置信息**：在世界上的许多地区，位置信息（例如邮政编码、邮递区号或居民区名称）可能与收入、种族和宗教高度相关。
* **用户对公平的理解**：即使应用程序所做决定是公正的，也要考虑到这样一种后果：用户对应用程序中显示的相应内容的看法会改变，而这种改变似乎与那些会被视为带有歧视性的特征相关联。
* **特征中存在的意外偏见**：如果所使用的特征仅影响部分人群，则可能引入某些类型的偏见。 如果特征是通过算法生成的（例如，使用图像分析提取图片中的项目，或通过文本分析发现文本中的实体），则需特别注意这一点。 让自己注意那些用于创建这些特征的服务的特征。

在选择需要在上下文和操作中发送给个性化体验创建服务的特征时，请应用以下做法：

* 考虑将某些特性用于某些应用程序时的合法性和道德问题，以及那些外观没有恶意的特性是否有着你想要避免或者应该避免的涵义。
* 明确告知用户，在对他们看到的选项进行个性化设置时会使用算法和数据分析。
* 问问你自己：如果我使用此信息对用户的内容进行个性化设置，他们会在意吗？会高兴吗？ 如果向他们展示突出显示或隐藏某些项目的具体决策过程，我会乐意吗？
* 使用行为数据而不是基于其他特征的分类或分段数据。 因为历史原因，人口统计信息传统上可供零售商用于商用目的（人口统计属性收集起来和操作起来似乎很简单，直到数字时代的到来改变了这一切）。但是，如果你有实际的交互、上下文和历史数据，而这些数据与用户的偏好和身份更相关，那么人口统计信息的相关性又如何呢？这是令人存疑的。
* 考虑如何防止恶意用户“欺骗性”地使用相关特征，这些特征在被大规模利用的情况下可能导致系统以误导方式训练个性化体验创建服务，让其特意干扰、妨碍和骚扰某些类型的用户。 
* 在适当且可行的情况下，请在设计应用程序时考虑允许用户选择使用或不使用某些个人特征。 这些个人特征信息可以分组，例如“位置信息”、“设备信息”、“过去的购买历史记录”，等等。


## <a name="computing-rewards-for-personalizer"></a>计算个性化体验创建服务的奖励

个性化体验创建服务的目标是根据应用程序业务逻辑提供的奖励分数，改进对需要奖励的具体操作的选择方式。

适当的奖励分数可以充当实现业务目标所需的短期跳板，而业务目标则与组织的使命密不可分。

例如，如果对点击率进行奖励，则会导致个性化体验创建服务寻求提高点击率而牺牲其他所有目标，即使所点击的东西只会导致用户分神或者与业务成果不相关。

与之相反，新站点可能需要将奖励与比点击率更有意义的事项挂钩，例如，“用户是否花了足够的时间来阅读内容？”、 “他们点击了相关的文章或参考资料了吗？”。 使用个性化体验创建服务可以轻松地将指标与奖励紧密联系在一起。 但是请注意，不要将用户的短期参与与良好的业务成果相混淆。

### <a name="unintended-consequences-from-reward-scores"></a>因奖励分数引发的意外结果
设立奖励分数的用心是良好的，但在通过个性化体验创建服务对内容进行排名时，仍可能产生意外的结果。 

请考虑以下示例：

* 如果按照已观看视频长度占整个视频长度的百分比来奖励个性化创作的视频内容，则可能导致短视频在排名时占优。
* 如果对社交媒体共享数目进行奖励，而不对共享方式或内容本身进行情绪分析，则可能导致冒犯性的、未受监管的或煽动性的内容在排名时占优。这些内容会引发用户大量的“参与”，但并没有什么价值。
* 如果对用户界面元素上的操作进行奖励，而用户并不期望更改这些元素，则可能导致按钮在没有警告的情况下意外更改位置或用途，影响用户界面的可用性和可预测性，使得某些用户群难以始终高效工作。

实施以下最佳做法：

* 使用不同的奖励方法对系统进行脱机试验，了解相关的影响和副作用。
* 对奖励函数进行评估，问自己这样一个问题：假如一个非常幼稚的人出现了理解偏差，那么在使用过程中会出现什么样的不良后果？


## <a name="responsible-design-considerations"></a>进行负责任的设计时的注意事项

下面是以负责任的方式实现 AI 时需要关注的设计领域。 请在 [The Future Computed](https://news.microsoft.com/futurecomputed/)《未来计算》中详细了解此框架。

![AI 在未来计算中的价值](media/ethics-and-responsible-use/ai-values-future-computed.png)

### <a name="accountability"></a>问责制
设计和部署 AI 系统的人必须对其系统的运行负责。 

* 就个性化体验创建服务和文档的实现方式制定内部准则，并将其传达给团队、高管和供应商。
* 定期审核奖励分数的计算方式，通过脱机评估了解哪些特征影响个性化体验创建服务，并根据结果消除不需要的和不必要的特征。
* 向用户清楚地说明个性化体验创建服务的使用方式、使用目的和使用数据。
* 存档供个性化体验创建服务正常运行以生成结果所需使用的信息和资产，例如模型、学习策略以及其他数据。

### <a name="transparency"></a>透明度
AI 系统应该是允许用户了解的。 使用个性化体验创建服务时，请确保：

•   告知用户内容是如何个性化的。 例如，可以向用户显示一个标记为“为何提供这些建议?”的按钮， 说明用户和操作的哪些排名靠前的特征在个性化体验创建服务的结果中起着重要作用。
•   确保使用条款提到你会使用用户及其行为的相关信息对体验进行个性化设置。


* 告知用户内容是如何个性化的。 例如，可以向用户显示一个标记为 `Why These Suggestions?` 的按钮，说明用户和操作的哪些排名靠前的特征在个性化中起着重要作用。
* 确保使用条款提到你会使用用户的相关信息对体验进行个性化设置。

### <a name="fairness"></a>公平
*AI 系统应该公平地对待所有人。

* 请勿将个性化体验创建服务用于其结果会带来长期损害、间接损害或涉及真正伤害的用例。
* 请勿使用不适合对内容进行个性化设置的特征，或者可能有助于传播不受欢迎的偏见的特征。 例如，就金融产品来说，应该为财务状况类似的所有人提供相同的个性化建议。
* 了解可能存在于多种特征中的偏见，这些特征可能由编辑器、算法工具或用户自己提供。

### <a name="reliability-and-safety"></a>可靠性和安全性
AI 系统应该能够可靠且安全地运行。 对于个性化体验创建服务，请注意以下事项：

* 请勿向个性化体验创建服务提供不应选择的操作。 例如，如果为匿名用户或未成年用户提供观影建议，请应从需要进行个性化设置的操作中筛选掉不适当的影片。
* 将个性化体验创建服务模型作为业务资产来管理。  考虑一下，在完成个性化体验创建服务循环以后，每隔多久就需要保存并备份一次模型和学习策略，而在其他情况下则将其作为重要的业务资产看待。 重现过去的结果对于自我审核和衡量改进情况很重要。
* 提供从用户获取直接反馈的渠道。 除了进行编码安全性检查以确保将相应的内容提供给相应的受众，还应提供一种反馈机制，方便用户报告那些可能会令人感到意外或厌烦的内容。 尤其是在内容来自用户或第三方的情况下，请考虑使用 Microsoft 内容审查器或其他工具来审核和验证内容。
* 进行频繁的脱机评估。 这样有助于监视趋势，确保了解有效性。
* 制定一个检测并处理恶意操纵行为的流程。 某些执行组件会利用机器学习和 AI 系统能够从环境中学习这一特点，将结果引向这些组件的目标。 如果你在使用个性化体验创建服务时希望其能够影响重要的抉择，请务必通过适当的手段来检测这些类型的攻击并减轻其后果，包括在适当的情况下进行人工审核。

### <a name="security-and-privacy"></a>安全性和隐私性
AI 系统应该既安全又尊重隐私。 使用个性化体验创建服务时，请遵守以下准则：

* 按照本地法规和行规的要求，预先告知用户要收集的数据以及数据的使用方式，事先征得用户同意。
* 提供隐私保护用户控件。 对于存储个人信息的应用程序，请考虑为相关功能提供易于查找的按钮，例如： 
   * `Show me all you know about me`    
   * `Forget my last interaction` 
   * `Delete all you know about me`

在某些情况下，根据法律的要求，可能必须这样做。 考虑一下，如果定期对模型重新进行训练，使之不包含已删除数据的跟踪，会有什么利弊。

### <a name="inclusiveness"></a>包容性
满足范围广泛的人类需求和体验。
* 为启用了辅助功能的界面提供个性化的体验。 良好的个性化（应用以后可以在交互时减少工作量、移动量和不必要的重复）所带来的效率可能对残障人士尤其有用。
* 根据上下文调整应用程序行为。 例如，可以通过个性化体验创建服务消除聊天机器人意向之间的歧义，因为正确的解释取决于上下文，不能一概而论。 


## <a name="proactive-readiness-for-increased-data-protection-and-governance"></a>主动进行准备，增强数据保护和治理

很难预测监管环境的具体变化，但通常情况下，应该摆脱遵循最小法律框架这样的思维，确保以尊重的方式使用个人数据，并在算法决策方面提供透明度和选择。


* 在某些情况下，从个人用户收集数据可能需遵循新的限制，并且需表明是如何使用数据进行决策的。对于这种情况，应考虑提前进行规划。
* 如果用户中可能包括被边缘化的弱势群体、儿童、经济窘迫用户或者在其他情况下易受算法操纵影响的用户，则应考虑进行额外的准备。
* 考虑到人们普遍对以受众为目标的以及影响受众的数据收集计划和算法的实施不满，应设法避免业已证明的策略性错误。


## <a name="proactive-assessments-during-your-project-lifecycle"></a>在项目生命周期内主动进行评估

考虑为团队成员、用户和业务所有者创建相关方法，用于报告与负责任的使用相关的疑虑；同时考虑制定一个流程以确定解决方法的优先级，防止报复。

任何人在考虑技术使用过程中造成的副作用时，都会囿于其自身的观察角度和生活体验。 为团队、用户或咨询委员会带来更多不一致的声音，集思广益，营造大胆说话的氛围。 考虑提供培训和学习资料，进一步扩大团队在此领域的知识，使大家具备讨论复杂、敏感主题的能力。

考虑将与负责任的使用相关的任务视为应用程序生命周期中的其他交叉任务，例如与用户体验、安全性或 DevOps 相关的任务。 对这些任务及其要求的考虑不能事后进行。 在整个应用程序生命周期中，都应讨论并验证负责任的使用这一准则。
 
## <a name="questions-and-feedback"></a>问题和反馈

Microsoft 始终致力于优化工具和文档，帮助你履行这些责任。 如果你相信额外的工具、产品功能和文档有助于你遵循这些有关如何使用个性化体验创建服务的准则，则请接受我们团队的邀请，[向 Microsoft 提供反馈](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D)。

## <a name="recommended-reading"></a>推荐阅读内容

* 请查看 Microsoft 的有关如何对 AI 进行负责任的开发的六项原则，这些原则发布在 2018 年 1 月份的 [The Future Computed](https://news.microsoft.com/futurecomputed/)《未来计算》一书中
* [Who Owns the Future?](https://www.goodreads.com/book/show/15802693-who-owns-the-future)《谁拥有未来？》，作者：Jaron Lanier
* [Weapons of Math Destruction](https://www.goodreads.com/book/show/28186015-weapons-of-math-destruction)《数学杀伤性武器》，作者：Cathy O'Neil
* [Ethics and Data Science](https://www.oreilly.com/library/view/ethics-and-data/9781492043898/)《道德和数据科学》，作者：DJ Patil、Hilary Mason、Mike Loukides
* [ACM Code of Ethics](https://www.acm.org/code-of-ethics)（ACM 道德准则）
* [Genetic Information Nondiscrimination Act - GINA](https://en.wikipedia.org/wiki/Genetic_Information_Nondiscrimination_Act)（反基因歧视法 - GINA）
* [FATML Principles for Accountable Algorithms](http://www.fatml.org/resources/principles-for-accountable-algorithms)（FATML 算法问责原则）


## <a name="next-steps"></a>后续步骤

[特征：操作和上下文](concepts-features.md)。