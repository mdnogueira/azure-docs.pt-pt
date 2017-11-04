---
title: "Converter aplicações Cloud Services do Azure micro-serviços | Microsoft Docs"
description: "Este guia compara sem monitorização de estado dos serviços Web de serviços de nuvem e as funções de trabalho e recursos de infraestrutura de serviço para o ajudar a migrar de serviços em nuvem para o Service Fabric."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 5880ebb3-8b54-4be8-af4b-95a1bc082603
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: d6dc1cddd6228d2841e1e77b6f2800f788e5e1bb
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="guide-to-converting-web-and-worker-roles-to-service-fabric-stateless-services"></a>Guia para converter Web e funções de trabalho para serviços sem monitorização de estado do Service Fabric
Este artigo descreve como migrar a sua nuvem serviços Web e funções de trabalho para serviços sem monitorização de estado do Service Fabric. Este é o mais simples caminho de migração de serviços em nuvem para o Service Fabric para as aplicações cuja arquitetura geral vai aproximadamente permaneça igual.

## <a name="cloud-service-project-to-service-fabric-application-project"></a>Projeto de serviço em nuvem para o projeto de aplicação de Service Fabric
 Um projeto de serviço em nuvem e de um projeto de aplicação de recursos de infraestrutura de serviço têm uma estrutura semelhante e ambos os representam a unidade de implementação para a sua aplicação - ou seja, que cada definem o pacote completo que é implementado para executar a sua aplicação. Um projeto de serviço em nuvem contém uma ou mais Web ou funções de trabalho. Da mesma forma, um projeto de aplicação de recursos de infraestrutura de serviço contém um ou mais serviços. 

A diferença é que o projeto de serviço em nuvem couples a implementação de aplicação com uma implementação de VM e, por conseguinte, contém definições de configuração de VM no mesmo, enquanto que o projeto de aplicação de recursos de infraestrutura de serviço define apenas uma aplicação que irá ser implementada para um conjunto de VMs existentes num cluster de Service Fabric. O próprio cluster do Service Fabric só é implementado uma vez, através de um modelo do Resource Manager ou através do portal do Azure, e várias aplicações de Service Fabric podem ser implementadas no mesmo.

![Comparação de projeto de Service Fabric e serviços em nuvem][3]

## <a name="worker-role-to-stateless-service"></a>Função de trabalho para o serviço sem monitorização de estado
Concecionais, uma função de trabalho representa uma carga de trabalho sem monitorização de estado, que significa que todas as instâncias da carga de trabalho é idêntica e os pedidos podem ser encaminhados para qualquer instância em qualquer altura. Não é esperada que cada instância Lembre-se o pedido anterior. Estado que a carga de trabalho opera em é gerido por um arquivo de estado externo, como o Table Storage do Azure ou do Azure Document DB. No Service Fabric, este tipo de carga de trabalho é representado por um serviço sem estado. A abordagem mais simples para migrar uma função de trabalho para o Service Fabric pode ser feita através de conversão de código da função de trabalho para um serviço sem estado.

![Função de trabalho para o serviço sem monitorização de estado][4]

## <a name="web-role-to-stateless-service"></a>Função da Web para o serviço sem monitorização de estado
Semelhante à função de trabalho, uma função da Web também representa uma carga de trabalho sem monitorização de estado e, por isso, essencialmente, este demasiado pode ser mapeado para um serviço sem monitorização de estado de Service Fabric. No entanto, ao contrário das funções da Web, Service Fabric não suportam o IIS. Para migrar uma web aplicação de uma função da Web para um serviço sem estado necessita de mover primeiro para uma arquitetura de web que pode ser autoalojada e não dependem do IIS ou System. Web, tal como ASP.NET Core 1.

| **Aplicação** | **Suportado** | **Caminho de migração** |
| --- | --- | --- |
| Formulários ASP.NET Web |Não |Converter em MVC do ASP.NET Core 1 |
| ASP.NET MVC |Com a migração |Atualização para o ASP.NET Core 1 MVC |
| ASP.NET Web API |Com a migração |Utilizar um servidor personalizada alojado ou ASP.NET Core 1 |
| ASP.NET Core 1 |Sim |N/D |

## <a name="entry-point-api-and-lifecycle"></a>API de ponto de entrada e de ciclo de vida
Pontos de entrada de semelhantes de oferta APIs do serviço de função de trabalho e recursos de infraestrutura do serviço: 

| **Ponto de entrada** | **Função de trabalho** | **Serviço do Service Fabric** |
| --- | --- | --- |
| Processamento |`Run()` |`RunAsync()` |
| Iniciar VM |`OnStart()` |N/D |
| Pare a VM |`OnStop()` |N/D |
| Serviço de escuta aberto para pedidos de cliente |N/D |<ul><li> `CreateServiceInstanceListener()`para sem monitorização de estado</li><li>`CreateServiceReplicaListener()`para monitorização de estado</li></ul> |

### <a name="worker-role"></a>Função de trabalho
```C#

using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
        }

        public override bool OnStart()
        {
        }

        public override void OnStop()
        {
        }
    }
}

```

### <a name="service-fabric-stateless-service"></a>Serviço sem monitorização de estado do serviço de recursos de infraestrutura
```C#

using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace Stateless1
{
    public class Stateless1 : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
        }

        protected override Task RunAsync(CancellationToken cancelServiceInstance)
        {
        }
    }
}

```

Têm uma substituição de principal "executar" na qual pretende iniciar o processamento. Combinação de serviços do Service Fabric `Run`, `Start`, e `Stop` para um ponto de entrada único, `RunAsync`. O serviço deve começar a trabalhar quando `RunAsync` inicia e devem parar de funcionar quando o `RunAsync` CancellationToken do método está marcado. 

Existem vários principais diferenças entre o ciclo de vida e a duração dos serviços de funções de trabalho e recursos de infraestrutura do serviço:

* **Ciclo de vida:** a maior diferença é que uma função de trabalho é uma VM e, por isso, o ciclo de vida está associado à VM, que inclui eventos para quando a VM é iniciado e deixa de. Um serviço do Service Fabric tem um ciclo de vida que é separado do ciclo de vida VM, pelo que não inclui eventos para quando o anfitrião, VM ou o computador é iniciado e para, que não estão relacionadas.
* **Duração:** uma instância de função de trabalho reciclará se o `Run` sai de método. O `RunAsync` método num serviço Service Fabric no entanto pode executar para conclusão e a instância de serviço permanecerão cópias de segurança. 

O Service Fabric fornece um ponto de entrada de configuração de comunicação opcionais para serviços que escutam para pedidos de cliente. O runasync com e a comunicação de ponto de entrada são opcionais substituições nos serviços de Service Fabric - o serviço poderá optar por apenas escutar para pedidos de cliente ou executar apenas um ciclo de processamento ou ambos -, que é a razão pela qual o método runasync com é permitido para sair sem reiniciar a instância de serviço, porque pode continuar para escutar os pedidos de cliente.

## <a name="application-api-and-environment"></a>Aplicação API e ambiente
O ambiente de serviços em nuvem API fornece informações e a funcionalidade para a instância atual do VM, bem como informações sobre outras instâncias de função da VM. O Service Fabric fornece informações relacionadas com o tempo de execução e algumas informações sobre o nó de um serviço está em execução. 

| **Tarefas do ambiente** | **Serviços Cloud** | **Service Fabric** |
| --- | --- | --- |
| Definições de configuração e a notificação de alteração |`RoleEnvironment` |`CodePackageActivationContext` |
| Armazenamento Local |`RoleEnvironment` |`CodePackageActivationContext` |
| Informações de ponto final |`RoleInstance` <ul><li>Instância atual:`RoleEnvironment.CurrentRoleInstance`</li><li>Outras funções e instância:`RoleEnvironment.Roles`</li> |<ul><li>`NodeContext`para o endereço do nó atual</li><li>`FabricClient`e `ServicePartitionResolver` para a deteção de ponto final de serviço</li> |
| Ambiente emulação |`RoleEnvironment.IsEmulated` |N/D |
| Evento de alteração em simultâneo |`RoleEnvironment` |N/D |

## <a name="configuration-settings"></a>Definições de configuração
Definições de configuração nos serviços em nuvem estão definidas para uma função VM e aplicam-se a todas as instâncias dessa função de VM. Estas definições são pares chave-valor definidas nos ficheiros de ServiceConfiguration.*.cscfg e podem ser acedidas diretamente através do RoleEnvironment. No Service Fabric, as definições se aplicam individualmente para cada serviço e para cada aplicação, em vez de uma VM, porque uma VM pode alojar vários serviços e aplicações. Um serviço é composto por três pacotes:

* **Código:** contém executáveis o serviço, os binários, DLLs e quaisquer outros ficheiros que necessita de um serviço para executar.
* **Configuração:** todos os ficheiros de configuração e definições para um serviço.
* **Dados:** ficheiros estáticos dados associados ao serviço.

Cada um destes pacotes pode ser independentemente com versão e atualizado. Semelhante aos serviços em nuvem, o pacote de configuração pode ser acedido através de programação através de uma API e eventos estão disponíveis para notificar o serviço de uma alteração de pacote de configuração. Um ficheiro de Settings.xml pode ser utilizado para a configuração de chave-valor e acesso programático semelhante à secção de definições da aplicação de um ficheiro App. config. No entanto, ao contrário dos serviços em nuvem, um pacote de configuração do Service Fabric pode conter quaisquer ficheiros de configuração em qualquer formato, se se trata de XML, JSON, YAML ou um formato binário personalizado. 

### <a name="accessing-configuration"></a>Aceder à configuração
#### <a name="cloud-services"></a>Serviços Cloud
Definições de configuração dos ServiceConfiguration.*.cscfg podem ser acedidas através de `RoleEnvironment`. Estas definições são globalmente disponíveis para todas as instâncias de função na mesma implementação do serviço em nuvem.

```C#

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### <a name="service-fabric"></a>Service Fabric
Cada serviço tem o seu próprio pacote de configuração individuais. Não há nenhum mecanismo incorporado para definições de configuração globais acessível por todas as aplicações num cluster. Ao utilizar o especial Settings.xml ficheiro de configuração Service Fabric dentro de um pacote de configuração, os valores em Settings.xml podem ser substituídos ao nível da aplicação, tornar as definições de configuração de nível de aplicação possíveis.

Definições de configuração são acessos dentro de cada instância de serviço através do serviço `CodePackageActivationContext`.

```C#

ConfigurationPackage configPackage = this.Context.CodePackageActivationContext.GetConfigurationPackageObject("Config");

// Access Settings.xml
KeyedCollection<string, ConfigurationProperty> parameters = configPackage.Settings.Sections["MyConfigSection"].Parameters;

string value = parameters["Key"]?.Value;

// Access custom configuration file:
using (StreamReader reader = new StreamReader(Path.Combine(configPackage.Path, "CustomConfig.json")))
{
    MySettings settings = JsonConvert.DeserializeObject<MySettings>(reader.ReadToEnd());
}

```

### <a name="configuration-update-events"></a>Eventos de configuração da atualização
#### <a name="cloud-services"></a>Serviços Cloud
O `RoleEnvironment.Changed` eventos é utilizado para notificar todas as instâncias de função quando ocorre uma alteração no ambiente, como uma alteração de configuração. Isto é utilizado para consumir atualizações de configuração sem Reciclagem instâncias de função ou reiniciar um processo de trabalho.

```C#

RoleEnvironment.Changed += RoleEnvironmentChanged;

private void RoleEnvironmentChanged(object sender, RoleEnvironmentChangedEventArgs e)
{
   // Get the list of configuration changes
   var settingChanges = e.Changes.OfType<RoleEnvironmentConfigurationSettingChange>();
foreach (var settingChange in settingChanges) 
   {
      Trace.WriteLine("Setting: " + settingChange.ConfigurationSettingName, "Information");
   }
}

```

#### <a name="service-fabric"></a>Service Fabric
Cada um dos tipos de três pacote num serviço - código, configuração e dados - ter eventos notificar uma instância de serviço, quando um pacote é atualizado, adicionado ou removido. Um serviço pode conter vários pacotes de cada tipo. Por exemplo, um serviço pode ter vários pacotes de configuração, cada individualmente com versão e actualizável. 

Estes eventos estão disponíveis para consumir alterações nos pacotes de service sem reiniciar a instância de serviço.

```C#

this.Context.CodePackageActivationContext.ConfigurationPackageModifiedEvent +=
                    this.CodePackageActivationContext_ConfigurationPackageModifiedEvent;

private void CodePackageActivationContext_ConfigurationPackageModifiedEvent(object sender, PackageModifiedEventArgs<ConfigurationPackage> e)
{
    this.UpdateCustomConfig(e.NewPackage.Path);
    this.UpdateSettings(e.NewPackage.Settings);
}

```

## <a name="startup-tasks"></a>Tarefas de arranque
Tarefas de arranque são ações efetuadas antes de iniciar uma aplicação. Uma tarefa de arranque é normalmente utilizada para executar scripts de configuração com privilégios elevados. Serviços em nuvem e de Service Fabric suportam tarefas de arranque. A principal diferença é que nos serviços em nuvem, uma tarefa de arranque está associada a uma VM porque faz parte de uma instância de função, enquanto no Service Fabric uma tarefa de arranque está associada a um serviço, que não está associado a qualquer VM específica.

| Serviços Cloud | Service Fabric |
| --- | --- | --- |
| Localização de configuração |Servicedefinition. Csdef |
| Privilégios |"limitado" ou "elevados" |
| Sequenciação |"simples", "em segundo plano", "em primeiro plano" |

### <a name="cloud-services"></a>Serviços Cloud
Nos serviços em nuvem está configurado um ponto de entrada de arranque por função no servicedefinition. Csdef. 

```xml

<ServiceDefinition>
    <Startup>
        <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
            <Environment>
                <Variable name="MyVersionNumber" value="1.0.0.0" />
            </Environment>
        </Task>
    </Startup>
    ...
</ServiceDefinition>

```

### <a name="service-fabric"></a>Service Fabric
No Service Fabric está configurado um ponto de entrada de arranque por serviço no ServiceManifest.xml:

```xml

<ServiceManifest>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Startup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    ...
</ServiceManifest>

``` 

## <a name="a-note-about-development-environment"></a>Nota sobre o ambiente de desenvolvimento
Serviços em nuvem e de recursos de infraestrutura de serviço estão integradas com o Visual Studio com modelos de projeto e suporte para depuração, configurar e implementar ambos localmente e para o Azure. Serviços em nuvem e de Service Fabric também de fornecer um ambiente de tempo de execução de desenvolvimento local. A diferença é que, enquanto o tempo de execução de desenvolvimento do serviço em nuvem emula o ambiente do Azure no qual é executado, Service Fabric não utiliza um emulador - utiliza o tempo de execução do Service Fabric concluído. O ambiente do Service Fabric que executar no seu computador de desenvolvimento local é o mesmo ambiente, que é executado na produção.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o serviço de recursos de infraestrutura Reliable Services e as diferenças fundamentais entre serviços em nuvem e arquitetura de aplicação de Service Fabric para compreender melhor como tirar partido do conjunto completo de funcionalidades do Service Fabric.

* [Introdução ao serviço de recursos de infraestrutura Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Guia conceptual para as diferenças entre os serviços em nuvem e de Service Fabric](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png
