---
title: Trabalhar com dados geoespacial do BD Azure Cosmos | Microsoft Docs
description: "Compreenda como criar, índice e geográficos objetos com base de dados do Azure Cosmos e a API do DocumentDB de consulta."
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: 82ce2898-a9f9-4acf-af4d-8ca4ba9c7b8f
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/20/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2c2213f663f539e123f70028fd70bedb1cb6511d
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2017
---
# <a name="working-with-geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Trabalhar com dados de localização de GeoJSON do BD Azure Cosmos e geoespacial
Este artigo é uma introdução para a funcionalidade de geoespacial no [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Depois de ler este artigo, poderá responder às seguintes questões:

* Como posso armazenar dados geográficos do BD Azure Cosmos?
* Como pode consultar os dados geoespacial na base de dados do Azure Cosmos no SQL Server e LINQ?
* Como ativar ou desativar a indexação geográficos do BD Azure Cosmos?

Este artigo mostra como trabalhar com dados geográficos com a API do DocumentDB. Consulte este [GitHub projeto](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs) para exemplos de código.

## <a name="introduction-to-spatial-data"></a>Introdução aos dados geográficos
Dados geográficos descreve a posição e a forma de objetos de espaço. Na maioria das aplicações, estas correspondem aos objetos na terra, ou seja, os dados de geoespacial. Dados geográficos podem ser utilizados para representar a localização de uma pessoa, um local de interesse ou limites de uma cidade ou uma lake. Casos de utilização comuns envolvem, muitas vezes, consultas de proximidade, para por exemplo, "encontrar todos os cafés quase meu localização atual". 

### <a name="geojson"></a>GeoJSON
BD do Cosmos do Azure suporta a indexação e consultar geoespacial ponto de dados de que tem representado utilizando o [GeoJSON especificação](https://tools.ietf.org/html/rfc7946). Estruturas de dados de GeoJSON são sempre objetos JSON válidos, para que podem ser armazenadas e consultados utilizando a base de dados do Azure Cosmos sem quaisquer ferramentas especializadas ou bibliotecas. Os SDKs do Azure Cosmos DB fornecem classes de programa auxiliar e métodos que tornam mais fácil trabalhar com dados geográficos. 

### <a name="points-linestrings-and-polygons"></a>Pontos, Multipoints e polígonos
A **ponto** indica uma posição único no espaço. Dados geoespacial, um ponto de representa a localização exata, que pode ser uma morada de um arquivo de grocery, um quiosque, um automóvel ou uma localidade.  É representado um ponto no par GeoJSON (e base de dados do Azure Cosmos) utilizando o respetiva coordenada ou latitude e longitude. Eis um exemplo JSON para um ponto.

**Pontos do Cosmos BD do Azure**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

> [!NOTE]
> A especificação de GeoJSON Especifica longitude primeiro e latitude segundo. Como nas outras aplicações de mapeamento, latitude e longitude são os ângulos em representado em termos de graus. Os valores de longitude são avaliados do meridiano de Prime e encontram-se entre-180 e e 180.0 graus e latitude valores são avaliados do Equador e são entre-90.0 e 90.0 graus. 
> 
> BD do Azure do Cosmos interpreta coordenadas conforme representado pelo sistema de referência de WGS 84. Consulte abaixo para obter mais detalhes sobre os sistemas de referência coordenada.
> 
> 

Isto pode ser incorporado um documento de base de dados do Azure Cosmos como o mostrado neste exemplo de um perfil de utilizador que contém dados de localização:

**Utilizar perfil com a localização armazenada na base de dados do Azure Cosmos**

```json
{
    "id":"documentdb-profile",
    "screen_name":"@CosmosDB",
    "city":"Redmond",
    "topics":[ "global", "distributed" ],
    "location":{
        "type":"Point",
        "coordinates":[ 31.9, -4.8 ]
    }
}
```

Para além de pontos, GeoJSON também suporta Multipoints e de polígonos. **Multipoints** representam uma série de duas ou mais pontos de espaço e os segmentos de linha que se ligam-los. Dados geoespacial, Multipoints são frequentemente utilizadas para representar highways ou rivers. A **polígono** é um limite de pontos ligados, o que constitui uma LineString fechada. Polígonos são frequentemente utilizados para representar formação que resultam em natural como lagos ou jurisdictions afiliações como cidades e Estados. Eis um exemplo de um polígono do BD Azure Cosmos. 

**Polígonos no GeoJSON**

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ],
        [ 32, -4.7 ],
        [ 32, -5 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> A especificação de GeoJSON requer que de polígonos válidos, o último par coordenado fornecido deve ser o mesmo que a primeira, para criar uma forma fechada.
> 
> Pontos de dentro de um polígono tem de ser especificados por ordem counter-no sentido horário. Um polígono especificado na ordem no sentido horário representa o inverso da região dentro da mesma.
> 
> 

Para além de ponto, LineString e polígono, GeoJSON também especifica a representação para agrupar várias localizações geoespacial, bem como associar propriedades arbitrários geolocalização como um **funcionalidade**. Uma vez que estes objetos JSON válido, estes podem todos ser armazenadas e processadas do BD Azure Cosmos. No entanto Azure Cosmos DB só suporta a indexação automática de pontos.

### <a name="coordinate-reference-systems"></a>Coordenar a sistemas de referência
Uma vez que a forma terrestre dados, coordenadas dos dados geoespacial é representado na muitos sistemas de referência coordenada (CR), cada um com os seus próprios frames de referência e unidades de medida. Por exemplo, o "National grelha de Britain" é um sistema de referência for muito exato para o Reino Unido, mas não fora-lo. 

O CR mais popular em utilização atualmente é o sistema Geodetic mundo [WGS 84](http://earth-info.nga.mil/GandG/wgs84/). Dispositivos GPS e muitas mapeamento serviços, incluindo mapas do Google e APIs do Bing Maps utilizam WGS 84. BD do Cosmos do Azure suporta a indexação e consultar dados geoespacial utilizando apenas a CR de WGS 84. 

## <a name="creating-documents-with-spatial-data"></a>Criar documentos com dados geográficos
Quando criar documentos que contenham valores GeoJSON, eles são automaticamente indexados com um índice geográfico no accordance para a política de indexação da coleção. Se estiver a trabalhar com um SDK de BD do Azure Cosmos num idioma escrito de forma dinâmica, como o Python ou Node.js, tem de criar GeoJSON válido.

**Criar um documento com dados Geoespacial no Node.js**

```json
var userProfileDocument = {
    "name":"documentdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

Se estiver a trabalhar com as APIs do DocumentDB, pode utilizar o `Point` e `Polygon` classes dentro de `Microsoft.Azure.Documents.Spatial` espaço de nomes para incorporar as informações de localização dentro os objetos da aplicação. Estas classes ajudam a simplificar a serialização e a anulação da serialização de dados geográficos para GeoJSON.

**Criar um documento com dados Geoespacial no .NET**

```json
using Microsoft.Azure.Documents.Spatial;

public class UserProfile
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "profiles"), 
    new UserProfile 
    { 
        Name = "documentdb", 
        Location = new Point (-122.12, 47.66) 
    });
```

Se não tiver as informações de latitude e longitude, mas tiver o nome de localização como cidade ou o país ou endereços físicos, pode ver as coordenadas reais utilizando um serviço a codificação geográfica como serviços de REST do Bing Maps. Saiba mais sobre a codificação geográfica Bing Maps [aqui](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="querying-spatial-types"></a>Consultar os tipos geográficos
Agora que iremos tiver decorrido ver como inserir dados geoespacial, vamos ver como consultar estes dados utilizando a BD do Cosmos do Azure com o SQL e LINQ.

### <a name="spatial-sql-built-in-functions"></a>Geográficos funções incorporadas do SQL Server
BD do Azure do Cosmos suporta as seguintes funções incorporadas abra Geoespacial Consortium (OGC) para consultar o geoespacial. Para obter mais detalhes sobre o conjunto completo de funções incorporadas no idioma do SQL Server, consulte [consulta Azure Cosmos DB](documentdb-sql-query.md).

<table>
<tr>
  <td><strong>Utilização</strong></td>
  <td><strong>Descrição</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (spatial_expr, spatial_expr)</td>
  <td>Devolve a distância entre duas expressões de ponto de GeoJSON, polígono ou LineString.</td>
</tr>
<tr>
  <td>ST_WITHIN (spatial_expr, spatial_expr)</td>
  <td>Devolve uma expressão booleana que indica se o objeto GeoJSON primeiro (ponto, polígono ou LineString) é no objeto GeoJSON segundo (ponto, polígono ou LineString).</td>
</tr>
<tr>
  <td>ST_INTERSECTS (spatial_expr, spatial_expr)</td>
  <td>Devolve uma expressão booleana que indica se os dois objetos de GeoJSON especificados (ponto, polígono ou LineString) intersect.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Devolve um valor booleano que indica se a expressão de ponto de GeoJSON, polígono ou LineString especificada é válida.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Devolve um valor JSON que contém um valor boleano valor se a expressão de ponto de GeoJSON, polígono ou LineString especificada é válida e se inválido, além do motivo como um valor de cadeia.</td>
</tr>
</table>

Funções geográficos podem ser utilizadas para executar consultas de proximidade contra dados geográficos. Por exemplo, aqui está uma consulta que devolva todos os documentos famílias que estejam dentro de 30 km da localização especificada utilizando a função incorporada ST_DISTANCE. 

**Consulta**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Resultados**

    [{
      "id": "WakefieldFamily"
    }]

Se incluir indexação geográficos na política de indexação, em seguida, "consultas distância" serão servidas forma eficiente através do índice. Para obter mais detalhes sobre a indexação geográficos, consulte a secção abaixo. Se não tiver um índice geográfico para caminhos especificados, ainda pode executar consultas geográficos especificando `x-ms-documentdb-query-enable-scan` cabeçalho de pedido com o valor definido como "true". No .NET, isto pode ser feito através da transmissão a opcional **FeedOptions** argumento para consultas com [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) definido como verdadeiro. 

ST_WITHIN pode ser utilizado para verificar se um ponto de se situa dentro de um polígono. Normalmente polígonos são utilizados para representar limites como zip códigos, limites de estado ou formação que resultam em natural. Novamente se incluir indexação geográficos na política de indexação, em seguida, "dentro" consultas serão servidas forma eficiente através do índice. 

Os argumentos polígono ST_WITHIN podem conter apenas um único toque, ou seja, os polígonos não podem conter holes nos mesmos. 

**Consulta**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Resultados**

    [{
      "id": "WakefieldFamily",
    }]

> [!NOTE]
> Semelhante ao funciona como erros de correspondência de tipos na consulta de base de dados do Azure Cosmos, se o valor da localização especificada no argumento está incorreto ou é inválido, em seguida, que será avaliada como **indefinido** e o documento avaliado ignorada da consulta resultados. Se a consulta devolve não existem resultados, execute ST_ISVALIDDETAILED para depuração por que motivo o tipo de spatail é inválido.     
> 
> 

BD do Azure do Cosmos também suporta a execução de consultas tangente, ou seja, pode indexar polígonos ou linhas na base de dados do Azure Cosmos e consultar as áreas que contenha um ponto especificado. Este padrão é frequentemente utilizado na logística para identificar, por exemplo, quando um camião entra ou sai de uma área designada. 

**Consulta**

    SELECT * 
    FROM Areas a 
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)


**Resultados**

    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon", 
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]

ST_ISVALID e ST_ISVALIDDETAILED podem ser utilizados para verificar se um objeto espacial é válido. Por exemplo, a seguinte consulta verifica a validade de um ponto com um fora do valor de intervalo de latitude (-132.8). ST_ISVALID devolve apenas um valor Booleano e ST_ISVALIDDETAILED devolve o valor de booleano e uma cadeia contendo o motivo por que motivo é considerada inválida.

* * Consultar * *

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Resultados**

    [{
      "$1": false
    }]

Estas funções também podem ser utilizadas para validar polígonos. Por exemplo, aqui, utilizamos ST_ISVALIDDETAILED para validar um polígono que não está fechado. 

**Consulta**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**Resultados**

    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]

### <a name="linq-querying-in-the-net-sdk"></a>LINQ consultar no SDK do .NET
O SDK do .NET DocumentDB também fornecedores stub métodos `Distance()` e `Within()` para utilização nas expressões de LINQ. O fornecedor DocumentDB LINQ traduz destas chamadas de método para as chamadas de função incorporada equivalentes do SQL Server (ST_DISTANCE e ST_WITHIN respetivamente). 

Eis um exemplo de uma consulta LINQ localiza todos os documentos na coleção de base de dados do Azure Cosmos cujo valor de "localização" está dentro de um radius de 30km de especificado ponto utilizando LINQ.

**Consulta LINQ para distância**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(u => u.ProfileType == "Public" && a.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

Da mesma forma, segue-se uma consulta para encontrar todos os documentos cuja "localização" está numa caixa/polígono especificado. 

**Consulta de LINQ para dentro do**

    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }


Agora que iremos tiver decorrido ver como consultar documentos utilizando LINQ e SQL Server, vamos ver como configurar a base de dados do Azure Cosmos para indexação geográficos.

## <a name="indexing"></a>Indexação
Conforme é descrito no [esquema Agnóstico indexação com base de dados do Azure Cosmos](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) documento, vamos concebido motor de base de dados da BD do Azure Cosmos estar verdadeiramente agnóstico de esquema e fornecer suporte de primeira classe para JSON. O motor de base de dados com otimização de escrita da base de dados do Azure Cosmos nativamente compreende dados geográficos (pontos, Multilinestrings e linhas) representados na GeoJSON padrão.

Um nutshell, a geometria é projetada de coordenadas geodetic para um plane 2D, em seguida, dividida progressivamente células utilizando um **quadtree**. Estes células estão mapeadas para 1D com base na localização da célula dentro de um **Hilbert espaço ao preencher em curva**, que ela vai preservando localidade de pontos. Além disso quando os dados de localização estão indexados, passa através de um processo conhecido como **tecelagem**, ou seja, todas as células que intersect uma localização estão identificadas e armazenadas como chaves no índice BD do Cosmos do Azure. No momento da consulta, argumentos, como pontos e de polígonos são também tessellated para extrair os intervalos de ID de célula relevantes, em seguida, utilizados para obter dados do índice.

Se especificar uma política de indexação que inclui o índice geográfico para / * (todos os caminhos), em seguida, todos os pontos de encontrado dentro da coleção são indexados para consultas geográficos eficiente (ST_WITHIN e ST_DISTANCE). Os índices espaciais não ter um valor de precisão e utilize sempre um valor de precisão predefinido.

> [!NOTE]
> BD do Cosmos do Azure suporta a indexação automática de pontos, Multilinestrings e Multipoints
> 
> 

O fragmento JSON seguinte mostra uma política de indexação com indexação geográficos ativado, ou seja, qualquer ponto GeoJSON encontrado documentos para consultas geográficos de índice. Se estiver a modificar a política de indexação através do Portal do Azure, pode especificar o seguinte JSON de política de indexação para ativar geográficos de indexação na sua coleção.

**Coleção JSON de política de indexação com Spatial ativada e de pontos de polígonos**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Range",
                   "dataType":"String",
                   "precision":-1
                },
                {
                   "kind":"Range",
                   "dataType":"Number",
                   "precision":-1
                },
                {
                   "kind":"Spatial",
                   "dataType":"Point"
                },
                {
                   "kind":"Spatial",
                   "dataType":"Polygon"
                }                
             ]
          }
       ],
       "excludedPaths":[
       ]
    }

Eis um fragmento de código no .NET que mostra como criar uma coleção com a indexação geográficos ativada para todos os caminhos que contenham pontos. 

**Criar uma coleção com a indexação geográficos**

    DocumentCollection spatialData = new DocumentCollection()
    spatialData.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point)); //override to turn spatial on by default
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), spatialData);

E Eis como pode modificar uma coleção existente para tirar partido de indexação geográficos através de quaisquer pontos que estão armazenados em documentos.

**Modificar uma coleção existente com a indexação geográficos**

    Console.WriteLine("Updating collection with spatial indexing enabled in indexing policy...");
    collection.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point));
    await client.ReplaceDocumentCollectionAsync(collection);

    Console.WriteLine("Waiting for indexing to complete...");
    long indexTransformationProgress = 0;
    while (indexTransformationProgress < 100)
    {
        ResourceResponse<DocumentCollection> response = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
        indexTransformationProgress = response.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromSeconds(1));
    }

> [!NOTE]
> Se a localização GeoJSON valor no documento é um formato incorreto ou é inválido, em seguida,-será não obter indexado para consultar geográficos. Pode validar utilizando ST_ISVALID e ST_ISVALIDDETAILED valores da localização.
> 
> Se a definição da coleção inclui uma chave de partição, não foi reportado o progresso de transformação de indexação. 
> 
> 

## <a name="next-steps"></a>Passos seguintes
Agora que já learnt sobre como começar com suporte de geoespacial do BD Azure Cosmos, pode:

* Iniciar a codificação com a [exemplos de código Geoespacial .NET no GitHub](https://github.com/Azure/azure-documentdb-dotnet/blob/fcf23d134fc5019397dcf7ab97d8d6456cd94820/samples/code-samples/Geospatial/Program.cs)
* Obter às mãos em com geoespacial consultar no [Azure Cosmos DB Query Playground](http://www.documentdb.com/sql/demo#geospatial)
* Saiba mais sobre [consulta de base de dados do Azure Cosmos](documentdb-sql-query.md)
* Saiba mais sobre [políticas de indexação do Azure Cosmos DB](indexing-policies.md)

