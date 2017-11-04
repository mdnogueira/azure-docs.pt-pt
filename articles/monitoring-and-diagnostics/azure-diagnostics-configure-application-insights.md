---
title: "Configurar o diagnóstico do Azure para enviar dados para o Application Insights | Microsoft Docs"
description: "Atualize a configuração pública de diagnóstico do Azure para enviar dados para o Application Insights."
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: f9e12c3e-c307-435e-a149-ef0fef20513a
ms.service: monitoring-and-diagnostics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2016
ms.author: robb
ms.openlocfilehash: 67dc2d5bbfa2012e4e098616edda593d023c4c1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>Enviar dados de diagnóstico de serviço em nuvem, a Máquina Virtual ou o Service Fabric para o Application Insights
Serviços cloud, máquinas virtuais, conjuntos de dimensionamento de Máquina Virtual e Service Fabric todas as utilizam a extensão de diagnóstico do Azure para recolher dados.  Diagnóstico do Azure envia dados para tabelas de armazenamento do Azure.  No entanto, também pode pipe todas ou um subconjunto dos dados para outras localizações utilizando a extensão de diagnóstico do Azure versão 1.5 ou posterior.

Este artigo descreve como enviar dados de extensão de diagnóstico do Azure ao Application Insights.

## <a name="diagnostics-configuration-explained"></a>Configuração de diagnósticos explicada
A extensão de diagnóstico do Azure 1.5 introduzida sinks, que são localizações adicionais onde pode enviar dados de diagnóstico.

Configuração de exemplo de um receptor de para o Application Insights:

```XML
<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "ApplicationInsights",
            "ApplicationInsights": "{Insert InstrumentationKey}",
            "Channels": {
                "Channel": [
                    {
                        "logLevel": "Error",
                        "name": "MyTopDiagData"
                    },
                    {
                        "logLevel": "Error",
                        "name": "MyLogData"
                    }
                ]
            }
        }
    ]
}
```
- O **Sink** *nome* atributo é um valor de cadeia que identifica exclusivamente o sink.

- O **ApplicationInsights** elemento Especifica a chave de instrumentação do recurso de insights na aplicação onde os dados de diagnóstico do Azure são enviados.
    - Se não tiver um recurso do Application Insights existente, consulte [criar um novo recurso do Application Insights](../application-insights/app-insights-create-new-resource.md) para obter mais informações sobre como criar um recurso e ao obter a chave de instrumentação.
    - Se estiver a desenvolver um serviço em nuvem com o Azure SDK 2.8 e posterior, esta chave de instrumentação é preenchido automaticamente. O valor é baseado no **APPINSIGHTS_INSTRUMENTATIONKEY** definição de configuração do serviço quando empacotamento o projeto de serviço em nuvem. Consulte [utilize Application Insights com o diagnóstico do Azure para resolver problemas de serviço em nuvem](../cloud-services/cloud-services-dotnet-diagnostics-applicationinsights.md).

- O **canais** elemento contém um ou mais **canal** elementos.
    - O *nome* atributo refere-se exclusivamente para esse canal.
    - O *loglevel* atributo permite-lhe especificar o nível de registo que permite que o canal. Os níveis de registo disponível por ordem de mais informações, pelo menos, são:
        - Verboso
        - Informações
        - Aviso
        - Erro
        - Crítico

Um canal funciona como um filtro e permite-lhe selecionar os níveis de registo específicos para enviar para o sink de destino. Por exemplo, pode recolher registos verbosos e enviá-los para armazenamento, mas enviar erros apenas para o sink.

O gráfico seguinte mostra esta relação.

![Configuração pública de diagnóstico](./media/azure-diagnostics-configure-applicationinsights/AzDiag_Channels_App_Insights.png)

O gráfico seguinte resume os valores de configuração e como funcionam. Pode incluir vários sinks na configuração em níveis diferentes da hierarquia. O sink no nível superior age como uma definição global e especificado no elemento individuais funciona como uma substituição para essa definição global.

![Diagnóstico Sinks configuração com o Application Insights](./media/azure-diagnostics-configure-applicationinsights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>Exemplo de configuração de receptores concluída
Eis um exemplo de conclusão da configuração pública de ficheiros que
1. envia a todos os erros para o Application Insights (especificado no **DiagnosticMonitorConfiguration** nó)
2. também envia registos nível verbosos para os registos da aplicação (especificado no **registos** nó).

```XML
<WadCfg>
  <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
       sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
    <DiagnosticInfrastructureLogs />
    <PerformanceCounters>
      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
      <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
    </PerformanceCounters>
    <WindowsEventLog scheduledTransferPeriod="PT1M">
      <DataSource name="Application!*" />
    </WindowsEventLog>
    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
            sinks="ApplicationInsights.MyLogData"/> <!-- This specific info sent to this channel -->
  </DiagnosticMonitorConfiguration>

<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
  </SinksConfig>
</WadCfg>
```
```JSON
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
        "overallQuotaInMB": 4096,
        "sinks": "ApplicationInsights.MyTopDiagData", "_comment": "All info below sent to this channel",
        "DiagnosticInfrastructureLogs": {
        },
        "PerformanceCounters": {
            "PerformanceCounterConfiguration": [
                {
                    "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                    "sampleRate": "PT3M"
                },
                {
                    "counterSpecifier": "\\Memory\\Available MBytes",
                    "sampleRate": "PT3M"
                }
            ]
        },
        "WindowsEventLog": {
            "scheduledTransferPeriod": "PT1M",
            "DataSource": [
                {
                    "name": "Application!*"
                }
            ]
        },
        "Logs": {
            "scheduledTransferPeriod": "PT1M",
            "scheduledTransferLogLevelFilter": "Verbose",
            "sinks": "ApplicationInsights.MyLogData", "_comment": "This specific info sent to this channel"
        }
    },
    "SinksConfig": {
        "Sink": [
            {
                "name": "ApplicationInsights",
                "ApplicationInsights": "{Insert InstrumentationKey}",
                "Channels": {
                    "Channel": [
                        {
                            "logLevel": "Error",
                            "name": "MyTopDiagData"
                        },
                        {
                            "logLevel": "Verbose",
                            "name": "MyLogData"
                        }
                    ]
                }
            }
        ]
    }
}
```
A configuração anterior, as seguintes linhas tem os significados seguintes:

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>Enviar todos os dados que está a ser recolhidos ao diagnóstico do Azure

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights",
}
```

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Enviar registos de erros apenas para o sink do Application Insights

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyTopDiagData",
}
```

### <a name="send-verbose-application-logs-to-application-insights"></a>Enviar registos de aplicação verboso ao Application Insights

```XML
<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyLogData",
}
```

## <a name="limitations"></a>Limitações

- **Canais apenas iniciar contadores de tipo e não o desempenho.** Se especificar um canal com um elemento de contador de desempenho, é ignorada.
- **O nível de registo para um canal não pode exceder o nível de registo para o que está a ser recolhido ao diagnóstico do Azure.** Por exemplo, não é possível recolher os erros do registo de aplicação no elemento de registos e tente enviar verboso registos para o sink do Application Insight. O *scheduledTransferLogLevelFilter* atributo sempre tem de recolher igual ou mais registos do que os registos está a tentar enviar para um receptor de.
- **Não é possível enviar os dados de blob recolhidos pela extensão de diagnóstico do Azure ao Application Insights.** Por exemplo, nada especificado sob o *diretórios* nós. Para informações de falhas de informação de falha real é enviada para o armazenamento de BLOBs e apenas uma notificação que foi gerada a informação de falha é enviada para o Application Insights.

## <a name="next-steps"></a>Passos Seguintes
* Saiba como [veja as suas informações de diagnóstico do Azure](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-cloudservices#view-azure-diagnostic-events) no Application Insights.
* Utilize [PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md) para ativar a extensão de diagnóstico do Azure para a sua aplicação.
* Utilize [Visual Studio](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) para ativar a extensão de diagnóstico do Azure para a sua aplicação
