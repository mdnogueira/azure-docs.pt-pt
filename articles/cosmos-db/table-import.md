---
title: "Importar dados para utilização com a API de tabela de base de dados do Azure Cosmos | Microsoft Docs"
description: Saiba como importar dados para utilizar com a API de tabela de base de dados do Azure Cosmos.
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: b60743e2-0227-43ab-965a-0ae3ebacd917
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: mimig
ms.openlocfilehash: 5163d20aece01addddeae93cb07355bde928a440
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="import-data-for-use-with-the-azure-cosmos-db-table-api"></a>Importar dados para utilização com a API de tabela de base de dados do Azure Cosmos

Este tutorial fornece instruções sobre importar dados para utilização com a base de dados do Azure Cosmos [API de tabela](table-introduction.md). Se tiver dados armazenados no armazenamento de tabelas do Azure, pode utilizar a ferramenta de migração de dados ou o AzCopy para importar os seus dados. Se tiver dados armazenados numa conta API de tabela de base de dados do Azure Cosmos (pré-visualização), tem de utilizar a ferramenta de migração de dados para migrar os dados. Depois dos dados são importados, poderá tirar partido das funcionalidades premium ofertas de BD do Cosmos do Azure, tais como chave na mão distribuição global, débito dedicado, latências dígito milissegundo, o percentil 99th, garantida elevada disponibilidade, e indexação secundário automática.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Importar dados com a ferramenta de migração de dados
> * Importar dados com o AzCopy
> * Migrar a partir da tabela de API (pré-visualização) à API de tabela 

## <a name="data-migration-tool"></a>Ferramenta de migração de dados

A ferramenta de migração de dados do Azure Cosmos DB da linha de comandos (dt.exe) pode ser utilizada para importar os dados de armazenamento de tabelas do Azure existentes para uma conta de API de tabela GA ou migra os dados de uma conta de API de tabela (pré-visualização) para uma conta de API de tabela GA. Outras origens não são atualmente suportadas. A IU com base em dados de migração ferramenta (dtui.exe) não é atualmente suportada para contas de API de tabela. 

Para efetuar uma migração de dados de tabela, conclua as seguintes tarefas:

1. Transferir a ferramenta de migração de [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Executar `dt.exe` utilizando os argumentos da linha de comandos para o seu cenário.

DT.exe executa um comando no seguinte formato:

    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 

As opções para o comando são:

    /ErrorLog: Optional. Name of the CSV file to redirect data transfer failures
    /OverwriteErrorLog: Optional. Overwrite error log file
    /ProgressUpdateInterval: Optional, default is 00:00:01. Time interval to refresh on-screen data transfer progress
    /ErrorDetails: Optional, default is None. Specifies that detailed error information should be displayed for the following errors: None, Critical, All

### <a name="command-line-source-settings"></a>Definições da origem da linha de comandos

Utilize as seguintes opções de origem quando se definem pré-visualização do Table Storage do Azure ou a API de tabela como origem da migração.

    /s:AzureTable: Reads data from Azure Table storage
    /s.ConnectionString: Connection string for the table endpoint. This can be retrieved from the Azure portal
    /s.LocationMode: Optional, default is PrimaryOnly. Specifies which location mode to use when connecting to Azure Table storage: PrimaryOnly, PrimaryThenSecondary, SecondaryOnly, SecondaryThenPrimary
    /s.Table: Name of the Azure Table
    /s.InternalFields: Set to All for table migration as RowKey and PartitionKey are required for import.
    /s.Filter: Optional. Filter string to apply
    /s.Projection: Optional. List of columns to select

Para obter a cadeia de ligação de origem quando importar do Table storage do Azure, abra o portal do Azure e clique em **contas do Storage** > **conta**  >   **Chaves de acesso**e, em seguida, utilize o botão Copiar para copiar o **cadeia de ligação**.

![Opções de origem de captura de ecrã do HBase](./media/table-import/storage-table-access-key.png)

Para obter a cadeia de ligação de origem ao importar a partir de uma conta de API de tabela de base de dados do Azure Cosmos (pré-visualização), abra o clique portal, do Azure **Azure Cosmos DB** > **conta**  >  **Cadeia de ligação** e utilize o botão Copiar para copiar o **cadeia de ligação**.

![Opções de origem de captura de ecrã do HBase](./media/table-import/cosmos-connection-string.png)

[Comando de Table Storage do Azure de exemplo](#azure-table-storage)

[Comando da API de tabela de base de dados do Azure Cosmos (pré-visualização) de exemplo](#table-api-preview)

### <a name="command-line-target-settings"></a>Definições do destino da linha de comandos

Utilize as seguintes opções de destino quando definir a API de tabela de base de dados do Azure Cosmos como o destino da migração.

    /t:TableAPIBulk: Uploads data into Azure CosmosDB Table in batches
    /t.ConnectionString: Connection string for the table endpoint
    /t.TableName: Specifies the name of the table to write to
    /t.Overwrite: Optional, default is false. Specifies if existing values should be overwritten
    /t.MaxInputBufferSize: Optional, default is 1GB. Approximate estimate of input bytes to buffer before flushing data to sink
    /t.Throughput: Optional, service defaults if not specified. Specifies throughput to configure for table
    /t.MaxBatchSize: Optional, default is 2MB. Specify the batch size in bytes

<a id="azure-table-storage"></a>
### <a name="sample-command-source-is-azure-table-storage"></a>Comandos de exemplo: origem é o Table storage do Azure

Eis um exemplo de linha de comandos que mostra como importar do Table storage do Azure para a API de tabela:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey==<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```
<a id="table-api-preview"></a>
### <a name="sample-command-source-is-azure-cosmos-db-table-api-preview"></a>Comandos de exemplo: origem é a API de tabela de base de dados do Azure Cosmos (pré-visualização)

Eis um exemplo de linha de comandos para importar a partir de pré-visualização de API de tabela à tabela API GA:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Table API preview account name>;AccountKey=<Table API preview account key>;TableEndpoint=https://<Account Name>.documents.azure.com; /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="azcopy-command"></a>Comandos do AzCopy

Utilizando o utilitário de linha de comandos do AzCopy é a outra opção para migrar dados do Table storage do Azure para a API de tabela de base de dados do Azure Cosmos. Para utilizar o AzCopy, primeiro exportar os dados conforme descrito em [exportar dados do armazenamento de tabelas](../storage/common/storage-use-azcopy.md#export-data-from-table-storage), em seguida, importar os dados à base de dados do Azure Cosmos conforme descrito em [API de tabela de base de dados do Azure Cosmos](../storage/common/storage-use-azcopy.md#import-data-into-table-storage).

Quando efetuar a importação para o Azure Cosmos DB, consulte o exemplo seguinte. Tenha em atenção que o valor de /Dest utiliza cosmosdb, não principal.

Comando de importação de exemplo:

```
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="migrate-from-table-api-preview-to-table-api"></a>Migrar a partir da tabela de API (pré-visualização) à API de tabela

> [!WARNING]
> Se pretender desfrutar imediatamente as vantagens das tabelas geralmente disponíveis, em seguida,. Migre as tabelas de pré-visualização existente conforme especificado nesta secção, caso contrário, iremos irá efetuar migrações automática para clientes de pré-visualização existentes nas próximas semanas, tenha em atenção No entanto, o que automática migrados tabelas de pré-visualização terão determinadas restrições-lhes que recentemente criado será de tabelas não.
> 

A API de tabela está normalmente disponível (GA). Não existem diferenças entre as versões de GA das tabelas tanto no código que é executado na nuvem, bem como em código que é executado no cliente e de pré-visualização. Por isso não é aconselhada para tentar combinar um cliente do SDK de pré-visualização com uma conta de API de tabela DG e vice-versa. Os clientes de pré-visualização de API que pretendem continuar a utilizar as respetivas tabelas existentes, mas num ambiente de produção tem de migrar a partir de pré-visualização para o ambiente de GA ou aguarde que a migração automática de tabela. Se a aguardar a migração automática, será notificado das restrições nas tabelas migradas. Após a migração, poderá criar novas tabelas na sua conta existente sem restrições (apenas tabelas migradas terão restrições).

Para migrar a partir da API de tabela (pré-visualização) para a API de tabela geralmente disponível:

1. Criar uma nova conta de base de dados do Azure Cosmos e defina o respetivo tipo de API à tabela de Azure conforme descrito em [criar uma conta de base de dados](create-table-dotnet.md#create-a-database-account).

2. Alterar aos clientes utilizar uma versão GA do [SDKs de API de tabela](table-sdk-dotnet.md).

3. Migre os dados de cliente de tabelas de pré-visualização para tabelas de GA utilizando a ferramenta de migração de dados. Instruções sobre como utilizar a ferramenta de migração de dados para esta finalidade descritas [ferramenta de migração de dados](#data-migration-tool). 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Importar dados com a ferramenta de migração de dados
> * Importar dados com o AzCopy
> * Migrar a partir da tabela de API (pré-visualização) à API de tabela

Agora pode avançar para o próximo tutorial e saiba como consultar dados utilizando a API de tabela de base de dados do Azure Cosmos. 

> [!div class="nextstepaction"]
>[Como consultar dados?](../cosmos-db/tutorial-query-table.md)
