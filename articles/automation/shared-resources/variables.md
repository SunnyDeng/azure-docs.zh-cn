---
title: Azure 自动化中的变量资产
description: 变量资产是可供 Azure 自动化中的所有 Runbook 和 DSC 配置使用的值。  本文介绍了变量的详细信息，以及如何在文本和图形创作中使用变量。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 04/01/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fc26c0357dcb071c4c75e8684fe47144a04177e4
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2019
ms.locfileid: "58806774"
---
# <a name="variable-assets-in-azure-automation"></a>Azure 自动化中的变量资产

变量资产是可供自动化帐户中的所有 Runbook 和 DSC 配置使用的值。 它们可以从 Azure 门户、 PowerShell、 runbook 或 DSC 配置中进行管理。 自动化变量可用于以下方案：

- 在多个 Runbook 或 DSC 配置之间共享某个值。

- 在同一 Runbook 或 DSC 配置中的多个作业之间共享某个值。

- 从门户或 PowerShell 命令行，可通过 runbook 或 DSC 配置，如一组常用配置项，如特定列表的 VM 名称、 特定资源组、 AD 域名，和的详细信息，请管理的值。  

自动化变量将保留，因为这些信息可即使 runbook 或 DSC 配置失败。 此行为允许值通过由同一 runbook 或 DSC 配置为运行下一次，然后使用另一个，或使用一个 runbook 设置。

创建变量时，可以指定将其加密存储。 加密的变量在 Azure 自动化中安全地存储和检索其值不能[Get-azurermautomationvariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)作为 Azure PowerShell 模块的一部分提供的 cmdlet。 可以检索加密值的唯一方法是从 Runbook 或 DSC 配置中的 **Get-AutomationVariable** 活动进行检索。

>[!NOTE]
>Azure 自动化中的安全资产包括凭据、证书、连接和加密的变量。 这些资产已使用针对每个自动化帐户生成的唯一密钥加密并存储在 Azure 自动化中。 此密钥存储在系统托管的密钥保管库中。 在存储安全资产之前，从密钥保管库加载密钥，然后使用该密钥加密资产。 此过程由 Azure 自动化管理。

## <a name="variable-types"></a>变量类型

当使用 Azure 门户创建变量时，必须通过下拉列表指定一个数据类型，以便门户可以显示用于输入变量值的相应控件。 该变量不会被限制为该数据类型。 必须使用 Windows PowerShell，如果你想要指定不同类型的值将变量进行设置。 如果指定**未定义**，则变量的值设置为 **$null**，并且你必须将的值与[Set-azurermautomationvariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable) cmdlet 或**Set-automationvariable**活动。 无法创建或更改在门户中，复杂变量类型的值，但您可以使用 Windows PowerShell 的任何类型的值。 复杂类型将作为 [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject)返回。

可以通过创建一个数组或哈希表并将其保存到变量，来将多个值存储到单一变量。

下面列出了自动化中的可用变量类型：

* 字符串
* 整数
* 日期时间
* Boolean
* NULL

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell cmdlet

对于 AzureRM，下表中的 cmdlet 用于通过 Windows PowerShell 创建和管理自动化凭据资产。 可在自动化 Runbook 和 DSC 配置中使用的 [AzureRM.Automation 模块](/powershell/azure/overview)已随附了这些 cmdlet。

| Cmdlet | 描述 |
|:---|:---|
|[Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)|检索现有变量的值。|
|[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable)|创建新变量并设置变量值。|
|[Remove-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationVariable)|删除现有变量。|
|[Set-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable)|设置现有变量的值。|

## <a name="activities"></a>活动

下表中的活动用于在 Runbook 和 DSC 配置中访问凭据。

| 活动 | 描述 |
|:---|:---|
|Get-AutomationVariable|检索现有变量的值。|
|Set-AutomationVariable|设置现有变量的值。|

> [!NOTE]
> 应避免在 Runbook 或 DSC 配置中的 **Get-AutomationVariable** 的 –Name 参数中使用变量，因为这可能会使设计时发现 Runbook 或 DSC 配置与自动化变量之间的依赖关系变得复杂化。

下表中的函数用于在 Python2 Runbook 中访问和检索变量。

|Python2 函数|描述|
|:---|:---|
|automationassets.get_automation_variable|检索现有变量的值。 |
|automationassets.set_automation_variable|设置现有变量的值。 |

> [!NOTE]
> 必须在 Python Runbook 顶部导入“automationassets”模块才能访问资产函数。

## <a name="creating-a-new-automation-variable"></a>创建新的自动化变量

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>使用 Azure 门户创建新变量

1. 在自动化帐户中，单击“资产”磁贴，然后在“资产”边栏选项卡中选择“变量”。
2. 在“变量”磁贴中，选择“添加变量”。
3. 完成“新建变量”边栏选项卡上的选项，然后单击“创建”保存新变量。

### <a name="to-create-a-new-variable-with-windows-powershell"></a>使用 Windows PowerShell 创建新变量

[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) cmdlet 创建一个新的变量并设置其初始值。 可以使用 [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable) 检索该值。 如果该值为简单类型，则返回相同的类型。 如果其为复杂类型，则返回 **PSCustomObject**。

下面的示例命令演示如何创建字符串类型的变量，并返回其值。

```powershell
New-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

下面的示例命令演示如何创建复杂类型的变量，并返回其属性。 在这种情况下，会使用来自 **Get-AzureRmVm** 的虚拟机对象。

```powershell
$vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>使用 Runbook 或 DSC 配置中的变量

使用 Set-AutomationVariable 活动设置 PowerShell Runbook 或 DSC 配置中自动化变量的值，并使用 Get-AutomationVariable 来检索该值。 不应在 Runbook 或 DSC 配置中使用 Set-AzureRMAutomationVariable 或 Get-AzureRMAutomationVariable cmdlet，因为它们的效率低于工作流活动。 也不能使用 Get-AzureRMAutomationVariable 来检索安全变量的值。 从 Runbook 或 DSC 配置中创建新变量的唯一方法是使用 [New-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) cmdlet。

### <a name="textual-runbook-samples"></a>文本 Runbook 示例

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>设置和检索变量中的一个简单值

下面的示例命令演示如何设置和检索文本 Runbook 中的变量。 在此示例中，假设的整数类型变量命名为*NumberOfIterations*并*NumberOfRunnings*和一个名为字符串类型的变量*SampleMessage*具有已创建。

```powershell
$NumberOfIterations = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

#### <a name="setting-and-retrieving-a-complex-object-in-a-variable"></a>设置和检索变量中的复杂对象

下面的示例代码演示如何更新文本 Runbook 中具有复杂值的变量。 在此示例中，使用 **Get-AzureVM** 检索到一个 Azure 虚拟机并保存到一个现有的自动化变量。  如 [变量类型](#variable-types)中所述，该对象存储为一个 PSCustomObject。

```powershell
$vm = Get-AzureVM -ServiceName "MyVM" -Name "MyVM"
Set-AutomationVariable -Name "MyComplexVariable" -Value $vm
```

在下面的代码中，从该变量检索值并将其用于启动虚拟机。

```powershell
$vmObject = Get-AutomationVariable -Name "MyComplexVariable"
if ($vmObject.PowerState -eq 'Stopped') {
    Start-AzureVM -ServiceName $vmObject.ServiceName -Name $vmObject.Name
}
```

#### <a name="setting-and-retrieving-a-collection-in-a-variable"></a>设置和检索变量中的集合

下面的示例代码演示如何使用文本 Runbook 中包含一组复杂值的变量。 在此示例中，使用 **Get-AzureVM** 检索到多个 Azure 虚拟机并保存到一个现有的自动化变量。 如 [变量类型](#variable-types)中所述，变量存储为一组 PSCustomObject。

```powershell
$vms = Get-AzureVM | Where -FilterScript {$_.Name -match "my"}
Set-AutomationVariable -Name 'MyComplexVariable' -Value $vms
```

在下面的代码中，从该变量检索此集合并将其用于启动每个虚拟机。

```powershell
$vmValues = Get-AutomationVariable -Name "MyComplexVariable"
ForEach ($vmValue in $vmValues)
{
    if ($vmValue.PowerState -eq 'Stopped') {
        Start-AzureVM -ServiceName $vmValue.ServiceName -Name $vmValue.Name
    }
}
```

#### <a name="setting-and-retrieving-a-variable-in-python2"></a>在 Python2 中设置和检索变量

以下代码示例演示了如何在 Python2 Runbook 中使用变量、设置变量以及处理关于不存在的变量的异常。

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a variable
value = automationassets.get_automation_variable("test-variable")
print value

# set a variable (value can be int/bool/string)
automationassets.set_automation_variable("test-variable", True)
automationassets.set_automation_variable("test-variable", 4)
automationassets.set_automation_variable("test-variable", "test-string")

# handle a non-existent variable exception
try:
    value = automationassets.get_automation_variable("non-existing variable")
except AutomationAssetNotFound:
    print "variable not found"
```

### <a name="graphical-runbook-samples"></a>图形 Runbook 示例

在图形 Runbook 中，通过在图形编辑器的“库”窗格中右键单击变量并选择所需的活动来添加 **Get-AutomationVariable** 或 **Set-AutomationVariable**。

![将变量添加到画布](../media/variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>设置变量中的值

下图显示了在图形 Runbook 中用于更新具有简单值的一个变量的示例活动。 在此示例中， **Get-azurermvm**检索单个 Azure 虚拟机和计算机名称将保存到字符串类型的一个现有自动化变量。 [链接是管道还是序列](../automation-graphical-authoring-intro.md#links-and-workflow)并不重要，因为你仅预期输出中的单个对象。

![设置简单变量](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>后续步骤

- 要了解有关在图形创作中将活动连接在一起的详细信息，请参阅[图形创作中的链接](../automation-graphical-authoring-intro.md#links-and-workflow)
- 若要开始使用图形 Runbook，请参阅 [我的第一个图形 Runbook](../automation-first-runbook-graphical.md)
