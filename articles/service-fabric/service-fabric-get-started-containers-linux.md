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
ms.date: 06/28/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 8355478cb2fff3a63bc4a9b359ec8e2b132c80f6
ms.contentlocale: pt-pt
ms.lasthandoff: 08/24/2017

---

# <a name="create-your-first-service-fabric-container-application-on-linux"></a>Criar a sua primeira aplicação de contentor do Service Fabric no Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

Para executar uma aplicação existente num contentor do Linux num cluster do Service Fabric, não precisa de fazer quaisquer alterações à sua aplicação. Este artigo orienta-o ao longo da criação de uma imagem do Docker que contém uma aplicação Web [Flask](http://flask.pocoo.org/) de Python e da implementação da mesma num cluster do Service Fabric.  Também vai partilhar a sua aplicação contentorizada através do [Azure Container Registry](/azure/container-registry/).  Este artigo pressupõe uma compreensão básica do Docker. Para saber mais sobre o Docker, leia a [Descrição Geral do Docker](https://docs.docker.com/engine/understanding-docker/).

## <a name="prerequisites"></a>Pré-requisitos
* Um computador de programação com:
  * [SDK e ferramentas do Service Fabric](service-fabric-get-started-linux.md).
  * [Docker CE para Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [CLI do Service Fabric](service-fabric-cli.md)

* Um registo no Azure Container Registry - [Criar um registo de contentor](../container-registry/container-registry-get-started-portal.md) na sua subscrição do Azure. 

## <a name="define-the-docker-container"></a>Definir o contentor do Docker
Crie uma imagem com base na [imagem de Python](https://hub.docker.com/_/python/) que se encontra no Docker Hub. 

Defina o contentor do Docker num Dockerfile. O Dockerfile contém instruções para configurar o ambiente no interior do contentor, para carregar a aplicação que pretende executar e para mapear as portas. O Dockerfile é a entrada para o comando `docker build`, o que cria a imagem. 

Crie um diretório vazio e crie o ficheiro *Dockerfile* (sem extensão de ficheiro). Adicione o seguinte ao *Dockerfile* e guarde as alterações:

```
# Use an official Python runtime as a base image
FROM python:2.7-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available outside this container
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
Execute o comando `docker build` para criar a imagem que executa a sua aplicação Web. Abra uma janela do PowerShell e navegue para *c:\temp\helloworldapp*. Execute o seguinte comando:

```bash
docker build -t helloworldapp .
```

Este comando cria a nova imagem através das instruções fornecidas no seu Dockerfile e dá à imagem o nome (identificação -t) "helloworldapp". Criar uma imagem extrai a imagem base a partir do Docker Hub e cria uma nova que adiciona a sua aplicação à parte superior da imagem base.  

Quando o comando de criação concluir, execute o comando `docker images` para ver informações sobre a nova imagem:

```bash
$ docker images
    
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              86838648aab6        2 minutes ago       194 MB
```

## <a name="run-the-application-locally"></a>Executar a aplicação localmente
Confirme que a sua aplicação contentorizada é executada localmente antes de a publicar no registo de contentores.  

Execute a aplicação ao mapear a porta 4000 do computador para a porta do contentor exposta, a 80:

```bash
docker run -d -p 4000:80 --name my-web-site helloworldapp
```

*nome* dá um nome ao contentor em execução (em vez do ID do contentor).

Ligue-se ao contentor em execução.  Abra um browser que aponte para o endereço IP devolvido na porta 4000, como, por exemplo, "http://localhost:4000". Deverá ver o cabeçalho "Hello World!" apresentado no browser.

![Hello World!][hello-world]

Para parar o contentor, execute:

```bash
docker stop my-web-site
```

Elimine o contentor do seu computador de programação:

```bash
docker rm my-web-site
```

## <a name="push-the-image-to-the-container-registry"></a>Enviar a imagem para o registo de contentor
Depois de confirmar que a aplicação é executada no Docker, envie a imagem para o seu registo no Azure Container Registry.

Execute `docker login` para iniciar sessão no registo de contentores com as suas [credenciais do registo](../container-registry/container-registry-authentication.md).

O exemplo seguinte transmite o ID e a palavra-passe de um [principal de serviço](../active-directory/active-directory-application-objects.md) do Azure Active Directory. Por exemplo, poderá ter atribuído um principal de serviço ao seu registo no âmbito de um cenário de automatização.  Em alternativa, pode iniciar sessão com o nome de utilizador e a palavra-passe do registo.

```bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

O comando seguinte cria uma etiqueta, ou alias, da imagem, com um caminho completamente qualificado para o seu registo. Este exemplo coloca a imagem no espaço de nomes `samples` para evitar sobrepovoar a raiz do registo.

```bash
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Envie a imagem para o seu registo de contentor:

```bash
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="package-the-docker-image-with-yeoman"></a>Empacotar a imagem do Docker com Yeoman
O Service Fabric SDK para Linux inclui um gerador [Yeoman](http://yeoman.io/) que permite criar facilmente a sua aplicação e adicionar uma imagem de contentor. Vamos utilizar o Yeoman para criar uma aplicação com um único contentor do Docker, chamado *SimpleContainerApp*.

Para criar uma aplicação de contentor do Service Fabric, abra uma janela de terminal e execute `yo azuresfcontainer`.  

Dê um nome à aplicação (por exemplo, “mycontainer”). 

Introduza o URL da imagem do contentor num registo de contentor (por exemplo, ""). 

Esta imagem tem um ponto de entrada de carga de trabalho definido, pelo que é necessário especificar explicitamente comandos de entrada (os comandos são executados dentro do contentor, o que faz com que este continue em execução após o arranque). 

Especifique uma contagem de instâncias de "1".

![Gerador Yeoman do Service Fabric para contentores][sf-yeoman]

## <a name="configure-port-mapping-and-container-repository-authentication"></a>Configurar o mapeamento de portas e a autenticação do repositório de contentores
O seu serviço contentorizado precisa de um ponto final para comunicação.  Agora, adicione o protocolo, a porta e o tipo a um `Endpoint` no ficheiro ServiceManifest.xml. Neste artigo, o serviço contentorizado escuta na porta 4000: 

```xml
<Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
```
Fornecer o `UriScheme` regista automaticamente o ponto final do contentor no serviço de Nomes do Service Fabric para deteção. Um ficheiro de exemplo completo do ServiceManifest.xml está disponível no final deste artigo. 

Configure o mapeamento de portas porta-a-anfitrião do contentor ao especificar uma política de `PortBinding` nas `ContainerHostPolicies` do ficheiro ApplicationManifest.xml.  Neste artigo, `ContainerPort` é 80 (o contentor expõe a porta 80, conforme especificado no Dockerfile) e `EndpointRef` é "myserviceTypeEndpoint" (o ponto final definido no manifesto do serviço).  Os pedidos de entrada para o serviço na porta 4000 são mapeados para a porta 80 no contentor.  Se precisar que a autenticação do contentor seja realizada com um repositório privado, adicione `RepositoryCredentials`.  Neste artigo, adicione o nome e a palavra-passe da conta do registo de contentores myregistry.azurecr.io. 

```xml
<Policies>
    <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
    </ContainerHostPolicies>
</Policies>
```

## <a name="build-and-package-the-service-fabric-application"></a>Criar e empacotar a aplicação do Service Fabric
Os modelos Yeoman do Service Fabric incluem um script de compilação para [Gradle](https://gradle.org/), que pode utilizar para criar a aplicação a partir do terminal. Para criar e empacotar a aplicação, execute o seguinte:

```bash
cd mycontainer
gradle
```

## <a name="deploy-the-application"></a>Implementar a aplicação
Depois de criada a aplicação, pode implementá-la no cluster local com a CLI do Service Fabric.

Ligue ao cluster do Service Fabric local.

```bash
sfctl cluster select --endpoint http://localhost:19080
```

Utilize o script de instalação fornecido no modelo para copiar o pacote de aplicação para o arquivo de imagens do cluster, registar o tipo de aplicação e criar uma instância da mesma.

```bash
./install.sh
```

Abra um browser e navegue para o Service Fabric Explorer, em http://localhost:19080/Explorer (substitua localhost pelo IP privado da VM se estiver a utilizar Vagrant em Mac OS X). Expanda o nó Aplicações e repare que há, agora, uma entrada para o tipo de aplicação e outra para a primeira instância desse tipo.

Ligue-se ao contentor em execução.  Abra um browser que aponte para o endereço IP devolvido na porta 4000, como, por exemplo, "http://localhost:4000". Deverá ver o cabeçalho "Hello World!" apresentado no browser.

![Hello World!][hello-world]

## <a name="clean-up"></a>Limpeza
Utilize o script de desinstalação fornecido no modelo para eliminar a instância da aplicação do cluster de desenvolvimento local e anular o registo do tipo de aplicação.

```bash
./uninstall.sh
```

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
<ServiceManifest Name="myservicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="myserviceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers 
      to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    
    <EnvironmentVariables>
      <!--
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
      -->
    </EnvironmentVariables>
    
  </CodePackage>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="mycontainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="myservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="myservice">
      <!-- On a local development cluster, set InstanceCount to 1.  On a multi-node production 
      cluster, set InstanceCount to -1 for the container service to run on every node in 
      the cluster.
      -->
      <StatelessService ServiceTypeName="myserviceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="adding-more-services-to-an-existing-application"></a>Adicionar mais serviços a uma aplicação existente

Para adicionar outro serviço de contentor a uma aplicação já criada com o yeoman, execute os seguintes passos:

1. Altere o diretório para a raiz da aplicação existente.  Por exemplo, `cd ~/YeomanSamples/MyApplication`, se `MyApplication` é a aplicação criada por Yeoman.
2. Execute `yo azuresfcontainer:AddService`

<a id="manually"></a>


## <a name="configure-time-interval-before-container-is-force-terminated"></a>Configurar o intervalo de tempo antes do contentor ser forçado a terminar

Pode configurar um intervalo de tempo para o tempo de execução para aguardar antes do contentor ser removido após a eliminação do serviço (ou uma mudança para outro nó) ser iniciada. Configurar o intervalo de tempo envia o comando `docker stop <time in seconds>` para o contentor.   Para obter mais detalhes, veja [paragem do docker](https://docs.docker.com/engine/reference/commandline/stop/). O intervalo de tempo de espera é especificado na secção `Hosting`. O fragmento do manifesto do cluster seguinte mostra como definir o intervalo de espera:

```xml
{
        "name": "Hosting",
        "parameters": [
          {
            "ContainerDeactivationTimeout": "10",
          ...
          }
        ]
}
```
O intervalo de tempo predefinido está definido para 10 segundos. Uma vez que esta configuração é dinâmica, uma configuração apenas de atualização no cluster atualiza tempo limite. 


## <a name="configure-the-runtime-to-remove-unused-container-images"></a>Configurar o tempo de execução para remover as imagens do contentor não utilizadas

Pode configurar o cluster do Service Fabric para remover as imagens do contentor não utilizadas do nó. Esta configuração permite que o espaço em disco seja recapturado se existirem demasiadas imagens do contentor no nó.  Para ativar esta funcionalidade, atualize a secção `Hosting` do manifesto do cluster, conforme apresentado no seguinte fragmento: 


```xml
{
        "name": "Hosting",
        "parameters": [
          {
            "PruneContainerImages": “True”,
            "ContainerImagesToSkip": "microsoft/windowsservercore|microsoft/nanoserver|…",
          ...
          }
        ]
} 
```

Para imagens que não devem ser eliminadas, pode especificá-las no parâmetro `ContainerImagesToSkip`. 


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre como executar [contentores no Service Fabric](service-fabric-containers-overview.md).
* Leia o tutorial [Deploy a .NET application in a container](service-fabric-host-app-in-a-container.md) (Implementar uma aplicação .NET num contentor).
* Saiba mais sobre o [ciclo de vida das aplicações](service-fabric-application-lifecycle.md) do Service Fabric.
* Veja os [exemplos de códigos de contentor do Service Fabric ](https://github.com/Azure-Samples/service-fabric-dotnet-containers) no GitHub.

[hello-world]: ./media/service-fabric-get-started-containers-linux/HelloWorld.png
[sf-yeoman]: ./media/service-fabric-get-started-containers-linux/YoSF.png

