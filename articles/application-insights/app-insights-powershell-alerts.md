---
title: Utilizar o Powershell para definir alertas no Application Insights | Microsoft Docs
description: "Automatizar a configuração do Application Insights para obter mensagens de correio eletrónico sobre as alterações à métricas."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 05d6a9e0-77a2-4a35-9052-a7768d23a196
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: mbullwin
ms.openlocfilehash: b90a540afd1c2815db8f5a99ee210ce21ea4d874
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="use-powershell-to-set-alerts-in-application-insights"></a>Utilizar o PowerShell para definir alertas no Application Insights
Pode automatizar a configuração de [alertas](app-insights-alerts.md) no [Application Insights](app-insights-overview.md).

Além disso, pode [definir webhooks para automatizar a resposta a um alerta](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

> [!NOTE]
> Se pretender criar recursos e de alertas ao mesmo tempo, considere [através de um modelo Azure Resource Manager](app-insights-powershell.md).
>
>

## <a name="one-time-setup"></a>Configuração de uso individual
Se ainda não utilizou o PowerShell com a sua subscrição do Azure antes de:

Instale o módulo Azure Powershell no computador onde pretende executar os scripts.

* Instalar [instalador de plataforma Web da Microsoft (v5 ou superior)](http://www.microsoft.com/web/downloads/platform.aspx).
* Utilizá-la para instalar o Microsoft Azure Powershell

## <a name="connect-to-azure"></a>Ligar ao Azure
Inicie o Azure PowerShell e [ligar à sua subscrição](/powershell/azure/overview):

```PowerShell

    Add-AzureAccount
```


## <a name="get-alerts"></a>Obtenha alertas
    Get-AzureAlertRmRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

## <a name="add-alert"></a>Adicionar o alerta
    Add-AlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
     -ResourceGroup "{GROUP NAME}" `
     -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
     -MetricName "{METRIC NAME}" `
     -Operator GreaterThan  `
     -Threshold {NUMBER}   `
     -WindowSize {HH:MM:SS}  `
     [-SendEmailToServiceOwners] `
     [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM" ] `
     -Location "East US" // must be East US at present
     -RuleType Metric



## <a name="example-1"></a>Exemplo 1
Enviar-me e-mail se a resposta do servidor para pedidos HTTP, uma média de mais de 5 minutos, é mais lenta do que 1 segundo. Recurso do Application Insights é chamado IceCreamWebApp e está no grupo de recursos Fabrikam. Sou o proprietário da subscrição do Azure.

O GUID é o ID de subscrição (não a chave de instrumentação da aplicação).

    Add-AlertRule -Name "slow responses" `
     -Description "email me if the server responds slowly" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "request.duration" `
     -Operator GreaterThan `
     -Threshold 1 `
     -WindowSize 00:05:00 `
     -SendEmailToServiceOwners `
     -Location "East US" -RuleType Metric

## <a name="example-2"></a>Exemplo 2
Tenho uma aplicação em que posso utilizar [trackmetric ()](app-insights-api-custom-events-metrics.md#trackmetric) para comunicar uma métrica com o nome "salesPerHour." Envie que um e-mail para os meus colegas se "salesPerHour" descerem abaixo de 100, apresentou uma média superior a 24 horas.

    Add-AlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

A mesma regra pode ser utilizada para a métrica comunicada utilizando o [parâmetro medida](app-insights-api-custom-events-metrics.md#properties) da chamada de outro controlo como TrackEvent ou trackPageView.

## <a name="metric-names"></a>Nomes de métricos
| Nome da métrica | Nome de ecrã | Descrição |
| --- | --- | --- |
| `basicExceptionBrowser.count` |Exceções de browser |Contagem de exceções não identificadas emitidas no browser. |
| `basicExceptionServer.count` |Exceções de servidor |Contagem de exceções não processadas emitidas pela aplicação |
| `clientPerformance.clientProcess.value` |Tempo de processamento de cliente |Tempo entre a receção do último byte de um documento até o DOM ser carregado. Pedidos assíncronos podem continuar a ser processados. |
| `clientPerformance.networkConnection.value` |Tempo de ligação de rede de carregamento de página |Tempo que demora o browser para ligar à rede. Pode ser 0 se colocados em cache. |
| `clientPerformance.receiveRequest.value` |Tempo de resposta de receção |Tempo entre a enviar o pedido para começar a receber a resposta do browser. |
| `clientPerformance.sendRequest.value` |Tempo do pedido de envio |Tempo decorrido pelo browser ao enviar o pedido. |
| `clientPerformance.total.value` |Tempo de carregamento de página do browser |Tempo do pedido do utilizador até o DOM, tramas, scripts e imagens foram carregadas. |
| `performanceCounter.available_bytes.value` |Memória disponível |Memória física imediatamente disponível para um processo ou para uso do sistema. |
| `performanceCounter.io_data_bytes_per_sec.value` |Taxa de e/s do processo |Total de bytes por segundo lidos e escritos em ficheiros, rede e dispositivos. |
| `performanceCounter.number_of_exceps_thrown_per_sec.value` |taxa de exceções |Exceções acionadas por segundo. |
| `performanceCounter.percentage_processor_time.value` |Processo da CPU |A percentagem de tempo decorrido de todos os threads de processos utilizados pelo processador para instruções de execução para o processo de aplicações. |
| `performanceCounter.percentage_processor_total.value` |Tempo do processador |A percentagem de tempo que o processador despende em threads não inativos. |
| `performanceCounter.process_private_bytes.value` |Bytes privados do processo |Memória atribuída exclusivamente aos processos da aplicação monitorizada. |
| `performanceCounter.request_execution_time.value` |Tempo de execução do pedido ASP.NET |Tempo de execução do pedido mais recente. |
| `performanceCounter.requests_in_application_queue.value` |Pedidos ASP.NET na fila de execução |Comprimento da fila de pedidos da aplicação. |
| `performanceCounter.requests_per_sec.value` |Taxa de pedidos do ASP.NET |Taxa de todos os pedidos para a aplicação por segundo do ASP.NET. |
| `remoteDependencyFailed.durationMetric.count` |Falhas de dependência |Número de chamadas falhadas efetuadas pela aplicação de servidor para recursos externos. |
| `request.duration` |Tempo de resposta do servidor |Tempo entre a receção de um pedido HTTP e a conclusão do envio da resposta. |
| `request.rate` |Taxa de pedidos |Taxa de todos os pedidos para a aplicação por segundo. |
| `requestFailed.count` |Pedidos falhados |Número de pedidos HTTP que resultaram num código de resposta > = 400 |
| `view.count` |Vistas de página |Contagem de pedidos de utilizador do cliente para uma página web. Tráfego sintético é filtrado. |
| {sua personalizado o nome da métrica} |{O nome da métrica} |O valor métrico comunicado pelo [TrackMetric](app-insights-api-custom-events-metrics.md#trackmetric) ou no [parâmetro medidas de uma chamada de controlo](app-insights-api-custom-events-metrics.md#properties). |

As métricas são enviadas por módulos de telemetria diferentes:

| Grupo de métrico | Módulo de recoletor |
| --- | --- |
| basicExceptionBrowser,<br/>clientPerformance,<br/>ver |[JavaScript do browser](app-insights-javascript.md) |
| performanceCounter |[Desempenho](app-insights-configuration-with-applicationinsights-config.md) |
| remoteDependencyFailed |[Dependência](app-insights-configuration-with-applicationinsights-config.md) |
| pedido,<br/>requestFailed |[Pedido do servidor](app-insights-configuration-with-applicationinsights-config.md) |

## <a name="webhooks"></a>Webhooks
Pode [automatizar a resposta a um alerta](../monitoring-and-diagnostics/insights-webhooks-alerts.md). Azure irá chamar um endereço de web à sua escolha, quando for gerado um alerta.

## <a name="see-also"></a>Consultar também
* [Script para configurar o Application Insights](app-insights-powershell-script-create-resource.md)
* [Criar o Application Insights e recursos de teste web a partir de modelos](app-insights-powershell.md)
* [Automatizar coupling diagnóstico do Microsoft Azure ao Application Insights](app-insights-powershell-azure-diagnostics.md)
* [Automatizar a resposta a um alerta](../monitoring-and-diagnostics/insights-webhooks-alerts.md)
