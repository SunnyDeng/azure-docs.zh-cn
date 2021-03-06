---
title: 跨数据库查询（垂直分区）入门 | Microsoft 文档
description: 如何在垂直分区数据库中使用弹性数据库查询
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 116a465a0ddc913e342e0ffcc1fb29f5bf969419
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55464154"
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>跨数据库查询（纵向分区）入门（预览）

适用于 Azure SQL 数据库弹性数据库查询（预览版），可让你使用单一连接点运行跨多个数据库的 T-SQL 查询。 本文适用于[垂直分区数据库](sql-database-elastic-query-vertical-partitioning.md)。  

完成时，会：了解如何配置和使用 Azure SQL 数据库执行跨多个相关数据库的查询。

有关弹性数据库查询功能的详细信息，请参阅 [Azure SQL 数据库弹性数据库查询概述](sql-database-elastic-query-overview.md)。

## <a name="prerequisites"></a>先决条件

需要 ALTER ANY EXTERNAL DATA SOURCE 权限。 此权限包含在 ALTER DATABASE 权限中。 引用基础数据源需要 ALTER ANY EXTERNAL DATA SOURCE 权限。

## <a name="create-the-sample-databases"></a>创建示例数据库

首先，我们在相同或不同 SQL 数据库服务器中创建两个数据库：Customers 和 Orders。

在 **Orders** 数据库中执行以下查询以创建 **OrderInformation** 表并输入示例数据。

    CREATE TABLE [dbo].[OrderInformation](
        [OrderID] [int] NOT NULL,
        [CustomerID] [int] NOT NULL
        )
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)

现在，在 **Customers** 数据库中执行以下查询以创建 **CustomerInformation** 表并输入示例数据。

    CREATE TABLE [dbo].[CustomerInformation](
        [CustomerID] [int] NOT NULL,
        [CustomerName] [varchar](50) NULL,
        [Company] [varchar](50) NULL
        CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC)
    )
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC')
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ')
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO')

## <a name="create-database-objects"></a>创建数据库对象

### <a name="database-scoped-master-key-and-credentials"></a>数据库范围的主密钥和凭据

1. 在 Visual Studio 中打开 SQL Server Management Studio 或 SQL Server Data Tools。
2. 连接到 Orders 数据库，并执行以下 T-SQL 命令：

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';  

    “username”和“password”应是用于登录到 Customers 数据库的用户名和密码。
    当前不支持使用 Azure Active Directory 通过弹性查询进行身份验证。

### <a name="external-data-sources"></a>外部数据源

若要创建外部数据源，请对 Orders 数据库执行以下命令：

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
        (TYPE = RDBMS,
        LOCATION = '<server_name>.database.windows.net',
        DATABASE_NAME = 'Customers',
        CREDENTIAL = ElasticDBQueryCred,
    ) ;

### <a name="external-tables"></a>外部表

在 Orders 数据库中创建外部表，该表应与 CustomerInformation 表的定义相匹配：

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation]
    ( [CustomerID] [int] NOT NULL,
      [CustomerName] [varchar](50) NOT NULL,
      [Company] [varchar](50) NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc)

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>执行示例弹性数据库 T-SQL 查询

定义外部数据源和外部表后，现在可以使用 T-SQL 查询外部表。 对 Orders 数据库执行以下查询：

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company
    FROM OrderInformation
    INNER JOIN CustomerInformation
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID

## <a name="cost"></a>成本

目前，弹性数据库查询功能包含在 Azure SQL 数据库的成本中。  

有关定价信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database)。

## <a name="next-steps"></a>后续步骤

* 有关弹性查询的概述，请参阅[弹性查询概述](sql-database-elastic-query-overview.md)。
* 有关垂直分区数据的语法和示例查询，请参阅[查询垂直分区数据](sql-database-elastic-query-vertical-partitioning.md)
* 有关水平分区（分片）的教程，请参阅[弹性查询入门 - 水平分区（分片）](sql-database-elastic-query-getting-started.md)。
* 有关水平分区数据的语法和示例查询，请参阅[查询水平分区数据](sql-database-elastic-query-horizontal-partitioning.md)
* 请参阅 [sp\_execute \_remote](https://msdn.microsoft.com/library/mt703714)，了解在单个远程 Azure SQL 数据库或在水平分区方案中用作分片的一组数据库中执行 Transact-SQL 语句的存储过程。
