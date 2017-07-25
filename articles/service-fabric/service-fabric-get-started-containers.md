---
title: "Criar uma aplicação de contentor do Azure Service Fabric | Microsoft Docs"
description: "Crie a sua primeira aplicação de contentor do Windows no Azure Service Fabric.  Crie uma imagem do Docker com a sua aplicação Python, envie-a para um registo de contentor e crie e implemente uma aplicação de contentor do Service Fabric."
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
ms.date: 06/30/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: 8c9d6c65666b5ffedf058e0a83d4fc41fff80235
ms.contentlocale: pt-pt
ms.lasthandoff: 07/15/2017

---

# <a name="create-your-first-service-fabric-container-application-on-windows"></a>Criar a sua primeira aplicação de contentor do Service Fabric no Windows
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

Para executar uma aplicação existente num contentor do Windows num cluster do Service Fabric, não precisa de fazer quaisquer alterações à sua aplicação. Este artigo orienta-o ao longo da criação de uma imagem do Docker que contém uma aplicação Web [Flask](http://flask.pocoo.org/) de Python e da implementação da mesma num cluster do Service Fabric.  Também vai partilhar a sua aplicação contentorizada através do [Azure Container Registry](/azure/container-registry/).  Este artigo pressupõe uma compreensão básica do Docker. Para saber mais sobre o Docker, leia a [Descrição Geral do Docker](https://docs.docker.com/engine/understanding-docker/).

## <a name="prerequisites"></a>Pré-requisitos
Um computador de programação com:
* Visual Studio 2015 ou Visual Studio 2017.
* [SDK e ferramentas do Service Fabric](service-fabric-get-started.md).
*  Docker para Windows.  [Obtenha o Docker CE para Windows (estável)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Depois de instalar e iniciar o Docker, clique com o botão direito do rato no ícone de tabuleiro e selecione **Mudar para os contentores do Windows**. Esta ação é necessária para executar imagens do Docker baseadas no Windows.

Um cluster do Windows com três ou mais nós em execução no Windows Server 2016 com Contentores - [Criar um cluster](service-fabric-cluster-creation-via-portal.md) ou [experimentar o Service Fabric gratuitamente](https://aka.ms/tryservicefabric). 

Um registo no Azure Container Registry - [Criar um registo de contentor](../container-registry/container-registry-get-started-portal.md) na sua subscrição do Azure. 

## <a name="define-the-docker-container"></a>Definir o contentor do Docker
Crie uma imagem com base na [imagem de Python](https://hub.docker.com/_/python/) que se encontra no Docker Hub. 

Defina o contentor do Docker num Dockerfile. O Dockerfile contém instruções para configurar o ambiente no interior do contentor, para carregar a aplicação que pretende executar e para mapear as portas. O Dockerfile é a entrada para o comando `docker build`, o que cria a imagem. 

Crie um diretório vazio e crie o ficheiro *Dockerfile* (sem extensão de ficheiro). Adicione o seguinte ao *Dockerfile* e guarde as alterações:

```
# Use an official Python runtime as a base image
FROM python:2.7-windowsservercore

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

Leia a [referência do Dockerfile](https://docs.docker.com/engine/reference/builder/) para obter mais informações.

## <a name="create-a-simple-web-application"></a>Criar uma aplicação Web simples
Crie uma aplicação web Flask que escute na porta 80 e que devolva "Hello World!".  No mesmo diretório, crie o ficheiro *requirements.txt*.  Adicione o seguinte e guarde as alterações:
```
Flask
```

Crie também o ficheiro *app.py* e adicione o seguinte:

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():
    
    return 'Hello World!'

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

## <a name="build-the-image"></a>Criar a imagem
Execute o comando `docker build` para criar a imagem que executa a sua aplicação Web. Abra uma janela do PowerShell e navegue para o diretório que contém o Dockerfile. Execute o seguinte comando:

```
docker build -t helloworldapp .
```

Este comando cria a nova imagem através das instruções fornecidas no seu Dockerfile e dá à imagem o nome (identificação -t) "helloworldapp". Criar uma imagem extrai a imagem base a partir do Docker Hub e cria uma nova que adiciona a sua aplicação à parte superior da imagem base.  

Quando o comando de criação concluir, execute o comando `docker images` para ver informações sobre a nova imagem:

```
$ docker images
    
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              8ce25f5d6a79        2 minutes ago       10.4 GB
```

## <a name="run-the-application-locally"></a>Executar a aplicação localmente
Verifique a sua imagem localmente antes de a enviar para o registo de contentor.  

Execute a aplicação:

```
docker run -d --name my-web-site helloworldapp
```

*nome* dá um nome ao contentor em execução (em vez do ID do contentor).

Assim que o contentor for iniciado, localize o respetivo endereço IP para que possa ligar-se ao seu contentor em execução a partir de um browser:
```
docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
```

Ligue-se ao contentor em execução.  Abra um browser que aponte para o endereço IP devolvido, como, por exemplo, "http://172.31.194.61”. Deverá ver o cabeçalho "Hello World!" apresentado no browser.

Para parar o contentor, execute:

```
docker stop my-web-site
```

Elimine o contentor do seu computador de programação:

```
docker rm my-web-site
```

## <a name="push-the-image-to-the-container-registry"></a>Enviar a imagem para o registo de contentor
Depois de confirmar que o contentor é executado no seu computador de programação, envie a imagem para o seu registo no Azure Container Registry.

Execute ``docker login`` para iniciar sessão no registo de contentores com as suas [credenciais do registo](../container-registry/container-registry-authentication.md).

O exemplo seguinte transmite o ID e a palavra-passe de um [principal de serviço](../active-directory/active-directory-application-objects.md) do Azure Active Directory. Por exemplo, poderá ter atribuído um principal de serviço ao seu registo no âmbito de um cenário de automatização. Em alternativa, pode iniciar sessão com o nome de utilizador e a palavra-passe do registo.

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

O comando seguinte cria uma etiqueta, ou alias, da imagem, com um caminho completamente qualificado para o seu registo. Este exemplo coloca a imagem no espaço de nomes ```samples``` para evitar sobrepovoar a raiz do registo.

```
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Envie a imagem para o seu registo de contentor:

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
6. Se o serviço do contentor necessita de um ponto final para comunicação, pode adicionar o protocolo, a porta e o tipo a um ```Endpoint``` no ficheiro ServiceManifest.xml. Neste artigo, o serviço contentorizado escuta na porta 80: 

    ```xml
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
    ```
    Fornecer o ```UriScheme``` regista automaticamente o ponto final do contentor no serviço de Nomes do Service Fabric para deteção. Um ficheiro de exemplo completo do ServiceManifest.xml está disponível no final deste artigo. 
7. Configure o mapeamento de portas porta-a-anfitrião do contentor ao especificar uma política de ```PortBinding``` nas ```ContainerHostPolicies``` do ficheiro ApplicationManifest.xml.  Neste artigo, ```ContainerPort``` é 8081 (o contentor expõe a porta 80, conforme especificado no Dockerfile) e ```EndpointRef``` é "Guest1TypeEndpoint" (o ponto final definido no manifesto do serviço).  Os pedidos de entrada para o serviço na porta 8081 são mapeados para a porta 80 no contentor.  Se precisar que a autenticação do contentor seja realizada com um repositório privado, adicione ```RepositoryCredentials```.  Neste artigo, adicione o nome e a palavra-passe da conta do registo de contentores myregistry.azurecr.io. 

    ```xml
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ```

    Um ficheiro de exemplo completo do ApplicationManifest.xml está disponível no final deste artigo.
8. Configure o ponto final de ligação do cluster para poder publicar a aplicação no seu cluster.  Pode encontrar o ponto final de ligação do cliente no painel Descrição Geral do seu cluster no [portal do Azure](https://portal.azure.com). No Explorador de Soluções, abra o ficheiro *Cloud.xml* em **MyFirstContainer**->**PublishProfiles**.  Adicione o nome e a porta de ligação do cluster a **ClusterConnectionParameters**.  Por exemplo:
    ```xml
    <ClusterConnectionParameters ConnectionEndpoint="containercluster.westus2.cloudapp.azure.com:19000" />
    ```
    
9. Guarde todos os ficheiros e compile o seu projeto.  

10. Para empacotar a sua aplicação, clique com o botão direito do rato em **MyFirstContainer**, no Explorador de Soluções, e selecione **Empacotar**. 

## <a name="deploy-the-container-application"></a>Implementar a aplicação de contentor
Para publicar a sua aplicação, clique com o botão direito do rato em **MyFirstContainer**, no Explorador de Soluções, e selecione **Publicar**.

O [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) é uma ferramenta baseada na Web utilizada para inspecionar e gerir aplicações e nós num cluster do Service Fabric. Abra um browser e navegue para http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/ e siga a implementação da aplicação.  A aplicação é implementada, mas fica em estado de erro até que a imagem seja transferida nos nós do cluster (o que pode demorar algum tempo, consoante o tamanho da imagem): ![Erro][1]

A aplicação está pronta quando estiver no estado ```Ready```: ![Pronto][2]

Abra um browser e navegue para http://containercluster.westus2.cloudapp.azure.com:8081. Deverá ver o cabeçalho "Hello World!" apresentado no browser.

## <a name="clean-up"></a>Limpeza
Continua a incorrer em custos enquanto o cluster está em execução, pelo que deve considerar [eliminar o seu cluster](service-fabric-get-started-azure-cluster.md#remove-the-cluster).  Os [clusters de grupo](http://tryazureservicefabric.westus.cloudapp.azure.com/) são eliminados automaticamente após algumas horas.

Depois de enviar a imagem para o registo de contentor, pode eliminar a imagem local do seu computador de programação:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Exemplo completo da aplicação do Service Fabric e dos manifestos do serviço
Seguem-se os manifestos completos do serviço e da aplicação utilizados neste artigo.

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
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
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
        <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
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
* Leia o tutorial [Deploy a .NET application in a container](service-fabric-host-app-in-a-container.md) (Implementar uma aplicação .NET num contentor).
* Saiba mais sobre o [ciclo de vida das aplicações](service-fabric-application-lifecycle.md) do Service Fabric.
* Veja os [exemplos de códigos de contentor do Service Fabric ](https://github.com/Azure-Samples/service-fabric-dotnet-containers) no GitHub.

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png

