---
title: Cosmos do Azure SDK .NET da DB & recursos | Microsoft Docs
description: "Saiba tudo sobre o SDK, incluindo as datas de versão, datas de extinção e as alterações efetuadas entre cada versão do SDK .NET da Azure Cosmos BD e .NET API."
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 8e239217-9085-49f5-b0a7-58d6e6b61949
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/10/2017
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2f32f16ae9559a057f9b39658e437aeedfd575c7
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="azure-cosmos-db-net-sdk-download-and-release-notes"></a>Cosmos do Azure SDK .NET da base de dados: Transferir e notas de versão
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

<tr><td>**Transferência do SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>

<tr><td>**Documentação da API**</td><td>[Documentação de referência da API de .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)</td></tr>

<tr><td>**Amostras**</td><td>[Exemplos de código do .NET](documentdb-dotnet-samples.md)</td></tr>

<tr><td>**Introdução**</td><td>[Introdução ao SDK .NET da Azure Cosmos DB](documentdb-get-started.md)</td></tr>

<tr><td>**Tutorial de aplicação Web**</td><td>[Desenvolvimento da aplicação Web com base de dados do Azure Cosmos](documentdb-dotnet-application.md)</td></tr>

<tr><td>**Arquitetura suportada atual**</td><td>[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## <a name="release-notes"></a>Notas de versão
### <a name="a-name11901190"></a><a name="1.19.0"/>1.19.0

* Documentação, informações de metadados em assemblagens e o pacote NuGet de imagem corporativa alteração do Azure DocumentDB à base de dados do Azure Cosmos de referência de API. 
* Expõe informações de diagnóstico e a latência da resposta de pedidos enviados com o modo de ligação direta. Os nomes de propriedade são RequestDiagnosticsString e RequestLatency na classe ResourceResponse.

### <a name="a-name11811181"></a><a name="1.18.1"/>1.18.1 

* Alterações internas para assemblagens de amigos de Microsoft.

### <a name="a-name11801180"></a><a name="1.18.0"/>1.18.0 

* Adicionar várias correções de fiabilidade e melhoramentos.

### <a name="a-name11701170"></a><a name="1.17.0"/>1.17.0 

* Suporte adicionado para PartitionKeyRangeId como um FeedOption para controlar o âmbito os resultados da consulta para um valor de intervalo de chaves de partição específica. 
* Suporte adicionado para StartTime como um ChangeFeedOption para começar a procurar para que as alterações após esse tempo.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Foi corrigido um problema na classe JsonSerializable que pode causar uma exceção de capacidade excedida da pilha.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
*   Foi corrigido um problema que é necessário recompilar da aplicação devido à introdução de JsonSerializerSettings como um parâmetro opcional no construtor DocumentClient.
* Marcado como o construtor de DocumentClient obsoleto esse JsonSerializerSettings necessário que o último parâmetro para permitir valores predefinidos dos ConnectionPolicy e ConsistencyLevel parâmetros ao transmitir parâmetro JsonSerializerSettings.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
*   Foi adicionado suporte para especificar JsonSerializerSettings personalizadas ao instanciar [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet).

### <a name="a-name11411141"></a><a name="1.14.1"/>1.14.1
*   Foi corrigido um problema que afetados x64 máquinas que não suportam instruções SSE4 e gerar um SEHException quando executar consultas de API de DocumentDB do Azure Cosmos DB.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
*   Suporte adicionado para um novo nível de consistência chamado ConsistentPrefix.
*   Adicionado suporte para as métricas de consulta para partições individuais.
*   Suporte adicionado para limitar o tamanho do token de continuação para consultas.
*   Suporte adicionado para o rastreio mais detalhado para pedidos falhados.
*   Efetuados alguns melhoramentos de desempenho no SDK.

### <a name="a-name11341134"></a><a name="1.13.4"/>1.13.4
* Funcionalmente igual 1.13.3. Efetuou algumas alterações internas.

### <a name="a-name11331133"></a><a name="1.13.3"/>1.13.3
* Funcionalmente igual 1.13.2. Efetuou algumas alterações internas.

### <a name="a-name11321132"></a><a name="1.13.2"/>1.13.2
* Foi corrigido um problema que ignorado o valor de PartitionKey fornecido FeedOptions para consultas de agregação.
* Foi corrigido um problema no processamento transparente de gestão de partição durante o voo intermédio partição cruzada Order By a execução da consulta.

### <a name="a-name11311131"></a><a name="1.13.1"/>1.13.1
* Foi corrigido um problema que causou a impasses em algumas do async APIs quando utilizada dentro do contexto do ASP.NET.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Correções para tornar mais resiliente do SDK para ativação pós-falha automática em determinadas condições.

### <a name="a-name11221122"></a><a name="1.12.2"/>1.12.2
* Corrigir um problema que ocasionalmente faz com que um WebException: não foi possível resolver o nome remoto.
* O suporte adicionado para diretamente ao ler um documento com tipo adicionando as sobrecargas da nova para ReadDocumentAsync API.

### <a name="a-name11211121"></a><a name="1.12.1"/>1.12.1
* Adicionado suporte LINQ para consultas de agregação (CONTAGEM, MIN, MAX, soma e média).
* Corrigir um problema de fuga de memória para o objeto de ConnectionPolicy causado pela utilização de processador de eventos.
* Corrigir um problema wherein UpsertAttachmentAsync foi não está a funcionar quando ETag foi utilizada.
* Corrigir um problema wherein continuação de partição cruzada por ordem consulta foi não funcionar ao ordenar no campo de cadeia.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Suporte adicionado para consultas de agregação (CONTAGEM, MIN, MAX, soma e média). Consulte [suporte de agregação](documentdb-sql-query.md#Aggregates).
* Lowered débito mínimo em coleções particionadas de 10,100 RU/s ao 2500 RU/s.

### <a name="a-name11141114"></a><a name="1.11.4"/>1.11.4
* Corrigir um problema wherein algumas das consultas de partição cruzada foram falha no processo de anfitrião de 32 bits.
* Corrigir um problema wherein o contentor de sessão não estava a ser atualizado com o token para pedidos falhados no modo de Gateway.
* Corrigir um problema wherein uma consulta com as chamadas UDF na projeção falhasse em alguns casos.
* Correções de desempenho do lado cliente para aumentar o débito de leitura e escrita dos pedidos.

### <a name="a-name11131113"></a><a name="1.11.3"/>1.11.3
* Corrigir um problema wherein o contentor de sessão não estava a ser atualizado com o token para pedidos falhados.
* Suporte adicionado para o SDK funcionar num processo anfitrião de 32 bits. Tenha em atenção que, se utilizar consultas de partição cruzada, anfitrião de 64 bits de processamento é recomendada para um melhor desempenho.
* Melhoria do desempenho para cenários que envolvem consultas com um grande número de valores de chave de partição numa expressão IN.
* Preenchido diversas estatísticas de quota de recursos no ResourceResponse para a coleção de documentos pedidos de leitura quando a opção de pedido de PopulateQuotaInfo está definida.

### <a name="a-name11111111"></a><a name="1.11.1"/>1.11.1
* Correção de desempenho secundária para a API de CreateDocumentCollectionIfNotExistsAsync introduzida no 1.11.0.
* Desempenho corrija o SDK para cenários que envolvem elevado grau de pedidos em simultâneo.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Suporte para novas classes e métodos para processar o [alterar feed](change-feed.md) de documentos numa coleção.
* Suporte para a consulta da partição cruzada continuação e alguns melhoramentos de desempenho para consultas de partição cruzada.
* Adição de métodos CreateDatabaseIfNotExistsAsync e CreateDocumentCollectionIfNotExistsAsync.
* Suporta LINQ para funções de sistema: IsDefined, IsNull e IsPrimitive.
* Corrigir para binplacing automática das assemblagens Microsoft.Azure.Documents.ServiceInterop.dll e DocumentDB.Spatial.Sql.dll para pasta de reciclagem da aplicação ao utilizar o pacote Nuget com projetos que tenham project.json ferramentas.
* Suporte para emitir os rastreios ETW do lado do cliente que pode ser útil em cenários de depuração.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Ligação direta adicionado suporte para coleções particionadas.
* Melhoria do desempenho para o nível de consistência vinculada tem um vínculo.
* Foram adicionadas polígono e tipos de dados de LineString ao especificar a coleção de indexação de política para consultas geográficos barreiras geográficas.
* LINQ suporte adicionado para StringEnumConverter, IsoDateTimeConverter e UnixDateTimeConverter ao traduzir a predicados.
* Várias correções de erros SDK.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Foi corrigido um problema que causou o NotFoundException seguinte: A sessão de leitura não está disponível para o token de sessão de entrada. Esta exceção ocorreu em alguns casos, quando consultar a região de leitura de uma conta geo-distribuição.
* Expostos a propriedade de ResponseStream na classe ResourceResponse, que permite acesso direto para o fluxo subjacente a partir de uma resposta.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Modificar as classes ResourceResponse, FeedResponse, StoredProcedureResponse e MediaResponse para implementar a interface pública correspondente para que pode ser mocked para teste orientadas por implementação (TDD).
* Foi corrigido um problema que causou a geração de um cabeçalho de chave de partição com formato incorreto ao utilizar um objeto de JsonSerializerSettings personalizado para serializar dados.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Foi corrigido um problema que causou a consultas de execução longa para falhar com o erro: token de autorização não é válido atualmente.
* Foi corrigido um problema que removeu o SqlParameterCollection original entre consultas de parte superior/ordem-por partição.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Suporte adicionado para consultas paralelas para coleções particionadas.
* Suporte adicionado para entre consultas de ORDER BY e parte superior de partição para coleções particionadas.
* Corrigir as referências em falta para DocumentDB.Spatial.Sql.dll e Microsoft.Azure.Documents.ServiceInterop.dll que são necessárias quando referenciar um projeto de base de dados do Azure Cosmos com uma referência ao pacote Nuget do Azure Cosmos DB.
* Corrigido a capacidade de utilizar os parâmetros de diferentes tipos, ao utilizar as funções definidas pelo utilizador no LINQ. 
* Fixo de erros para contas global replicadas onde Upsert chamadas foram ser direcionadas para localizações em vez de localizações de escrita de leitura.
* Foram adicionados métodos para a interface IDocumentClient que estavam em falta: 
  * Método de UpsertAttachmentAsync que assume mediaStream e opções como parâmetros
  * Método de CreateAttachmentAsync que aceita opções como parâmetro
  * Método de CreateOfferQuery que assume querySpec como parâmetro.
* Classes públicas não seladas que são expostas na interface de IDocumentClient.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* O suporte adicionado para contas de multirregião base de dados.
* Suporte adicionado para repetição nos pedidos otimizadas.  Utilizador pode personalizar o número de tentativas e o tempo de espera máximo ao configurar a propriedade ConnectionPolicy.RetryOptions.
* Adicionar uma nova interface de IDocumentClient que define as assinaturas de todos os métodos e propriedades de DocumenClient.  Como parte desta alteração, alterar também os métodos de extensão que criar IQueryable e IOrderedQueryable dos métodos na própria classe DocumentClient.
* Opção de configuração adicional para definir o ServicePoint.ConnectionLimit para um ponto de final de base de dados do Azure Cosmos especificado Uri.  Utilize ConnectionPolicy.MaxConnectionLimit para alterar o valor predefinido, que está definido como 50.
* IPartitionResolver preterida e a implementação.  Suporte para IPartitionResolver está obsoleto. É recomendado que utilize coleções Particionadas de armazenamento superior e débito.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Adicionar uma sobrecarga para o Uri com base em método ExecuteStoredProcedureAsync que demora RequestOptions como parâmetro.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Tempo adicionado ao suporte (TTL) em direto para documentos.

### <a name="a-name163163"></a><a name="1.6.3"/>1.6.3
* Corrigido um erro em empacotamento Nuget do .NET SDK de empacotamento como parte de uma solução de serviço de nuvem do Azure.

### <a name="a-name162162"></a><a name="1.6.2"/>1.6.2
* Implementado [particionada coleções](partition-data.md) e [níveis de desempenho definido pelo utilizador](performance-levels.md). 

### <a name="a-name153153"></a><a name="1.5.3"/>1.5.3
* **[Fixo]**  Emite de ponto final de consultar a base de dados de Cosmos do Azure: ' System.Net.Http.HttpRequestException: erro ao copiar o conteúdo para uma transmissão em fluxo '.

### <a name="a-name152152"></a><a name="1.5.2"/>1.5.2
* LINQ expandido, incluindo operadores novo para paginação, condicionais expressões de suporte e intervalo de comparação.
  * Tirar operador para ativar o comportamento de SELECIONE superior no LINQ
  * Operador de CompareTo para ativar comparações de cadeias de intervalo
  * Condicional (?) e unir operadores (?)
* **[Fixo]**  ArgumentOutOfRangeException ao combinar a projecção de modelo com Where-numa consulta LINQ. [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* **[Fixo]**  Se Select não é a última expressão o fornecedor LINQ considerados não projeção e produzidos SELECIONE * incorretamente.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Upsert implementado, métodos de UpsertXXXAsync adicionado
* Melhorias de desempenho para todos os pedidos
* Suporte de fornecedor LINQ para condicional, unir e os métodos de CompareTo para cadeias
* **[Fixo]**  Fornecedor LINQ--> contém a implementar o método na lista para gerar o mesmo SQL em IEnumerable e a matriz
* **[Fixo]**  BackoffRetryUtility utiliza o mesmo HttpRequestMessage novamente em vez de criar um novo, tente novamente
* **[Obsoleto]**  UriFactory.CreateCollection--> agora deve utilizar UriFactory.CreateDocumentCollection

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1
* **[Fixo]**  Localização problemas ao utilizar as informações de cultura de não en, como nl-NL, etc. 

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Adicionado com base no ID de encaminhamento
  * Para melhorar a construção de ligações de recursos com base no ID de programa auxiliar de UriFactory novo
  * Novo sobrecargas no DocumentClient para colocar no URI
* Foram adicionadas IsValid() e IsValidDetailed() no LINQ para geoespacial
* Suporte de fornecedor LINQ avançado:
  * **Bibliotecas** -Abs, Acos, Asin, Atan, limite, Cos, Exp, piso, registo, Log10, Pow, Round, início de sessão único, Sqrt, Tan, truncar
  * **Cadeia** -Concat, contém, EndsWith IndexOf, Count, ToLower, TrimStart, substituir, inverso, TrimEnd, StartsWith, subcadeia, ToUpper
  * **Matriz** -Concat, contém, contagem
  * **EM** operador

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Suporte adicionado para modificar as políticas de indexação.
  * Novo método de ReplaceDocumentCollectionAsync DocumentClient
  * Nova propriedade IndexTransformationProgress no ResourceResponse<T> para controlar o progresso da percentagem de alterações de política de índice
  * Está agora mutável DocumentCollection.IndexingPolicy
* Suporte adicionado para indexação geográficos e a consulta.
  * Novo Microsoft.Azure.Documents.Spatial espaço de nomes para serialização/anulação da serialização de tipos geográficos como ponto e polígono
  * Nova classe de SpatialIndex para indexação de dados de GeoJSON armazenados na base de dados do Cosmos
* **[Fixo]**  Consulta de SQL incorreto gerada a partir de uma expressão LINQ [#38](https://github.com/Azure/azure-documentdb-net/issues/38).

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Adicionar uma dependência no v5.0.7 newtonsoft.
* Efetuou alterações para suportar Order By:
  
  * Suporte de fornecedor LINQ para OrderBy() ou OrderByDescending()
  * IndexingPolicy para suportar Order By 
    
    **Alteração inovadora possíveis** 
    
    Se tiver código existente coleções que Aprovisiona com uma política de indexação personalizada, em seguida, o código existente tem de ser atualizadas para suportar a nova classe de IndexingPolicy. Não se tiver nenhuma política de indexação personalizada, em seguida, esta alteração não afeta a.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Suporte adicionado para criação de partições de dados utilizando as novas classes HashPartitionResolver e RangePartitionResolver e o IPartitionResolver.
* Serialização de DataContract adicionada.
* GUID adicionado suporte no fornecedor LINQ.
* Foram adicionada UDF suporta LINQ.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK

## <a name="release--retirement-dates"></a>Versão & extinção datas
A Microsoft disponibiliza notificação, pelo menos, **12 meses** previamente extinguir um SDK para smooth a transição para uma versão mais recente/suportado.

Novas funcionalidades e a funcionalidade e otimizações apenas são adicionadas ao SDK atual, como tal, recomenda-se que atualize sempre para a versão mais recente SDK como antecipadamente quanto possível. 

Todos os pedidos de BD do Cosmos do Azure utilizando um SDK extinto são rejeitados pelo serviço.

<br/>

| Versão | Data da versão | Data de retirada |
| --- | --- | --- |
| [1.19.0](#1.19.0) |10 de Novembro de 2017 |--- |
| [1.18.1](#1.18.1) |07 de Novembro de 2017 |--- |
| [1.18.0](#1.18.0) |17 de Outubro de 2017 |--- |
| [1.17.0](#1.17.0) |10 de Agosto de 2017 |--- |
| [1.16.1](#1.16.1) |07 de Agosto de 2017 |--- |
| [1.16.0](#1.16.0) |02 de Agosto de 2017 |--- |
| [1.15.0](#1.15.0) |30 de Junho de 2017 |--- |
| [1.14.1](#1.14.1) |23 de Maio de 2017 |--- |
| [1.14.0](#1.14.0) |10 de maio de 2017 |--- |
| [1.13.4](#1.13.4) |09 de Maio de 2017 |--- |
| [1.13.3](#1.13.3) |06 de Maio de 2017 |--- |
| [1.13.2](#1.13.2) |19 de Abril de 2017 |--- |
| [1.13.1](#1.13.1) |29 de Março de 2017 |--- |
| [1.13.0](#1.13.0) |24 de Março de 2017 |--- |
| [1.12.2](#1.12.2) |20 de março de 2017 |--- |
| [1.12.1](#1.12.1) |14 de março de 2017 |--- |
| [1.12.0](#1.12.0) |15 de Fevereiro de 2017 |--- |
| [1.11.4](#1.11.4) |06 de Fevereiro de 2017 |--- |
| [1.11.3](#1.11.3) |26 de Janeiro de 2017 |--- |
| [1.11.1](#1.11.1) |21 de Dezembro de 2016 |--- |
| [1.11.0](#1.11.0) |08 de Dezembro de 2016 |--- |
| [1.10.0](#1.10.0) |27 de Setembro de 2016 |--- |
| [1.9.5](#1.9.5) |01 de Setembro de 2016 |--- |
| [1.9.4](#1.9.4) |24 de Agosto de 2016 |--- |
| [1.9.3](#1.9.3) |15 de Agosto de 2016 |--- |
| [1.9.2](#1.9.2) |23 de Julho de 2016 |--- |
| [1.8.0](#1.8.0) |14 de Junho de 2016 |--- |
| [1.7.1](#1.7.1) |06 de Maio de 2016 |--- |
| [1.7.0](#1.7.0) |26 de Abril de 2016 |--- |
| [1.6.3](#1.6.3) |08 de Abril de 2016 |--- |
| [1.6.2](#1.6.2) |29 de Março de 2016 |--- |
| [1.5.3](#1.5.3) |19 de Fevereiro de 2016 |--- |
| [1.5.2](#1.5.2) |14 de Dezembro de 2015 |--- |
| [1.5.1](#1.5.1) |23 de Novembro de 2015 |--- |
| [1.5.0](#1.5.0) |05 de Outubro de 2015 |--- |
| [1.4.1](#1.4.1) |25 de Agosto de 2015 |--- |
| [1.4.0](#1.4.0) |13 de Agosto de 2015 |--- |
| [1.3.0](#1.3.0) |05 de Agosto de 2015 |--- |
| [1.2.0](#1.2.0) |06 de Julho de 2015 |--- |
| [1.1.0](#1.1.0) |30 de Abril de 2015 |--- |
| [1.0.0](#1.0.0) |08 de Abril de 2015 |--- |


## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consultar também
Para saber mais sobre a base de dados do Cosmos, consulte [base de dados do Microsoft Azure Cosmos](https://azure.microsoft.com/services/cosmos-db/) página do serviço. 

