---
title: "Nível de aplicação de recursos de infraestrutura de serviço do Azure monitorização | Microsoft Docs"
description: "Saiba mais sobre os registos de aplicações e eventos de nível de serviço e utilizados para monitorizar e diagnosticar os clusters de Service Fabric do Azure."
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
ms.openlocfilehash: 9b07c7cd256cea31c5654f0c3325e9fb675b39f8
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="application-and-service-level-event-and-log-generation"></a>Aplicação e nível de serviço geração de eventos e de registo

## <a name="instrumenting-the-code-with-custom-events"></a>Instrumentação de código com eventos personalizados

Instrumentação o código é a base para a maioria dos outros aspetos dos seus serviços de monitorização. Instrumentação é a única forma que pode saber que algo está errado e para diagnosticar o que tem de ser corrigidos. Embora seja tecnicamente possível ligar um depurador a um serviço de produção, não é uma prática comum. Por isso, ter detalhadas dados de instrumentação é importante.

Alguns produtos instrumentem automaticamente o seu código. Apesar destas soluções podem funcionar bem, instrumentação manual é quase sempre necessária. No final, tem de ter informações suficientes para forensically depurar a aplicação. Este documento descreve diferentes abordagens para instrumentar o seu código e escolher uma abordagem em vez de outro.

## <a name="eventsource"></a>EventSource
Quando cria uma solução de Service Fabric a partir de um modelo no Visual Studio, uma **EventSource**-classe derivada (**ServiceEventSource** ou **ActorEventSource**) é gerado . Um modelo é criado, na qual pode adicionar eventos para a aplicação ou serviço. O **EventSource** nome **tem** de ser exclusivo e deve ser alterado de cadeia de modelo predefinido aminhaempresa -&lt;solução&gt;-&lt;projeto &gt;. Ter vários **EventSource** definições que utilizam o mesmo nome provocar um problema em tempo de execução. Cada evento definido tem de ter um identificador exclusivo. Se um identificador não for exclusivo, ocorre uma falha de tempo de execução. Algumas organizações preassign intervalos de valores para os identificadores evitar conflitos entre as equipas de desenvolvimento separados. Para obter mais informações, consulte [blogue de Vance](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) ou [documentação MSDN](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

### <a name="using-structured-eventsource-events"></a>A utilização de eventos de EventSource estruturados

Cada um dos eventos os exemplos de código nesta secção são definidos para um cenário específico, por exemplo, quando é registado um tipo de serviço. Quando definir mensagens por caso de utilização, dados podem ser compactados com o texto do erro e pode mais facilmente procurar e filtrar baseado nos nomes ou valores das propriedades especificados. Structuring faz com saída de instrumentação que facilitam a consumir, mas requer mais profundamente e tempo para definir um novo evento para cada caso de utilização. Algumas definições de evento podem ser partilhadas entre a aplicação completa. Por exemplo, um início de método ou parar eventos deverá ser reutilizados em vários serviços dentro de uma aplicação. Um serviço de domínio específico, como um sistema de ordem, pode ter um **CreateOrder** evento, o que tem o seu próprio evento exclusivo. Esta abordagem poderá gerar muitos eventos e potencialmente requerem a coordenação de identificadores entre equipas de projeto. 

```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // The instance constructor is private to enforce singleton semantics.
        private ServiceEventSource() : base() { }

        ...

        // The ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
        private const int ServiceTypeRegisteredEventId = 3;
        [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
        public void ServiceTypeRegistered(int hostProcessId, string serviceType)
        {
            WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
        }

        // The ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
        private const int ServiceHostInitializationFailedEventId = 4;
        [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
        public void ServiceHostInitializationFailed(string exception)
        {
            WriteEvent(ServiceHostInitializationFailedEventId, exception);
        }
```

### <a name="using-eventsource-generically"></a>Utilizar EventSource genericamente

Como definir eventos específicos pode ser difícil, muitas pessoas definem alguns eventos com um conjunto comum de parâmetros que geralmente as respetivas informações como uma cadeia de saída. Grande parte do aspeto estruturado perde-se, e é mais difícil procurar e filtrar os resultados. Esta abordagem, alguns eventos que normalmente correspondem aos níveis de registo são definidos. O fragmento seguinte define uma mensagem de erro e de depuração:

```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // The Instance constructor is private, to enforce singleton semantics.
        private ServiceEventSource() : base() { }

        ...

        private const int DebugEventId = 10;
        [Event(DebugEventId, Level = EventLevel.Verbose, Message = "{0}")]
        public void Debug(string msg)
        {
            WriteEvent(DebugEventId, msg);
        }

        private const int ErrorEventId = 11;
        [Event(ErrorEventId, Level = EventLevel.Error, Message = "Error: {0} - {1}")]
        public void Error(string error, string msg)
        {
            WriteEvent(ErrorEventId, error, msg);
        }
```

Utilizar uma versão híbrida do estruturados e genérica instrumentação também pode funcionar bem. Instrumentação Structured é utilizada para relatórios de erros e de métricas. Eventos genéricos podem ser utilizados para o registo de detalhado que é consumido pelo engenheiros para resolução de problemas.

## <a name="aspnet-core-logging"></a>O registo do ASP.NET Core

É importante planear cuidadosamente como irá instrumentar o seu código. O plano de instrumentação direita pode ajudar a evitar potencialmente destabilizing a base de código e, em seguida, necessitar de reinstrument o código. Para reduzir o risco, pode escolher uma biblioteca de instrumentação como [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), que faz parte do Microsoft ASP.NET Core. ASP.NET Core tem um [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) interface que pode utilizar com o fornecedor da sua escolha, para minimizar o efeito no código existente. Pode utilizar o código ASP.NET Core no Windows e Linux e, no .NET Framework completa, por isso, o código de instrumentação está padronizado. Isto é mais explorou abaixo:

### <a name="using-microsoftextensionslogging-in-service-fabric"></a>Utilizar Microsoft.Extensions.Logging no Service Fabric

1. Adicione o pacote Microsoft.Extensions.Logging NuGet ao projeto que pretende instrumento. Além disso, adicione quaisquer pacotes de fornecedor (para um pacote de terceiros, consulte o exemplo seguinte). Para obter mais informações, consulte [iniciar sessão ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).
2. Adicionar um **utilizando** directiva para Microsoft.Extensions.Logging ao seu ficheiro de serviço.
3. Defina uma variável privada dentro da sua classe de serviço.

  ```csharp
  private ILogger _logger = null;

  ```
4. O construtor de classe de serviço, adicione este código:

  ```csharp
  _logger = new LoggerFactory().CreateLogger<Stateless>();

  ```
5. Inicie a instrumentação o código no seu métodos. Seguem-se alguns exemplos:

  ```csharp
  _logger.LogDebug("Debug-level event from Microsoft.Logging");
  _logger.LogInformation("Informational-level event from Microsoft.Logging");

  // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
  // Later in the article, we discuss why this step is useful.
  _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);

  ```

## <a name="using-other-logging-providers"></a>Utilizar outros fornecedores de registo

Alguns fornecedores de terceiros utilizar a abordagem descrita na secção anterior, incluindo [Serilog](https://serilog.net/), [NLog](http://nlog-project.org/), e [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Pode ligue cada um no registo do ASP.NET Core ou, pode utilizá-las separadamente. Serilog tem uma funcionalidade que otimiza todas as mensagens enviadas a partir de um registo. Esta funcionalidade pode ser útil para o nome do serviço, tipo e informações da partição de saída. Para utilizar esta capacidade na infraestrutura do ASP.NET Core, execute estes passos:

1. Adicione os pacotes Serilog, Serilog.Extensions.Logging e Serilog.Sinks.Observable NuGet para o projeto. Para o exemplo seguinte, adicione também Serilog.Sinks.Literate. Uma abordagem de melhor é mostrada posteriormente neste artigo.
2. Serilog, crie um LoggerConfiguration e a instância de registo.

  ```csharp
  Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
  ```

3. Adicione um argumento de Serilog.ILogger para o construtor de serviço e passar o registo recentemente criado.

  ```csharp
  ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
  ```

4. O construtor de serviço, adicione o seguinte código, que cria os enrichers de propriedade para o **ServiceTypeName**, **ServiceName**, **PartitionId**e **InstanceId** propriedades do serviço. Também adiciona um enricher de propriedade para a fábrica de registo do ASP.NET Core, pelo que pode utilizar Microsoft.Extensions.Logging.ILogger no seu código.

  ```csharp
  public Stateless(StatelessServiceContext context, Serilog.ILogger serilog)
      : base(context)
  {
      PropertyEnricher[] properties = new PropertyEnricher[]
      {
          new PropertyEnricher("ServiceTypeName", context.ServiceTypeName),
          new PropertyEnricher("ServiceName", context.ServiceName),
          new PropertyEnricher("PartitionId", context.PartitionId),
          new PropertyEnricher("InstanceId", context.ReplicaOrInstanceId),
      };

      serilog.ForContext(properties);

      _logger = new LoggerFactory().AddSerilog(serilog.ForContext(properties)).CreateLogger<Stateless>();
  }
  ```

5. Instrumente o código a mesma como se estava a utilizar o ASP.NET Core sem Serilog.

  >[!NOTE]
  >Recomendamos que não utilize o Log.Logger estático com o exemplo anterior. Service Fabric pode alojar várias instâncias do mesmo tipo de serviço dentro de um único processo. Se utilizar o Log.Logger estáticos, ao último escritor de enrichers a propriedade irá apresentar valores para todas as instâncias que estão em execução. Este é um motivo por que razão a variável de _logger é uma variável de membro privado da classe de serviço. Além disso, tem de se o _logger disponíveis para código comum, que pode ser utilizado em serviços.

## <a name="choosing-a-logging-provider"></a>Escolher um fornecedor de registo

Se a aplicação depende de elevado desempenho, **EventSource** é normalmente uma boa abordagem. **EventSource** *geralmente* utiliza menos recursos e efetua melhor do que o registo do ASP.NET Core ou qualquer uma das soluções de terceiros disponíveis.  Esta operação não é um problema para vários serviços, mas se o seu serviço é desempenho orientados, utilizando **EventSource** poderá ser uma melhor opção. No entanto obter estas vantagens do estruturados registo, **EventSource** requer um investimento superior da sua equipa de engenharia. Se for possível, não um protótipo rápido de algumas opções de registo e, em seguida, escolha a que melhor se adeque às suas necessidades.

## <a name="next-steps"></a>Passos seguintes

Depois de escolher o fornecedor de registo instrumentem os seus serviços e aplicações, os registos e eventos precisam para serem agregados antes que podem ser enviadas para qualquer plataforma de análise. Leia sobre [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) e [WAD](service-fabric-diagnostics-event-aggregation-wad.md) para melhor compreender algumas das opções recomendadas.
