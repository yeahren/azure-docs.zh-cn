<properties
	pageTitle="Azure 密钥保管库入门 | 概述"
	description="本教程将会帮助你开始使用 Azure 密钥保管库在 Azure 中创建强化容器，以存储和管理 Azure 中的加密密钥和机密。"
	services="key-vault"
	documentationCenter=""
	authors="cabailey"
	manager="mbaldwin"/>

<tags
	ms.service="key-vault"
	ms.date="05/04/2015"
	wacn.date=""/>

# Azure 密钥保管库入门 #

## 介绍  
本教程将会帮助你开始使用 Azure 密钥保管库（当前以预览版提供）在 Azure 中创建强化容器（保管库），以存储和管理 Azure 中的加密密钥和机密。本教程将逐步引导你完成使用 Windows PowerShell 创建包含密钥或密码（稍后可用于 Azure 应用程序）的保管库程序。然后，将会说明应用程序后续如何使用该密钥或密码。

**估计完成时间：**20 分钟。

>[AZURE.NOTE]本教程未说明如何编写其中一个步骤所包括的 Azure 应用程序，但说明了如何授权应用程序使用密钥保管库中的密钥或机密。
>
>在预览期，你无法在 Azure 门户中配置 Azure 密钥保管库。请改用这些 Azure PowerShell 说明。

有关 Azure 密钥保管库的概述信息，请参阅[什么是 Azure 密钥保管库？](key-vault-whatis)

## 先决条件

若要完成本教程，你必须准备好以下各项：

- Microsoft Azure 订阅。如果你没有订阅，可以注册[试用版](/pricing/1rmb-trial)。
- Azure PowerShell 0.9.1 版或更高版本。若要安装最新版本并将其与 Azure 订阅相关联，请参阅[如何安装和配置 Azure PowerShell](powershell-install-configure)。
- 配置为使用你在本教程中所创建的密钥或密码的应用程序。你可以从 [Microsoft 下载中心](http://www.microsoft.com/zh-cn/download/details.aspx?id=45343)获取示例应用程序。有关说明，请参阅随附的自述文件。


本教程专为 Windows PowerShell 新手设计，但它假定您了解基本概念，如模块、cmdlet 和会话。有关 Windows PowerShell 的详细信息，请参阅 [Windows PowerShell 入门](https://technet.microsoft.com/zh-cn/library/hh857337.aspx)。

若要获得您在此教程中看到的任何 cmdlet 的详细帮助，请使用 Get-Help cmdlet。

	Get-Help <cmdlet-name> -Detailed

例如，若要获得有关 Add-AzureAccount cmdlet 的帮助，请键入：

	Get-Help Add-AzureAccount -Detailed

另请阅读以下教程以熟悉如何在 Windows PowerShell 中使用 Azure 资源管理器：

- [如何安装和配置 Azure PowerShell](powershell-install-configure)
- [将 Windows PowerShell 与资源管理器配合使用](powershell-azure-resource-manager)


## <a id="connect"></a>连接到订阅 ##

启动 Azure PowerShell 会话，然后使用以下命令登录你的 Azure 帐户：

    Add-AzureAccount

在弹出的浏览器窗口中，输入你的 Azure 帐户用户名和密码。Windows PowerShell 会获取与此帐户关联的所有订阅，并按默认使用第一个订阅。

如果你有多个订阅，并想要指定其中一个订阅供 Azure 密钥保管库使用，请键入以下内容以查看帐户的订阅：

    Get-AzureSubscription

然后，若要指定要使用的订阅，请键入：

    Select-AzureSubscription -SubscriptionName <subscription name>

有关配置 Azure PowerShell 的详细信息，请参阅[如何安装和配置 Azure PowerShell](powershell-install-configure)。

## <a id="switch"></a>切换到 Azure 资源管理器 ##

密钥保管库 cmdlet 需有 Azure 资源管理器，因此请键入以下内容切换到 Azure 资源管理器模式：

	Switch-AzureMode AzureResourceManager

## <a id="resource"></a>创建新的资源组 ##

使用 Azure 资源管理器时，会在资源组中创建所有相关资源。在本教程中，我们将创建名为“ContosoResourceGroup”的新资源组：

	New-AzureResourceGroup –Name 'ContosoResourceGroup' –Location 'China East'

对于 -Location 参数，请使用 [Get-AzureLocation](https://msdn.microsoft.com/zh-cn/library/azure/dn654582.aspx) 命令来了解如何针对本示例中的位置指定替代位置。如需更多信息，请键入：`Get-Help Get-AzureLocation`


## <a id="vault"></a>创建密钥保管库 ##

使用 [New-AzureKeyVault cmdlet](https://msdn.microsoft.com/zh-cn/library/azure/dn903602.aspx) 创建密钥保管库。此 cmdlet 包含三个必需参数：**资源组名称**、**密钥保管库名称**和**地理位置**。

例如，如果使用的保管库名称为 **ContosoKeyVault**，资源组名称为 **ContosoResourceGroup**，位置为**中国东部**，请键入：

    New-AzureKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'China East'

此 cmdlet 的输出会显示你刚刚创建的密钥保管库属性。两个最重要的属性是：

- **名称**：在本示例中为 ContosoKeyVault。你将在其他密钥保管库 cmdlet 中使用此名称。
- **vaultUri**：在本示例中为 https://contosokeyvault.vault.azure.net/。通过其 REST API 使用保管库的应用程序必须使用此 URI。

你的 Azure 帐户现已获取在此密钥保管库上执行任何作业的授权。而且没有其他人有此授权。

## <a id="add"></a>将密钥或机密添加到保管库 ##

如果你希望 Azure 密钥保管库为你创建一个受软件保护的密钥，请使用 [Add-AzureKeyVaultKey](https://msdn.microsoft.com/zh-cn/library/azure/dn868048.aspx) cmdlet，并键入以下内容：

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'


但是，如果 C:\ 驱动器中已存储了一个包含现有的受软件保护的密钥，并且要将文件名为 softkey.pfx 的 .PFX 文件上载到 Azure 密钥保管库，请键入以下内容来设置 **securepfxpwd** 变量，以将 .PFX 文件的密码设为 **123**：

    $securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force

然后键入以下内容以从 .PFX 文件导入密钥，这样，便会使用密钥保管库服务中的软件来保护密钥：

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd


现在，你可以通过使用密钥的 URI，引用已创建或上载到 Azure 密钥保管库的密钥。例如：**https://ContosoKeyVault.vault.azure.net/Keys/ContosoFirstKey/a10f5336-9d93-44a3-9e26-e86e3488b768** 

若要显示此密钥的 URI，请键入：

	$Key.key.kid

若要将名为 SQLPassword 且其 Azure 密钥保管库的值为 Pa$$w0rd 的机密添加到保管库，请先键入以下内容，将 Pa$$w0rd 的值转换成安全字符串：

	$secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force

然后键入以下内容：

	$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue

现在，你可以通过使用密码的 URI，引用已添加到 Azure 密钥保管库的此密码。例如：**https://ContosoVault.vault.azure.net/Secrets/778c3e43-3fdb-4cdf-b58e-7f501eb41d68**

若要显示此机密的 URI，请键入：

	$secret.Id

让我们查看一下刚刚创建的密钥或机密：

- 若要查看密钥，请键入：`Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'`
- 若要查看机密，请键入：`Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`

现在，你可以在应用程序中使用该密钥保管库以及密钥或机密。必须授权应用程序使用这些信息。

## <a id="register"></a>将应用程序注册到 Azure Active Directory ##

此步骤通常由开发人员在独立的计算机上完成。这并非 Azure 密钥保管库的特有状况，在此列出是为了让过程完整。


>[AZURE.IMPORTANT]若要完成本教程，你的帐户、保管库以及将在本步骤中注册的应用程序全都必须位于相同的 Azure 目录中。

使用密钥保管库的应用程序必须使用 Azure Active Directory 的令牌进行身份验证。为此，应用程序的所有者首先必须在其 Azure Active Directory 中注册该应用程序。注册结束后，应用程序所有者将获得以下值：


- **应用程序 ID**（也称为客户端 ID）和**身份验证密钥**（也称为共享机密）。应用程序必须向 Azure Active Directory 提供这两个值才能获取令牌。如何将应用程序配置为执行此操作取决于应用程序。对于密钥保管库示例应用程序，应用程序所有者将在 app.config 文件中设置这些值。



要在 Azure Active Directory 中注册应用程序，请执行以下操作：

1. 登录到 Azure 门户。
2. 单击左侧的“Active Directory”，然后选择你要在其中注册应用程序的目录。<br><br>注意：你必须选择包含用于创建密钥保管库的 Azure 订阅的相同目录。如果你不知道是哪个目录，请单击“设置”，找到用于创建密钥保管库的订阅，并记下最后一列中显示的目录名称。

3. 单击“应用程序”。如果你的目录中尚未添加任何应用程序，则此页只会显示“添加应用程序”链接。单击该链接，或者单击命令栏上的“添加”。
4.	在“添加应用程序”向导的“要执行什么操作?”页面上，单击“添加我的组织正在开发的应用程序”。
5.	在“向我们说明你的应用程序”页上，指定应用程序名称，然后选择“WEB 应用程序和/或 WEB API”（默认值）。单击“下一步”图标。
6.	在“应用程序属性”页上，为你的 Web 应用程序指定“登录 URL”和“应用程序ID URI”。如果你的应用程序没有这些值，可以在此步骤中虚构这些值（例如，可以在两个框中指定 http://test1.contoso.com）。这些网站是否存在并不重要；重要的是目录中每个应用程序的应用程序 ID URI 都不相同。目录会使用此字符串来识别你的应用程序。
7.	单击“完成”图标以保存向导中的更改。
8.	在“快速启动”页上，单击“映配置”。
9.	滚动到“密钥”部分，选择持续时间，然后单击“保存”。页面将会刷新，随后显示密钥值。你必须使用此密钥值和“客户端 ID”值来配置应用程序。（有关此配置的说明仅适用于特定的应用程序。）
10.	从此页中复制客户端 ID，后面的步骤将使用它来设置对保管库的权限。




## <a id="authorize"></a>授权应用程序使用密钥或机密 ##


若要授权应用程序访问保管库中的密钥或机密，请使用 [Set-AzureKeyVaultAccessPolicy](https://msdn.microsoft.com/zh-cn/library/azure/dn903607.aspx) cmdlet。

例如，如果保管库名称是 ContosoKeyVault，要授权的应用程序的客户端 ID 为 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed，而你希望授权应用程序使用保管库中的密钥来进行解密和签名，那么，请执行以下操作：

	Set-AzureKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign



## <a id="HSM"></a>如果你要使用硬件安全模块 (HSM) ##

为了提高可靠性，你可以在硬件安全模块 (HSM) 中导入或生成永不超出 HSM 边界的密钥。HSM 已通过 FIPS 140-2 Level 2 认证。如果此要求对你不适用，请跳过本部分并转到[删除密钥保管库以及关联的密钥和机密](#delete)。

若要创建这些受 HSM 保护的密钥，你必须具有[支持 HSM 保护密钥的保管库订阅](/pricing/1rmb-trial)。


创建保管库时，请添加“SKU”参数：

	New-AzureKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'China East' -SKU 'Premium'

你可以将软件保护的密钥（如前所示）和 HSM 保护的密钥添加到此保管库。若要创建受 HSM 保护的密钥，请将 Destination 参数设为“HSM”：

	$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'

你可以使用以下命令，从计算机上的 .PFX 文件导入密钥。此命令会将密钥导入到密钥保管库服务中的 HSM：

	$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'

下一个命令会导入“自带密钥”(BYOK) 包。这样，你就可以在本地 HSM 中生成密钥，并将其传输到密钥保管库服务中的 HSM，而密钥不会超出 HSM 界限：

	$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'

有关如何生成此 BYOK 包的详细说明，请参阅[如何对 Azure 密钥保管库使用受 HSM 保护的密钥](https://msdn.microsoft.com/zh-cn/library/azure/dn903624.aspx)。

## <a id="delete"></a>删除密钥保管库以及关联的密钥和机密 ##

如果你不再需要密钥保管库及其包含的密钥或机密，可以使用 [Remove-AzureKeyVault](https://msdn.microsoft.com/zh-cn/library/azure/dn903603.aspx) cmdlet 来删除密钥保管库：

	Remove-AzureKeyVault -VaultName 'ContosoKeyVault'

或者，你可以删除整个 Azure 资源组，其中包括密钥保管库和你加入该组的任何其他资源：

	Remove-AzureResourceGroup -ResourceGroupName 'ContosoResourceGroup'


## <a id="other"></a>其他 Azure PowerShell Cmdlet ##

可能有助于管理 Azure 密钥保管库的其他命令：

- `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`：此命令获取以表格形式显示的所有密钥和所选属性。
- `$Keys[0]`：此命令显示特定密钥的完整属性列表
- `Get-AzureKeyVaultSecret`：此命令列出以表格形式显示的所有机密名称和所选属性。
- `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`：示范如何删除特定密钥。
- `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`：示范如何删除特定机密。






## <a id="next"></a>后续步骤 ##

有关 Azure 密钥保管库的 Windows PowerShell cmdlet 列表，请参阅 [Azure 密钥保管库 Cmdlet](https://msdn.microsoft.com/zh-cn/library/azure/dn868052.aspx)。

有关编程参考，请参阅 [Azure 密钥保管库 REST API 参考](https://msdn.microsoft.com/zh-cn/library/azure/dn903609.aspx)和 [Azure 密钥保管库 C# 客户端 API 参考](https://msdn.microsoft.com/zh-cn/library/azure/dn903628.aspx)。

<!---HONumber=61-->