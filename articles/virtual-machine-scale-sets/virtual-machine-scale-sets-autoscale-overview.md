---
title: "自动缩放和虚拟机规模集 | Microsoft Docs"
description: "了解如何使用诊断和自动缩放资源自动缩放规模集中的虚拟机。"
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d29a3385-179e-4331-a315-daa7ea5701df
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2017
ms.author: adegeo
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 06ff9d9ae1dd8256f0d22c1a60ed6a85554f1f17
ms.contentlocale: zh-cn
ms.lasthandoff: 07/01/2017


---
# <a name="how-to-use-automatic-scaling-and-virtual-machine-scale-sets"></a>如何使用自动缩放和虚拟机规模集
在规模集内自动缩放虚拟机是指按需在规模集内创建或删除虚拟机，以符合性能需求。 当工作量增大时，应用程序可能需要额外的资源才能有效执行任务。

自动缩放是一个自动化过程，可帮助减少管理开销。 通过减少开销，使用户无需持续监控系统性能或决定如何管理资源。 缩放是一个灵活的过程。 当负载增加时可添加更多的资源。 当需求减少时可通过删除资源来使成本最小化，同时维持性能级别。

使用 Azure Resource Manager 模板、Azure PowerShell、Azure CLI 或 Azure 门户在规模集上设置自动缩放。

## <a name="set-up-scaling-by-using-resource-manager-templates"></a>使用 Resource Manager 模板设置缩放
可使用模板以单次协调的操作来部署所有资源，而无需单独部署和管理应用程序的每个资源。 在模板中，会定义应用程序资源，并针对不同的环境指定部署参数。 模板中包含可用于为部署构造值的 JSON 和表达式。 若要了解详细信息，请参阅[创作 Azure Resource Manager 模板](../azure-resource-manager/resource-group-authoring-templates.md)。

在模板中，可以指定容量元素：

```json
"sku": {
  "name": "Standard_A0",
  "tier": "Standard",
  "capacity": 3
},
```

容量会识别缩放集中的虚拟机数目。 您可以部署具有不同值的模板，以便手动更改容量。 如果部署模板只是为了更改容量，则可以仅包含具有更新容量的 SKU 元素。

可以将 autoscaleSettings 资源和诊断扩展组合使用，从而自动调整规模集的容量。

### <a name="configure-the-azure-diagnostics-extension"></a>配置 Azure 诊断扩展
仅当缩放集中每个虚拟机上的指标收集成功时，才能完成自动缩放。 Azure 诊断扩展提供监视和诊断功能，符合自动缩放资源的指标收集需求。 您可以安装扩展作为 Resource Manager 模板的一部分。

此示例显示在模板中用来配置诊断扩展的变量：

```json
"diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
"accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/', 'Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Thread Count\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
"wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"
```

部署模板时需要提供参数。 在此示例中，提供了存储帐户（在其中存储数据）和规模集（从其中收集数据）的名称。 此外，在此 Windows Server 示例中，只会收集 Thread Count 性能计数器。 Windows 或 Linux 中所有可用的性能计数器都可以用来收集诊断信息，并且可以包含在扩展配置中。

此示例显示模板中扩展的定义：

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "Microsoft.Insights.VMDiagnosticsSettings",
      "properties": {
        "publisher": "Microsoft.Azure.Diagnostics",
        "type": "IaaSDiagnostics",
        "typeHandlerVersion": "1.5",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
          "storageAccount": "[variables('diagnosticsStorageAccountName')]"
        },
        "protectedSettings": {
          "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
          "storageAccountKey": "[listkeys(variables('accountid'), variables('apiVersion')).key1]",
          "storageAccountEndPoint": "https://core.windows.net"
        }
      }
    }
  ]
}
```

当诊断扩展运行时，会存储数据并将其收集到一个表中，该表位于指定的存储帐户中。 在 WADPerformanceCounter 表中，可以找到收集的数据：

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountBefore2.png)

### <a name="configure-the-autoscalesettings-resource"></a>配置 autoScaleSettings 资源
autoscaleSettings 资源使用诊断扩展中的信息，以决定是增加规模集中虚拟机的数量还是减少虚拟机的数量。

此示例显示模板中资源的配置：

```json
{
  "type": "Microsoft.Insights/autoscaleSettings",
  "apiVersion": "2015-04-01",
  "name": "[concat(parameters('resourcePrefix'),'as1')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
  ],
  "properties": {
    "enabled": true,
    "name": "[concat(parameters('resourcePrefix'),'as1')]",
    "profiles": [
      {
        "name": "Profile1",
        "capacity": {
          "minimum": "1",
          "maximum": "10",
          "default": "1"
        },
        "rules": [
          {
            "metricTrigger": {
              "metricName": "\\Processor(_Total)\\Thread Count",
              "metricNamespace": "",
              "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT5M",
              "timeAggregation": "Average",
              "operator": "GreaterThan",
              "threshold": 650
            },
            "scaleAction": {
              "direction": "Increase",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          },
          {
            "metricTrigger": {
              "metricName": "\\Processor(_Total)\\Thread Count",
              "metricNamespace": "",
              "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT5M",
              "timeAggregation": "Average",
              "operator": "LessThan",
              "threshold": 550
            },
            "scaleAction": {
              "direction": "Decrease",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          }
        ]
      }
    ],
    "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
  }
}
```

在上述示例中，创建了两个规则来定义自动缩放操作。 第一个规则定义扩大操作，第二个规则定义缩小操作。 在规则中提供了这些值：

| 规则 | 说明 |
| ---- | ----------- |
| metricName        | 此值与在诊断扩展的 wadperfcounter 变量中定义的性能计数器相同。 上述示例使用的是 Thread Count 计数器。    |
| metricResourceUri | 此值是虚拟机规模集的资源标识符。 此标识符包含资源组的名称、资源提供程序的名称和要缩放的缩放集的名称。 |
| timeGrain         | 此值是收集的指标的粒度。 在前面的示例中，数据以每隔一分钟的时间进行收集。 此值用于 timeWindow。 |
| statistic         | 此值可确定如何组合指标以调节自动缩放操作。 可能的值包括：Average、Min、Max。 |
| timeWindow        | 此值是收集实例数据的时间范围。 它必须介于 5 分钟和 12 个小时之间。 |
| timeAggregation   | 此值可确定应如何随着时间的推移组合收集的数据。 默认值为 Average。 可能的值包括：Average、Minimum、Maximum、Last、Total、Count。 |
| operator          | 此值是用于比较指标数据和阈值的运算符。 可能的值包括：Equals、NotEquals、GreaterThan、GreaterThanOrEqual、LessThan、LessThanOrEqual。 |
| 阈值         | 此值是触发缩放操作的值。 请确保在扩大操作的阈值与缩小操作的阈值之间提供足够大的差异。 如果为两种操作设置的值相同，系统则会期待常量更改，这会阻止其实现缩放操作。 例如，同时设置为 600，前面示例中的线程则不起作用。 |
| direction         | 此值可确定在达到阈值时执行的操作。 可能的值为 Increase 或 Decrease。 |
| type              | 此值是应发生的操作类型，且必须设置为 ChangeCount。 |
| value             | 此值是在规模集中添加或删除的虚拟机数量。 此值必须大于或等于 1。 |
| cooldown          | 此值是自上次缩放操作后，至下次操作发生之前要等待的时间。 此值必须介于一分钟到一周之间。 |

根据所使用的性能计数器，会以不同的方式使用模板配置中的某些元素。 在前面的示例中，性能计数器是 Thread Count，扩大操作的阈值为 650，缩小操作的阈值为 550。 如果使用诸如 %Processor Time 之类的计数器，则将阈值设置为 CPU 使用率的百分比，以确定缩放操作。

触发缩放操作（如高负载）时，规模集的容量会根据模板中的值而增加。 例如，在容量设置为 3 且缩放操作值设置为 1 的缩放集中：

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerBefore.png)

在当前负载导致平均线程计数超过 650 的阈值时：

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountAfter.png)

触发扩大操作，使规模集的容量增加 1：

```json
"sku": {
  "name": "Standard_A0",
  "tier": "Standard",
  "capacity": 4
},
```

结果是向规模集添加一个虚拟机：

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerAfter.png)

经过五分钟的冷却期间之后，如果虚拟机上的线程平均数目仍然超过 600，则会向缩放集再添加一个虚拟机。 如果平均线程计数保持在 550 以下，则缩放集的容量会减少 1，并且从缩放集中删除一个虚拟机。

## <a name="set-up-scaling-using-azure-powershell"></a>使用 Azure PowerShell 设置缩放

若要查看使用 PowerShell 设置自动缩放的示例，请参阅 [Azure 监视器 PowerShell 快速入门示例](../monitoring-and-diagnostics/insights-powershell-samples.md)。

## <a name="set-up-scaling-using-azure-cli"></a>使用 Azure CLI 设置缩放

若要查看使用 Azure CLI 设置自动缩放的示例，请查看 [Azure 监视器跨平台 CLI 快速入门示例](../monitoring-and-diagnostics/insights-cli-samples.md)。

## <a name="set-up-scaling-using-the-azure-portal"></a>使用 Azure 门户设置缩放

若要查看使用 Azure 门户设置自动缩放的示例，请查看[使用 Azure 门户创建虚拟机规模集](virtual-machine-scale-sets-portal-create.md)。

## <a name="investigate-scaling-actions"></a>调查缩放操作

* **Azure 门户**  
当前使用门户可以获取有限数量的信息。

* **Azure 资源浏览器**  
要浏览规模集的当前状态，这是最适合的工具。 遵循此路径，你应该看到所创建的规模集的实例视图：  
**订阅 > {你的订阅} > resourceGroups > {你的资源组} > 提供程序 > Microsoft.Compute > virtualMachineScaleSets > {你的规模集} > virtualMachines**

* **Azure PowerShell**  
使用此命令可获取一些信息：

  ```powershell
  Get-AzureRmResource -name vmsstest1 -ResourceGroupName vmsstestrg1 -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15
  Get-Autoscalesetting -ResourceGroup rainvmss -DetailedOutput
  ```

* 就像连接任何其他虚拟机一样连接到 jumpbox 虚拟机，然后可以远程访问规模集中的虚拟机，以监视单个进程。

## <a name="next-steps"></a>后续步骤
* 请参阅[自动缩放虚拟机规模集中的虚拟机](virtual-machine-scale-sets-windows-autoscale.md)，以查看有关如何创建已配置自动缩放的规模集的示例。

* 在 [Azure 监视器 PowerShell 快速入门示例](../monitoring-and-diagnostics/insights-powershell-samples.md)中查找 Azure 监视器监视功能的示例

* 若要了解有关通知功能的相关信息，请参阅[使用自动缩放操作在 Azure 监视器中发送电子邮件和 webhook 警报通知](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md)。

* 了解如何[使用审核日志在 Azure 监视器中发送电子邮件和 webhook 警报通知](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)

* 了解[高级自动缩放方案](virtual-machine-scale-sets-advanced-autoscale.md)的相关信息。

