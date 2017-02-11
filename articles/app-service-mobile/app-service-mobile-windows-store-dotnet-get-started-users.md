---
title: "向通用 Windows 平台 (UWP) 应用添加身份验证 | Microsoft Docs"
description: "了解如何使用 Azure App Service 移动应用通过各种标识提供者（包括 AAD、Google、Facebook、Twitter 和 Microsoft）对通用 Windows 平台 (UWP) 应用的用户进行身份验证。"
services: app-service\mobile
documentationcenter: windows
author: adrianhall
manager: erikre
editor: 
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 17ced2fb19b2beb057b67a0aff2f67fb2bfe49e9


---
# <a name="add-authentication-to-your-windows-app"></a>向 Windows 应用添加身份验证
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

本主题演示如何向移动应用添加基于云的身份验证。 在本教程中，使用 Azure 应用服务支持的标识提供者向移动应用的通用 Windows 平台 (UWP) 快速入门项目添加身份验证。 在移动应用后端成功进行身份验证和授权后，显示用户 ID 值。

本教程基于移动应用快速入门。 必须先完成[移动应用入门](app-service-mobile-windows-store-dotnet-get-started.md)教程。

## <a name="a-nameregisteraregister-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>注册应用以进行身份验证并配置应用服务
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="a-namepermissionsarestrict-permissions-to-authenticated-users"></a><a name="permissions"></a>将权限限制给已经过身份验证的用户
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

现在，可以验证是否已禁用对后端的匿名访问。 将 UWP 应用项目设为启动项目后，部署并运行该应用；验证启动该应用后，是否会引发状态代码为 401（“未授权”）的未处理异常。 发生此异常的原因是应用尝试以未经身份验证的用户身份访问移动应用代码，但 *TodoItem* 表现在要求身份验证。

接下来，更新应用，以便在从应用服务请求资源之前对用户进行身份验证。

## <a name="a-nameadd-authenticationaadd-authentication-to-the-app"></a><a name="add-authentication"></a>向应用程序添加身份验证
1. 在 UWP 应用项目文件 MainPage.cs 中，将以下代码片段添加到 MainPage 类：
   
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;
   
        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message =
                    string.Format("You are now signed in - {0}", user.UserId);
   
                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }
   
    此代码使用 Facebook 登录对用户进行身份验证。 如果使用的标识提供商不是 Facebook，请将上述 **MobileServiceAuthenticationProvider** 的值更改为你的提供商的值。
2. 注释掉或删除现有 **OnNavigatedTo** 方法重写中对 **ButtonRefresh_Click** 方法（或 **InitLocalStoreAsync** 方法）的调用。 这可以防止在对用户进行身份验证之前加载数据。 接下来，向应用添加用于触发身份验证的“登录”按钮。
3. 将以下代码段添加到 MainPage 类：
   
        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Switch the buttons and load items from the mobile app.
                ButtonLogin.Visibility = Visibility.Collapsed;
                ButtonSave.Visibility = Visibility.Visible;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
4. 打开 MainPage.xaml 项目文件，找到定义“保存”按钮的元素，将其替换为以下代码：
   
        <Button Name="ButtonSave" Visibility="Collapsed" Margin="0,8,8,0" 
                Click="ButtonSave_Click">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Add"/>
                <TextBlock Margin="5">Save</TextBlock>
            </StackPanel>
        </Button>
        <Button Name="ButtonLogin" Visibility="Visible" Margin="0,8,8,0" 
                Click="ButtonLogin_Click" TabIndex="0">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Permissions"/>
                <TextBlock Margin="5">Sign in</TextBlock> 
            </StackPanel>
        </Button>
5. 按 F5 键运行该应用，单击“登录”按钮，然后使用所选的标识提供者登录到该应用。 成功登录后，该应用运行时不会出错，用户能够查询后端，并对数据进行更新。

## <a name="a-nametokensastore-the-authentication-token-on-the-client"></a><a name="tokens"></a>在客户端上存储身份验证令牌
前一示例显示了标准登录，这要求在该应用每次启动时客户端同时联系标识提供者和应用服务。 此方法不仅效率低下，而且如果很多客户尝试同时启动你的应用，你会遇到关于使用率的问题。 更好的方法是缓存应用服务返回的授权令牌，然后在使用基于提供者的登录之前首先尝试使用此令牌。

> [!NOTE]
> 无论使用的是客户端管理的还是服务管理的身份验证，都可以缓存应用服务颁发的令牌。 本教程使用服务管理的身份验证。
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>后续步骤
完成此基本身份验证教程后，请考虑继续学习以下教程之一：

* [向应用添加推送通知](app-service-mobile-windows-store-dotnet-get-started-push.md)  
   了解如何为应用添加推送通知支持，以及如何将移动应用后端配置为使用 Azure 通知中心发送推送通知。
* [为应用启用脱机同步](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
   了解如何使用移动应用后端向应用添加脱机支持。 脱机同步允许最终用户与移动应用进行交互（查看、添加或修改数据），即使在没有网络连接时也是如此。

<!-- URLs. -->
[开始使用移动应用]: app-service-mobile-windows-store-dotnet-get-started.md




<!--HONumber=Nov16_HO3-->

