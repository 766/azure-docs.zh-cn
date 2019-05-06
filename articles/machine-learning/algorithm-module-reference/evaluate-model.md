---
title: 评估模型：模块参考
titleSuffix: Azure Machine Learning service
description: 了解如何在 Azure 机器学习服务中使用评估模型模块来度量经过训练的模型的准确性。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 40a8247c22da1f7a057e222565ffb2ec4c6b7fb3
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028735"
---
# <a name="evaluate-model-module"></a>评估模型模块

本指南介绍了 Azure 机器学习服务的可视界面 （预览版） 的模块。

使用此模块来度量经过训练的模型的准确性。 提供一个包含从模型生成的评分数据集和**评估模型**模块可计算一组行业标准的评估指标。
  
 返回的度量值**评估模型**取决于您正在评估的模型的类型：  
  
-   **分类模型**    
-   **回归模型**    



> [!TIP]
> 如果您不熟悉模型评估，我们建议通过灾难恢复的视频系列。Stephen Elston，作为的一部分[机器学习课程](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/)EdX 中。 


有三种方法使用**评估模型**模块：

+ 通过将训练数据，生成评分和评估模型基于这些分数
+ 生成模型，评分但比较这些分数对得分的保留的测试集
+ 比较两个不同但相关的模型，使用相同的数据集的分数

## <a name="use-the-training-data"></a>使用定型数据

若要评估模型，必须连接包含一组输入的列和评分的数据集。  如果没有任何其他数据可用，可以使用原始数据集。

1. 连接**已评分数据集**的输出[评分模型](./score-model.md)到的输入**评估模型**。 
2. 单击**评估模型**模块，然后运行实验后，若要生成的评估分数。

## <a name="use-testing-data"></a>使用测试数据

机器学习中的常见方案是将原始数据集分成定型集和测试数据集，使用[拆分](./split-data.md)模块，或[分区和采样](./partition-and-sample.md)模块。 

1. 连接**已评分数据集**的输出[评分模型](score-model.md)到的输入**评估模型**。 
2. 连接包含到右侧的输入测试数据拆分数据模块的输出**评估模型**。
2. 单击**评估模型**模块，然后选择**运行所选**生成评估分数。

## <a name="compare-scores-from-two-models"></a>比较两个模型的分数

你还可以连接到评分的第二个集**评估模型**。  评分可能是具有已知的结果，一共享的评估组或一组来自相同数据的不同模型的结果。

此功能很有用，因为可以轻松地比较来自两个相同的数据的不同模型的结果。 或者，您可以通过使用不同的参数相同的数据比较两个不同运行的分数。

1. 连接**已评分数据集**的输出[评分模型](score-model.md)到的输入**评估模型**。 
2. 第二个模型的评分模型模块的输出连接到右侧的输入**评估模型**。
3. 右键单击**评估模型**，然后选择**运行所选**生成评估分数。

## <a name="results"></a>结果

运行后**评估模型**，右键单击模块并选择**评估结果**以查看结果。 可以：

+ 将结果保存为数据集，更易于与其他工具进行分析
+ 在界面中生成可视化效果

如果您连接到的两个输入数据集**评估模型**，结果将包含这两个组的数据或这两种模型的指标。
模型或数据附加到左侧端口首先会出现在报表中，数据集、 度量值后跟或附加正确的端口上的模型中。  

例如下, 图表示比较结果相同的数据，但使用不同的参数生成的两个聚类分析模型中。  

![AML&#95;Comparing2Models](media/module/aml-comparing2models.png "AML_Comparing2Models")  

由于这是聚类分析模型，评估结果将不同于比较两个回归模型的分数是否比较两个分类模型。 但是，整个演示文稿是相同的。 

## <a name="metrics"></a>度量值

本部分介绍了为特定类型的支持与一起使用的模型返回的指标**评估模型**:

+ [分类模型](#bkmk_classification)
+ [回归模型](#bkmk_regression)

###  <a name="bkmk_classification"></a> 分类模型的指标

评估分类模型时，将报告以下度量值。 如果您比较模型，优先级由你选择用于评估的指标。  
  
-   **准确性**测量分类模型的好坏作为事例总计指 true 结果的比例。  
  
-   **精度**占所有阳性结果是 true 的结果的比例。  
  
-   **回想一下**是由模型返回的所有正确结果的小部分。  
  
-   **F 分数**计算为精度和召回率介于 0 和 1，其中的理想之选 F 评分值是 1 之间的加权平均值。  
  
-   **AUC**曲线下的面积绘制与度量值真正在 y 轴和假正 x 轴上。 此指标很有用，因为它提供了可用于比较不同类型的模型的单个数字。  
  
- **平均对数损失**是用来表示对结果错误处罚的单个评分。 它将计算为两个概率分布 – 一个值为 true，而是在模型之间的差异。  
  
- **训练对数损失**是一个单一的分数，表示随机预测相比，分类器的优势。 对数损失通过比较其输出到标签中的已知值 （地面点真实成分） 的概率来衡量您的模型的不确定性。 你想要将模型作为一个整体的日志丢失降到最低。

##  <a name="bkmk_regression"></a> 回归模型的指标
 
返回的回归模型的度量值通常用于估计的错误。  考虑使用模型来容纳这些数据，如果观察到与预测值之间的差异很小。 但是，查看对剩余的模式 （任何预测的点和及其对应的实际值之间的差异） 可以提供许多有关模型中的潜在偏差。  
  
 以下指标报告评估回归模型。 当比较模型时，它们按按选择的计算度量值。  
  
- **平均绝对误差 (MAE)** 测量实际结果的接近程度的预测不; 因此，较低评分是更好。  
  
- **均方根误差 (RMSE)** 创建单个值，其中总结了在模型中的错误。 通过求平方差异，该指标不考虑过度预测和未得到充分预测之间的差异。  
  
- **相对绝对误差 (RAE)** 是预期和实际值; 之间的相对绝对差异相对因为均数差除以算术平均值。  
  
- **相对平方误差 (RSE)** 同样对预测值的总平方的误差除以总平方误差的实际值进行规范化。  
  
- **表示一个错误零 (MZOE)** 指示预测是否已正确。  即：`ZeroOneLoss(x,y) = 1`时`x!=y`; 否则为`0`。
  
- **决定系数**，通常称为 R<sup>2</sup>，表示为介于 0 和 1 之间的值的模型的预测能力。 零表示该模型是随机的 （解释了执行任何操作）;1 意味着是最佳选择。 但是，应谨慎使用在解释 R<sup>2</sup>值，如低值可以是完全正常和较高的值可以是可疑。
  

## <a name="next-steps"></a>后续步骤

请参阅[可用的模块集](module-reference.md)到 Azure 机器学习服务。 