---
title: Transformar dados utilizando a atividade de procedimento armazenado no Azure Data Factory | Microsoft Docs
description: "Explica como utilizar a atividade de procedimento armazenado do SQL Server para invocar um procedimento armazenado num armazém de base de dados SQL do Azure a partir de um pipeline do Data Factory."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: shengc
ms.openlocfilehash: f13f8aa0ca8686c0582bed77d047c9e6b39f7aa2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Transformar dados utilizando a atividade de procedimento de armazenado do SQL Server no Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-stored-proc-activity.md)
> * [Versão 2 - Pré-visualização](transform-data-using-stored-procedure.md)


Utilize atividades de transformação de dados numa fábrica de dados [pipeline](concepts-pipelines-activities.md) para transformar e processar dados não processados para predições e das informações. A atividade de procedimento armazenado é uma das atividades de transformação que suporte a fábrica de dados. Este artigo baseia-se a [transformar dados](transform-data.md) artigo, que apresenta uma descrição geral de transformação de dados e as atividades de transformação suportado na fábrica de dados.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [atividade de procedimento armazenado no V1](v1/data-factory-stored-proc-activity.md).
> 
> Se estiver familiarizado com o Azure Data Factory, leia [introdução ao Azure Data Factory](introduction.md) e efetue o tutorial: [Tutorial: transformar dados](tutorial-transform-data-spark-powershell.md) antes de ler este artigo. 

Pode utilizar a atividade de procedimento armazenado para invocar um procedimento armazenado dos arquivos de dados seguintes na sua empresa ou numa máquina virtual do Azure (VM): 

- Base de Dados SQL do Azure
- Azure SQL Data Warehouse
- Base de dados do SQL Server.  Se estiver a utilizar o SQL Server, instale o runtime de integração personalizada alojada no mesmo computador que aloja a base de dados ou num computador separado que tenha acesso à base de dados. Tempo de execução automática alojada integração é um componente que liga dados origens no local/no VM do Azure com serviços em nuvem de forma segura e gerida. Consulte [autoalojado integração runtime](create-self-hosted-integration-runtime.md) artigo para obter detalhes.

> [!IMPORTANT]
> Quando copiar dados para a SQL Database do Azure ou SQL Server, pode configurar o **SqlSink** na atividade de cópia para invocar um procedimento armazenado utilizando o **sqlWriterStoredProcedureName** propriedade. Para obter detalhes sobre a propriedade, consulte os artigos de conector: [SQL Database do Azure](connector-azure-sql-database.md), [do SQL Server](connector-sql-server.md). Invocar um procedimento armazenado ao copiar dados para um Azure SQL Data Warehouse, utilizando uma atividade de cópia não é suportada. No entanto, pode utilizar a atividade de procedimento armazenado para invocar um procedimento armazenado num armazém de dados SQL. 
>
> Quando copiar dados de SQL Database do Azure ou SQL Server ou o Azure SQL Data Warehouse, pode configurar **SqlSource** na atividade de cópia para invocar um procedimento armazenado para ler os dados da base de dados de origem utilizando o  **sqlReaderStoredProcedureName** propriedade. Para obter mais informações, consulte os seguintes artigos de conector: [SQL Database do Azure](connector-azure-sql-database.md), [do SQL Server](connector-sql-server.md), [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Detalhes de sintaxe
Eis o formato JSON para definir uma atividade de procedimento armazenado:

```json
{
    "name": "Stored Procedure Activity",
    "description":"Description",
    "type": "SqlServerStoredProcedure",
    "linkedServiceName": {
        "referenceName": "AzureSqlLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "storedProcedureName": "sp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

A tabela seguinte descreve estas propriedades JSON:

| Propriedade                  | Descrição                              | Necessário |
| ------------------------- | ---------------------------------------- | -------- |
| nome                      | Nome da atividade                     | Sim      |
| descrição               | Texto que descreve o que é utilizada a atividade para | Não       |
| tipo                      | Para a atividade de procedimento armazenados, o tipo de atividade é SqlServerStoredProcedure | Sim      |
| linkedServiceName         | Referência para a SQL Database do Azure ou o Azure SQL Data Warehouse ou o SQL Server registado como um serviço ligado no Factory de dados. Para saber mais sobre este serviço ligado, consulte [serviços ligados de computação](compute-linked-services.md) artigo. | Sim      |
| storedProcedureName       | Especifique o nome do procedimento armazenado na base de dados SQL do Azure ou do armazém de dados do SQL do Azure ou SQL Server da base de dados que é representada pelo serviço ligado que utilize a tabela de saída. | Sim      |
| storedProcedureParameters | Especifique os valores dos parâmetros de procedimento armazenado. Utilize `"param1": { "value": "param1Value","type":"param1Type" }` transmitir parâmetro valores e o respetivo tipo nativo suportada pela origem de dados. Se tiver de passar nulo para um parâmetro, utilize *"param1": {"valor": nulo}* (todas as minúsculas). | Não       |

## <a name="next-steps"></a>Passos seguintes
Consulte os artigos seguintes que explicam como transformar dados de outras formas: 

* [Atividade U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade do ramo de registo](transform-data-using-hadoop-hive.md)
* [Atividade do PIg](transform-data-using-hadoop-pig.md)
* [Atividade de MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de transmissão em fluxo do Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade do Spark](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de execução do Machine Learning Bach](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
