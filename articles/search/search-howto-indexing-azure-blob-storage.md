---
title: Indexar o Blob Storage do Azure com a pesquisa do Azure
description: Saiba como indexar o Blob Storage do Azure e extraia o texto da documentos com a Azure Search
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 2a5968f4-6768-4e16-84d0-8b995592f36a
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/22/2017
ms.author: eugenesh
ms.openlocfilehash: 97c1fc602ba27472fed2f11fd634e617ae9c636f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="indexing-documents-in-azure-blob-storage-with-azure-search"></a>Indexar documentos no Blob Storage do Azure com a pesquisa do Azure
Este artigo mostra como utilizar a pesquisa do Azure para documentos do índice (tais como PDFs, documentos do Microsoft Office e vários outros formatos comuns) armazenados no Blob storage do Azure. Em primeiro lugar, explica as noções básicas de definir e configurar um indexador de blob. Em seguida, oferece uma exploração mais aprofundada de comportamentos e cenários que é provável que encontrar.

## <a name="supported-document-formats"></a>Documento formatos suportados
O indexador de blob pode extraia o texto dos formatos de documento seguinte:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>Configurar a indexação de BLOBs
Pode configurar um indexador do Blob Storage do Azure utilizando:

* [Portal do Azure](https://ms.portal.azure.com)
* A pesquisa do Azure [REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* A pesquisa do Azure [.NET SDK](https://aka.ms/search-sdk)

> [!NOTE]
> Algumas funcionalidades (por exemplo, mapeamentos campo) ainda não estão disponíveis no portal e têm de ser utilizadas através de programação.
>
>

Aqui, iremos demonstrar o fluxo utilizando a API REST.

### <a name="step-1-create-a-data-source"></a>Passo 1: criar uma origem de dados
Uma origem de dados Especifica que dados de índice, as credenciais necessárias para aceder a dados e políticas de forma eficiente identificar as alterações nos dados (novas, modificadas ou eliminadas linhas). Uma origem de dados pode ser utilizada por vários indexadores no mesmo serviço de pesquisa.

Para a indexação de BLOBs, a origem de dados tem de ter as seguintes propriedades necessárias:

* **nome** é o nome exclusivo da origem de dados dentro do seu serviço de pesquisa.
* **tipo** tem de ser `azureblob`.
* **credenciais** fornece a cadeia de ligação da conta de armazenamento como o `credentials.connectionString` parâmetro. Consulte [como especificar credenciais](#Credentials) abaixo para obter mais detalhes.
* **contentor** Especifica um contentor na sua conta de armazenamento. Por predefinição, todos os blobs no contentor são recuperável. Se pretender apenas blobs de índice num diretório virtual específico, pode especificar o diretório a utilizar o opcional **consulta** parâmetro.

Para criar uma origem de dados:

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

Para mais informações sobre a API para criar origem de dados, consulte [criar origem de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Como especificar credenciais ####

Pode fornecer as credenciais para o contentor de blob de uma das seguintes formas:

- **Cadeia de ligação da conta de armazenamento de acesso total**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`. Pode obter a cadeia de ligação a partir do portal do Azure, aceda ao painel de conta de armazenamento > Definições > chaves (para contas de armazenamento do clássico) ou definições > chaves (para contas de armazenamento do Azure Resource Manager) de acesso.
- **Assinatura de acesso partilhado de conta de armazenamento** cadeia de ligação (SAS): `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` o SAS deve tiverem a lista e permissões em contentores e objetos de leitura (blobs neste caso).
-  **Assinatura de acesso partilhado do contentor**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` SAS a deve ter a lista e permissões de leitura no contentor.

Para obter mais informações sobre o armazenamento partilhado assinaturas de acesso, consulte [utilizar assinaturas de acesso partilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Se utilizar credenciais SAS, terá de atualizar as credenciais da origem de dados periodicamente com assinaturas renovadas para impedir a respetiva expiração. Se as credenciais SAS expirarem, o indexador irá falhar com uma mensagem de erro semelhante a `Credentials provided in the connection string are invalid or have expired.`.  

### <a name="step-2-create-an-index"></a>Passo 2: criar um índice
O índice Especifica os campos de um documento, atributos, e outras construções que formam a pesquisa de experiência.

Eis como criar um índice com um pesquisável `content` campo para armazenar o texto extraído de blobs:   

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }

Para obter mais informações sobre a criação de índices, consulte [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="step-3-create-an-indexer"></a>Passo 3: Criar um indexador
Um indexador liga uma origem de dados com um índice de pesquisa de destino e fornece uma agenda para automatizar a atualização de dados.

Depois de criar a origem de dados e índice, está pronto para criar o indexador:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Este indexador será executado a cada duas horas (intervalo de agendamento está definido como "PT2H"). Para executar um indexador cada 30 minutos, defina o intervalo para "PT30M". O mais curto intervalo suportado é 5 minutos. A agenda é opcional - se for omitido, um indexador é executado apenas quando quando for criado. No entanto, pode executar uma indexador a pedido em qualquer altura.   

Para obter mais detalhes sobre a API de indexador criar, veja [criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

## <a name="how-azure-search-indexes-blobs"></a>Como a Azure Search indexa blobs

Consoante o [configuração indexador](#PartsOfBlobToIndex), o indexador de blob pode indexar apenas os metadados de armazenamento (útil quando se preocupar apenas os metadados e não precisa do conteúdo de blobs de índice), os metadados de armazenamento e de conteúdo, ou ambos os metadados e conteúdo textual. Por predefinição, o indexador extrai metadados e o conteúdo.

> [!NOTE]
> Por predefinição, os blobs com conteúdo estruturado como JSON ou CSV são indexados como um segmento de texto único. Se pretender blobs JSON e CSV de índice de uma forma structured, consulte [blobs JSON de indexação](search-howto-index-json-blobs.md) e [CSV de indexação de blobs](search-howto-index-csv-blobs.md) funcionalidades de pré-visualização.
>
> Um documento composto ou incorporado (como um arquivo ZIP ou um documento do Word com incorporados Outlook e-mail com anexos) também é indexado como um único documento.

* O conteúdo textual do documento é extraído para um campo de cadeia denominado `content`.

> [!NOTE]
> A pesquisa do Azure limita a quantidade texto extrai consoante o escalão de preço: 32,000 carateres gratuitamente camada, 64,000 para básico e milhões de 4 para camadas Standard, Standard S2 e Standard S3. Um aviso está incluído na resposta de estado de indexador para documentos truncadas.  

* Propriedades de metadados especificado de um utilizador presentes no blob, se aplicável, são extraídas verbatim.
* Propriedades de metadados de blob padrão são extraídas para os seguintes campos:

  * **Metadados\_armazenamento\_nome** (EDM) - o nome de ficheiro do blob. Por exemplo, se tiver um /my-container/my-folder/subfolder/resume.pdf blob, o valor deste campo é `resume.pdf`.
  * **Metadados\_armazenamento\_caminho** (EDM) - o URI completo do blob, incluindo a conta de armazenamento. Por exemplo, `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **Metadados\_armazenamento\_conteúdo\_tipo** (EDM) - tipo conforme especificado pelo código da que utilizou para carregar o blob de conteúdo. Por exemplo, `application/octet-stream`.
  * **Metadados\_armazenamento\_último\_modificado** (Edm.DateTimeOffset) - última modificação timestamp do blob. A pesquisa do Azure utiliza este timestamp para identificar blobs alterados, para evitar reindexing tudo após a indexação inicial.
  * **Metadados\_armazenamento\_tamanho** (Edm.Int64) - blob de tamanho em bytes.
  * **Metadados\_armazenamento\_conteúdo\_md5** (EDM) - MD5 hash do conteúdo de blob, se disponível.
* As propriedades de metadados específicas para cada formato do documento são extraídas nos campos indicados [aqui](#ContentSpecificMetadata).

Não precisa de definir os campos para todas as propriedades acima no seu índice de pesquisa-capturar apenas as propriedades que precisa para a sua aplicação.

> [!NOTE]
> Muitas vezes, os nomes de campo no seu índice existente será diferentes dos nomes de campo gerados durante a extração do documento. Pode utilizar **campo mapeamentos** para mapear os nomes de propriedade fornecidos pelo Azure Search para os nomes de campo no seu índice de pesquisa. Irá ver um exemplo de campo mapeamentos utilizam abaixo.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>Definir chaves do documento e mapeamentos campo
Na Azure Search, a chave do documento identifica exclusivamente um documento. Todos os índices de pesquisa tem de ter exatamente um campo de chave do tipo EDM. O campo de chave é necessário para cada documento que está a ser adicionado para o índice (é, na verdade, o campo obrigatório apenas).  

Deve considere com cuidado o campo que extraídos deve mapear para o campo de chave para o seu índice. Os candidatos são:

* **Metadados\_armazenamento\_nome** - Isto pode ser um candidato conveniente, mas tenha em atenção que 1) os nomes poderão não ser exclusivos, como pode ter blobs com o mesmo nome em pastas diferentes e 2) o nome pode conter os carateres que são inválido nas chaves de documento, tais como os traços. Pode lidar com carateres inválidos, utilizando o `base64Encode` [campo a função de mapeamento](search-indexer-field-mappings.md#base64EncodeFunction) - se o fizer, não se esqueça de codificar chaves documento quando passá-los na API chama, tais como a pesquisa. (Por exemplo, no .NET, pode utilizar o [UrlTokenEncode método](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) para esse fim).
* **Metadados\_armazenamento\_caminho** - utilizar o caminho completo garante a exclusividade, mas o caminho contém sem dúvida `/` carateres que são [inválido uma chave de documento](https://docs.microsoft.com/rest/api/searchservice/naming-rules).  Conforme apresentado acima, tem a opção de codificação de chaves utilizando o `base64Encode` [função](search-indexer-field-mappings.md#base64EncodeFunction).
* Se nenhuma das opções acima resolver o problema, pode adicionar uma propriedade de metadados personalizados para os blobs. Esta opção precisa, no entanto, o processo de carregamento de BLOBs para adicionar essa propriedade de metadados para todos os blobs. Uma vez que a chave de uma propriedade necessária, todos os blobs que não tenham essa propriedade serão não ser indexados.

> [!IMPORTANT]
> Se não houver nenhum mapeamento explícito para o campo de chave no índice, da Azure Search utiliza automaticamente `metadata_storage_path` como a chave e a base-64 codifica valores de chave (a segunda opção acima).
>
>

Neste exemplo, vamos escolher o `metadata_storage_name` campo como a chave do documento. Vamos assumir também o índice tem um campo de chave com o nome `key` e um campo `fileSize` para armazenar o tamanho do documento. Para ligar coisas cópias de segurança conforme pretendido, especifique os seguintes mapeamentos de campo quando criar ou atualizar o indexador:

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

Para fazer isto em conjunto, eis como pode adicionar mapeamentos de campo e ativar a codificação base 64 de chaves de para um indexador existente:

    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
      ]
    }

> [!NOTE]
> Para saber mais sobre os mapeamentos de campo, consulte o artigo [neste artigo](search-indexer-field-mappings.md).
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>Controlar os blobs são indexados
Pode controlar os blobs são indexados e que são ignorados.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Apenas os blobs com extensões de ficheiro específicas de índice
Pode indexar apenas os blobs com as extensões de nome de ficheiro que especificar utilizando o `indexedFileNameExtensions` parâmetro de configuração do indexador. O valor é uma cadeia que contém uma lista separada por vírgulas de extensões de ficheiro (com um ponto à esquerda). Por exemplo, para índice apenas o. PDF e. DOCX blobs, efetue o seguinte:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>Excluir blobs com extensões de ficheiro específico
Pode excluir blobs com extensões de nome de ficheiro específicas de indexação utilizando o `excludedFileNameExtensions` parâmetro de configuração. O valor é uma cadeia que contém uma lista separada por vírgulas de extensões de ficheiro (com um ponto à esquerda). Por exemplo, para o índice de todos os blobs, exceto as que o. PNG e. Extensões JPEG, efetue o seguinte:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Se ambos os `indexedFileNameExtensions` e `excludedFileNameExtensions` parâmetros estão presentes, a Azure Search pela primeira vez observa `indexedFileNameExtensions`, em seguida, no `excludedFileNameExtensions`. Isto significa que se a mesma extensão de ficheiro está presente em ambos, será excluído da indexação.

### <a name="dealing-with-unsupported-content-types"></a>Lidar com os tipos de conteúdo não suportados

Por predefinição, o indexador de blob interrompe assim que o se detetar um blob com um tipo de conteúdo não suportado (por exemplo, uma imagem). Obviamente, pode utilizar o `excludedFileNameExtensions` parâmetro para ignorar a determinados tipos de conteúdo. No entanto, poderá ter de blobs de índice sem saberem a todos os tipos de conteúdo possíveis antecipadamente. Para continuar a indexação quando é encontrado um tipo de conteúdo não suportado, defina o `failOnUnsupportedContentType` parâmetro de configuração para `false`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

### <a name="ignoring-parsing-errors"></a>A ignorar erros de análise

Lógica de extração de documento de pesquisa do Azure não é perfeita e, por vezes, não conseguirá analisar documentos de um tipo de conteúdo suportado, tal como. DOCX ou. PDF. Se não pretender interromper a indexação nestes casos, defina o `maxFailedItems` e `maxFailedItemsPerBatch` parâmetros de configuração para alguns valores razoáveis. Por exemplo:

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Controlar as partes do blob são indexadas

Pode controlar as partes dos blobs são indexadas utilizando o `dataToExtract` parâmetro de configuração. Pode demorar até os seguintes valores:

* `storageMetadata`-Especifica que apenas o [propriedades blob padrão e o utilizador especificado metadados](../storage/blobs/storage-properties-metadata.md) são indexados.
* `allMetadata`-Especifica que os metadados de armazenamento e a [metadados específicos do tipo de conteúdo](#ContentSpecificMetadata) extraiu o blob do conteúdo são indexados.
* `contentAndMetadata`-Especifica que todos os metadados e o conteúdo textual extraídos de blob são indexados. Este é o valor predefinido.

Por exemplo, para apenas os metadados de armazenamento de índice, utilize:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Metadados do blob a utilizar para controlar a forma como os blobs são indexados

Os parâmetros de configuração descritos acima se aplicam a todos os blobs. Por vezes, pode querer controlar como *blobs individuais* são indexados. Pode fazê-lo ao adicionar as seguintes propriedades de metadados de blob e os valores:

| Nome da propriedade | Valor da propriedade | Explicação |
| --- | --- | --- |
| AzureSearch_Skip |"true" |Indica o indexador de BLOBs para ignorar completamente o blob. Extração nem metadados nem o conteúdo é tentada. Isto é útil quando um blob específico falha repetidamente e interrupções o processo de indexação. |
| AzureSearch_SkipContent |"true" |Isto é equivalente do `"dataToExtract" : "allMetadata"` definição descrito [acima](#PartsOfBlobToIndex) âmbito para um blob específico. |

## <a name="incremental-indexing-and-deletion-detection"></a>Deteção de indexação e eliminação incremental
Quando configurar um indexador de BLOBs para executar com base numa agenda, indexa novamente apenas alteradas blobs, conforme determinado pelo blob `LastModified` timestamp.

> [!NOTE]
> Não tem de especificar uma política de deteção de alteração – indexação incremental é automaticamente ativado para si.

Para suportar a eliminação de documentos, utilize uma abordagem de "eliminação de forma recuperável". Se eliminar os blobs de imediato, documentos correspondentes não serão removidos desde o índice de pesquisa. Em alternativa, utilize os seguintes passos:  

1. Adicionar uma propriedade de metadados personalizados para o blob para indicar para a Azure Search, que é logicamente eliminado
2. Configure uma política de deteção de eliminação de forma recuperável na origem de dados
3. Assim que o indexador foi processada blob (conforme exibido o estado de indexador API), pode eliminar o blob fisicamente

Por exemplo, a seguinte política considera um blob para ser eliminado quando tem uma propriedade de metadados `IsDeleted` com o valor `true`:

    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",     
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }   

## <a name="indexing-large-datasets"></a>A indexação de grandes conjuntos de dados

A indexação de blobs, pode ser um processo moroso. Nos casos em que tenha milhões de blobs para indexar, que pode acelerar indexar os dados de criação de partições e utilizando vários indexadores para processar os dados em paralelo. Eis como pode configurar esta opção:

- Os dados de partição para vários contentores de BLOBs ou pastas virtuais
- Configure várias origens de dados de pesquisa do Azure, um por cada contentor ou pasta. Para apontar para uma pasta de BLOBs, utilize o `query` parâmetro:

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Crie um indexador correspondente para cada origem de dados. Todos os indexadores podem apontar para o mesmo índice de pesquisa de destino.  

- Uma unidade de pesquisa no seu serviço pode executar um indexador em qualquer momento. Criar vários indexadores conforme descrito acima só é útil se efetivamente executam em paralelo. Para executar vários indexadores em paralelo, ampliar o serviço de pesquisa através da criação de um número adequado de partições e réplicas. Por exemplo, se o serviço de pesquisa tem 6 unidades de pesquisa (por exemplo, as réplicas 2 partições x 3), em seguida, 6 indexadores podem executar em simultâneo, resultando num six-fold aumento do débito de indexação. Para obter mais informações sobre dimensionamento e planeamento de capacidade, consulte [Dimensionar níveis de recursos de consulta e indexação cargas de trabalho na Azure Search](search-capacity-planning.md).

## <a name="indexing-documents-along-with-related-data"></a>Indexar documentos juntamente com dados relacionados

Pode querer "monte" documentos de várias origens no seu índice. Por exemplo, poderá pretender uniu o texto de blobs com outros metadados armazenados na base de dados do Cosmos. Mesmo pode utilizar o push indexação API juntamente com vários indexadores para criar cópias de segurança documentos sobre pesquisa de várias partes. 

Para isto funcionar, todos os indexadores e outros componentes a concordar com a chave de documento. Para instruções detalhadas, consulte este artigo externo: [combinar documentos com outros dados na Azure Search ](http://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Indexação em texto simples 

Se todos os seus blobs contêm texto simples no mesmo codificação, pode significativamente melhorar o desempenho de indexação utilizando **modo de análise de texto**. Para utilizar o modo de análise de texto, defina o `parsingMode` propriedade de configuração para `text`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

Por predefinição, o `UTF-8` codificação pressupõe-se. Para especificar uma codificação diferente, utilize o `encoding` propriedade de configuração: 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Propriedades de metadados específicos do tipo de conteúdo
A tabela seguinte resume processamento feito para cada formato do documento e descreve as propriedades de metadados extraídas pela pesquisa do Azure.

| Formato do documento / tipo de conteúdo | Propriedades de metadados específicos do tipo de conteúdo | Detalhes de processamento |
| --- | --- | --- |
| HTML (`text/html`) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Da faixa markup HTML e extraia o texto |
| PDF (`application/pdf`) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Extraia o texto, incluindo documentos incorporados (excluindo imagens) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extraia o texto, incluindo documentos incorporados |
| DOCUMENTO (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extraia o texto, incluindo documentos incorporados |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extraia o texto, incluindo documentos incorporados |
| XLS (aplicação/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extraia o texto, incluindo documentos incorporados |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extraia o texto, incluindo documentos incorporados |
| PPT (vnd.ms/aplicação-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extraia o texto, incluindo documentos incorporados |
| Tarifas de mensagens (vnd.ms/aplicação-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_message_bcc`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Extraia o texto, incluindo os anexos |
| ZIP (aplicação/zip) |`metadata_content_type` |Extraia o texto a partir de todos os documentos no arquivo |
| XML (application/xml) |`metadata_content_type`</br>`metadata_content_encoding`</br> |Texto de markup e extrair do XML de faixa |
| JSON (application/json) |`metadata_content_type`</br>`metadata_content_encoding` |Extraia o texto<br/>Nota: Se precisar de extrair vários campos de documentos de um blob JSON, consulte o artigo [blobs JSON de indexação](search-howto-index-json-blobs.md) para obter detalhes |
| EML (mensagem/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Extraia o texto, incluindo os anexos |
| RTF (aplicação/rtf) |`metadata_content_type`</br>`metadata_author`</br>`metadata_character_count`</br>`metadata_creation_date`</br>`metadata_page_count`</br>`metadata_word_count`</br> | Extraia o texto|
| Texto simples (texto/plain) |`metadata_content_type`</br>`metadata_content_encoding`</br> | Extraia o texto|


## <a name="help-us-make-azure-search-better"></a>Ajude-na tornar o melhor da Azure Search
Se tiver de pedidos de funcionalidades ou ideias para melhoramentos, indique no nosso [UserVoice site](https://feedback.azure.com/forums/263029-azure-search/).
