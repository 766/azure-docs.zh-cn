---
title: 教程：Azure Active Directory 与 Workteam 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Workteam 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 41df17a1-ba69-414f-8ec3-11079b030df6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: d628b12171cd67455ea308a2ca2b29e67855cccc
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68825611"
---
# <a name="tutorial-azure-active-directory-integration-with-workteam"></a>教程：Azure Active Directory 与 Workteam 集成

本教程介绍如何将 Workteam 与 Azure Active Directory (Azure AD) 集成。
将 Workteam 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Workteam。
* 可以让用户使用其 Azure AD 帐户自动登录到 Workteam（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Workteam 的集成，需要准备好以下各项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了 Workteam 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Workteam 支持启用了 SP 和 IDP 的 SSO  

## <a name="adding-workteam-from-the-gallery"></a>从库中添加 Workteam

要配置 Workteam 与 Azure AD 的集成，需要从库中将 Workteam 添加到托管 SaaS 应用列表。

**若要从库中添加 Workteam，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入“Workteam”，在结果面板中选择“Workteam”，然后单击“添加”按钮添加该应用程序    。

     ![结果列表中的 Workteam](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于一个名为“Britta Simon”的测试用户使用 Workteam 配置和测试 Azure AD 单一登录  。
若要运行单一登录，需要在 Azure AD 用户与 Workteam 相关用户之间建立链接关系。

若要配置和测试 Workteam 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Workteam 单一登录](#configure-workteam-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Workteam 测试用户](#create-workteam-test-user)** - 在 Workteam 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Workteam 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中，在 Workteam 应用程序集成页上，选择“单一登录”   。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分，若要在“IDP 发起的模式”下配置应用程序，用户无需执行任何步骤，因为该应用已预先集成到 Azure。  

    ![Workteam 域和 URL 单一登录信息](common/preintegrated.png)

5. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    ![Workteam 域和 URL 单一登录信息](common/metadata-upload-additional-signon.png)

    在“登录 URL”文本框中，键入 URL：`https://app.workte.am` 

6. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

7. 在“设置 Workteam”部分中，根据要求复制相应 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-workteam-single-sign-on"></a>配置 Workteam 单一登录

1. 在另一个 Web 浏览器窗口中，以安全管理员身份登录 Workteam。

2. 在右上角单击**个人资料徽标**，然后单击“组织设置”。  

    ![Workteam 设置](./media/workteam-tutorial/tutorial_workteam_settings.png)

3. 在“身份验证”部分下，单击“设置”徽标。  

     ![Workteam azure](./media/workteam-tutorial/tutorial_workteam_azure.png)

4. 在“SAML 设置”  页上，执行以下步骤：

     ![Workteam saml](./media/workteam-tutorial/tutorial_workteam_saml.png)

    a. 为“SAML IdP”选择“AD Azure”。  

    b. 在“SAML 单一登录服务 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    c. 在“SAML 实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    d. 在记事本中，打开从 Azure 门户下载的 base-64 编码证书，复制其内容，然后将其粘贴到“SAML 签名证书(Base64)”框中   。

    e. 单击“确定”。 

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

在本部分，我们通过向 Britta Simon 授予 Workteam 的访问权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Workteam”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Workteam”  。

    ![应用程序列表中的 Workteam 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-workteam-test-user"></a>创建 Workteam 测试用户

为了使 Azure AD 用户能够登录 Workteam，必须对其进行预配才能使其登录 Workteam。 在 Workteam 中，预配属于手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以安全管理员身份登录 Workteam。

2. 在“组织设置”页的中上部分，依次单击“用户”、“新建用户”。   

    ![Workteam 用户](./media/workteam-tutorial/tutorial_workteam_user.png)

3. 在“新建员工”页上执行以下步骤： 

    ![Workteam newuser](./media/workteam-tutorial/tutorial_workteam_newuser.png)

    a. 在“姓名”文本框中，输入用户的名字，例如 **Brittasimon**。 

    b. 在“电子邮件”文本框中，输入用户的电子邮件，例如 Brittasimon\@contoso.com   。

    c. 单击“确定”。 

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Workteam 磁贴时，应当会自动登录到你为其设置了 SSO 的 Workteam。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

