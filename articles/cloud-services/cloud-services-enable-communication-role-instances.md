---
title: "Comunicação para funções nos serviços em nuvem | Microsoft Docs"
description: "Instâncias de função nos serviços em nuvem podem ter pontos finais (http, https, tcp, udp) definidos para os mesmos que comunicam com exterior ou entre outras instâncias de função."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 7008a083-acbe-4fb8-ae60-b837ef971ca1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: adegeo
ms.openlocfilehash: 8e171d56bb67c971337fa383014988074ec828b1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-communication-for-role-instances-in-azure"></a>Ativar a comunicação para instâncias de função no azure
Funções de serviço na nuvem comunicam através de ligações internas e externas. Ligações externas são denominadas **pontos finais de entrada** enquanto são denominadas ligações internas **pontos finais internos**. Este tópico descreve como modificar o [definição de serviço](cloud-services-model-and-package.md#csdef) para criar pontos finais.

## <a name="input-endpoint"></a>Ponto final de entrada
O ponto final de entrada é utilizado quando pretende expor uma porta para o exterior. Especifique o tipo de protocolo e a porta do ponto final que, em seguida, aplica-se para portas internas e externas para o ponto final. Se quiser, pode especificar uma porta diferente interna para o ponto final com o [localPort](https://msdn.microsoft.com/library/azure/gg557552.aspx#InputEndpoint) atributo.

O ponto final de entrada, pode utilizar os seguintes protocolos: **http, https, tcp, udp**.

Para criar um ponto final de entrada, adicione o **InputEndpoint** elemento subordinado para o **pontos finais** elemento da função de um web ou trabalho.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Ponto final de entrada de instância
Pontos finais de entrada de instância são semelhantes à entrada pontos finais, mas permite-lhe mapear portas públicas específicas para cada instância de função individuais utilizando o reencaminhamento de porta no balanceador de carga. Pode especificar uma única porta destinado ao público, ou um intervalo de portas.

O ponto de final de entrada de instância só pode utilizar **tcp** ou **udp** como o protocolo.

Para criar um ponto de final de entrada de instância, adicione o **InstanceInputEndpoint** elemento subordinado para o **pontos finais** elemento da função de um web ou trabalho.

```xml
<Endpoints>
  <InstanceInputEndpoint name="Endpoint2" protocol="tcp" localPort="10100">
    <AllocatePublicPortFrom>
      <FixedPortRange max="10109" min="10105" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
</Endpoints>
```

## <a name="internal-endpoint"></a>Ponto final interno
Pontos finais internos estão disponíveis para a comunicação de instância de instância. A porta é opcional e, se for omitido uma porta dinâmica está atribuída ao ponto final. Pode ser utilizado um intervalo de portas. Não há um limite de cinco pontos finais internos por função.

O ponto final interno pode utilizar os seguintes protocolos: **http, tcp, udp, qualquer**.

Para criar um ponto final de entrada interno, adicione o **InternalEndpoint** elemento subordinado para o **pontos finais** elemento da função de um web ou trabalho.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" port="8999" />
</Endpoints> 
```

Também pode utilizar um intervalo de portas.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any">
    <FixedPortRange max="8995" min="8999" />
  </InternalEndpoint>
</Endpoints>
```


## <a name="worker-roles-vs-web-roles"></a>Vs de funções de trabalho. Funções da Web
Há uma diferença secundária com pontos finais ao trabalhar com trabalho e funções da web. A função da web tem de ter no mínimo um único ponto final de entrada, utilizando o **HTTP** protocolo.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>Utilizando o SDK .NET para aceder a um ponto final
A biblioteca gerida do Azure fornece métodos para instâncias de função comunicar em tempo de execução. A partir do código em execução dentro de uma instância de função, pode obter informações sobre a existência de outras instâncias de função e os respetivos pontos finais, bem como informações sobre a instância de função atual.

> [!NOTE]
> Apenas pode obter informações sobre as instâncias de função são executadas no seu serviço de nuvem e de que define o ponto final interno, pelo menos, um. Não é possível obter dados sobre instâncias de função em execução num serviço diferente.
> 
> 

Pode utilizar o [instâncias](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.role.instances.aspx) propriedade obter as instâncias de uma função. Primeiro utilizar o [CurrentRoleInstance](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.currentroleinstance.aspx) é devolvida uma referência para a instância de função atual e, em seguida, utilizar o [função](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.role.aspx) propriedade para devolver uma referência à função de si próprio.

Quando se liga a uma instância de função através de programação através do SDK do .NET, é relativamente fácil de aceder às informações de ponto final. Por exemplo, depois de já tiver ligado a um ambiente de função específico, pode obter a porta de um ponto final específico com este código:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

O **instâncias** propriedade devolve uma coleção de **RoleInstance** objetos. Esta coleção sempre contém a instância atual. Se a função não definir um ponto final interno, a coleção inclui a instância atual, mas não existem outras instâncias. O número de instâncias de função na coleção será sempre 1 nos casos em que nenhum ponto final interno está definido para a função. Se a função define um ponto final interno, suas instâncias são Detetáveis no tempo de execução e o número de instâncias da coleção será corresponde ao número de instâncias especificado para a função no ficheiro de configuração do serviço.

> [!NOTE]
> A biblioteca gerida do Azure fornece um meio de determinar o estado de funcionamento de outras instâncias de função, mas pode implementar as avaliações de estado de funcionamento manualmente se o serviço tem esta funcionalidade. Pode utilizar [diagnósticos do Azure](cloud-services-dotnet-diagnostics.md) para obter informações sobre como executar instâncias de função.
> 
> 

Para determinar o número de porta para um ponto final interno numa instância de função, pode utilizar o [InstanceEndpoints](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.instanceendpoints.aspx) propriedade para devolver um objeto de dicionário que contém nomes de ponto final e os respetivos correspondente IP endereços e portas. O [IPEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstanceendpoint.ipendpoint.aspx) propriedade devolve o endereço IP e a porta para um ponto final especificado. O **PublicIPEndpoint** propriedade devolve a porta para um ponto final com balanceamento de carga. A parte do endereço IP do **PublicIPEndpoint** propriedade não é utilizada.

Eis um exemplo que itera instâncias de função.

```csharp
foreach (RoleInstance roleInst in RoleEnvironment.CurrentRoleInstance.Role.Instances)
{
    Trace.WriteLine("Instance ID: " + roleInst.Id);
    foreach (RoleInstanceEndpoint roleInstEndpoint in roleInst.InstanceEndpoints.Values)
    {
        Trace.WriteLine("Instance endpoint IP address and port: " + roleInstEndpoint.IPEndpoint);
    }
}
```

Eis um exemplo de uma função de trabalho que obtém o ponto final exposto através da definição de serviço e começa a escutar ligações.

> [!WARNING]
> Este código irá funcionar apenas para um serviço implementado. Quando em execução no emulador de computação do Azure, os elementos de configuração que criar pontos finais de porta direta do serviço (**InstanceInputEndpoint** elementos) são ignorados.
> 
> 

```csharp
using System;
using System.Diagnostics;
using System.Linq;
using System.Net;
using System.Net.Sockets;
using System.Threading;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.Diagnostics;
using Microsoft.WindowsAzure.ServiceRuntime;
using Microsoft.WindowsAzure.StorageClient;

namespace WorkerRole1
{
  public class WorkerRole : RoleEntryPoint
  {
    public override void Run()
    {
      try
      {
        // Initialize method-wide variables
        var epName = "Endpoint1";
        var roleInstance = RoleEnvironment.CurrentRoleInstance;

        // Identify direct communication port
        var myPublicEp = roleInstance.InstanceEndpoints[epName].PublicIPEndpoint;
        Trace.TraceInformation("IP:{0}, Port:{1}", myPublicEp.Address, myPublicEp.Port);

        // Identify public endpoint
        var myInternalEp = roleInstance.InstanceEndpoints[epName].IPEndpoint;

        // Create socket listener
        var listener = new Socket(
          myInternalEp.AddressFamily, SocketType.Stream, ProtocolType.Tcp);

        // Bind socket listener to internal endpoint and listen
        listener.Bind(myInternalEp);
        listener.Listen(10);
        Trace.TraceInformation("Listening on IP:{0},Port: {1}",
          myInternalEp.Address, myInternalEp.Port);

        while (true)
        {
          // Block the thread and wait for a client request
          Socket handler = listener.Accept();
          Trace.TraceInformation("Client request received.");

          // Define body of socket handler
          var handlerThread = new Thread(
            new ParameterizedThreadStart(h =>
            {
              var socket = h as Socket;
              Trace.TraceInformation("Local:{0} Remote{1}",
                socket.LocalEndPoint, socket.RemoteEndPoint);

              // Shut down and close socket
              socket.Shutdown(SocketShutdown.Both);
              socket.Close();
            }
          ));

          // Start socket handler on new thread
          handlerThread.Start(handler);
        }
      }
      catch (Exception e)
      {
        Trace.TraceError("Caught exception in run. Details: {0}", e);
      }
    }

    public override bool OnStart()
    {
      // Set the maximum number of concurrent connections 
      ServicePointManager.DefaultConnectionLimit = 12;

      // For information on handling configuration changes
      // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.
      return base.OnStart();
    }
  }
}
```

## <a name="network-traffic-rules-to-control-role-communication"></a>Regras de tráfego de rede para controlar a comunicação de função
Depois de definir pontos finais internos, pode adicionar regras de tráfego de rede (baseadas em pontos finais que criou) para controlar a forma como instâncias de função podem comunicar entre si. O diagrama seguinte mostra alguns cenários comuns para controlar a comunicação de função:

![Cenários de regras de tráfego de rede](./media/cloud-services-enable-communication-role-instances/scenarios.png "cenários de regras de tráfego de rede")

Exemplo de código seguinte mostra as definições de função para as funções mostradas no diagrama anterior. Cada definição de função inclui, pelo menos, um ponto final interno definido:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalTCP1" protocol="tcp" />
    </Endpoints>
  </WebRole>
  <WorkerRole name="WorkerRole1">
    <Endpoints>
      <InternalEndpoint name="InternalTCP2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
  <WorkerRole name="WorkerRole2">
    <Endpoints>
      <InternalEndpoint name="InternalTCP3" protocol="tcp" />
      <InternalEndpoint name="InternalTCP4" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

> [!NOTE]
> Restrição de comunicação entre funções pode ocorrer com pontos finais internos de ambos fixed e -lhe automaticamente atribuídas as portas.
> 
> 

Por predefinição, depois de um ponto final interno está definido, comunicação pode fluir de qualquer função para o ponto final interno de uma função sem restrições. Para restringir a comunicação, tem de adicionar um **NetworkTrafficRules** elemento para que o **ServiceDefinition** elemento no ficheiro de definição de serviço.

### <a name="scenario-1"></a>Cenário 1
Permitir apenas o tráfego de rede do **WebRole1** para **WorkerRole1**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-2"></a>Cenário 2
Permite apenas tráfego de rede do **WebRole1** para **WorkerRole1** e **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-3"></a>Cenário 3
Permite apenas tráfego de rede do **WebRole1** para **WorkerRole1**, e **WorkerRole1** para **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-4"></a>Cenário 4
Permite apenas tráfego de rede do **WebRole1** para **WorkerRole1**, **WebRole1** para **WorkerRole2**, e **WorkerRole1** para **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP4" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

É possível localizar uma referência de esquema XML para os elementos utilizado acima [aqui](https://msdn.microsoft.com/library/azure/gg557551.aspx).

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o serviço em nuvem [modelo](cloud-services-model-and-package.md).

