---
title: 管理 Azure Stack 中每个用户对资源的权限 | Microsoft Docs
description: 作为服务管理员或租户，了解如何管理 RBAC 权限。
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: 58c16b8a102ea27499fc464c209d4ca1c0d4db33
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59264703"
---
# <a name="manage-access-to-resources-with-azure-stack-role-based-access-control"></a>使用 Azure Stack 基于角色的访问控制管理对资源的访问权限

*适用于Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure Stack 支持基于角色的访问控制 (RBAC)，相同[访问管理的安全模型](https://docs.microsoft.com/azure/role-based-access-control/overview)的 Microsoft Azure 使用。 可以使用 RBAC 来管理用户、组或应用程序对订阅、资源和服务的访问权限。

## <a name="basics-of-access-management"></a>访问管理基础知识

基于角色的访问控制提供了精细的访问控制，可以用来保护环境安全。 通过在特定范围内分配 RBAC 角色，可以为用户授予他们所需的确切权限。 角色分配的范围可以是订阅、资源组或单个资源。 若要获取有关访问管理的更多详细信息，请阅读 [Azure 门户中基于角色的访问控制](https://docs.microsoft.com/azure/role-based-access-control/overview)一文。

### <a name="built-in-roles"></a>内置角色

Azure Stack 有三个可应用于所有资源类型的基本角色：

* **所有者**可以管理所有内容，包括对资源的访问权限。
* **参与者**可以管理除了对资源的访问权限以外的所有内容。
* **读者**可以查看所有内容，但不能进行任何更改。

### <a name="resource-hierarchy-and-inheritance"></a>资源层次结构和继承

Azure Stack 具有以下资源层次结构：

* 每个订阅属于一个目录。
* 每个资源组属于一个订阅。
* 每个资源属于一个资源组。

子范围将继承在父范围授予的访问权限。 例如：

* 你向某个 Azure AD 组分配了在订阅范围内的**读者**角色。 该组成员可以查看订阅中的每个资源组和资源。
* 你向某个应用程序分配了在资源组范围内的**参与者**角色。 该应用程序可以管理该资源组中所有类型的资源，但不能管理订阅中的其他资源组。

### <a name="assigning-roles"></a>分配角色

可以向一位用户分配多个角色，并且每个角色可以与不同的范围相关联。 例如：

* 向 TestUser-A 分配 Subscription-1 的“读者”角色。
* 向 TestUser-A 分配 TestVM-1 的“所有者”角色。

Azure [角色分配](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)一文提供了有关查看、分配和删除角色的详细信息。

## <a name="set-access-permissions-for-a-user"></a>设置用户的访问权限

以下步骤介绍了如何为用户配置权限。

1. 使用对要管理的资源具有所有者权限的帐户登录。
2. 在左侧导航窗格中，选择“资源组”。
3. 选择要针对其设置权限的资源组的名称。
4. 在资源组的导航窗格中，选择“访问控制(标识和访问管理)”。 “角色分配”视图会列出对该资源组具有访问权限的项。 可以对结果进行筛选和分组。
5. 在“访问控制”菜单栏上，选择“添加”。
6. 在“添加权限”窗格上：

   * 从“角色”下拉列表中选择要分配的角色。
   * 从“将访问权限分配到”下拉列表中选择要分配的资源。
   * 在你想要授予访问权限的目录中选择用户、组或应用程序。 可以通过显示名称、电子邮件地址和对象标识符搜索该目录。

7. 选择“保存”。

## <a name="next-steps"></a>后续步骤

[创建服务主体](azure-stack-create-service-principals.md)
