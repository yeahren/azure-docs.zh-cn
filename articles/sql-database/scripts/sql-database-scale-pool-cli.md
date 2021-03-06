---
title: "CLI 示例 - 缩放 SQL 弹性池 - Azure SQL 数据库 | Microsoft Docs"
description: "在 Azure SQL 数据库中缩放 SQL 弹性池的 Azure CLI 示例脚本"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 06/23/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: f1f178d05826e8d2a37b41f2f98ed0ddf83f542b
ms.contentlocale: zh-cn
ms.lasthandoff: 06/28/2017

---

# <a name="use-azure-cli-to-scale-a-sql-elastic-pool-in-azure-sql-database"></a>使用 Azure CLI 在 Azure SQL 数据库中缩放 SQL 弹性池

以下 Azure CLI 脚本示例创建 SQL 弹性池，移动共用数据库，并更改弹性池性能级别。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本主题要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[主要](../../../cli_scripts/sql-database/scale-pool/scale-pool.sh "在池之间移动数据库")]

## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、逻辑服务器、SQL 数据库和防火墙规则。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 创建用于存储所有资源的资源组。 |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#create) | 创建用于托管 SQL 数据库的逻辑服务器。 |
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pools#create) | 在逻辑服务器中创建弹性数据库池。 |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#create) | 在逻辑服务器中创建 SQL 数据库。 |
| [az sql elastic-pools update](https://docs.microsoft.com/cli/azure/sql/elastic-pools#update) | 更新弹性数据库池，在此示例中更改分配的 eDTU。 |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

其他 SQL 数据库 CLI 脚本示例可以在 [Azure SQL 数据库文档](../sql-database-cli-samples.md)中找到。

