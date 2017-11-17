---
title: 'Azure Cosmos DB: API de Java do DocumentDB, SDK & recursos | Microsoft Docs'
description: "Saiba tudo sobre Java API e o SDK, incluindo as datas de versão, datas de extinção e as alterações efetuadas entre cada versão do SDK de Java do Azure Cosmos DB DocumentDB."
services: cosmos-db
documentationcenter: java
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 7861cadf-2a05-471a-9925-0fec0599351b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 11/14/2017
ms.author: khdang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 38737c8a296e27a4256e19b8c45cab17fc71b81a
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="azure-cosmos-db-documentdb-java-sdk-release-notes-and-resources"></a>Azure Cosmos DB: SDK de DocumentDB Java notas de versão e recursos
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [Feed de alteração de .NET](documentdb-sdk-dotnet-changefeed.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.js](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [Fornecedor de Recursos REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

<table>

<tr><td>**Transferência do SDK**</td><td>[Maven](http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)</td></tr>

<tr><td>**Documentação da API**</td><td>[Documentação de referência da API de Java](/java/api/com.microsoft.azure.documentdb)</td></tr>

<tr><td>**Contribuir para o SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr>

<tr><td>**Introdução**</td><td>[Começar com o SDK de Java](documentdb-java-get-started.md)</td></tr>

<tr><td>**Tutorial de aplicação Web**</td><td>[Desenvolvimento da aplicação Web com base de dados do Azure Cosmos](documentdb-java-application.md)</td></tr>

<tr><td>**Tempo de execução mínimo suportado**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Notas de Versão

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
* Desempenho melhorado de serialização Json.
* Esta versão do SDK requer a versão mais recente do Azure Cosmos DB emulador disponível para transferência a partir do https://aka.ms/cosmosdb-emulator.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
* Alterações internas para bibliotecas de amigos de Microsoft.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Foi corrigido um problema na leitura única partição intervalos de chaves.
* Foi corrigido um problema no ResourceID análise que afeta a base de dados com nomes abreviados.
* Corrigido uma causa do problema por codificação de chave de partição.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Correções de erros críticos para processamento durante divisões de partição de pedidos.
* Foi corrigido um problema com os níveis de consistência forte e BoundedStaleness.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Suporte adicionado para um novo nível de consistência chamado ConsistentPrefix.
* Corrigido um erro ao ler a coleção no modo de sessão.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Suporte ativado para uma coleção particionada com como baixa como 2500 RU/seg e dimensionar em incrementos de 100 RU/seg.
* Corrigido um erro na assemblagem nativa que pode originar a exceção de NullRef em algumas consultas.

### <a name="a-name196196"></a><a name="1.9.6"/>1.9.6
* Corrigido um erro na configuração do motor de consulta que pode fazer com que as exceções para consultas no modo de Gateway.
* Corrigido alguns erros no contentor de sessão que podem causar uma exceção de "Recurso de proprietário não encontrada" para pedidos imediatamente após a criação de coleção.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Suporte adicionado para consultas de agregação (CONTAGEM, MIN, MAX, soma e média). Consulte [suporte de agregação](documentdb-sql-query.md#Aggregates).
* Suporte adicionado para alteração do feed.
* Suporte adicionado para informações sobre a quota coleção através de RequestOptions.setPopulateQuotaInfo.
* Adicionado suporte para registo de script do procedimento armazenado através de RequestOptions.setScriptLoggingEnabled.
* Corrigido um erro onde a consulta no modo de DirectHttps poderá bloquear quando encontra falhas de limitação.
* Corrigido um erro no modo de consistência de sessão.
* Corrigido um erro que poderá provocar NullReferenceException HttpContext quando a taxa de pedidos é elevada.
* Melhoria do desempenho do modo de DirectHttps.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Suporte de proxy baseado na instância de cliente simples adicionado ConnectionPolicy.setProxy() API.
* API de DocumentClient.close() adicionado à instância de DocumentClient corretamente encerrado.
* Melhorado desempenho das consultas no modo de ligação direta ao efetuar a derivação de plano de consulta da assemblagem nativa em vez do Gateway.
* Definir FAIL_ON_UNKNOWN_PROPERTIES = false para que os utilizadores não têm de definir JsonIgnoreProperties no respetivo POJO.
* Registo refatorizado para utilizar SLF4J.
* Corrigidos alguns outros erros no leitor de consistência.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Corrigido um erro na gestão de ligações para impedir fugas de ligação no modo de ligação direta.
* Corrigido um erro na consulta principal onde pode acionar NullReferenece exceção.
* Melhoria do desempenho ao reduzir o número de chamadas de rede para as caches internas.
* Código de estado adicionado, ActivityID e URI de pedido no DocumentClientException para melhor de resolução de problemas.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Foi corrigido um problema na gestão de ligação para a estabilidade.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1
* Suporte adicionado para BoundedStaleness nível de consistência.
* Suporte adicionado para ligação direta para as operações CRUD para coleções particionadas.
* Corrigido um erro no consultar uma base de dados com o SQL Server.
* Corrigido um erro na cache da sessão onde o token da sessão pode ser definida incorretamente.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Suporte adicionado para entre consultas paralelas de partição.
* Suporte adicionado para consultas de parte superior/ORDER BY para coleções particionadas.
* Adicionado suporte para a consistência forte.
* Suporte adicionado para pedidos de nomes com base em quando utilizar a ligação direta.
* Corrigido tornar ActivityId permanecer consistente entre todas as tentativas de pedido.
* Corrigido um erro relacionado com a cache da sessão quando recriar uma coleção com o mesmo nome.
* Foram adicionadas polígono e tipos de dados de LineString ao especificar a coleção de indexação de política para consultas geográficos barreiras geográficas.
* Fixos problemas com a documentação do Java para 1.8 do Java.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1
* Corrigido um erro no PartitionKeyDefinitionMap para coleções de partições únicas em cache e não efetuar obtenção extra pedidos de chave de partição.
* Corrigido um erro para repetir não quando é fornecido um valor de chave de partição incorreto.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* O suporte adicionado para contas de multirregião base de dados.
* Suporte adicionado para repetição automática em pedidos otimizadas com opções para personalizar o máximo de tentativas e o tempo de espera máximo de tentativas.  Ver RetryOptions e ConnectionPolicy.getRetryOptions().
* IPartitionResolver preterido com a base de código personalizado de criação de partições. Utilize coleções particionadas para superior armazenamento e débito.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Suporte de política de repetição adicionado para limitação.  

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Tempo adicionado ao suporte (TTL) em direto para documentos.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Implementado [particionada coleções](partition-data.md) e [níveis de desempenho definido pelo utilizador](performance-levels.md).

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* Corrigido um erro no HashPartitionResolver para gerar valores de hash de little endian para estar consistente com outros SDKs.

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Adicionar Hash & intervalo de partição resoluções para ajudá-lo com aplicações de fragmentação entre várias partições.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Implemente Upsert. Novos métodos de upsertXXX adicionados para suportar a funcionalidade de Upsert.
* ID de implementar baseado Routing. Sem alterações de API públicas, todas as alterações internas.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Versão ignorado para colocar o número de versão em alinhamento com outros SDKs

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Suporta Geoespacial índice
* Valida a propriedade id de todos os recursos. Os IDs de recursos não podem conter?, /, #, \, carateres nem terminar com um espaço.
* Adiciona ResourceResponse novo cabeçalho "índice transformação curso".

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Implementa a política de indexação V2

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK

## <a name="release--retirement-dates"></a>Datas de extinção & versão
A Microsoft vai fornecer pelo menos notificação **12 meses** previamente extinguir um SDK para smooth a transição para uma versão mais recente/suportado.

Novas funcionalidades e a funcionalidade e otimizações apenas são adicionadas ao SDK atual, como tal, recomendamos que atualize sempre para a versão mais recente SDK como antecipadamente quanto possível.

Qualquer pedido de BD do Cosmos utilizando um SDK extinto será rejeitado pelo serviço.

> [!WARNING]
> Todas as versões do SDK para Java DocumentDB anteriores à versão **1.0.0** serão descontinuados no **29 de Fevereiro de 2016**.
> 
> 

<br/>

| Versão | Data da versão | Data de retirada |
| --- | --- | --- |
| [1.15.0](#1.15.0) |14 de Novembro de 2017 |--- |
| [1.14.0](#1.14.0) |28 de FPO de 2017 |--- |
| [1.13.0](#1.13.0) |25 de Agosto de 2017 |--- |
| [1.12.0](#1.12.0) |11 de Julho de 2017 |--- |
| [1.11.0](#1.11.0) |10 de maio de 2017 |--- |
| [1.10.0](#1.10.0) |11 de Março de 2017 |--- |
| [1.9.6](#1.9.6) |21 de Fevereiro de 2017 |--- |
| [1.9.5](#1.9.5) |31 de Janeiro de 2017 |--- |
| [1.9.4](#1.9.4) |24 de Novembro de 2016 |--- |
| [1.9.3](#1.9.3) |30 de Outubro de 2016 |--- |
| [1.9.2](#1.9.2) |28 de Outubro de 2016 |--- |
| [1.9.1](#1.9.1) |26 de Outubro de 2016 |--- |
| [1.9.0](#1.9.0) |03 de Outubro de 2016 |--- |
| [1.8.1](#1.8.1) |30 de Junho de 2016 |--- |
| [1.8.0](#1.8.0) |14 de Junho de 2016 |--- |
| [1.7.1](#1.7.1) |30 de Abril de 2016 |--- |
| [1.7.0](#1.7.0) |27 de Abril de 2016 |--- |
| [1.6.0](#1.6.0) |29 de Março de 2016 |--- |
| [1.5.1](#1.5.1) |31 de Dezembro de 2015 |--- |
| [1.5.0](#1.5.0) |04 de Dezembro de 2015 |--- |
| [1.4.0](#1.4.0) |05 de Outubro de 2015 |--- |
| [1.3.0](#1.3.0) |05 de Outubro de 2015 |--- |
| [1.2.0](#1.2.0) |05 de Agosto de 2015 |--- |
| [1.1.0](#1.1.0) |09 de Julho de 2015 |--- |
| [1.0.1](#1.0.1) |12 de Maio de 2015 |--- |
| [1.0.0](#1.0.0) |07 de Abril de 2015 |--- |
| 0.9.5-prelease |09 de mar de 2015 |29 de Fevereiro de 2016 |
| 0.9.4-prelease |17 de Fevereiro de 2015 |29 de Fevereiro de 2016 |
| 0.9.3-prelease |13 de Janeiro de 2015 |29 de Fevereiro de 2016 |
| 0.9.2-prelease |19 de Dezembro de 2014 |29 de Fevereiro de 2016 |
| 0.9.1-prelease |19 de Dezembro de 2014 |29 de Fevereiro de 2016 |
| 0.9.0-prelease |10 de Dezembro de 2014 |29 de Fevereiro de 2016 |

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Veja Também
Para saber mais sobre a base de dados do Cosmos, consulte [base de dados do Microsoft Azure Cosmos](https://azure.microsoft.com/services/cosmos-db/) página do serviço.

