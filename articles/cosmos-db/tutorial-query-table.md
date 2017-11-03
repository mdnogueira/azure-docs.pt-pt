---
title: Como dados da tabela de consulta na base de dados do Azure Cosmos? | Microsoft Docs
description: Saiba como consultar dados de tabela na base de dados do Azure Cosmos
services: cosmos-db
documentationcenter: 
author: kanshiG
manager: jhubbard
editor: 
tags: 
ms.assetid: 14bcb94e-583c-46f7-9ea8-db010eb2ab43
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/10/2017
ms.author: govindk
ms.openlocfilehash: e59cfa85c6bf584e44bdc6e88cc19d67df390041
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-how-to-query-table-data-by-using-the-table-api-preview"></a>Azure Cosmos DB: Como consultar dados da tabela, utilizando a API de tabela (pré-visualização)?

A BD do Cosmos Azure [API de tabela](table-introduction.md) (pré-visualização) suporta OData e [LINQ](https://docs.microsoft.com/rest/api/storageservices/fileservices/writing-linq-queries-against-the-table-service) consultas em relação a dados de chave/valor (tabela).  

Este artigo abrange as seguintes tarefas: 

> [!div class="checklist"]
> * Consultar os dados com a API de tabela

As consultas neste artigo, utilize o seguinte exemplo `People` tabela:

| PartitionKey | RowKey | E-mail | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0101 |
| Santos | Bernardo | Ben@contoso.com| 425-555-0102 |
| Santos | Jorge | Jeff@contoso.com| 425-555-0104 | 

Porque a base de dados do Azure Cosmos é compatível com as APIs de armazenamento de tabelas do Azure, consulte [consultar tabelas e entidades] (https://docs.microsoft.com/rest/api/storageservices/fileservices/querying-tables-and-entities) para obter detalhes sobre a consulta utilizando a tabela API. 

Para obter mais informações sobre as capacidades de premium oferece de BD do Cosmos do Azure, consulte [BD do Azure Cosmos: API de tabela](table-introduction.md) e [desenvolver com a API de tabela no .NET](tutorial-develop-table-dotnet.md). 

## <a name="prerequisites"></a>Pré-requisitos

Para estas consultas funcione, tem de ter uma conta de base de dados do Azure Cosmos e ter dados de entidade no contentor. Não tem qualquer um desses? Concluir o [início rápido de cinco minutos](https://aka.ms/acdbtnetqs) ou [tutorial programador](https://aka.ms/acdbtabletut) para criar uma conta e preencher a base de dados.

## <a name="query-on-partitionkey-and-rowkey"></a>A consulta de PartitionKey e RowKey
Porque as propriedades PartitionKey e RowKey formam a chave primária de uma entidade, pode utilizar a seguinte sintaxe especial para identificar a entidade: 

**Consulta**

```
https://<mytableendpoint>/People(PartitionKey='Harp',RowKey='Walter')  
```
**Resultados**

| PartitionKey | RowKey | E-mail | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0104 |

Em alternativa, pode especificar estas propriedades como parte do `$filter` opção, conforme mostrado na secção seguinte. Tenha em atenção que os nomes de propriedade de chave e os valores constantes diferenciam maiúsculas de minúsculas. As PartitionKey RowKey propriedades e são do tipo cadeia. 

## <a name="query-by-using-an-odata-filter"></a>Consultar utilizando um filtro OData
Quando estiver a construir uma string de filtragem, mantenha estas regras em mente: 

* Utilize os operadores lógicos definidos pela especificação de protocolo OData para comparar uma propriedade para um valor. Tenha em atenção que não é possível comparar uma propriedade para um valor dinâmico. Lado da expressão tem de ser uma constante. 
* O nome da propriedade, um operador e um valor constante têm de ser separados por espaços com codificação URL. É um espaço com codificação URL como `%20`. 
* Todas as partes da cadeia de filtro diferenciam maiúsculas de minúsculas. 
* O valor constante tem de ser o mesmo tipo de dados como a propriedade por ordem para o filtro para devolver resultados válidos. Para obter mais informações sobre os tipos de propriedade suportados, consulte [compreender o modelo de dados do serviço tabela](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model). 

Segue-se uma consulta de exemplo que mostra como filtrar por propriedades de E-Mail e PartitionKey utilizando um OData `$filter`.

**Consulta**

```
https://<mytableapi-endpoint>/People()?$filter=PartitionKey%20eq%20'Smith'%20and%20Email%20eq%20'Ben@contoso.com'
```

Para obter mais informações sobre como construir as expressões de filtro para diversos tipos de dados, consulte [consultar tabelas e entidades](https://docs.microsoft.com/rest/api/storageservices/querying-tables-and-entities).

**Resultados**

| PartitionKey | RowKey | E-mail | PhoneNumber |
| --- | --- | --- | --- |
| Bernardo |Santos | Ben@contoso.com| 425-555-0102 |

## <a name="query-by-using-linq"></a>Consultar utilizando LINQ 
Também pode consultar utilizando LINQ, que traduz-se as expressões de consulta de OData correspondentes. Eis um exemplo de como criar consultas utilizando o SDK .NET:

```csharp
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("people");

TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
    .Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition(PartitionKey, QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition(Email, QueryComparisons.Equal,"Ben@contoso.com")
    ));

await table.ExecuteQuerySegmentedAsync<CustomerEntity>(query, null);
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, fez o seguinte:

> [!div class="checklist"]
> * Aprendeu a consulta utilizando a API de tabela (pré-visualização) 

Agora pode avançar para o próximo tutorial para saber como distribuir dados globalmente.

> [!div class="nextstepaction"]
> [Distribuir dados globalmente](tutorial-global-distribution-documentdb.md)
