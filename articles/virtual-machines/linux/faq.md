---
title: "有关 Azure 中 Linux VM 的常见问题 | Microsoft 文档"
description: "回答了通过 Resource Manager 模型创建的 Linux 虚拟机的一些常见问题。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-management
ms.assetid: 3648e09c-1115-4818-93c6-688d7a54a353
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 5a0092481cb461f26ba463f4c9bbaf114ecb1248
ms.contentlocale: zh-cn
ms.lasthandoff: 05/11/2017


---
# 有关 Linux 虚拟机的常见问题
<a id="frequently-asked-question-about-linux-virtual-machines" class="xliff"></a>
本文讨论有关在 Azure 中使用 Resource Manager 部署模型创建的 Linux 虚拟机的一些常见问题。 有关本主题的 Windows 版本，请参阅[有关 Windows 虚拟机的常见问题](../windows/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## 我可以在 Azure VM 上运行什么程序？
<a id="what-can-i-run-on-an-azure-vm" class="xliff"></a>
所有订户都可以在 Azure 虚拟机上运行服务器软件。 有关详细信息，请参阅 [Azure 认可的分发版本中的 Linux](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## 使用虚拟机时，我可以使用多少存储？
<a id="how-much-storage-can-i-use-with-a-virtual-machine" class="xliff"></a>
每个数据磁盘的容量高达 1 TB。 你可以使用的数据磁盘的数目取决于虚拟机的大小。 有关详细信息，请参阅[虚拟机大小](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

Azure 存储帐户提供可用于操作系统磁盘和任意数据磁盘的存储。 每个磁盘都是一个 .vhd 文件，以页 blob 形式存储。 有关定价详细信息，请参阅 [Storage Pricing Details](https://azure.microsoft.com/pricing/details/storage/)（存储定价详细信息）。

## 如何访问我的虚拟机？
<a id="how-can-i-access-my-virtual-machine" class="xliff"></a>
使用安全外壳 (SSH) 建立远程连接，登录到虚拟机。 请参阅如何[从 Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 或[从 Linux 和 Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 进行连接的相关说明。 默认情况下，SSH 允许的并发连接最多为 10 个。 通过编辑配置文件，可以增大此数目。

如果遇到问题，请查阅[排除安全外壳 (SSH) 连接故障](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## 我是否可以使用临时磁盘 (/dev/sdb1) 存储数据？
<a id="can-i-use-the-temporary-disk-devsdb1-to-store-data" class="xliff"></a>
不要使用临时磁盘 (/dev/sdb1) 存储数据。 它只是用于临时存储。 有丢失无法恢复的数据的风险。

## 我是否可以复制或克隆现有的 Azure VM？
<a id="can-i-copy-or-clone-an-existing-azure-vm" class="xliff"></a>
是的。 有关说明，请参阅[如何在 Resource Manager 部署模型中创建 Linux 虚拟机的副本](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## 为什么在 Azure Resource Manager 中看不到加拿大中部和加拿大东部区域？
<a id="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager" class="xliff"></a>
针对现有 Azure 订阅创建的虚拟机不会自动注册到加拿大中部和加拿大东部这两个新区域。 通过 Azure 门户使用 Azure Resource Manager 将虚拟机部署到其他任何区域时，将自动完成注册。 将虚拟机部署到其他任何 Azure 区域后，新区域可供后续虚拟机使用。

## 创建 VM 后能否向 VM 添加 NIC？
<a id="can-i-add-a-nic-to-my-vm-after-its-created" class="xliff"></a>
能，目前可行。 首先需停止解除分配 VM。 然后便可添加或删除 NIC（除非它是 VM 上的最后一个 NIC）。 

## 是否有任何计算机名称要求？
<a id="are-there-any-computer-name-requirements" class="xliff"></a>
是的。 计算机名称的最大长度为 64 个字符。 有关命名资源的详细信息，请参阅 [Infrastructure naming guidelines](infrastructure-naming-guidelines.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（基础结构命名准则）。

## 是否存在资源组名称要求？
<a id="are-there-any-resource-group-name-requirements" class="xliff"></a>
是的。 资源组名称的最大长度为 90 个字符。 有关资源组的详细信息，请参阅[基础结构资源组指南](infrastructure-resource-groups-guidelines.md)。

## 创建 VM 时，用户名有什么要求？
<a id="what-are-the-username-requirements-when-creating-a-vm" class="xliff"></a>
用户名的长度必须为 1 到 64 个字符。

不允许使用以下用户名：

<table>
    <tr>
        <td style="text-align:center">administrator </td><td style="text-align:center"> admin </td><td style="text-align:center"> user </td><td style="text-align:center"> user1</td>
    </tr>
    <tr>
        <td style="text-align:center">测试 </td><td style="text-align:center"> user2 </td><td style="text-align:center"> test1 </td><td style="text-align:center"> user3</td>
    </tr>
    <tr>
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


## 创建 VM 时，密码有什么要求？
<a id="what-are-the-password-requirements-when-creating-a-vm" class="xliff"></a>
密码的长度必须为 6 到 72 个字符，并满足以下 4 个复杂性要求中的 3 个要求：

* 具有小写字符
* 具有大写字符
* 具有数字
* 具有特殊字符（正则表达式匹配 [\W_]）

不允许使用以下密码：

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Pa$$word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Password!</td>
        <td style="text-align:center">Password1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">iloveyou!</td>
    </tr>
</table>

