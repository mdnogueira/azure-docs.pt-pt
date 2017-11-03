---
title: "Depurar micro-serviços do Azure no Linux | Microsoft Docs"
description: "Saiba como monitorizar e diagnosticar os serviços escritos utilizando o Microsoft Azure Service Fabric numa máquina de desenvolvimento local."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 4eebe937-ab42-4429-93db-f35c26424321
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 4bc73f581f4855ebc724df19dd56fab8bf103854
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Monitorizar e diagnosticar os serviços de uma configuração de desenvolvimento do computador local


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

Monitorização, detetar, diagnosticar e resolver problemas permitem de serviços continuar com a mínima interrupção a experiência de utilizador. Monitorização e diagnóstico é crítico num ambiente de produção implementado real. Adotar um modelo semelhante durante o desenvolvimento de serviços assegura que o pipeline de diagnóstico funciona quando mover para um ambiente de produção. Service Fabric torna mais fácil para os programadores de serviço implementar o diagnóstico de forma totalmente integrada pode trabalhar em setups única máquina de desenvolvimento local e setups de cluster de produção do mundo real.


## <a name="debugging-service-fabric-java-applications"></a>A depuração de aplicações Java de recursos de infraestrutura de serviço

Para aplicações de Java, [várias arquiteturas de registo](http://en.wikipedia.org/wiki/Java_logging_framework) estão disponíveis. Uma vez que `java.util.logging` é a opção predefinida com o JRE, também é utilizado para o [exemplos em github de código](http://github.com/Azure-Samples/service-fabric-java-getting-started).  O debate da seguinte explica como configurar o `java.util.logging` framework.

Utilizar Util pode redirecionar os registos de aplicação para memória, as sequências de saída, ficheiros de consola ou sockets. Para cada uma destas opções, existem processadores predefinidos já são fornecidos no framework. Pode criar um `app.properties` ficheiros para configurar o processador de ficheiros para a sua aplicação redirecionar todos os registos para um ficheiro local.

O fragmento de código seguinte contém uma configuração de exemplo:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log             
```

A pasta indicada pelo `app.properties` ficheiro tem de existir. Depois do `app.properties` ficheiro é criado, tem também modificar o script de ponto de entrada, `entrypoint.sh` no `<applicationfolder>/<servicePkg>/Code/` pasta para definir a propriedade `java.util.logging.config.file` para `app.propertes` ficheiro. A entrada deverá existir o seguinte fragmento:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Esta configuração resulta em registos estão a ser recolhidos de forma rotating em `/tmp/servicefabric/logs/`. O ficheiro de registo neste caso é denominado mysfapp%u.%g.log onde:
* **%u** é um número exclusivo para resolver conflitos entre os processos de Java em simultâneo.
* **%g** é o número de geração para distinguir entre a rotação dos registos.

Por predefinição se nenhuma rotina de tratamento explicitamente estiver configurada, o processador de consola está registado. Um pode ver os registos do syslog em /var/log/syslog.

Para obter mais informações, consulte o [exemplos em github de código](http://github.com/Azure-Samples/service-fabric-java-getting-started).  


## <a name="debugging-service-fabric-c-applications"></a>Depurar aplicações de Service Fabric c#


Várias estruturas estão disponíveis para rastreio CoreCLR aplicações no Linux. Para obter mais informações, consulte [GitHub: registo](http:/github.com/aspnet/logging).  Uma vez que EventSource familiar para c# programadores,' Este artigo utiliza EventSource para o rastreio nos exemplos CoreCLR no Linux.

O primeiro passo é incluir System.Diagnostics.Tracing, de modo a que pode escrever os registos de memória, as sequências de saída ou ficheiros da consola.  Para o registo através de EventSource, adicione o projeto seguinte à sua project.json:

```
    "System.Diagnostics.StackTrace": "4.0.1"
```

Pode utilizar um EventListener personalizado para escutar para o evento de serviço e, em seguida, redirecioná-los adequadamente para ficheiros de rastreio. O fragmento de código seguinte mostra uma implementação de exemplo de início de sessão através de EventSource e um EventListener personalizado:


```csharp

 public class ServiceEventSource : EventSource
 {
        public static ServiceEventSource Current = new ServiceEventSource();

        [NonEvent]
        public void Message(string message, params object[] args)
        {
            if (this.IsEnabled())
            {
                var finalMessage = string.Format(message, args);
                this.Message(finalMessage);
            }
        }

        // TBD: Need to add method for sample event.

}

```


```csharp
   internal class ServiceEventListener : EventListener
   {

        protected override void OnEventSourceCreated(EventSource eventSource)
        {
            EnableEvents(eventSource, EventLevel.LogAlways, EventKeywords.All);
        }
        protected override void OnEventWritten(EventWrittenEventArgs eventData)
        {
            using (StreamWriter Out = new StreamWriter( new FileStream("/tmp/MyServiceLog.txt", FileMode.Append)))           
        { 
                 // report all event information               
         Out.Write(" {0} ",  Write(eventData.Task.ToString(), eventData.EventName, eventData.EventId.ToString(), eventData.Level,""));
                if (eventData.Message != null)              
            Out.WriteLine(eventData.Message, eventData.Payload.ToArray());              
            else             
        { 
                    string[] sargs = eventData.Payload != null ? eventData.Payload.Select(o => o.ToString()).ToArray() : null; 
                    Out.WriteLine("({0}).", sargs != null ? string.Join(", ", sargs) : "");             
        }
           }
        }
    }
```


O fragmento anterior produz os registos para um ficheiro no `/tmp/MyServiceLog.txt`. Este nome de ficheiro tem de ser corretamente atualizado. No caso de pretender redirecionar os registos para a consola, utilize o seguinte fragmento na sua classe EventListener personalizado:

```csharp
public static TextWriter Out = Console.Out;
```

Os exemplos em [amostras de c#](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) utilizar EventSource e um EventListener personalizada para eventos de registo para um ficheiro.



## <a name="next-steps"></a>Passos seguintes
O mesmo código de rastreio adicionado à sua aplicação também funciona com o diagnóstico da aplicação num cluster do Azure. Consulte estes artigos que abordam as diferentes opções para as ferramentas e descrevem como configurá-los.
* [Como recolher registos de diagnóstico do Azure](service-fabric-diagnostics-how-to-setup-lad.md)
