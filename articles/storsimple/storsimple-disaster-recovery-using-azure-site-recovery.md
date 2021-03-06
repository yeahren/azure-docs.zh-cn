---
title: "使用 Azure Site Recovery 实现 StorSimple 文件共享的自动灾难恢复 | Microsoft 文档"
description: "介绍有关针对 Microsoft Azure StorSimple 存储中托管的文件共享创建灾难恢复解决方案的步骤和最佳实践。"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 23049a2c-055e-4d0e-b8f5-af2a87ecf53f
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/09/2017
ms.author: vidarmsft
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: 19346f2e4f2860258c421d76729abeb82f0e8987
ms.contentlocale: zh-cn
ms.lasthandoff: 06/10/2017


---
# 使用 Azure Site Recovery 针对 StorSimple 上托管的文件共享创建自动灾难恢复解决方案
<a id="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple" class="xliff"></a>
## 概述
<a id="overview" class="xliff"></a>
Microsoft Azure StorSimple 是一种混合型云存储解决方案，可解决通常与文件共享关联的非结构化数据的复杂性。 StorSimple 使用云存储作为本地解决方案的扩展，可跨本地存储和云存储自动将数据分层。 使用本地快照和云快照的集成式数据保护不需要扩建存储基础结构。

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) 是一项基于 Azure 的服务，可提供灾难恢复 (DR) 功能，对虚拟机的复制、故障转移和恢复进行协调。 Azure Site Recovery 支持一系列复制技术，可以前后一致地对虚拟机和应用程序进行复制、保护和无缝故障转移，转移目标是私有云/公用云或托管商的云。

使用 Azure Site Recovery、虚拟机复制和 StorSimple 云快照功能，可以保护整个文件服务器环境。 发生中断时，可以在 Azure 中使用单键恢复功能使文件系统在几分钟内联机。

本文档详细说明如何为 StorSimple 存储上托管的文件共享创建灾难恢复解决方案，以及使用单键恢复计划来执行计划内、计划外及测试故障转移。 简单而言，本文档说明如何修改 Azure Site Recovery 保管库中的恢复计划，以便在发生灾难时实现 StorSimple 故障转移。 此外，介绍了支持的配置与先决条件。 本文档假设读者熟悉 Azure Site Recovery 和 StorSimple 体系结构的基本概念。

## 支持的 Azure Site Recovery 部署选项
<a id="supported-azure-site-recovery-deployment-options" class="xliff"></a>
客户可以将文件服务器部署为在 Hyper-V 或 VMware 上运行的物理服务器或虚拟机 (VM)，然后基于从 StorSimple 存储中划分出来的卷创建文件共享。 Azure Site Recovery 可以保护辅助站点或 Azure 中的物理部署与虚拟部署。 本文档详细介绍某个 DR 解决方案，它使用 Azure 作为 Hyper-V 上托管的文件服务器 VM 的恢复站点，在 StorSimple 存储中使用文件共享。 文件服务器 VM 位于 VMware VM 或物理计算机上的其他方案也可以通过类似的方式实现。

## 先决条件
<a id="prerequisites" class="xliff"></a>
使用 Azure Site Recovery 针对 StorSimple 存储上托管的文件共享实现单键灾难恢复解决方案时，必须满足以下先决条件：

* 已在 Hyper-V 或 VMware 或物理计算机上托管本地 Windows Server 2012 R2 文件服务器 VM
* 已在 Azure StorSimple Manager 中注册本地 StorSimple 存储设备
* 已在 Azure StorSimple Manager（可以保持关闭状态）中创建 StorSimple Cloud Appliance
* 已在 StorSimple 存储设备上配置的卷中托管文件共享
* 已在 Microsoft Azure 订阅中创建 [Azure Site Recovery 服务保管库](../site-recovery/site-recovery-vmm-to-vmm.md)

此外，如果 Azure 是恢复站点，则可以在 VM 上运行 [Azure 虚拟机准备情况评估工具](http://azure.microsoft.com/downloads/vm-readiness-assessment/)，确保这些 VM 与 Azure VM 和 Azure Site Recovery 服务兼容。

为避免出现延迟问题（可能会导致成本增加），请确保在同一区域中创建 StorSimple Cloud Appliance、自动化帐户和存储帐户。

## 为 StorSimple 文件共享启用 DR
<a id="enable-dr-for-storsimple-file-shares" class="xliff"></a>
若要实现完整复制与恢复，本地环境的每个组件都需要受到保护。 本部分介绍如何执行以下操作：

* 设置 Active Directory 和 DNS 复制（可选）
* 使用 Azure Site Recovery 保护文件服务器 VM
* 保护 StorSimple 卷
* 配置网络

### 设置 Active Directory 和 DNS 复制（可选）
<a id="set-up-active-directory-and-dns-replication-optional" class="xliff"></a>
如果想要保护运行 Active Directory 和 DNS 的计算机，使它们能够在 DR 站点中可供使用，需要明确地对它们进行保护（以便在故障转移之后可以使用身份验证来访问文件服务器）。 根据客户本地环境的复杂性，可以使用两个建议的选项。

#### 选项 1
<a id="option-1" class="xliff"></a>
如果客户的整个本地网站中只有少量的应用程序和一个域控制器，并且需要故障转移整个站点，我们建议使用 Azure Site Recovery 复制将域控制器计算机复制到辅助站点（适用于站点到站点以及站点到 Azure 方案）。

#### 方法 2
<a id="option-2" class="xliff"></a>
如果客户有大量应用程序，正在运行 Active Directory 林，并且每次只故障转移少量的应用程序，我们建议在 DR 站点（辅助站点或 Azure 中）另外设置一个域控制器。

请参阅 [Automated DR solution for Active Directory and DNS using Azure Site Recovery](../site-recovery/site-recovery-active-directory.md)（使用 Azure Site Recovery 为 Active Directory 和 DNS 创建自动 DR 解决方案），获取在 DR 站点上提供域控制器的说明。 本文档余下内容假设 DR 站点上提供了域控制器。

### 使用 Azure Site Recovery 保护文件服务器 VM
<a id="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm" class="xliff"></a>
此步骤需要准备本地服务器环境、创建并准备 Azure Site Recovery 保管库，以及为 VM 启用文件保护。

#### 准备本地文件服务器环境
<a id="to-prepare-the-on-premises-file-server-environment" class="xliff"></a>
1. 将“用户帐户控制”设置为“永不通知”。 只有这样设置，才可以在通过 Azure Site Recovery 故障转移后使用 Azure 自动化脚本连接 iSCSI 目标。

   1. 按 Windows 键 + Q 并搜索 **UAC**。
   2. 选择“更改用户帐户控制设置”。
   3. 将底部横条朝“永不通知”方向拖动。
   4. 单击“确定”，然后在出现提示时选择“是”。

      ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image1.png)
2. 在每个文件服务器 VM 上安装 VM 代理。 必须这样做才能在已故障转移的 VM 上运行 Azure 自动化脚本。

   1. [将代理下载](http://aka.ms/vmagentwin)到 `C:\\Users\\<username>\\Downloads`。
   2. 以管理员模式（以管理员身份运行）打开 Windows PowerShell，然后输入以下命令导航到下载位置：

      `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`

      > [!NOTE]
      > 根据具体的版本，文件名可能有所不同。
      >
      >
3. 单击“下一步”。
4. 接受**协议条款**，然后单击“下一步”。
5. 单击“完成” 。
6. 使用从 StorSimple 存储中划分出来的卷创建文件共享。 有关详细信息，请参阅 [Use the StorSimple Manager service to manage volumes](storsimple-manage-volumes.md)（使用 StorSimple Manager 服务管理卷）。

   1. 在本地 VM 上，按 Windows 键 + Q 并搜索 **iSCSI**。
   2. 选择“iSCSI 发起程序”。
   3. 选择“配置”选项卡并复制发起程序的名称。
   4. 登录到 [Azure 门户](https://portal.azure.com/)。
   5. 选择“StorSimple”选项卡，然后选择包含物理设备的 StorSimple Manager 服务。
   6. 创建卷容器，然后创建卷。 （这些卷供文件服务器 VM 上的文件共享使用）。 创建卷时，请复制发起程序名称并为“访问控制记录”提供适当的名称。
   7. 选择“配置”选项卡并记下设备的 IP 地址。
   8. 在本地 VM 上，再次转到“iSCSI 发起程序”，然后在“快速连接”部分中输入 IP。 单击“快速连接”（设备现在应已连接）。
   9. 打开 Azure 门户，然后选择“卷和设备”选项卡。 单击“自动配置”。 应会出现刚刚创建的卷。
   10. 在门户中选择“设备”选项卡，然后选择“创建新虚拟设备”。 （此虚拟设备将在发生故障转移时使用）。 可将此新虚拟设备保持脱机状态，以免产生额外的费用。 若要使虚拟机脱机，请转到门户的“虚拟机”部分，然后关闭该虚拟机。
   11. 返回本地 VM 并打开磁盘管理（按 Windows 键 + X 并选择“磁盘管理”）。
   12. 可以看到一些附加的磁盘（数量取决于创建的卷数）。 右键单击第一个磁盘，选择“初始化磁盘”，然后选择“确定”。 右键单击“未分配”部分，选择“新建简单卷”，为卷分配一个盘符，然后完成向导操作。
   13. 针对所有磁盘重复步骤 I。 现在，可以在 Windows 资源管理器中的“此电脑”上看到所有磁盘。
   14. 使用文件和存储服务角色在这些卷上创建文件共享。

#### 创建和准备 Azure Site Recovery 保管库
<a id="to-create-and-prepare-an-azure-site-recovery-vault" class="xliff"></a>
在保护文件服务器 VM 之前，请参阅 [Azure Site Recovery 文档](../site-recovery/site-recovery-hyper-v-site-to-azure.md)了解 Azure Site Recovery。

#### 启用保护
<a id="to-enable-protection" class="xliff"></a>
1. 通过 Azure Site Recovery 将 iSCSI 目标与要保护的本地 VM 断开连接：

   1. 按 Windows 键 + Q 并搜索 **iSCSI**。
   2. 选择“设置 iSCSI 发起程序”。
   3. 断开以前连接的 StorSimple 设备。 或者，可以在启用保护时将文件服务器关闭几分钟。

   > [!NOTE]
   > 这将导致文件共享暂时不可用。
   >
   >
2. 通过 Azure Site Recovery 门户为文件服务器 VM [启用虚拟机保护](../site-recovery/site-recovery-hyper-v-site-to-azure.md#enable-replication)。
3. 初始同步开始时，可以再次重新连接目标。 转到 iSCSI 发起程序，选择 StorSimple 设备，然后单击“连接”。
4. 当同步完成且 VM 的状态为“受保护”时，请选择 VM，选择“配置”选项卡，然后相应地更新 VM 的网络（这是已故障转移的 VM 所属的网络）。 如果网络未显示，则表示同步仍在进行。

### 保护 StorSimple 卷
<a id="enable-protection-of-storsimple-volumes" class="xliff"></a>
如果没有为 StorSimple 卷选择“为此卷启用默认备份”选项，请转到 StorSimple Manager 服务中的“备份策略”，然后为所有卷创建适当的备份策略。 建议将备份频率设置为要查看的应用程序的恢复点目标 (RPO)。

### 配置网络
<a id="configure-the-network" class="xliff"></a>
对于文件服务器 VM，请在 Azure Site Recovery 中配置网络设置，使 VM 网络能够在故障转移之后连接到正确的 DR 网络。

可以在“复制的项”选项卡中选择要配置网络设置的 VM，如下图中所示。

![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image2.png)

## 创建恢复计划
<a id="create-a-recovery-plan" class="xliff"></a>
可以在 ASR 中创建恢复计划，将文件共享的故障转移过程自动化。 发生中断时，只需按一下鼠标就能在几分钟内让文件共享重新联机。 若要启用此自动化功能，需要一个 Azure 自动化帐户。

#### 创建自动化帐户
<a id="to-create-an-automation-account" class="xliff"></a>
1. 转到 Azure 门户 &gt;“自动化”部分。
2. 单击“+ 添加”按钮，打开下面的边栏选项卡。

   ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image11.png)

   * 名称 - 输入新的自动化帐户
   * 订阅 - 选择订阅
   * 资源组 - 创建新的/选择现有的资源组
   * 位置 - 选择位置，将该帐户保留在创建 StorSimple 云设备和存储帐户的同一个地区/区域。
   * 创建 Azure 运行方式帐户 - 选择“是”选项。

3. 转到自动化帐户，依次单击“Runbook”&gt;“浏览库”，将所需的全部 runbook 导入该自动化帐户。
4. 通过在库中查找“灾难恢复”标记，添加以下 runbook：

   * 在测试故障转移 (TFO) 后清理 StorSimple 卷
   * 故障转移 StorSimple 卷容器
   * 故障转移后在 StorSimple 设备上装载卷
   * 在 Azure VM 中卸载自定义脚本扩展
   * 启动 StorSimple 虚拟设备

     ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image3.png)

5. 通过以下方式发布所有脚本：在自动化帐户中选择 runbook，依次单击“编辑”&gt;“发布”，然后在显示验证消息时单击“是”。 完成此步骤后，“Runbook”选项卡将如下所示：

    ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image4.png)

6. 在自动化帐户中，选择“资产”选项卡 &gt; 依次单击“变量”&gt;“添加变量”，然后添加以下变量。 可以选择将这些资产加密。 这些变量特定于恢复计划。 如果恢复计划（将在下一步骤中创建）名为 TestPlan，则变量应该是 TestPlan-StorSimRegKey、TestPlan-AzureSubscriptionName，等等。

   * *RecoveryPlanName***-StorSimRegKey**：StorSimple Manager 服务的注册密钥。
   * *RecoveryPlanName***-AzureSubscriptionName**：Azure 订阅的名称。
   * *RecoveryPlanName***-ResourceName**：包含 StorSimple 设备的 StorSimple 资源的名称。
   * *RecoveryPlanName***-DeviceName**：必须故障转移的设备。
   * *RecoveryPlanName***-VolumeContainers**：需故障转移的设备上显示的卷容器逗号分隔字符串，例如 volcon1、volcon2、volcon3。
   * *RecoveryPlanName***-TargetDeviceName**：容器故障转移之后所在的 StorSimple Cloud Appliance。
   * *RecoveryPlanName***-TargetDeviceDnsName**：目标设备的服务名称（可以在“虚拟机”部分中找到：服务名称与 DNS 名称相同）。
   * *RecoveryPlanName***-StorageAccountName**：存储脚本（必须在已故障转移的 VM 上运行）的存储帐户名。 可以是具有一些空间暂时存储脚本的任何存储帐户。
   * *RecoveryPlanName***-StorageAccountKey**：上述存储帐户的访问密钥。
   * *RecoveryPlanName***-ScriptContainer**：存储脚本的云容器的名称。 如果该容器不存在，系统会创建该容器。
   * *RecoveryPlanName***-VMGUIDS**：保护 VM 时，Azure Site Recovery 将为每个 VM分配唯一 ID，该 ID 可提供已故障转移的 VM 的详细信息。 若要获取 VMGUID，请选择“恢复服务”选项卡，然后依次单击“受保护的项”&gt;“保护组”&gt;“计算机”&gt;“属性”。 如果有多个 VM，请以逗号分隔字符串的形式添加 GUID。
   * *RecoveryPlanName***-AutomationAccountName** – 已在其中添加 Runbook 和资产的自动化帐户的名称。

  例如，如果恢复计划名称为 fileServerpredayRP，在添加了所有资产后，“凭据” & “变量”选项卡应如下所示。

   ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image5.png)

7. 转到“恢复服务”部分，然后选择前面创建的 Azure Site Recovery 保管库。
8. 从“管理”组中选择“恢复计划 (Site Recovery)”选项，然后创建新的恢复计划，如下所示：

   a.  单击“+ 恢复计划”按钮，将打开以下边栏选项卡。

      ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image6.png)

   b.保留“数据库类型”设置，即设置为“共享”。  输入恢复计划名称，选择源、目标和部署模型的值。

   c.  从保护组中选择要包含在恢复计划中的 VM，然后单击“确定”按钮。

   d.  选择之前创建的恢复计划，单击“自定义”按钮以打开恢复计划自定义视图。

   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。  右键单击“所有组关闭”，然后单击“添加预操作”。

   f.  打开“插入操作”边栏选项卡，输入名称，在“运行位置”选项中选择“主端”选项，选择自动化帐户（在其中添加了 runbook），然后选择“Failover-StorSimple-Volume-Containers”runbook。

   g.  右键单击“组 1: 启动”并单击“添加保护项”选项，然后选择要在恢复计划中进行保护的 VM 并单击“确定”按钮。 （可选）如果已选择 VM。

   h.  右键单击“组 1: 启动”并单击“后操作”选项，然后添加以下所有脚本：

   * Start-StorSimple-Virtual-Appliance Runbook
   * Fail over-StorSimple-volume-containers Runbook
   * Mount-volumes-after-failover Runbook
   * Uninstall-custom-script-extension Runbook

   i.  在同一个“组 1: 后续步骤”部分中，在上面 4 个脚本后面添加一个手动操作。 通过此操作可以确认所有配置是否正常运行。 只需为测试故障转移添加此操作（因此选中“测试故障转移”复选框即可）。

   j.  执行手动操作后，使用针对其他 runbook 执行的相同过程添加 **Cleanup** 脚本。 **保存**恢复计划。

    > [!NOTE]
    > 运行测试故障转移时，应在手动操作步骤中验证所有配置，因为在目标设备上克隆的 StorSimple 卷将在手动操作完成后执行清理的过程中被删除。
    >

    ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image7.png)

## 执行测试故障转移
<a id="perform-a-test-failover" class="xliff"></a>
请参阅 [Active Directory DR 解决方案](../site-recovery/site-recovery-active-directory.md)随附的指南，了解在测试故障转移期间有关 Active Directory 的具体注意事项。 执行测试故障转移时，本地设置不受干扰。 已附加到本地 VM 的 StorSimple 卷将克隆到 Azure 上的 StorSimple Cloud Appliance。 Azure 中用于测试的 VM 将会启动，云卷将附加到 VM。

#### 执行测试故障转移
<a id="to-perform-the-test-failover" class="xliff"></a>
1. 在 Azure 门户中，选择 Site Recovery 保管库。
2. 单击为文件服务器 VM 创建的恢复计划。
3. 单击“测试故障转移”。
4. 选择 Azure VM 在发生故障转移后要连接到的 Azure 虚拟网络。

   ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image8.png)
5. 单击“确定”开始故障转移。 若要跟踪进度，可以单击 VM 以打开其属性，或者在保管库名称 &gt;“作业”&gt;“Site Recovery 作业”中单击“测试故障转移”作业。
6. 故障转移完成后，还应该能够看到副本 Azure 计算机显示在 Azure 门户 &gt;“虚拟机”中。 可以执行验证。
7. 完成验证后，单击“验证完成”。 这将清除 StorSimple 卷并关闭 StorSimple 云设备。
8. 完成后，在恢复计划上单击“清理测试故障转移”。 在“说明”中，记录并保存与测试性故障转移相关联的任何观测结果。 此时会删除在测试故障转移期间创建的虚拟机。

## 执行计划内故障转移
<a id="perform-a-planned-failover" class="xliff"></a>
   在计划内故障转移期间，本地文件服务器 VM 将正常关闭，并为 StorSimple 设备上的卷创建云备份快照。 StorSimple 卷将故障转移到虚拟设备，副本 VM 将在 Azure 上启动，卷将附加到 VM。

#### 执行计划内故障转移
<a id="to-perform-a-planned-failover" class="xliff"></a>
1. 在 Azure 门户中，依次选择“恢复服务”保管库 &gt;“恢复计划 (Site Recovery)”&gt;“recoveryplan_name”（为文件服务器 VM 创建的恢复计划名称）。
2. 在“恢复计划”边栏选项卡上，依次单击“更多”&gt;“计划的故障转移”。

   ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image9.png)
3. 在“确认计划的故障转移”边栏选项卡上，选择源和目标位置并选择目标网络，然后单击勾选图标 ✓ 开始故障转移过程。
4. 副本虚拟机在创建后处于待提交状态。 单击“提交”提交故障转移。
5. 复制完成后，虚拟机将在辅助位置启动。

## 执行故障转移
<a id="perform-a-failover" class="xliff"></a>
在计划外故障转移期间，StorSimple 卷将故障转移到虚拟设备，副本 VM 将在 Azure 上启动，卷将附加到 VM。

#### 若要执行故障转移
<a id="to-perform-a-failover" class="xliff"></a>
1. 在 Azure 门户中，依次选择“恢复服务”保管库 &gt;“恢复计划 (Site Recovery)”&gt;“recoveryplan_name”（为文件服务器 VM 创建的恢复计划名称）。
2. 在“恢复计划”边栏选项卡上，依次单击“更多”&gt;“故障转移”。
3. 在“确认故障转移”边栏选项卡上，选择源和目标位置。
4. 选择“关闭虚拟机并同步最新数据”，指定 Site Recovery 应尝试关闭受保护的虚拟机并同步数据，以便对最新版的数据进行故障转移。
5. 故障转移后，虚拟机处于待提交状态。 单击“**提交**”以提交故障转移。


## 执行故障回复
<a id="perform-a-failback" class="xliff"></a>
在故障回复期间，StorSimple 卷容器在创建备份之后故障回复到物理设备。

#### 执行故障回复
<a id="to-perform-a-failback" class="xliff"></a>
1. 在 Azure 门户中，依次选择“恢复服务”保管库 &gt;“恢复计划 (Site Recovery)”&gt;“recoveryplan_name”（为文件服务器 VM 创建的恢复计划名称）。
2. 在“恢复计划”边栏选项卡上，依次单击“更多”&gt;“计划的故障转移”。
3. 选择源和目标位置，选择合适的数据同步和 VM 创建选项。
4. 单击“确定”按钮开始故障回复过程。

   ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image10.png)

## 最佳实践
<a id="best-practices" class="xliff"></a>
### 容量规划和准备情况评估
<a id="capacity-planning-and-readiness-assessment" class="xliff"></a>
#### Hyper-V 站点
<a id="hyper-v-site" class="xliff"></a>
使用 [User Capacity Planner 工具](http://www.microsoft.com/download/details.aspx?id=39057)为 Hyper-V 副本环境设计服务器、存储和网络基础结构。

#### Azure
<a id="azure" class="xliff"></a>
可以在 VM 上运行 [Azure 虚拟机准备情况评估工具](http://azure.microsoft.com/downloads/vm-readiness-assessment/)，确保这些 VM 与 Azure VM 和 Azure Site Recovery 服务兼容。 准备情况评估工具将检查 VM 配置，当配置与 Azure 不兼容时会发出警告。 例如，如果 C: 磁盘大小超过 127 GB，它就会发出警告。

容量规划至少包括两个重要过程：

* 将本地 Hyper-V VM 映射为 Azure VM 大小（例如 A6、A7、A8 和 A9）。
* 确定所需的 Internet 带宽。

## 限制
<a id="limitations" class="xliff"></a>
* 目前只能故障转移 1 台 StorSimple 设备（故障转移到单个 StorSimple Cloud Appliance）。 不支持跨越多台 StorSimple 设备的文件服务器方案。
* 如果在为 VM 启用保护时发生错误，请确保已断开连接 iSCSI 目标。
* 由于备份策略跨越不同卷容器而分组在一起的所有卷容器将一同故障转移。
* 在卷容器中选择的所有卷将故障转移。
* 总大小超过 64 TB 的卷无法故障转移，因为单个 StorSimple Cloud Appliance 的容量上限为 64 TB。
* 如果计划内/计划外故障转移失败并且 Azure 中已创建 VM，请不要清理 VM， 而应该执行故障回复。 如果删除了 VM，则无法再次打开本地 VM。
* 故障转移后如果看不到卷，请转到 VM，打开“磁盘管理”，重新扫描磁盘，然后将卷联机。
* 在某些情况下，DR 站点中的盘符可能与本地盘符不同。 如果发生此情况，需要在故障转移完成后手动更正问题。
* 应该针对在自动化帐户中作为资产输入的 Azure 凭据禁用多重身份验证。 如果不禁用这种身份验证，脚本将无法自动运行，恢复计划将会失败。
* 故障转移作业超时：如果故障转移卷容器花费的时间超过每个脚本的 Azure Site Recovery 限制（目前为 120 分钟），StorSimple 脚本将会超时。
* 备份作业超时：如果备份卷花费的时间超过每个脚本的 Azure Site Recovery 限制（目前为 120 分钟），StorSimple 脚本将会超时。

  > [!IMPORTANT]
  > 请从 Azure 门户手动运行备份，然后再次运行恢复计划。

* 克隆作业超时：如果克隆卷花费的时间超过每个脚本的 Azure Site Recovery 限制（目前为 120 分钟），StorSimple 脚本将会超时。
* 时间同步错误：StorSimple 脚本出错，指出尽管在门户中备份成功，但事实上备份失败。 发生此问题的可能原因是 StorSimple 设备的时间没有与时区中的当前时间同步。

  > [!IMPORTANT]
  > 请将设备时间与时区中的当前时间同步。

* 设备故障转移错误：如果运行恢复计划时进行设备故障转移，StorSimple 脚本可能会失败。

  > [!IMPORTANT]
  > 请在设备故障转移完成后重新运行恢复计划。


## 摘要
<a id="summary" class="xliff"></a>
使用 Azure Site Recovery，可以针对在 StorSimple 存储中托管文件共享的文件服务器 VM 创建完整的自动化灾难恢复计划。 发生服务中断时，可以在数秒内从任何位置启动故障转移，在数分钟内启动和运行应用程序。

