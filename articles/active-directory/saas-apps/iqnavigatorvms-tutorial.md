---
title: 教程：Azure Active Directory 与 IQNavigator VMS 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 IQNavigator VMS 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a8a09b25-dfa5-4c31-aea2-53bf1853b365
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: 579b35a06b74ffcef47ca0b41ff60bfdc4e0eb2b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67099786"
---
# <a name="tutorial-azure-active-directory-integration-with-iqnavigator-vms"></a>教程：Azure Active Directory 与 IQNavigator VMS 集成

本教程介绍如何将 IQNavigator VMS 与 Azure Active Directory (Azure AD) 集成。
将 IQNavigator VMS 与 Azure AD 集成具有以下优势：

* 可在 Azure AD 中控制谁有权访问 IQNavigator VMS。
* 可以让用户通过其 Azure AD 帐户自动登录到 IQNavigator VMS（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 IQNavigator VMS 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 IQNavigator VMS 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* IQNavigator VMS 支持 **IDP** 发起的 SSO

## <a name="adding-iqnavigator-vms-from-the-gallery"></a>从库添加 IQNavigator VMS

若要配置 IQNavigator VMS 与 Azure AD 的集成，需要从库中将 IQNavigator VMS 添加到托管 SaaS 应用列表。

若要从库中添加 IQNavigator VMS，请执行以下步骤： 

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入“IQNavigator VMS”，在结果面板中选择“IQNavigator VMS”，然后单击“添加”按钮添加该应用程序    。

     ![结果列表中的 IQNavigator VMS](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于一个名为“Britta Simon”的测试用户配置并测试 IQNavigator VMS 的 Azure AD 单一登录。 
若要使单一登录有效，需要在 Azure AD 用户与 IQNavigator VMS 相关用户之间建立链接关系。

若要配置和测试 IQNavigator VMS 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 IQNavigator VMS 单一登录](#configure-iqnavigator-vms-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 IQNavigator VMS 测试用户](#create-iqnavigator-vms-test-user)** - 在 IQNavigator VMS 中有一个与 Azure AD 中的 Britta Simon 相对应的关联用户。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 IQNavigator VMS 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“IQNavigator VMS”应用程序集成页上，选择“单一登录”。  

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![IQNavigator VMS 域和 URL 单一登录信息](common/idp-relay.png)

    a. 在“标识符”文本框中键入 URL：`iqn.com` 

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<subdomain>.iqnavigator.com/security/login?client_name=https://sts.window.net/<instance name>`

    c. 单击“设置其他 URL”  。

    d. 在“中继状态”文本框中，使用以下模式键入 URL：`https://<subdomain>.iqnavigator.com` 

    > [!NOTE]
    > 这些不是实际值。 使用实际回复 URL 和中继状态更新这些值。 请联系 [IQNavigator VMS 客户端支持团队](https://www.beeline.com/iqn-product-support/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. IQNavigator 应用程序希望在名称标识符声明中的用户标识符是唯一的。 用户可以为名称标识符声明映射正确的值。 在这种情况下，出于演示目的，我们映射了 user.UserPrincipalName。 但是你应该根据组织设置映射正确的值。

    ![image](common/edit-attribute.png)

6. 在“设置 SAML 单一登录”  页的“SAML 签名证书”  部分中，单击“复制”按钮，以复制“应用联合元数据 URL”  ，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

### <a name="configure-iqnavigator-vms-single-sign-on"></a>配置 IQNavigator VMS 单一登录

若要在“IQNavigator VMS”端配置单一登录，需将应用联合元数据 URL 发送给 [IQNavigator VMS 支持团队](https://www.beeline.com/iqn-product-support/)   。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”  字段中键入 brittasimon@yourcompanydomain.extension   
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 IQNavigator VMS 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“IQNavigator VMS”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“IQNavigator VMS”  。

    ![应用程序列表中的 IQNavigator VMS 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-iqnavigator-vms-test-user"></a>创建 IQNavigator VMS 测试用户

在本部分，将在 IQNavigator VMS 中创建一个名为 Britta Simon 的用户。 请与  [IQNavigator VMS 支持团队](https://www.beeline.com/iqn-product-support/)协作，将用户添加到 IQNavigator VMS 平台中。 使用单一登录前，必须先创建并激活用户。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 IQNavigator VMS 磁贴时，应会自动登录到为其设置了 SSO 的 IQNavigator VMS。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)