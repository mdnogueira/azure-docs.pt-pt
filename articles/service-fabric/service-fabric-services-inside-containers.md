---
title: "Como containerize micro-serviços o Service Fabric do Azure (pré-visualização)"
description: "Azure Service Fabric tem de adicionar novas funcionalidades para containerize micro-serviços o Service Fabric. Esta funcionalidade encontra-se em pré-visualização."
services: service-fabric
documentationcenter: .net
author: anmolah
manager: anmolah
editor: anmolah
ms.assetid: 0b41efb3-4063-4600-89f5-b077ea81fa3a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/04/2017
ms.author: anmola
ms.openlocfilehash: 6f8ad0bad8d1ae861e6b72f7e1a32ab0675813c2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-containerize-your-service-fabric-reliable-services-and-reliable-actors-preview"></a>Como containerize o serviço de recursos de infraestrutura Reliable Services e Reliable Actors (pré-visualização)

Service Fabric suporta containerizing micro-serviços de Service Fabric (Reliable Services e serviços de Atores fiável com base em). Para obter mais informações, consulte [contentores de recursos de infraestrutura de serviço](service-fabric-containers-overview.md).


 Esta funcionalidade está em pré-visualização e este artigo fornece os vários passos para obter o serviço está em execução no interior de um contentor.  

> [!NOTE]
> Esta funcionalidade está em pré-visualização e não é suportada na produção. Atualmente, esta funcionalidade funciona apenas para o Windows.

## <a name="steps-to-containerize-your-service-fabric-application"></a>Passos para containerize a aplicação de recursos de infraestrutura do serviço

1. Abra a aplicação de Service Fabric no Visual Studio.

2. Adicionar classe de [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) ao seu projeto. O código nesta classe é um programa auxiliar de carregar corretamente os binários de tempo de execução do Service Fabric no interior da sua aplicação ao executar dentro de um contentor.

3. Para cada pacote do código que gostaria de containerize, inicializar o carregador de entrada de programa do ponto. Adicione o construtor estático mostrado no seguinte fragmento de código para o ficheiro de ponto de entrada do programa.

  ```csharp
  namespace MyApplication
  {
      internal static class Program
      {
          static Program()
          {
              SFBinaryLoader.Initialize();
          }

          /// <summary>
          /// This is the entry point of the service host process.
          /// </summary>
          private static void Main()
          {
  ```

4. Criar e [pacote](service-fabric-package-apps.md#Package-App) seu projeto. Para compilar e criar um pacote, clique com o botão direito no projeto de aplicação no Explorador de soluções e escolha o **pacote** comando.

5. Para cada pacote do código é necessário containerize, execute o script de PowerShell [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1). A utilização é o seguinte:
  ```powershell
    $codePackagePath = 'Path to the code package to containerize.'
    $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
    $applicationExeName = 'Name of the ode package executable.'
    CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -ApplicationExeName $applicationExeName
 ```
  O script cria uma pasta com artefactos de Docker em $dockerPackageOutputDirectoryPath. Modificar o Dockerfile gerado para expor as portas, execute scripts de configuração etc. com base nas suas necessidades.

6. Em seguida precisa [criar](service-fabric-get-started-containers.md#Build-Containers) e [push](service-fabric-get-started-containers.md#Push-Containers) seu pacote de contentor do Docker para o seu repositório.

7. Modificar o ApplicationManifest.xml e ServiceManifest.xml para adicionar a imagem de contentor, informações do repositório, autenticação de registo e mapeamento de porta para o anfitrião. Para modificar os manifestos, consulte [criar uma aplicação de contentor do Azure Service Fabric](service-fabric-get-started-containers.md). A definição de pacote do código no manifesto de serviço tem de ser substituído com a imagem de contentor correspondente. Certifique-se alterar o ponto de entrada para um tipo de ContainerHost.

  ```xml
<!-- Code package is your service executable. -->
<CodePackage Name="Code" Version="1.0.0">
  <EntryPoint>
    <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
    <ContainerHost>
      <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
    </ContainerHost>
  </EntryPoint>
  <!-- Pass environment variables to your container: -->    
</CodePackage>
  ```

8. Adicione o mapeamento do anfitrião de porta para o replicador e o ponto final de serviço. Uma vez que ambas estas portas são atribuídas durante a execução pelo Service Fabric, o ContainerPort está definido como zero para utilizar a porta atribuída para o mapeamento.

 ```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
  </ContainerHostPolicies>
</Policies>
 ```

9. Para testar esta aplicação, tem de implementá-la para um cluster que está a executar versão 5.7 ou superior. Além disso, tem de editar e atualizar as definições de cluster para ativar esta funcionalidade de pré-visualização. Siga os passos deste [artigo](service-fabric-cluster-fabric-settings.md) para adicionar a definição mostrada seguinte.
```
      {
        "name": "Hosting",
        "parameters": [
          {
            "name": "FabricContainerAppsEnabled",
            "value": "true"
          }
        ]
      }
```
10. Seguinte [implementar](service-fabric-deploy-remove-applications.md) o pacote de aplicação editado para este cluster.

Agora, deve ter uma aplicação de Service Fabric de executar o seu cluster.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre como executar [contentores no Service Fabric](service-fabric-get-started-containers.md).
* Saiba mais sobre o [ciclo de vida das aplicações](service-fabric-application-lifecycle.md) do Service Fabric.
