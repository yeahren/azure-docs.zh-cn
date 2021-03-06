---
title: Deploy the Azure Stack Development Kit | Microsoft Docs
description: Learn how to prepare the Azure Stack Development Kit and run the PowerShell script to deploy it.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 0ad02941-ed14-4888-8695-b82ad6e43c66
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/10/2017
ms.author: erikje
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 483c641c9705ad967ac9ea0c9591500288e7aa84
ms.contentlocale: zh-cn
ms.lasthandoff: 07/11/2017


---
# Deploy the Azure Stack Development Kit
<a id="deploy-the-azure-stack-development-kit" class="xliff"></a>
To deploy the development kit, you must complete the following steps:

1. [Download the deployment package](https://azure.microsoft.com/overview/azure-stack/try/?v=try) to get the Cloudbuilder.vhdx.
2. [Prepare the cloudbuilder.vhdx](#prepare-the-development-kit-host) by running the asdk-installer.ps1 script to configure the computer (the development kit host) on which you want to install development kit. After this step, the development kit host will boot to the Cloudbuilder.vhdx.
3. [Deploy the development kit](#deploy-the-development-kit) on the development kit host.

> [!NOTE]
> For best results, even if you want to use a disconnected Azure Stack environment, it is best to deploy while connected to the internet. That way, the Windows Server 2016 evaluation version can be activated at deployment time. If the Windows Server 2016 evaluation version is not activated within 10 days, it shuts down.
> 
> 

## Download and extract the development kit
<a id="download-and-extract-the-development-kit" class="xliff"></a>
1. Before you start the download, make sure that your computer meets the following prerequisites:

   * The computer must have at least 60 GB of free disk space.
   * [.NET Framework 4.6 (or a later version)](https://aka.ms/r6mkiy) must be installed.

2. [Go to the Get Started page](https://azure.microsoft.com/overview/azure-stack/try/?v=try), provide your details, and click **Submit**.
3. Under **Download the software**, click **Azure Stack Development Kit**.
4. Run the downloaded AzureStackDownloader.exe file.
5. In the **Azure Stack Development Kit Downloader** window, follow steps 1 through 5.
6. After the download completes, click **Run** to launch the MicrosoftAzureStackPOC.exe.
7. Review the License Agreement screen and information of the Self-Extractor Wizard and then click **Next**.
8. Review the Privacy Statement screen and information of the Self-Extractor Wizard and then click **Next**.
9. Select the Destination for the files to be extracted, click **Next**.
   * The default is: <drive letter>:\<current folder>\Microsoft Azure Stack
10. Review the Destination location screen and information of the Self-Extractor Wizard, and then click **Extract** to extract the CloudBuilder.vhdx (~25 GB) and ThirdPartyLicenses.rtf files. This process will take some time to complete.

> [!NOTE]
> After you extract the files, you can delete the exe and bin files to recover space on the machine. Or, you can move these files to another location so that if you need to redeploy you don’t need to download the files again.
> 
> 

## Prepare the development kit host
<a id="prepare-the-development-kit-host" class="xliff"></a>
1. Make sure that you can physically connect to the development kit host, or have physical console access (such as KVM). You must have such access after you reboot the development kit host in step 13 below.
2. Make sure the development kit host meets the [minimum requirements](azure-stack-deploy.md). You can use the [Deployment Checker for Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) to confirm your requirements.
3. Sign in as the Local Administrator to your development kit host.
4. Copy or move the CloudBuilder.vhdx file to the root of the C:\ drive (C:\CloudBuilder.vhdx).
5. Run the following script to download the development kit installer file (asdk-installer.ps1) to the c:\AzureStack_Installer folder on your development kit host.
    ```powershell
    # Variables
    $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
    $LocalPath = 'c:\AzureStack_Installer'

    # Create folder
    New-Item $LocalPath -Type directory

    # Download file
    Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
    ```
6. Open an elevated PowerShell console > navigate to the asdk-installer.ps1 script > run it > click **Prepare vhdx**.
7. On the **Select Cloudbuilder vhdx** page of the installer, browse to and select the cloudbuilder.vhdx file that you downloaded in the previous steps.
8. Optional: Check the **Add drivers** box to specify a folder containing additional drivers that you want on the host.
9. On the **Optional settings** page, provide the local administrator account for the development kit host. If you don't provide these credentials, you'll need KVM access to the host during the install process below.
10. Also on the **Optional settings** page, you have the option to set the following:
    - **Computername**: This option sets the name for the development kit host. The name must comply with FQDN requirements and must be 15 characters or less in length. The default is a random computer name generated by Windows.
    - **Time zone**: Sets the time zone for the development kit host. The default is (UTC-8:00) Pacific Time (US & Canada).
    - **Static IP configuration**: Sets your deployment to use a static IP address. Otherwise, when the installer reboots into the cloudbuilder.vhx, the network interfaces are configured with DHCP.
11. Click **Next**.
12. If you chose a static IP configuration in the previous step, you must now:
    - Select a network adapter. Make sure you can connect to the adapter before you click **Next**.
    - Make sure that the **IP address**, **Gateway**, and **DNS** values are correct and then click **Next**.
13. Click **Next** to start the preparation process.
14. When the preparation indicates **Completed**, click **Next**.
15. Click **Reboot now** to boot into the cloudbuilder.vhdx and continue the deployment process.

## Deploy the development kit
<a id="deploy-the-development-kit" class="xliff"></a>
1. Sign in as the Local Administrator to the development kit host. Use the credentials specified in the previous steps.

    > [!IMPORTANT]
    > For Azure Active Directory deployments, Azure Stack requires access to the Internet, either directly or through a transparent proxy. The deployment supports exactly one NIC for networking. If you have multiple NICs, make sure that only one is enabled (and all others are disabled) before running the deployment script in the next section.
    
2. Open an elevated PowerShell console > navigate to the asdk-installer.ps1 script > run it > click **Install**.
3. In the **Type** box, select **Azure Cloud** or **ADFS**.
    - **Azure Cloud**: Azure Active Directory is the identity provider. Use this parameter to specify a specific directory where the AAD account has global admin permissions. Full name of an AAD Directory tenant in the format of .onmicrosoft.com. 
    - **ADFS**: The default stamp Directory Service is the identity provider, the default account to sign in with is azurestackadmin@azurestack.local, and the password to use is the one you provided as part of the setup.
4. Under **Local administrator password**, in the **Password** box, type the local administrator password (which must match the current configured local administrator password), and then click **Next**.
5. Select a network adapter to use for the development kit and then click **Next**.
6. Select DHCP or static network configuration for the BGPNAT01 virtual machine.
    - **DHCP** (default): The virtual machine gets the IP network configuration from the DHCP server.
    - **Static**: Only use this option if DHCP can’t assign a valid IP address for Azure Stack to access the Internet. A static IP address must be specified with the subnetmask length (for example, 10.0.0.5/24).
7. Optionally, set the following values:
    - **VLAN ID**: Sets the VLAN ID. Only use this option if the host and AzS-BGPNAT01 must configure VLAN ID to access the physical network (and Internet). 
    - **DNS forwarder**: A DNS server is created as part of the Azure Stack deployment. To allow computers inside the solution to resolve names outside of the stamp, provide your existing infrastructure DNS server. The in-stamp DNS server forwards unknown name resolution requests to this server.
    - **Time server**: Sets a specific time server. 
8. Click **Next**. 
9. On the **Verifying network interface card properties** page, you'll see a progress bar. 
    - If it says **An update cannot be downloaded**, follow the instructions on the page.
    - When it says **Completed**, click **Next**.
10. On **Summary** page, click **Deploy**.
11. If you're using an Azure Active Directory deployment, you'll be asked to enter your Azure Active Directory global administrator account credentials.
12. The deployment process can take a few hours, during which the system automatically reboots once.
   
   > [!IMPORTANT]
   > If you want to monitor the deployment progress, sign in as azurestack\AzureStackAdmin. If you sign in as a local admin after the machine is joined to the domain, you won't see the deployment progress. Do not rerun deployment, instead sign in as azurestack\AzureStackAdmin to validate that it's running.
   > 
   > 
   
    When the deployment succeeds, the PowerShell console displays: **COMPLETE: Action ‘Deployment’**.
   
    If the deployment fails, you can rerun the installer and choose the **Rerun installation** option. Or, you can [redeploy](azure-stack-redeploy.md) it from scratch.


## Reset the password expiration to 180 days
<a id="reset-the-password-expiration-to-180-days" class="xliff"></a>

To make sure that the password for the development kit host doesn't expire too soon, follow these steps after you deploy:

1. Sign in to the development kit host as azurestack\azurestackadmin.

2. Run the following command to display the current MaxPasswordAge of 42 days: `Get-ADDefaultDomainPasswordPolicy`

3. Run the following command to update the MaxPasswordAge to 180 days:

    `Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local`   

4. Run the following command again to confirm the password age change: `Get-ADDefaultDomainPasswordPolicy`.

## Next steps
<a id="next-steps" class="xliff"></a>
[Register Azure Stack with your Azure subscription](azure-stack-register.md)

[Connect to Azure Stack](azure-stack-connect-azure-stack.md)


