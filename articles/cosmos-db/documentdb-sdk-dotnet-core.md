---
title: "Azure DocumentDB .NET Core API、SDK 和资源 | Microsoft 文档"
description: "了解有关 .NET Core API 和 SDK 的全部信息，包括发布日期、停用日期和 DocumentDB .NET Core SDK 各版本之间的更改。"
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: f899b314-26ac-4ddb-86b2-bfdf05c2abf2
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/12/2017
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: c1f0bbfd1bea292eedaaf8904a2f60e9415dcbcf
ms.contentlocale: zh-cn
ms.lasthandoff: 06/20/2017


---
# <a name="documentdb-net-core-sdk-release-notes-and-resources"></a>DocumentDB .NET Core SDK：发行说明和资源
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.js](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [REST 资源提供程序](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

<table>

<tr><td>**SDK 下载**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)</td></tr>

<tr><td>**API 文档**</td><td>[ 参考文档](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)</td></tr>

<tr><td>**示例**</td><td>[.NET代码示例](documentdb-dotnet-samples.md)</td></tr>

<tr><td>**入门**</td><td>[开始使用 DocumentDB .NET Core SDK](documentdb-dotnetcore-get-started.md)</td></tr>

<tr><td>**Web 应用教程**</td><td>[使用 DocumentDB 开发 Web 应用程序](documentdb-dotnet-application.md)</td></tr>

<tr><td>**当前受支持的框架**</td><td>[.NET Standard 1.6 和 .NET Standard 1.5](https://www.nuget.org/packages/NETStandard.Library)</td></tr>
</table></br>

## <a name="release-notes"></a>发行说明

DocumentDB .NET Core SDK 具有与最新版 [DocumentDB.NET SDK](documentdb-sdk-dotnet.md) 相同的功能。

> [!NOTE] 
> DocumentDB .NET Core SDK 与通用 Windows 平台 (UWP) 应用尚不兼容。 如果不支持 UWP 应用的 .NET Core SDK 感兴趣，请向 [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) 发送电子邮件。

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2

*   支持 .NET Standard 1.5 作为目标框架之一。

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1

*   修复了影响 x64 计算机的一个问题，该计算机不支持 SSE4 指令，并在运行 DocumentDB 查询时引发 SEHException。

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

*   添加了对每分钟请求单位 (RU/m) 功能的支持。
*   添加了对名为 ConsistentPrefix 的新一致性级别的支持。
*   添加了对单独分区的查询指标的支持。
*   添加了对限制查询的继续令牌大小的支持。
*   添加了对失败请求的详情跟踪的支持。
*   改进了 SDK 中的一些性能。

### <a name="a-name122122"></a><a name="1.2.2"/>1.2.2

* 修复了忽略 FeedOptions 中为聚合查询提供的 PartitionKey 值的问题。
* 修复了在中途跨分区执行 OrderBy 查询期间透明处理分区管理的问题。

### <a name="a-name121121"></a><a name="1.2.1"/>1.2.1

* 修复了在 ASP.NET 上下文内使用时，在某些异步 API 中导致死锁的问题。

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* 修复程序，用于使 SDK 更具弹性，以便在某些情况下自动故障转移。

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* 修复偶尔导致 WebException 的问题：无法解析远程名称。
* 通过向 ReadDocumentAsync API 添加新重载，添加了对直接读取类型化文档的支持。

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* 添加了对聚合查询（COUNT、MIN、MAX、SUM 和 AVG）的 LINQ 支持。
* 修复了由于使用了事件处理程序而导致的 ConnectionPolicy 对象的内存泄漏问题。
* 修复了使用 ETag 时 UpsertAttachmentAsync 不正常工作的问题。
* 修复了对字符串字段进行排序时跨分区按查询条件排序不正常工作的问题。

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* 添加了对聚合查询（COUNT、MIN、MAX、SUM、AVG）的支持。 请参阅[聚合支持](documentdb-sql-query.md#Aggregates)。
* 将分区集合上的最小吞吐量从 10,100 RU/s 降低到 2500 RU/s。

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

通过 DocumentDB .NET Core SDK，可构建能在 Windows、Mac 和 Linux 上快速运行的跨平台 [ASP.NET Core](https://www.asp.net/core) 和 [.NET Core](https://www.microsoft.com/net/core#windows) 应用。 DocumentDB .NET Core SDK 的最新版本完全兼容 [Xamarin](https://www.xamarin.com)，可用于生成面向 iOS、Android 和 Mono (Linux) 的应用程序。  

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-preview

通过 DocumentDB .NET Core 预览版 SDK，可构建能在 Windows、Mac 和 Linux 上快速运行的跨平台 [ASP.NET Core](https://www.asp.net/core) 和 [.NET Core](https://www.microsoft.com/net/core#windows) 应用。

DocumentDB .NET Core 预览版 SDK 与最新版 [DocumentDB.NET SDK](documentdb-sdk-dotnet.md) 功能相同，并支持以下内容：
* 所有[连接模式](performance-tips.md#networking)：网关模式、Direct TCP 和 Direct HTTP。 
* 所有[一致性级别](consistency-levels.md)：强一致性、会话一致性、有限过期一致性和最终一致性。
* [已分区集合](partition-data.md)。 
* [多区域数据库帐户和异地复制](distribute-data-globally.md)。

若有与此 SDK 相关的问题，请发布到 [StackOverflow](http://stackoverflow.com/questions/tagged/azure-documentdb)，或在 [github 存储库](https://github.com/Azure/azure-documentdb-dotnet/issues)中提出问题。 

## <a name="release--retirement-dates"></a>发布和停用日期

| 版本 | 发布日期 | 停用日期 |
| --- | --- | --- |
| [1.3.2](#1.3.2) |2017 年 6 月 12 日 |--- |
| [1.3.1](#1.3.1) |2017 年 5 月 23 日 |--- |
| [1.3.0](#1.3.0) |2017 年 5 月 10 日 |--- |
| [1.2.2](#1.2.2) |2017 年 4 月 19 日 |--- |
| [1.2.1](#1.2.1) |2017 年 3 月 29 日 |--- |
| [1.2.0](#1.2.0) |2017 年 3 月 25 日 |--- |
| [1.1.2](#1.1.2) |2017 年 3 月 20 日 |--- |
| [1.1.1](#1.1.1) |2017 年 3 月 14 日 |--- |
| [1.1.0](#1.1.0) |2017 年 2 月 16 日 |--- |
| [1.0.0](#1.0.0) |2016 年 12 月 21 日 |--- |
| [0.1.0-preview](#0.1.0-preview) |2016 年 11 月 15 日 |2016 年 12 月 31 日 |

## <a name="see-also"></a>另请参阅
若要了解有关 Cosmos DB 的详细信息，请参阅 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服务页。 


