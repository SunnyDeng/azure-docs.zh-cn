---
title: 为 Hyper-V VM 到 Azure 的灾难恢复准备本地 Hyper-V 服务器 | Microsoft Docs
description: 了解如何使用 Azure Site Recovery 服务准备本地 Hyper-V VM 以进行到 Azure 的灾难恢复。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6e57b629a0007b06af6e37f96e1466e35afafccc
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361885"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>为 Hyper-V VM 到 Azure 的灾难恢复准备本地 Hyper-V 服务器

本文介绍如何准备本地 HYPER-V 基础结构，想要设置的 Hyper-v Vm 到 Azure 的灾难恢复时使用[Azure Site Recovery](site-recovery-overview.md)。


这是一系列，演示如何为在本地 HYPER-V Vm 设置灾难恢复到 Azure 中的第二个教程。 在第一个教程中，我们[设置 Azure 组件](tutorial-prepare-azure.md)进行 HYPER-V 灾难恢复。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 如果由 System Center VMM 管理 HYPER-V 主机，请查看 HYPER-V 要求和 VMM 要求。
> * 如果适用，请准备 VMM。
> * 验证 internet 访问的 Azure 位置。
> * 准备 Vm，以便可以故障转移到 Azure 后进行访问。

> [!NOTE]
> 教程介绍了一种方案的最简单部署路径。 它们尽可能使用默认选项，并且不显示所有可能的设置和路径。 有关详细说明，请查看站点恢复表的内容的操作方法部分中文章。

## <a name="before-you-start"></a>开始之前

请确保你已准备好 Azure 中所述[本系列中的第一个教程](tutorial-prepare-azure.md)。

## <a name="review-requirements-and-prerequisites"></a>审查要求和先决条件

确保 Hyper-V 主机和 VM 符合要求。

1. [验证](hyper-v-azure-support-matrix.md#on-premises-servers)本地服务器要求。
2. [检查要复制到 Azure 的 Hyper-V VM 的要求](hyper-v-azure-support-matrix.md#replicated-vms)。
3. 检查 Hyper-V 主机[网络](hyper-v-azure-support-matrix.md#hyper-v-network-configuration)以及主机和来宾[存储](hyper-v-azure-support-matrix.md#hyper-v-host-storage)是否支持本地 Hyper-V 主机。
4. 故障转移后，检查 [Azure 网络](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover)、[存储](hyper-v-azure-support-matrix.md#azure-storage)和[计算](hyper-v-azure-support-matrix.md#azure-compute-features)支持的功能。
5. 复制到 Azure 的本地 VM 必须符合 [Azure VM 要求](hyper-v-azure-support-matrix.md#azure-vm-requirements)。


## <a name="prepare-vmm-optional"></a>准备 VMM（可选）

如果 Hyper-V 主机由 VMM 管理，则需准备本地 VMM 服务器。 

- 请确保 VMM 服务器至少有一个云，包含一个或多个主机组。 运行在虚拟机上的 Hyper-V 主机应位于云中。
- 为网络映射准备 VMM 服务器。

### <a name="prepare-vmm-for-network-mapping"></a>为网络映射准备 VMM

如果使用 VMM，则[网络映射](site-recovery-network-mapping.md)在本地 VMM VM 网络和 Azure 虚拟网络之间进行映射。 映射将确保在故障转移后创建 Azure VM 时，将其连接到正确的网络。

为网络映射准备 VMM，如下所示：

1. 确保有与 Hyper-V 主机所在的云相关联的 [VMM 逻辑网络](https://docs.microsoft.com/system-center/vmm/network-logical)。
2. 确保有链接到逻辑网络的[虚拟机网络](https://docs.microsoft.com/system-center/vmm/network-virtual)。
3. 在 VMM 中，将 VM 连接到 VM 网络。

## <a name="verify-internet-access"></a>验证 Internet 访问

1. 对于本教程，最简单的配置是让 Hyper-V 主机和 VMM 服务器能够直接访问 Internet 而无需使用代理。 
2. 请确保该 Hyper-V 主机和 VMM 服务器（如果相关）可以访问以下所需 URL。   
3. 如果要通过 IP 地址来控制访问，请确保：
    - 基于 IP 地址的防火墙规则可以连接到 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 端口。
    - 允许订阅的 Azure 区域的 IP 地址范围。
    
### <a name="required-urls"></a>所需 URL


[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>准备在故障转移后连接到 Azure VM

在实施故障转移方案期间，可能需要连接到已复制的本地网络。

若要在故障转移后使用 RDP 连接到 Windows VM，请按如下所示允许访问：

1. 若要通过 Internet 访问，请在故障转移之前在本地 VM 上启用 RDP。 请确保已针对“公共”配置文件添加了 TCP 和 UDP 规则，并确保在“Windows 防火墙” > “允许的应用”中针对所有配置文件允许 RDP。
2. 若要通过站点到站点 VPN 进行访问，请在本地计算机上启用 RDP。 应在“Windows 防火墙” -> “允许的应用和功能”中针对“域和专用”网络允许 RDP。
   检查操作系统的 SAN 策略是否已设置为 OnlineAll。 [了解详细信息](https://support.microsoft.com/kb/3031135)。 触发故障转移时，VM 上不应存在待处理的 Windows 更新。 如果有，你将无法登录到虚拟机在更新完成之前。
3. 在 Windows Azure VM 上执行故障转移后，请选中“启动诊断”，查看 VM 的屏幕截图。 如果无法连接，请检查 VM 是否正在运行，并查看这些[疑难解答提示](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。

故障转移后，可以使用与复制的本地 VM 相同的 IP 地址或不同的 IP 地址访问 Azure VM。 [详细了解](concepts-on-premises-to-azure-networking.md)如何为故障转移设置 IP 寻址。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [为 Hyper-V VM 设置到 Azure 的灾难恢复](tutorial-hyper-v-to-azure.md)
> [为 VMM 云中的 Hyper-V VM 设置到 Azure 的灾难恢复](tutorial-hyper-v-vmm-to-azure.md)
