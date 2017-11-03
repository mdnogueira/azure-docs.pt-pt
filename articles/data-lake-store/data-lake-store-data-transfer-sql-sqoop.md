---
title: Copiar dados entre o Data Lake Store e a SQL database do Azure utilizando o Sqoop | Microsoft Docs
description: Utilizar o Sqoop para copiar dados entre SQL Database do Azure e a Data Lake Store
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 3f914b2a-83cc-4950-b3f7-69c921851683
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/03/2017
ms.author: nitinme
ms.openlocfilehash: ac73006f3589bf6d8adb0d7935b694b444d1415c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-between-data-lake-store-and-azure-sql-database-using-sqoop"></a>Copiar dados entre o Data Lake Store e a SQL database do Azure utilizando o Sqoop
Saiba como utilizar o Apache Sqoop para importar e exportar dados entre SQL Database do Azure e o Data Lake Store.

## <a name="what-is-sqoop"></a>O que é Sqoop?
Aplicações de macrodados são uma opção natural para processamento de dados não estruturados e semiestruturados, tais como registos e ficheiros. No entanto, podem também existir uma necessidade para processar dados estruturados, que são armazenados nas bases de dados relacionais.

[O Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) é uma ferramenta concebida para transferir dados entre bases de dados relacionais e um repositório de macrodados, tais como o Data Lake Store. Pode utilizá-lo para importar dados a partir de um sistema de gestão de base de dados relacional (RDBMS), tais como SQL Database do Azure para o Data Lake Store. Pode, em seguida, transformar e analisar dados com cargas de trabalho de macrodados e, em seguida, exportar os dados de volta para um RDBMS. Neste tutorial, utilize uma base de dados do SQL do Azure como a base de dados relacional para importar/exportar do.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, tem de ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do Azure Data Lake Store**. Para obter instruções sobre como criar um, consulte [introdução ao Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Cluster do HDInsight Azure** com acesso a uma conta de Data Lake Store. Consulte [criar um cluster do HDInsight com o Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Este artigo pressupõe que tem um cluster do HDInsight com Linux com acesso de Data Lake Store.
* **Base de Dados SQL do Azure**. Para obter instruções sobre como criar um, consulte [criar uma base de dados SQL do Azure](../sql-database/sql-database-get-started.md)

## <a name="do-you-learn-fast-with-videos"></a>Aprende depressa com vídeos?
[Veja este vídeo](https://mix.office.com/watch/1butcdjxmu114) sobre como copiar dados entre o Blobs Storage do Azure e utilizar o DistCp do Data Lake Store.

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Criar tabelas de exemplo na base de dados do SQL do Azure
1. Para começar, crie duas tabelas de exemplo na base de dados do SQL do Azure. Utilize [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) ou o Visual Studio para ligar à SQL Database do Azure e, em seguida, execute as seguintes consultas.

    **Criar Table1**

        CREATE TABLE [dbo].[Table1](
        [ID] [int] NOT NULL,
        [FName] [nvarchar](50) NOT NULL,
        [LName] [nvarchar](50) NOT NULL,
         CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED
            (
                   [ID] ASC
            )
        ) ON [PRIMARY]
        GO

    **Criar tabela2**

        CREATE TABLE [dbo].[Table2](
        [ID] [int] NOT NULL,
        [FName] [nvarchar](50) NOT NULL,
        [LName] [nvarchar](50) NOT NULL,
         CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED
            (
                   [ID] ASC
            )
        ) ON [PRIMARY]
        GO
2. No **Table1**, adicionar alguns dados de exemplo. Deixe **Table2** vazio. Iremos importar dados a partir de **Table1** no Data Lake Store. Em seguida, vamos exportar dados do Data Lake Store para **Table2**. Execute o seguinte fragmento.

        INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');


## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-store"></a>Utilizar o Sqoop de um cluster do HDInsight com acesso ao Data Lake Store
Um cluster do HDInsight já tem os pacotes de Sqoop disponíveis. Se tiver configurado o cluster do HDInsight ao utilizar o Data Lake Store como um armazenamento adicional, pode utilizar o Sqoop (sem quaisquer alterações de configuração) para importar/exportar dados entre uma base de dados relacional (neste exemplo, SQL Database do Azure) e uma conta de Data Lake Store .

1. Para este tutorial, iremos partem do princípio de que criou um cluster do Linux, pelo que deve utilizar o SSH para ligar ao cluster. Consulte [ligar a um cluster do HDInsight baseado em Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
2. Certifique-se de que o se pode aceder à conta de Data Lake Store do cluster. Execute o seguinte comando a partir da linha de SSH:

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net/

    Isto deve fornecer uma lista de ficheiros/pastas na conta do Data Lake Store.

### <a name="import-data-from-azure-sql-database-into-data-lake-store"></a>Importar dados do SQL Database do Azure para o Data Lake Store
1. Navegue para o diretório onde o Sqoop pacotes estão disponíveis. Normalmente, esta será em `/usr/hdp/<version>/sqoop/bin`.
2. Importar dados do **Table1** para a conta de Data Lake Store. Utilize a seguinte sintaxe:

        sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

    Tenha em atenção que **-base de dados--nome do servidor sql** marcador de posição representa o nome do servidor onde a base de dados SQL do Azure está em execução. **nome da base de dados SQL** marcador de posição representa o nome de base de dados real.

    Por exemplo,


        sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1

1. Certifique-se de que os dados foi transferidos para a conta de Data Lake Store. Execute o seguinte comando:

        hdfs dfs -ls adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/

    Deverá ver o seguinte resultado.


        -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
        -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
        -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
        -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
        -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

    Cada **parte-m -*** ficheiro corresponde a uma linha na tabela de origem, **Table1**. Pode ver o conteúdo da peça - m-* ficheiros para verificar.


### <a name="export-data-from-data-lake-store-into-azure-sql-database"></a>Exportar dados do Data Lake Store numa SQL Database do Azure
1. Exportar os dados da conta do Data Lake Store para a tabela vazia, **Table2**, na base de dados do SQL do Azure. Utilize a seguinte sintaxe.

        sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

    Por exemplo,


        sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. Certifique-se de que os dados foi carregados para a tabela de base de dados SQL. Utilize [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) ou o Visual Studio para ligar à SQL Database do Azure e, em seguida, execute a consulta seguinte.

        SELECT * FROM TABLE2

    Isto deve ter o seguinte resultado.

         ID  FName   LName
        ------------------
        1    Neal    Kell
        2    Lila    Fulton
        3    Erna    Myers
        4    Annette    Simpson

## <a name="performance-considerations-while-using-sqoop"></a>Considerações sobre o desempenho ao utilizar o Sqoop

Para sua tarefa Sqoop para copiar dados para o Data Lake Store de otimização do desempenho, consulte [documento de desempenho Sqoop](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/).

## <a name="see-also"></a>Consultar também
* [Copiar dados de Blobs de armazenamento do Azure para o Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
* [Secure data in Data Lake Store (Proteger dados no Data Lake Store)](data-lake-store-secure-data.md)
* [Utilizar o Azure Data Lake Analytics com o Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Use Azure HDInsight with Data Lake Store (Utilizar o Azure HDInsight com o Data Lake Store)](data-lake-store-hdinsight-hadoop-use-portal.md)
