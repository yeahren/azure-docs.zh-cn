---
title: "了解如何在逻辑应用中使用 Azure 服务总线连接器 | Microsoft Docs"
description: "使用 Azure 应用服务创建逻辑应用。 连接到 Azure 服务总线以发送和接收消息。 可执行发送到队列、发送到主题、从队列接收和从订阅接收等操作。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/02/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e5d3301c90adf993b1cba35969dfe4dbeaeab499
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="get-started-with-the-azure-service-bus-connector"></a>Azure 服务总线连接器入门
连接到 Azure 服务总线以发送和接收消息。 可执行发送到队列、发送到主题、从队列接收和从订阅接收等操作。

若要使用 [任何连接器](apis-list.md)，首先需要创建逻辑应用。 可通过 [立即创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md) 开始操作。

## <a name="connect-to-service-bus"></a>连接到服务总线
需要先创建到任何服务的连接，然后逻辑应用才能访问该服务。 [连接](connectors-overview.md)提供逻辑应用和其他服务之间的连接性。  

> [!INCLUDE [Steps to create a connection to Azure Service Bus](../../includes/connectors-create-api-servicebus.md)]
> 
> 

## <a name="use-a-service-bus-trigger"></a>使用服务总线触发器
触发器是用于启动在逻辑应用中定义的工作流的事件。 [了解有关触发器的详细信息](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。  

> [!INCLUDE [Steps to create a Service Bus trigger](../../includes/connectors-create-api-servicebus-trigger.md)]
> 
> 

## <a name="use-a-service-bus-action"></a>使用服务总线操作
操作是指在逻辑应用中定义的由工作流执行的操作。 [了解有关操作的详细信息](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。

[!INCLUDE [Steps to create a Service Bus action](../../includes/connectors-create-api-servicebus-action.md)]

## <a name="view-the-swagger"></a>查看 Swagger
请参阅 [Swagger 详细信息](/connectors/servicebus/)。 

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。


