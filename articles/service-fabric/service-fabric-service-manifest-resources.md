---
title: "Especificar pontos finais de serviço do Service Fabric | Microsoft Docs"
description: "Como descrevem os recursos de ponto final no manifesto do serviço, incluindo como configurar pontos finais HTTPS"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: da36cbdb-6531-4dae-88e8-a311ab71520d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: subramar
ms.openlocfilehash: 615b758d6aa48f94ec8c9159d4f52e32f413c8d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="specify-resources-in-a-service-manifest"></a>Especificar recursos num manifesto do serviço
## <a name="overview"></a>Descrição geral
O manifesto de serviço permite que os recursos que são utilizados pelo serviço de ser declarado/alterar sem alterar o código compilado. Recursos de infraestrutura de serviço do Azure suporta a configuração dos recursos de ponto final para o serviço. O acesso a recursos que são especificados no manifesto de serviço pode ser controlado através de SecurityGroup no manifesto da aplicação. A declaração de recursos permite estes recursos para ser alterada no momento da implementação, o que significa que o serviço não tem de introduzir um mecanismo de configuração de novo. A definição de esquema para o ficheiro ServiceManifest.xml é instalada com o SDK de Service Fabric e ferramentas para *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Pontos Finais
Quando um recurso de ponto final está definido no manifesto de serviço, o Service Fabric atribui portas o intervalo de portas de aplicação reservado quando não está especificada uma porta explicitamente. Por exemplo, observe o ponto final *ServiceEndpoint1* especificado no manifesto fragmento fornecido após este parágrafo. Além disso, os serviços também podem solicitar uma porta específica num recurso. Réplicas de serviço em execução em nós de cluster diferentes podem ser atribuídas a números de porta diferente, enquanto as réplicas de um serviço em execução no mesmo nó de partilham a porta. As réplicas do serviço, em seguida, podem utilizar estas portas conforme for necessário para replicação e a escutar para pedidos de cliente.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Consulte [configurar Reliable Services com monitorização de estado](service-fabric-reliable-services-configuration.md) para ler mais sobre os pontos finais de referência de definições de configuração do pacote de ficheiros (settings.xml).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Exemplo: especificar um ponto final de HTTP para o seu serviço
O manifesto de serviço seguinte define um recurso de ponto final TCP e dois recursos de ponto final HTTP no &lt;recursos&gt; elemento.

Pontos finais de HTTP são automaticamente que ACL seria pelo Service Fabric.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Exemplo: especificar um ponto final de HTTPS para o seu serviço
O protocolo HTTPS fornece autenticação de servidor e também é utilizado para encriptar a comunicação cliente-servidor. Para ativar HTTPS no seu serviço do Service Fabric, especifique o protocolo no *recursos -> pontos finais -> ponto final* secção do manifesto do serviço, conforme mostrado anteriormente para o ponto final *ServiceEndpoint3*.

> [!NOTE]
> Protocolo de um serviço não pode ser alterado durante a atualização da aplicação. Se for alterada durante a atualização, é uma alteração inovadora.
> 
> 

Eis um exemplo ApplicationManifest tem de definir para HTTPS. O thumbprint do certificado tem de ser fornecido. O EndpointRef é uma referência a EndpointResource no ServiceManifest, para que definiu o protocolo HTTPS. Pode adicionar mais do que um EndpointCertificate.  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_ ]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

Para os clusters do Linux, o **MY** armazenar as predefinições para a pasta **/var/lib/sfcerts**.


## <a name="overriding-endpoints-in-servicemanifestxml"></a>Substituir pontos finais no ServiceManifest.xml

O ApplicationManifest adicione uma secção de ResourceOverrides que será colateral ConfigOverrides secção. Nesta secção, pode especificar a substituição para a secção de pontos finais na secção de recursos especificada no manifesto de serviço.

Para substituir o ponto final no ServiceManifest utilizando ApplicationParameters alteração o ApplicationManifest seguinte forma:

Na secção ServiceManifestImport adicionar uma nova secção "ResourceOverrides"

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
        <Endpoint Name="ServiceEndpoint1" Port="[Port1]" Protocol="[Protocol1] "/>
      </Endpoints>
    </ResourceOverrides>
        <Policies>
           <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint"/>
        </Policies>
  </ServiceManifestImport>
```

Nos parâmetros adicionar abaixo:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

Ao implementar a aplicação agora, pode passar estes valores como ApplicationParameters por exemplo:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Nota: Se os valores fornecidos para o ApplicationParameters está vazia, volte para o valor predefinido fornecido a ServiceManifest para o EndPointName correspondente.

Por exemplo:

Se, na ServiceManifest especificado

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

E o valor Port1 e Protocol1 para parâmetros de aplicação é nulo ou estar vazio. A porta ainda é decidida por ServiceFabric. E o protocolo de tcp será.

Suponha que especifica um valor incorreto. Como para a porta que especificou um valor de cadeia "Foo" em vez de um inteiro.  Novo ServiceFabricApplication comando irá falhar com um erro: O parâmetro de substituição com o atributo de 'ServiceEndpoint1' name 'Port1' na secção 'ResourceOverrides' é inválido. O valor especificado é "Foo", não sendo necessário 'int'.
