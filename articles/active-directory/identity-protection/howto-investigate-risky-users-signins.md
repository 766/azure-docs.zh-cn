---
title: 如何在 Azure Active Directory 标识保护（已刷新）中调查有风险用户和登录| Microsoft Docs
description: 了解如何在 Azure Active Directory 标识保护（已刷新）中调查有风险用户和登录。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c89658e962654f005eaee5ceff220d5fb343e86e
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68370326"
---
# <a name="how-to-investigate-risky-users-and-sign-ins"></a>如何：调查有风险的用户和登录 

使用有风险的登录和有风险的用户报告, 你可以调查并深入了解你的环境中的风险。 通过对有风险登录和用户进行筛选和排序的功能，可以更好地了解组织中的潜在入侵情况。 

## <a name="risky-users-report"></a>“有风险用户”报告

通过“有风险用户”报告提供的信息，可以找到下面这些问题的答案：

- 哪些用户的风险高？
- 哪些用户的风险状态为“已修正”？

此报告的第一个入口点是“安全性”页上的“调查”部分。

![“有风险用户”报告](./media/howto-investigate-risky-users-signins/01.png)

“有风险用户”报告的默认视图显示以下信息：

- 名称
- 风险状态
- 风险级别
- 风险详细信息
- 风险上次更新时间
- type
- 状态

![“有风险用户”报告](./media/howto-investigate-risky-users-signins/03.png)

单击工具栏中的“列”即可自定义列表视图。

![“有风险用户”报告](./media/howto-investigate-risky-users-signins/04.png)

使用“列”对话框，可以显示其他字段或删除已显示的字段。

通过单击列表视图中的项，可以在水平视图中获得有关该项的所有可用详细信息。

![“有风险用户”报告](./media/howto-investigate-risky-users-signins/05.png)

“详细信息”视图显示：

- 基本信息
- 最新有风险登录
- 未链接到登录的风险事件
- 风险历史记录

除此之外，还可以：

![“有风险用户”报告](./media/howto-investigate-risky-users-signins/08.png)

- 查看所有登录快捷方式，以查看相应用户的登录报告。
- 查看所有有风险登录，以查看相应用户所有被标记为有风险的登录。
- 重置用户密码，如果你认为用户身份已被盗用的话。
- 消除用户风险，如果你认为用户的活动风险事件是误报的话。 有关详细信息, 请参阅文章[Azure AD Identity Protection 中的风险事件提供反馈](howto-provide-risk-event-feedback.md)。

### <a name="filter-risky-users"></a>筛选有风险用户

若要将报告数据的范围缩小到适当级别，可使用下面的默认字段来筛选有风险用户数据：

- 名称
- 用户名
- 风险状态
- 风险级别
- 类型
- 状态

![“有风险用户”报告](./media/howto-investigate-risky-users-signins/06.png)

使用“名称”筛选器，可以指定你关注用户的用户名或用户主体名称 (UPN)。

使用“风险状态”筛选器，可以选择：

- 存在风险
- 已修正
- 已消除

使用“风险级别”筛选器，可以选择：

- 高
- 中型
- 低

使用“类型”筛选器，可以选择：

- 来宾
- 成员

使用“状态”筛选器，可以选择：

- 已删除
- 活跃

### <a name="download-risky-users-data"></a>下载有风险用户数据

如果要在 Azure 门户之外使用这些数据, 则可以下载有风险的用户数据。 单击 "下载" 将创建最新2500记录的 CSV 文件。 

![有风险的用户报告](./media/howto-investigate-risky-users-signins/07.png)

单击工具栏中的“列”可以自定义列表视图。
 
用于显示其他字段，或者删除已显示的字段。
 
若要详细了解有风险用户，请单击“详细信息”导航抽屉以展开它

## <a name="risky-sign-ins-report"></a>风险登录报告

通过“有风险登录”报告提供的信息，可以找到下面这些问题的答案：

- 上周有多少次成功登录有匿名 IP 地址风险事件？
- 已确认哪些用户在上个月遭入侵？
- 哪些用户的 Office 365 门户登录有风险？

此报告的第一个入口点是“安全性”页上的“调查”部分。

![风险登录报告](./media/howto-investigate-risky-users-signins/02.png)

“有风险登录”报告的默认视图显示以下信息：

- Date
- 用户
- 应用程序
- 登录状态
- 风险状态
- 风险级别(总计)
- 风险级别(实时)
- 条件访问
- 需要 MFA  

![风险登录报告](./media/howto-investigate-risky-users-signins/09.png)

单击工具栏中的“列”即可自定义列表视图。

![“有风险用户”报告](./media/howto-investigate-risky-users-signins/11.png)

使用“列”对话框，可以显示其他字段或删除已显示的字段。

通过单击列表视图中的项，可以在水平视图中获得有关该项的所有可用详细信息。

![“有风险用户”报告](./media/howto-investigate-risky-users-signins/12.png)

“详细信息”视图显示：

- 基本信息
- 设备信息
- 风险信息
- MFA 信息
- 条件访问

除此之外，还可以：

![“有风险用户”报告](./media/howto-investigate-risky-users-signins/13.png)

- 确认是否已遭入侵 
- 确认安全

有关详细信息, 请参阅文章[Azure AD Identity Protection 中的风险事件提供反馈](howto-provide-risk-event-feedback.md)。

### <a name="filter-risky-sign-ins"></a>筛选有风险登录

若要将报告数据的范围缩小到适当级别，可使用下面的默认字段来筛选有风险用户数据：

- 用户
- 应用程序
- 登录状态
- 风险状态
- 风险级别(总计)
- 风险级别(实时)
- 条件访问
- Date
- 风险级别类型

![风险登录报告](./media/howto-investigate-risky-users-signins/14.png)

使用“名称”筛选器，可以指定你关注用户的用户名或用户主体名称 (UPN)。

使用“应用”筛选器，可以指定用户已尝试访问的云应用。

“登录状态”筛选器用于选择：

- 全部
- Success
- 失败

使用“风险状态”筛选器，可以选择：

- 存在风险
- 已确认遭入侵
- 已确认安全
- 已消除
- 已修正

使用“风险级别(总计)”筛选器，可以选择：

- 高
- 中型
- 低

使用“风险级别(实时)”筛选器，可以选择：

- 高
- 中型
- 低

使用 "**条件性访问**" 筛选器可以选择:

- 全部
- 未应用
- Success
- 失败

“日期”筛选器用于定义已返回数据的时间范围。
可能的值包括：

- 过去 1 个月
- 最近 7 天
- 前 24 小时
- 自定义时间范围

### <a name="download-risky-sign-ins-data"></a>下载有风险登录数据

如果要在 Azure 门户之外使用这些数据, 可以下载有风险的登录数据。 单击 "下载" 将创建最新2500记录的 CSV 文件。 

![有风险的用户报告](./media/howto-investigate-risky-users-signins/15.png)

## <a name="next-steps"></a>后续步骤

若要获取“Azure AD 标识保护”的概述，请参阅 [Azure AD 标识保护概述](overview-v2.md)。
