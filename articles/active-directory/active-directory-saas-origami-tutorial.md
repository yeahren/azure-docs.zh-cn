---
title: "教程：Azure Active Directory 与 Origami 集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 Origami 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: a28bb0ba-b564-46ba-accc-e587699295d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: 53251465d5153002fbdef6ac69ecd3979bb19ec3
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>教程：Azure Active Directory 与 Origami 的集成
本教程介绍如何将 Origami 与 Azure Active Directory (Azure AD) 集成。

将 Origami 与 Azure AD 集成具有以下优势：

* 可在 Azure AD 中控制谁有权访问 Origami
* 可以让用户通过其 Azure AD 帐户自动登录到 Origami 单一登录 (SSO)
* 可以在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 Origami 的集成，需备齐以下项目：

* Azure AD 订阅
* 启用了 Origami 单一登录 (SSO) 的订阅

>[!NOTE]
>不建议使用生产环境测试本教程中的步骤。 
> 

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，在测试环境中测试 Azure AD 单一登录。

本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Origami
2. 配置和测试 Azure AD SSO

## <a name="add-origami-from-the-gallery"></a>从库中添加 Origami
若要通过配置将 Origami 集成到 Azure AD 中，需从库将 Origami 添加到托管式 SaaS 应用的列表中。

**若要从库添加 Origami，请执行以下步骤：**

1. 在 **Azure 经典门户**的左侧导航窗格上，单击“Active Directory”。
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]
6. 在搜索框中，键入“Origami”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-origami-tutorial/tutorial_origami_01.png)
7. 在结果窗格中，选择“Origami”，然后单击“完成”添加该应用程序。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-origami-tutorial/tutorial_origami_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>配置和测试 Azure AD SSO
在本部分中，将基于名为“Britta Simon”的测试用户配置并测试 Origami 的 Azure AD SSO。

若要运行 SSO，Azure AD 需要知道与 Azure AD 用户相对应的 Origami 用户。 换句话说，需要建立 Azure AD 用户与 Origami 中相关用户之间的关联关系。

将 Azure AD 中“用户名”的值指定为 Origami 中“用户名”的值，即可建立此关联关系。

若要配置和测试 Origami 的 Azure AD SSO，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Origami 测试用户](#creating-a-origami-test-user)** - 目的是在 Origami 中有一个与 Azure AD 中的 Britta Simon 相对应的关联用户。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO
本部分需在经典门户中启用 Azure AD 单一登录，并在 Origami 应用程序中配置单一登录。

**若要通过 Origami 配置 Azure AD 单一登录，请执行以下步骤：**

1. 在经典门户中的“Origami”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录][6] 
2. 在“你希望用户如何登录 Origami”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-origami-tutorial/tutorial_origami_03.png) 
3. 在“配置应用设置”对话框页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-origami-tutorial/tutorial_origami_04.png)
  1. 在“登录 URL”文本框中，使用以下模式键入用户用于登录 Origami 应用程序的 URL：**https://live.origamirisk.com/origami/account/login?account=\<公司名称\>** 
  2. 单击“下一步”。
4. 在“在 Origami 中配置单一登录”页中，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-origami-tutorial/tutorial_origami_05.png)
  1. 单击“下载证书”，然后将文件保存在计算机上。
  2. 单击“资源组名称” 的 Azure 数据工厂。
5. 使用管理员权限登录到 Origami 帐户。
6. 在顶部菜单中，单击“管理员”。
   
    ![配置单一登录](./media/active-directory-saas-origami-tutorial/tutorial_origami_51.png)
7. 在“单一登录设置”对话框页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-origami-tutorial/123.png)
  1. 选择“启用单一登录”。
  2. 在 Azure 经典门户中，复制“SAML SSO URL”，然后将其粘贴到“标识提供者的登录页 URL”文本框中。
  3. 在 Azure 经典门户中，复制”单一注销服务 URL”，然后将其粘贴到“标识提供者的注销页 URL”文本框中。
  4. 单击“浏览”上载从 Azure 经典门户下载的证书。
  5. 单击“保存更改”。
8. 在经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
   
    ![Azure AD 单一登录][10]
9. 在“单一登录确认”页上，单击“完成”。  
   
    ![Azure AD 单一登录][11]

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
在本部分中，将在经典门户中创建一个名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-origami-tutorial/create_aaduser_09.png) 
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-origami-tutorial/create_aaduser_03.png) 
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-origami-tutorial/create_aaduser_04.png) 
5. 在“告诉我们有关此用户的信息”对话框页中，执行以下步骤： ![创建 Azure AD 测试用户](./media/active-directory-saas-origami-tutorial/create_aaduser_05.png) 
  1. 在“用户类型”中，选择“你的组织中的新用户”。
  2. 在“用户名”文本框中，键入“BrittaSimon”。
  3. 单击“资源组名称” 的 Azure 数据工厂。
6. 在“用户配置文件”对话框页上，执行以下步骤：

  ![创建 Azure AD 测试用户](./media/active-directory-saas-origami-tutorial/create_aaduser_06.png) 
  1. 在“名字”文本框中，键入“Britta”。    
  2. 在“姓氏”文本框中，键入“Simon”。
  3. 在“显示名称”文本框中，键入“Britta Simon”。
  4. 在“角色”列表中，选择“用户”。
  5. 单击“资源组名称” 的 Azure 数据工厂。
7. 在“获取临时密码”对话框页上，单击“创建”。
   
  ![创建 Azure AD 测试用户](./media/active-directory-saas-origami-tutorial/create_aaduser_07.png) 
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
  ![创建 Azure AD 测试用户](./media/active-directory-saas-origami-tutorial/create_aaduser_08.png)  
  1. 写下“新密码”的值。
  2. 单击“完成”。   

### <a name="create-an-origami-test-user"></a>创建 Origami 测试用户
本部分需在 Origami 中创建名为“Britta Simon”的用户。 

1. 使用管理员权限登录到 Origami 帐户。
2. 在顶部菜单中，单击“管理员”。
   
    ![配置单一登录](./media/active-directory-saas-origami-tutorial/tutorial_origami_51.png)
3. 在“用户和安全”对话框中，单击“用户”。
   
    ![配置单一登录](./media/active-directory-saas-origami-tutorial/tutorial_origami_54.png)
4. 单击“添加新用户”。
   
    ![配置单一登录](./media/active-directory-saas-origami-tutorial/tutorial_origami_55.png)
5. 在“添加新用户”对话框中，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-origami-tutorial/tutorial_origami_56.png)
  1. 在 Azure 经典门户的“用户名”文本框中键入用户名“Britta Simon”。
  2. 在“密码”文本框中，键入密码。
  3. 在“确认密码”文本框中，再次键入该密码。
  4. 在“名字”文本框中，键入“Britta”。
  5. 在“姓氏”文本框中，键入“Simon”。
  6. 单击“保存” 。
   
    ![配置单一登录](./media/active-directory-saas-origami-tutorial/tutorial_origami_57.png)
6. 向用户分配“用户角色”和“客户端访问权限”。 
   
    ![配置单一登录](./media/active-directory-saas-origami-tutorial/tutorial_origami_58.png)

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
在本部分中，通过授予 Britta Simon 访问 Origami 的权限，允许她使用 Azure SSO。

![分配用户][200] 

**若要将 Britta Simon 分配到 Origami，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201] 
2. 在应用程序列表中，选择“Origami”。
   
    ![配置单一登录](./media/active-directory-saas-origami-tutorial/tutorial_origami_50.png) 
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203]
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="test-single-sign-on"></a>测试单一登录
在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“Origami”磁贴时，用户就会自动登录到 Origami 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-origami-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-origami-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-origami-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-origami-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-origami-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-origami-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-origami-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-origami-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-origami-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-origami-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-origami-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-origami-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-origami-tutorial/tutorial_general_205.png

