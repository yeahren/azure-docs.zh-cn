---
title: "教程：Azure Active Directory 与 Cornerstone OnDemand 的集成 | Microsoft 文档"
description: "了解如何将 Cornerstone OnDemand 与 Azure Active Directory 配合使用来启用单一登录、自动化预配及更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f57c5fef-49b0-4591-91ef-fc0de6d654ab
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: bbbae7d7a5e9b0fbfe07bd3f2aa61df77b56c5df
ms.openlocfilehash: 7fce07464b90c81da7a015246d5757ecc768792a
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-cornerstone-ondemand"></a>教程：Azure Active Directory 与 Cornerstone OnDemand 的集成
本教程的目标是展示 Azure 与 Cornerstone OnDemand 的集成。

在本教程中概述的方案假定已具有以下各项：

* 有效的 Azure 订阅
* Cornerstone OnDemand 租户

完成本教程后，分配到 Cornerstone OnDemand 的 Azure AD 用户将能够在你的 Cornerstone OnDemand 公司站点上（服务提供商启动的登录）或者使用[访问面板简介](active-directory-saas-access-panel-introduction.md)以单一登录方式登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

* 为 Cornerstone OnDemand 启用应用程序集成
* 配置单一登录 (SSO)
* 配置用户设置
* 分配用户

![方案](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781593.png "方案")

## <a name="enable-the-application-integration-for-cornerstone-ondemand"></a>为 Cornerstone OnDemand 启用应用程序集成
本部分的目的是概述如何为 Cornerstone OnDemand 启用应用程序集成。

**若要为 Cornerstone OnDemand 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749322.png "从库添加应用程序")
6. 在**搜索框**中，键入“cornerstone ondemand”。
   
   ![应用程序库](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781594.png "应用程序库")
7. 在结果窗格中，选择“Cornerstone OnDemand”，然后单击“完成”以添加该应用程序。
   
   ![Cornerstone OnDemand](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781595.png "Cornerstone OnDemand")
   
## <a name="configure-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Cornerstone OnDemand 证明自己的身份。

**若要配置 SSO，请执行以下步骤：**
1. 在 Azure 经典门户中，在 **Cornerstone OnDemand** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
   ![启用单一登录](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781596.png "Enable Single Sign-On")
2. 在“你希望用户如何登录到 Cornerstone OnDemand”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![Microsoft Azure AD 单一登录](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781597.png "Microsoft Azure AD 单一登录")
3. 在“配置应用 URL”页上，在“Cornerstone OnDemand 登录 URL”文本框中，使用“*http://company.csod.com*”模式键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781598.png "配置应用 URL")
4. 在“在 Cornerstone OnDemand 处配置单一登录”页上，若要下载证书，请单击“下载证书”，然后将证书文件保存在计算机本地。
   
   ![配置单一登录](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781599.png "配置单一登录")
5. 将以下各项发送给 Cornerstone OnDemand 支持团队：  
   1. 下载的证书
   2. **远程登录 URL** 值。
   3. **远程注销 URL** 值。

   >[!NOTE]
   >需要由 Cornerstone OnDemand 支持团队来配置单一登录。 配置完成后，你会从支持团队收到通知。
   > 
6. 选择“单一登录配置确认”，然后单击“完成”以关闭“配置单一登录”对话框。
   
  ![配置单一登录](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781600.png "配置单一登录")
   
## <a name="configure-user-provisioning"></a>配置用户设置

为了使 Azure AD 用户能够登录到 Cornerstone OnDemand，必须将其预配到 Cornerstone OnDemand 中。 对于 Cornerstone OnDemand，需要手动执行预配。

**若要配置用户预配，请执行以下步骤：**

1. 将你要预配的 Azure AD 用户的相关信息（例如：名字、电子邮件）发送给 Cornerstone OnDemand 支持团队。

>[!NOTE]
>可以使用 Cornerstone OnDemand 提供的任何其他 Cornerstone OnDemand 用户帐户创建工具或 API 来预配 AAD 用户帐户。 
> 

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 Cornerstone OnDemand，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在 **Cornerstone OnDemand** 应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC775564.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![分配用户](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781601.png "分配用户")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


