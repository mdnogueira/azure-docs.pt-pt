---
title: "Invocar um procedimento armazenado de atividade de cópia de fábrica de dados do Azure | Microsoft Docs"
description: "Saiba como invocar um procedimento armazenado na base de dados do Azure SQL ou SQL Server de uma atividade de cópia de Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 460785d0d3f8b3d8a0a53d544788cb1a74db8c00
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>Invocar um procedimento armazenado de atividade de cópia no Azure Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [transformar dados utilizando a atividade de procedimento armazenado na versão 2 do Data Factory](../transform-data-using-stored-procedure.md).


Ao copiar dados para [do SQL Server](data-factory-sqlserver-connector.md) ou [SQL Database do Azure](data-factory-azure-sql-connector.md), pode configurar o **SqlSink** na atividade de cópia para invocar um procedimento armazenado. Poderá pretender utilizar o armazenado é necessário o procedimento para efetuar qualquer processamento adicional (intercalação colunas, procurar valores de inserção em várias tabelas, etc.) antes de inserir os dados na tabela de destino. Esta funcionalidade tira partido da [Table-Valued parâmetros](https://msdn.microsoft.com/library/bb675163.aspx). 

O exemplo seguinte mostra como invocar um procedimento armazenado numa base de dados do SQL Server a partir de um pipeline (atividade de cópia) do Data Factory:  

## <a name="output-dataset-json"></a>Conjunto de dados de saída JSON
O conjunto de dados de saída JSON, defina o **tipo** para: **SqlServerTable**. Defina-o como **AzureSqlTable** para utilizar com uma base de dados SQL do Azure. O valor para **tableName** propriedade tem de corresponder ao nome do primeiro parâmetro do procedimento armazenado.  

```json
{
  "name": "SqlOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlLinkedService",
    "typeProperties": {
      "tableName": "Marketing"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

## <a name="sqlsink-section-in-copy-activity-json"></a>Secção SqlSink JSON da atividade de cópia
Definir o **SqlSink** secção o JSON da atividade de cópia da seguinte forma. Para invocar um procedimento armazenado ao inserir dados na base de dados de destino/sink, especifique valores para ambos **SqlWriterStoredProcedureName** e **SqlWriterTableType** propriedades. Para obter descrições destas propriedades, consulte [SqlSink secção do artigo de conector do SQL Server](data-factory-sqlserver-connector.md#sqlsink).

```json
"sink":
{
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
    "storedProcedureParameters":
            {
                "stringData": 
                {
                    "value": "str1"     
                }
            }
}
```

## <a name="stored-procedure-definition"></a>Definição de procedimento armazenado 
Na base de dados, definir o procedimento armazenado com o mesmo nome que **SqlWriterStoredProcedureName**. O procedimento armazenado processa dados de entrada do arquivo de dados de origem e insere dados numa tabela na base de dados de destino. O nome do primeiro parâmetro do procedimento armazenado tem de corresponder ao tableName definida no conjunto de dados JSON (Marketing).

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
AS
BEGIN
    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    INSERT [dbo].[Marketing](ProfileID, State)
    SELECT * FROM @Marketing
END
```

## <a name="table-type-definition"></a>Definição de tipo de tabela
Na base de dados, definir o tipo de tabela com o mesmo nome que **SqlWriterTableType**. O esquema do tipo de tabela deve coincidir com o esquema do conjunto de dados de entrada.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>Passos seguintes
Reveja o conector seguinte artigos que, para obter exemplos JSON concluídos: 

- [Base de Dados SQL do Azure](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
