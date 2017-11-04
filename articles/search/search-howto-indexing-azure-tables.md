---
title: Indexar o Table storage do Azure com a Azure Search | Microsoft Docs
description: Saiba como indexar os dados armazenados no armazenamento de tabelas do Azure com a pesquisa do Azure
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 1cc27411-d0cc-40ed-8aed-c7cb9ab402b9
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/10/2017
ms.author: eugenesh
ms.openlocfilehash: b167f69f853f6ecdfd56179e6ffb946cdf2f45b8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="index-azure-table-storage-with-azure-search"></a>Índice Table storage do Azure com a pesquisa do Azure
Este artigo mostra como utilizar a pesquisa do Azure para dados do índice armazenados no Table storage do Azure.

## <a name="set-up-azure-table-storage-indexing"></a>Configurar a indexação de armazenamento de tabelas do Azure

Pode configurar um indexador de armazenamento de tabelas do Azure utilizando estes recursos:

* [Portal do Azure](https://ms.portal.azure.com)
* A pesquisa do Azure [REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* A pesquisa do Azure [.NET SDK](https://aka.ms/search-sdk)

Aqui iremos demonstrar o fluxo, utilizando a API REST. 

### <a name="step-1-create-a-datasource"></a>Passo 1: Criar uma origem de dados

Uma origem de dados Especifica que dados de índice, as credenciais necessárias para aceder os dados e as políticas que ative a pesquisa do Azure identificar eficientemente as alterações nos dados.

Para indexação de tabela, a origem de dados tem de ter as seguintes propriedades:

- **nome** é o nome exclusivo da origem de dados dentro do seu serviço de pesquisa.
- **tipo** tem de ser `azuretable`.
- **credenciais** parâmetro contém a cadeia de ligação da conta de armazenamento. Consulte o [especificar credenciais](#Credentials) secção para obter detalhes.
- **contentor** define o nome da tabela e uma consulta opcional.
    - Especifique o nome da tabela, utilizando o `name` parâmetro.
    - Opcionalmente, especifique uma consulta ao utilizar o `query` parâmetro. 

> [!IMPORTANT] 
> Sempre que possível, utilize um filtro em PartitionKey para um melhor desempenho. Qualquer outra consulta efetua uma análise completa de tabela, resultando num fraco desempenho para tabelas grandes. Consulte o [considerações de desempenho](#Performance) secção.


Para criar uma origem de dados:

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Para obter mais informações sobre a API para criar origem de dados, consulte [criar origem de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>Formas para especificar credenciais ####

Pode fornecer as credenciais para a tabela de uma das seguintes formas: 

- **Cadeia de ligação da conta de armazenamento de acesso total**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` pode obter a cadeia de ligação a partir do portal do Azure acedendo ao **painel de conta de armazenamento** > **definições** > **chaves** (para contas de armazenamento clássico) ou **definições** > **chaves de acesso** (para contas de armazenamento do Azure Resource Manager).
- **Conta de armazenamento partilhado a cadeia de ligação de assinatura de acesso**: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` a assinatura de acesso partilhado deve ter a lista e permissões de leitura no contentores (tabelas neste caso) e objetos (linhas da tabela).
-  **Assinatura de acesso partilhado de tabela**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` a assinatura de acesso partilhado deve ter permissões de consulta (leitura) na tabela.

Para obter mais informações sobre o armazenamento partilhado assinaturas de acesso, consulte [utilizar assinaturas de acesso partilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Se utilizar credenciais de assinatura de acesso partilhado, terá de atualizar as credenciais de origem de dados periodicamente com assinaturas renovadas para impedir a respetiva expiração. Se as credenciais de assinatura de acesso partilhado expirarem, o indexador falha com uma mensagem de erro semelhante a "As credenciais fornecidas na cadeia de ligação são inválidas ou tem expirado."  

### <a name="step-2-create-an-index"></a>Passo 2: criar um índice
O índice Especifica os campos de um documento, os atributos e outras construções que formam a pesquisa de experiência.

Para criar um índice:

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }

Para obter mais informações sobre a criação de índices, consulte [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-3-create-an-indexer"></a>Passo 3: Criar um indexador
Um indexador liga uma origem de dados com um índice de pesquisa de destino e fornece uma agenda para automatizar a atualização de dados. 

Depois do índice e a origem de dados são criados, está pronto para criar o indexador:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Este indexador for executado a cada duas horas. (O intervalo de agendamento está definido como "PT2H".) Para executar um indexador cada 30 minutos, defina o intervalo para "PT30M". O mais curto intervalo suportado é de cinco minutos. A agenda é opcional; Se for omitido, um indexador é executado apenas quando quando for criado. No entanto, pode executar um indexador a pedido em qualquer altura.   

Para obter mais informações sobre a API de indexador criar, consulte [criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

## <a name="deal-with-different-field-names"></a>Lidar com os nomes de campos diferentes
Por vezes, os nomes de campo no seu índice existente são diferentes dos nomes de propriedade na tabela. Pode utilizar os mapeamentos de campo para mapear os nomes das propriedades da tabela para os nomes de campo no seu índice de pesquisa. Para saber mais sobre os mapeamentos de campo, consulte o artigo [mapeamentos de campo de indexador de Azure Search ponte as diferenças entre os índices de pesquisa e origens de dados](search-indexer-field-mappings.md).

## <a name="handle-document-keys"></a>Processar as chaves do documento
Na Azure Search, a chave do documento identifica exclusivamente um documento. Todos os índices de pesquisa tem de ter exatamente um campo de chave do tipo `Edm.String`. O campo de chave é necessário para cada documento que está a ser adicionado ao índice. (Na realidade, é o único campo obrigatório.)

Porque as linhas de tabela tem uma chave composta, pesquisa do Azure gera um sintético campo chamado `Key` que é uma concatenação dos valores de chave linha e chave de partição. Por exemplo, se uma linha 's PartitionKey é `PK1` e RowKey `RK1`, em seguida, a `Key` o valor do campo é `PK1RK1`.

> [!NOTE]
> O `Key` valor pode conter carateres inválidos nas chaves de documento, tais como os traços. Pode lidar com carateres inválidos, utilizando o `base64Encode` [campo a função de mapeamento](search-indexer-field-mappings.md#base64EncodeFunction). Se o fizer, lembre-se também utilizar o URL segura Base64 codificação quando a transmissão de chaves do documento na API chama, tais como a pesquisa.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Deteção de indexação e eliminação incremental
Quando configurar um indexador de tabela para executar com base numa agenda,-reindexes linhas apenas novas ou atualizadas, conforme determinado por uma linha `Timestamp` valor. Não tem de especificar uma política de deteção de alteração. A indexação incremental é automaticamente ativado para si.

Para indicar que certos documentos tem de ser removidos do índice, pode utilizar uma estratégia de eliminação de forma recuperável. Em vez de eliminar uma linha, adicione uma propriedade para indicar que tem eliminado e configurar uma política de deteção de eliminação de forma recuperável na datasource. Por exemplo, a seguinte política considera que uma linha é eliminada se a linha tem uma propriedade `IsDeleted` com o valor `"true"`:

    PUT https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "<query>" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   

<a name="Performance"></a>
## <a name="performance-considerations"></a>Considerações de desempenho

Por predefinição, a pesquisa do Azure utiliza o seguinte filtro de consulta: `Timestamp >= HighWaterMarkValue`. Porque as tabelas do Azure não tem um índice secundário o `Timestamp` campo, este tipo de consulta requer uma análise completa de tabela e, por conseguinte, é lento para tabelas grandes.


Seguem-se duas abordagens possíveis para melhorar o desempenho de indexação de tabela. Ambos estes abordagens dependem da utilização de partições de tabela: 

- Se os dados naturalmente podem ser particionados em vários intervalos de partição, crie uma origem de dados e um indexador correspondente para cada intervalo de partição. Cada indexador agora tem de processar apenas um partição específica intervalo, resultando num melhor desempenho das consultas. Se os dados que tem de ser indexados têm um pequeno número de partições fixos, mesmo melhores: cada indexador apenas uma análise de partição. Por exemplo, para criar uma origem de dados para processamento de um intervalo de partição com as chaves de `000` para `100`, utilizar uma consulta como esta: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Se os dados particionados tempo (por exemplo, criar uma nova partição cada dia ou de uma semana), considere a seguinte abordagem: 
    - Utilizar uma consulta com o formato: `(PartitionKey ge <TimeStamp>) and (other filters)`. 
    - Monitorizar o progresso de indexador utilizando [obter API de estado de indexador](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)e atualizar regularmente o `<TimeStamp>` condição da consulta com base no valor de nível máximo mais recente com êxito. 
    - Com esta abordagem, se necessário acionar um reindexing concluída, terá de repor a consulta de origem de dados, além de repor o indexador. 


## <a name="help-us-make-azure-search-better"></a>Ajude-na tornar o melhor da Azure Search
Se tiver de pedidos de funcionalidades ou ideias para melhoramentos, submetê-las no nosso [UserVoice site](https://feedback.azure.com/forums/263029-azure-search/).
