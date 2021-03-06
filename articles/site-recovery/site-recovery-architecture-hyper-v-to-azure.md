---
title: "如何使用 Azure Site Recovery 将 Hyper-V 复制到 Azure？ | Microsoft Docs"
description: "本文概述通过 Azure Site Recovery 服务将本地 Hyper-V VM 复制到 Azure 所用的组件和体系结构。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/23/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 552794a2c7bba6f551ada5f431cacc236e7732a4
ms.contentlocale: zh-cn
ms.lasthandoff: 06/15/2017

---


# <a name="how-does-hyper-v-replication-to-azure-work-in-site-recovery"></a>如何在 Site Recovery 中完成从 Hyper-V 到 Azure 的复制？


本文介绍使用 [Azure Site Recovery](site-recovery-overview.md) 服务将本地 Hyper-V 虚拟机复制到 Azure 时涉及的组件和进程。

Site Recovery 可复制使用或不使用 System Center Virtual Machine Manager (VMM) 托管的 Hyper-V 群集和独立主机上的 Hyper-V VM。

请将任何评论发布到本文底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。



## <a name="architectural-components"></a>体系结构组件

将 Hyper-V VM 复制到 Azure 涉及多个组件。

**组件** | **位置** | **详细信息**
--- | --- | ---
**Azure** | 在 Azure 中，需要创建 Microsoft Azure 帐户、Azure 存储帐户和 Azure 网络。 | 复制的数据存储在存储帐户中；从本地站点故障转移时，将使用复制的数据创建 Azure VM。<br/><br/> 创建 Azure VM 后，它们将连接到 Azure 虚拟网络。
**VMM 服务器** | Hyper-V 主机位于 VMM 云中 | 如果 Hyper-V 主机在 VMM 云中托管，则可在恢复服务保管库中注册 VMM 服务器。<br/><br/> 在 VMM 服务器上，可以安装 Site Recovery 提供程序来协调与 Azure 的复制。<br/><br/> 若要配置网络映射，需设置逻辑网络和 VM 网络。 VM 网络应链接到与云关联的逻辑网络。
**Hyper-V 主机** | 可使用或不使用 VMM 服务器部署 Hyper-V 主机和群集。 | 如果没有 VMM 服务器，则会将 Site Recovery 提供程序安装在主机上，以便协调通过 Internet 进行的与 Site Recovery 的复制。 如果有 VMM 服务器，则会将提供程序安装在其上，而不是安装在主机上。<br/><br/> 恢复服务代理安装在主机上，用于处理数据复制。<br/><br/> 来自提供程序和代理的通信都是安全且经过加密的。 Azure 存储中的复制数据也已加密。
**Hyper-V VM** | 需要一个或多个在 Hyper-V 主机服务器上运行的 VM。 | 不需在 VM 上显式安装任何内容。

在[支持矩阵](site-recovery-support-matrix-to-azure.md)中了解每个组件的部署先决条件和要求。

图 1：Hyper-V 站点到 Azure 的复制

![组件](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

图 2：VMM 云中 Hyper-V 到 Azure 的复制

![组件](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)


## <a name="replication-process"></a>复制过程

图 3：从 Hyper-V 到 Azure 的复制和恢复过程

![工作流](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

### <a name="enable-protection"></a>启用保护

1. 为 Hyper-V VM 启用保护以后，就会在 Azure 门户中或本地启动“启用保护”。
2. 该作业将检查计算机是否符合先决条件，然后调用 [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx)，以使用配置的设置来设置复制。
3. 该作业通过调用 [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) 方法启动初始复制，以便初始化完整的 VM 复制，然后将 VM 的虚拟磁盘发送到 Azure。
4. 可以在“作业”选项卡中监视作业。
        ![作业列表](media/site-recovery-hyper-v-azure-architecture/image1.png)
        ![启用保护向下钻取](media/site-recovery-hyper-v-azure-architecture/image2.png)

### <a name="replicate-the-initial-data"></a>复制初始数据

1. 当触发初始复制时，系统会拍摄一个 [Hyper-V VM 快照](https://technet.microsoft.com/library/dd560637.aspx)。
2. 虚拟硬盘是逐一复制的，直至全部复制到 Azure 为止。 可能需要一段时间才能完成，具体取决于 VM 大小和网络带宽。 若要优化你的网络使用，请参阅[如何管理本地到 Azure 保护网络带宽使用](https://support.microsoft.com/kb/3056159)。
3. 如果在初始复制期间发生磁盘更改，Hyper-V 副本复制跟踪器将跟踪这些更改，并将其记录在 Hyper-V 复制日志 (.hrl) 中。 这些文件位于与磁盘相同的文件夹中。 每个磁盘都有一个关联的 .hrl 文件，该文件将发送到辅助存储。
4. 当初始复制正在进行时，快照和日志将占用磁盘资源。
5. 当初始复制完成时，将删除 VM 快照。 日志中的增量磁盘更改会进行同步，并合并到父磁盘中。


### <a name="finalize-protection"></a>完成保护

1. 初始复制完成后，“在虚拟机上完成保护”作业将配置网络和其他复制后设置，使虚拟机受到保护。
    ![完成保护作业](media/site-recovery-hyper-v-azure-architecture/image3.png)
2. 如果要复制到 Azure，可能需要调整虚拟机的设置，使其随时可进行故障转移。 此时，你可以运行测试故障转移以检查一切是否按预期工作。

### <a name="replicate-the-delta"></a>复制增量

1. 在完成初始复制后，根据复制设置开始增量同步。
2. Hyper-V 副本复制跟踪器跟踪对虚拟硬盘所做的更改，并将其另存为 .hrl 文件。 为复制配置的每个磁盘都有一个关联的 .hrl 文件。 在初始复制完成后，此日志会被发送到客户的存储帐户中。 当日志正处于传输到 Azure 的过程中时，主磁盘中的变更将被记录到同一目录的另一日志文件中。
3. 在初始复制和增量复制过程中，可以在“VM”视图中监视 VM。 [了解详细信息](site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machines)。  

### <a name="synchronize-replication"></a>同步复制

1. 如果增量复制失败且完整复制因为带宽或时间限制而需要大量开销，则会将 VM 标记为需要重新同步。 例如，如果 .hrl 文件达到磁盘大小的 50%，系统会将 VM 标记为重新同步。
2.  重新同步通过计算源虚拟机磁盘和目标虚拟机的校验和并只发送增量数据来最大程度地减小发送的数据量。 重新同步使用固定块区块算法，其中源文件和目标文件被分到固定区块。 系统会针对每个区块生成校验和，然后进行比较，以确定源中的哪些区块需要应用到目标。
3. 重新同步完成后，应会恢复正常增量复制。 默认情况下，重新同步安排为在非工作时间自动运行，但你可以手动重新同步虚拟机。 例如，如果发生网络中断或其他中断，可以继续重新同步。 为此，请在“门户”>“重新同步”中选择 VM。

    ![手动重新同步](media/site-recovery-hyper-v-azure-architecture/image4.png)


### <a name="retry-logic"></a>重试逻辑

如果发生复制错误，将会进行内置重试。 此逻辑可以分为以下两类：

**类别** | **详细信息**
--- | ---
**不可恢复的错误** | 不尝试执行任何重试操作。 VM 状态为“严重”，并且需要管理员干预。 这些错误的示例包括：VHD 链断裂；副本 VM 的状态无效；网络身份验证错误：授权错误；“找不到 VM”错误（适用于独立的 Hyper-V 服务器）
**可恢复的错误** | 使用从第一次尝试开始增大重试间隔时间（1、2、4、8、10 分钟）的指数退避算法，在到达复制间隔时间后重试。 如果错误仍然存在，则每隔 30 分钟重试一次。 示例包括：网络错误；磁盘空间不足错误；内存不足的情况 |



## <a name="failover-and-failback-process"></a>故障转移和故障回复过程

1. 可以运行从本地 Hyper-V VM 到 Azure 的计划内或计划外[故障转移](site-recovery-failover.md)。 如果运行计划的故障转移，源 VM 将关闭以确保不会丢失数据。
2. 可以故障转移单台计算机，或创建[恢复计划](site-recovery-create-recovery-plans.md)来协调多台计算机的故障转移。
4. 运行故障转移后，应会在 Azure 中看到创建的副本 VM。 如果需要，可向 VM 分配公共 IP 地址。
5. 然后提交故障转移，开始从副本 Azure VM 访问工作负荷。
6. 当本地主站点再次可用时，便可以[故障回复](site-recovery-failback-from-azure-to-hyper-v.md)。 启动从 Azure 到主站点的计划内故障转移。 运行计划内故障转移时，可以选择故障回复到同一 VM 或备用位置，并同步 Azure 与本地之间的更改，确保不会丢失数据。 在本地创建 VM 时，请提交故障转移。




## <a name="next-steps"></a>后续步骤

查看[支持矩阵](site-recovery-support-matrix-to-azure.md)

