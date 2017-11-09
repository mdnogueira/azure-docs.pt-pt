---
title: "Sugestões de desempenho - Azure Cosmos base de dados NoSQL | Microsoft Docs"
description: "Saiba mais opções de configuração de cliente para melhorar o desempenho da base de dados de base de dados do Azure Cosmos"
keywords: como melhorar o desempenho de base de dados
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 94ff155e-f9bc-488f-8c7a-5e7037091bb9
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: mimig
ms.openlocfilehash: a34c47efbe811bec2ebe2c43abb9516cdab492bd
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="performance-tips-for-azure-cosmos-db"></a>Sugestões de desempenho para a base de dados do Azure Cosmos
BD do Cosmos do Azure é um rápido e flexível base de dados distribuída que dimensiona de forma totalmente integrada com latência garantida e débito. Não é necessário efetuar alterações de arquitetura de principais ou escrever código complexo para dimensionar a base de dados com base de dados do Cosmos. Aumentar e reduzir verticalmente é tão fácil como fazer uma única chamada API ou [a chamada de método SDK](set-throughput.md#set-throughput-sdk). No entanto, porque a BD do Cosmos é acedida através de chamadas de rede existem otimizações de lado do cliente que pode efetuar para alcançar um desempenho das horas de ponta.

Para que o se estiver a pedir "como posso melhorar o meu desempenho de base de dados?" Considere as seguintes opções:

## <a name="networking"></a>Redes
<a id="direct-connection"></a>

1. **Política de ligação: utilizar o modo de ligação direta**

    Como um cliente liga à base de dados do Cosmos tem implicações importantes no desempenho, especialmente em termos de latência observado do lado do cliente. Existem duas definições de configuração da chave para configurar a política de ligação – a ligação de cliente *modo* e [ligação *protocolo*](#connection-protocol).  Os dois modos de disponíveis são:

   1. Modo de gateway (predefinição)
   2. Modo direto

      O modo de gateway é suportado em todas as plataformas do SDK e é a predefinição configurada.  Se a aplicação é executado dentro de uma rede empresarial com restrições de strict firewall, o modo de Gateway é a melhor opção uma vez que utiliza a porta HTTPS padrão e um único ponto final. O compromisso de desempenho, no entanto, é o modo de Gateway envolve um salto de rede adicionais, sempre que os dados são de leitura ou escrita na base de dados do Cosmos. Por este motivo, o modo direto oferece um melhor desempenho devido a menos saltos de rede.
<a id="use-tcp"></a>
2. **Política de ligação: utilizar o protocolo TCP**

    Quando utilizar o modo direto, existem duas opções de protocolo disponíveis:

   * TCP
   * HTTPS

     BD do cosmos oferece uma simples e abra o modelo de programação RESTful através de HTTPS. Além disso, oferece um protocolo TCP eficiente, que também é RESTful no seu modelo de comunicação e está disponível através do SDK do cliente do .NET. TCP direta e HTTPS utilizem SSL para autenticação inicial e o tráfego de encriptação. Para melhor desempenho, utilize o protocolo TCP sempre que possível.

     Quando o TCP no modo de Gateway, TCP 443 de porta é a porta de BD do Cosmos e 10255 é a porta de API do MongoDB. Quando utilizar TCP no modo direto, além das portas de Gateway, tem de garantir a porta está aberto intervalo entre 10000 e 20000 porque a BD do Cosmos utiliza as portas TCP dinâmicas. Se estas portas não estão abertas e tentar utilizar TCP, receberá um erro 503 de serviço indisponível.

     O modo de conectividade é configurado durante a construção da instância com o parâmetro ConnectionPolicy DocumentClient. Se for utilizado modo direto, o protocolo também pode ser definido no parâmetro ConnectionPolicy.

    ```C#
    var serviceEndpoint = new Uri("https://contoso.documents.net");
    var authKey = new "your authKey from the Azure portal";
    DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
    new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp
    });
    ```

    Uma vez TCP só é suportado no modo direto, se o modo de Gateway é utilizado, em seguida, o protocolo HTTPS é sempre utilizado para comunicar com o Gateway e o valor de protocolo no ConnectionPolicy é ignorado.

    ![Ilustração da política de ligação de base de dados do Azure Cosmos](./media/performance-tips/connection-policy.png)

3. **Chamar OpenAsync para evitar a latência de arranque no primeiro pedido**

    Por predefinição, o primeiro pedido tem uma latência superior porque tem de obter a tabela de encaminhamento de endereço. Para evitar esta latência de arranque no pedido primeiro, deve chamar OpenAsync() uma vez durante a inicialização da seguinte forma.

        await client.OpenAsync();
   <a id="same-region"></a>
4. **Colocar os clientes na mesma região do Azure para desempenho**

    Sempre que possível, coloque todas as aplicações a chamar base de dados do Cosmos na mesma região que a base de dados de base de dados do Cosmos. Para uma comparação aproximada, as chamadas à base de dados do Cosmos na mesma região concluída dentro do ms 1-2, mas a latência entre o oeste e da Costa Leste dos E.U.A. é > 50 ms. Esta latência pode variar provavelmente de um pedido para o pedido, consoante a rota colocada por pedido, conforme tenha sido enviado a partir do cliente para os limites de datacenter do Azure. A menor latência possível é conseguida ao garantir que a aplicação de chamada está localizada na mesma região do Azure como o aprovisionamento ponto final da BD do Cosmos. Para obter uma lista de regiões disponíveis, consulte [regiões do Azure](https://azure.microsoft.com/regions/#services).

    ![Ilustração da política de ligação de base de dados do Azure Cosmos](./media/performance-tips/same-region.png)
   <a id="increase-threads"></a>
5. **Aumentar o número de threads/tarefas**

    Uma vez que as chamadas à base de dados do Azure Cosmos são efetuadas através da rede, poderá ter de variar o grau de paralelismo dos seus pedidos de modo a que a aplicação cliente despende pouco tempo à espera entre pedidos. Por exemplo, se estiver a utilizar. Do NET [biblioteca de tarefas paralelas](https://msdn.microsoft.com//library/dd460717.aspx), criar, por ordem de 100s das tarefas de leitura ou escrita de BD do Cosmos.

## <a name="sdk-usage"></a>Utilização do SDK
1. **Instalar o SDK mais recente**

    Os SDKs de BD do Cosmos estão constantemente a ser melhorados para proporcionar o melhor desempenho. Consulte o [Cosmos DB SDK](documentdb-sdk-dotnet.md) páginas para determinar o SDK mais recente e reveja melhoramentos.
2. **Utilizar um cliente de base de dados do Cosmos singleton para a duração da sua aplicação**

    Tenha em atenção que cada instância de DocumentClient seguro para thread e efetua a gestão de ligações eficiente e endereço a colocação em cache quando funcionar no modo direto. Para permitir a gestão de ligações eficiente e melhor desempenho por DocumentClient, é recomendado utilizar uma única instância DocumentClient por AppDomain para a duração da aplicação.

   <a id="max-connection"></a>
3. **Aumentar System.Net MaxConnections por anfitrião, ao utilizar o modo de Gateway**

    São efetuados os pedidos de cosmos DB através de HTTPS/REST ao utilizar o modo de Gateway e estão sujeitos ao limite de ligação predefinido por nome de anfitrião ou endereço IP. Terá de definir o MaxConnections para um valor mais alto (100-1000) para que a biblioteca de clientes pode utilizar várias ligações simultâneas a BD do Cosmos. O SDK .NET 1.8.0 em acima, o valor predefinido para [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) 50 e para alterar o valor, pode definir o [Documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx)para um valor superior.   
4. **Otimizar as consultas paralelas para coleções particionadas**

     SDK do .NET DocumentDB versão 1.9.0 e acima consultas paralelas de suporte, o que lhe permite consultar uma coleção particionada em paralelo (consulte [trabalhar com os SDKs](documentdb-partition-data.md#working-with-the-azure-cosmos-db-sdks) o relacionados e [exemplos de código](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) para obter mais informações Info). Consultas paralelas são concebidas para melhorar a latência de consulta e débito ao longo do respetivo homólogo série. Consultas paralelas fornecem dois parâmetros que os utilizadores podem otimizar para personalizada ajuste os respetivos requisitos, (a) MaxDegreeOfParallelism: para controlar o número máximo de partições, em seguida, pode ser consultado em paralelo e (b) MaxBufferedItemCount: para controlar o número de resultados de pré-obtidos.

    (a) ***otimização MaxDegreeOfParallelism\:***  paralela consulta funciona consultando várias partições em paralelo. No entanto, os dados a partir de uma recolha particionada individuais são obtidos serialmente no que respeita à consulta. Assim, definir o MaxDegreeOfParallelism para o número de partições é a probabilidade de máxima de alcançar a maioria dos consulta performant, fornecido todas as outras condições de sistema permanecerem o mesmo. Se não souber o número de partições, pode definir o MaxDegreeOfParallelism para um número elevado e o sistema escolhe o mínimo (número de partições, entrada fornecido pelo utilizador) como o MaxDegreeOfParallelism.

    É importante ter em atenção as consultas paralelas produzem as vantagens de melhor, se os dados é distribuída uniformemente pelos todas as partições no que respeita à consulta. Se a coleção particionada está particionada uma forma que todos os ou a maioria dos dados devolvidos por uma consulta é concentrated em partições uns (uma partição na pior das hipóteses), em seguida, o desempenho da consulta deverá ser condicionada por essas partições apoio.

    (b) ***otimização MaxBufferedItemCount\:***  paralela da consulta foi concebida para previamente obtenha os resultados enquanto o lote de resultados atual está a ser processado pelo cliente. A pré-obtenção de ajuda na melhoria geral de latência de uma consulta. MaxBufferedItemCount é o parâmetro para limitar o número de resultados de pré-obtidos. Definição MaxBufferedItemCount para o número esperado de resultados devolvidos (ou um número mais alto) permite a consulta para recebem benefícios máximo da pré-a obter.

    Tenha em atenção que previamente obter funciona da mesma forma, independentemente de MaxDegreeOfParallelism e existe uma única memória intermédia de dados de todas as partições.  
5. **Ativar o GC do lado do servidor**

    Pode ajudar a reduzir a frequência de recolha de lixo em alguns casos. No .NET, defina [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) como true.
6. **Implementar o término intervalos RetryAfter**

    Durante os testes de desempenho, deve aumentar a carga até uma pequena taxa de pedidos de obter limitadas. Se limitadas, a aplicação cliente deve término no limitação para o intervalo entre tentativas de servidor especificado. Respecting o término garante que passam a quantidade mínima de espera de tempo entre tentativas. Suporte de política de repetição está incluído na versão 1.8.0 e acima do DocumentDB [.NET](documentdb-sdk-dotnet.md) e [Java](documentdb-sdk-java.md), versão 1.9.0 e acima do [Node.js](documentdb-sdk-node.md) e [Python ](documentdb-sdk-python.md), e todas as versões do [.NET Core](documentdb-sdk-dotnet-core.md) SDKs. Para obter mais informações, consulte [Exceeding reservado limites de débito](request-units.md#RequestRateTooLarge) e [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
7. **Aumentar horizontalmente a cliente carga de trabalho**

    Se estiver a testar em níveis de débito elevado (> 50.000 RU/s), a aplicação cliente pode tornar-se a engarrafamento devido a máquina capping limite na utilização de CPU ou à rede. Se atingir este ponto, pode continuar a emitir a conta de base de dados do Cosmos ainda mais ao aumentar horizontalmente as aplicações de cliente por vários servidores.
8. **Documento de cache de URI para menor latência de leitura**

    Documento de cache de URI sempre que possível para o melhor desempenho de leitura.
   <a id="tune-page-size"></a>
9. **Ajustar o tamanho da página de feeds de consultas/leitura para um melhor desempenho**

    Quando efetuar uma em massa de leitura de documentos utilizando leitura feed funcionalidade (por exemplo, ReadDocumentFeedAsync) ou ao emitir uma consulta de SQL do DocumentDB, os resultados são devolvidos de uma forma segmentada se o conjunto de resultados for demasiado grande. Por predefinição, os resultados são devolvidos em segmentos de 100 itens ou de 1 MB, consoante o limite é atingido primeiro.

    Para reduzir o número de rede arredondar viagens necessárias para obter todos os resultados aplicáveis, pode aumentar o tamanho de página utilizando o cabeçalho de pedido x-ms-máx.--número de itens a até 1000. Nos casos em que seja necessário apresentar apenas alguns resultados, por exemplo, se a API de aplicação ou interface de utilizador devolve apenas os 10 resultados de uma hora, também pode diminuir o tamanho da página para 10 para reduzir o débito consumido para leituras e consultas.

    Também pode definir o tamanho de página com os SDKs de base de dados disponíveis do Cosmos.  Por exemplo:

        IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
10. **Aumentar o número de threads/tarefas**

    Consulte [aumentar o número de threads/tarefas](#increase-threads) na secção de redes.

11. **Utilize o processamento de anfitrião de 64 bits**

    O SDK DocumentDB funciona num processo anfitrião de 32 bits quando estiver a utilizar o SDK do .NET DocumentDB versão 1.11.4 e acima. No entanto, se estiver a utilizar consultas de partição cruzada, anfitrião de 64 bits de processamento é recomendada para um melhor desempenho. Os seguintes tipos de aplicações têm processo anfitrião de 32 bits como predefinição, pelo que, para alterar que para 64 bits, siga estes passos com base no tipo de aplicação:

    - Para aplicações executável, isto pode ser feito desmarcando a **preferir 32-bit** opção o **propriedades do projeto** janela, no **criar** separador.

    - Para VSTest baseada em projetos de teste, isto pode ser efetuado selecionando **testar**->**testar definições**->**arquitectura de processador predefinida como X64**, do **teste do Visual Studio** opção do menu.

    - Para aplicações de ASP.NET Web implementadas localmente, isto pode ser feito ao verificar o **utiliza a versão de 64 bits do IIS Express para web sites e projetos**, em **ferramentas**->**opções**  -> **Projetos e soluções**->**Web projetos**.

    - Para aplicações de ASP.NET Web implementadas no Azure, isto pode ser feito ao escolher o **plataforma como 64-bit** no **definições da aplicação** no portal do Azure.

## <a name="indexing-policy"></a>Política de indexação
 
1. **Excluir os caminhos de indexação para escritas mais rápidas**

    Política de indexação do cosmos da base de dados também permite-lhe especificar os caminhos do documento para incluir ou excluir da indexação tirando partido dos caminhos de indexação (IndexingPolicy.IncludedPaths e IndexingPolicy.ExcludedPaths). A utilização de indexação caminhos pode oferecer desempenho melhorado de escrita e de armazenamento de índice inferior para cenários em que os padrões de consulta são conhecidos com antecedência, os custos de indexação diretamente são correlacionados com o número exclusivos caminhos indexados.  Por exemplo, o código seguinte mostra como excluir uma secção completa dos documentos (a.k.a. uma subárvore) de indexação a utilizar o "*" caráter universal.

    ```C#
    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
    ```

    Para obter mais informações, consulte [Azure Cosmos DB indexação políticas](indexing-policies.md).

## <a name="throughput"></a>Débito
<a id="measure-rus"></a>

1. **Medir e otimizar o pedido inferior unidades por segundo utilização**

    BD do cosmos oferece um vasto conjunto de operações de base de dados, incluindo consultas relacionais e hierárquicas com tudo funcionar em documentos de uma coleção de base de dados – acionadores, UDFs e procedimentos armazenados. Os custos associados a cada uma destas operações variam consoante a CPU, a e/s e a memória necessária para concluir a operação. Em vez de ter em consideração e a gerir recursos de hardware, pode considerar uma unidade de pedido (RU) como uma medida único para os recursos necessários para desempenhar várias operações de base de dados e um pedido de aplicação de serviço.

    Débito aprovisionado com base na quantidade de [unidades de pedido](request-units.md) definido para cada contentor. Consumo de unidade de pedido é avaliado como uma taxa por segundo. Aplicações que excedem a taxa de pedidos de aprovisionamento de unidade para a conta é limitada até que a taxa de ignora abaixo do nível reservado para a conta. Se a sua aplicação requer um nível mais elevado de débito, pode aumentar o débito ao aprovisionamento unidades de pedido adicionais. 

    A complexidade de uma consulta afeta o número de unidades de pedido são consumidas para uma operação. O número de predicados, natureza os predicados, número de UDFs e o tamanho do conjunto de dados de origem todos os influenciar o custo das operações de consulta.

    Para medir a sobrecarga de qualquer operação (criar, atualizar ou eliminar), Inspecione o cabeçalho x-ms-pedido-encargos (ou a propriedade RequestCharge equivalente no ResourceResponse<T> ou FeedResponse<T> no SDK do .NET) para medir o número de unidades de pedido consumidas por estas operações.

    ```C#
    // Measure the performance (request units) of writes
    ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
    Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
    // Measure the performance (request units) of queries
    IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
    while (queryable.HasMoreResults)
         {
              FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
              Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
         }
    ```             

    A taxa de pedido devolvida neste cabeçalho é fração do seu débito aprovisionado (ou seja, de 2000 RUs / segundo). Por exemplo, se a consulta anterior devolve 1000 1KB-documentos, o custo da operação é 1000. Como tal, dentro de um segundo, o servidor honra apenas dois esses pedidos antes dos pedidos subsequentes de limitação. Para obter mais informações, consulte [unidades de pedido](request-units.md) e [Calculadora de unidade de pedido](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Taxa de pedidos/limitação de taxa de identificador demasiado grande**

    Quando um cliente tenta exceder o débito reservado para uma conta, há sem degradação do desempenho no servidor e sem utilização de capacidade de débito para além do nível reservado. O servidor irá preventivamente terminar o pedido com RequestRateTooLarge (código de estado HTTP 429) e devolver o cabeçalho x-ms-repetição-após-ms que indica a quantidade de tempo, em milissegundos, que o utilizador terá de aguardar antes de reattempting o pedido.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Os SDKs implicitamente todos os catch esta resposta, respeitem o servidor especificado depois de repetir cabeçalho e repita o pedido. A menos que a sua conta está a ser acedida em simultâneo por vários clientes, a tentativa seguinte ocorrerá será bem sucedida.

    Se tiver mais do que um cliente cumulativamente e operativo consistentemente acima a taxa de pedidos, a contagem de repetições de predefinição atualmente definida para 9 internamente pelo cliente não poderá suffice; Neste caso, o cliente emite um DocumentClientException com o código de estado 429 à aplicação. A contagem de repetições predefinido pode ser alterada ao definir o RetryOptions na instância ConnectionPolicy. Por predefinição, o DocumentClientException com o código de estado 429 é devolvido após um tempo cumulativo de espera de 30 segundos, se o pedido continua a funcionar acima a taxa de pedidos. Isto ocorre mesmo quando o número de tentativas atual é menor que o número máximo de tentativas, seja a predefinição de 9 ou um valor definido pelo utilizador.

    Enquanto o comportamento de repetição automatizada ajuda a melhorar a resiliência e a facilidade de utilização para a maioria das aplicações, poderá ser at odds ao efetuar testes de desempenho, especialmente quando medir a latência.  A latência observados de cliente será aumentam se a experimentação pedidos com êxito a limitação de servidor e faz com que o cliente SDK para repetir automaticamente. Para evitar picos de latência durante experimentações de desempenho, medir a taxa devolvida por cada operação e certifique-se de que pedidos estão a funcionar abaixo a taxa de pedidos reservado. Para obter mais informações, consulte [unidades de pedido](request-units.md).
3. **Conceção para documentos mais pequenos para maior débito**

    A taxa de pedido (ou seja, o custo de processamento de pedidos) de uma operação fornecida é diretamente correlacionada com o tamanho do documento. Operações de grande documentos custos mais de operações para documentos pequenos.

## <a name="next-steps"></a>Passos seguintes
Para uma aplicação de exemplo utilizada para avaliar a BD do Cosmos para cenários de elevado desempenho em alguns computadores de cliente, consulte [desempenho e dimensionamento de teste com base de dados do Cosmos](performance-testing.md).

Além disso, para saber mais sobre como criar a sua aplicação para o dimensionamento e elevado desempenho, consulte o artigo [divisão em partições e o dimensionamento do BD Azure Cosmos](partition-data.md).
