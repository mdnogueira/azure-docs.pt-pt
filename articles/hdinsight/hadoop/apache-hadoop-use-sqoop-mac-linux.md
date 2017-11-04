---
title: O Apache Sqoop com o Hadoop - o Azure HDInsight | Microsoft Docs
description: Saiba como utilizar o Apache Sqoop para importar e exportar entre o Hadoop no HDInsight e uma base de dados do SQL do Azure.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
author: Blackmist
tags: azure-portal
keywords: hadoop sqoop, sqoop
ms.assetid: 303649a5-4be5-4933-bf1d-4b232083c354
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: larryfr
ms.openlocfilehash: 250fb1dfed5cdab5308c2d91744e0cf051c32ccc
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-hadoop-on-hdinsight-and-sql-database"></a>Utilizar o Apache Sqoop para importar e exportar dados entre o Hadoop no HDInsight e a base de dados SQL

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como utilizar o Apache Sqoop para importar e exportar entre um cluster do Hadoop no Azure HDInsight e a base de dados SQL Database do Azure ou Microsoft SQL Server. Os passos neste documento utilize o `sqoop` comando diretamente a partir do headnode do cluster de Hadoop. Utilizar o SSH para ligar ao nó principal e execute os comandos neste documento.

> [!IMPORTANT]
> Os passos neste documento apenas trabalham com clusters do HDInsight que utilizam o Linux. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

> [!WARNING]
> Os passos neste documento partem do princípio de que já criou uma base de dados de SQL do Azure com o nome `sqooptest`.
>
> Este documento fornece instruções de T-SQL que são utilizadas para criar e consultar uma tabela na base de dados do SQL Server. Existem muitos clientes que pode utilizar estas instruções com base de dados do SQL Server. Recomendamos os seguintes clientes:
>
> * [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md)
> * [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md)
> * O [sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility) utilitário.

## <a name="create-the-table-in-sql-database"></a>Criar a tabela na base de dados do SQL Server

> [!IMPORTANT]
> Se estiver a utilizar o cluster do HDInsight e a base de dados SQL criadas no [criar o cluster e a SQL database](hdinsight-use-sqoop.md), ignorar os passos nesta secção. A base de dados e tabela foram criados como parte dos passos de [criar o cluster e a SQL database](hdinsight-use-sqoop.md) documento.

Utilizar um cliente do SQL Server para ligar para o `sqooptest` base de dados na base de dados SQL. Em seguida, utilize o seguinte T-SQL para criar uma tabela com o nome `mobiledata`:

```sql
CREATE TABLE [dbo].[mobiledata](
[clientid] [nvarchar](50),
[querytime] [nvarchar](50),
[market] [nvarchar](50),
[deviceplatform] [nvarchar](50),
[devicemake] [nvarchar](50),
[devicemodel] [nvarchar](50),
[state] [nvarchar](50),
[country] [nvarchar](50),
[querydwelltime] [float],
[sessionid] [bigint],
[sessionpagevieworder] [bigint])
GO
CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
GO
```

## <a name="sqoop-export"></a>Exportação de Sqoop

1. Utilize o SSH para ligar ao cluster do HDInsight. Por exemplo, o seguinte comando liga-se a headnode principal de um cluster com o nome `mycluster`:

    ```bash
    ssh CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Para verificar se Sqoop pode ver a sua base de dados do SQL Server, utilize o seguinte comando:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> -P
    ```
    Quando lhe for pedido, introduza a palavra-passe para o início de sessão da base de dados SQL.

    Este comando devolve uma lista de bases de dados, incluindo o **sqooptest** base de dados utilizada anteriormente.

3. Para exportar dados do ramo do **hivesampletable** tabela para o **mobiledata** tabela na base de dados do SQL Server, utilize o seguinte comando:

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Para verificar que os dados foram exportados, utilize a seguinte consulta do seu cliente SQL para ver os dados exportados:

    ```sql
    SET ROWCOUNT 50;
    SELECT * FROM mobiledata;"
    ```

    Este comando lista 50 linhas que foram importadas para a tabela.

## <a name="sqoop-import"></a>Importação de Sqoop

1. Utilize o seguinte comando para importar dados a partir de **mobiledata** tabela na base de dados do SQL Server, para o **wasb: / / / tutoriais/usesqoop/importeddata** diretório no HDInsight:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

    Os campos de dados são separados por um caráter de separador e as linhas são terminadas por um caráter de nova linha.

    > [!IMPORTANT]
    > O `wasb:///` caminho funciona com clusters que utilizam o armazenamento do Azure como armazenamento de cluster predefinido. Para os clusters que utilizam o Azure Data Lake Store, utilize `adl:///` em vez disso.

2. Depois de concluída a importação, utilize o seguinte comando para a lista de saída de dados no novo diretório:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="using-sql-server"></a>Utilizar o SQL Server

Também pode utilizar o Sqoop para importar e exportar dados do SQL Server. As diferenças entre a utilização da base de dados do SQL Server e SQL Server são:

* HDInsight e SQL Server tem de ser na mesma rede Virtual do Azure.

    Por exemplo, consulte o [HDInsight ligar à sua rede no local](./../connect-on-premises-network.md) documento.

    Para obter mais informações sobre como utilizar o HDInsight com uma rede Virtual do Azure, consulte o [expandir HDInsight com o Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md) documento. Para obter mais informações sobre a rede Virtual do Azure, consulte o [descrição geral de rede Virtual](../../virtual-network/virtual-networks-overview.md) documento.

* SQL Server deve ser configurado para permitir a autenticação do SQL Server. Para obter mais informações, consulte o [escolher um modo de autenticação](https://msdn.microsoft.com/ms144284.aspx) documento.

* Poderá ter de configurar o SQL Server para aceitar ligações remotas. Para obter mais informações, consulte o [como resolver problemas de ligação para o motor de base de dados do SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) documento.

* Utilize as seguintes declarações de Transact-SQL para criar o **mobiledata** tabela:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [clientid] [nvarchar](50),
    [querytime] [nvarchar](50),
    [market] [nvarchar](50),
    [deviceplatform] [nvarchar](50),
    [devicemake] [nvarchar](50),
    [devicemodel] [nvarchar](50),
    [state] [nvarchar](50),
    [country] [nvarchar](50),
    [querydwelltime] [float],
    [sessionid] [bigint],
    [sessionpagevieworder] [bigint])
    ```

* Ao ligar ao SQL Server do HDInsight, poderá ter de utilizar o endereço IP do SQL Server. Por exemplo:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> -P <adminPassword> -table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

## <a name="limitations"></a>Limitações

* Exportação em massa - baseado em Linux com o HDInsight, o conector de Sqoop utilizado para exportar dados para o Microsoft SQL Server ou SQL Database do Azure não suporta inserções em massa.

* Criação de batches - com o HDInsight baseado em Linux, ao utilizar o `-batch` comutador quando efetuar inserções, Sqoop faz com que várias inserções em vez das operações de inserção de criação de batches.

## <a name="next-steps"></a>Passos seguintes

Agora tem aprendeu a utilizar o Sqoop. Para saber mais, consulte:

* [Utilizar o Oozie com o HDInsight](../hdinsight-use-oozie.md): Utilize Sqoop ação um fluxo de trabalho do Oozie.
* [Analisar dados de atraso de voo utilizando HDInsight](../hdinsight-analyze-flight-delay-data.md): utilizar o Hive para analisar voo atrasar dados e, em seguida, utilizar o Sqoop para exportar dados para uma base de dados SQL do Azure.
* [Carregar dados para o HDInsight](../hdinsight-upload-data.md): localizar outros métodos para carregar dados para armazenamento de Blobs do HDInsight/Azure.

[hdinsight-versions]:  ../hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
