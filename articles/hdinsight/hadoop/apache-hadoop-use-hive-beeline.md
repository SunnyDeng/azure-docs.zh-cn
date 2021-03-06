---
title: 将 Apache Beeline 与 Apache Hive 配合使用 - Azure HDInsight
description: 了解如何使用 Beeline 客户端通过 Hadoop on HDInsight 运行 Hive 查询。 Beeline 是用于通过 JDBC 操作 HiveServer2 的实用工具。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: hrasheed
ms.openlocfilehash: 89303e5c827fc24540d345a9a2b9a0743e453a4d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59257121"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>将 Apache Beeline 客户端与 Apache Hive 配合使用

了解如何使用 [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) 在 HDInsight 上运行 Apache Hive 查询。

Beeline 是一个 Hive 客户端，包含在 HDInsight 群集的头节点上。 Beeline 使用 JDBC 连接到 HiveServer2，后者是 HDInsight 群集上托管的一项服务。 还可以使用 Beeline 通过 Internet 远程访问 Hive on HDInsight。 以下示例提供最常见的连接字符串，用于从 Beeline 连接到 HDInsight：

## <a name="types-of-connections"></a>类型的连接

### <a name="from-an-ssh-session"></a>从 SSH 会话

从 SSH 会话连接到群集头节点，您可以连接到`headnodehost`端口上的地址`10001`:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>通过 Azure 虚拟网络

在通过 Azure 虚拟网络从客户端连接到 HDInsight，你必须提供群集头节点的完全限定的域名 (FQDN)。 由于直接与群集节点建立此连接，因此此连接使用端口 `10001`：

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

替换为`<headnode-FQDN>`群集头节点的完全限定域名。 若要查找头节点的完全限定域名，请使用[使用 Apache Ambari REST API 管理 HDInsight](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) 文档中的信息。

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster"></a>为 HDInsight 企业安全包 (ESP) 群集

当连接到企业安全包 (ESP) 群集的客户端加入到 Azure Active Directory (AAD) 时，还必须指定域名称`<AAD-Domain>`域用户帐户有权访问群集的名称和`<username>`:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

将 `<username>` 替换为域中有权访问群集的帐户的名称。 替换为`<AAD-DOMAIN>`具有名称的 Azure Active Directory (AAD) 加入群集。 使用的大写字符串`<AAD-DOMAIN>`值，否则不会找到凭据。 检查`/etc/krb5.conf`必要的领域名称。

---

### <a name="over-public-internet"></a>通过公共 internet

通过公共 Internet 连接时，必须提供群集登录帐户名（默认 `admin`）和密码。 例如，使用 Beeline 从客户端系统连接到 `<clustername>.azurehdinsight.net` 地址。 此连接通过端口 `443` 建立，并使用 SSL 进行加密：

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
```

将 `clustername` 替换为 HDInsight 群集的名称。 将 `admin` 替换为群集的群集登录帐户。 将 `password` 替换为群集登录帐户的密码。

---

### <a id="sparksql"></a>将 Beeline 与 Apache Spark 配合使用

Apache Spark 提供自己的 HiveServer2 实现（有时称为 Spark Thrift 服务器）。 此服务使用 Spark SQL 而不是 Hive 来解析查询，并且可以根据查询改善性能。

#### <a name="over-public-internet-with-apache-spark"></a>公共 internet 上使用 Apache Spark

通过 internet 连接时使用的连接字符串是略有不同。 而不是包含`httpPath=/hive2`它是`httpPath/sparkhive2`:

```bash 
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p password
```

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>从群集头或深入了解 Azure 虚拟网络与 Apache Spark

当直接从群集头节点或者从 HDInsight 群集所在的 Azure 虚拟网络中的资源进行连接时，应当为 Spark Thrift 服务器使用端口 `10002` 而非 `10001`。 下面的示例演示如何直接连接到在头节点：

```bash
beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a id="prereq"></a>先决条件

* 在 HDInsight Hadoop 群集。 请参阅 [Linux 上的 HDInsight 入门](./apache-hadoop-linux-tutorial-get-started.md)。

* 请注意[URI 方案](../hdinsight-hadoop-linux-information.md#URI-and-scheme)群集的主存储。 例如，`wasb://`适用于 Azure 存储`abfs://`有关 Azure 数据湖存储第 2 代或`adl://`的 Azure 数据湖存储 Gen1。 如果为 Azure 存储或数据湖存储第 2 代启用安全传输，则 URI 是`wasbs://`或`abfss://`分别。 有关详细信息，请参阅[安全传输](../../storage/common/storage-require-secure-transfer.md)。


* 选项 1：SSH 客户端。 有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。 本文档中的大多数步骤都假定从与群集的 SSH 会话中使用 Beeline。

* 选项 2：本地 Beeline 客户端。


## <a id="beeline"></a>运行 Hive 查询

此示例基于使用 Beeline 客户端通过 SSH 连接。

1. 使用以下代码打开与群集建立 SSH 连接。 将 `sshuser` 替换为群集的 SSH 用户，并将 `CLUSTERNAME` 替换为群集的名称。 出现提示时，输入 SSH 用户帐户的密码。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. 通过连接到 HiveServer2 Beeline 客户端与打开的 SSH 会话中输入以下命令：

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Beeline 命令以 `!` 字符开头，例如，`!help` 会显示帮助。 但是，`!` 对于某些命令可以省略。 例如，`help` 也是有效的。

    有一个 `!sql`，用于执行 HiveQL 语句。 但是，由于 HiveQL 非常流行，因此可以省略前面的 `!sql`。 以下两个语句等效：

    ```hiveql
    !sql show tables;
    show tables;
    ```

    在新群集上，只会列出一个表：**hivesampletable**。

4. 使用以下命令显示 hivesampletable 的架构：

    ```hiveql
    describe hivesampletable;
    ```

    此命令返回以下信息：

        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    此信息描述表中的列。

5. 输入以下语句，以使用 HDInsight 群集随附的示例数据来创建名为 **log4jLogs** 的表：(根据需要修改基于你[URI 方案](../hdinsight-hadoop-linux-information.md#URI-and-scheme)。)

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs 
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' 
        GROUP BY t4;
    ```

    这些语句将执行以下操作：

    * `DROP TABLE` -如果表存在，它删除。

    * `CREATE EXTERNAL TABLE` -创建**外部**中 Hive 表。 外部表只会在 Hive 中存储表定义。 数据将保留在原始位置。

    * `ROW FORMAT` -如何格式化数据。 在此情况下，每个日志中的字段以空格分隔。

    * `STORED AS TEXTFILE LOCATION` -将数据存储位置和文件格式。

    * `SELECT` -选择所有行的计数的列**t4**包含值 **[ERROR]**。 此查询返回值 **3**，因为有三行包含此值。

    * `INPUT__FILE__NAME LIKE '%.log'` -Hive 会尝试将架构应用于目录中的所有文件。 在这种情况下，目录包含与架构不匹配的文件。 为防止结果中包含垃圾数据，此语句指示 Hive 应当仅返回以 .log 结尾的文件中的数据。

   > [!NOTE]  
   > 如果希望通过外部源更新基础数据，应使用外部表。 例如，自动化数据上传进程或 MapReduce 操作。
   >
   > 删除外部表**不会**删除数据，只会删除表定义。

    此命令的输出类似于以下文本：

        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :

        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

6. 若要退出 Beeline，请使用 `!exit`。

## <a id="file"></a>运行 HiveQL 文件

这是延续的前面的示例。 使用以下步骤创建文件，并使用 Beeline 运行该文件。

1. 使用以下命令创建一个名为 **query.hql** 的文件：

    ```bash
    nano query.hql
    ```

2. 将以下文本用作文件的内容。 此查询创建名为 **errorLogs** 的新“内部”表：

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    这些语句将执行以下操作：

   * **CREATE TABLE IF NOT EXISTS** - 创建表（如果该表尚不存在）。 因为未使用 **EXTERNAL** 关键字，此语句创建内部表。 内部表存储在 Hive 数据仓库中，由 Hive 全权管理。
   * **STORED AS ORC**：以优化行纵栏表 (ORC) 格式存储数据。 ORC 格式是高度优化且有效的 Hive 数据存储格式。
   * **INSERT OVERWRITE ...SELECT**- 从包含 **[ERROR]** 的 **log4jLogs** 表中选择行，然后将数据插入 **errorLogs** 表中。

    > [!NOTE]  
    > 与外部表不同，删除内部表会同时删除基础数据。

3. 要保存文件，请使用 **Ctrl**+**_X**，并输入 **Y**，最后按 **Enter**。

4. 使用以下命令以通过 Beeline 运行该文件：

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > `-i` 参数将启动 Beeline，并运行 `query.hql` 文件中的语句。 查询完成后，会出现 `jdbc:hive2://headnodehost:10001/>` 提示符。 还可以使用 `-f` 参数运行文件，该参数在查询完成后会退出 Beeline。

5. 若要验证是否已创建 **errorLogs** 表，请使用以下语句从 **errorLogs** 返回所有行：

    ```hiveql
    SELECT * from errorLogs;
    ```

    应返回三行数据，所有行都包含 t4 列中的 **[ERROR]**：

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)




## <a id="summary"></a><a id="nextsteps"></a>后续步骤

有关 HDInsight 中 Hive 的更多常规信息，请参阅以下文档：

* [Apache Hadoop on HDInsight 中使用 Apache hive 配合使用](hdinsight-use-hive.md)

若要深入了解使用 Hadoop on HDInsight 的其他方法，请参阅以下文档：

* [将 Apache Pig 与 Apache Hadoop on HDInsight 配合使用](hdinsight-use-pig.md)
* [将 MapReduce 与 HDInsight 上的 Apache Hadoop](hdinsight-use-mapreduce.md)

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md

[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx
