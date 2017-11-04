---
title: "Análise de eventos de recursos de infraestrutura de serviço do Azure com o Application Insights | Microsoft Docs"
description: "Saiba mais sobre como visualizar e analisar eventos utilizando o Application Insights para monitorização e diagnóstico de clusters de Service Fabric do Azure."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 34f14f42150e46edae2d1352827f96a411117a62
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Análise de eventos e visualização com o Application Insights

Azure Application Insights é uma plataforma extensível para monitorização de aplicações e de diagnóstico. Inclui uma análise poderosa e consultar a ferramenta, dashboard personalizável e visualizações e ainda mais opções, incluindo automática de alertas. É a plataforma recomendada para a monitorização e diagnóstico de serviços e aplicações de Service Fabric.

## <a name="setting-up-application-insights"></a>Configurar o Application Insights

### <a name="creating-an-ai-resource"></a>Criar um recurso de AI

Para criar um recurso de AI head ativação pós-falha para o Azure Marketplace e procure "Application Insights". Deve aparecer como primeira solução (está sob a categoria "Web + móvel"). Clique em **criar** quando está a visualizar o recurso à direita (confirmar que o caminho corresponde a imagem abaixo).

![Novo recurso do Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/create-new-ai-resource.png)

Terá de preencher algumas informações para aprovisionar o recurso corretamente. No *tipo de aplicação* campo, utilize "Uma aplicação web ASP.NET" se irá utilizar qualquer um dos recursos de infraestrutura de serviço do modelos de programação ou publicar uma aplicação .NET para o cluster. Se for implementar convidado executáveis e contentores, utilize "Geral". Em geral, por predefinição para utilizar "Uma aplicação web ASP.NET" para manter as suas opções aberta no futuro. O nome é até a sua preferência e o grupo de recursos e de subscrição são pós-implementação das partições do recurso. Recomendamos que o recurso de AI está no mesmo grupo de recursos do seu cluster. Se precisar de mais informações, consulte [crie um recurso do Application Insights](../application-insights/app-insights-create-new-resource.md)

Tem a chave de instrumentação AI configurar AI com a ferramenta de agregação de eventos. Depois do recurso de AI configurado (demora alguns minutos após a implementação é validada), navegue para a mesma e localize o **propriedades** secção na barra de navegação esquerdo. Um novo painel abre-se que mostra um *chave de instrumentação*. Se precisar de alterar a subscrição ou grupo de recursos do recurso, pode ser feita aqui bem.

### <a name="configuring-ai-with-wad"></a>Configurar AI com WAD

>[!NOTE]
>Isto só é aplicável a clusters do Windows neste momento.

Existem duas formas de principais para enviar dados de WAD para AI do Azure, que é conseguido ao adicionar um receptor de AI à configuração do WAD, conforme detalhado em [neste artigo](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

#### <a name="add-an-ai-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Adicionar uma chave de instrumentação AI quando criar um cluster no portal do Azure

![Adicionar um AIKey](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

Quando criar um cluster, se Diagnostics está ativada "", irá mostrar um campo opcional para introduzir uma chave de instrumentação do Application Insights. Se colar a IKey AI aqui, o sink de AI será automaticamente configurado para si no modelo do Resource Manager que é utilizado para implementar o cluster.

#### <a name="add-the-ai-sink-to-the-resource-manager-template"></a>Adicionar o Sink de AI para o modelo do Resource Manager

No "WadCfg" do modelo do Resource Manager, adicione "Sink", incluindo as seguintes duas alterações:

1. Adicionar a configuração de receptores imediatamente após a declaração do `DiagnosticMonitorConfiguration` concluída:

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. Incluem o Sink no `DiagnosticMonitorConfiguration` adicionando a seguinte linha no `DiagnosticMonitorConfiguration` do `WadCfg` (antes do `EtwProviders` declarados):

    ```json
    "sinks": "applicationInsights"
    ```

Em ambos os fragmentos de código acima, o nome "applicationInsights" foi utilizado para descrever o sink. Não é um requisito e desde que o nome do sink está incluído no "sinks", pode definir o nome para qualquer cadeia.

Atualmente, os registos do cluster irão mostrar como rastreios no Visualizador de registo do AI. Uma vez que a maioria os rastreios proveniente da plataforma de nível "Informativa", pode também considerar a alteração da configuração do sink para apenas enviar os registos do tipo "Críticas" ou "Error". Isto pode ser feito através da adição de "Canais de" para o sink, como é demonstrado na [neste artigo](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

>[!NOTE]
>Se utilizar um IKey AI incorreto no portal ou no seu modelo do Resource Manager, terá de alterar a chave manualmente e atualizar o cluster / reimplementá-lo. 

### <a name="configuring-ai-with-eventflow"></a>Configurar AI com EventFlow

Se estiver a utilizar EventFlow para agregar eventos, certifique-se importar o `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights`pacote NuGet. O seguinte tem de ser incluído no *produz* secção o *eventFlowConfig.json*:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        // (replace the following value with your AI resource's instrumentation key)
        "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
]
```

Certifique-se efetuar as alterações necessárias nos filtros, bem como incluir quaisquer outras entradas (juntamente com os seus respetivos pacotes de NuGet).

## <a name="aisdk"></a>AI. SDK

Geralmente, é recomendado utilizar EventFlow e WAD como soluções de agregação, porque permitem uma abordagem mais modulares de diagnóstico e monitorização, ou seja, não se pretender alterar as saídas de EventFlow, necessita de nenhuma alteração ao seu instrumento real, apenas uma simple a modificação ao ficheiro de configuração. Se, no entanto, optar por investir em utilizar o Application Insights e não é provável que alterar para uma plataforma diferentes, deve ter o aspeto na utilizando novo SDK do AI para agregar eventos e enviando-as para AI. Isto significa que já não será necessário configurar EventFlow para enviar os dados para AI, mas em vez disso, instalará o pacote NuGet do Service Fabric do ApplicationInsight. Podem ser encontrados detalhes no pacote [aqui](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

[Suportem de Application Insights para micro-serviços e contentores](https://azure.microsoft.com/app-insights-microservices/) mostra algumas das novas funcionalidades que estão a ser trabalhadas (beta ainda atualmente no), que permitem-lhe ter mais rica opções de monitorização de out of box com AI. Estes incluem o controlo de dependência (utilizado na criação de um AppMap de todos os seus serviços e aplicações em cluster e a comunicação entre elas) e melhor correlação de rastreios provenientes da sua serviços (ajuda-o no melhor pinpointing um problema no fluxo de trabalho do uma aplicação ou serviço).

Se estiver a desenvolver no .NET e irá, provavelmente, utilizar algumas das Service Fabric programação modelos e estão pretendo utilizar o AI como a plataforma para visualizar e analisar dados de eventos e de registo, em seguida, recomendamos que leia através da rota de AI SDK como as de monitorização e diagnos fluxo de trabalho tics. Leitura [isto](../application-insights/app-insights-asp-net-more.md) e [isto](../application-insights/app-insights-asp-net-trace-logs.md) para começar a utilizar com a utilização de AI para recolher e apresentar os seus registos.

## <a name="navigating-the-ai-resource-in-azure-portal"></a>Navegar o recurso de AI no portal do Azure

Assim que tiver configurado o AI como uma saída para os eventos e registos, devem começar informações apareçam no seu recurso AI dentro de alguns minutos. Navegue para o recurso de AI, o que leva-o para o dashboard de recursos de AI. Clique em **pesquisa** na barra de tarefas AI para ver os rastreios mais recentes que se tiver recebido e conseguir filtrar através de-los.

*Explorador de métricas* é uma ferramenta útil para a criação de dashboards personalizados com base nas métricas que as aplicações, serviços e cluster podem ser Reporting Services. Consulte [explorar métricas no Application Insights](../application-insights/app-insights-metrics-explorer.md) configurar alguns gráficos para si com base nos dados que está a recolher.

Ao clicar em **análise** leva-o ao portal do Application Insights Analytics, onde pode consultar eventos e rastreios com o maior âmbito e optionality. Saiba mais sobre no [análise no Application Insights](../application-insights/app-insights-analytics.md).

## <a name="next-steps"></a>Passos seguintes

* [Configurar alertas no AI](../application-insights/app-insights-alerts.md) para ser notificado sobre as alterações no desempenho ou utilização
* [Smart deteção no Application Insights](../application-insights/app-insights-proactive-diagnostics.md) efetua uma análise proativa de telemetria a ser enviada para AI para o avisar de potenciais problemas de desempenho