---
title: 数据探索和可视化工具 - Azure | Microsoft Docs
description: 数据科学虚拟机的数据浏览和可视化工具。
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: vijetaj
ms.openlocfilehash: 31b05ec4fa68c3d4804000caee94b62432bdaed9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68557765"
---
# <a name="data-exploration-and-visualization-tools-on-the-data-science-virtual-machine"></a>数据科学虚拟机上的数据浏览和可视化工具

数据科学的关键步骤之一是了解数据。 可视化和数据浏览工具可帮助加速数据理解。 下面是 DSVM 上提供的一些工具, 这些工具使此密钥步骤更容易。 

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| 这是什么?   | 开源大数据 SQL 查询引擎    |
| 支持的 DSVM 版本      | Windows、Linux  |
| 如何在 DSVM 上配置/安装它？      |  仅以嵌入模式安装在 `/dsvm/tools/drill*` 中   |
| 典型用途      |  无需 ETL 就地浏览数据。 查询不同的数据源和格式，包括 CSV、JSON、关系表、Hadoop     |
| 如何使用/运行它？      | 桌面快捷方式  <br/> [10 分钟后即可开始钻取](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| DSVM 上的相关工具      |   Rattle、Weka、SQL Server Management Studio      |

## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| 这是什么?   |  Weka 是适用于数据挖掘任务的机器学习算法的集合。 这些算法可以直接应用于数据集，也可以从你自己的 Java 代码中调用。 Weka 包含用于数据预处理、分类、回归、群集、关联规则和可视化的工具。 |
| 支持的 DSVM 版本     | Windows、Linux     |
| 典型用途      | 常规 ML 工具     |
| 如何使用/运行它？      | 在 Windows 上，在“开始”菜单中搜索 Weka。 在 Linux 上, 用 X2Go 登录, 然后导航到应用程序-> 开发-> Weka。 |
| 示例链接      | [Weka 示例](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| DSVM 上的相关工具      |LightGBM、Rattle、Xgboost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| 这是什么?   |   一种使用 R 编写的用于数据挖掘的图形用户界面   |
| 支持的 DSVM 版本     | Windows、Linux     |
| 典型用途      | R 适用的常规 UI 数据挖掘工具    |
| 如何使用/运行它？      | UI 工具。 在 Windows 上，启动命令提示符，运行 R，然后在 R 中运行 `rattle()`。 在 Linux 上，使用 X2Go 连接，启动终端，运行 R，然后在 R 中运行 `rattle()`。 |
| 示例链接      | [Rattle](https://togaware.com/onepager/) |
| DSVM 上的相关工具      |LightGBM、Weka、Xgboost   |

## <a name="power-bi-desktop"></a>Power BI Desktop 
|    |           |
| ------------- | ------------- |
| 这是什么?   | 交互式数据可视化和 BI 工具    |
| 支持的 DSVM 版本      | Windows  |
| 典型用途      |  数据可视化和构建仪表板   |
| 如何使用/运行它？      | 桌面快捷方式 (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| DSVM 上的相关工具      |   Visual Studio 2019, Visual Studio Code, Juno      |

