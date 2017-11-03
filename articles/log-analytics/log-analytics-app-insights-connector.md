---
title: "Ver dados de aplicações do Azure Application Insights | Microsoft Docs"
description: "Pode utilizar a solução de conector do Application Insights para diagnosticar problemas de desempenho e compreender o que fazem os utilizadores com a sua aplicação quando monitorizadas com o Application Insights."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 49280cad-3526-43e1-a365-c6a3bf66db52
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: banders
ms.openlocfilehash: fe6c003e095b25cf3ec3430fc68dcd399150b3ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="application-insights-connector-solution-preview-in-operations-management-suite-oms"></a>Solução de conector das informações da aplicação (pré-visualização) no Operations Management Suite (OMS)

![Símbolo do Application Insights](./media/log-analytics-app-insights-connector/app-insights-connector-symbol.png)

A solução de conector das informações de aplicações ajuda-o a diagnosticar problemas de desempenho e compreenda que os utilizadores fazem com a aplicação quando está a ser monitorizado com [Application Insights](../application-insights/app-insights-overview.md). Vistas da mesma telemetria de aplicações que os programadores ver no Application Insights estão disponíveis no OMS. No entanto, quando integrar as suas aplicações do Application Insights com o OMS, visibilidade das suas aplicações é aumentada, fazendo com que a operação e dados de aplicações num único local. Ter as mesmas vistas ajuda-o a colaborar com os programadores de aplicações. As vistas comuns podem ajudá-lo a reduzir o tempo para detetar e resolver a aplicação e problemas de plataforma.

Quando utilizar a solução, pode:

- Ver todas as suas aplicações do Application Insights num único local, mesmo quando estão em diferentes subscrições do Azure
- Correlacionar dados de infraestrutura com dados de aplicação
- Visualizar dados de aplicação com perspetivas na pesquisa de registo
- Passar a partir dos dados de análise de registos para a aplicação Application Insights no OMS e portais do Azure

## <a name="connected-sources"></a>Origens ligadas

Ao contrário da maioria dos outros soluções de análise de registos, não se encontra recolhidos dados para o conector do Application Insights por agentes. Todos os dados utilizados pela solução inclui diretamente a partir do Azure.

| Origem Ligada | Suportado | Descrição |
| --- | --- | --- |
| [Agentes do Windows](log-analytics-windows-agents.md) | Não | A solução não recolhe informações de agentes do Windows. |
| [Agentes do Linux](log-analytics-linux-agents.md) | Não | A solução não recolhe informações de agentes Linux. |
| [Grupo de gestão do SCOM](log-analytics-om-agents.md) | Não | A solução não recolhe informações de agentes num grupo de gestão do SCOM ligado. |
| [Conta de armazenamento do Azure](log-analytics-azure-storage.md) | Não | A solução não não as informações de recolha do armazenamento do Azure. |

## <a name="prerequisites"></a>Pré-requisitos

- Para aceder às informações de conector do Application Insights, tem de ter uma subscrição do Azure
- Tem de ter pelo menos um recurso do Application Insights configurado.
- Tem de ser o proprietário ou contribuinte o recurso do Application Insights.

## <a name="configuration"></a>Configuração

1. Ativar a solução de análise de aplicações Web do Azure a partir de [do Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ApplicationInsights?tab=Overview) ou utilizando o processo descrito no [soluções de análise de registos adicionar da galeria do soluções](log-analytics-add-solutions.md).
2. No portal do OMS, clique em **definições** &gt; **dados** &gt; **Application Insights**.
3. Em **selecionar uma subscrição**, selecione uma subscrição com recursos do Application Insights e, em **nome da aplicação**, selecione uma ou mais aplicações.
4. Clique em **Guardar**.

Em aproximadamente 30 minutos, dados ficam disponíveis e o mosaico do Application Insights é atualizado com dados, como a imagem seguinte:

![Mosaico do Application Insights](./media/log-analytics-app-insights-connector/app-insights-tile.png)

Outros pontos a ter em mente:

- Só pode associar aplicações do Application Insights para uma área de trabalho do OMS.
- Só pode associar [recursos Standard ou Premium Application Insights](https://azure.microsoft.com/pricing/details/application-insights) à análise de registos do OMS. No entanto, pode utilizar o escalão gratuito de análise de registos.

## <a name="management-packs"></a>Pacotes de gestão

Esta solução não instala quaisquer pacotes de gestão em grupos de gestão ligado.

## <a name="use-the-solution"></a>Utilizar a solução

As secções seguintes descrevem como pode utilizar os painéis apresentados no dashboard do Application Insights para ver e interagir com os dados das suas aplicações.

### <a name="view-application-insights-connector-information"></a>Ver informações de conector do Application Insights

Clique em de **Application Insights** mosaico para abrir o **Application Insights** dashboard para ver os seguintes painéis.

![Dashboard de conhecimentos de aplicação](./media/log-analytics-app-insights-connector/app-insights-dash01.png)

![Dashboard de conhecimentos de aplicação](./media/log-analytics-app-insights-connector/app-insights-dash02.png)

O dashboard inclui os painéis mostrados na tabela. Cada painel lista até 10 itens correspondentes aos critérios de nesse painel para o âmbito especificado e o intervalo de tempo. Pode executar uma pesquisa de registo que devolve todos os registos quando clicar em **ver todos os** na parte inferior do painel ou ao clicar no cabeçalho do painel.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

| **Coluna** | **Descrição** |
| --- | --- |
| Aplicações - número de aplicações | Mostra o número de aplicações em recursos de aplicação. Também apresenta uma lista de nomes de aplicação e para cada um, a contagem de registos de aplicação. Clique no número para executar uma pesquisa de registo para<code>Type=ApplicationInsights &#124; measure sum(SampledCount) by ApplicationName</code> <br><br>  Clique num nome de aplicação para executar uma pesquisa de registo para a aplicação que mostra os registos de aplicações por anfitrião, registos por tipo de telemetria e todos os dados por tipo (com base no último dia). |
| Volume de dados – aloja o envio de dados | Mostra o número de anfitriões de computador que está a enviar dados. Também apresenta uma lista de anfitriões de computador e a contagem de registos para cada anfitrião. Clique no número para executar uma pesquisa de registo para<code>Type=ApplicationInsights &#124; measure sum(SampledCount) by Host</code> <br><br> Clique no nome do computador para executar uma pesquisa de registo para o anfitrião que mostra os registos de aplicações por anfitrião, registos por tipo de telemetria e todos os dados por tipo (com base no último dia). |
| Disponibilidade – resultados do teste Web | Mostra um gráfico de anel para os resultados do teste web, que indica que passou ou falhar. Clique em gráfico para executar uma pesquisa de registo para<code>Type=ApplicationInsights TelemetryType=Availability &#124; measure sum(SampledCount) by AvailabilityResult</code> <br><br> Resultados mostram o número de falhas de todos os testes e transmite. Mostra todas as aplicações Web com o tráfego para o último minuto. Clique num nome de aplicação para ver uma pesquisa de registo que mostra detalhes de testes web falhou. |
| Pedidos de servidor – pedidos por hora | Mostra um gráfico de linhas de pedidos de servidor por hora para várias aplicações. Coloque o cursor sobre uma linha no gráfico para ver as aplicações de 3 principais receber pedidos para um ponto no tempo. Também mostra uma lista das aplicações receber pedidos e o número de pedidos para o período selecionado. <br><br>Clique em gráfico para executar uma pesquisa de registo para <code>Type=ApplicationInsights TelemetryType=Request &#124; measure sum(SampledCount) by ApplicationName interval 1hour</code> que mostra um gráfico de linhas mais detalhado dos pedidos de servidor por hora para várias aplicações. <br><br> Clique em executar uma pesquisa de registo para uma aplicação na lista <code>Type=ApplicationInsights  ApplicationName=yourapplicationname  TelemetryType=Request</code> que mostra uma lista de pedidos, gráficos para pedidos ao longo da duração de tempo e o pedido e uma lista de pedido de códigos de resposta.   |
| Falhas – pedidos com falhas por hora | Mostra um gráfico de linhas com falhas de pedidos da aplicação por hora. Coloque o cursor sobre o gráfico para ver as aplicações de 3 superiores com pedidos falhados para um ponto no tempo. Também mostra uma lista de aplicações com o número de pedidos falhados para cada. Clique em gráfico para executar uma pesquisa de registo para <code>Type=ApplicationInsights TelemetryType=Request  RequestSuccess = false &#124; measure sum(SampledCount) by ApplicationName interval 1hour</code> que mostra um gráfico de linhas mais detalhado de pedidos de aplicação que falhou. <br><br>Clique num item na lista para executar uma pesquisa de registo para <code>Type=ApplicationInsights ApplicationName=yourapplicationname TelemetryType=Request  RequestSuccess=false</code> que mostra falhou pedidos, gráficos para pedidos falhados ao longo da duração de tempo e o pedido e uma lista de códigos de resposta de pedidos falhados. |
| Exceções – exceções por hora | Mostra um gráfico de linhas de exceções por hora. Coloque o cursor sobre o gráfico para ver as aplicações de 3 superiores com exceções para um ponto no tempo. Também mostra uma lista de aplicações com o número de exceções para cada. Clique em gráfico para executar uma pesquisa de registo para <code>Type=ApplicationInsights TelemetryType=Exception &#124; measure sum(SampledCount) by ApplicationName interval 1hour</code> que mostra um gráfico de ligação mais detalhado de exceções. <br><br>Clique num item na lista para executar uma pesquisa de registo para <code>Type=ApplicationInsights  ApplicationName=yourapplicationname TelemetryType=Exception</code> que mostra uma lista de exceções, gráficos exceções através de pedidos falhados e tempo e uma lista de tipos de exceção.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Ver a perspetiva do Application Insights com a pesquisa de registo

Quando clicar em qualquer item no dashboard, verá uma perspetiva de Application Insights mostrada na pesquisa. Perspetiva fornece uma visualização expandida, consoante o tipo de telemetria que selecionou. Por isso, visualização alterações de conteúdo para os tipos de telemetria diferentes.

Ao clicar em qualquer lugar no painel de aplicações, consulte a predefinição **aplicações** perspetiva.

![Perspetiva do Application Insights aplicações](./media/log-analytics-app-insights-connector/applications-blade-drill-search.png)

Perspetiva mostra uma descrição geral da aplicação que selecionou.

O **disponibilidade** painel mostra uma vista de perspetiva diferentes onde pode ver os resultados do teste web e de pedidos falhados relacionados.

![Perspetiva do Application Insights disponibilidade](./media/log-analytics-app-insights-connector/availability-blade-drill-search.png)

Quando clica em qualquer lugar no **pedidos de servidor** ou **falhas** painéis, os componentes de perspetiva alterar para lhe fornecer uma visualização de relacionadas com pedidos.

![Painel de informações de falhas de aplicações](./media/log-analytics-app-insights-connector/server-requests-failures-drill-search.png)

Quando clica em qualquer lugar a **exceções** painel, verá uma visualização de que é adaptada às exceções.

![Painel do Application Insights exceções](./media/log-analytics-app-insights-connector/exceptions-blade-drill-search.png)

Independentemente se clicar em algo um o **Application Insights conector** dashboard, dentro de **pesquisa** página próprio, qualquer consulta devolver dados do Application Insights mostram a aplicação Perspetiva de informações. Por exemplo, se estiver a ver dados do Application Insights, um **&#42;** consulta também apresenta o separador de perspetiva como a imagem seguinte:

![Application Insights ](./media/log-analytics-app-insights-connector/app-insights-search.png)

Componentes de perspetiva são atualizados dependendo da consulta de pesquisa. Isto significa que pode filtrar os resultados utilizando qualquer campo de pesquisa que dá-lhe a capacidade para ver os dados a partir de:

- Todas as suas aplicações
- Uma única aplicação selecionada
- Um grupo de aplicações

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Passar para uma aplicação no portal do Azure

Painéis do conector das informações de aplicação foram concebidos para permitir a passar para a aplicação Application Insights selecionada *ao utilizar o portal do OMS*. Pode utilizar a solução como uma plataforma de monitorização de alto nível que o ajuda a resolver problemas de uma aplicação. Quando vir um potencial problema em qualquer uma das suas aplicações ligadas, pode a desagregação para a mesma na OMS pesquisa ou pode ser dinâmico diretamente para a aplicação Application Insights.

Para dinâmico, clique nas reticências (**...** ) que é apresentada no final de cada linha e selecione **aberta no Application Insights**.

>[!NOTE]
>**Abrir no Application Insights** não está disponível no portal do Azure.

![Abrir no Application Insights](./media/log-analytics-app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Dados de exemplo-corrigidos

Application Insights fornece  *[amostragem correção](../application-insights/app-insights-sampling.md)*  para ajudar a reduzir o tráfego de telemetria. Quando ativa a amostragem na sua aplicação Application Insights, obter um número reduzido de entradas armazenados no Application Insights e no OMS. Enquanto a consistência dos dados é preservada no **Application Insights conector** página e perspetivas, manualmente deve corrigir amostras de dados para as suas consultas personalizadas.

Eis um exemplo de correção de amostra de uma consulta de pesquisa de registo:

```
Type=ApplicationInsights | measure sum(SampledCount) by TelemetryType
```

O **amostragem contagem** campo está presente em todas as entradas e mostra o número de pontos de dados que representa a entrada. Se ativar a amostragem para a sua aplicação Application Insights, **amostragem contagem** for superior a 1. Para contabilizar o número real de entradas que gera a sua aplicação, soma o **amostragem contagem** campos.

A amostragem afeta apenas o número total de entradas que gera a sua aplicação. Não é necessário corrigir amostragem para campos de métricos como **RequestDuration** ou **AvailabilityDuration** porque os campos mostram a média para entradas representadas.

## <a name="input-data"></a>Dados de entrada

A solução recebe os seguintes tipos de telemetria dos dados das suas aplicações ligadas do Application Insights:

- Disponibilidade
- Exceções
- Pedidos
- Vistas de página – para a sua área de trabalho receber as vistas de página, tem de configurar as suas aplicações para recolher informações. Fore obter mais informações, consulte [PageViews](../application-insights/app-insights-api-custom-events-metrics.md#page-views).
- Eventos personalizados – para a sua área de trabalho receber eventos personalizados, tem de configurar as suas aplicações para recolher informações. Fore obter mais informações, consulte [TrackEvent](../application-insights/app-insights-api-custom-events-metrics.md#trackevent).

Dados são recebidos pelo OMS do Application Insights como ficar disponível.

## <a name="output-data"></a>dados de saída

Um registo com um *tipo* de *ApplicationInsights* é criada para cada tipo de dados de entrada. Registos de ApplicationInsights têm propriedades mostradas nas seguintes secções:

### <a name="generic-fields"></a>Campos genéricos

| Propriedade | Descrição |
| --- | --- |
| Tipo | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | Tempo do registo |
| ApplicationId | Chave de instrumentação da aplicação Application Insights |
| ApplicationName | Nome do Application Insights aplicação |
| RoleInstance | ID de anfitrião do servidor |
| DeviceType | Dispositivo cliente |
| ScreenResolution |   |
| Continente | Continente onde o pedido teve origem |
| País | País em que o pedido teve origem |
| Distrito | Distrito, estado ou região onde o pedido teve origem |
| Cidade | Cidade ou town onde o pedido teve origem |
| isSynthetic | Indica se o pedido foi criado por um utilizador ou pelo método automatizado. = TRUE gerados pelo utilizador ou = false método automatizado |
| SamplingRate | Percentagem de telemetria gerada pelo SDK que é enviado para o portal. Intervalo entre 0,0 e 100,0. |
| SampledCount | 100/(SamplingRate). Por exemplo, 4 =&gt; 25% |
| IsAuthenticated | VERDADEIRO ou FALSO |
| OperationID | Itens que tenham a mesma operação ID são apresentadas como itens relacionados no portal. Normalmente, o ID do pedido |
| ParentOperationID | ID da operação principal |
| OperationName |   |
| ID de sessão | GUID para identificar exclusivamente a sessão em que o pedido foi criado |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Campos específicos de disponibilidade

| Propriedade | Descrição |
| --- | --- |
| TelemetryType | Disponibilidade |
| AvailabilityTestName | Nome do teste web |
| AvailabilityRunLocation | Origem geográfica do pedido de http |
| AvailabilityResult | Indica o resultado de êxito do teste web |
| AvailabilityMessage | A mensagem ligada para o teste web |
| AvailabilityCount | 100 /(Sampling Rate). Por exemplo, 4 =&gt; 25% |
| DataSizeMetricValue | 1.0 ou 0,0 |
| DataSizeMetricCount | 100 /(Sampling Rate). Por exemplo, 4 =&gt; 25% |
| AvailabilityDuration | Tempo, em milissegundos, da duração de teste web |
| AvailabilityDurationCount | 100 /(Sampling Rate). Por exemplo, 4 =&gt; 25% |
| AvailabilityValue |   |
| AvailabilityMetricCount |   |
| AvailabilityTestId | GUID exclusivo para o teste web |
| AvailabilityTimestamp | Timestamp preciso do teste de disponibilidade |
| AvailabilityDurationMin | Para registos de amostras, este campo mostra a duração de teste web mínimo (milissegundos) para os pontos de dados representado |
| AvailabilityDurationMax | Para registos de amostras, este campo mostra a duração de teste web máximo (em milissegundos) para os pontos de dados representado |
| AvailabilityDurationStdDev | Para registos de amostras, este campo mostra o desvio-padrão entre todas as durações de teste de web (milissegundos) para os pontos de dados representado |
| AvailabilityMin |   |
| AvailabilityMax |   |
| AvailabilityStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Campos específicos de exceção

| Tipo | ApplicationInsights |
| --- | --- |
| TelemetryType | Exceção |
| ExceptionType | Tipo de exceção |
| ExceptionMethod | O método que cria a exceção |
| ExceptionAssembly | Assemblagem inclui a estrutura e versão, bem como token de chave pública |
| ExceptionGroup | Tipo de exceção |
| ExceptionHandledAt | Indica o nível que processada a exceção |
| ExceptionCount | 100 /(Sampling Rate). Por exemplo, 4 =&gt; 25% |
| ExceptionMessage | Mensagem da exceção |
| ExceptionStack | Pilha completa da exceção |
| ExceptionHasStack | VERDADEIRO se a exceção tem uma pilha de |



### <a name="request-specific-fields"></a>Campos específicos do pedido

| Propriedade | Descrição |
| --- | --- |
| Tipo | ApplicationInsights |
| TelemetryType | Pedir |
| ResponseCode | Resposta HTTP enviada ao cliente |
| RequestSuccess | Indica o êxito ou falha. VERDADEIRO ou FALSO. |
| RequestID | ID para identificar exclusivamente o pedido |
| RequestName | GET/POST + base de URL |
| RequestDuration | Tempo, em segundos, da duração de pedido |
| URL | URL do pedido não incluindo anfitrião |
| Anfitrião | Anfitrião do servidor Web |
| URLBase | URL completo do pedido |
| ApplicationProtocol | Tipo de protocolo utilizado pela aplicação |
| requestCount | 100 /(Sampling Rate). Por exemplo, 4 =&gt; 25% |
| RequestDurationCount | 100 /(Sampling Rate). Por exemplo, 4 =&gt; 25% |
| RequestDurationMin | Para registos de amostras, este campo mostra a duração mínima de pedido (milissegundos) para os pontos de dados representado. |
| RequestDurationMax | Para registos de amostras, este campo mostra a duração máxima de pedido (milissegundos) para os pontos de dados representado |
| RequestDurationStdDev | Para registos de amostras, este campo mostra o desvio-padrão entre todas as durações de pedido (milissegundos) para os pontos de dados representado |

## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo

Esta solução não tem um conjunto de pesquisas de registo de exemplo apresentadas no dashboard. No entanto, as consultas de pesquisa de registo de exemplo com descrições são mostradas no [informações de vista Application Insights conector](#view-application-insights-connector-information) secção.

## <a name="next-steps"></a>Passos seguintes

- Utilize [pesquisa registo](log-analytics-log-searches.md) para ver informações detalhadas sobre as aplicações do Application Insights.
