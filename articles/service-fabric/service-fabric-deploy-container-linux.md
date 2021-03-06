---
title: "Linux 中的 Service Fabric 和部署容器 | Microsoft 文档"
description: "介绍 Service Fabric，以及如何使用 Linux 容器部署微服务应用程序。 本文介绍 Service Fabric 为容器提供的功能，以及如何在群集中部署 Linux 容器映像"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 4ba99103-6064-429d-ba17-82861b6ddb11
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/29/2017
ms.author: msfussell
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 9dcec753e5f999a1bac07276373c0c25f89ec58d
ms.contentlocale: zh-cn
ms.lasthandoff: 07/01/2017


---
# <a name="deploy-a-linux-container-to-service-fabric"></a>将 Linux 容器部署到 Service Fabric
> [!div class="op_single_selector"]
> * [部署 Windows 容器](service-fabric-deploy-container.md)
> * [部署 Linux 容器](service-fabric-deploy-container-linux.md)
>
>

本文将指导你完成在 Linux 上的 Docker 容器中构建容器化的服务。

Service Fabric 提供多种容器功能，可帮助你构建由容器化的微服务组成的应用程序。 这些服务称为容器化服务。

功能包括：

* 容器映像部署和激活
* 资源调控
* 存储库身份验证
* 容器端口到主机端口的映射
* 容器到容器的发现和通信
* 能够配置和设置环境变量

## <a name="packaging-a-docker-container-with-yeoman"></a>使用 yeoman 打包 docker 容器
打包 Linux 上的容器时，可以选择使用 yeoman 项目模板，或者[手动创建应用程序包](#manually)。

Service Fabric 应用程序可以包含一个或多个容器，每个容器在提供应用程序功能时都具有特定角色。 用于 Linux 的 Service Fabric SDK 包括 [Yeoman](http://yeoman.io/) 生成器，利用它可以轻松地创建第一个服务应用程序和添加容器映像。 让我们使用 Yeoman 创建具有单个 Docker 容器（名为 *SimpleContainerApp*）的应用程序。 稍后，你可以通过编辑生成的清单文件添加更多服务。

## <a name="install-docker-on-your-development-box"></a>在开发环境中安装 Docker

运行以下命令以在 Linux 开发环境中安装 docker（如果是在 OSX 上使用 vagrant 映像，则已安装 docker）：

```bash
    sudo apt-get install wget
    wget -qO- https://get.docker.io/ | sh
```

## <a name="create-the-application"></a>创建应用程序
1. 在终端中，键入 `yo azuresfcontainer`。
2. 为应用程序命名，例如 mycontainerap
3. 提供 DockerHub 存储库中容器映像的 URL。 此映像参数采用的格式为 [repo]/[image name]
4. 如果映像没有定义工作负载入口点，则需要显式指定输入命令以及要在容器内运行的以逗号分隔的一组命令，这将在启动后使容器保持运行。

![适用于容器的 Service Fabric Yeoman 生成器][sf-yeoman]

## <a name="deploy-the-application"></a>部署应用程序

### <a name="using-xplat-cli"></a>使用 XPlat CLI
生成应用程序后，可以使用 Azure CLI 将其部署到本地群集。

1. 连接到本地 Service Fabric 群集。

    ```bash
    azure servicefabric cluster connect
    ```

2. 使用模板中提供的安装脚本可将应用程序包复制到群集的映像存储、注册应用程序类型和创建应用程序的实例。

    ```bash
    ./install.sh
    ```

3. 打开浏览器并导航到 http://localhost:19080/Explorer 的 Service Fabric Explorer（如果在 Mac OS X 上使用 Vagrant，则使用 VM 的专用 IP 替换 localhost）。
4. 展开应用程序节点，注意现在有一个条目是用于你的应用程序类型，另一个条目用于该类型的第一个实例。
5. 使用模板中提供的卸载脚本删除应用程序实例并取消注册应用程序类型。

    ```bash
    ./uninstall.sh
    ```

### <a name="using-azure-cli-20"></a>使用 Azure CLI 2.0

请参阅[使用 Azure CLI 2.0 管理应用程序生命周期](service-fabric-application-lifecycle-azure-cli-2-0.md)的参考文档。

有关示例应用程序，请[查看 GitHub 上的 Service Fabric 容器代码示例](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="adding-more-services-to-an-existing-application"></a>将更多服务添加到现有应用程序

若要将其他容器服务添加到使用 `yo` 创建的应用程序，请执行以下步骤：

1. 将目录更改为现有应用程序的根目录。  例如，如果 `MyApplication` 是 Yeoman 创建的应用程序，则使用 `cd ~/YeomanSamples/MyApplication`。
2. 运行 `yo azuresfcontainer:AddService`

<a id="manually"></a>

## <a name="manually-package-and-deploy-a-container-image"></a>手动打包和部署容器映像
手动打包容器化服务的过程基于以下步骤进行：

1. 将容器发布到存储库。
2. 创建包目录结构。
3. 编辑服务清单文件。
4. 编辑应用程序清单文件。

## <a name="deploy-and-activate-a-container-image"></a>部署和激活容器映像
在 Service Fabric [应用程序模型](service-fabric-application-model.md)中，容器表示放置多个服务副本的应用程序主机。 若要部署和激活某个容器，请在服务清单的 `ContainerHost` 元素中输入容器映像的名称。

在服务清单中，为入口点添加 `ContainerHost`。 然后将 `ImageName` 设置为容器存储库和映像的名称。 下面的不完整清单演示如何从名为 `myrepo` 的存储库部署名为 `myimage:v1` 的容器：

```xml
    <CodePackage Name="Code" Version="1.0">
        <EntryPoint>
          <ContainerHost>
            <ImageName>myrepo/myimage:v1</ImageName>
            <Commands></Commands>
          </ContainerHost>
        </EntryPoint>
    </CodePackage>
```

可以指定含有逗号分隔命令集的可选 `Commands` 元素在容器内部运行，来提供输入命令。

> [!NOTE]
> 如果映像没有定义工作负载入口点，则需要显式指定 `Commands` 元素内的输入命令，以及要在容器内运行的一组以逗号分隔的命令，这将在启动后使容器保持运行。

## <a name="understand-resource-governance"></a>了解资源监管
资源监管是一项容器功能，其限制容器可在主机上使用的资源。 在应用程序清单中指定的 `ResourceGovernancePolicy` 用于声明服务代码包的资源限制。 可以为以下资源设置资源限制：

* 内存
* MemorySwap
* CpuShares（CPU 相对权重）
* MemoryReservationInMB  
* BlkioWeight（BlockIO 相对权重）

> [!NOTE]
> 在以后版本中，将包含对指定特定块 IO 限制（例如 IOP、读/写 BPS）和其他的支持。
>
>

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuShares="500"
            MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="authenticate-a-repository"></a>对存储库进行身份验证
若要下载容器，必须向容器存储库提供登录凭据。 应用程序清单中指定的登录凭据用于指定从映像存储库下载容器映像所需的登录信息或 SSH 密钥。 以下示例显示名为 *TestUser* 的帐户以及明文密码（*不*建议使用）：

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="12345" PasswordEncrypted="false"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

建议使用已部署到计算机的证书加密密码。

以下示例显示名为 *TestUser* 的帐户，其密码已使用名为 *MyCert* 的证书加密。 可以使用 `Invoke-ServiceFabricEncryptText` PowerShell 命令创建密码的机密加密文本。 有关详细信息，请参阅文章[管理 Service Fabric 应用程序中的密钥](service-fabric-application-secret-management.md)。

用于解密密码的证书私钥必须使用带外方法部署到本地计算机。 （在 Azure 中该方法是 Azure Resource Manager）然后当 Service Fabric 将服务包部署到计算机时，可解密密钥。 通过使用密钥和帐户名，则可对容器存储库进行身份验证。

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="[Put encrypted password here using MyCert certificate ]" PasswordEncrypted="true"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

## <a name="configure-container-port-to-host-port-mapping"></a>配置容器端口到主机端口映射
可以在应用程序清单中指定 `PortBinding`，设置用来与容器通信的主机端口。 端口绑定可将服务在容器中侦听的端口映射到主机上的端口。

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

## <a name="configure-container-to-container-discovery-and-communication"></a>配置容器到容器的发现和通信
通过使用 `PortBinding` 策略将容器端口映射到服务清单中的 `Endpoint`。 终结点 `Endpoint1` 可以指定固定端口（例如端口 80）。 也可不指定任何端口，在此情况下，将从群集的应用程序端口范围内选择一个随机端口。

如果在来宾容器的服务清单中使用 `Endpoint` 标记指定了终结点，则 Service Fabric 可以自动将此终结点发布到命名服务。 因此群集中运行的其他服务可以使用 REST 查询进行解析来发现该容器。

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

在命名服务中注册后，可以轻松地在容器中的代码内使用[反向代理](service-fabric-reverseproxy.md)来执行容器到容器的通信。 将 http 侦听端口和想要与其通信的服务名称作为环境变量提供给反向代理，以此方式执行通信。 有关详细信息，请参阅后续部分。

## <a name="configure-and-set-environment-variables"></a>配置和设置环境变量
对于部署在容器中的服务，或者部署为进程/来宾可执行文件的服务，可以在服务清单中为每个代码包指定环境变量。 可以在应用程序清单中明确重写这些环境变量的值，或者在部署期间将其指定为应用程序参数。

以下服务清单 XML 代码片段演示如何指定代码包的环境变量：

```xml
    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>a guest executable service in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
    </ServiceManifest>
```

可在应用程序清单级别重写这些环境变量：

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <EnvironmentOverrides CodePackageRef="FrontendService.Code">
            <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvc]"/>
            <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentOverrides>
    </ServiceManifestImport>
```

在前面示例中，我们为 `HttpGateway` 环境变量指定了显式值 (19000)，`BackendServiceName` 参数的值是通过 `[BackendSvc]` 应用程序参数设置的。 通过这些设置可以在部署应用程序时指定 `BackendServiceName` 的值，而无需在清单中使用固定值。

## <a name="complete-examples-for-application-and-service-manifest"></a>应用程序清单和服务清单的完整示例

下面是应用程序清单示例：

```xml
    <ApplicationManifest ApplicationTypeName="SimpleContainerApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>A simple service container application</Description>
        <Parameters>
            <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
            <Parameter Name="BackEndSvcName" DefaultValue="bkend"></Parameter>
        </Parameters>
        <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
            <EnvironmentOverrides CodePackageRef="FrontendService.Code">
                <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvcName]"/>
                <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentOverrides>
            <Policies>
                <ResourceGovernancePolicy CodePackageRef="Code" CpuShares="500" MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
                <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                    <RepositoryCredentials AccountName="username" Password="****" PasswordEncrypted="true"/>
                    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
                </ContainerHostPolicies>
            </Policies>
        </ServiceManifestImport>
    </ApplicationManifest>
```

下面是服务清单示例（在前面的应用程序清单中指定）：

```xml
    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description> A service that implements a stateless front end in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
        <ConfigPackage Name="FrontendService.Config" Version="1.0" />
        <DataPackage Name="FrontendService.Data" Version="1.0" />
        <Resources>
            <Endpoints>
                <Endpoint Name="Endpoint1" UriScheme="http" Port="80" Protocol="http"/>
            </Endpoints>
        </Resources>
    </ServiceManifest>
```

## <a name="next-steps"></a>后续步骤
现在已部署了容器化服务，请通过阅读 [Service Fabric 应用程序生命周期](service-fabric-application-lifecycle.md)了解如何管理其生命周期。

* [Service Fabric 和容器概述](service-fabric-containers-overview.md)
* [使用 Azure CLI 与 Service Fabric 群集交互](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-deploy-container-linux/sf-container-yeoman1.png

## <a name="related-articles"></a>相关文章

* [Service Fabric 和 Azure CLI 2.0 入门](service-fabric-azure-cli-2-0.md)
* [Service Fabric XPlat CLI 入门](service-fabric-azure-cli.md)

