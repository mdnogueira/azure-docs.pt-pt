---
title: "Criar uma aplicação de contentor do Azure Service Fabric no Linux | Microsoft Docs"
description: "Crie a sua primeira aplicação de contentor do Linux no Azure Service Fabric.  Crie uma imagem do Docker com a sua aplicação, envie-a para um registo de contentor e crie e implemente uma aplicação de contentor do Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
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
ms.openlocfilehash: 78306672e812745fd1902ae264c2adea196ab721
ms.contentlocale: pt-pt
ms.lasthandoff: 09/07/2017

---

# <a name="deploy-a-service-fabric-linux-container-application-on-azure"></a>Implementar uma aplicação de contentor do Linux do Service Fabric no Azure
O Azure Service Fabric é uma plataforma de sistemas distribuídos par implementar e gerir microsserviços e contentores dimensionáveis e fiáveis. 

Para executar uma aplicação existente num contentor do Linux num cluster do Service Fabric, não precisa de fazer quaisquer alterações à sua aplicação. Este guia de início rápido mostra como implementar uma imagem de contentor do Docker pré-concebida numa aplicação do Service Fabric. Quando tiver terminado, terá um contentor nginx em execução.  Este guia de início rápido descreve a implementação de um contentor do Linux. Leia [este início rápido](service-fabric-quickstart-containers.md) para implementar um contentor do Windows.

![Nginx][nginx]

Neste início rápido, vai aprender a:
> [!div class="checklist"]
> * Empacotar um contentor de imagens do Docker
> * Configurar a comunicação
> * Criar e empacotar a aplicação do Service Fabric
> * Implementar a aplicação de contentor no Azure

## <a name="prerequisites"></a>Pré-requisitos
Instale o [SDK do Service Fabric, a CLI do Service Fabric e os geradores de modelos yeoman do Service Fabric](service-fabric-get-started-linux.md).
  
## <a name="package-a-docker-image-container-with-yeoman"></a>Empacotar um contentor de imagens do Docker com o Yeoman
O Service Fabric SDK para Linux inclui um gerador [Yeoman](http://yeoman.io/) que permite criar facilmente a sua aplicação e adicionar uma imagem de contentor. 

Para criar uma aplicação de contentor do Service Fabric, abra uma janela de terminal e execute `yo azuresfcontainer`.  

Dê o nome "MyFirstContainer" à aplicação e "MyContainerService.” ao serviço de aplicações.

Indique o nome "nginx:latest" para a imagem de contentor (a [imagem de contentor nginx](https://hub.docker.com/r/_/nginx/) no Docker Hub). 

Esta imagem tem um ponto de entrada de carga de trabalho definido, pelo que tem de especificar explicitamente os comandos de entrada. 

Especifique uma contagem de instâncias de "1".

![Gerador Yeoman do Service Fabric para contentores][sf-yeoman]

## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>Configurar a comunicação e o mapeamento de portas de porta para anfitrião do contentor
Configure um ponto final de HTTP de modo a que os clientes possam comunicar com o seu serviço.  Abra o ficheiro *./MyFirstContainer/MyContainerServicePkg/ServiceManifest.xml* e declare num recurso de ponto final no elemento **ServiceManifest**.  Adicione o protocolo, a porta e o nome. Neste início rápido, o serviço escuta na porta 80: 

```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
    <Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
  </Endpoints>
</Resources>

```
Fornecer o `UriScheme` regista automaticamente o ponto final do contentor no serviço de Nomes do Service Fabric para deteção. Um ficheiro de exemplo completo do ServiceManifest.xml está disponível no final deste artigo. 

Mapeie uma porta de contentor para um `Endpoint` do serviço através de uma política `PortBinding` em `ContainerHostPolicies` do ficheiro ApplicationManifest.xml.  Neste início rápido, `ContainerPort` é 80 (o contentor expõe a porta 80) e `EndpointRef` é "myserviceTypeEndpoint" (o ponto final definido anteriormente no manifesto do serviço).  Os pedidos de entrada para o serviço na porta 80 são mapeados para a porta 80 no contentor.  

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="build-and-package-the-service-fabric-application"></a>Criar e empacotar a aplicação do Service Fabric
Os modelos Yeoman do Service Fabric incluem um script de compilação para [Gradle](https://gradle.org/), que pode utilizar para criar a aplicação a partir do terminal. Guarde todas as alterações.  Para criar e empacotar a aplicação, execute o seguinte:

```bash
cd MyFirstContainer
gradle
```
## <a name="create-a-cluster"></a>Criar um cluster
Para implementar a aplicação num cluster no Azure, pode optar por criar o seu próprio cluster ou utilizar um party cluster.

Os party clusters são clusters do Service Fabric gratuitos, limitados temporalmente, alojados no Azure e executados pela equipa do Service Fabric, nos quais qualquer pessoa pode implementar aplicações e saber mais sobre a plataforma. Para obter acesso a um party cluster, [siga as instruções](http://aka.ms/tryservicefabric).  

Para obter informações sobre como criar o seu próprio cluster, veja [Crie o seu primeiro cluster do Service Fabric no Azure](service-fabric-get-started-azure-cluster.md).

Anote o ponto final da ligação, que vai utilizar no próximo passo.

## <a name="deploy-the-application-to-azure"></a>Implementar a aplicação no Azure
Depois de criada a aplicação, pode implementá-la no cluster do Azure com a CLI do Service Fabric.

Ligue ao cluster do Service Fabric no Azure.

```bash
sfctl cluster select --endpoint http://lnxt10vkfz6.westus.cloudapp.azure.com:19080
```

Utilize o script de instalação fornecido no modelo para copiar o pacote de aplicação para o arquivo de imagens do cluster, registar o tipo de aplicação e criar uma instância da mesma.

```bash
./install.sh
```

Abra um browser e navegue para Service Fabric Explorer, em http://lnxt10vkfz6.westus.cloudapp.azure.com:19080/Explorer. Expanda o nó Aplicações e repare que há, agora, uma entrada para o tipo de aplicação e outra para a primeira instância desse tipo.

![Service Fabric Explorer][sfx]

Ligue-se ao contentor em execução.  Abra um browser que aponte para o endereço IP devolvido na porta 80, como, por exemplo, "lnxt10vkfz6.westus.cloudapp.azure.com:80". Deverá ver a página de boas-vindas do nginx no browser.

![Nginx][nginx]

## <a name="clean-up"></a>Limpeza
Utilize o script de desinstalação fornecido no modelo para eliminar a instância da aplicação do cluster local e anular o registo do tipo de aplicação.

```bash
./uninstall.sh
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Exemplo completo da aplicação do Service Fabric e dos manifestos do serviço
Seguem-se os manifestos completos do serviço e da aplicação utilizados neste início rápido.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyContainerServicePkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="MyContainerServiceType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>
   
   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>nginx:latest</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables> 
      </EnvironmentVariables> 
   </CodePackage>
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>
 </ServiceManifest>

```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest  ApplicationTypeName="MyFirstContainerType" ApplicationTypeVersion="1.0.0"
                      xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
   
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyContainerServicePkg" ServiceManifestVersion="1.0.0" />
   <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
   
   <DefaultServices>
      <Service Name="MyContainerService">
        <!-- On a local development cluster, set InstanceCount to 1.  On a multi-node production 
        cluster, set InstanceCount to -1 for the container service to run on every node in 
        the cluster.
        -->
        <StatelessService ServiceTypeName="MyContainerServiceType" InstanceCount="1">
            <SingletonPartition />
        </StatelessService>
      </Service>
   </DefaultServices>
   
</ApplicationManifest>

```

## <a name="next-steps"></a>Passos seguintes
Neste início rápido, vai aprender a:
> [!div class="checklist"]
> * Empacotar um contentor de imagens do Docker
> * Configurar a comunicação
> * Criar e empacotar a aplicação do Service Fabric
> * Implementar a aplicação de contentor no Azure

* Saiba mais sobre como executar [contentores no Service Fabric](service-fabric-containers-overview.md).
* Leia o tutorial [Deploy a .NET application in a container](service-fabric-host-app-in-a-container.md) (Implementar uma aplicação .NET num contentor).
* Saiba mais sobre o [ciclo de vida das aplicações](service-fabric-application-lifecycle.md) do Service Fabric.
* Veja os [exemplos de códigos de contentor do Service Fabric ](https://github.com/Azure-Samples/service-fabric-dotnet-containers) no GitHub.

[sfx]: ./media/service-fabric-quickstart-containers-linux/SFX.png
[nginx]: ./media/service-fabric-quickstart-containers-linux/nginx.png
[sf-yeoman]: ./media/service-fabric-quickstart-containers-linux/YoSF.png

