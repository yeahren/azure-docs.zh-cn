---
title: "Azure PowerShell 脚本示例 - 将托管磁盘的快照复制（移动）到同一或不同订阅 | Microsoft Docs"
description: "Azure PowerShell 脚本示例 - 将托管磁盘的快照复制（移动）到同一或不同订阅"
services: managed-disks
documentationcenter: storage
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: 
ms.service: managed-disks-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/06/2017
ms.author: ramankum
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 5ae87c485f06f9eec6b64e8a072e334c73b56a47
ms.contentlocale: zh-cn
ms.lasthandoff: 06/08/2017

---

# 使用 PowerShell 将托管磁盘的快照复制到同一订阅或不同订阅
<a id="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell" class="xliff"></a>

此脚本在同一订阅或不同订阅中创建某个快照的副本。 使用此脚本将快照移动到不同的订阅以保留数据。 将快照存储在不同的订阅中可以在你意外删除主订阅中的快照时提供保护。 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## 示例脚本
<a id="sample-script" class="xliff"></a>

[!code-powershell[main](../../../powershell_scripts/storage/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "复制快照")]


## 脚本说明
<a id="script-explanation" class="xliff"></a>

此脚本使用以下命令并使用源快照的 Id 在目标订阅中创建新快照。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzureRmSnapshotConfig](/powershell/module/azurerm.compute/New-AzureRmSnapshotConfig) | 创建用于创建快照的快照配置。 它包括父快照的资源 Id 以及与父快照相同的位置。  |
| [New-AzureRmSnapshot](/powershell/module/azurerm.compute/New-AzureRmDisk) | 使用作为参数传递的快照配置、快照名称和资源组名称创建快照。 |


## 后续步骤
<a id="next-steps" class="xliff"></a>

[基于快照创建虚拟机](./../../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure Windows VM 文档](../../virtual-machines/windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他虚拟机 PowerShell 脚本示例。
