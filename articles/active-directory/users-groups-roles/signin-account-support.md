---
title: 如何知道是否 Azure AD 登录页接受 Microsoft 帐户 |Microsoft Docs
description: 如何屏幕消息传送在登录期间反映用户名查找
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/10/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d26ff0f9259e3531259673f94fe477444cc786b
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/11/2019
ms.locfileid: "59491589"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>对于 Azure Active Directory 中的 Microsoft 帐户登录选项

Azure Active Directory (Azure AD) 的登录页 Microsoft 365 支持工作或学校帐户和 Microsoft 帐户，但具体取决于用户的情况下，它可能是一个或另一个或两者。 例如，支持 Azure AD 登录页：

* 接受从这两种类型的帐户登录的应用
* 接受来宾的组织

## <a name="identification"></a>识别
您可以告诉你的组织使用的登录页是否通过查看中用户名字段的提示文本支持 Microsoft 帐户。 如果提示文本显示为"电子邮件、 电话或 Skype"，登录页将支持 Microsoft 帐户。

![帐户登录页之间的差异](./media/signin-account-support/ui-prompt.png)

[其他登录选项仅适用于个人 Microsoft 帐户](https://azure.microsoft.com/updates/microsoft-account-signin-options/ )但不能用于登录到工作或学校帐户资源。

## <a name="next-steps"></a>后续步骤

[自定义登录品牌](../fundamentals/add-custom-domain.md)