---
title: 使用 Azure CLI 管理 Azure Cosmos DB 资源
description: 使用 Azure CLI 管理 Azure Cosmos DB 帐户、数据库和容器。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 4/8/2019
ms.author: mjbrown
ms.openlocfilehash: 1d19e58b2d1381725de490b68d9e4d00a2ca4cb6
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/11/2019
ms.locfileid: "59495475"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>使用 Azure CLI 管理 Azure Cosmos 资源

以下指南介绍了常用的命令自动管理 Azure Cosmos DB 帐户、 数据库和使用 Azure CLI 的容器。 [Azure CLI 参考](https://docs.microsoft.com/cli/azure/cosmosdb)中收录了所有 Azure Cosmos DB CLI 命令的参考页。 还可以在[针对 Azure Cosmos DB 的 Azure CLI 示例](cli-samples.md)中找到更多示例，包括如何为 MongoDB、Gremlin、Cassandra 和表 API 创建和管理 Cosmos DB 帐户、数据库和容器。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本主题要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-an-azure-cosmos-db-account"></a>创建 Azure Cosmos DB 帐户

若要使用 SQL API 创建 Azure Cosmos DB 帐户，在美国东部和美国西部区域中的会话一致性，请运行以下命令：

```azurecli-interactive
az cosmosdb create \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations EastUS=0 WestUS=1 \
   --enable-multiple-write-locations false
```

> [!IMPORTANT]
> Azure Cosmos 帐户名称必须小写。

## <a name="create-a-database"></a>创建数据库

若要创建 Cosmos DB 数据库，请运行以下命令：

```azurecli-interactive
az cosmosdb database create \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup
```

## <a name="create-a-container"></a>创建容器

若要使用 RU/秒的 400 和分区键创建 Cosmos DB 容器中，运行以下命令：

```azurecli-interactive
# Create a container
az cosmosdb collection create \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --partition-key-path /myPartitionKey \
   --throughput 400
```

## <a name="change-the-throughput-of-a-container"></a>更改容器的吞吐量

若要更改为 1000 RU/s 的 Cosmos DB 容器的吞吐量，请运行以下命令：

```azurecli-interactive
# Update container throughput
az cosmosdb collection update \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --throughput 1000
```

## <a name="list-account-keys"></a>列出帐户密钥

若要获取 Cosmos 帐户的密钥，请运行以下命令：

```azurecli-interactive
# List account keys
az cosmosdb list-keys \
   --name  mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="list-connection-strings"></a>列出连接字符串

若要获得 Cosmos 帐户连接字符串，请运行以下命令：

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="regenerate-account-key"></a>重新生成帐户密钥

若要重新生成新的主密钥 Cosmos 帐户，请运行以下命令：

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --key-kind primary
```

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅：

- [安装 Azure CLI](/cli/azure/install-azure-cli)
- [Azure CLI 参考](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Azure Cosmos DB 的其他 Azure CLI 示例](cli-samples.md)
