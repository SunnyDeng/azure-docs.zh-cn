---
title: 自动训练时间序列预测的模型
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 机器学习服务来训练时间序列预测回归模型使用自动完成机器学习。
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 03/19/2019
ms.openlocfilehash: e1b584d38c4583e37b7c47535c836d1fa7d428f1
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357245"
---
# <a name="auto-train-a-time-series-forecast-model"></a>自动训练时间序列预测的模型

在本文中，您将了解如何使用自动化的机器学习在 Azure 机器学习服务中对时间序列预测回归模型定型。 配置一个预测模型，类似于设置标准回归模型中使用自动化的机器学习，但某些配置选项和预处理步骤存在用于时间序列数据。 以下示例演示了如何为：

* 为时序建模准备数据
* 配置中的特定时间序列参数[ `AutoMLConfig` ](/python/api/azureml-train-automl/azureml.train.automl.automlconfig)对象
* 运行时间序列数据预测

## <a name="prerequisites"></a>必备组件

* Azure 机器学习服务工作区。 若要创建工作区，请参阅[创建 Azure 机器学习服务工作区](setup-create-workspace.md)。
* 本文假定你基本熟悉设置自动化机器学习实验。 请按照[教程](tutorial-auto-train-models.md)或[操作指南](how-to-configure-auto-train.md)若要查看基本自动化的机器学习实验设计模式。

## <a name="preparing-data"></a>准备数据

最重要的区别之间预测回归任务类型和回归任务自动化的机器学习中的类型表示有效的时间序列数据中包括一项功能。 固定的时间系列具有定义完善且一致的频率，此时，每个示例中的持续时间跨度值。 请考虑以下快照文件的`sample.csv`。

    week_starting,store,sales_quantity,week_of_year
    9/3/2018,A,2000,36
    9/3/2018,B,600,36
    9/10/2018,A,2300,37
    9/10/2018,B,550,37
    9/17/2018,A,2100,38
    9/17/2018,B,650,38
    9/24/2018,A,2400,39
    9/24/2018,B,700,39
    10/1/2018,A,2450,40
    10/1/2018,B,650,40

此数据集是一个简单示例的每周销售数据的公司中有两个不同的存储，A 和 b。 此外，还有一项功能的`week_of_year`这将允许该模型以检测每周的季节性。 该字段`week_starting`每周频率，与该字段表示清理时间序列`sales_quantity`是运行预测的目标列。 将数据读入 Pandas 数据帧，然后使用`to_datetime`函数，以确保时序是`datetime`类型。

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["week_starting"] = pd.to_datetime(data["week_starting"])
```

在这种情况下数据已排序按时间字段的升序`week_starting`。 但是，设置时试验，请确保所需的时间列按升序排序以生成有效的时间序列。 假定数据包含 1,000 条记录，并在要创建训练和测试数据集的数据进行确定性拆分。 然后单独的目标字段`sales_quantity`来创建预测训练和测试集。

```python
X_train = data.iloc[:950]
X_test = data.iloc[-50:]

y_train = X_train.pop("sales_quantity").values
y_test = X_test.pop("sales_quantity").values
```

> [!NOTE]
> 在用于预测未来的值为模型定型，确保所有运行在预期范围的预测时，可以使用培训中使用的功能。 例如，在创建预测的需求，包括当前股价的功能可能大规模会增加训练准确性。 但是，如果你想要预测的长时间范围，您可能不能准确地预测未来与将来的时间序列点相对应的股票值和模型准确性可能会受到影响。

## <a name="configure-experiment"></a>配置试验

预测任务，自动化的机器学习使用预处理和估计特定于时间序列数据的步骤。 将执行以下预处理步骤：

* 检测时间序列示例频率 （例如每小时、 每天、 每周），并为创建新记录不存在时间点，以使连续系列。
* 输入中的目标 （通过转发填充） 和 （使用中间值的列的值） 的特征列的缺失值
* 创建基于粒度的功能，从而能够在不同的系列的固定的效果
* 创建基于时间的功能，以帮助在学习季节性模式
* 编码到数字量的分类变量

`AutoMLConfig`对象定义的设置和自动的机器学习任务所需数据。 类似于回归问题，您定义标准的培训参数，如任务类型和数量的迭代，训练数据，交叉验证的次数。 对于预测任务，有其他必须设置影响实验的参数。 下表说明了每个参数和其使用情况。

| Param | 描述 | 需要 |
|-------|-------|-------|
|`time_column_name`|用于指定用于生成时间序列和推断其频率的输入数据中的日期时间列。|✓|
|`grain_column_names`|在输入数据中定义每个序列组的名称。 如果未定义粒度，数据集被假定为一个时间序列。||
|`max_horizon`|最大所需的时间序列的频率单位的预测时间范围。|✓|

创建时间序列设置为字典对象。 设置`time_column_name`到`week_starting`字段中数据集。 定义`grain_column_names`参数，以确保**两个单独的时间序列组**为我们的数据; 一个用于存储 A 和 b。 最后，将创建`max_horizon`设置为 50 为整个测试的预测。

```python
time_series_settings = {
    "time_column_name": "week_starting",
    "grain_column_names": ["store"],
    "max_horizon": 50
}
```

现在将创建一个标准`AutoMLConfig`对象，指定`forecasting`任务类型，并提交试验。 模型完成后，检索最佳的运行的迭代。

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

> [!NOTE]
> 有关交叉验证 (CV) 过程中，时间系列数据可以违反规范的 K-折叠交叉验证策略的基本统计假设以便自动的机器学习实现滚动源验证过程中创建时间序列数据的交叉验证折叠。 若要使用此过程，指定`n_cross_validations`中的参数`AutoMLConfig`对象。 您可以绕过验证并使用自己的验证设置与`X_valid`和`y_valid`参数。

## <a name="forecasting-with-best-model"></a>通过最佳的模型预测

使用最佳的模型迭代预测的测试数据集的值。

```python
y_predict = fitted_model.predict(X_test)
y_actual = y_test.flatten()
```

计算 RMSE （方根误差） 之间`y_test`实际值和预测的值中`y_pred`。

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

现在，整体确定模型精确度，大多数实际的下一步是使用模型来预测将来的未知的值。 只需提供作为测试集相同的格式中的数据集`X_test`但具有未来日期时间和生成预测集是每个时间序列步骤的预测的值。 假设在数据集中的最后一个时间序列记录了用于每周启动 2018 年 12 月 31 日。 预测下一步一周的需求 (或以预测，所需的任意多个段 < = `max_horizon`)，创建一个每周启动的每个存储时间系列记录 01/07/2019年。

    week_starting,store,week_of_year
    01/07/2019,A,2
    01/07/2019,A,2

重复此未来将数据加载到数据帧，然后运行的必要步骤`best_run.predict(X_test)`来预测将来值。

> [!NOTE]
> 值不能预测的数量大于段`max_horizon`。 必须使用更大范围超出当前即将推出的未来值进行预测重新定型模型。

## <a name="next-steps"></a>后续步骤

* 请按照[教程](tutorial-auto-train-models.md)若要了解如何创建使用试验自动机器学习。
* 视图[适用于 Python 的 Azure 机器学习 SDK](https://aka.ms/aml-sdk)参考文档。
