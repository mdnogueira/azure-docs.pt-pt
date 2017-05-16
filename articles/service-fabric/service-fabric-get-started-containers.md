---
title: "Criar uma aplicação de contentor do Azure Service Fabric | Microsoft Docs"
description: "Crie a sua primeira aplicação de contentor no Azure Service Fabric.  Crie uma imagem de Docker com a sua aplicação, envie a imagem para um registo de contentor e crie e implemente uma aplicação de contentor do Service Fabric."
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
ms.date: 05/08/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: acb68b274228aa647dc7be5d36b2b077bd213c1b
ms.contentlocale: pt-pt
ms.lasthandoff: 05/10/2017


---

# <a name="create-your-first-service-fabric-container-app"></a>Criar a primeira aplicação de contentor do Service Fabric
Para executar uma aplicação existente num contentor do Windows num cluster do Service Fabric, não precisa de fazer quaisquer alterações à sua aplicação. Este guia de introdução explica-lhe como criar uma imagem do Docker que contenha uma aplicação Web, enviar a nova imagem para o Azure Container Registry, criar uma aplicação de contentor do Service Fabric e implementar a aplicação de contentor num cluster do Service Fabric.  Este artigo pressupõe uma compreensão básica do Docker. Para saber mais sobre o Docker, leia a [Descrição Geral do Docker](https://docs.docker.com/engine/understanding-docker/).

## <a name="prerequisites"></a>Pré-requisitos
Um computador de programação com:
* Visual Studio 2015 ou Visual Studio 2017.
* [SDK e ferramentas do Service Fabric](service-fabric-get-started.md).
*  Docker para Windows.  [Obtenha o Docker CE para Windows (estável)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Depois de instalar e iniciar o Docker, clique com o botão direito do rato no ícone de tabuleiro e selecione **Mudar para os contentores do Windows**. Esta ação é necessária para executar imagens do Docker baseadas no Windows.

Um cluster do Windows com três ou mais nós em execução no Windows Server 2016 com Contentores - [Criar um cluster](service-fabric-get-started-azure-cluster.md) ou [experimentar o Service Fabric gratuitamente](http://tryazureservicefabric.westus.cloudapp.azure.com/). 

Um registo no Azure Container Registry - [Criar um registo de contentor](../container-registry/container-registry-get-started-portal.md) na sua subscrição do Azure. 

## <a name="create-a-simple-web-app"></a>Criar uma aplicação Web simples
Recolha todos os recursos que terá de carregar para uma imagem do Docker num único local. Neste início rápido, crie uma aplicação Web "Hello World" no seu computador de programação.

1. Crie um diretório, como, por exemplo, *c:\temp\helloworldapp*.
2. Crie um subdiretório *c:\temp\helloworldapp\content*.
3. Crie um ficheiro *index.html* em *c:\temp\helloworldapp\content*.
4. Edite o ficheiro *index.html* e adicione a seguinte linha:
    ```
    <h1>Hello World!</h1>
    ```
5. Guarde as alterações que fez a *index.html*.

## <a name="build-the-docker-image"></a>Criar a imagem do Docker
Crie uma imagem com base na [imagem microsft/iis](https://hub.docker.com/r/microsoft/iis/) que se encontra no Docker Hub. A imagem microsoft/iis deriva da imagem de SO base do Windows Server Core e contém os Serviços de Informação Internet (IIS).  Executar esta imagem no seu contentor automaticamente inicia o IIS e os sites instalados.

Defina a imagem do Docker num Dockerfile. O Dockerfile contém instruções para criar a imagem e carregar a aplicação que quer executar. O Dockerfile é a entrada para o comando ```docker build```, o que cria a imagem. 

1. Crie um ficheiro *Dockerfile* (sem extensão de nome de ficheiro) em *c:\temp\helloworldapp* e adicione o seguinte:

    ```
    # The `FROM` instruction specifies the base image. You are
    # extending the `microsoft/iis` image.
    FROM microsoft/iis

    # Create a directory to hold the web app in the container.
    RUN mkdir C:\site

    # Create a new IIS site.
    RUN powershell -NoProfile -Command \
        Import-module IISAdministration; \
        New-IISSite -Name "Site" -PhysicalPath C:\site -BindingInformation "*:8000:"

    # Opens port 8000 on the container.
    EXPOSE 8000

    # The final instruction copies the web app you created earlier into the container.
    ADD content/ /site
    ```

    Não existe nenhum comando ```ENTRYPOINT``` neste Dockerfile. Não precisa. Quando executar o Windows Server com o IIS, o processo IIS é o ponto de entrada, o qual está configurado para iniciar na imagem base.

    Leia a [referência do Dockerfile](https://docs.docker.com/engine/reference/builder/) para obter mais informações.

2. Execute o comando ```docker build``` para criar a imagem que executa a sua aplicação Web. Abra uma janela do PowerShell e navegue para *c:\temp\helloworldapp*. Execute o seguinte comando:

    ```
    docker build -t helloworldapp .
    ```
    Este comando cria a nova imagem através das instruções fornecidas no seu Dockerfile e dá à imagem o nome (identificação -t) "helloworldapp". Criar uma imagem extrai a imagem base a partir do Docker Hub e cria uma nova imagem que adiciona a aplicação à parte superior da imagem base.  A [imagem microsft/iis](https://hub.docker.com/r/microsoft/iis/) e as imagens base do SO têm 10,5 GB, pelo que a extração e transferência das mesmas para o seu computador de programação demora algum tempo.  Aproveite esse tempo para ir almoçar ou beber um café.  A transferência demora menos tempo se, anteriormente, tiver extraído a imagem de SO base para o computador de programação.

3. Quando o comando de criação concluir, execute o comando `docker images` para ver informações sobre a nova imagem:

    ```
    docker images
    
    REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
    helloworldapp              latest              86838648aab6        2 minutes ago       10.1 GB
    ```

## <a name="verify-the-image-runs-locally"></a>Verificar se a imagem é executada localmente
Verifique a sua imagem localmente antes de a enviar para o registo de contentor.  

1. Inicie o contentor com ```docker run```:

    ```
    docker run -d -p 8000:8000 --name my-web-site helloworldapp
    ```

    *nome* dá um nome ao contentor em execução (em vez do ID do contentor).

2. Assim que o contentor for iniciado, localize o respetivo endereço IP para que possa ligar-se ao seu contentor em execução a partir de um browser:
    ```
    docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
    ```

3. Ligue-se ao contentor em execução.  Abra um browser que aponte para o endereço IP devolvido na porta 8000, como, por exemplo, "http://172.31.194.61:8000". Deverá ver o cabeçalho "Hello World!" apresentado no browser.

4. Para parar o contentor, execute:

    ```
    docker stop my-web-site
    ```

5. Elimine o contentor do seu computador de programação:

    ```
    docker rm my-web-site
    ```

## <a name="push-the-image-to-the-container-registry"></a>Enviar a imagem para o registo de contentor
Depois de confirmar que o contentor é executado no seu computador de programação, envie a imagem para o seu registo no Azure Container Registry.

1. Execute ``docker login`` para iniciar sessão no registo de contentores com as suas [credenciais do registo](../container-registry/container-registry-authentication.md).

    O exemplo seguinte transmite o ID e a palavra-passe de um [principal de serviço](../active-directory/active-directory-application-objects.md) do Azure Active Directory. Por exemplo, poderá ter atribuído um principal de serviço ao seu registo no âmbito de um cenário de automatização.

    ```
    docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
    ```

2. O comando seguinte cria uma etiqueta, ou alias, da imagem, com um caminho completamente qualificado para o seu registo. Este exemplo coloca a imagem no espaço de nomes ```samples``` para evitar sobrepovoar a raiz do registo.

    ```
    docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
    ```

3.  Envie a imagem para o seu registo de contentor:

    ```
    docker push myregistry.azurecr.io/samples/helloworldapp
    ```

## <a name="create-and-package-the-containerized-service-in-visual-studio"></a>Criar e empacotar o serviço do contentor no Visual Studio
O SDK e as ferramentas do Service Fabric fornecem um modelo de serviço para o ajudar a implementar um contentor num cluster do Service Fabric.

1. Inicie o Visual Studio.  Selecione **Ficheiro** > **Novo** > **Projeto**.
2. Selecione **Aplicação do Service Fabric**, dê-lhe o nome "MyFirstContainer" e clique em **OK**.
3. Selecione **Contentor Convidado** da lista de **modelos de serviço**.
4. Em **Nome da Imagem**, introduza "myregistry.azurecr.io/samples/helloworldapp", a imagem que enviou para o repositório do seu contentor. 
5. Dê um nome ao serviço e clique em **OK**.
6. Se o serviço do contentor necessita de um ponto final para comunicação, pode adicionar o protocolo, a porta e o tipo a um ```Endpoint``` no ficheiro ServiceManifest.xml. Neste início rápido, o serviço do contentor escuta na porta 80: 

    ```xml
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    ```
    Fornecer o ```UriScheme``` regista automaticamente o ponto final do contentor no serviço de Nomes do Service Fabric para deteção. Um ficheiro de exemplo completo do ServiceManifest.xml está disponível no final deste artigo. 
7. Configure o mapeamento de portas porta-a-anfitrião do contentor ao especificar uma política de ```PortBinding``` nas ```ContainerHostPolicies``` do ficheiro ApplicationManifest.xml.  Neste início rápido, ```ContainerPort``` é 8000 (o contentor expõe a porta 8000, conforme especificado no Dockerfile) e ```EndpointRef``` é "Guest1TypeEndpoint" (o ponto final definido no manifesto do serviço).  Os pedidos de entrada para o serviço na porta 80 são mapeados para a porta 8000 no contentor.  Se precisar que a autenticação do contentor seja realizada com um repositório privado, adicione ```RepositoryCredentials```.  Neste início rápido, adicione o nome e a palavra-passe da conta do registo de contentor myregistry.azurecr.io. 

    ```xml
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="8000" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ```

    Um ficheiro de exemplo completo do ApplicationManifest.xml está disponível no final deste artigo.
8. Configure o ponto final de ligação do cluster para poder publicar a aplicação no seu cluster.  Pode encontrar o ponto final de ligação do cliente no painel Descrição Geral do seu cluster no [portal do Azure](https://portal.azure.com). No Explorador de Soluções, abra o ficheiro *Cloud.xml* em **MyFirstContainer**->**PublishProfiles**.  Adicione o nome e a porta de ligação do cluster a **ClusterConnectionParameters**.  Por exemplo:
    ```xml
    <ClusterConnectionParameters ConnectionEndpoint="containercluster.westus2.cloudapp.azure.com:19000" />
    ```
    
9. Guarde todos os ficheiros e compile o seu projeto.  

10. Para empacotar a sua aplicação, clique com o botão direito do rato em **MyFirstContainer** no Explorador de Soluções e selecione **Pacote**. 

## <a name="deploy-the-container-app"></a>Implementar a aplicação de contentor
1. Para publicar a sua aplicação, clique com o botão direito do rato em **MyFirstContainer** no Explorador de Soluções e selecione **Publicar**.

2. O [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) é uma ferramenta baseada na Web utilizada para inspecionar e gerir aplicações e nós num cluster do Service Fabric. Abra um browser e navegue para http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/ e siga a implementação da aplicação.  A aplicação é implementada, mas fica em estado de erro até que a imagem seja transferida nos nós do cluster (o que pode demorar algum tempo, consoante o tamanho da imagem):  ![Erro][1]

3. A aplicação está pronta quando estiver no estado ```Ready```:  ![Pronto][2]

4. Abra um browser e navegue para http://containercluster.westus2.cloudapp.azure.com. Deverá ver o cabeçalho "Hello World!" apresentado no browser.

## <a name="clean-up"></a>Limpeza
Continua a incorrer em custos enquanto o cluster está em execução, pelo que deve considerar [eliminar o seu cluster](service-fabric-get-started-azure-cluster.md#remove-the-cluster).  Os [clusters de grupo](http://tryazureservicefabric.westus.cloudapp.azure.com/) são eliminados automaticamente após algumas horas.

Depois de enviar a imagem para o registo de contentor, pode eliminar a imagem local do seu computador de programação:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Exemplo completo da aplicação do Service Fabric e dos manifestos do serviço
Seguem-se os manifestos completos do serviço e da aplicação utilizados neste início rápido.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
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
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
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
    <Parameter Name="Guest1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="8000" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Guest1">
      <StatelessService ServiceTypeName="Guest1Type" InstanceCount="[Guest1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre como executar [contentores no Service Fabric](service-fabric-containers-overview.md).
* Saiba mais sobre o [ciclo de vida das aplicações](service-fabric-application-lifecycle.md) do Service Fabric.
* Veja os [exemplos de códigos de contentor do Service Fabric ](https://github.com/Azure-Samples/service-fabric-dotnet-containers) no GitHub.

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png

