---
title: "Agregação de eventos de recursos de infraestrutura de serviço do Azure com EventFlow | Microsoft Docs"
description: "Saiba mais sobre agregar e recolha de eventos utilizando EventFlow para monitorização e diagnóstico de clusters de Service Fabric do Azure."
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
ms.openlocfilehash: 9a6e629582b6966d270a2378e585572efe133f3e
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="event-aggregation-and-collection-using-eventflow"></a>Agregação de eventos e coleção utilizando EventFlow

[Microsoft Diagnostics EventFlow](https://github.com/Azure/diagnostics-eventflow) pode encaminhar eventos a partir de um nó para um ou mais destinos de monitorização. Porque é incluído como um pacote NuGet do seu projeto de serviço, EventFlow código e a configuração transmitida juntamente com o serviço, eliminando o problema de configuração por nó mencionado anteriormente sobre diagnósticos do Azure. EventFlow é executado dentro do processo de serviço e estabelece ligação direta às saídas configuradas. Devido à ligação direta, EventFlow funciona para o Azure, o contentor e implementações de serviços no local. Seja cuidadoso ao se executar o EventFlow high-density cenários, tal como num contentor, porque cada pipeline EventFlow estabelece uma ligação externa. Por isso, se alojar vários processos, receberá várias ligações de saída! Esta operação não é o mesmo uma preocupação para aplicações de Service Fabric, porque todas as réplicas de um `ServiceType` executadas no mesmo processo e isto limita o número de ligações de saída. EventFlow também oferece filtragem de eventos, para que são enviados apenas os eventos que correspondem ao filtro especificado.

## <a name="set-up-eventflow"></a>Configurar EventFlow

Binários EventFlow estão disponíveis como um conjunto de pacotes NuGet. Para adicionar EventFlow a um projeto de serviço do Service Fabric, clique com o botão direito no projeto no Explorador de soluções e escolha "Gerir NuGet packages." Mudar para o separador "Procurar", procure por "`Diagnostics.EventFlow`":

![Pacotes de EventFlow NuGet no Gestor de pacote NuGet do Visual Studio da IU](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

Verá uma lista dos pacotes de vários aparecer, identificados com "Entradas" e "Saídas". EventFlow suporta vários fornecedores de registo diferentes e analisadores. O serviço de alojamento EventFlow deve incluir pacotes adequadas, consoante a origem e destino para os registos da aplicação. Para além do pacote de ServiceFabric core, também precisa de, pelo menos, uma entrada e saída configurado. Por exemplo, pode adicionar os seguintes pacotes para eventos de EventSource enviados para o Application Insights:

* `Microsoft.Diagnostics.EventFlow.Input.EventSource`para capturar os dados da classe de EventSource do serviço e a partir de EventSources padrão, tais como *dos serviços Microsoft-ServiceFabric* e *Microsoft-ServiceFabric-Atores*)
* `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights`(Iremos enviar os registos para um recurso do Application Insights do Azure)
* `Microsoft.Diagnostics.EventFlow.ServiceFabric`(permite a inicialização do pipeline EventFlow da configuração de serviço do Service Fabric e relatórios de problemas com o envio de dados de diagnóstico como relatórios de estado de funcionamento do Service Fabric)

>[!NOTE]
>`Microsoft.Diagnostics.EventFlow.Input.EventSource`o pacote necessita que o projeto de serviço para o .NET Framework 4.6 ou mais recente de destino. Certifique-se de que define o framework de destino adequada nas propriedades do projeto antes de instalar este pacote.

Depois de instalar todos os pacotes, o passo seguinte consiste em configurar e ativar EventFlow no serviço.

## <a name="configure-and-enable-log-collection"></a>Configurar e ativar a recolha de registos
O pipeline de EventFlow responsável pelo envio de registos é criado a partir de uma especificação armazenada num ficheiro de configuração. O `Microsoft.Diagnostics.EventFlow.ServiceFabric` pacote instala um ficheiro de configuração inicial do EventFlow em `PackageRoot\Config` pasta de solução, denominada `eventFlowConfig.json`. Este ficheiro de configuração tem de ser modificados para capturar os dados do serviço predefinido `EventSource` classe e quaisquer outras entradas que pretende configurar e enviar dados para o local adequado.

Eis um exemplo *eventFlowConfig.json* com base nos pacotes de NuGet mencionados acima:
```json
{
  "inputs": [
    {
      "type": "EventSource",
      "sources": [
        { "providerName": "Microsoft-ServiceFabric-Services" },
        { "providerName": "Microsoft-ServiceFabric-Actors" },
        // (replace the following value with your service's ServiceEventSource name)
        { "providerName": "your-service-EventSource-name" }
      ]
    }
  ],
  "filters": [
    {
      "type": "drop",
      "include": "Level == Verbose"
    }
  ],
  "outputs": [
    {
      "type": "ApplicationInsights",
      // (replace the following value with your AI resource's instrumentation key)
      "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
  ],
  "schemaVersion": "2016-08-11"
}
```

O nome do ServiceEventSource serviço é o valor da propriedade nome o `EventSourceAttribute` aplicado à classe ServiceEventSource. Todos os estiver especificado no `ServiceEventSource.cs` ficheiro, que faz parte o código do serviço. Por exemplo, o seguinte fragmento de código o nome do ServiceEventSource é *aminhaempresa-Application1-Stateless1*:

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

Tenha em atenção que `eventFlowConfig.json` ficheiro faz parte do pacote de configuração do serviço. Alterações a este ficheiro podem ser incluídas numa completo ou configuração-apenas atualizações do serviço, sujeita verificações de estado de funcionamento de atualização do Service Fabric e Reversão automática se houver falha da atualização. Para obter mais informações, consulte [atualização da aplicação de Service Fabric](service-fabric-application-upgrade.md).

O *filtros* secção de configuração permite-lhe mais personalizar as informações que vai aceder através do pipeline de EventFlow para as saídas, permitindo-lhe remover ou incluir determinadas informações ou alterar a estrutura do dados do evento. Para obter mais informações sobre filtragem, consulte [EventFlow filtros](https://github.com/Azure/diagnostics-eventflow#filters).

O passo final é instanciar EventFlow pipeline no código de arranque do serviço, localizado na `Program.cs` ficheiro:

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Services.Runtime;

// **** EventFlow namespace
using Microsoft.Diagnostics.EventFlow.ServiceFabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>
        private static void Main()
        {
            try
            {
                // **** Instantiate log collection via EventFlow
                using (var diagnosticsPipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MyApplication-MyService-DiagnosticsPipeline"))
                {

                    ServiceRuntime.RegisterServiceAsync("Stateless1Type",
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);
                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}
```

O nome transmitido como parâmetro do `CreatePipeline` método do `ServiceFabricDiagnosticsPipelineFactory` é o nome do *entidade de estado de funcionamento* que representa o pipeline de coleção de registo EventFlow. Este nome é utilizado se detetar EventFlow e o erro e relatórios-lo através do subsistema de estado de funcionamento do Service Fabric.

### <a name="use-service-fabric-settings-and-application-parameters-in-eventflowconfig"></a>Utilize as definições de Service Fabric e os parâmetros de aplicação eventFlowConfig

EventFlow suporta utilizando as definições de Service Fabric e aplicação paremeters para configurar as definições de EventFlow. Pode consultar os parâmetros de definições de Service Fabric utilizando esta sintaxe especial para valores:

```json
servicefabric:/<section-name>/<setting-name>
```

`<section-name>`é o nome da secção de configuração do Service Fabric, e `<setting-name>` é a definição de configuração fornecer o valor que será utilizado para configurar uma definição de EventFlow. Para ler mais sobre como fazê-lo, aceda a [suporte para definições de Service Fabric e os parâmetros da aplicação](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters).

## <a name="verification"></a>Verificação

Iniciar o serviço e observe a depuração janela de saída no Visual Studio. Depois do serviço é iniciado, deve começar a ver provas de que o serviço está a enviar registos de saída que tiver configurado. Navegue para a sua plataforma de análise e visualização de eventos e confirme que os registos de iniciar a Mostrar cópia de segurança (operação pode demorar alguns minutos).

## <a name="next-steps"></a>Passos seguintes

* [Análise de eventos e visualização com o Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Análise de eventos e visualização com o OMS](service-fabric-diagnostics-event-analysis-oms.md)
* [Documentação de EventFlow](https://github.com/Azure/diagnostics-eventflow)
