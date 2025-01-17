---
title: 提升的决策树回归：模块参考
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 机器学习服务中的提升决策树回归模块创建使用提升的回归树系综构成。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 67e54f10074ee566ce974dbd27485904bfe0a653
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65411553"
---
# <a name="boosted-decision-tree-regression-module"></a>提升的决策树回归模块

本指南介绍了 Azure 机器学习服务的可视界面 （预览版） 的模块。

使用此模块创建使用提升的回归树系综构成。 *提升*意味着每个树依赖于前面的树。 通过拟合它前面的树剩余学习算法。 因此，提升决策树系综中往往会提高准确性，但降低覆盖一些小小的风险。  
  
此回归方法是一种监督式的学习方法，并因此需要*标记为数据集*。 标签列必须包含数字值。  

> [!NOTE]
> 使用此模块只适用于使用数字变量的数据集。  

定义模型后，它使用定型[训练模型](./train-model.md)。

> [!TIP]
> 想要了解有关创建树的详细信息？ 训练模型后，右键单击的输出[训练模型](./train-model.md)模块，并选择**可视化**若要查看在每次迭代创建的树。 可以向下钻取到每个树的拆分，并查看每个节点的规则。  
  
## <a name="more-about-boosted-regression-trees"></a>有关提升的回归树的详细信息  

提升是创建系综模型以及装袋、 随机林等多种典型方法之一。  在 Azure 机器学习，提升的决策树使用 MART 渐进提升算法的有效实现。 渐进提升是机器学习技术对于回归问题。 它将构建以步进的方式，使用预定义的损失函数测量每个步骤中的错误和更正为其在接下来的每个回归树。 因此预测模型是实际的较弱预测模型的系综构成。  
  
在回归问题中，提升生成一系列采用逐步的方式中的树，然后选择使用任意可微损失函数的最佳树。  
  
有关其他信息，请参阅以下文章：  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    上渐进提升此维基百科文章提供了有关提升树的一些背景。 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research：从 RankNet 到 lambdarank 再到 LambdaMART:概述。 By J.C. Burges 撰写。

渐进提升法也可以对于分类问题划归到回归与适当的损失函数的使用。 有关用于分类任务的提升的树实现的详细信息，请参阅[双类提升决策树](./two-class-boosted-decision-tree.md)。  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>如何配置提升决策树回归

1.  添加**提升决策树**模块在试验。 您可以找到此模块下的**机器学习**，**初始化**下**回归**类别。 
  
2.  指定要通过设置训练的模型的方式**创建训练器模式**选项。  
  
    -   **单一参数**:如果你知道你想要配置模型，并提供一组特定的值作为参数，请选择此选项。  
   
  
3. **最大数量的每个树的叶**:指示可以在任何树中创建的终端节点 （叶） 的最大数量。  

    通过增加此值后，你可能会增加树的大小并获取更好的精度，存在过度拟合和训练时间延长的风险。  

4. **最小每个叶节点的样本数**:指示最小所需创建任何终端节点 （叶） 树中的事例数。

    通过增加此值后，可以增加创建新规则的阈值。 例如，默认值为 1，甚至单个案例可以导致要创建新规则。 如果您增加的值为 5，训练数据将必须包含至少 5 个满足相同条件的情况。

5. **学习速率**:键入一个介于 0 和 1，定义学习时的步骤大小。 学习率确定如何快速或慢速学习器收敛的最佳解决方案。 如果步骤大小太大，可能会超过最佳解决方案。 如果步骤大小太小，训练时间聚合上的最佳解决方案。

6. **构造的树数**:表示要在系综中创建的决策树的总数。 通过创建多个决策树，你可能获得更好的覆盖范围，但训练时间会增加。

    此值还可以控制当可视化训练的模型时，显示的树数。 如果你想要查看或打印 ingle 树，可以将值设置为 1;但是，生成只有一个树 （与初始参数集的树），并会执行任何进一步迭代。

7. **随机数种子**:键入要用作随机种子值的可选的非负整数。 指定种子确保在运行具有相同的数据和参数的可再现性。

    默认情况下，随机种子设置为 0，这意味着从系统时钟获取初始种子值。
  
8. **允许未知的分类级别**:选择此选项可创建为未知值组在训练和验证集。 如果取消选择此选项，该模型会接受训练数据中包含的值。 该模型可能不太精确的已知值，但它可以提供更好地预测新 （未知） 值。

9. 添加训练数据集和定型模块之一：

    - 如果您设置**创建训练器模式**选项设为**单个参数**，使用[训练模型](train-model.md)模块。  
  
    

10. 运行试验。  
  
### <a name="results"></a>结果

培训完成后：

+ 若要查看在每次迭代创建的树，右键单击的输出[训练模型](train-model.md)模块，并选择**可视化**。
  
     单击每个树向下钻取到拆分，并查看每个节点的规则。  

+ 若要使用该模型进行评分，将其连接到[评分模型](./score-model.md)，以预测新的输入示例的值。

+ 若要保存的训练的模型快照，请右键单击**Trained 模型**输出的培训模块，并选择**另存为**。 连续运行试验时不更新保存的已训练模型的副本。

## <a name="next-steps"></a>后续步骤

请参阅[可用的模块集](module-reference.md)到 Azure 机器学习服务。 