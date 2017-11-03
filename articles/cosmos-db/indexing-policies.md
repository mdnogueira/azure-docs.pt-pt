---
title: "BD do Azure do Cosmos indexação políticas | Microsoft Docs"
description: "Compreenda como indexação funciona do BD Azure Cosmos. Saiba como configurar e alterar a política de indexação para indexação automática e melhor desempenho."
keywords: "como a indexação funciona, automática indexação, a indexação de base de dados"
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: d5e8f338-605d-4dff-8a61-7505d5fc46d7
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 08/17/2017
ms.author: arramac
ms.openlocfilehash: 30a21645831f0cfcb3b52c797dbddfa6b5283960
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-does-azure-cosmos-db-index-data"></a>Como funciona a dados do índice de BD do Cosmos do Azure?

Por predefinição, todos os dados de base de dados do Azure Cosmos está indexada. E embora muitos clientes estiverem satisfeitos permitir que a base de dados do Cosmos do Azure processam automaticamente todos os aspetos da indexação, Azure Cosmos DB também suporta a especificação personalizadas **política de indexação** para coleções durante a criação. As políticas de indexação do BD Azure Cosmos são mais flexível e de índices secundários disponibilizados em outras plataformas de base de dados, porque permitem-lhe criar e personalizar a forma do índice sem sacrificar a flexibilidade de esquema. Para saber como indexação funciona do BD Azure Cosmos, tem de compreender ao gerir a política de indexação, pode efetuar fala detalhada entre tolerância de armazenamento de índice, escrita e débito de consulta e consistência de consulta.  

Neste artigo, vamos observe fechar Azure Cosmos DB indexação políticas, como pode personalizar a política de indexação e os compromissos associados. 

Depois de ler este artigo, poderá responder às seguintes questões:

* Como pode a substituir as propriedades para incluir ou excluir da indexação?
* Como posso configurar o índice para atualizações de uma eventual?
* Como configurar a indexação para executar consultas de Order By ou um intervalo?
* Como efetuar alterações à política de indexação de uma coleção?
* Como comparar armazenamento e o desempenho da indexação de políticas diferentes?

## <a id="CustomizingIndexingPolicy"></a>Personalizar a política de indexação de uma coleção
Os programadores podem personalizar os compromissos entre o armazenamento, o desempenho de escrita/consulta e consistência de consulta, ao substituir a política de indexação predefinida de uma coleção de base de dados do Azure Cosmos e configurar os seguintes aspetos.

* **Incluindo/excluindo documentos e caminhos deíndice/**. Os programadores podem escolher certos documentos excluídas ou incluídas no índice no momento da inserir ou substitui-los para a coleção. Os programadores também podem optar por incluir ou excluir a.k.a. determinadas propriedades JSON caminhos (incluindo padrões de carateres universais) ser indexados em documentos que estão incluídos num índice.
* **Configurar vários tipos de índice**. Para cada um dos caminhos de incluído, os programadores também podem especificar o tipo de índice que necessitam, ao longo de uma coleção com base nos seus dados e esperado carga de trabalho de consulta e a numérica/cadeia "precisão" para cada caminho.
* **Configurar os modos de atualização do índice**. BD do Azure do Cosmos suporta três modos de indexação que podem ser configurados através da política de indexação de uma coleção de base de dados do Azure Cosmos: consistente, Lazy e nenhum. 

O seguinte fragmento de código do .NET mostra como definir uma política de indexação personalizada durante a criação de uma coleção. Aqui iremos definir a política com o índice de intervalos de cadeias e números de cada a precisão máxima. Esta política permite-na executar consultas de Order By em relação a cadeias.

    DocumentCollection collection = new DocumentCollection { Id = "myCollection" };

    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);   


> [!NOTE]
> O esquema JSON para a política de indexação foi alterado com a versão de API de REST versão 2015-06-03 para suportar os índices de intervalo em relação a cadeias. .NET SDK 1.2.0 e Java, Python e Node.js SDKs 1.1.0 suportam o novo esquema de política. SDKs anteriores utilizam a API de REST versão 2015-04-08 e suportam o esquema mais antigo da política de indexação.
> 
> Por predefinição, o Azure Cosmos DB indexa todas as propriedades de cadeia dentro de documentos de forma consistente com um índice de Hash e propriedades de um valor numérico com um índice de intervalo.  
> 
> 

### <a name="customizing-the-indexing-policy-using-the-portal"></a>Personalizar a política de indexação a utilizar o portal

Pode alterar a política de indexação de uma coleção no portal do Azure. Abra a conta de base de dados do Azure Cosmos no portal do Azure, selecione a coleção, no menu navegação à esquerda, clique em **definições**e, em seguida, clique em **política de indexação**. No **política de indexação** painel, altere a sua política de indexação e, em seguida, clique em **OK** para guardar as alterações. 

### <a id="indexing-modes"></a>Modos de indexação de base de dados
BD do Azure do Cosmos suporta três indexação modos que podem ser configurados através da política de indexação de uma coleção de BD do Cosmos Azure – Consistent, Lazy e nenhum.

**Consistente**: se a política de uma coleção de base de dados do Azure Cosmos é designada como "consistente", as consultas de uma dada coleção da base de dados do Azure Cosmos siga o mesmo nível de consistência especificados para as ponto de leituras (ou seja, forte, consistência vinculada, sessão ou eventual). O índice é atualizado de forma síncrona como parte da atualização de documento (ou seja, inserir, substituir, atualizar e eliminar um documento numa coleção de base de dados do Azure Cosmos).  A indexação consistente suporta consultas consistentes ao custo de redução possíveis débito de escrita. Este redução é uma função caminhos exclusivo que precisam de ser indexados e "nível de consistência". Modo de indexação consistente foi concebido para cargas de trabalho "escrever rapidamente, consulta imediatamente".

**Em diferido**: para permitir que o débito de ingestão de documento máximo, uma coleção de BD do Cosmos Azure pode ser configurada com consistência lento; consultas significado são eventualmente consistentes. O índice é atualizado de forma assíncrona quando uma coleção de base de dados do Azure Cosmos quiescent ou seja, quando a capacidade de débito da coleção não é totalmente utilizada para servir pedidos de utilizador. Para "de inserção agora, consulta mais tarde" cargas de trabalho que requerem a ingestão de documento unhindered, "lento" modo de indexação possam ser adequado.

**Nenhum**: uma coleção marcada com o modo de índice de "None" não tem nenhum índice associado. Isto é normalmente utilizado se a base de dados do Azure Cosmos é utilizada como um armazenamento de chave-valor e os documentos são acedidos apenas pela respetiva propriedade ID. 

> [!NOTE]
> Configurar a política de indexação com "None" tem o efeito de remover um índice existente. Utilize esta opção se os seus padrões de acesso são apenas requerem "id" e/ou "ligação personalizada".
> 
> 

A tabela seguinte mostra a consistência do consultas baseadas em modo indexação (Consistent e Lazy) configurada para a coleção e o nível de consistência especificado para o pedido de consulta. Isto aplica-se a consultas efetuadas com os SDKs de interface - REST API, ou a partir do e procedimentos armazenados acionadores. 

|Consistência|Modo de indexação: consistente|Modo de indexação: lento|
|---|---|---|
|Forte|Forte|Eventual|
|Estagnação Limitada|Estagnação Limitada|Eventual|
|Sessão|Sessão|Eventual|
|Eventual|Eventual|Eventual|

BD do Azure do Cosmos devolve um erro de consultas efetuadas em coleções com nenhum modo de indexação. Ainda pode executar consultas como análises através de explícita `x-ms-documentdb-enable-scan` cabeçalho na REST API ou o `EnableScanInQuery` opção utilizando o SDK .NET do pedido. Algumas funcionalidades de consulta como ORDER BY não são suportadas como análises com `EnableScanInQuery`.

A tabela seguinte mostra a consistência do consultas baseadas no modo de indexação (Consistent, Lazy e nenhum) quando EnableScanInQuery for especificado.

|Consistência|Modo de indexação: consistente|Modo de indexação: lento|Modo de indexação: nenhuma|
|---|---|---|---|
|Forte|Forte|Eventual|Forte|
|Estagnação Limitada|Estagnação Limitada|Eventual|Estagnação Limitada|
|Sessão|Sessão|Eventual|Sessão|
|Eventual|Eventual|Eventual|Eventual|

A mostrar de exemplo de código seguinte como criar uma coleção de BD do Cosmos do Azure com o SDK .NET de indexação consistente em todos os inserções de documento.

     // Default collection creates a hash index for all string fields and a range index for all numeric    
     // fields. Hash indexes are compact and offer efficient performance for equality queries.

     var collection = new DocumentCollection { Id ="defaultCollection" };

     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

     collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("mydb"), collection);


### <a name="index-paths"></a>Caminhos de índice
BD do Azure do Cosmos modelos documentos JSON e o índice como árvores e permite-lhe otimizar políticas para caminhos de árvore. Dentro de documentos, pode escolher quais caminhos tem de ser incluídos ou excluídos da indexação. Isto pode oferecer desempenho melhorado de escrita e de armazenamento de índice inferior para cenários de quando os padrões de consulta são conhecidos previamente.

Caminhos de índice começar a utilizar a raiz (/) e, normalmente, terminar com o? operador de caráter universal, que indica que existem vários valores possíveis para o prefixo. Por exemplo, para servir SELECIONAR * de famílias F onde F.familyName = ou "seja", tem de incluir um caminho de índice para /familyName/? na política de índice da coleção.

Caminhos de índice também podem utilizar o * operador de caráter universal para especificar o comportamento de caminhos recursivamente o prefixo. Por exemplo, payload / * pode ser utilizado para excluir tudo sob a propriedade de payload da indexação.

Seguem-se os padrões comuns para especificar os caminhos de índice:

| Caminho                | Caso de utilização/descrição                                                                                                                                                                                                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| /                   | Caminho predefinido para a coleção. Recursiva e aplica-se a árvore de documentos todo.                                                                                                                                                                                                                                   |
| prop /?             | Caminho de índice necessário para efetuar consultas semelhante ao seguinte (com Hash ou um intervalo de tipos de respetivamente):<br><br>SELECIONE da coleção c WHERE c.prop = "valor"<br><br>SELECIONE da coleção c WHERE c.prop > 5<br><br>SELECIONE a partir da coleção c ORDER BY c.prop                                                                       |
| / prop / *             | Caminho de índice para todos os caminhos sob a etiqueta especificada. Funciona com as seguintes consultas<br><br>SELECIONE da coleção c WHERE c.prop = "valor"<br><br>SELECIONE da coleção c WHERE c.prop.subprop > 5<br><br>SELECIONE da coleção c WHERE c.prop.subprop.nextprop = "valor"<br><br>SELECIONE a partir da coleção c ORDER BY c.prop         |
| [] propriedades / /?         | Caminho de índice necessários para servir iteração e associar as consultas em relação a matrizes de escalares comparáveis como ["a", "b", "c"]:<br><br>SELECIONE tag da tag IN collection.props tag onde = "valor"<br><br>SELECIONE tag da coleção c associação tag IN c.props onde tag > 5                                                                         |
| /Props/ [] /subprop/? | Caminho de índice necessários para efetuar iteração e consultas de associação em relação a matrizes de objetos como [{subprop: "a"}, {subprop: "b"}]:<br><br>SELECIONE tag da tag IN collection.props onde tag.subprop = "valor"<br><br>SELECIONE tag da coleção c associação tag IN c.props onde tag.subprop = "valor"                                  |
| / prop/subprop /?     | Caminho de índice necessário para efetuar consultas (com Hash ou um intervalo de tipos de respetivamente):<br><br>SELECIONE da coleção c WHERE c.prop.subprop = "valor"<br><br>SELECIONE da coleção c WHERE c.prop.subprop > 5                                                                                                                    |

> [!NOTE]
> Ao definir caminhos de índice personalizado, é necessário especificar a regra de indexação predefinida para a árvore de documentos todo o caminho especial a designação "/ *". 
> 
> 

O exemplo a seguir configura um caminho específico com o intervalo de indexação e um valor de precisão personalizada de 20 bytes:

    var collection = new DocumentCollection { Id = "rangeSinglePathCollection" };    

    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = 20 } } 
            });

    // Default for everything else
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*" ,
            Indexes = new Collection<Index> {
                new HashIndex(DataType.String) { Precision = 3 }, 
                new RangeIndex(DataType.Number) { Precision = -1 } 
            }
        });

    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), pathRange);


### <a name="index-data-types-kinds-and-precisions"></a>Tipos de dados do índice, tipos e precisions
Agora que iremos tiver decorrido Consulte como especificar caminhos, vamos ver as opções que pode utilizar para configurar a política de indexação de um caminho. Pode especificar uma ou mais definições de indexação para cada caminho:

* Tipo de dados: **cadeia**, **número**, **ponto**, **polígono**, ou **LineString** (pode conter apenas um introdução por tipo de dados por caminho)
* Tipo de índice: **Hash** (consultas de igualdade), **intervalo** (igualdade, intervalo ou consultas de Order By), ou **Spatial** (consultas geográficos) 
* Precisão: Para o índice de hash varia entre 1 a 8 para cadeias e números com predefinida como 3. Para o índice de intervalo este valor pode ser -1 (precisão máxima) e variam entre 1-100 (precisão máxima) para a cadeia ou valores numéricos.

#### <a name="index-kind"></a>Tipo de índice
BD do Azure do Cosmos suporta tipos de índice de Hash e o intervalo para cada caminho (que podem configuradas para cadeias, números ou ambos).

* **Hash** suporta igualdade eficiente e consultas de associação. A maioria dos casos de utilização, índices de hash não é necessário uma precisão maior que o valor predefinido de 3 bytes. Tipo de dados pode ser a cadeia ou um número.
* **Intervalo** suporta consultas de igualdade eficiente, consultas de intervalo (utilizando >, <>, =, < =,! =) e consultas de Order By. Consultas de Order By por predefinição também necessitam de precisão de índice máximo (-1). Tipo de dados pode ser a cadeia ou um número.

BD do Azure do Cosmos também suporta o tipo de índice espacial para cada caminho, que pode ser especificado para os tipos de dados ponto, polígono ou LineString. O valor no caminho especificado tem de ser um fragmento de GeoJSON válido como `{"type": "Point", "coordinates": [0.0, 10.0]}`.

* **Geográficos** suporta eficiente geográficos (dentro e distância) consultas. Tipo de dados pode ser ponto, Polygon ou LineString.

> [!NOTE]
> BD do Cosmos do Azure suporta a indexação automática de pontos, Multilinestrings e Multipoints.
> 
> 

Eis os tipos de índice suportados e exemplos de consultas que podem ser utilizados para servir:

| Tipo de índice | Caso de utilização/descrição                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Hash       | Hash através de/prop /? (ou /) pode ser utilizado para efetuar as seguintes consultas de forma eficiente:<br><br>SELECIONE da coleção c WHERE c.prop = "valor"<br><br>Hash através de propriedades / [] /? (ou / ou propriedades /) pode ser utilizado para efetuar as seguintes consultas de forma eficiente:<br><br>SELECIONE tag da coleção c associação tag IN c.props tag onde = 5                                                                                                                       |
| intervalo      | Intervalo de ativação pós-falha/prop /? (ou /) pode ser utilizado para efetuar as seguintes consultas de forma eficiente:<br><br>SELECIONE da coleção c WHERE c.prop = "valor"<br><br>SELECIONE da coleção c WHERE c.prop > 5<br><br>SELECIONE a partir da coleção c ORDER BY c.prop                                                                                                                                                                                                              |
| Espacial     | Intervalo de ativação pós-falha/prop /? (ou /) pode ser utilizado para efetuar as seguintes consultas de forma eficiente:<br><br>SELECIONE da coleção c<br><br>ONDE ST_DISTANCE (c.prop, {"type": "Ponto", "coordenadas": [0.0, 10.0]}) < 40<br><br>SELECIONE de coleção c onde ST_WITHIN(c.prop, {"type": "Polygon",...}) – com a indexação pontos ativada<br><br>SELECIONE da coleção c onde ST_WITHIN({"type": "Point",...}, c.prop) – com a indexação polígonos ativados              |

Por predefinição, é devolvido um erro de consultas com operadores de intervalo como > = Se não houver nenhum índice de intervalo (de qualquer precisão) para assinalar que uma análise poderá ser necessária servir a consulta. Podem executar consultas de intervalo sem um índice de intervalo utilizando o cabeçalho x-ms-documentdb-enable-análise da API REST ou a opção de pedido de EnableScanInQuery utilizando o SDK .NET. Se existirem quaisquer outros filtros na consulta que BD do Cosmos Azure pode utilizar o índice para filtrar contra, em seguida, não será devolvido nenhum erro.

As mesmas regras aplicam-se para consultas geográficos. Por predefinição, é devolvido um erro de consultas geográficos se existir nenhum índice geográfico, e existem não existem outros filtros que podem ser servidos do índice. Podem ser realizados como uma análise utilizando o x-ms-documentdb-enable-análise/EnableScanInQuery.

#### <a name="index-precision"></a>Índice de precisão
Precisão índice permite-lhe o compromisso entre sobrecarga armazenamento de índice e o desempenho das consultas. Para números, recomendamos que utilize a configuração de precisão predefinida de -1 ("máximo"). Uma vez que os números são 8 bytes no JSON, isto é equivalente a uma configuração de 8 bytes. Escolha um valor inferior para precisão, por exemplo, 1 a 7, significa que os valores dentro de alguns intervalos de mapeiam para a entrada de índice mesmo. Unidades de pedido, por conseguinte, irá reduzir o espaço de armazenamento de índice, mas a execução da consulta poderá ter de processar mais documentos e, consequentemente, ou seja, a consumir mais de débito.

Configuração de precisão de índice possui uma aplicação mais prática com intervalos de cadeia. Uma vez que as cadeias podem ter qualquer comprimento arbitrário, a escolha de precisão o índice pode afetar o desempenho das consultas de intervalo de cadeia e afetar a quantidade de espaço de armazenamento de índice necessário. Os índices de intervalo de cadeia podem ser configurados com 1-100 ou -1 ("máximo"). Se gostaria de executar consultas de Order By em relação a propriedades de cadeia, tem de especificar uma precisão de -1 para os caminhos correspondentes.

Os índices espaciais sempre utilizam a precisão do índice predefinido para todos os tipos (pontos, Multipoints e polígonos) e não podem ser substituído. 

O exemplo seguinte mostra como aumentar a precisão para índices de intervalo de uma coleção utilizando o SDK .NET. 

**Criar uma coleção com uma precisão de índice personalizado**

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };

    // Override the default policy for Strings to range indexing and "max" (-1) precision
    rangeDefault.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), rangeDefault);   


> [!NOTE]
> BD do Azure do Cosmos devolve um erro quando uma consulta utiliza Order By, mas não tem um índice de intervalo contra o caminho consultado com a precisão máxima. 
> 
> 

Da mesma forma, podem ser completamente excluídos caminhos de indexação. O exemplo seguinte mostra como excluir uma secção completa dos documentos (a.k.a. uma árvore secundárias) de indexação a utilizar o "*" caráter universal.

    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*" });

    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);



## <a name="opting-in-and-opting-out-of-indexing"></a>Aceitar e aceitar fora de indexação
Pode escolher se pretende que a coleção para indexar automaticamente todos os documentos. Por predefinição, todos os documentos são automaticamente indexados, mas pode optar por desativá-la. Quando a indexação está desativada, documentos podem ser acedidos através de apenas os respetivos auto-ligações ou por consultas com o ID.

Com automática indexação desativada, pode adicionar ainda seletivamente apenas os documentos específicos para o índice. Por outro lado, pode deixar automática de indexação no e escolha seletivamente a excluir apenas a documentos específica. A indexação/desative as configurações são úteis quando tem apenas um subconjunto de documentos que precisam de ser consultado.

Por exemplo, o exemplo seguinte mostra como incluir um documento explicitamente com o [SDK do .NET DocumentDB API](https://docs.microsoft.com/en-us/azure/cosmos-db/documentdb-sdk-dotnet) e [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx) propriedade.

    // If you want to override the default collection behavior to either
    // exclude (or include) a Document from indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## <a name="modifying-the-indexing-policy-of-a-collection"></a>Modificar a política de indexação de uma coleção
BD do Cosmos do Azure permite-lhe efetuar alterações à política de indexação de uma coleção no momento. Uma alteração na política de uma coleção de base de dados do Azure Cosmos de indexação pode levar a uma alteração na forma do índice, incluindo os caminhos que podem ser indexados, os respetivos precisão, bem como o modelo de consistência do índice em si. Deste modo, uma alteração na política de indexação, efetivamente requer uma transformação do índice antigo para um novo. 

**Transformações de índice online**

![Como a indexação funciona – transformações de índice online de base de dados do Azure Cosmos](./media/indexing-policies/index-transformations.png)

Transformações de índice ficam online, o que significa que os documentos indexados pela política antiga eficientemente são transformados pela nova política **sem afetar a disponibilidade de escrita ou o débito aprovisionado** da coleção. A consistência de leitura e escrita operações efetuadas utilizando a API REST, SDKs ou a partir do acionadores e procedimentos armazenados não é afetado durante a transformação do índice. Isto significa que não existe nenhum degradação do desempenho ou o período de indisponibilidade às suas aplicações quando efetuar uma política de indexação alterar.

No entanto, durante o tempo de transformação de índice progresso, as consultas são eventualmente consistentes, independentemente da configuração do modo de indexação (Consistent ou Lazy). Isto também se aplica a consultas de todas as interfaces – REST API, SDKs e a partir do e procedimentos armazenados acionadores. Tal como com Lazy indexação, a transformação do índice é executada no modo assíncrono em segundo plano em réplicas com os recursos dos componentes de reserva disponíveis para uma réplica especificada. 

Transformações de índice também são efetuadas **no situ** (no local), ou seja, o Azure Cosmos DB não manter duas cópias do índice e trocar o índice antigo enviados pelo novo. Isto significa que não existe espaço em disco adicional necessário ou consumido na suas coleções ao efetuar transformações do índice.

Quando alterar a política de indexação, como as alterações são aplicadas para mover desde o índice antigo para o novo um dependem principalmente as configurações de modo de indexação mais assim que os outros valores como incluído/excluído caminhos, tipos de índice e precisions. Se ambas as políticas de antigas e novas utilizam indexação consistente, base de dados do Azure Cosmos efetua uma transformação de índice online. Não é possível aplicar outra alteração de política de indexação com o modo de indexação consistente enquanto a transformação está em curso.

No entanto, pode mover Lazy ou nenhum modo de indexação a uma transformação está em curso. 

* Quando mover para Lazy, a alteração de política do índice é efetuada em vigor imediatamente e base de dados do Azure Cosmos começa a recriar o índice de forma assíncrona. 
* Quando move como None, em seguida, o índice foi removido em vigor imediatamente. Mover para None é útil quando pretender cancelar um em curso transformação e inicie novamente com uma outra política de indexação. 

Se estiver a utilizar o SDK .NET, pode iniciar uma alteração de política de indexação a utilizar o novo **ReplaceDocumentCollectionAsync** método e controlar o progresso de percentagem de utilização de transformação de índice de  **IndexTransformationProgress** propriedade de resposta de um **ReadDocumentCollectionAsync** chamada. Outros SDKs e a API REST suportam métodos e propriedades equivalentes para efetuar alterações de política de indexação.

Eis um fragmento de código que mostra como modificar política de indexação de uma coleção do modo de indexação consistente para Lazy.

**Modificar a política de indexação de consistente para lento**

    // Switch to lazy indexing.
    Console.WriteLine("Changing from Default to Lazy IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.Lazy;

    await client.ReplaceDocumentCollectionAsync(collection);


Pode verificar o progresso de uma transformação de índice ao chamar ReadDocumentCollectionAsync, por exemplo, conforme mostrado abaixo.

**Controlar o progresso da transformação do índice**

    long smallWaitTimeMilliseconds = 1000;
    long progress = 0;

    while (progress < 100)
    {
        ResourceResponse<DocumentCollection> collectionReadResponse = await client.ReadDocumentCollectionAsync(
            UriFactory.CreateDocumentCollectionUri("db", "coll"));

        progress = collectionReadResponse.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromMilliseconds(smallWaitTimeMilliseconds));
    }

Pode remover o índice de uma coleção, movendo para nenhum modo de indexação. Pode ser uma ferramenta operacional útil se pretender cancelar uma transformação em curso e iniciar um novo imediatamente.

**Remover o índice de uma coleção**

    // Switch to lazy indexing.
    Console.WriteLine("Dropping index by changing to to the None IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.None;

    await client.ReplaceDocumentCollectionAsync(collection);

Quando seria são efetuadas alterações de política de indexação a suas coleções de BD do Cosmos do Azure? Seguem-se os casos de utilização mais comuns:

* Servir resultados consistentes durante o funcionamento normal, mas enquadram-se a indexação lento durante a importação de dados em massa
* Início utilizando a nova indexação funcionalidades nos seus coleções de BD do Cosmos Azure atuais, por exemplo, como geoespacial consultas que requerem o tipo de índice geográfico ou Order By / consultas de intervalo, o que exige a cadeia de tipo de índice de intervalo de cadeia
* Mão Selecione as propriedades para ser indexados e alterá-los ao longo do tempo
* Otimizar a indexação de precisão e melhorar o desempenho de consulta ou reduza o armazenamento consumido

> [!NOTE]
> Para modificar a política de indexação utilizando ReplaceDocumentCollectionAsync, terá de versão > = 1.3.0 do .NET SDK
> 
> Para transformação do índice concluir com êxito, tem de se certificar de que existe espaço suficiente espaço de armazenamento livre disponível na coleção. Se a coleção atingir a quota de armazenamento, em seguida, a transformação do índice irá ser colocada em pausa. Transformação de índice será retomado automaticamente quando o espaço de armazenamento estiver disponível, por exemplo, se não eliminar alguns documentos.
> 
> 

## <a name="performance-tuning"></a>Otimização do desempenho
As APIs do DocumentDB fornecem informações sobre as métricas de desempenho, tais como o armazenamento de índice utilizado e o custo de débito (unidades de pedido) para cada operação. Esta informação pode ser utilizada para comparar várias políticas de indexação e de otimização de desempenho.

Para verificar a quota de armazenamento e a utilização de uma coleção, executar um pedido HEAD ou GET contra o recurso de coleção e inspecionar o x-ms-pedido-quota e os cabeçalhos de x-ms-pedido-utilização. SDK .NET, o [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) e [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) propriedades no [ResourceResponse < T\> ](http://msdn.microsoft.com/library/dn799209.aspx) conter estes valores correspondentes.

     // Measure the document size usage (which includes the index size) against   
     // different policies.
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


Para medir a sobrecarga de indexação de cada operação de escrita (criar, atualizar ou eliminar), Inspecione o cabeçalho x-ms-pedido-encargos (ou equivalente [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) propriedade no [ResourceResponse < T\> ](http://msdn.microsoft.com/library/dn799209.aspx) no SDK do .NET) para medir o número de unidades de pedido consumidos por estas operações.

     // Measure the performance (request units) of writes.     
     ResourceResponse<Document> response = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), myDocument);              
     Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);

     // Measure the performance (request units) of queries.    
     IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"), queryString).AsDocumentQuery();

     double totalRequestCharge = 0;
     while (queryable.HasMoreResults)
     {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
        Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
        totalRequestCharge += queryResponse.RequestCharge;
     }

     Console.WriteLine("Query consumed {0} request units in total", totalRequestCharge);

## <a name="changes-to-the-indexing-policy-specification"></a>Alterações a especificação de política de indexação
Uma alteração no esquema de política de indexação foi introduzida no 7 de Julho de 2015 com a API de REST versão 2015-06-03. As classes correspondentes nas versões do SDK tem novas implementações para fazer corresponder o esquema. 

As seguintes alterações foram implementadas na especificação de JSON:

* Política de indexação suporta índices de intervalo para cadeias
* Cada caminho pode ter várias definições de índice, uma para cada tipo de dados
* A indexação de precisão suporta 1-8 para números, 1-100 para cadeias de e -1 (precisão máxima)
* Os segmentos de caminhos não requerem uma aspas para cada caminho de escape dupla. Por exemplo, pode adicionar um caminho para o título /? em vez de / "title" /?
* O caminho de raiz que representa a "todos os caminhos" pode ser representado como / * (para além /)

Se tiver código que Aprovisiona coleções com uma política de indexação personalizada escrita com a versão 1.1.0 do SDK do .NET ou anterior, terá de alterar o código da aplicação para processar estas alterações para mover para o SDK versão 1.2.0. Se não tiver código que configura a política de indexação, ou se pretende continuar a utilizar uma versão mais antiga do SDK, não são necessárias alterações.

Para ver uma comparação prática, segue-se um exemplo personalizado política de indexação escritas utilizando a API de REST versão 2015-06-03, bem como a versão anterior de 2015-04-08.

**Política de indexação anterior JSON**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "IncludedPaths":[
          {
             "IndexType":"Hash",
             "Path":"/",
             "NumericPrecision":7,
             "StringPrecision":3
          }
       ],
       "ExcludedPaths":[
          "/\"nonIndexedContent\"/*"
       ]
    }

**Política de indexação atual JSON**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Hash",
                   "dataType":"String",
                   "precision":3
                },
                {
                   "kind":"Hash",
                   "dataType":"Number",
                   "precision":7
                }
             ]
          }
       ],
       "ExcludedPaths":[
          {
             "path":"/nonIndexedContent/*"
          }
       ]
    }

## <a name="next-steps"></a>Passos Seguintes
Siga as ligações abaixo para exemplos de gestão de política de índice e para saber mais sobre a linguagem de consulta da BD do Cosmos do Azure.

1. [Exemplos de código do índice de .NET do DocumentDB API Management](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
2. [Operações de coleção do DocumentDB API REST](https://msdn.microsoft.com/library/azure/dn782195.aspx)
3. [Consulta com o SQL Server](documentdb-sql-query.md)

