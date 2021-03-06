---
title: "使用 .NET 针对 Azure 文件存储进行开发 | Microsoft Docs"
description: "了解如何开发使用 Azure 文件存储来存储文件数据的 .NET 应用程序和服务。"
services: storage
documentationcenter: .net
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 6a889ee1-1e60-46ec-a592-ae854f9fb8b6
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: e26da88ef1803d47d7286c5ae836ac9c041dadd1
ms.contentlocale: zh-cn
ms.lasthandoff: 07/12/2017

---

# 使用 .NET 针对 Azure 文件存储进行开发
<a id="develop-for-azure-file-storage-with-net" class="xliff"></a> 
> [!NOTE]
> 本文介绍如何使用 .NET 代码管理 Azure 文件存储。 若要详细了解 Azure 文件存储，请参阅 [Azure 文件存储简介](storage-files-introduction.md)。
>

[!INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../includes/storage-check-out-samples-dotnet.md)]

## 关于本教程
<a id="about-this-tutorial" class="xliff"></a>
本教程将演示使用 .NET 开发应用程序或服务的基本操作，这些程序或服务可以使用 Azure 文件存储来存储文件数据。 在本教程中，我们将创建一个简单的控制台应用程序，并演示如何通过 .NET 和 Azure 文件存储执行基本的操作：

* 获取文件内容
* 设置文件共享的配额（最大大小）。
* 若一个文件使用在共享中定义的共享访问策略，则为该文件创建一个共享访问签名（SAS 密钥）。
* 将文件复制到同一存储帐户中的另一个文件。
* 将文件复制到同一存储帐户中的一个 Blob。
* 使用 Azure 存储度量值进行故障排除

> [!Note]  
> 由于 Azure 文件存储可以通过 SMB 进行访问，因此可以编写简单的应用程序，通过标准的适用于文件 I/O 的 System.IO 类来访问 Azure 文件共享。 本文将介绍如何编写使用 Azure 存储 .NET SDK 的应用程序，该 SDK 使用 [Azure 文件存储 REST API](https://docs.microsoft.com/rest/api/storageservices/fileservices/file-service-rest-api) 与 Azure 文件存储通信。 


## 创建控制台应用程序，并获取程序集
<a id="create-the-console-application-and-obtain-the-assembly" class="xliff"></a>
在 Visual Studio 中创建新的 Windows 控制台应用程序。 以下步骤演示如何在 Visual Studio 2017 中创建控制台应用程序，但是，其他 Visual Studio 版本中的步骤是类似的。

1. 选择“文件” > “新建” > “项目”
2. 选择“已安装” > “模板” > “Visual C#” > “Windows 经典桌面”
3. 选择“控制台应用(.NET Framework)”
4. 在“名称:”字段中输入应用程序的名称
5. 选择“确定”

本教程中的所有代码示例都可以添加到控制台应用程序的 `Program.cs` 文件的 `Main()` 方法。

可以在任意类型的 .NET 应用程序（包括 Azure 云服务或 Web 应用，以及桌面和移动应用程序）中使用 Azure 存储客户端库。 为简单起见，我们在本指南中使用控制台应用程序。

## 使用 NuGet 安装所需包
<a id="use-nuget-to-install-the-required-packages" class="xliff"></a>
为完成此教程，需要在项目中引用两个包：

* [适用于 .NET 的 Microsoft Azure 存储客户端库](https://www.nuget.org/packages/WindowsAzure.Storage/)：此包提供以编程方式访问存储帐户中数据资源的权限。
* [适用于 .NET 的 Microsoft Azure Configuration Manager 库](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)：此包提供用于分析配置文件中连接字符串的类，而不考虑应用程序在何处运行。

可以使用 NuGet 获取这两个包。 执行以下步骤:

1. 在“解决方案资源管理器”中，右键单击你的项目并选择“管理 NuGet 包”。
2. 在线搜索“WindowsAzure.Storage”，然后单击“安装”  以安装存储客户端库和依赖项。
3. 在线搜索“WindowsAzure.ConfigurationManager”，然后单击“安装”以安装 Azure Configuration Manager。

## 将存储帐户凭据保存到 app.config 文件
<a id="save-your-storage-account-credentials-to-the-appconfig-file" class="xliff"></a>
接下来，将你的凭据保存到项目的 app.config 文件中。 编辑 app.config 文件，使其看起来类似于下面的示例，将 `myaccount` 替换为你的存储帐户名称，并将 `mykey` 替换为你的存储帐户密钥。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
    </appSettings>
</configuration>
```

> [!NOTE]
> 最新版本的 Azure 存储模拟器不支持 Azure 文件存储。 连接字符串必须针对云中要使用 Azure 文件存储的 Azure 存储帐户。

## 添加 using 指令
<a id="add-using-directives" class="xliff"></a>
从解决方案资源管理器打开 `Program.cs` 文件，并在该文件顶部添加以下 using 指令。

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.WindowsAzure.Storage; // Namespace for Storage Client Library
using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.WindowsAzure.Storage.File; // Namespace for Azure File storage
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

## 以编程方式访问文件共享
<a id="access-the-file-share-programmatically" class="xliff"></a>
接下来，将以下代码添加到 `Main()` 方法（在上面显示的代码后面）以检索连接字符串。 此代码将获取我们先前创建的文件的引用，并将其内容输出到控制台窗口中。

```csharp
// Create a CloudFileClient object for credentialed access to Azure File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

运行控制台应用程序以查看输出。

## 设置文件共享的最大大小
<a id="set-the-maximum-size-for-a-file-share" class="xliff"></a>
从 Azure 存储客户端库的 5.x 版开始，可以设置文件共享的配额（或最大大小），单位为千兆字节。 你还可以查看共享当前存储了多少数据。

通过设置一个共享的配额，可以限制在该共享上存储的文件的总大小。 如果共享上文件的总大小超过在共享上设定的配额，则客户端将不能增加现有文件的大小或创建新文件，除非这些文件是空的。

下面的示例演示如何检查共享的当前使用情况，以及如何设置共享的配额。

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.WindowsAzure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GB.
    // This line sets the quota to be 10 GB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
}
```

### 为文件或文件共享生成共享访问签名
<a id="generate-a-shared-access-signature-for-a-file-or-file-share" class="xliff"></a>
从 Azure 存储客户端库的 5.x 版开始，可以为文件共享或单个文件生成共享访问签名 (SAS)。 还可以在文件共享上创建一个共享访问策略以管理共享访问签名。 建议创建共享访问策略，因为它提供了一种在受到威胁时撤消 SAS 的方式。

以下示例在一个共享上创建共享访问策略，然后使用该策略为共享中的一个文件提供 SAS 约束。

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new shared access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the shared access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authenticated via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

有关创建和使用共享访问签名的更多信息，请参阅[使用共享访问签名 (SAS)](storage-dotnet-shared-access-signature-part-1.md) 和[创建 SAS 并将其与 Azure Blob 结合使用](storage-dotnet-shared-access-signature-part-2.md)。

## 复制文件
<a id="copy-files" class="xliff"></a>
从 Azure 存储客户端库的 5.x 版开始，可以将一个文件复制到另一个文件，将一个文件复制到一个 Blob，或将一个 Blob 复制到一个文件。 在后续部分中，我们将演示如何以编程方式执行这些复制操作。

还可以使用 AzCopy 将一个文件复制到另一个文件或将一个 Blob 复制到一个文件，反之亦然。 请参阅 [使用 AzCopy 命令行实用程序传输数据](storage-use-azcopy.md)。

> [!NOTE]
> 如果将一个 Blob 复制到一个文件，或将一个文件复制到一个 Blob，必须使用共享访问签名 (SAS) 对源对象进行身份验证，即使你在同一存储帐户内进行复制。
> 
> 

将文件复制到另一文件：以下示例将一个文件复制到同一共享中的另一个文件。 因为此操作在同一存储帐户中的文件之间进行复制，可以使用共享密钥身份验证来进行复制。

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

将文件复制到 Blob：以下示例创建一个文件并将其复制到同一存储帐户中的某个 Blob。 该示例为源文件创建一个 SAS，服务在复制操作期间使用该 SAS 验证对源文件的访问。

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authenticate access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

可以用相同的方式将一个 Blob 复制到一个文件。 如果源对象是一个 Blob，则创建一个 SAS 以复制操作期间验证对该 Blob 的访问。

## 使用指标对 Azure 文件存储进行故障排除
<a id="troubleshooting-azure-file-storage-using-metrics" class="xliff"></a>
Azure 存储分析现在支持用于 Azure 文件存储的指标。 使用指标数据，可以跟踪请求和诊断问题。


可以通过 [Azure 门户](https://portal.azure.com)为 Azure 文件存储启用指标。 你还可以通过 REST API 或存储客户端库中的类似物之一调用“设置文件服务属性”操作，以编程方式启用指标。


下面的代码示例演示如何使用适用于 .NET 的存储客户端库启用 Azure 文件存储的指标。

首先，在添加以上指令后，将以下 `using` 指令添加到 `Program.cs` 文件中：

```csharp
using Microsoft.WindowsAzure.Storage.File.Protocol;
using Microsoft.WindowsAzure.Storage.Shared.Protocol;
```

请注意，Azure Blob、Azure 表和 Azure 队列使用 `Microsoft.WindowsAzure.Storage.Shared.Protocol` 命名空间中的共享 `ServiceProperties` 类型，而 Azure 文件存储使用其自己的类型，即 `Microsoft.WindowsAzure.Storage.File.Protocol` 命名空间中的 `FileServiceProperties` 类型。 但是，你的代码中必须同时引用这两个命名空间，才能编译后续代码。

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.WindowsAzure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

还可以参考 [Azure 文件存储故障排除文章](storage-troubleshoot-file-connection-problems.md)，了解端到端故障排除指南。

## 后续步骤
<a id="next-steps" class="xliff"></a>
请参阅以下链接以获取有关 Azure 文件存储的更多信息。

### 概念性文章和视频
<a id="conceptual-articles-and-videos" class="xliff"></a>
* [Azure File storage: a frictionless cloud SMB file system for Windows and Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)（Azure 文件存储：适用于 Windows 和 Linux 的顺畅的云 SMB 文件系统）
* [如何将 Azure 文件存储与 Linux 配合使用](storage-how-to-use-files-linux.md)

### 文件存储的工具支持
<a id="tooling-support-for-file-storage" class="xliff"></a>
* [对 Azure 存储空间使用 Azure PowerShell](storage-powershell-guide-full.md)
* [如何对 Microsoft Azure 存储使用 AzCopy](storage-use-azcopy.md)
* [将 Azure CLI 用于 Azure 存储](storage-azure-cli.md#create-and-manage-file-shares)
* [排查 Azure 文件存储问题](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### 引用
<a id="reference" class="xliff"></a>
* [.NET 存储客户端库参考](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [文件服务 REST API 参考](http://msdn.microsoft.com/library/azure/dn167006.aspx)

### 博客文章
<a id="blog-posts" class="xliff"></a>
* [Azure 文件存储现已正式发布](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inside Azure File storage](https://azure.microsoft.com/blog/inside-azure-file-storage/)（Azure 文件存储内部）
* [Microsoft Azure 文件服务简介](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Persisting connections to Microsoft Azure File storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)（将连接保存到 Microsoft Azure 文件存储中）
