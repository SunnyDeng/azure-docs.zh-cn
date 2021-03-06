---
title: 通过 Azure 安全中心监视的 Azure Policy 定义 | Microsoft Docs
description: 通过 Azure 安全中心监视的 Azure Policy 定义。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: c89cb1aa-74e8-4ed1-980a-02a7a25c1a2f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: 9d9369afd36f64c27cd2222cab0de5912aa913de
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57877490"
---
# <a name="azure-security-policies-monitored-by-security-center"></a>通过安全中心监视的 azure 安全策略
本文提供了一系列可在 Azure 安全中心中监视的 Azure 策略定义。 有关安全策略的详细信息，请参阅[使用安全策略](tutorial-security-policy.md)。

## <a name="available-security-policies"></a>可用的安全策略

若要了解如何通过安全中心监视的内置策略，请参阅下表：

| 策略 | 策略的用途 |
| --- | --- |
|在 Azure Service Fabric 和虚拟机规模集的诊断启用审核日志|我们建议，以便可调查后将事件或危害的活动跟踪启用日志。|
|审核事件中心命名空间上的授权规则|Azure 事件中心客户端不应使用一个命名空间级别访问策略，提供对所有队列和主题的命名空间中的访问。 若要符合最低特权安全模型，应在针对队列和主题，以提供对特定的实体的访问的实体级创建访问策略。|
|审核事件中心实体上的授权规则的存在|审核事件中心实体授予最低特权的访问权限的授权规则的存在。|
|审核对存储帐户的不受限的网络访问|在存储帐户防火墙设置中审核无限制的网络访问权限。 配置网络规则，以便只有来自许可网络的应用程序可以访问存储帐户。 若要允许来自特定 internet 或本地客户端连接，授予访问权限，从特定的 Azure 虚拟网络或公共 internet 的流量的 IP 地址范围。|
|审核自定义 RBAC 规则的使用|审核内置角色，如"所有者、 参与者、 读取者"而不是自定义基于角色的访问控制 (RBAC) 角色，容易出错。 使用自定义角色都被视为异常并需要严格审核和威胁建模。|
|审核在 Azure 流分析中启用诊断日志|审核日志的启用，并将其用于长达一年。 当发生安全事件或者泄漏您的网络时，这将创建调查的活动跟踪的信息。|
|审核确认指向存储帐户的传输的安全性|审核存储帐户中的安全传输的要求。 安全传输选项会强制存储帐户仅接受来自安全连接 (HTTPS) 的请求。 使用 HTTPS 可确保在服务器和服务之间的身份验证。 它还可防止数据在传输过程中的网络层攻击，如-拦截，窃听，和会话劫持。|
|审核的 SQL Server 的 Azure Active Directory 管理员预配|审核的 SQL Server 以启用 Azure AD 身份验证的 Azure Active Directory (Azure AD) 管理员预配。 Azure AD 身份验证支持简化的权限管理和集中式的标识管理的数据库用户和其他 Microsoft 服务。|
|审核服务总线命名空间的授权规则|Azure 服务总线客户端不应使用一个命名空间级别访问策略，提供对所有队列和主题的命名空间中的访问。 若要对齐使用最低特权安全模型，请在针对队列和主题，以提供对特定的实体的访问的实体级创建访问策略。|
|审核确认已在服务总线中启用诊断日志|审核日志的启用，并将其注册到一年。 当发生安全事件或者泄漏您的网络时，这将创建调查的活动跟踪的信息。|
|审核确认 Service Fabric 中的 ClusterProtectionLevel 属性设置为 EncryptAndSign|Service Fabric 提供了三个级别的节点到节点通信使用一个主要群集证书的保护：无、 符号和 EncryptAndSign。 请设置保护级别，确保节点到节点的所有消息经过加密和数字签名。|
|审核确认已在 Service Fabric 中使用 Azure Active Directory，用于实施客户端身份验证|审核客户端身份验证只能通过 Service Fabric 中的 Azure AD 的使用。|
|审核确认已启用搜索服务诊断日志|审核日志的启用，并将其用于长达一年。 当发生安全事件或者泄漏您的网络时，这将创建调查的活动跟踪的信息。|
|审核启用的仅安全连接到 Azure 缓存的 Redis|审核仅启用通过 SSL 与 Azure 缓存的 Redis 的连接。 使用安全连接可确保在服务器和服务之间的身份验证。 它还可防止数据在传输过程中的网络层攻击，如-拦截，窃听，和会话劫持。|
|审核确认已在逻辑应用中启用诊断日志|审核日志的启用，并将其用于长达一年。 当发生安全事件或者泄漏您的网络时，这将创建调查的活动跟踪的信息。|
|审核确认已在 Key Vault 中启用诊断日志|审核日志的启用，并将其用于长达一年。 当发生安全事件或者泄漏您的网络时，这将创建调查的活动跟踪的信息。|
|在事件中心的诊断启用审核日志|审核日志的启用，并将其用于长达一年。 当发生安全事件或者泄漏您的网络时，这将创建调查的活动跟踪的信息。|
|审核确认已在 Azure Data Lake Store 中启用诊断日志|审核日志的启用和保留长达一年。 当发生安全事件或者泄漏您的网络时，这将创建调查的活动跟踪的信息。|
|审核在 Data Lake Analytics 中启用诊断日志|审核日志的启用，并将其用于长达一年。 当发生安全事件或者泄漏您的网络时，这将创建调查的活动跟踪的信息。|
|审核经典存储帐户的使用|使用 Azure 资源管理器为存储帐户提供的安全增强功能。 其中包括： <br>-更强的访问控制 (RBAC)<br>-更好地审核<br>-Azure 资源管理器基于部署和管理<br>访问权限管理的标识<br>访问 Azure 密钥保管库的机密<br>-Azure 基于 AD 的身份验证<br>-对标记和简化安全管理的资源组的支持|
|审核经典虚拟机的使用|使用 Azure 资源管理器为虚拟机提供的安全增强功能。  其中包括： <br>-更强的访问控制 (RBAC)<br>-更好地审核<br>-Azure 资源管理器基于部署和管理<br>访问权限管理的标识<br>访问 Azure 密钥保管库的机密<br>-Azure 基于 AD 的身份验证<br>-对标记和简化安全管理的资源组的支持|
|审核确认已配置 Batch 帐户指标警报规则|在 Azure Batch 帐户，以启用所需的指标的指标警报规则的审核配置。|
|审核确认已在 Batch 帐户中启用诊断日志|审核日志的启用，并将其用于长达一年。 当发生安全事件或者泄漏您的网络时，这将创建调查的活动跟踪的信息。|
|审核的自动化帐户的变量的加密启用|请务必存储敏感数据时，启用加密的 Azure 自动化帐户的变量资产。|
|审核启用的应用服务中的诊断日志|审核确认已在应用上启用诊断日志。 当发生安全事件或者泄漏您的网络时，这将创建调查的活动跟踪的信息。|
|审核透明数据加密状态|审核 SQL 数据库的透明数据加密状态。|
|审核 SQL Server 级别审核设置|审核 SQL 审核在服务器级别存在。|
|\[预览]:监视 Azure 安全中心内未加密的 SQL 数据库|Azure 安全中心监视未加密的 SQL 服务器或数据库的建议。|
|\[预览]:监视 Azure 安全中心内未审核的 SQL 数据库|Azure 安全中心监视 SQL 服务器和没有按照建议启用 SQL 审核的数据库。|
|\[预览]:监视 Azure 安全中心内系统更新的缺失情况|Azure 安全中心监视缺失的安全系统更新，在服务器上的建议。|
|\[预览]:审核存储帐户是否缺少 blob 加密|审核不使用 blob 加密的存储帐户。 这仅适用于 Microsoft 存储资源类型，不存储来自其他提供程序。 Azure 安全中心监视可能的网络中实时访问的建议。|
|\[预览]:监视 Azure 安全中心中的可能的网络中实时访问|Azure 安全中心监视可能的网络中实时访问的建议。|
|\[预览]:监视 Azure 安全中心内列入允许列表的可能的应用|Azure 安全中心监视可能的应用程序白名单配置。|
|\[预览]:监视 Azure 安全中心内规则较宽松的网络访问|Azure 安全中心监视网络安全组，具有规则过于宽松，建议。|
|\[预览]:监视 Azure 安全中心的 OS 漏洞|Azure 安全中心监视不满足配置的基线的建议的服务器。| 
|\[预览]:监视 Azure 安全中心 Endpoint Protection 的缺失情况|Azure 安全中心监视服务器没有安装的 Microsoft System Center Endpoint Protection 代理的建议。|
|\[预览]:监视 Azure 安全中心中的未加密的 VM 磁盘|Azure 安全中心监视虚拟机不具有的建议启用磁盘加密的。|
|\[预览]:在 Azure 安全中心监视 VM 漏洞|监视漏洞检测到的漏洞评估解决方案和没有漏洞评估解决方案在 Azure 安全中心建议的 Vm。|
|\[预览]:监视 Azure 安全中心内未受保护的 Web 应用程序|Azure 安全中心监视的 web 应用程序缺乏 web 应用程序防火墙保护的建议。|
|\[预览]:监视 Azure 安全中心内未受保护的网络终结点|Azure 安全中心监视网络的建议不具有下一代防火墙保护的终结点。|
|\[预览]:监视 Azure 安全中心的 SQL 漏洞评估结果|监视漏洞评估扫描结果，并建议如何修正的数据库漏洞。|
|\[预览]:审核最大订阅所有者数|我们建议您将指定最多三个订阅所有者，以减少漏洞遭到入侵的所有者的可能性。|
|\[预览]:审核最大订阅所有者数|我们建议将多个订阅所有者，以确保管理员访问冗余。|
|\[预览]:审核具有所有者权限但未启用 MFA 的订阅帐户|应为具有所有者权限，以防止帐户或资源出现违规问题的所有订阅帐户启用多重身份验证 (MFA)。|
|\[预览]:审核具有写入权限但未启用 MFA 的订阅帐户|应为具有写权限来防止帐户或资源出现违规问题的所有订阅帐户启用多重身份验证。|
|\[预览]:审核具有读取权限但未启用 MFA 的订阅帐户|应为具有读取权限，以防止帐户或资源出现违规问题的所有订阅帐户启用多重身份验证。|
|\[预览]:审核具有所有者权限但已被弃用的订阅帐户|应从订阅中删除不推荐使用具有所有者权限的帐户。 不推荐使用的帐户已阻止登录。|
|\[预览]:审核已弃用的订阅帐户|应从订阅中删除弃用的帐户。 不推荐使用的帐户已阻止登录。|
|\[预览]:审核具有所有者权限的外部订阅帐户|应从你的订阅以防止权限访问删除具有所有者权限的外部帐户。|
|\[预览]:审核具有写入权限的外部订阅帐户|外部帐户具有写入权限应该删除的订阅以防止未受监视的访问。|
|\[预览]:审核具有读取权限的外部订阅帐户|从你的订阅以防止未受监视的访问，应删除具有读取权限的外部帐户。|




## <a name="next-steps"></a>后续步骤
本文中已经介绍了如何在安全中心配置安全策略。 若要了解有关安全中心的详细信息，请参阅以下文章。

* [Azure 安全中心规划和操作指南](security-center-planning-and-operations-guide.md)：了解如何规划和了解 Azure 安全中心中的设计注意事项。
* [在 Azure 安全中心进行安全运行状况监视](security-center-monitoring.md)：了解如何监视 Azure 资源的运行状况。
* [在 Azure 安全中心内管理和响应安全警报](security-center-managing-and-responding-alerts.md)：了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md)：了解如何监视合作伙伴解决方案的运行状况。
* [Azure 安全中心常见问题解答](security-center-faq.md)：获取服务使用方面的常见问题解答。
* [Azure 安全博客](https://blogs.msdn.com/b/azuresecurity/)：查找关于 Azure 安全性及合规性的博客文章。

若要了解有关 Azure 策略的详细信息，请参阅[什么是 Azure 策略？](../governance/policy/overview.md)。
