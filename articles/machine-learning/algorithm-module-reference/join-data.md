---
title: 联接数据:模块参考
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 机器学习服务中的联接联接数据模块来合并数据集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterlu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 1022bdc26a340b6b54ad840d1fe47674509fa865
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871653"
---
# <a name="join-data"></a>联接数据

本文介绍如何使用 Azure 机器学习服务视觉对象界面中的**联接数据**模块, 通过数据库样式的联接操作来合并两个数据集。  

## <a name="how-to-configure-join-data"></a>如何配置联接数据

若要在两个数据集上执行联接, 应按键列进行关联。 还支持使用多个列的组合键。 

1. 添加要合并的数据集, 然后将**联接数据**模块拖动到试验中。 

    您可以在 "**操作**" 下的 "**数据转换**" 类别中找到该模块。

1. 将数据集连接到**联接数据**模块。 
 
1. 选择 "**启动列选择器**" 以选择键列。 请记得为左侧输入和右侧输入选择列。

    对于单个密钥:

    为两个输入选择一个键列。
    
    对于组合键:

    按相同顺序从左输入和右输入中选择所有键列。 **联接数据**模块将在所有键列匹配时联接表。 如果列顺序与原始表不同, 则选中 "**允许重复项并保留列顺序**" 选项。 

    ![列选择器](media/module/join-data-column-selector.png)


1. 如果要在文本列联接上保留区分大小写选项, 请选择 "区分**大小写**" 选项。 
   
1. 使用 "**联接类型**" 下拉列表指定应如何合并数据集。  
  
    * **内部联接**:*内部联接*是最常见的联接操作。 仅当键列的值匹配时, 它才返回组合的行。  
  
    * **左外部联接**:*左外部联接*返回左表中所有行的联接行。 如果左侧表中的某行在右表中没有匹配的行, 则返回的行包含右侧表中所有列的缺失值。 还可以为缺失值指定替换值。  
  
    * **完全外部联接**:*完全外部联接*返回左表 (**table1**) 和右表 (**table2**) 中的所有行。  
  
         对于两个表中与另一个表中没有匹配行的每个行, 结果包括一个包含缺失值的行。  
  
    * **左半部联接**:当键列的值匹配时,*左半部联接*仅返回左侧表中的值。  

1. 对于 "**在联接的表中保留右键列**" 选项:

    * 选择此选项可以查看两个输入表中的键。
    * 取消选择此项将只返回左侧输入中的键列。

1. 运行试验, 或选择 "联接数据" 模块, 并选择 "所选**运行**" 以执行联接。

1. 若要查看结果, 请右键单击**联接数据** > **结果数据集** > **可视化**。

## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习服务[的模块集](module-reference.md)。 