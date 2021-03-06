---
title: "使用 Visual Studio 和 .NET 查询 Azure SQL 数据库 | Microsoft Docs"
description: "本主题介绍如何使用 Visual Studio 创建连接到 Azure SQL 数据库的程序并使用 Transact-SQL 语句对其进行查询。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/05/2017
ms.author: carlrab
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: c7d54f8355803933e0d581470804ee98a0172937
ms.contentlocale: zh-cn
ms.lasthandoff: 07/12/2017

---
# 使用 .NET (C#) 和 Visual Studio 来连接和查询 Azure SQL 数据库
<a id="use-net-c-with-visual-studio-to-connect-and-query-an-azure-sql-database" class="xliff"></a>

本快速入门教程演示了如何使用 [.NET framework](https://www.microsoft.com/net/) 与 Visual Studio 来创建连接到 Azure SQL 数据库的 C# 程序，并使用 Transact-SQL 语句来查询数据。

## 先决条件
<a id="prerequisites" class="xliff"></a>

若要完成本快速入门教程，请确保符合以下条件：

- Azure SQL 数据库。 此快速入门使用以下某个快速入门中创建的资源： 

   - [创建 DB - 门户](sql-database-get-started-portal.md)
   - [创建 DB - CLI](sql-database-get-started-cli.md)
   - [创建 DB - PowerShell](sql-database-get-started-powershell.md)

- 针对用于本快速入门教程的计算机的公共 IP 地址制定[服务器级防火墙规则](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)。
- 已安装 [Visual Studio Community 2017、Visual Studio Professional 2017 或 Visual Studio Enterprise 2017](https://www.visualstudio.com/downloads/)。

## SQL Server 连接信息
<a id="sql-server-connection-information" class="xliff"></a>

获取连接到 Azure SQL 数据库所需的连接信息。 在后续过程中，将需要完全限定的服务器名称、数据库名称和登录信息。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 从左侧菜单中选择“SQL 数据库”，然后单击“SQL 数据库”页上的数据库。 
3. 在数据库的“概览”页上，查看如下图所示的完全限定的服务器名称。 可以将鼠标悬停在服务器名称上以打开“单击以复制”选项。 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. 如果忘了 Azure SQL 数据库服务器的登录信息，请导航到 SQL 数据库服务器页，以查看服务器管理员名称。 必要时可重置密码。

5. 单击“显示数据库连接字符串”。

6. 查看完整的 ADO.NET 连接字符串。

    ![ADO.NET 连接字符串](./media/sql-database-connect-query-dotnet/adonet-connection-string.png)

> [!IMPORTANT]
> 对于在其上执行本教程操作的计算机，必须为其公共 IP 地址制定防火墙规则。 如果使用其他计算机或其他公共 IP 地址，则[使用 Azure 门户创建服务器级防火墙规则](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)。 
>
  
## 创建新的 Visual Studio 项目
<a id="create-a-new-visual-studio-project" class="xliff"></a>

1. 在 Visual Studio 中，依次选择“文件”、“新建”、“项目”。 
2. 在“新建项目”对话框中，展开“Visual C#”。
3. 选择“控制台应用”，然后输入“sqltest”作为项目名称。
4. 单击“确定”，在 Visual Studio 中创建并打开新项目
4. 在“解决方案资源管理器”中，右键单击“sqltest”，然后单击“管理 NuGet 包”。 
5. 转到“浏览”，搜索 ```System.Data.SqlClient```，找到后将其选中。
6. 在“System.Data.SqlClient”页中单击“安装”。
7. 安装完成后，查看所做的更改，然后单击“确定”以关闭“预览”窗口。 
8. 如果显示“接受许可证”窗口，则单击“我接受”。

## 插入用于查询 SQL 数据库的代码
<a id="insert-code-to-query-sql-database" class="xliff"></a>

1. 切换到 Program.cs（或者根据需要将其打开）

2. 将 Program.cs 的内容替换为以下代码，为服务器、数据库、用户和密码添加相应的值。

```csharp
using System;
using System.Data.SqlClient;
using System.Text;

namespace sqltest
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                builder.DataSource = "your_server.database.windows.net"; 
                builder.UserID = "your_user";            
                builder.Password = "your_password";     
                builder.InitialCatalog = "your_database";

                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nQuery data example:");
                    Console.WriteLine("=========================================\n");
                    
                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                    sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                    sb.Append("JOIN [SalesLT].[Product] p ");
                    sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                    String sql = sb.ToString();

                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        using (SqlDataReader reader = command.ExecuteReader())
                        {
                            while (reader.Read())
                            {
                                Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                            }
                        }
                    }                    
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
            Console.ReadLine();
        }
    }
}
```

## 运行代码
<a id="run-the-code" class="xliff"></a>

1. 按 **F5** 运行应用程序。
2. 验证是否已返回前 20 行，然后关闭应用程序窗口。

## 后续步骤
<a id="next-steps" class="xliff"></a>

- 了解如何在 Windows/Linux/macOS 中[使用 .NET Core 连接和查询 Azure SQL 数据库](sql-database-connect-query-dotnet-core.md)。  
- 了解[在 Windows/Linux/macOS 中通过命令行使用 .NET Core 入门](/dotnet/core/tutorials/using-with-xplat-cli.md)。
- 了解如何[使用 SSMS 设计你的第一个 Azure SQL 数据库](sql-database-design-first-database.md)，或者如何[使用 .NET 设计你的第一个 Azure SQL 数据库](sql-database-design-first-database-csharp.md)。
- 有关 .NET 的详细信息，请参阅 [.NET 文档](https://docs.microsoft.com/dotnet/)。

