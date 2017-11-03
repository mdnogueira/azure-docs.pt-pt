---
title: Procurar dados semiestruturados no armazenamento na nuvem do Azure
description: "A procurar dados de BLOBs semiestruturados utilização da Azure Search."
author: roygara
manager: timlt
ms.service: search
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: v-rogara
ms.custom: mvc
ms.openlocfilehash: ea57fa35f09299f95cdfd3c11b44657d35972295
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="search-semi-structured-data-in-cloud-storage"></a>Procurar dados semiestruturados no armazenamento na nuvem

Esta série de duas partes tutorial, irá aprender a procurar dados não estruturados e semiestruturados, utilizando a pesquisa do Azure. Este tutorial mostra como procurar dados semiestruturados, tais como JSON, armazenado em blobs do Azure. Dados semiestruturados contém etiquetas ou visuais que separam conteúdo dentro os dados. Difere da dados estruturados na medida em que não está estruturada formally, de acordo com um modelo de dados, tal como um esquema de base de dados relacional.

Nesta parte abrange como:

> [!div class="checklist"]
> * Criar e preencher um índice no interior de um serviço de pesquisa do Azure
> * Utilizar o serviço de pesquisa do Azure para pesquisar o índice

> [!NOTE]
> "Suporte de matriz JSON é uma funcionalidade de pré-visualização do Azure Search. Não está atualmente disponível no portal. Por este motivo, estamos a utilizar a API REST, que fornece esta funcionalidade e a ferramenta de cliente para chamar a API REST de pré-visualização."

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:
* Concluir o [tutorial anterior](../storage/blobs/storage-unstructured-search.md)
    * Este tutorial utiliza o serviço de pesquisa e conta de armazenamento criado no tutorial anterior
* Instalar um cliente REST e que compreende como construir um pedido de HTTP


## <a name="set-up-the-rest-client"></a>Configurar o cliente REST

Para concluir este tutorial precisa de um cliente REST. Para efeitos deste tutorial, estamos a utilizar [Postman](https://www.getpostman.com/). Pode utilizar um cliente REST diferente se já estiver familiarizado com um tipo específico.

Depois de instalar o Postman, inicie-a.

Se esta for a primeira vez, efetuar chamadas REST para o Azure, eis uma breve introdução dos componentes importantes para este tutorial: O método de pedido para cada chamada neste tutorial é "POST". As chaves de cabeçalho estão "Content-type" e "chave de api." Os valores das chaves de cabeçalho são "application/json" e a chave"admin" (a chave de administrador é um marcador de posição para a sua chave primária de pesquisa), respetivamente. O corpo é onde colocar o conteúdo da chamada real. Consoante o cliente que estiver a utilizar, poderão existir algumas variações no como pode construir a consulta, mas os são as noções básicas.

  ![Pesquisa semiestruturada](media/search-semi-structured-data/postmanoverview.png)

Para as chamadas REST abrangidas neste tutorial, é necessária a chave de api de pesquisa. Pode encontrar a chave de api em **chaves** no interior do seu serviço de pesquisa. Esta chave de api tem de ser no cabeçalho de cada chamada de API (substitua "chave de administrador" na captura de ecrã anterior com a mesma) neste tutorial direciona fazer. Manter a chave, uma vez que precisa para cada chamada.

  ![Pesquisa semiestruturada](media/search-semi-structured-data/keys.png)

## <a name="download-the-sample-data"></a>Transferir os dados de exemplo

Um conjunto de dados de exemplo foi preparado para si. **Transferir [json.zip clinical avaliações](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip)**  e deszipe-lo para a sua própria pasta.

Contidos no exemplo é ficheiros JSON, o que faziam originalmente texto exemplo ficheiros obtida [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). Iremos convertido-los para JSON para sua comodidade.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [Portal do Azure](http://portal.azure.com).

## <a name="upload-the-sample-data"></a>Carregar os dados de exemplo

No portal do Azure, navegue de volta para a conta de armazenamento criada no [tutorial anterior](../storage/blobs/storage-unstructured-search.md). Em seguida, abra o **dados** contentor e clique em **carregar**.

Clique em **avançadas**, introduza "clinical-avaliações-json" e, em seguida, carregue todos os ficheiros JSON que transferiu.

  ![Pesquisa semiestruturada](media/search-semi-structured-data/clinicalupload.png)

Após a conclusão do carregamento, os ficheiros devem ser apresentadas na sua própria subpasta no interior do contentor de dados.

## <a name="connect-your-search-service-to-your-container"></a>Ligar o serviço de pesquisa para o contentor

Estamos a utilizar Postman para três chamadas de API para o serviço de pesquisa para criar uma origem de dados, um índice e um indexador. A origem de dados inclui um ponteiro para a sua conta de armazenamento e os dados JSON. O serviço de pesquisa faz a ligação ao carregar os dados.

A cadeia de consulta tem de conter **api-version = 2016-09-01-Preview** e cada chamada deve devolver um **criado 201**. A api-version geralmente disponível ainda não tem a capacidade de processar json como um jsonArray, atualmente que só a pré-visualização api-versão.

Execute as seguintes três chamadas de API a partir do seu cliente REST.

### <a name="create-a-datasource"></a>Criar uma origem de dados

Uma origem de dados especifica quais os dados para o índice.

O ponto final desta chamada é `https://[service name].search.windows.net/datasources?api-version=2016-09-01-Preview`. Substitua `[service name]` com o nome do seu serviço de pesquisa.

Para esta chamada, terá do nome da sua conta de armazenamento e a chave de conta de armazenamento. A chave de conta de armazenamento pode ser encontrada no portal do Azure dentro da sua conta de armazenamento **chaves de acesso**. A localização é mostrada na imagem seguinte:

  ![Pesquisa semiestruturada](media/search-semi-structured-data/storagekeys.png)

Certifique-se de que substitui o `[storage account name]` e `[storage account key]` no corpo do seu chamada antes de executar a chamada.

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "data", "query" : "clinical-trials-json" }
}
```

A resposta deve ter o seguinte aspeto:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
    "@odata.etag": "\"0x8D505FBC3856C9E\"",
    "name": "clinical-trials-json",
    "description": null,
    "type": "azureblob",
    "subtype": null,
    "credentials": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
    },
    "container": {
        "name": "data",
        "query": "clinical-trials-json"
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

### <a name="create-an-index"></a>Criar um índice
    
A segunda chamada de API cria um índice. Especifica um índice de todos os parâmetros e respetivos atributos.

O URL para esta chamada é `https://[service name].search.windows.net/indexes?api-version=2016-09-01-Preview`. Substitua `[service name]` com o nome do seu serviço de pesquisa.

Primeiro, substitua o URL. Em seguida, copie e cole o seguinte código no seu corpo e execute a consulta.

```json
{
  "name": "clinical-trials-json-index",  
  "fields": [
  {"name": "FileName", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "Description", "type": "Edm.String", "searchable": true, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MinimumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Title", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "URL", "type": "Edm.String", "searchable": false, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MyURL", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "Gender", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "MaximumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Summary", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "NCTID", "type": "Edm.String", "key": true, "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "Phase", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Date", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "OverallStatus", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "OrgStudyId", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": false},
  {"name": "HealthyVolunteers", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Keywords", "type": "Collection(Edm.String)", "searchable": true, "retrievable": true, "facetable": true, "filterable": false, "sortable": false},
  {"name": "metadata_storage_last_modified", "type":"Edm.DateTimeOffset", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_storage_size", "type":"Edm.String", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_content_type", "type":"Edm.String", "searchable": true, "retrievable": true, "filterable": true, "sortable": false}
  ],
  "suggesters": [
  {
    "name": "sg",
    "searchMode": "analyzingInfixMatching",
    "sourceFields": ["Title"]
  }
  ]
}
```

A resposta deve ter o seguinte aspeto:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag": "\"0x8D505FC00EDD5FA\"",
    "name": "clinical-trials-json-index",
    "fields": [
        {
            "name": "FileName",
            "type": "Edm.String",
            "searchable": false,
            "filterable": false,
            "retrievable": true,
            "sortable": true,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": false,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        ...
          "scoringProfiles": [],
    "defaultScoringProfile": null,
    "corsOptions": null,
    "suggesters": [],
    "analyzers": [],
    "tokenizers": [],
    "tokenFilters": [],
    "charFilters": []
}
```

### <a name="create-an-indexer"></a>Criar um indexador

Um indexador liga-se a origem de dados para o índice de pesquisa de destino e, opcionalmente, fornece uma agenda para automatizar a atualização de dados.

O URL para esta chamada é `https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview`. Substitua `[service name]` com o nome do seu serviço de pesquisa.

Primeiro, substitua o URL. Em seguida, copie e cole o seguinte código no seu corpo e execute a consulta.

```json
{
  "name" : "clinical-trials-json-indexer",
  "dataSourceName" : "clinical-trials-json",
  "targetIndexName" : "clinical-trials-json-index",
  "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

A resposta deve ter o seguinte aspeto:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
    "@odata.etag": "\"0x8D505FDE143D164\"",
    "name": "clinical-trials-json-indexer",
    "description": null,
    "dataSourceName": "clinical-trials-json",
    "targetIndexName": "clinical-trials-json-index",
    "schedule": null,
    "parameters": {
        "batchSize": null,
        "maxFailedItems": null,
        "maxFailedItemsPerBatch": null,
        "base64EncodeKeys": null,
        "configuration": {
            "parsingMode": "jsonArray"
        }
    },
    "fieldMappings": [],
    "enrichers": [],
    "disabled": null
}
```

## <a name="search-your-json-files"></a>Procure os ficheiros JSON

Agora que o serviço de pesquisa foi ligado ao seu contentor de dados, pode começar a procurar os ficheiros.

Abra o portal do Azure e navegue de volta para o serviço de pesquisa. Tal como fez no tutorial anterior.

  ![Pesquisa não estruturada](media/search-semi-structured-data/indexespane.png)

### <a name="user-defined-metadata-search"></a>Pesquisa de metadados definidos pelo utilizador

Como anteriormente, os dados podem ser consultados numa de várias formas: a pesquisa em texto completo, as propriedades do sistema ou os metadados definidos pelo utilizador. Propriedades do sistema e os metadados definidos pelo utilizador só podem ser procurados com o `$select` parâmetro se foram marcados como **recuperável** durante a criação do índice de destino. Os parâmetros no índice não poderão ser alterados assim que forem criadas. No entanto, podem ser adicionados parâmetros adicionais.

Um exemplo de uma consulta básico é `$select=Gender,metadata_storage_size`, que limita o retorno para os dois parâmetros.

  ![Pesquisa semiestruturada](media/search-semi-structured-data/lastquery.png)

Um exemplo de consulta mais complexa seria `$filter=MinimumAge ge 30 and MaximumAge lt 75`, que devolve resultados apenas em que os parâmetros MinimumAge é maior que ou igual a 30 e MaximumAge é menos de 75.

  ![Pesquisa semiestruturada](media/search-semi-structured-data/metadatashort.png)

Se gostaria de experimentação e tente algumas consultas mais por si, pode fazê-lo. Sabe que pode utilizar operadores lógicos (e, em alternativa, não) e operadores de comparação (eq, ne, gt, lt, ge, le). Comparações de cadeias diferenciam maiúsculas de minúsculas.

O `$filter` parâmetro só funciona com os metadados que foram marcadas como filtrável na criação do seu índice.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre pesquisa semiestruturados dados utilizando a pesquisa do Azure, tais como:

> [!div class="checklist"]
> * Criar um serviço de pesquisa do Azure utilizando a API REST
> * Utilizar o serviço de pesquisa do Azure para procurar o contentor

Siga esta ligação para obter mais informações sobre a pesquisa.

> [!div class="nextstepaction"]
> [Indexar documentos no armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)