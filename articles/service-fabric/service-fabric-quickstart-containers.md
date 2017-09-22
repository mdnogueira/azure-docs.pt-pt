---
title: "Criar uma aplicação de contentor do Windows no Azure Service Fabric | Microsoft Docs"
description: "Crie a sua primeira aplicação de contentor do Windows no Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: c4f8c94e23a165b22533ffd74e04c9a7310f2d22
ms.contentlocale: pt-pt
ms.lasthandoff: 09/07/2017

---

# <a name="deploy-a-service-fabric-windows-container-application-on-azure"></a>Implementar uma aplicação de contentor do Windows do Service Fabric no Azure
O Azure Service Fabric é uma plataforma de sistemas distribuídos par implementar e gerir microsserviços e contentores dimensionáveis e fiáveis. 

Para executar uma aplicação existente num contentor do Windows num cluster do Service Fabric, não precisa de fazer quaisquer alterações à sua aplicação. Este guia de início rápido mostra como implementar uma imagem de contentor do Docker pré-concebida numa aplicação do Service Fabric. Quando tiver terminado, terá em funcionamento um contentor do Windows Server 2016 Nano Server e do IIS. Este guia de início rápido descreve a implementação de um contentor do Windows. Leia [este início rápido](service-fabric-quickstart-containers-linux.md) para implementar um contentor do Linux.

![Página Web do IIS predefinida][iis-default]

Com este início rápido, vai aprender a:
> [!div class="checklist"]
> * Empacotar um contentor de imagens do Docker
> * Configurar a comunicação
> * Criar e empacotar a aplicação do Service Fabric
> * Implementar a aplicação de contentor no Azure

## <a name="prerequisites"></a>Pré-requisitos
* Uma subscrição do Azure (pode criar uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)).
* Um computador de programação com:
  * Visual Studio 2015 ou Visual Studio 2017.
  * [SDK e ferramentas do Service Fabric](service-fabric-get-started.md).

## <a name="package-a-docker-image-container-with-visual-studio"></a>Empacotar um contentor de imagens do Docker com o Visual Studio
O SDK e as ferramentas do Service Fabric fornecem um modelo de serviço para o ajudar a implementar um contentor num cluster do Service Fabric.

Inicie o Visual Studio como “Administrador”.  Selecione **Ficheiro** > **Novo** > **Projeto**.

Selecione **Aplicação do Service Fabric**, dê-lhe o nome "MyFirstContainer" e clique em **OK**.

Selecione **Contentor** na lista de **modelos de serviço**.

Em **Nome da Imagem**, introduza "nanoserver/iis", a [imagem de base do Windows Server 2016 Nano Server e do IIS](https://hub.docker.com/r/nanoserver/iis/). 

Dê ao serviço o nome "MyContainerService" e clique em **OK**.

## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>Configurar a comunicação e o mapeamento de portas de porta para anfitrião do contentor
O serviço de precisa de um ponto final para comunicação.  Agora, pode adicionar o protocolo, a porta e o tipo a um `Endpoint` no ficheiro ServiceManifest.xml. Neste início rápido, o serviço contentorizado escuta na porta 80: 

```xml
<Endpoint Name="MyContainerServiceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
```
Fornecer o `UriScheme` regista automaticamente o ponto final do contentor no serviço de Nomes do Service Fabric para deteção. Um ficheiro de exemplo completo do ServiceManifest.xml está disponível no final deste artigo. 

Configure o mapeamento de portas porta-a-anfitrião do contentor ao especificar uma política de `PortBinding` nas `ContainerHostPolicies` do ficheiro ApplicationManifest.xml.  Neste início rápido, `ContainerPort` é 80 e `EndpointRef` é "MyContainerServiceTypeEndpoint" (o ponto final definido no manifesto do serviço).  Os pedidos de entrada para o serviço na porta 80 são mapeados para a porta 80 no contentor.  

```xml
<ServiceManifestImport>
...
  <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="80" EndpointRef="MyContainerServiceTypeEndpoint"/>
    </ContainerHostPolicies>
  </Policies>
</ServiceManifestImport>
```

Um ficheiro de exemplo completo do ApplicationManifest.xml está disponível no final deste artigo.

## <a name="create-a-cluster"></a>Criar um cluster
Para implementar a aplicação num cluster no Azure, pode optar por criar o seu próprio cluster ou utilizar um party cluster.

Os party clusters são clusters do Service Fabric gratuitos, limitados temporalmente, alojados no Azure e executados pela equipa do Service Fabric, nos quais qualquer pessoa pode implementar aplicações e saber mais sobre a plataforma. Para obter acesso a um party cluster, [siga as instruções](http://aka.ms/tryservicefabric).  

Para obter informações sobre como criar o seu próprio cluster, veja [Crie o seu primeiro cluster do Service Fabric no Azure](service-fabric-get-started-azure-cluster.md).

Anote o ponto final da ligação, que vai utilizar no próximo passo.  

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>Implementar a aplicação no Azure com o Visual Studio
Agora que a aplicação está pronta, pode implementá-la num cluster diretamente a partir do Visual Studio.

Clique com o botão direito do rato em **MyFirstContainer**, no Explorador de Soluções, e escolha **Publicar**. É apresentada a caixa de diálogo Publicar.

![Caixa de diálogo Publicar](./media/service-fabric-quickstart-dotnet/publish-app.png)

Introduza o Ponto Final da Ligação do cluster no campo **Ponto Final da Ligação** e clique em **Publicar**. Quando se registar no party cluster, o Ponto Final da Ligação é fornecido no browser - por exemplo, `winh1x87d1d.westus.cloudapp.azure.com:19000`.

Abra um browser e navegue para http://winh1x87d1d.westus.cloudapp.azure.com:80. Deverá ver a página Web predefinida do IIS: ![Página Web predefinida do IIS][iis-default]

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Exemplo completo da aplicação do Service Fabric e dos manifestos do serviço
Seguem-se os manifestos completos do serviço e da aplicação utilizados neste início rápido.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyContainerServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="MyContainerServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>nanoserver/iis</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    <!--
    <EnvironmentVariables>
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
    </EnvironmentVariables>
    -->
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="MyContainerServiceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="MyContainerService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyContainerServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <PortBinding ContainerPort="80" EndpointRef="MyContainerServiceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="MyContainerService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyContainerServiceType" InstanceCount="[MyContainerService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="next-steps"></a>Passos seguintes
Neste início rápido, aprendeu a:
> [!div class="checklist"]
> * Empacotar um contentor de imagens do Docker
> * Configurar a comunicação
> * Criar e empacotar a aplicação do Service Fabric
> * Implementar a aplicação de contentor no Azure

* Saiba mais sobre como executar [contentores no Service Fabric](service-fabric-containers-overview.md).
* Leia o tutorial [Deploy a .NET application in a container](service-fabric-host-app-in-a-container.md) (Implementar uma aplicação .NET num contentor).
* Saiba mais sobre o [ciclo de vida das aplicações](service-fabric-application-lifecycle.md) do Service Fabric.
* Veja os [exemplos de códigos de contentor do Service Fabric ](https://github.com/Azure-Samples/service-fabric-dotnet-containers) no GitHub.

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png

