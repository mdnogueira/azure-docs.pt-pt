---
title: "Monitorizar aplicações no App Service do Azure | Microsoft Docs"
description: "Saiba como monitorizar aplicações no App Service do Azure utilizando o Portal do Azure."
services: app-service
documentationcenter: 
author: btardif
manager: erikre
editor: 
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2016
ms.author: byvinyal
ms.openlocfilehash: 25d3776920d683fffedcd8ac6ed0e84dfe875974
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-monitor-apps-in-azure-app-service"></a>Como: monitorizar aplicações no App Service do Azure
[Serviço de aplicações](http://go.microsoft.com/fwlink/?LinkId=529714) fornece a funcionalidade de monitorização incorporada no [Portal do Azure](https://portal.azure.com).
Isto inclui a capacidade para rever **quotas** e **métricas** para uma aplicação, bem como o plano de serviço de aplicações, como configurar **alertas** e até mesmo **dimensionamento**automaticamente com base nestas métricas.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="understanding-quotas-and-metrics"></a>Noções sobre Quotas e métricas
### <a name="quotas"></a>Quotas
Aplicações alojadas no App Service estão sujeitas a determinados *limites* nos recursos que possam utilizar. Os limites são definidos pelo **plano do App Service** associado à aplicação.

Se a aplicação estiver alojada num **livres** ou **partilhados** planear, em seguida, os limites nos recursos que pode utilizar a aplicação são definidos pelo **Quotas**.

Se a aplicação estiver alojada num **básico**, **padrão** ou **Premium** planear, em seguida, os limites nos recursos que possam utilizar são definidos pelo **tamanho**(Pequena, média e grande) e **instância contagem** (1, 2, 3,...) da **plano do App Service**.

**Quotas** para **livres** ou **partilhados** as aplicações são:

* **CPU(short)**
  * Quantidade de CPU permitida para esta aplicação num intervalo de 5 minutos. Esta quota define novamente a cada 5 minutos.
* **CPU(Day)**
  * Quantidade total de CPU permitida para esta aplicação num dia. Esta quota define novamente a cada 24 horas à meia-noite UTC.
* **Memória**
  * Quantidade total de memória permitido para esta aplicação.
* **Largura de banda**
  * Quantidade total de saída da largura de banda permitida para esta aplicação num dia.
    Esta quota define novamente a cada 24 horas à meia-noite UTC.
* **Sistema de ficheiros**
  * Quantidade total de armazenamento permitido.

A quota apenas aplicável a aplicações alojadas no **básico**, **padrão** e **Premium** planos é **Filesystem**.

Obter mais informações sobre as quotas específicas, os limites e funcionalidades disponíveis para os SKUs de serviço de aplicação diferente podem ser encontradas aqui: [limites de serviço de subscrição do Azure](../azure-subscription-service-limits.md#app-service-limits)

#### <a name="quota-enforcement"></a>Imposição de quota
Se uma aplicação na sua utilização exceder o **CPU (abreviado)**, **CPU (dia)**, ou **largura de banda** quota, em seguida, a aplicação será parada até que a quota define novamente. Durante este período, todos os pedidos recebidos causará uma **HTTP 403**.
![][http403]

Se a aplicação **memória** é excedida a quota, em seguida, a aplicação irá ser novamente iniciada.

Se o **Filesystem** é excedida a quota, em seguida, qualquer operação irá falhar, isto inclui a escrever nos registos de escrita.

Quotas podem ser aumentadas ou removidas da sua aplicação atualizando o seu plano de serviço de aplicações.

### <a name="metrics"></a>Métricas
**Métricas** fornecem informações sobre a aplicação ou o comportamento do plano de serviço aplicacional.

Para um **aplicação**, as métricas disponíveis são:

* **Tempo de resposta médio**
  * O tempo médio decorrido para a aplicação servir pedidos em ms.
* **Memória média conjunto de trabalho**
  * A quantidade média de memória em MiBs utilizado pela aplicação.
* **Tempo de CPU**
  * A quantidade de CPU, em segundos consumidos pela aplicação. Para obter mais informações sobre esta métrica, consulte: [percentagem de CPU do vs de tempo de CPU](#cpu-time-vs-cpu-percentage)
* **Dados em**
  * A quantidade de largura de banda de entrada consumida pela aplicação na MiBs.
* **Dados de saída**
  * A quantidade de largura de banda de saída consumida pela aplicação na MiBs.
* **2xx de HTTP**
  * Contagem de pedidos, resultando num código de estado http > = 200 mas < 300.
* **3xx de HTTP**
  * Contagem de pedidos, resultando num código de estado http > = 300 mas < 400.
* **HTTP 401**
  * Contagem de pedidos, resultando em código de estado de HTTP 401.
* **HTTP 403**
  * Contagem de pedidos, resultando em código de estado HTTP 403.
* **HTTP 404**
  * Contagem de pedidos, resultando em código de estado de HTTP 404.
* **HTTP 406**
  * Contagem de pedidos, resultando em código de estado de HTTP 406.
* **4xx de HTTP**
  * Contagem de pedidos, resultando num código de estado http > = 400 mas < 500.
* **Erros de servidor de HTTP**
  * Contagem de pedidos, resultando num código de estado http > = 500 mas < 600.
* **Conjunto de trabalho de memória**
  * Montante actual de memória utilizada pela aplicação na MiBs.
* **Pedidos**
  * Número total de pedidos independentemente do seu código de estado HTTP resultante.

Para um **plano do App Service**, as métricas disponíveis são:

> [!NOTE]
> As métricas do plano de serviço de aplicações só estão disponíveis para planos no **básico**, **padrão** e **Premium** SKU.
> 
> 

* **Percentagem de CPU**
  * A CPU média utilizada em todas as instâncias do plano.
* **Percentagem de memória**
  * A memória de média utilizada em todas as instâncias do plano.
* **Dados em**
  * A média receber largura de banda utilizada em todas as instâncias do plano.
* **Dados de saída**
  * A média de largura de banda utilizada em todas as instâncias do plano de saída.
* **Comprimento da fila de disco**
  * O número médio de leitura e escrita pedidos que foram colocados em fila no armazenamento. Um comprimento de fila de disco elevada é uma indicação de uma aplicação que poderá ser abrandamento devido a e/s de disco excessivo.
* **Comprimento da fila de HTTP**
  * O número médio de pedidos de HTTP que tinha a manter-se na fila antes de a ser concluído. Um comprimento de fila de HTTP elevado ou crescente é um sintoma de um plano com muita carga.

### <a name="cpu-time-vs-cpu-percentage"></a>Percentagem de CPU do vs de tempo de CPU
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Existem 2 métricas que refletem a utilização da CPU. **Tempo de CPU** e **percentagem de CPU**

**Tempo de CPU** é útil para aplicações alojadas no **livres** ou **partilhados** planos de uma vez que um dos respetivos quotas está definido em minutos de CPU utilizados pela aplicação.

**Percentagem de CPU** por outro lado é útil para aplicações alojadas no **básico**, **padrão** e **premium** planos de uma vez que podem ser aumentados horizontalmente e esta métrica é um bom indicador de que a utilização geral em todas as instâncias de mensagens em fila.

## <a name="metrics-granularity-and-retention-policy"></a>Granularidade de métricas e política de retenção
Métricas para uma aplicação e o plano do app service são registadas e agregadas pelo serviço com as políticas de retenção e granularidades seguintes:

* **Minuto** métricas de granularidade são retidas por **48 horas**
* **Hora** métricas de granularidade são retidas por **30 dias**
* **Dia** métricas de granularidade são retidas por **90 dias**

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Monitorização Quotas e métricas no Portal do Azure.
Pode rever o estado dos diferentes **quotas** e **métricas** que afetam uma aplicação no [Portal do Azure](https://portal.azure.com).

![][quotas]
**Quotas** podem ser encontrados em Definições >**Quotas**. O UX permite-lhe rever: (1) o nome de quotas, (2) o intervalo de reposição, (3) o limite atual e valor (4) atual.

![][metrics]
**Métricas** pode ser acesso diretamente a partir do painel de recursos. Também pode personalizar o gráfico por: (1) **clique** no mesmo e selecione (2) **editar gráfico**.
Aqui pode alterar (3) **intervalo de tempo**, (4) **tipo de gráfico**e (5) **métricas** para apresentar.  

Pode saber mais sobre as métricas aqui: [monitorizar métricas de serviço](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

## <a name="alerts-and-autoscale"></a>Alertas e o dimensionamento automático
As métricas para pode ser estabelecer ligação cópias de segurança de com ele um plano de aplicação ou serviço de aplicações para alertas, para obter mais informações, consulte [receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

Aplicações de serviço de aplicações alojadas nos básico, standard ou premium planos de App Service suporte **dimensionamento automático**. Isto permite-lhe configurar regras que monitorizar as métricas do plano de serviço de aplicações e podem aumentar ou diminuir a contagem de instâncias que fornece recursos adicionais, conforme necessário ou guardar dinheiro quando a aplicação é superior aprovisionar. Pode saber mais sobre aqui a escala automática: [como escala](../monitoring-and-diagnostics/insights-how-to-scale.md) e aqui [melhores práticas para o dimensionamento automático de Monitor do Azure](../monitoring-and-diagnostics/insights-autoscale-best-practices.md)

> [!NOTE]
> Se pretender começar a utilizar o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [Experimentar o App Service](https://azure.microsoft.com/try/app-service/), onde pode criar de imediato uma aplicação Web de arranque de curta duração no App Service. Sem cartões de crédito; sem compromissos.
> 
> 

## <a name="whats-changed"></a>O que mudou
* Para obter um guia da alteração de Web sites para o App Service, consulte: [App Service do Azure e o Respetivo Impacto nos Serviços do Azure Existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

[fzilla]:http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:http://go.microsoft.com/fwlink/?LinkID=309169



<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png
