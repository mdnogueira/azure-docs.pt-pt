---
title: Suporte de funcionalidades do Cosmos BD do Azure para o MongoDB | Microsoft Docs
description: Saiba mais sobre o suporte de funcionalidades que fornece o API do MongoDB Cosmos BD do Azure para MongoDB 3.4.
services: cosmos-db
author: alekseys
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 29b6547c-3201-44b6-9e0b-e6f56e473e24
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: alekseys
ms.openlocfilehash: 007b530cd7a14f063ae4f86d18daa9742c6655c2
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="mongodb-api-support-for-mongodb-features-and-syntax"></a>Suporte de APIS de MongoDB para a sintaxe e funcionalidades do MongoDB

BD do Azure do Cosmos é serviço de base de dados com múltiplos modelo global distribuída da Microsoft. Pode comunicar com MongoDB API a base de dados através de qualquer um cliente open source para MongoDB [controladores](https://docs.mongodb.org/ecosystem/drivers). A API do MongoDB permite a utilização de controladores existentes do cliente para o MongoDB a cumprir os [ligar protocolo](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Ao utilizar a API do Azure Cosmos DB MongoDB, possam desfrutar as vantagens das APIs do MongoDB está a ser utilizado, com todas as capacidades de enterprise da BD do Azure Cosmos: [distribuição global](distribute-data-globally.md), [fragmentação automática](partition-data.md), disponibilidade e a latência garantias, automáticas a indexação de cada campo, a encriptação rest, as cópias de segurança e muito mais.

## <a name="mongodb-query-language-support"></a>Suporte de idioma de consulta do MongoDB

API de MongoDB de BD do Azure Cosmos fornece suporte abrangente para construções de idioma de consulta do MongoDB. Abaixo pode encontrar a lista detalhada das operações atualmente suportadas, operadores, fases, comandos e as opções.


## <a name="database-commands"></a>Comandos de base de dados

BD do Cosmos do Azure suporta os seguintes comandos de base de dados em todas as contas de API do MongoDB. 

### <a name="query-and-write-operation-commands"></a>Comandos de operação de consulta e de escrita
- eliminar
- localizar
- findAndModify
- getLastError
- getMore
- Inserir
- Atualização

### <a name="authentication-commands"></a>Comandos de autenticação
- terminar sessão
- autenticar
- getnonce

### <a name="administration-commands"></a>Comandos de administração
- dropDatabase
- listCollections
- remover
- criar
- filemd5
- createIndexes
- listIndexes
- dropIndexes
- ConnectionStatus
- Reindexar

### <a name="diagnostics-commands"></a>Comandos de diagnóstico
- buildInfo
- collStats
- dbStats
- hostInfo
- listDatabases
- whatsmyuri

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>Pipeline de agregação</a>

BD do Azure do Cosmos suporta pipeline de agregação em pré-visualização pública. Consulte o [blogue do Azure](https://aka.ms/mongodb-aggregation) para obter instruções sobre como integrar para a pré-visualização pública.

### <a name="aggregation-commands"></a>Comandos de agregação
- agregado
- contagem
- Distintos

### <a name="aggregation-stages"></a>Fases de agregação
- $project
- $match
- $limit
- $skip
- $unwind
- $group
- $sample
- $sort
- $lookup
- $out
- $count

### <a name="aggregation-expressions"></a>Expressões de agregação

#### <a name="boolean-expressions"></a>Expressões
- $e
- $ou
- $not

#### <a name="set-expressions"></a>Expressões de conjunto
- $setEquals
- $setIntersection
- $setUnion
- $setDifference
- $setIsSubset
- $anyElementTrue
- $allElementsTrue

#### <a name="comparison-expressions"></a>Expressões de comparação
- $cmp
- $eq
- $gt
- $gte
- $lt
- $lte
- $ne

#### <a name="arithmetic-expressions"></a>Expressões aritméticas
- $abs
- Adicionar $
- $ceil
- $divide
- $exp
- $floor
- $ln
- $log
- $log10
- $mod
- $multiplicar
- $pow
- $sqrt
- $subtrair
- $trunc

#### <a name="string-expressions"></a>Expressões de cadeia
- $concat
- $indexOfBytes
- $indexOfCP
- $split
- $strLenBytes
- $strLenCP
- $strcasecmp
- $substr
- $substrBytes
- $substrCP
- $toLower
- $toUpper

#### <a name="array-expressions"></a>Expressões de matriz
- $arrayElemAt
- $concatArrays
- $filter
- $indexOfArray
- $isArray
- $range
- $reverseArray
- $size
- $slice
- $no

#### <a name="date-expressions"></a>Expressões de data
- $dayOfYear
- $dayOfMonth
- $dayOfWeek
- $year
- $month
- $week
- $hour
- $minute
- $segundo
- $millisecond
- $isoDayOfWeek
- $isoWeek

#### <a name="conditional-expressions"></a>Expressões condicionais
- $cond
- $ifNull

## <a name="aggregation-accumulators"></a>Accumulators de agregação
- $sum
- $avg
- $primeiro
- $última
- $max
- $min
- $push
- $addToSet

## <a name="operators"></a>Operadores

Operadores seguintes são suportados com correspondentes exemplos da sua utilização. Considere este documento de exemplo utilizado em consultas abaixo:

```json
{
  "Volcano Name": "Rainier",
  "Country": "United States",
  "Region": "US-Washington",
  "Location": {
    "type": "Point",
    "coordinates": [
      -121.758,
      46.87
    ]
  },
  "Elevation": 4392,
  "Type": "Stratovolcano",
  "Status": "Dendrochronology",
  "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

Operador | Exemplo |
--- | --- |
$eq | ``` { "Volcano Name": { $eq: "Rainier" } } ``` |  | -
$gt | ``` { "Elevation": { $gt: 4000 } } ``` |  | -
$gte | ``` { "Elevation": { $gte: 4392 } } ``` |  | -
$lt | ``` { "Elevation": { $lt: 5000 } } ``` |  | -
$lte | ``` { "Elevation": { $lte: 5000 } } ``` | | -
$ne | ``` { "Elevation": { $ne: 1 } } ``` |  | -
$no | ``` { "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } } ``` |  | -
$nin | ``` { "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } } ``` | | -
$ou | ``` { $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$e | ``` { $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$not | ``` { "Elevation": { $not: { $gt: 5000 } } } ```|  | -
$nem | ``` { $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] } ``` |  | -
$existe | ``` { "Status": { $exists: true } } ```|  | -
$type | ``` { "Status": { $type: "string" } } ```|  | -
$mod | ``` { "Elevation": { $mod: [ 4, 0 ] } } ``` |  | -
$regex | ``` { "Volcano Name": { $regex: "^Rain"} } ```|  | -

### <a name="notes"></a>Notas

Nas consultas $regex, esquerda-ancorada expressões permitem a pesquisa do índice. No entanto, utilizar 'i' modificador (case-insensitivity) e estou ' modificador (múltiplas) faz com que a análise de coleção em todas as expressões.
Quando for necessário para incluir '$' ou ' |', é melhor criar consultas de regex dois (ou mais). Por exemplo, dada a seguinte consulta original: ```find({x:{$regex: /^abc$/})```, tem de ser modificados da seguinte forma: ```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.
A primeira parte irá utilizar o índice para restringir a pesquisa a esses documentos a partir ^ abc e a segunda parte irá estabeler correspondência exatas entradas. Barra do operador ' |' funciona como uma função "ou" - a consulta ```find({x:{$regex: /^abc|^def/})``` corresponde a whin documentos o campo 'x' tem um valor que comece com "abc" ou "def". Para utilizar o índice, é recomendado para interromper a consulta duas consultas diferentes associadas pelo operador ou $: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.


### <a name="geospatial-operators"></a>Operadores de Geoespacial

Operador | Exemplo 
--- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Sim
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Sim
$quase | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Sim
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Sim
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Sim
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | Sim
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Sim
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | Sim
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Sim
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | Sim
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Sim

## <a name="additional-operators"></a>Operadores adicionais

Operador | Exemplo | Notas 
--- | --- | --- |
$all | ```{ "Location.coordinates": { $all: [-121.758, 46.87] } }``` | 
$elemMatch | ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } } }``` |  
$size | ```{ "Location.coordinates": { $size: 2 } }``` | 
$comment |  ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } }, $comment: "Negative values"}``` | 
$text |  | Não é suportado. Em alternativa, utilize $regex 

### <a name="methods"></a>Métodos

São suportados os seguintes métodos:

#### <a name="cursor-methods"></a>Métodos de cursor

Método | Exemplo | Notas 
--- | --- | --- |
cursor.sort() | ```cursor.sort({ "Elevation": -1 })``` | Não são devolvidos documentos sem chave de ordenação

## <a name="unique-indexes"></a>Índices exclusivos

BD do Azure do Cosmos indexa todos os campos em documentos que são escritos na base de dados por predefinição. Índices exclusivos Certifique-se de que um campo específico não tem valores duplicados em todos os documentos numa coleção, semelhante a exclusividade de forma é preservado na chave predefinido ID". Agora, pode criar índices personalizados do BD Azure Cosmos utilizando o comando createIndex, incluindo a restrição 'exclusiva'.

Índices exclusivos estão disponíveis para todas as contas de API do MongoDB.

## <a name="time-to-live-ttl"></a>Time-to-live (TTL)

BD do Azure do Cosmos suporta um caminho relativo time-to-live (TTL) com base no timestamp do documento. TTL pode ser ativada para coleções de API do MongoDB através de [portal do Azure](https://portal.azure.com).

## <a name="user-and-role-management"></a>Gestão e função de utilizador

BD do Azure do Cosmos ainda não suporte utilizadores e funções. BD do Azure do Cosmos suporta o controlo de acesso baseado em funções (RBAC) e de leitura / escrita só de leitura palavras-passe/chaves e que podem ser obtidas através de [portal do Azure](https://portal.azure.com) (página de cadeia de ligação).

## <a name="replication"></a>Replicação

BD do Cosmos do Azure suporta a replicação automática, nativa as camadas mais baixas. Esta lógica é expandida enviados para alcançar, bem como a replicação global, latência baixa. BD do Azure do Cosmos não suporta comandos replicação manual.

## <a name="sharding"></a>Fragmentação

BD do Azure do Cosmos suporta fragmentação automática, lado do servidor. BD do Azure do Cosmos não suporta comandos de fragmentação manual.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [utilizar Studio 3T](mongodb-mongochef.md) com uma API para a base de dados de MongoDB.
- Saiba como [utilizar Robo 3T](mongodb-robomongo.md) com uma API para a base de dados de MongoDB.
- Explorar a base de dados do Azure Cosmos com suporte de protocolos para MongoDB [amostras](mongodb-samples.md).
