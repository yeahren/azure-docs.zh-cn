---
title: "Azure 多重身份验证提供程序 | Microsoft Docs"
description: "了解如何创建 Azure 多重身份验证提供程序。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: a7dd5030-7d40-4654-8fbd-88e53ddc1ef5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/28/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 977640041f4b58a751848c96e2aa48eb2b284154
ms.contentlocale: zh-cn
ms.lasthandoff: 06/30/2017

---

# Azure 多重身份验证提供程序入门
<a id="getting-started-with-an-azure-multi-factor-auth-provider" class="xliff"></a>
双重验证对于具有 Azure Active Directory 和 Office 365 用户的全局管理员默认可用。 但是，如果想充分利用[高级功能](multi-factor-authentication-whats-next.md)，则应购买完整版的 Azure 多重身份验证 (MFA)。

Azure 多重身份验证提供程序用于利用 Azure MFA 完整版提供的功能。 它适用于没有通过 Azure MFA、Azure AD Premium 或企业移动性 + 安全性 (EMS) 获得许可证的用户。  默认情况下，Azure MFA、Azure AD Premium 和 EMS 包括 Azure MFA 完整版。 若有许可证，则无需 Azure 多重身份验证提供程序。

需要 Azure 多重身份验证提供程序才能下载 SDK。

> [!IMPORTANT]
> 即使拥有 Azure MFA、AAD 高级版或 EMS 许可证，若要下载 SDK，也请创建 Azure 多重身份验证提供程序。  如果处于此目的而创建 Azure 多重身份验证提供程序且已拥有许可证，请务必使用**按启用的用户**模型创建提供程序。 然后，将该提供程序链接到包含 Azure MFA、Azure AD Premium 或 EMS 许可证的目录。 该配置将确保仅当执行双重验证的唯一用户数大于你所拥有的许可证数时才进行计费。

## 什么是 Azure 多重身份验证提供程序？
<a id="what-is-an-azure-multi-factor-auth-provider" class="xliff"></a>

如果不具有 Azure 多重身份验证的许可证，则可以创建身份验证提供程序要求用户进行双重验证。 如果计划开发自定义应用并且要启用 Azure MFA，请创建身份验证提供程序并[下载 SDK](multi-factor-authentication-sdk.md)。

有两种身份验证提供程序，区别主要在于 Azure 订阅的收费方式。 基于身份验证的选项会计算一个月内对租户执行的身份验证次数。 此选项最适用于仅偶尔对大量用户进行身份验证的情况，例如需要将 MFA 用于自定义应用程序。 基于用户的选项会计算租户中一个月内执行双重验证的用户数量。 此选项最适用于部分用户有许可证，但需要为超出许可限制的更多用户扩展 MFA 的情况。

## 创建多重身份验证提供程序
<a id="create-a-multi-factor-auth-provider" class="xliff"></a>
使用以下步骤创建 Azure 多重身份验证提供程序。

1. 以管理员身份登录到 [Azure 经典门户](https://manage.windowsazure.com)。
2. 在左侧选择“Active Directory”。
3. 在“Active Directory”页的顶部，选择“多重身份验证提供程序”。
   
   ![创建 MFA 提供程序](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)

4. 在底部单击“新建” 。
   
   ![创建 MFA 提供程序](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)

5. 在“应用服务”下选择“多重身份验证提供程序”
   
   ![创建 MFA 提供程序](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)

6. 选择“快速创建”。
   
   ![创建 MFA 提供程序](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)

7. 填写以下字段，然后选择“创建” 。
   1. **名称** - 多重身份验证提供程序的名称。
   2. **使用模型** - 可选择以下两个选项之一：
      * 按身份验证 - 购买按身份验证收费的模型。 通常用于在面向使用者的应用程序中使用 Azure 多重身份验证的方案。
      * 按启用的用户 - 购买按每个启用的用户收费的模型。 通常用于员工访问 Office 365 等应用程序的方案。 若有已拥有 Azure MFA 许可证的用户，请选择此选项。
   3. **目录** - 与多重身份验证提供程序关联的 Azure Active Directory 租户。 请注意以下事项：
      * 无需 Azure AD 目录即可创建多重身份验证提供程序。 如果仅计划使用 Azure 多重身份验证服务器或 SDK，请将此框留空。
      * 多重身份验证提供程序必须与 Azure AD 目录相关联才能利用高级功能。
      * 仅当要将本地 Active Directory 环境与 Azure AD 目录同步时，才需要 Azure AD Connect、AAD Sync 或 DirSync。  如果你只使用未同步的 Azure AD 目录，则对此无要求。
        
        ![创建 MFA 提供程序](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)

8. 单击“创建”后，将创建多重身份验证提供程序，此时你会看到以下消息：“已成功创建多重身份验证提供程序” 。 单击“确定” 。
   
   ![创建 MFA 提供程序](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)  

