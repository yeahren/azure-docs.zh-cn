---
title: "教程：Azure Active Directory 与 TOPdesk - Public 集成 | Microsoft 文档"
description: "了解如何使用 TOPdesk - Public 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1c22e4fc17226578aaaf272fdf79178da65c63c2
ms.openlocfilehash: 54c7da3fc0693ee44358cb77973c8c37e653d3cc
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-directory-integration-with-topdesk---public"></a>教程：Azure Active Directory 与 TOPdesk - Public 集成
本教程的目的是说明 Azure 与 TOPdesk - Public 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 TOPdesk - Public 单一登录的订阅

完成本教程后，已向 TOPdesk - Public 分配的 Azure AD 用户将能够在 TOPdesk - Public 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 TOPdesk - Public 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-topdesk-public-tutorial/IC790613.png "方案")

## <a name="enabling-the-application-integration-for-topdesk---public"></a>为 TOPdesk - Public 启用应用程序集成
本部分的目的是概述如何为 TOPdesk - Public 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-topdesk---public-perform-the-following-steps"></a>若要为 TOPdesk - Public 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
    ![Active Directory](./media/active-directory-saas-topdesk-public-tutorial/IC700993.png "Active Directory")

2. 在“目录”列表中，选择要启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序](./media/active-directory-saas-topdesk-public-tutorial/IC700994.png "应用程序")

4. 在页面底部单击“添加”。
   
    ![添加应用程序](./media/active-directory-saas-topdesk-public-tutorial/IC749321.png "添加应用程序")

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![从库添加应用程序](./media/active-directory-saas-topdesk-public-tutorial/IC749322.png "从库添加应用程序")

6. 在搜索框中，键入“TOPdesk - Public”。
   
    ![应用程序库](./media/active-directory-saas-topdesk-public-tutorial/IC790614.png "应用程序库")

7. 在结果窗格中，选择“TOPdesk - Public”，然后单击“完成”以添加该应用程序。
   
    ![TOPdesk Public](./media/active-directory-saas-topdesk-public-tutorial/IC791317.png "TOPdesk Public")

## <a name="configuring-single-sign-on"></a>配置单一登录
本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 TOPdesk - Public 进行身份验证。  
配置 TOPdesk - Public 的单一登录需要上载徽标图标文件。 若要获取图标文件，请与 TOPdesk 支持团队联系。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 以管理员身份登录 **TOPdesk - Public** 公司站点。

2. 在“TOPdesk”菜单中，单击“设置”。
   
    ![设置](./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "设置")

3. 单击“登录设置”。
   
    ![登录设置](./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "登录设置")

4. 展开“登录设置”菜单，然后单击“常规”。
   
    ![常规](./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "常规")

5. 在“SAML 登录”配置部分的“公共”部分中，执行以下步骤：
   
    ![技术设置](./media/active-directory-saas-topdesk-public-tutorial/IC790601.png "技术设置")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“下载”下载公共元数据文件，并将该文件保存到本地计算机上。
   
    b. 打开元数据文件，然后找到“AssertionConsumerService”节点。
    ![AssertionConsumerService](./media/active-directory-saas-topdesk-public-tutorial/IC790619.png "AssertionConsumerService")
   
    c. 复制 **AssertionConsumerService** 值。  
      
    > [!NOTE]
    > 在本教程后面的“配置应用 URL”部分中将需要该值。
    > 
    > 

6. 在另一个 Web 浏览器窗口中，以管理员身份登录 **Azure 经典门户**。

7. 在“TOPdesk - Public”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-topdesk-public-tutorial/IC790620.png "配置单一登录")

8. 在“你希望用户如何登录 TOPdesk - Public”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-topdesk-public-tutorial/IC790621.png "配置单一登录")

9. 在“配置应用 URL”页上，执行以下步骤：
   
    ![配置应用 URL](./media/active-directory-saas-topdesk-public-tutorial/IC790622.png "配置应用 URL")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“TOPdesk - Public 登录 URL”文本框中，输入用户用于登录 TOPdesk - Public 应用程序的 URL（例如：“*https://qssolutions.topdesk.net*”）。
   
    b.保留“数据库类型”设置，即设置为“共享”。 在“TOPdesk - Public 回复 URL”文本框中，粘贴 **TOPdesk - Public AssertionConsumerService URL**（例如：“*https://qssolutions.topdesk.net/tas/public/login/saml*”）
   
    c. 单击“下一步”。

10. 在“配置 TOPdesk - Public 的单一登录”页上，若要下载元数据文件，请单击“下载元数据”，然后将文件保存在本地计算机上。
    
    ![配置单一登录](./media/active-directory-saas-topdesk-public-tutorial/IC790623.png "配置单一登录")

11. 若要创建证书文件，请执行以下步骤：
    
    ![证书](./media/active-directory-saas-topdesk-public-tutorial/IC790606.png "证书")
    
    1. 打开下载的元数据文件。
    2. 展开其 **xsi:type** 为 **fed:ApplicationServiceType** 的 **RoleDescriptor** 节点。
    3. 复制 **X509Certificate** 节点的值。
    4. 将复制的 **X509Certificate** 值保存到本地计算机上的文件中。
12. 在 TOPdesk - Public 公司站点上的“TOPdesk”菜单中，单击“设置”。
    
    ![设置](./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "设置")
13. 单击“登录设置”。
    
    ![登录设置](./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "登录设置")
14. 展开“登录设置”菜单，然后单击“常规”。
    
    ![常规](./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "常规")
15. 在“公共”部分中，单击“添加”。
    
    ![SAML 登录](./media/active-directory-saas-topdesk-public-tutorial/IC790625.png "SAML 登录")
16. 在“SAML 配置助手”对话框页上，执行以下步骤：
    
    ![SAML 配置助手](./media/active-directory-saas-topdesk-public-tutorial/IC790608.png "SAML 配置助手")
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 若要上载已下载的元数据文件，请在“联合元数据”下单击“浏览”。

    b.保留“数据库类型”设置，即设置为“共享”。 若要上载证书文件，请在“证书(RSA)”下，单击“浏览”。

    c. 若要上载从 TOPdesk 支持团队获得的徽标文件，请在“徽标图标”下，单击“浏览”。

    d.单击“下一步”。 在“用户名属性”文本框中，键入“http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress”。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 在“显示名称”文本框中，键入配置名称。

    f. 单击“保存” 。

17. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
    
    ![配置单一登录](./media/active-directory-saas-topdesk-public-tutorial/IC790627.png "配置单一登录")

## <a name="configuring-user-provisioning"></a>配置用户设置
要使 Azure AD 用户能够登录 TOPdesk - Public，必须将这些用户预配到 TOPdesk - Public 中。  
对于 TOPdesk - Public，预配是一项手动任务。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要配置用户设置，请执行以下步骤：
1. 以管理员身份登录 **TOPdesk - Public** 公司站点。

2. 在顶部菜单中，单击“TOPdesk”\>“新建”\>“支持文件”\>“人员”。
   
    ![人员](./media/active-directory-saas-topdesk-public-tutorial/IC790628.png "人员")

3. 在“新建用户”对话框中，执行以下步骤：
   
    ![新建人员](./media/active-directory-saas-topdesk-public-tutorial/IC790629.png "新建人员")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“常规”选项卡。

    b. 在“姓氏”文本框中，键入要预配的有效 Azure Active Directory 帐户的姓氏。
 
    c. 为该帐户选择**站点**。
 
    d.单击“下一步”。 单击“保存” 。

> [!NOTE]
> 可以使用任何其他 TOPdesk - Public 用户帐户创建工具或 TOPdesk - Public 提供的 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向要允许其使用应用程序的 Azure AD 用户授予该应用程序的访问权限。

### <a name="to-assign-users-to-topdesk---public-perform-the-following-steps"></a>若要将用户分配到 TOPdesk - Public，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。

2. 在“TOPdesk - Public”应用程序集成页上，单击“分配用户”。
   
    ![分配用户](./media/active-directory-saas-topdesk-public-tutorial/IC790630.png "分配用户")

3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
    ![是](./media/active-directory-saas-topdesk-public-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


