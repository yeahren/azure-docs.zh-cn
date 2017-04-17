---
title: "有关 Azure 中 Windows VM 的常见问题 | Microsoft Docs"
description: "回答了通过 Resource Manager 模型创建的 Windows 虚拟机的一些常见问题。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-management
ms.assetid: 757da816-a050-4889-a010-6f75d7978eb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/17/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 6ce37a9bc4db2473c3751955e46cc9796cb051e1
ms.lasthandoff: 04/03/2017


---

# <a name="frequently-asked-question-about-windows-virtual-machines"></a>有关 Windows 虚拟机的常见问题
本文讨论了在 Azure 中使用 Resource Manager 部署模型创建的 Windows 虚拟机的一些常见问题。 有关本主题的 Linux 版本，请参阅[有关 Linux 虚拟机的常见问题](../linux/faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>我可以在 Azure VM 上运行什么程序？
所有订户都可以在 Azure 虚拟机上运行服务器软件。 有关在 Azure 中运行 Microsoft 服务器软件的支持策略的信息，请参阅 [Microsoft server software support for Azure Virtual Machines](https://support.microsoft.com/kb/2721672)（对 Azure 虚拟机中的 Microsoft 服务器软件的支持）

MSDN Azure 权益订户以及 MSDN 开发和测试即用即付订户可使用某些版本的 Windows 7、Windows 8.1 和 Windows 10 执行开发和测试任务。 有关详细信息（包括说明和限制），请参阅[适用于 MSDN 订户的 Windows 客户端映像](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/)。 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>使用虚拟机时，我可以使用多少存储？
每个数据磁盘的容量高达 1 TB。 你可以使用的数据磁盘的数目取决于虚拟机的大小。 有关详细信息，请参阅[虚拟机大小](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

Azure 存储帐户提供可用于操作系统磁盘和任意数据磁盘的存储。 每个磁盘都是一个 .vhd 文件，以页 blob 形式存储。 有关定价详细信息，请参阅 [Storage Pricing Details](https://azure.microsoft.com/pricing/details/storage/)（存储定价详细信息）。

## <a name="how-can-i-access-my-virtual-machine"></a>如何访问我的虚拟机？
使用适用于 Windows VM 的远程桌面连接 (RDP) 建立远程连接。 有关说明，请参阅[如何连接并登录到运行 Windows 的 Azure 虚拟机](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 除非将服务器配置为远程桌面服务会话主机，否则最多支持两个并发连接。  

如果在使用远程桌面时遇到问题，请参阅[对与基于 Windows 的 Azure 虚拟机的远程桌面连接进行故障排除](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 

如果熟悉 Hyper-V，可以寻找类似于 VMConnect 的工具。 Azure 不提供类似的工具，因为不支持通过控制台来访问虚拟机。

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>我是否可以使用临时磁盘（默认为 D: 驱动器）存储数据？
不要使用临时磁盘来存储数据。 它只是临时存储空间，因此存在丢失数据且数据不能恢复的风险。 将虚拟机移到另一主机时，可能会发生数据丢失的情况。 调整虚拟机大小、更新主机、主机硬件故障等都是需要移动虚拟机的原因。

如果有应用程序需要使用 D: 驱动器号，可以重新分配驱动器号以便临时磁盘使用除 D: 以外的位置。 有关说明，请参阅[更改 Windows 临时磁盘的驱动器号](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>如何更改临时磁盘的驱动器号？
可以通过移动页面文件和重新分配驱动器号来更改驱动器号，但需确保按特定顺序执行这些步骤。 有关说明，请参阅[更改 Windows 临时磁盘的驱动器号](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>我是否可以将现有 VM 添加到可用性集？
否。 不可以。如果希望 VM 成为可用性集的一部分，需要在该集内创建 VM。 目前不支持在创建 VM 之后再将其添加到可用性集。
## <a name="can-i-upload-a-virtual-machine-to-azure"></a>我是否可以将虚拟机上传到 Azure？
是的。 有关说明，请参阅 [Upload a Windows VM image to Azure](upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)（将 Windows VM 映像上载到 Azure）

## <a name="can-i-resize-the-os-disk"></a>我是否可以调整 OS 磁盘的大小？
是的。 有关说明，请参阅 [How to expand the OS drive of a Virtual Machine in an Azure Resource Group](expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)（如何扩展 Azure 资源组中虚拟机的 OS 驱动器）。

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>我是否可以复制或克隆现有的 Azure VM？
是的。 有关说明，请参阅 [How to create a copy of a Windows virtual machine in the Resource Manager deployment model](vhd-copy.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)（如何在 Resource Manager 部署模型中创建 Windows 虚拟机的副本）。

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>为什么在 Azure Resource Manager 中看不到加拿大中部和加拿大东部区域？

针对现有 Azure 订阅创建的虚拟机不会自动注册到加拿大中部和加拿大东部这两个新区域。 通过 Azure 门户使用 Azure Resource Manager 将虚拟机部署到其他任何区域时，将自动完成注册。 将虚拟机部署到其他任何 Azure 区域后，新区域可供后续虚拟机使用。

## <a name="does-azure-support-linux-vms"></a>Azure 是否支持 Linux VM？
是的。 若要快速创建 Linux VM 进行试用，请参阅[使用门户在 Azure 上创建 Linux VM](../linux/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>创建 VM 后能否向 VM 添加 NIC？
能，目前可行。 首先需停止解除分配 VM。 然后便可添加或删除 NIC（除非它是 VM 上的最后一个 NIC）。 

## <a name="are-there-any-computer-name-requirements"></a>是否有任何计算机名称要求？
是的。 计算机名称的最大长度为 15 个字符。 有关命名资源的详细信息，请参阅 [Infrastructure naming guidelines](infrastructure-naming-guidelines.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)（基础结构命名准则）。

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>创建 VM 时，用户名有什么要求？

用户名最长为 20 个字符，不能以句点（“.”）结尾。 


不允许使用以下用户名：
<table>
    <tr>
        <td style="text-align:center">administrator </td><td style="text-align:center"> admin </td><td style="text-align:center"> user </td><td style="text-align:center"> user1</td>
    </tr>
    <tr>
        <td style="text-align:center">测试 </td><td style="text-align:center"> user2 </td><td style="text-align:center"> test1 </td><td style="text-align:center"> user3</td>
    </tr>    <tr>
        <td style="text-align:center">admin1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> a</td>
    </tr>
    <tr>
        <td style="text-align:center">actuser  </td><td style="text-align:center"> adm </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> aspnet</td>
    </tr>
    <tr>
        <td style="text-align:center">backup </td><td style="text-align:center"> console </td><td style="text-align:center"> david </td><td style="text-align:center"> guest</td>
    </tr>
    <tr>
        <td style="text-align:center">john </td><td style="text-align:center"> owner </td><td style="text-align:center"> root </td><td style="text-align:center"> server</td>
    </tr>
    <tr>
        <td style="text-align:center">sql </td><td style="text-align:center"> support </td><td style="text-align:center"> support_388945a0 </td><td style="text-align:center"> sys</td>
    </tr>
    <tr>
        <td style="text-align:center">test2 </td><td style="text-align:center"> test3 </td><td style="text-align:center"> user4 </td><td style="text-align:center"> user5</td>
    </tr>
</table>

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>创建 VM 时，密码有什么要求？
密码的长度必须为 12 到 123 个字符，并满足以下 4 个复杂性要求中的 3 个要求：

* 具有小写字符
* 具有大写字符
* 具有数字
* 具有特殊字符（正则表达式匹配 [\W_]）

不允许使用以下密码：

<table>
    <tr>
        <td>abc@123 </td>
        <td>P@$$w0rd </td>
        <td>P@ssw0rd </td>
        <td>P@ssword123 </td>
        <td>Pa$$word </td>
    </tr>
    <tr>
        <td>pass@word1 </td>
        <td>Password! </td>
        <td>Password1 </td>
        <td>Password22 </td>
        <td>iloveyou! </td>
    </tr>
</table>
