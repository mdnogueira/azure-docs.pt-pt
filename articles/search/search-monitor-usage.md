---
title: "Monitorizar a utilização e as estatísticas num serviço da Azure Search | Microsoft Docs"
description: "Controlar o tamanho de consumo e o índice de recursos para a Azure Search, um serviço de pesquisa em nuvem alojado no Microsoft Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 
ms.service: search
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/09/2017
ms.author: heidist
ms.openlocfilehash: fe852afedfc1cce99d81b8ab53c6c80df34ac6d6
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="monitoring-an-azure-search-service"></a>Monitorizar um serviço da Azure Search

A pesquisa do Azure oferece vários recursos para o registo de utilização e desempenho dos serviços de pesquisa. Fornece acesso a métricas, os registos, estatísticas de índice e as capacidades de monitorização expandidas no Power BI. Este artigo descreve como ativar as estratégias de monitorização diferentes e como interpretar dados resultantes.

## <a name="azure-search-metrics"></a>Métricas de pesquisa do Azure
Métricas dão-lhe quase em tempo real visibilidade para o serviço de pesquisa e estão disponíveis para cada serviço, com nenhuma configuração adicional. Permitem-lhe monitorizar o desempenho do seu serviço de 30 dias.

A pesquisa do Azure recolhe dados de três métricas diferentes:

* Latência de pesquisa: tempo o serviço de pesquisa necessário para processar consultas de pesquisa, agregadas por minuto.
* Procurar consultas por segundo (QPS): número de pesquisa consultas recebidas por segundo, agregados por minuto.
* Percentagem de consulta de pesquisa otimizada: percentagem de consultas de pesquisa que foram limitado, agregado por minuto.

![Atividade de captura de ecrã de QPS][1]

### <a name="set-up-alerts"></a>configurar alertas
Página Detalhes métrica, pode configurar alertas para acionar uma notificação por e-mail ou uma ação automática quando uma métrica atravesse um limiar que foram definidos.

Para obter mais informações sobre as métricas, consulte a documentação completa no Monitor do Azure.  

## <a name="how-to-track-resource-usage"></a>Como controlar a utilização de recursos
Controlar o crescimento de índices e o tamanho do documento pode ajudá-lo proativamente ajustar capacidade antes de atingir o limite superior que estabeleceu para o seu serviço. Pode fazê-lo no portal ou através de programação utilizando a API REST.

### <a name="using-the-portal"></a>Utilizar o portal

Para monitorizar a utilização de recursos, ver o contagens e as estatísticas para o seu serviço no [portal](https://portal.azure.com).

1. Iniciar sessão para o [portal](https://portal.azure.com).
2. Abra o dashboard de serviço do seu serviço da Azure Search. Os mosaicos para o serviço podem ser encontrados na Home page, ou pode navegar para o serviço de pesquisa na barra de índice.

A secção de utilização inclui um medidor que indica que parte dos recursos disponíveis estão atualmente em utilização. Para obter informações sobre limites de serviço a para o armazenamento, índices e documentos, consulte [os limites de serviço](search-limits-quotas-capacity.md).

  ![Mosaico utilização][2]

> [!NOTE]
> A captura de ecrã acima para o serviço livre, o que tem um máximo de uma réplica, cada partição e pode apenas índices de anfitrião, 3, 10 000 documentos ou 50 MB de dados, o que ocorrer primeiro. Serviços criados num escalão básicas ou Standard têm limites de serviço muito maiores. Para obter mais informações sobre como escolher uma camada, consulte [escolha um escalão ou SKU](search-sku-tier.md).
>
>

### <a name="using-the-rest-api"></a>Utilizando a API REST
A API REST da Azure Search e o SDK .NET fornecem acesso programático para as métricas de serviço.  Se estiver a utilizar [indexadores](https://msdn.microsoft.com/library/azure/dn946891.aspx) para carregar um índice da SQL Database do Azure ou a base de dados do Azure Cosmos, está disponível para obter os números que requerem uma API adicional.

* [Obter estatísticas de índice](/rest/api/searchservice/get-index-statistics)
* [Contagem de documentos](/rest/api/searchservice/count-documents)
* [Obter o estado do indexador](/rest/api/searchservice/get-indexer-status)

## <a name="how-to-export-logs-and-metrics"></a>Como exportar os registos e as métricas

Pode exportar os registos de operações para o serviço e os dados não processados para as métricas descritos na secção anterior. Os registos de operações permitem que sabe como o serviço está a ser utilizado e pode ser consumido do Power BI quando os dados são copiados para uma conta de armazenamento. A pesquisa do Azure fornece um pacote de conteúdos do Power BI monitorização para esta finalidade.


### <a name="enabling-monitoring"></a>Ativar a monitorização
Abra o serviço da Azure Search no [portal do Azure](http://portal.azure.com) sob a opção de ativar a monitorização.

Escolher os dados que pretende exportar: registos, métricas ou ambos. Pode copiar para uma conta de armazenamento, enviá-lo para um hub de eventos ou exportá-lo à análise de registos.

![Como ativar a monitorização no portal][3]

Para ativar a utilizar o PowerShell ou a CLI do Azure, consulte a documentação [aqui](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

### <a name="logs-and-metrics-schemas"></a>Esquemas de métricas e registos
Quando os dados são copiados para uma conta de armazenamento, os dados são formatados como JSON e do local em dois contentores:

* operationlogs de registos de insights: para registos de tráfego de pesquisa
* insights-as métricas-pt1m: com base nas métricas

Não há um blob, por hora, por contentor.

Caminho de exemplo:`resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

#### <a name="log-schema"></a>Esquema de registo
Os blobs de registos contém os registos de tráfego do serviço de pesquisa.
Cada blob tem um objeto de raiz denominado **registos** que contém uma matriz de objetos de registo.
Cada blob tem registos na operação demorou local durante a mesma hora.

| Nome | Tipo | Exemplo | Notas |
| --- | --- | --- | --- |
| hora |DateTime |"2015-12-07T00:00:43.6872559Z" |TimeStamp da operação |
| resourceId |Cadeia |"SUBSCRIÇÕES/11111111-1111-1111-1111-111111111111 /<br/>FORNECEDORES/PREDEFINIDOS/RESOURCEGROUPS /<br/> MICROSOFT. PESQUISA/SEARCHSERVICES/SEARCHSERVICE" |O ResourceId |
| operationName |Cadeia |"Query.Search" |O nome da operação |
| operationVersion |Cadeia |"2015-02-28" |A api-version utilizada |
| categoria |Cadeia |"OperationLogs" |constante |
| resultType |Cadeia |"Êxito" |Os valores possíveis: êxito ou falha |
| resultSignature |Int |200 |Código de resultado HTTP |
| durationMS |Int |50 |Duração da operação em milissegundos |
| propriedades |objeto |consulte a tabela seguinte |Objeto que contém dados específicos da operação |

**Esquema de propriedades**
| Nome | Tipo | Exemplo | Notas |
| --- | --- | --- | --- |
| Descrição |Cadeia |"Obter /indexes('content')/docs" |Ponto final da operação |
| Consulta |Cadeia |"? pesquisa = azuresearch, uma vez & $count = true & api-version = 2015-02-28" |Os parâmetros de consulta |
| Documentos |Int |42 |Número de documentos processados |
| indexName |Cadeia |"testindex" |Nome do índice associado com a operação |

#### <a name="metrics-schema"></a>Esquema de métricas
| Nome | Tipo | Exemplo | Notas |
| --- | --- | --- | --- |
| resourceId |Cadeia |"SUBSCRIÇÕES/11111111-1111-1111-1111-111111111111 /<br/>FORNECEDORES/PREDEFINIDOS/RESOURCEGROUPS /<br/>MICROSOFT. PESQUISA/SEARCHSERVICES/SEARCHSERVICE" |o id de recurso |
| metricName |Cadeia |"Latência" |o nome da métrica |
| hora |DateTime |"2015-12-07T00:00:43.6872559Z" |carimbo da operação |
| Média |Int |64 |O valor médio dos exemplos não processados no intervalo de métrica de tempo |
| mínimo |Int |37 |O valor mínimo de exemplos não processados no intervalo de métrica de tempo |
| Máximo |Int |78 |O valor máximo de exemplos não processados no intervalo de métrica de tempo |
| total |Int |258 |O valor total de exemplos não processados no intervalo de métrica de tempo |
| Contagem |Int |4 |O número de amostras em bruto utilizado para gerar a métrica |
| intervalo de agregação |Cadeia |"PT1M" |O intervalo de tempo da métrica no ISO 8601 |

Todas as métricas são reportadas nos intervalos de um minuto. Cada métrica expõe valores mínimos, máximo e médio por minuto.

Para a métrica de SearchQueriesPerSecond mínimo é o valor mais baixo para consultas de pesquisa por segundo que foi registado durante esse minuto. O mesmo se aplica ao valor máximo. Médio, é o agregado entre o minuto completo.
Considere sobre este cenário, durante um minuto: um segundo de alta de carga que é o número máximo para SearchQueriesPerSecond, seguido de segundos 58 da carga média, e, finalmente, um segundo com apenas uma consulta, que é o mínimo.

Para ThrottledSearchQueriesPercentage, mínimo, máximo, médio e total, têm o mesmo valor: a percentagem de consultas de pesquisa que foram limitado, do número total de consultas de pesquisa durante um minuto.

## <a name="analyzing-your-data-with-power-bi"></a>Analisar os dados com o Power BI

Recomendamos que utilize [Power BI](https://powerbi.microsoft.com) para explorar e visualizar os dados. Pode ligar facilmente à sua conta de armazenamento do Azure e começar rapidamente a analisar os dados.

A pesquisa do Azure fornece um [o pacote de conteúdos do Power BI](https://app.powerbi.com/getdata/services/azure-search) que lhe permite monitorizar e compreender o tráfego de pesquisa com tabelas e gráficos predefinidos. Contém um conjunto de relatórios do Power BI que automaticamente ligar aos seus dados e fornecem visual informações sobre o serviço de pesquisa. Para obter mais informações, consulte o [página de ajuda do pacote de conteúdos](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/).

![Dashboard do Power BI para a pesquisa do Azure][4]

## <a name="next-steps"></a>Passos seguintes
Reveja [dimensionar as réplicas e de partições](search-limits-quotas-capacity.md) para obter orientações sobre como equilibrar a atribuição de partições e réplicas para um serviço existente.

Visite [gerir o serviço de pesquisa no Microsoft Azure](search-manage.md) para obter mais informações sobre a administração de serviço, ou [desempenho e a otimização de](search-performance-optimization.md) para obter orientações sobre a otimização.

Saiba mais sobre a criação de relatórios incrível. Consulte [introdução ao Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) para obter detalhes

<!--Image references-->
[1]: ./media/search-monitor-usage/AzSearch-Monitor-BarChart.PNG
[2]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG
[3]: ./media/search-monitor-usage/AzureSearch-Enable-Monitoring.PNG
[4]: ./media/search-monitor-usage/AzureSearch-PowerBI-Dashboard.png
