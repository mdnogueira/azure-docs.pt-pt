---
title: Azure Cosmos DB .NET Core API, SDK & recursos | Microsoft Docs
description: "Saiba tudo sobre a API do .NET Core e o SDK, incluindo as datas de versão, datas de extinção e as alterações efetuadas entre cada versão do Cosmos DB SDK .NET da Azure Core."
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: f899b314-26ac-4ddb-86b2-bfdf05c2abf2
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/10/2017
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4520b9ea730d2be7d30d946a8cbc7437c5be7899
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="azure-cosmos-db-net-core-sdk-release-notes-and-resources"></a>Azure Cosmos DB .NET Core SDK: Notas de versão e recursos
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

<tr><td>**Transferência do SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)</td></tr>

<tr><td>**Documentação da API**</td><td>[Documentação de referência da API de .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)</td></tr>

<tr><td>**Amostras**</td><td>[Exemplos de código do .NET](documentdb-dotnet-samples.md)</td></tr>

<tr><td>**Introdução**</td><td>[Introdução ao Azure Cosmos DB .NET Core SDK](documentdb-dotnetcore-get-started.md)</td></tr>

<tr><td>**Tutorial de aplicação Web**</td><td>[Desenvolvimento da aplicação Web com base de dados do Azure Cosmos](documentdb-dotnet-application.md)</td></tr>

<tr><td>**Arquitetura suportada atual**</td><td>[.NET 1.6 padrão e .NET 1.5 padrão](https://www.nuget.org/packages/NETStandard.Library)</td></tr>
</table></br>

## <a name="release-notes"></a>Notas de Versão

Cosmos DB SDK .NET da Azure Core tem paridade de funcionalidades com a versão mais recente do [SDK .NET da Azure Cosmos DB](documentdb-sdk-dotnet.md).

> [!NOTE] 
> O Azure Cosmos DB .NET Core SDK ainda não é compatível com aplicações da plataforma Universal do Windows (UWP). Se estiver interessado em .NET Core SDK que suportam aplicações UWP, enviar correio eletrónico para [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com).

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
 
 * Documentação, informações de metadados em assemblagens e o pacote NuGet de imagem corporativa alteração do Azure DocumentDB à base de dados do Azure Cosmos de referência de API. 
 * Expõe informações de diagnóstico e a latência da resposta de pedidos enviados com o modo de ligação direta. Os nomes de propriedade são RequestDiagnosticsString e RequestLatency na classe ResourceResponse.
 
### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* Adicionar várias correções de fiabilidade e melhoramentos.

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1 

* Internas alterações relacionadas com suporte [Microsoft.Azure.Graphs](https://docs.microsoft.com/en-us/azure/cosmos-db/graph-sdk-dotnet)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0 

* Suporte adicionado para PartitionKeyRangeId como um FeedOption para controlar o âmbito os resultados da consulta para um valor de intervalo de chaves de partição específica. 
* Suporte adicionado para StartTime como um ChangeFeedOption para começar a procurar para que as alterações após esse tempo. 

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

*   Foi corrigido um problema na classe JsonSerializable que pode causar uma exceção de capacidade excedida da pilha.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

*   Foi adicionado suporte para especificar JsonSerializerSettings personalizadas ao instanciar um [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet) instância.

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2

*   Suporte 1.5 padrão de .NET como uma das estruturas de destino.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1

*   Foi corrigido um problema que afetados x64 máquinas que não suportam instruções SSE4 e throw SEHException quando executar consultas de base de dados do Azure Cosmos.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

*   Suporte adicionado para um novo nível de consistência chamado ConsistentPrefix.
*   Adicionado suporte para as métricas de consulta para partições individuais.
*   Suporte adicionado para limitar o tamanho do token de continuação para consultas.
*   Suporte adicionado para o rastreio mais detalhado para pedidos falhados.
*   Efetuados alguns melhoramentos de desempenho no SDK.

### <a name="a-name122122"></a><a name="1.2.2"/>1.2.2

* Foi corrigido um problema que ignorado o valor de PartitionKey fornecido FeedOptions para consultas de agregação.
* Foi corrigido um problema no processamento transparente de gestão de partição durante o voo intermédio partição cruzada Order By a execução da consulta.

### <a name="a-name121121"></a><a name="1.2.1"/>1.2.1

* Foi corrigido um problema que causou a impasses em algumas do async APIs quando utilizada dentro do contexto do ASP.NET.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Correções para tornar mais resiliente do SDK para ativação pós-falha automática em determinadas condições.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* Corrigir um problema que ocasionalmente faz com que um WebException: não foi possível resolver o nome remoto.
* O suporte adicionado para diretamente ao ler um documento com tipo adicionando as sobrecargas da nova para ReadDocumentAsync API.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Adicionado suporte LINQ para consultas de agregação (CONTAGEM, MIN, MAX, soma e média).
* Corrigir um problema de fuga de memória para o objeto de ConnectionPolicy causado pela utilização de processador de eventos.
* Corrigir um problema wherein UpsertAttachmentAsync foi não está a funcionar quando ETag foi utilizada.
* Corrigir um problema wherein continuação de partição cruzada por ordem consulta foi não funcionar ao ordenar no campo de cadeia.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Suporte adicionado para consultas de agregação (CONTAGEM, MIN, MAX, soma e média). Consulte [suporte de agregação](documentdb-sql-query.md#Aggregates).
* Lowered débito mínimo em coleções particionadas de 10,100 RU/s ao 2500 RU/s.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

Cosmos DB SDK .NET da Azure Core permite-lhe criar rapidamente, plataforma [ASP.NET Core](https://www.asp.net/core) e [.NET Core](https://www.microsoft.com/net/core#windows) aplicações sejam executadas no Windows, Mac e Linux. A versão mais recente do Azure Cosmos DB .NET Core SDK é totalmente [Xamarin](https://www.xamarin.com) compatível e ser utilizado para criar aplicações direcionadas para iOS, Android e Mono (Linux).  

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-Preview

Cosmos DB SDK .NET da Azure Core pré-visualização permite-lhe criar rapidamente, plataforma [ASP.NET Core](https://www.asp.net/core) e [.NET Core](https://www.microsoft.com/net/core#windows) aplicações sejam executadas no Windows, Mac e Linux.

Cosmos DB SDK .NET da Azure Core pré-visualização tem paridade de funcionalidades com a versão mais recente do [SDK .NET da Azure Cosmos DB](documentdb-sdk-dotnet.md) e suporta o seguinte:
* Todos os [modos de ligação](performance-tips.md#networking): modo, o TCP direta e o HTTPs direta de Gateway. 
* Todos os [níveis de consistência](consistency-levels.md): forte, sessão, consistência vinculada e Eventual.
* [Coleções de partições](partition-data.md). 
* [Contas de base de dados de multirregião e georreplicação](distribute-data-globally.md).

Se tiver perguntas relacionadas com este SDK, publicar [StackOverflow](http://stackoverflow.com/questions/tagged/azure-documentdb), ou um problema de ficheiros a [repositório do github](https://github.com/Azure/azure-documentdb-dotnet/issues). 

## <a name="release--retirement-dates"></a>Datas de extinção & versão

| Versão | Data da versão | Data de retirada |
| --- | --- | --- |
| [1.7.0](#1.7.0) |10 de Novembro de 2017 |--- |
| [1.6.0](#1.6.0) |17 de Outubro de 2017 |--- |
| [1.5.1](#1.5.1) |02 de Outubro de 2017 |--- |
| [1.5.0](#1.5.0) |10 de Agosto de 2017 |--- | 
| [1.4.1](#1.4.1) |07 de Agosto de 2017 |--- |
| [1.4.0](#1.4.0) |02 de Agosto de 2017 |--- |
| [1.3.2](#1.3.2) |12 de junho de 2017 |--- |
| [1.3.1](#1.3.1) |23 de Maio de 2017 |--- |
| [1.3.0](#1.3.0) |10 de maio de 2017 |--- |
| [1.2.2](#1.2.2) |19 de Abril de 2017 |--- |
| [1.2.1](#1.2.1) |29 de Março de 2017 |--- |
| [1.2.0](#1.2.0) |25 de março de 2017 |--- |
| [1.1.2](#1.1.2) |20 de março de 2017 |--- |
| [1.1.1](#1.1.1) |14 de março de 2017 |--- |
| [1.1.0](#1.1.0) |16 de Fevereiro de 2017 |--- |
| [1.0.0](#1.0.0) |21 de Dezembro de 2016 |--- |
| [0.1.0-Preview](#0.1.0-preview) |15 de Novembro de 2016 |31 de Dezembro de 2016 |

## <a name="see-also"></a>Veja Também
Para saber mais sobre a base de dados do Cosmos, consulte [base de dados do Microsoft Azure Cosmos](https://azure.microsoft.com/services/cosmos-db/) página do serviço. 

