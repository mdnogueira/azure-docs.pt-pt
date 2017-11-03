---
title: Expirar os dados na base de dados do Azure Cosmos com TTL | Microsoft Docs
description: "Com o TTL, base de dados do Microsoft Azure Cosmos fornece a capacidade de ter documentos automaticamente removidos do sistema após um período de tempo."
services: cosmos-db
documentationcenter: 
keywords: TTL
author: arramac
manager: jhubbard
editor: 
ms.assetid: 25fcbbda-71f7-414a-bf57-d8671358ca3f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2017
ms.author: arramac
ms.openlocfilehash: 6213019131eec60263172f468ced516037a33c61
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="expire-data-in-azure-cosmos-db-collections-automatically-with-time-to-live"></a>Expirar os dados em coleções de base de dados do Azure Cosmos automaticamente com TTL
As aplicações podem produzir e armazenar grandes quantidades de dados. Alguns destes dados, incluindo machine gerado dados, os registos e utilizador a sessão do evento informações apenas são útil para um período de tempo finito. Depois dos dados ficam surplus às necessidades da aplicação é seguro remover estes dados e reduzir as necessidades de armazenamento de uma aplicação.

Com "tempo TTL" ou TTL, base de dados do Microsoft Azure Cosmos fornece a capacidade de ter documentos automaticamente removidos da base de dados após um período de tempo. O tempo predefinido em direto pode ser definido ao nível da coleção e substituí-lo numa base por documento. Depois do TTL é definido como uma predefinição de coleção ou a um nível de documento, base de dados do Cosmos removerá automaticamente documentos que existem depois desse período de tempo, em segundos, desde que foram modificado pela última vez.

TTL na base de dados do Cosmos utiliza um desvio contra quando o documento foi modificado pela última vez. Para fazê-lo utiliza o `_ts` campo de que existe em todos os documentos. O campo de _ts é um carimbo de época de estilo unix que representa a data e hora. O `_ts` campo é atualizado sempre que um documento é modificado. 

## <a name="ttl-behavior"></a>Comportamento TTL
A funcionalidade TTL é controlada pelas propriedades TTL em dois níveis - nível da coleção e o nível de documento. Os valores estão definidos em segundos e são tratados como um delta do `_ts` que o documento foi efetuada a último modificação em.

1. DefaultTTL para a coleção
   
   * Se estiverem em falta (ou definido como nulo), documentos não são eliminados automaticamente.
   * Se presente e o valor for "-1" = infinita – documentos não expirarem por predefinição
   * Se presente e o valor é algumas número ("n") – documentos expirarem "n" segundos após a última modificação
2. Valor de TTL para os documentos: 
   
   * Propriedade só é aplicável se DefaultTTL está presente para a coleção principal.
   * Substitui o valor de DefaultTTL para a coleção principal.

Assim que o documento expirou (`ttl`  +  `_ts` < = hora atual do servidor), o documento está marcado como "expirado". Nenhuma operação será permitida nestes documentos após esta hora e irá ser excluídas dos resultados de consultas de efetuar. Os documentos são eliminados fisicamente no sistema e são eliminados em segundo plano opportunistically numa altura posterior. Isto não consome qualquer [unidades de pedido (RUs)](request-units.md) da atribuição de coleção.

A lógica acima pode ser apresentada na matriz seguinte:

|  | DefaultTTL em falta/não definido na coleção de | DefaultTTL = -1 na coleção | DefaultTTL = "n" na coleção |
| --- |:--- |:--- |:--- |
| TTL em falta no documento |Nada para substituir ao nível do documento, uma vez que o documento e a coleção não tem nenhum conceito de TTL. |Não existem documentos nesta coleção irão expirar. |Os documentos nesta coleção irão expirar quando intervalo n decorrida. |
| TTL = -1 no documento |Nada para substituir ao nível do documento, uma vez que a coleção não define a propriedade de DefaultTTL que pode substituir um documento. TTL num documento é não interpretado pelo sistema. |Não existem documentos nesta coleção irão expirar. |O documento com o valor de TTL =-1 nesta coleção nunca irá expirar. Todos os outros documentos irão expirar após o intervalo de "n". |
| TTL = n no documento |Nada para substituir ao nível do documento. TTL num documento no não interpretado pelo sistema. |O documento com o valor de TTL = n irá expirar após n intervalo, em segundos. Outros documentos irão herdar o intervalo de -1 e nunca expirem. |O documento com o valor de TTL = n irá expirar após n intervalo, em segundos. Outros documentos serão herdam a coleção de intervalo "n". |

## <a name="configuring-ttl"></a>Configurar o valor de TTL
Por predefinição, o TTL está desativada por predefinição em todas as coleções de BD do Cosmos e em todos os documentos. TTL pode ser definida através de programação ou no portal do Azure, além de **definições** secção para a coleção. 

## <a name="enabling-ttl"></a>Ativar o TTL
Para ativar o TTL uma coleção ou os documentos numa coleção, tem de definir a propriedade DefaultTTL de uma coleção para um número positivo diferente de zero ou -1. Definir o DefaultTTL para-1 significa que por predefinição, todos os documentos na coleção serão permanentemente em direto, mas o serviço de base de dados do Cosmos deverá monitorizar esta coleção para documentos que tenham substituído esta predefinição.

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive =-1; //never expire by default

    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(databaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });

## <a name="configuring-default-ttl-on-a-collection"></a>Configurar o TTL predefinido de uma coleção
É possível configurar uma hora predefinida TTL um nível da coleção. Para definir o valor de TTL numa coleção, tem de fornecer um número positivo diferente de zero, que indica o período de, em segundos, para expirar todos os documentos na coleção após a última modificação timestamp do documento (`_ts`). Em alternativa, pode definir a predefinição como -1, o que significa que todos os documentos inseridos na coleção serão em direto indefinidamente por predefinição.

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days
    
    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        "/dbs/salesdb",
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });


## <a name="setting-ttl-on-a-document"></a>Definição TTL de um documento
Além de definir um TTL predefinido de uma coleção pode definir o TTL específico a um nível de documento. Fazer isto irá substituir a predefinição da coleção.

* Para definir o valor de TTL num documento, tem de fornecer um número positivo diferente de zero, que indica o período de, em segundos, para expirar o documento após a última modificação timestamp do documento (`_ts`).
* Se um documento não tiver nenhum campo de valor de TTL, a predefinição da coleção serão aplicadas.
* Se o TTL é desativado ao nível da coleção, o campo de valor de TTL no documento será ignorado enquanto o TTL seja ativada de novo na coleção.

Eis um fragmento que mostra como definir a expiração de TTL num documento:

    // Include a property that serializes to "ttl" in JSON
    public class SalesOrder
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        
        [JsonProperty(PropertyName="cid")]
        public string CustomerId { get; set; }
        
        // used to set expiration policy
        [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
        public int? TimeToLive { get; set; }
        
        //...
    }
    
    // Set the value to the expiration in seconds
    SalesOrder salesOrder = new SalesOrder
    {
        Id = "SO05",
        CustomerId = "CO18009186470",
        TimeToLive = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days 
    };


## <a name="extending-ttl-on-an-existing-document"></a>Expandir o TTL num documento existente
Pode repor o valor de TTL num documento efetuando qualquer operação de escrita no documento. Fazer isto irá definir o `_ts` para a hora atual e a contagem decrescente para a expiração do documento, conforme definido pelo `ttl`, começará novamente. Se pretender alterar o `ttl` de um documento, pode atualizar o campo como pode fazer com qualquer outro campo pode ser definido.

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = 60 * 30 * 30; // update time to live
    
    response = await client.ReplaceDocumentAsync(salesOrder);

## <a name="removing-ttl-from-a-document"></a>Remover o TTL de um documento
Se tiver sido definido um valor de TTL num documento e já não pretender esse documento a expirar, em seguida, pode obter o documento, remover o campo de valor de TTL e substitua o documento no servidor. Quando o campo de valor de TTL é removido do documento, a predefinição da coleção será aplicada. Para parar um documento de expirar e não herda da coleção, em seguida, tem de definir o valor de TTL como -1.

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = null; // inherit the default TTL of the collection
    
    response = await client.ReplaceDocumentAsync(salesOrder);

## <a name="disabling-ttl"></a>Desativar o TTL
Para desativar o TTL inteiramente numa coleção e parar o processo em segundo plano à procura de documentos expirados, que a propriedade DefaultTTL na coleção deve ser eliminada. Eliminar esta propriedade é diferente do defini-la como -1. Definição para-1 significa que os novos documentos adicionado à coleção será permanentemente em direto, mas pode substituir isto em documentos específicos na coleção. Remover esta propriedade inteiramente da coleção significa que não existem documentos irão expirar, mesmo existem documentos que tenham explicitamente substituído um predefinido anterior.

    DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
    
    // Disable TTL
    collection.DefaultTimeToLive = null;
    
    await client.ReplaceDocumentCollectionAsync(collection);


## <a name="faq"></a>FAQ
**O que irá TTL custo-me?**

Não há sem custos adicionais para definir um valor de TTL num documento.

**Quanto tempo necessário para eliminar os meus documentos depois do TTL é de cópia de segurança?**

Os documentos são expirou imediatamente depois do valor de TTL está a funcionar e não estará acessível através de CRUD ou APIs de consulta. 

**TTL num documento não terá qualquer impacto em custos de RU?**

Não, existirá nenhum impacto em custos de RU para eliminações de expirada documentos através de TTL na base de dados do Cosmos.

**É a funcionalidade TTL apenas aplicável a documentos todos ou posso expirar os valores de propriedade de documento individuais?**

TTL aplica-se a todo o documento. Se gostaria de expirar apenas uma parte de um documento, em seguida, recomenda-se que extrair a parte do documento no principal para um documento de "ligado" separado e, em seguida, utilize o TTL nesse documento extraídos.

**A funcionalidade TTL tem quaisquer requisitos específicos de indexação?**

Sim. A coleção tem de ter [conjunto de política de indexação](indexing-policies.md) Consistent ou Lazy. Tentar definir DefaultTTL numa coleção com indexação definido como None resultará num erro, como irá tentar desativar a indexação de uma coleção que tenha um DefaultTTL já definida.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a BD do Cosmos do Azure, consulte o serviço [ *documentação* ](https://azure.microsoft.com/documentation/services/cosmos-db/) página.

