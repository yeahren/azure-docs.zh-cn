---
title: "教程：Azure Active Directory 与 TalentLMS 集成 | Microsoft 文档"
description: "了解如何使用 TalentLMS 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 3/07/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: a9de3004a1968f514227f0ba5dfde0f562a2b392
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>教程：Azure Active Directory 与 TalentLMS 集成
本教程的目的是说明 Azure 与 TalentLMS 的集成。  

在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* TalentLMS 租户

完成本教程后，已向 TalentLMS 分配的 Azure AD 用户将能够在 TalentLMS 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 TalentLMS 启用应用程序集成
2. 配置单一登录 (SSO)
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-talentlms-tutorial/IC777289.png "方案")

## <a name="enable-the-application-integration-for-talentlms"></a>为 TalentLMS 启用应用程序集成
本部分的目的是概述如何为 TalentLMS 启用应用程序集成。

**若要为 TalentLMS 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
    ![Active Directory](./media/active-directory-saas-talentlms-tutorial/IC700993.png "Active Directory")

2. 在“目录”列表中，选择要启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序](./media/active-directory-saas-talentlms-tutorial/IC700994.png "应用程序")

4. 在页面底部单击“添加”。
   
    ![添加应用程序](./media/active-directory-saas-talentlms-tutorial/IC749321.png "添加应用程序")

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![从库添加应用程序](./media/active-directory-saas-talentlms-tutorial/IC749322.png "从库添加应用程序")

6. 在搜索框中，键入“TalentLMS”。
   
    ![应用程序库](./media/active-directory-saas-talentlms-tutorial/IC777290.png "应用程序库")

7. 在结果窗格中，选择“TalentLMS”，然后单击“完成”以添加该应用程序。
   
   ![TalentLMS](./media/active-directory-saas-talentlms-tutorial/IC777291.png "TalentLMS")

## <a name="configure-single-sign-on"></a>配置单一登录
本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 TalentLMS 进行身份验证。

配置 TalentLMS 的 SSO 需要检索证书的指纹值。  

如果不熟悉此过程，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI)。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户的“TalentLMS”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-talentlms-tutorial/IC777292.png "配置单一登录")

2. 在“你希望用户如何登录 TalentLMS”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-talentlms-tutorial/IC777293.png "配置单一登录")
3. 在“配置应用 URL”页上的“TalentLMS 登录 URL”文本框中，使用模式“*https://\<tenant-name\>.TalentLMSapp.com*”键入 URL，然后单击“下一步”。
   
    ![登录 URL](./media/active-directory-saas-talentlms-tutorial/IC777294.png "登录 URL")

4. 在“配置 TalentLMS 的单一登录”页上，若要下载证书，请单击“下载证书”，然后在本地将证书文件保存为 **c:\\TalentLMS.cer**。
   
    ![配置单一登录](./media/active-directory-saas-talentlms-tutorial/IC777295.png "配置单一登录")

5. 在另一个 Web 浏览器窗口中，以管理员身份登录 TalentLMS 公司站点。

6. 在“帐户与设置” 部分中，单击“用户”选项卡。
   
    ![帐户与设置](./media/active-directory-saas-talentlms-tutorial/IC777296.png "帐户与设置")

7. 单击“单一登录(SSO)”。

8. 在“单一登录”部分中，执行以下步骤：
   
    ![单一登录](./media/active-directory-saas-talentlms-tutorial/IC777297.png "单一登录")   
  1. 从“SSO 集成类型”列表中，选择“SAML 2.0”。
  2. 在 Azure 经典门户的“配置 TalentLMS 的单一登录”对话框页上，复制“标识提供者 ID”值，然后将其粘贴到“标识提供者(IdP)”文本框中。
  3. 从导出的证书中复制“指纹”值，然后将其粘贴到“证书指纹”文本框中。
      
     >[!TIP]
     >有关更多详细信息，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI)。 
     >    

  4. 在 Azure 经典门户的“配置 TalentLMS 的单一登录”对话框页面上，复制“远程登录 URL”值，然后将其粘贴到“远程登录 URL”文本框中。 
  5. 在 Azure 经典门户的“配置 TalentLMS 的单一登录”对话框页面上，复制“远程注销 URL”值，然后将其粘贴到“远程注销 URL”文本框中。
  6. 填写以下信息： 
    * 在“目标 ID”文本框中，键入“http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”。
    * 在“名字”文本框中，键入“http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”。
    * 在“姓氏”文本框中，键入“http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”。
    * 在“电子邮件”文本框中，键入“http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress”。
  7. 单击“保存” 。

9. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-talentlms-tutorial/IC777298.png "配置单一登录")

## <a name="configure-user-provisioning"></a>配置用户设置
要使 Azure AD 用户能够登录 TalentLMS，必须将这些用户预配到 TalentLMS 中。  

* 对于 TalentLMS，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 登录到 **TalentLMS** 租户。

2. 单击“用户”，然后单击“添加用户”。

3. 在“添加用户”对话框页上，执行以下步骤：
   
    ![添加用户](./media/active-directory-saas-talentlms-tutorial/IC777299.png "添加用户")   
  1. 在以下文本框中键入 Azure AD 用户帐户的相关属性值：**名字**、**姓氏**、**电子邮件地址**。
  2. 单击“添加用户”。

>[!NOTE]
>可以使用任何其他 TalentLMS 用户帐户创建工具或 TalentLMS 提供的 API 来预配 AAD 用户帐户。
>  

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 TalentLMS，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。

2. 在“TalentLMS”应用程序集成页上，单击“分配用户”。
   
    ![分配用户](./media/active-directory-saas-talentlms-tutorial/IC777300.png "分配用户")

3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
    ![是](./media/active-directory-saas-talentlms-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


