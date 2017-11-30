---
title: "Monitorizar aplicações no App Service do Azure | Microsoft Docs"
description: "Saiba como monitorizar aplicações no App Service do Azure utilizando o portal do Azure."
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
ms.date: 11/28/2017
ms.author: byvinyal
ms.openlocfilehash: 58ccdba6f01cfb7de72f28f185102bf7f618eab4
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-monitor-apps-in-azure-app-service"></a>Como: monitorizar aplicações no App Service do Azure
[Serviço de aplicações](http://go.microsoft.com/fwlink/?LinkId=529714) fornece a funcionalidade de monitorização incorporada no [portal do Azure](https://portal.azure.com).
O portal do Azure inclui a capacidade para rever **quotas** e **métricas** para uma aplicação, bem como o plano de serviço de aplicações, como configurar **alertas** e até mesmo **dimensionamento**  automaticamente com base nestas métricas.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="understanding-quotas-and-metrics"></a>Noções sobre Quotas e métricas
### <a name="quotas"></a>Quotas
Aplicações alojadas no App Service estão sujeitas a determinados *limites* nos recursos que possam utilizar. Os limites são definidos pelo **plano do App Service** associado à aplicação.

Se a aplicação estiver alojada num **livres** ou **partilhados** planear, em seguida, os limites nos recursos que pode utilizar a aplicação são definidos pelo **Quotas**.

Se a aplicação estiver alojada num **básico**, **padrão** ou **Premium** planear, em seguida, os limites nos recursos que possam utilizar são definidos pelo **tamanho**(Pequena, média e grande) e **instância contagem** (1, 2, 3,...) da **plano do App Service**.

**Quotas** para **livres** ou **partilhados** as aplicações são:

* **CPU(short)**
  * Quantidade de CPU permitida para esta aplicação num intervalo de 5 minutos. Esta quota repõe a cada cinco minutos.
* **CPU(Day)**
  * Quantidade total de CPU permitida para esta aplicação num dia. Esta quota repõe a cada 24 horas à meia-noite UTC.
* **Memória**
  * Quantidade total de memória permitido para esta aplicação.
* **Largura de banda**
  * Quantidade total de saída da largura de banda permitida para esta aplicação num dia.
    Esta quota repõe a cada 24 horas à meia-noite UTC.
* **Sistema de ficheiros**
  * Quantidade total de armazenamento permitido.

A quota apenas aplicável a aplicações alojadas no **básico**, **padrão**, e **Premium** planos é **Filesystem**.

Obter mais informações sobre as quotas específicas, os limites e funcionalidades disponíveis para os SKUs de serviço de aplicação diferente podem ser encontradas aqui: [limites de serviço de subscrição do Azure](../azure-subscription-service-limits.md#app-service-limits)

#### <a name="quota-enforcement"></a>Imposição de quota
Se uma aplicação excede o **CPU (abreviado)**, **CPU (dia)**, ou **largura de banda** quota, em seguida, a aplicação está parado até que repõe a quota. Durante este período, todos os pedidos recebidos originar uma **HTTP 403**.
![][http403]

Se a aplicação **memória** é excedida a quota, em seguida, a aplicação for reiniciada.

Se o **Filesystem** é excedida a quota, em seguida, escrever qualquer falha da operação, que inclui qualquer escritas nos registos.

Quotas podem ser aumentadas ou removidas da sua aplicação atualizando o seu plano de serviço de aplicações.

### <a name="metrics"></a>Métricas
**Métricas** fornecem informações sobre a aplicação ou o comportamento do plano de serviço aplicacional.

Para um **aplicação**, as métricas disponíveis são:

* **Tempo de resposta médio**
  * O tempo médio decorrido para a aplicação servir pedidos em ms.
* **Memória média conjunto de trabalho**
  * A quantidade média de memória em MiBs utilizado pela aplicação.
* **Tempo de CPU**
  * A quantidade de CPU, em segundos consumidos pela aplicação. Para mais informações sobre esta métrica, consulte: [percentagem de CPU do vs de tempo de CPU](#cpu-time-vs-cpu-percentage)
* **Dados em**
  * A quantidade de largura de banda de entrada consumida pela aplicação na MiBs.
* **Dados de saída**
  * A quantidade de largura de banda de saída consumida pela aplicação na MiBs.
* **2xx de HTTP**
  * Contagem de pedidos, resultando num código de estado HTTP > = 200 mas < 300.
* **3xx de HTTP**
  * Contagem de pedidos, resultando num código de estado HTTP > = 300 mas < 400.
* **HTTP 401**
  * Contagem de pedidos, resultando em código de estado de HTTP 401.
* **HTTP 403**
  * Contagem de pedidos, resultando em código de estado HTTP 403.
* **HTTP 404**
  * Contagem de pedidos, resultando em código de estado de HTTP 404.
* **HTTP 406**
  * Contagem de pedidos, resultando em código de estado de HTTP 406.
* **4xx de HTTP**
  * Contagem de pedidos, resultando num código de estado HTTP > = 400 mas < 500.
* **Erros de servidor de HTTP**
  * Contagem de pedidos, resultando num código de estado HTTP > = 500 mas < 600.
* **Conjunto de trabalho de memória**
  * Montante actual de memória utilizada pela aplicação na MiBs.
* **Pedidos**
  * Número total de pedidos independentemente do seu código de estado HTTP resultante.

Para um **plano do App Service**, as métricas disponíveis são:

> [!NOTE]
> As métricas do plano de serviço de aplicações só estão disponíveis para planos no **básico**, **padrão**, e **Premium** camadas.
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

Existem duas métricas que refletem a utilização da CPU. **Tempo de CPU** e **percentagem de CPU**

**Tempo de CPU** é útil para aplicações alojadas no **livres** ou **partilhados** planos de uma vez que um dos respetivos quotas está definido em minutos de CPU utilizados pela aplicação.

**Percentagem de CPU** é útil para aplicações alojadas no **básico**, **padrão**, e **premium** planos de uma vez que podem ser aumentados horizontalmente. Percentagem de CPU é um bom indicador de que a utilização geral em todas as instâncias.

## <a name="metrics-granularity-and-retention-policy"></a>Granularidade de métricas e política de retenção
Métricas para uma aplicação e o plano do app service são registadas e agregadas pelo serviço com as políticas de retenção e granularidades seguintes:

* **Minuto** métricas de granularidade são retidas por **30 horas**
* **Hora** métricas de granularidade são retidas por **30 dias**
* **Dia** métricas de granularidade são retidas por **30 dias**

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Monitorização de Quotas e métricas no portal do Azure.
Pode rever o estado dos diferentes **quotas** e **métricas** que afetam uma aplicação no [portal do Azure](https://portal.azure.com).

![][quotas]
**Quotas** podem ser encontrados em Definições >**Quotas**. O UX permite-lhe rever: (1) o nome de quotas, (2) o intervalo de reposição, (3) o limite atual e o valor (4) atual.

![][metrics]
**Métricas** pode ser acesso diretamente a partir da página de recursos. Também pode personalizar o gráfico por: (1) **clique** no mesmo e selecione (2) **editar gráfico**.
Aqui pode alterar (3) **intervalo de tempo**, (4) **tipo de gráfico**e (5) **métricas** para apresentar.  

Pode saber mais sobre as métricas aqui: [monitorizar métricas de serviço](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

## <a name="alerts-and-autoscale"></a>Alertas e o dimensionamento automático
As métricas para um plano de aplicação ou serviço de aplicações podem ser estabelecer ligação com ele cópias de segurança para alertas. Para saber mais sobre o assunto, consulte [receber notificações de alerta](../monitoring-and-diagnostics/insights-alerts-portal.md).

Aplicações de serviço de aplicações alojadas nos básica, standard ou premium do serviço de aplicações planos suporte **dimensionamento automático**. Dimensionamento automático permite-lhe configurar regras de monitorizar as métricas do plano de serviço de aplicações. As regras podem aumentar ou diminuir a contagem de instâncias que fornece recursos adicionais, conforme necessário. As regras também podem ajudar a poupar dinheiro quando a aplicação é aprovisionada excessiva. Pode saber mais sobre aqui a escala automática: [como escala](../monitoring-and-diagnostics/insights-how-to-scale.md) e aqui [melhores práticas para o dimensionamento automático de Monitor do Azure](../monitoring-and-diagnostics/insights-autoscale-best-practices.md)

> [!NOTE]
> Se pretender começar a utilizar o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [Experimentar o App Service](https://azure.microsoft.com/try/app-service/), onde pode criar de imediato uma aplicação Web de arranque de curta duração no App Service. Sem cartões de crédito; sem compromissos.
> 
> 

[fzilla]:http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:http://go.microsoft.com/fwlink/?LinkID=309169



<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png
