---
title: "Suporte do Azure Application Insights para vários componentes, micro-serviços e contentores | Microsoft Docs"
description: "Monitorização de aplicações que consistem em vários componentes do ou funções para o desempenho e utilização."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/17/2017
ms.author: mbullwin
ms.openlocfilehash: 046661bf7903b4e5ea528282ad5170901a45b35c
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="monitor-multi-component-applications-with-application-insights-preview"></a>Monitorizar aplicações com múltiplos componente com o Application Insights (pré-visualização)

Pode monitorizar as aplicações que consistem de vários componentes de servidor, funções ou serviços com [Azure Application Insights](app-insights-overview.md). Estado de funcionamento dos componentes e as relações entre eles são apresentados num mapa de aplicação único. Pode analisar individuais operações através de vários componentes com a correlação de HTTP automática. Diagnóstico de contentor pode ser integrado e correlacionado com a telemetria da aplicação. Utilize um único recurso do Application Insights para todos os componentes da aplicação. 

![Mapa de múltiplos componente da aplicação](./media/app-insights-monitor-multi-role-apps/app-map.png)

Utilizamos 'componente' aqui tem de estar verdadeiramente qualquer parte de uma aplicação grande funcionar. Por exemplo, uma aplicação comercial normal pode consistir em código de cliente em execução nos browsers da web, falar com um ou mais serviços de aplicação web, que por sua vez voltar a utilizar serviços de fim. Componentes de servidor podem ser alojado no local na nuvem, poderão ser funções da web e de trabalho do Azure ou podem executar nos contentores, tais como Docker ou de Service Fabric. 

### <a name="sharing-a-single-application-insights-resource"></a>Partilhar um único recurso do Application Insights 

A chave técnica aqui está a enviar telemetria a partir de cada componente na sua aplicação ao mesmo recurso do Application Insights, mas utilize o `cloud_RoleName` propriedade para distinguir os componentes quando for necessário. O Application Insights SDK adiciona o `cloud_RoleName` emissão de propriedade para os componentes de telemetria. Por exemplo, o SDK irá adicionar um nome do web site ou o nome do serviço de função para o `cloud_RoleName` propriedade. Pode substituir este valor com um telemetryinitializer. O mapa de aplicação utiliza o `cloud_RoleName` propriedade para identificar os componentes no mapa.

Para obter mais informações sobre como substituir o `cloud_RoleName` propriedade consulte [adicionar propriedades: ITelemetryInitializer](app-insights-api-filtering-sampling.md#add-properties-itelemetryinitializer).  

Em alguns casos, este não poderá ser apropriado e pode preferir utilizar recursos separados para diferentes grupos de componentes. Por exemplo, poderá ter de utilizar diferentes recursos para fins de faturação ou de gestão. Utilizar recursos separados significa que não veja todos os componentes apresentados num mapa de aplicação único; e que não é possível consultar componentes no [análise](app-insights-analytics.md). Também tem de configurar os recursos separados.

Com esse advertência, iremos assumir o resto deste documento que pretende enviar dados de vários componentes para um recurso do Application Insights.

## <a name="configure-multi-component-applications"></a>Configurar aplicações com múltiplos componente

Para obter um mapa de múltiplos componente de aplicação, terá de alcançar estes objetivos:

* **Instalar a pré-versão mais recente de** pacote do Application Insights em cada componente da aplicação. 
* **Partilhar um único recurso do Application Insights** para todos os componentes da aplicação.
* **Ativar o mapa de aplicação de função Multi** no painel de pré-visualizações.

Configure cada componente da aplicação utilizando o método adequado para o respetivo tipo. ([ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md), [JavaScript](app-insights-javascript.md).)

### <a name="1-install-the-latest-pre-release-package"></a>1. Instalar o pacote de versão de pré-lançamento mais recente

Atualizar ou instalar os pacotes do Application Insights no projeto para cada componente do servidor. Se estiver a utilizar o Visual Studio:

1. Um projeto com o botão direito e selecione **gerir pacotes NuGet**. 
2. Selecione **incluir pré-lançamento**.
3. Se o Application Insights pacotes são apresentados nas atualizações, selecione-os. 

    Caso contrário, procurar e instalar o pacote adequado:
    
    * Microsoft.ApplicationInsights.WindowsServer
    * Microsoft.ApplicationInsights.ServiceFabric - de componentes em execução como convidado executáveis e contentores de Docker a executar uma aplicação de Service Fabric em
    * Microsoft.ApplicationInsights.ServiceFabric.Native - fiável dos serviços de no ServiceFabric aplicações
    * Microsoft.ApplicationInsights.Kubernetes de componentes em execução no Docker Kubernetes

### <a name="2-share-a-single-application-insights-resource"></a>2. Partilhar um único recurso do Application Insights

* No Visual Studio, clique com o botão direito um projeto e selecione **configurar o Application Insights**, ou **Application Insights > configurar**. Para o projeto primeiro, utilize o Assistente para criar um recurso do Application Insights. Para projetos subsequentes, selecione o recurso do mesmo.
* Se não houver nenhuma menu do Application Insights, configure manualmente:

   1. No [portal do Azure](https://portal,azure.com), abra o recurso do Application Insights já criado para outro componente.
   2. No painel de descrição geral, abra a lista pendente Essentials separador e copie o **chave de instrumentação.**
   3. No seu projeto, abra Applicationinsights e inserir:`<InstrumentationKey>your copied key</InstrumentationKey>`

![Copie a chave de instrumentação para o ficheiro. config](./media/app-insights-monitor-multi-role-apps/copy-instrumentation-key.png)


### <a name="3-enable-multi-role-application-map"></a>3. Ativar função multi o mapeamento de aplicações

No portal do Azure, abra o recurso para a sua aplicação. No painel de pré-visualizações, ativar *mapa de aplicação de função Multi*.

### <a name="4-enable-docker-metrics-optional"></a>4. Ativar as métricas de Docker (opcional) 

Se um componente é executado num Docker alojada em VM Windows do Azure, pode recolher métricas adicionais do contentor. Insira-o no seu [diagnóstico do Azure](../monitoring-and-diagnostics/azure-diagnostics.md) ficheiro de configuração:

```
"DiagnosticMonitorConfiguration": {
        ...
        "sinks": "applicationInsights",
        "DockerSources": {
                "Stats": {
                    "enabled": true,
                    "sampleRate": "PT1M"
                }
            },
        ...
    }
    ...   
    "SinksConfig": {
        "Sink": [{
            "name": "applicationInsights",
            "ApplicationInsights": "<your instrumentation key here>"
        }]
    }
    ...
}

```

## <a name="use-cloudrolename-to-separate-components"></a>Utilize cloud_RoleName para separar os componentes

O `cloud_RoleName` propriedade está ligada a toda a telemetria. Identifica o componente - à função ou serviço - que origina a telemetria. (Não é igual à cloud_RoleInstance, que separa idênticas funções que estejam a executar em paralelo em vários processos de servidor ou máquinas.)

No portal, pode filtrar ou segmentar a telemetria com esta propriedade. Neste exemplo, o painel de falhas é filtrado para mostrar apenas as informações do serviço web de front-end, filtragem de falhas de back-end da API de CRM:

![Métrico gráfico segmentado pelo nome da função de nuvem](./media/app-insights-monitor-multi-role-apps/cloud-role-name.png)

## <a name="trace-operations-between-components"></a>Operações de rastreio entre componentes

Pode de rastreio de um componente para outro, as chamadas efetuadas ao processar uma operação individual.


![Mostrar a telemetria para a operação](./media/app-insights-monitor-multi-role-apps/show-telemetry-for-operation.png)

Clicar para uma lista de telemetria para esta operação correlacionada entre o servidor web front-end e a API de back-end:

![Componentes de pesquisa](./media/app-insights-monitor-multi-role-apps/search-across-components.png)


## <a name="next-steps"></a>Passos seguintes

* [Telemetria separada de desenvolvimento, teste e produção](app-insights-separate-resources.md)
