---
title: "Considerações do Azure de desempenho e a otimização de pesquisa | Microsoft Docs"
description: Otimizar o desempenho da Azure Search e configure a escala ideal
services: search
documentationcenter: 
author: LiamCavanagh
manager: pablocas
editor: 
ms.assetid: 4d3cd864-29d2-4921-be0d-a3f1a819de46
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: liamca
ms.openlocfilehash: f4e371fc16bc57e6963f1ec51c0ea864fa568f0c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-search-performance-and-optimization-considerations"></a>Considerações do Azure de desempenho e a otimização de pesquisa
Uma experiência de pesquisa excelente é uma chave para êxito Mobile muitos e aplicações web. Da propriedade real, para utilizar marketplaces carro para catálogos de mercados online, pesquisa rápida e resultados relevantes irão afetar a experiência do cliente. Este documento destina-se a ajuda a detetar as melhores práticas tirar o máximo partido da Azure Search, especialmente para cenários avançados com requisitos sofisticados para escalabilidade, multilingues suportam ou classificação personalizada.  Além disso, este documento descreve as características e abrange abordagens funcionem eficazmente nas aplicações de cliente do mundo real.

## <a name="performance-and-scale-tuning-for-search-services"></a>Desempenho e a otimização de escala para serviços de pesquisa
Iremos são todos utilizados para procurar motores como o Bing e Google e elevado desempenho oferecem.  Como resultado, quando os clientes utilizam a sua pesquisa ativada web ou de aplicações móveis, irá esperar características de desempenho semelhante.  Quando a otimização de desempenho de pesquisa, uma das abordagens melhor é focar-se na latência, que é o tempo de que uma consulta demora a concluir e devolver resultados.  Quando a otimizar a latência de pesquisa é importante:

1. Escolha uma latência de destino (ou a quantidade máxima de tempo) que pedem uma pesquisa normal deve efetuar para concluir.
2. Criar e testar uma carga de trabalho real relativamente ao seu serviço de pesquisa com um conjunto de dados realista para medir as taxas de latência.
3. Começar com um número reduzido de consultas por segundo (QPS) e continuar para aumentar o número de executar o teste até que a latência de consulta descerem abaixo a latência de destino definidos.  Este é um benchmark importante para o ajudar a planear para escala conforme a sua aplicação cresce em utilização.
4. Sempre que possível, reutilize ligações HTTP.  Se estiver a utilizar o SDK .NET da Azure Search, isto significa que deve reutilizar uma instância ou [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) instância, e se estiver a utilizar a API REST, deve reutilizar um HttpClient único.

Ao criar estas cargas de trabalho de teste, existem algumas características da Azure Search a lembrar:

1. É possível push para que pesquisa várias consultas em simultâneo, que irão ser overwhelmed os recursos disponíveis no serviço da Azure Search.  Quando isto acontecer, verá os códigos de resposta de HTTP 503.  Por este motivo, é melhor começar a utilizar vários intervalos de pedidos de pesquisa para ver as diferenças no taxas de latência à medida que adiciona mais pedidos de pesquisa.
2. O carregamento de conteúdo para a Azure Search irá afetar o desempenho global e a latência do serviço da Azure Search.  Se pretende enviar dados enquanto os utilizadores estão a efetuar pesquisas, é importante ter em consideração esta carga de trabalho nos testes.
3. Nem todas as consultas de pesquisa irão efetuar nos mesmos níveis de desempenho.  Por exemplo, uma sugestão de pesquisa ou a procura de documentos, normalmente, irá efetuar mais rapidamente do que uma consulta com um número significativo de facetas e os filtros.  É melhor efetuar várias consultas que esperava em consideração quando criar os seus testes.  
4. Variação de pedidos de pesquisa é importante porque se continuamente a executar os mesmo pedidos de pesquisa, colocação em cache de dados será iniciado tornar o desempenho parecer melhor a defini-lo poderá com uma consulta mais diferentes.

> [!NOTE]
> [Testar e carga do Visual Studio](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) é uma realmente boa forma de efetuar o benchmark testa como permite-lhe executar pedidos HTTP quantos são necessários para executar consultas em relação a Azure Search e permite parallelization de pedidos.
> 
> 

## <a name="scaling-azure-search-for-high-query-rates-and-throttled-requests"></a>Dimensionamento da Azure Search para taxas de consulta elevado e limitadas pedidos
Quando está a receber demasiados pedidos otimizados ou exceder as taxas de latência de destino de uma carga maior, pode ver a diminuir as taxas de latência de uma das seguintes formas:

1. **Réplicas de aumento:** uma réplica é como uma cópia dos seus dados, permitindo a Azure Search carregar balancear pedidos contra as múltiplas cópias.  Todos os balanceamento de carga e replicação dos dados nas réplicas é gerida pelo Azure Search e pode alterar o número de réplicas alocado para o seu serviço em qualquer altura.  Pode atribuir até 12 réplicas num serviço de pesquisa padrão e 3 réplicas num serviço de pesquisa básica. Réplicas podem ser ajustado a partir de [portal do Azure](search-create-service-portal.md) ou [PowerShell](search-manage-powershell.md).
2. **Camada de pesquisa de aumento:** a Azure Search é apresentada num [número de camadas](https://azure.microsoft.com/pricing/details/search/) e cada uma destas camadas oferece diferentes níveis de desempenho.  Em alguns casos, poderá ter consultas tantas que a camada que está em não é possível fornecer taxas de latência baixa suficientemente, mesmo quando as réplicas são ser excessivamente utilizadas enviados.  Neste caso, poderá pretender considerar a tirar partido de um dos escalões de pesquisa superiores, como o escalão de S3 de pesquisa do Azure é também adequada para cenários com grandes quantidades de documentos e consultas extremamente elevado cargas de trabalho.

## <a name="scaling-azure-search-for-slow-individual-queries"></a>Dimensionamento da Azure Search para consultas individuais lentas
Outro motivo por que razão as taxas de latência podem ser lentas é de uma única consulta demorar demasiado tempo a concluir.  Neste caso, a adição de réplicas não irá melhorar as taxas de latência.  Para este cenário existem duas opções disponíveis:

1. **Aumentar as partições** uma partição é um mecanismo para dividir os dados nos recursos adicionais.  Por este motivo, quando adiciona uma segunda partição, obtém dividir os dados em dois.  Uma partição terceira divide o índice em três, etc.  Isto também tem o efeito que em alguns casos, as consultas lentas executará mais rapidamente devido a parallelization de computação.  Existem alguns exemplos de onde iremos ter visto esta parallelization funcionam muito bem com consultas que tenham consultas selectivity baixa.  Isto é composto por consultas que corresponde ao número de documentos ou quando facetamento tem de fornecer contagens através de grandes quantidades de documentos.  Uma vez que existe uma grande quantidade de computação for necessária para pontuar relevancy dos documentos ou para contabilizar o número de documentos, adicionar partições adicionais pode ajudar a fornecer o cálculo adicional.  
   
   Pode existir um máximo de 12 partições no serviço de pesquisa padrão e de 1 partição no serviço de pesquisa básica.  Partições podem ser ajustado a partir de [portal do Azure](search-create-service-portal.md) ou [PowerShell](search-manage-powershell.md).
2. **Campos de cardinalidade elevada limite:** um campo de cardinalidade elevada é composta por uma facetável ou um campo filtrável que tem um número significativo de valores exclusivos e, consequentemente, demora uma grande quantidade de recursos para resultados de computação ao longo.   Por exemplo, a definição de um campo de ID do produto ou a descrição como facetável filtrável iria tornar de cardinalidade elevada porque a maioria dos valores do documento documento é exclusiva. Sempre que possível, limite o número de campos de cardinalidade elevada.
3. **Camada de pesquisa de aumento:** mover até um escalão superior de pesquisa do Azure pode ser outra forma para melhorar o desempenho das consultas lentas.  Cada escalão superior também fornece a CPU mais rápida e mais memória que pode ter um impacto positivo no desempenho das consultas.

## <a name="scaling-for-availability"></a>Dimensionamento de disponibilidade
Réplicas não só ajudam a reduzir a latência de consulta, mas também podem permitir a elevada disponibilidade.  Com uma única réplica, deve esperar periódico período de indisponibilidade devido a reinícios do servidor após as atualizações de software ou para outros eventos de manutenção que irão ocorrer.  Como resultado, é importante considerar se a aplicação necessita de elevada disponibilidade de pesquisas (consultas), bem como as escritas (indexação eventos).  A Azure Search oferece opções de SLA em todas as ofertas de pesquisa paga com os seguintes atributos:

* 2 réplicas de disponibilidade elevada de só de leitura cargas de trabalho (consultas)
* 3 ou mais réplicas para elevada disponibilidade de cargas de trabalho de leitura / escrita (consultas e indexação)

Para obter mais detalhes sobre isto, visite o [contrato de nível de serviço de pesquisa do Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Uma vez que as réplicas são cópias dos seus dados, ter várias réplicas permite da Azure Search no computador reinícios e manutenção contra uma réplica de cada vez, permitindo que as consultas para continuar a ser executado contra outras réplicas.  Por esse motivo, também terá de considerar como este período de indisponibilidade poderá ter impacto em consultas que agora tem de ser executado contra um menor cópia dos dados.

## <a name="scaling-geo-distributed-workloads-and-provide-geo-redundancy"></a>Dimensionamento geo-distribuição cargas de trabalho e fornecer redundância geográfica
Para cargas de trabalho geo-distribuição, encontrará que utilizadores localizados longe do Centro de dados onde está alojado o serviço da Azure Search terão as taxas de latência superiores.  Por este motivo, muitas vezes, é importante que tenha vários serviços de pesquisa em regiões que estão na proximidade quanto mais próximo para estes utilizadores.  A pesquisa do Azure não atualmente fornecem um método automático de índices de pesquisa do Azure a replicar georreplicação em regiões, mas existem algumas que podem ser utilizadas técnicas que podem efetuar este processo simples implementar e gerir. Estes são descritos nas secções alguns seguintes.

O objetivo de um conjunto geo-distribuição dos serviços de pesquisa é ter duas ou mais índices disponíveis em dois ou mais regiões onde um utilizador será encaminhado para o serviço de pesquisa do Azure que fornece a latência mais baixa, como mostrado neste exemplo:

   ![Entre-separador de serviços por região][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Manter os dados em sincronização em vários serviços de pesquisa do Azure
Existem duas opções para manter os seus serviços de pesquisa distribuída sincronizados que consistem em qualquer um dos utilizando o [indexador de pesquisas de Azure](search-indexer-overview.md) ou a API de Push (também referido como o [API REST da Azure Search](https://docs.microsoft.com/rest/api/searchservice/)).  

### <a name="azure-search-indexers"></a>Indexadores de pesquisa do Azure
Se estiver a utilizar o indexador de pesquisa do Azure, já está a importar as alterações de dados de um arquivo de dados central como BD SQL do Azure ou a base de dados do Azure Cosmos. Quando cria uma nova procura serviço, pode simplesmente também criar um novo indexador de pesquisas do Azure para que o serviço que aponta para este arquivo de dados do mesmo. Dessa forma, sempre que novas alterações entrem no arquivo de dados, estes serão, em seguida, ser indexados por vários indexadores.  

Eis um exemplo de nessa arquitetura seria aspeto.

   ![Única origem de dados com o indexador distribuída e combinações de serviço][2]

### <a name="push-api"></a>API de push
Se estiver a utilizar o Push API da Azure Search para [atualizar conteúdo no seu índice da Azure Search](https://docs.microsoft.com/rest/api/searchservice/update-index), pode manter os vários serviços de pesquisa em sincronização ao enviar as alterações para todos os serviços de pesquisa, sempre que é necessária uma atualização.  Quando efetuar este procedimento é importante certificar-se lidar com casos em que uma atualização do serviço de um pesquisa falha e uma ou mais atualizações concluída com êxito.

## <a name="leveraging-azure-traffic-manager"></a>Aproveitamento do Traffic Manager do Azure
[Traffic Manager do Azure](../traffic-manager/traffic-manager-overview.md) permite-lhe para pedidos de rota para vários sites localizados em georreplicação, em seguida, são apoiados por vários serviços de pesquisa do Azure.  Uma vantagem do Gestor de tráfego é que pode a pesquisa da Azure Search, certifique-se de que está disponível e encaminhar os utilizadores para serviços de pesquisa alternativo em caso de um período de indisponibilidade.  Além disso, se o encaminhamento de pedidos de pesquisa de Web Sites através do Azure, Azure Traffic Manager permite carregar casos de balanceamento em que o Web site está ativo, mas não da Azure Search.  Eis um exemplo de que a arquitetura que tira partido do Gestor de tráfego.

   ![Entre-separador de serviços por região, com o Gestor de tráfego central][3]

## <a name="monitoring-performance"></a>Monitorização do desempenho
A pesquisa do Azure oferece a capacidade de analisar e monitorizar o desempenho do seu serviço através de [análise de tráfego de pesquisa (STA)](search-traffic-analytics.md). Através de STA, pode, opcionalmente, iniciar sessão a operações de pesquisa individuais, bem como as métricas agregadas para uma conta de armazenamento do Azure que, em seguida, pode ser processada para o Analysis Services ou visualizada no Power BI.  Utilizar métricas STA, pode rever as estatísticas de desempenho, tais como o número médio de consultas ou tempos de resposta das consultas.  Além disso, o registo da operação permite-lhe explorar detalhes de operações de pesquisa específico.

STA é uma ferramenta valiosa para compreender as taxas de latência essa perspetiva da Azure Search.  Uma vez que as métricas de desempenho de consulta registadas baseiam-se na hora que uma consulta demora a ser totalmente processados na Azure Search (desde o momento em que é pedido quando é enviado), que é possível utilizar esta opção para determinar se os problemas de latência são no lado do serviço de pesquisa do Azure ou fora do serviço, tal como de latência de rede.  

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre os limites de serviços e escalões de preços para cada um deles, consulte o artigo [Service limites na Azure Search](search-limits-quotas-capacity.md).

Visite [planeamento de capacidade](search-capacity-planning.md) para saber mais sobre as combinações de partição e réplica.

Para obter mais pesquisa no desempenho e para ver alguns demonstrações sobre como implementar as otimizações abordadas neste artigo, veja o vídeo seguinte:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
