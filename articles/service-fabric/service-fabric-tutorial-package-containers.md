---
title: "O pacote e implementar uma aplicação de contentores do Service Fabric | Microsoft Docs"
description: "Saiba como gerar uma definição de aplicação de Service Fabric do Azure utilizando o Yeoman e pacote de aplicação."
services: service-fabric
documentationcenter: 
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: "Docker, contentores, micro-serviços, recursos de infraestrutura de serviço, do Azure"
ms.assetid: 
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 9b1498d76680185b45edf9ac7e1747bfa6794eec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="package-and-deploy-containers-as-a-service-fabric-application"></a>O pacote e implementar contentores como uma aplicação de Service Fabric

Este tutorial é parte dois uma série. Neste tutorial, uma ferramenta de gerador de modelo (Yeoman) é utilizada para gerar uma definição de aplicação de Service Fabric. Esta aplicação, em seguida, pode ser utilizada para implementar contentores Service Fabric. Neste tutorial, ficará a saber como: 

> [!div class="checklist"]
> * Instalar o Yeoman  
> * Criar um pacote de aplicação utilizando o Yeoman
> * Configurar as definições no pacote de aplicação para utilização com contentores
> * Criar a aplicação  
> * Implementar e executar a aplicação 
> * Limpar a aplicação

## <a name="prerequisites"></a>Pré-requisitos

- As imagens de contentor instalada no registo de contentor do Azure criado no [parte 1](service-fabric-tutorial-create-container-images.md) deste tutorial série é utilizada.
- Ambiente de desenvolvimento de Linux está [configurar](service-fabric-tutorial-create-container-images.md).

## <a name="install-yeoman"></a>Instalar o Yeoman
O Service fabric fornece ferramentas de andaime para ajudar a criar aplicações do terminal utilizando Yeoman gerador de modelo. Siga os passos abaixo para se certificar de que tem o Yeoman gerador de modelo. 

1. Instale nodejs e NPM no seu computador. Tenha em atenção que, os utilizadores de Mac OSX terá de utilizar o Gestor de pacotes Homebrew

    ```bash
    sudo apt-get install npm && sudo apt install nodejs-legacy
    ```
2. Instalar Yeoman gerador do modelo na máquina a partir do NPM 

    ```bash
    sudo npm install -g yo
    ```
3. Instalar o gerador de contentor do serviço Fabric Yeoman

    ```bash 
    sudo npm install -g generator-azuresfcontainer
    ```

## <a name="package-a-docker-image-container-with-yeoman"></a>Empacotar um contentor de imagens do Docker com o Yeoman

1. Para criar um contentor de Service Fabric aplicação, no diretório 'contentor-tutorial' do repositório clonado, execute o seguinte comando.

    ```bash
    yo azuresfcontainer
    ```
2. Nome da aplicação "TestContainer" e atribua o nome do serviço de aplicações "azurevotefront".
3. Forneça o caminho da imagem do contentor no ACR para o repositório de front-end - por exemplo 'test.azurecr.io/azure-vote-front:v1'. 
4. Prima Enter para deixar os comandos secção vazio.
5. Especifique uma contagem de instâncias de 1.

O seguinte mostra a entrada e saída da execução de yo de comando:

```bash
? Name your application TestContainer
? Name of the application service: azurevotefront
? Input the Image Name: <acrName>.azurecr.io/azure-vote-front:v1
? Commands: 
? Number of instances of guest container application: 1
   create TestContainer/TestContainer/ApplicationManifest.xml
   create TestContainer/TestContainer/azurevotefrontPkg/ServiceManifest.xml
   create TestContainer/TestContainer/azurevotefrontPkg/config/Settings.xml
   create TestContainer/TestContainer/azurevotefrontPkg/code/Dummy.txt
   create TestContainer/install.sh
   create TestContainer/uninstall.sh
```

Para adicionar outro serviço de contentor a uma aplicação já criada com o yeoman, execute os seguintes passos:

1. Diretório de alteração para o **TestContainer** diretório
2. Execute `yo azuresfcontainer:AddService` 
3. Nome do serviço 'azurevoteback'
4. Forneça o caminho da imagem do contentor no ACR para o repositório de back-end - por exemplo 'test.azurecr.io/azure-vote-back:v1'
5. Prima Enter para deixar os comandos secção vazio
6. Especifique uma contagem de instâncias de "1".

As entradas para adicionar o serviço utilizado são todas apresentadas:

```bash
? Name of the application service: azurevoteback
? Input the Image Name: <acrName>.azurecr.io/azure-vote-back:v1
? Commands: 
? Number of instances of guest container application: 1
   create TestContainer/azurevotebackPkg/ServiceManifest.xml
   create TestContainer/azurevotebackPkg/config/Settings.xml
   create TestContainer/azurevotebackPkg/code/Dummy.txt
```

Para o resto deste tutorial, estamos a trabalhar **TestContainer** diretório.

## <a name="configure-the-application-manifest-with-credentials-for-azure-container-registry"></a>Configurar o manifesto da aplicação com as credenciais para o registo de contentor do Azure
Para o Service Fabric solicitar as imagens de contentor do registo de contentor do Azure, é necessário fornecer as credenciais no **ApplicationManifest.xml**. 


Inicie sessão sua instância ACR. Utilize o [início de sessão do az acr](/cli/azure/acr#az_acr_login) comando para concluir a operação. Forneça o nome exclusivo especificado no registo do contentor quando foi criado.

```bash
az acr login --name <acrName>
```

O comando devolve um **início de sessão foi efetuado com êxito** mensagem depois de concluir.

Em seguida, execute o seguinte comando para obter a palavra-passe do seu registo de contentor. Esta palavra-passe é utilizada pelo serviço de recursos de infraestrutura para autenticar com o ACR para solicitar as imagens de contentor.

```bash
az acr credential show -n <acrName> --query passwords[0].value
```

No **ApplicationManifest.xml**, adicione o fragmento de código sob o **ServiceManifestImport** elemento para cada um dos serviços. Inserir o **acrName** para o **AccountName** campo e a palavra-passe devolvido pelo comando anterior é utilizado para o **palavra-passe** campo. Um completo **ApplicationManifest.xml** é fornecida no final deste documento. 

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <RepositoryCredentials AccountName="<acrName>" Password="<password>" PasswordEncrypted="false"/>
  </ContainerHostPolicies>
</Policies>
```
## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>Configurar a comunicação e o mapeamento de portas de porta para anfitrião do contentor

### <a name="configure-communication-port"></a>Configurar a porta de comunicação

Configure um ponto final de HTTP de modo a que os clientes possam comunicar com o seu serviço.  Abra o *./TestContainer/azurevotefrontPkg/ServiceManifest.xml* de ficheiros e declarar um recurso de ponto final no **ServiceManifest** elemento.  Adicione o protocolo, a porta e o nome. Para este tutorial, o serviço de escuta na porta 80. 
  
```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
            listen. Please note that if your service is partitioned, this port is shared with 
            replicas of different partitions that are placed in your code. -->
    <Endpoint Name="azurevotefrontTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
  </Endpoints>
</Resources>

```
  
Da mesma forma, modifique o manifesto do serviço para o serviço de back-end. Para este tutorial, a predefinição redis 6379 é mantida.
```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
            listen. Please note that if your service is partitioned, this port is shared with 
            replicas of different partitions that are placed in your code. -->
    <Endpoint Name="azurevotebackTypeEndpoint" UriScheme="http" Port="6379" Protocol="http"/>
  </Endpoints>
</Resources>
```
Fornecer o **UriScheme**regista automaticamente o ponto final de contentor com o serviço de nomenclatura de recursos de infraestrutura do serviço de mensagens em fila para deteção. Por exemplo, um ficheiro de exemplo ServiceManifest.xml completo para o serviço de back-end é fornecido no final deste artigo. 

### <a name="map-container-ports-to-a-service"></a>Portas de contentor do mapa para um serviço
    
Para expor os contentores no cluster, é também necessário criar um enlace da porta no 'ApplicationManifest.xml'. O **PortBinding** política referências a **pontos finais** que definimos no **ServiceManifest.xml** ficheiros. Os pedidos recebidos para estes pontos finais obterem mapeados para as portas de contentor que estão abertas e tem um vínculo aqui. No **ApplicationManifest.xml** ficheiro, adicione o seguinte código para vincular a porta 80 e 6379 para os pontos finais. Um completo **ApplicationManifest.xml** está disponível no final deste documento. 
  
```xml
<ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="azurevotefrontTypeEndpoint"/>
</ContainerHostPolicies>
```

```xml
<ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="6379" EndpointRef="azurevotebackTypeEndpoint"/>
</ContainerHostPolicies>
```

### <a name="add-a-dns-name-to-the-backend-service"></a>Adicionar um nome DNS para o serviço de back-end
  
Para o Service Fabric atribuir este nome DNS para o serviço de back-end, o nome tem de ser especificado o **ApplicationManifest.xml**. Adicionar o **ServiceDnsName** atributo para o **serviço** elemento conforme mostrado: 
  
```xml
<Service Name="azurevoteback" ServiceDnsName="redisbackend.testapp">
  <StatelessService ServiceTypeName="azurevotebackType" InstanceCount="1">
    <SingletonPartition/>
  </StatelessService>
</Service>
```

O serviço de front-end lê uma variável de ambiente de saber o nome DNS da instância do Redis. A variável de ambiente está definida o Dockerfile conforme mostrado:
  
```Dockerfile
ENV REDIS redisbackend.testapp
```
  
O script de python que compõe a utiliza final front-nome este DNS para resolver e ligue para o arquivo de redis do back-end, conforme mostrado:

```python
# Get DNS Name
redis_server = os.environ['REDIS'] 

# Connect to the Redis store
r = redis.StrictRedis(host=redis_server, port=6379, db=0)
```

Neste momento no tutorial, o modelo para uma aplicação de pacote de serviço está disponível para a implementação para um cluster. O tutorial subsequentes, esta aplicação é implementada e foi executada num cluster de Service Fabric.

## <a name="create-a-service-fabric-cluster"></a>Criar um cluster do Service Fabric
Para implementar a aplicação num cluster no Azure, utilize o seu próprio cluster ou utilize um Party Cluster.

Party clusters são clusters do Service Fabric gratuitos e de tempo limitado, alojados no Azure. Esta é mantida pela equipa do Service Fabric em que qualquer pessoa pode implementar aplicações e saber mais sobre a plataforma. Para obter acesso a um Party Cluster, [siga as instruções](http://aka.ms/tryservicefabric). 

Para obter informações sobre como criar o seu próprio cluster, veja [Crie o seu primeiro cluster do Service Fabric no Azure](service-fabric-get-started-azure-cluster.md).

## <a name="build-and-deploy-the-application-to-the-cluster"></a>Criar e implementar a aplicação em cluster
Pode implementar a aplicação do cluster do Azure utilizando a CLI de recursos de infraestrutura de serviço. Se o serviço de recursos de infraestrutura CLI não está instalado no seu computador, siga as instruções [aqui](service-fabric-get-started-linux.md#set-up-the-service-fabric-cli) instalá-la. 

Ligue ao cluster do Service Fabric no Azure.

```bash
sfctl cluster select --endpoint http://lin4hjim3l4.westus.cloudapp.azure.com:19080
```

Utilizar o script de instalação fornecido no **TestContainer** diretório para copiar o pacote de aplicação para o arquivo de imagens do cluster, registar o tipo de aplicação e criar uma instância da aplicação.

```bash
./install.sh
```

Abra um browser e navegue para o Service Fabric Explorer em http://lin4hjim3l4.westus.cloudapp.azure.com:19080/Explorer. Expanda o nó de aplicações e tenha em atenção que é uma entrada para o tipo de aplicação e outro para a instância.

![Service Fabric Explorer][sfx]

Para ligar a aplicação em execução, abra um browser e avance para o url de cluster - por exemplo http://lin0823ryf2he.cloudapp.azure.com:80. Deverá ver a aplicação de voto a IU da web.

![votingapp][votingapp]

## <a name="clean-up"></a>Limpeza
Utilize o script de desinstalação fornecido no modelo para eliminar a instância da aplicação do cluster local e anular o registo do tipo de aplicação. Este comando demora algum tempo para limpar a instância e não é possível executar o comando 'install.sh' imediatamente após este script. 

```bash
./uninstall.sh
```

## <a name="examples-of-completed-manifests"></a>Exemplos de manifestos concluídos

### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ApplicationManifest ApplicationTypeName="TestContainerType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="azurevotefrontPkg" ServiceManifestVersion="1.0.0"/>
    <Policies> 
    <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myaccountname" Password="<password>" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="azurevotefrontTypeEndpoint"/>
    </ContainerHostPolicies>
        </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="azurevotebackPkg" ServiceManifestVersion="1.0.0"/>
      <Policies> 
        <ContainerHostPolicies CodePackageRef="Code">
          <RepositoryCredentials AccountName="myaccountname" Password="<password>" PasswordEncrypted="false"/>
          <PortBinding ContainerPort="6379" EndpointRef="azurevotebackTypeEndpoint"/>
        </ContainerHostPolicies>
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="azurevotefront">
      <StatelessService ServiceTypeName="azurevotefrontType" InstanceCount="1">
        <SingletonPartition/>
      </StatelessService>
    </Service>
    <Service Name="azurevoteback" ServiceDnsName="redisbackend.testapp">
      <StatelessService ServiceTypeName="azurevotebackType" InstanceCount="1">
        <SingletonPartition/>
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

### <a name="front-end-servicemanifestxml"></a>ServiceManifest.xml front-end 
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="azurevotefrontPkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="azurevotefrontType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>
   
   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>my.azurecr.io/azure-vote-front:v1</ImageName>
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
      <Endpoint Name="azurevotefrontTypeEndpoint" UriScheme="http" Port="8080" Protocol="http"/>
    </Endpoints>
  </Resources>

 </ServiceManifest>
```

### <a name="redis-servicemanifestxml"></a>Redis ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="azurevotebackPkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="azurevotebackType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>
   
   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>my.azurecr.io/azure-vote-back:v1</ImageName>
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
      <Endpoint Name="azurevotebackTypeEndpoint" UriScheme="http" Port="6379" Protocol="http"/>
    </Endpoints>
  </Resources>
 </ServiceManifest>
```
## <a name="next-steps"></a>Passos seguintes

Neste tutorial, vários contentores foram empacotados para uma aplicação de Service Fabric utilizando Yeoman. Esta aplicação, em seguida, foi implementada e executada num cluster Service Fabric. Foram efetuados os seguintes passos:

> [!div class="checklist"]
> * Instalar o Yeoman  
> * Criar um pacote de aplicação utilizando o Yeoman
> * Configurar as definições no pacote de aplicação para utilização com contentores
> * Criar a aplicação  
> * Implementar e executar a aplicação 
> * Limpar a aplicação

Avançar para o próximo tutorial para saber mais sobre a ativação pós-falha e o dimensionamento da aplicação no Service Fabric.

> [!div class="nextstepaction"]
> [Saiba mais sobre a ativação pós-falha e dimensionamento de aplicações](service-fabric-tutorial-containers-failover.md)

[votingapp]: ./media/service-fabric-tutorial-deploy-run-containers/votingapp.png
[sfx]: ./media/service-fabric-tutorial-deploy-run-containers/containerspackagetutorialsfx.png


