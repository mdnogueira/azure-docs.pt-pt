---
title: "Implementar uma aplicação de arranque a utilizar o plug-in do Fabric8 Maven"
description: "Este tutorial orienta-o embora os passos para implementar uma aplicação de arranque de mola no Microsoft Azure utilizando o plug-in de Fabric8 para Apache Maven."
services: container-service
documentationcenter: java
author: rmcmurray
manager: routlaw
editor: 
ms.assetid: 
ms.service: container-service
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 09/15/2017
ms.author: yuwzho;robmcm
ms.custom: 
ms.openlocfilehash: 6ec48caef0eebeacf260b0dbc91b175368faea69
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="deploy-a-spring-boot-app-using-the-fabric8-maven-plugin"></a>Implementar uma aplicação de arranque a utilizar o plug-in do Fabric8 Maven

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

**[Fabric8]**  é uma solução de open source que está incorporada no  **[Kubernetes]**, que ajuda a programadores criam aplicações nos contentores do Linux.

Este tutorial explica-lhe utilizar o plug-in de Fabric8 para Maven para desenvolver para implementar uma aplicação para um anfitrião Linux o [serviço de contentor do Azure (ACS)].

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste tutorial, tem de ter os seguintes pré-requisitos:

* Uma subscrição do Azure; Se ainda não tiver uma subscrição do Azure, pode ativar o [benefícios de subscritor do MSDN] ou inscrever-se um [conta do Azure gratuita].
* O [Interface de linha de comandos do Azure (CLI)].
* Um atualizados [Java Developer Kit (JDK)].
* Do Apache [Maven] criar ferramenta (versão 3).
* A [Git] cliente.
* A [Docker] cliente.

> [!NOTE]
>
> Devido às necessidades de Virtualização deste tutorial, não é possível seguir os passos neste artigo numa máquina virtual; tem de utilizar um computador físico com funcionalidades de Virtualização ativadas.
>

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Criar o arranque de mola na aplicação de web de introdução do Docker

Os seguintes passos guiá-lo através da criação de uma aplicação web de arranque de mola e testá-lo localmente.

1. Abra uma linha de comandos e criar um diretório local para manter a sua aplicação e alterar a esse diretório; Por exemplo:
   ```shell
   md /home/GenaSoto/SpringBoot
   cd /home/GenaSoto/SpringBoot
   ```
   - ou -
   ```shell
   md C:\SpringBoot
   cd C:\SpringBoot
   ```

1. Clone o [arranque mola no Docker introdução] projeto de exemplo para o diretório.
   ```shell
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Altere o diretório para o projeto concluído; Por exemplo:
   ```shell
   cd gs-spring-boot-docker/complete
   ```
   - ou -
   ```shell
   cd gs-spring-boot-docker\complete
   ```

1. Utilize Maven para criar e executar a aplicação de exemplo.
   ```shell
   mvn clean package spring-boot:run
   ```

1. Testar a aplicação web, navegando para http://localhost:8080 ou com o seguinte `curl` comando:
   ```shell
   curl http://localhost:8080
   ```

   Deverá ver uma **mundo Olá Docker** mensagens apresentadas.

   ![Procure a aplicação de exemplo localmente][SB01]


## <a name="install-the-kubernetes-command-line-interface-and-create-an-azure-resource-group-using-the-azure-cli"></a>Instalar interface de linha de comandos Kubernetes e criar um grupo de recursos do Azure utilizando a CLI do Azure

1. Abra uma linha de comandos.

1. Escreva o seguinte comando para iniciar sessão sua conta do Azure:
   ```azurecli
   az login
   ```
   Siga as instruções para concluir o processo de início de sessão
   
   A CLI do Azure irá apresentar uma lista das suas contas; Por exemplo:

   ```json
   [
     {
       "cloudName": "AzureCloud",
       "id": "00000000-0000-0000-0000-000000000000",
       "isDefault": false,
       "name": "Windows Azure MSDN - Visual Studio Ultimate",
       "state": "Enabled",
       "tenantId": "00000000-0000-0000-0000-000000000000",
       "user": {
         "name": "Gena.Soto@wingtiptoys.com",
         "type": "user"
       }
     }
   ]
   ```

1. Se ainda não tiver a interface de linha de comandos Kubernetes (`kubectl`) instalado, pode instalar utilizando a CLI do Azure; por exemplo:
   ```azurecli
   az acs kubernetes install-cli
   ```

   > [!NOTE]
   >
   > Os utilizadores do Linux poderão ter de prefixo deste comando com `sudo` , uma vez que implementa o CLI Kubernetes para `/usr/local/bin`.
   >
   > Se já tiver `kubectl`) instalado e a versão do `kubectl` é demasiado antigo, poderá ver uma mensagem de erro semelhante ao exemplo a seguir ao tentar concluir os passos apresentados neste artigo:
   >
   > ```
   > error: group map[autoscaling:0x0000000000 batch:0x0000000000 certificates.k8s.io
   > :0x0000000000 extensions:0x0000000000 storage.k8s.io:0x0000000000 apps:0x0000000
   > 000 authentication.k8s.io:0x0000000000 policy:0x0000000000 rbac.authorization.k8
   > s.io:0x0000000000 federation:0x0000000000 authorization.k8s.io:0x0000000000 comp
   > onentconfig:0x0000000000] is already registered
   > ```
   >
   > Se isto acontecer, terá de reinstalar `kubectl` ao atualizar a sua versão.
   >

1. Criar um grupo de recursos para os recursos do Azure que irá utilizar neste tutorial; Por exemplo:
   ```azurecli
   az group create --name=wingtiptoys-kubernetes --location=westeurope
   ```
   Em que:  
      * *wingtiptoys kubernetes* é um nome exclusivo para o grupo de recursos  
      * *westeurope* é uma localização geográfica adequada para a sua aplicação  

   A CLI do Azure irá apresentar os resultados da sua criação do grupo de recursos; Por exemplo:  

   ```json
   {
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/wingtiptoys-kubernetes",
     "location": "westeurope",
     "managedBy": null,
     "name": "wingtiptoys-kubernetes",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "tags": null
   }
   ```


## <a name="create-a-kubernetes-cluster-using-the-azure-cli"></a>Criar um cluster de Kubernetes utilizando a CLI do Azure

1. Criar um cluster de Kubernetes no seu novo grupo de recursos; Por exemplo:  
   ```azurecli 
   az acs create --orchestrator-type kubernetes --resource-group wingtiptoys-kubernetes --name wingtiptoys-cluster --generate-ssh-keys --dns-prefix=wingtiptoys
   ```
   Em que:  
      * *wingtiptoys kubernetes* é o nome do grupo de recursos de anteriormente no artigo  
      * *wingtiptoys cluster* é um nome exclusivo para o seu cluster Kubernetes
      * *wingtiptoys* é um nome DNS do nome exclusivo para a sua aplicação

   A CLI do Azure irá apresentar os resultados da sua criação do cluster; Por exemplo:  

   ```json
   {
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/wingtiptoys-kubernetes/providers/Microsoft.Resources/deployments/azurecli0000000000.00000000000",
     "name": "azurecli0000000000.00000000000",
     "properties": {
       "correlationId": "00000000-0000-0000-0000-000000000000",
       "debugSetting": null,
       "dependencies": [],
       "mode": "Incremental",
       "outputs": {
         "masterFQDN": {
           "type": "String",
           "value": "wingtiptoysmgmt.westeurope.cloudapp.azure.com"
         },
         "sshMaster0": {
           "type": "String",
           "value": "ssh azureuser@wingtiptoysmgmt.westeurope.cloudapp.azure.com -A -p 22"
         }
       },
       "parameters": {
         "clientSecret": {
           "type": "SecureString"
         }
       },
       "parametersLink": null,
       "providers": [
         {
           "id": null,
           "namespace": "Microsoft.ContainerService",
           "registrationState": null,
           "resourceTypes": [
             {
               "aliases": null,
               "apiVersions": null,
               "locations": [
                 "westeurope"
               ],
               "properties": null,
               "resourceType": "containerServices"
             }
           ]
         }
       ],
       "provisioningState": "Succeeded",
       "template": null,
       "templateLink": null,
       "timestamp": "2017-09-15T01:00:00.000000+00:00"
     },
     "resourceGroup": "wingtiptoys-kubernetes"
   }
   ```

1. Transferir as suas credenciais para o novo cluster Kubernetes; Por exemplo:  
   ```azurecli 
   az acs kubernetes get-credentials --resource-group=wingtiptoys-kubernetes --name wingtiptoys-cluster
   ```

1. Verificar a ligação com o seguinte comando:
   ```shell 
   kubectl get nodes
   ```

   Deverá ver uma lista de nós e Estados semelhante ao seguinte exemplo:

   ```shell
   NAME                    STATUS                     AGE       VERSION
   k8s-agent-00000000-0    Ready                      5h        v1.6.6
   k8s-agent-00000000-1    Ready                      5h        v1.6.6
   k8s-agent-00000000-2    Ready                      5h        v1.6.6
   k8s-master-00000000-0   Ready,SchedulingDisabled   5h        v1.6.6
   ```


## <a name="create-a-private-azure-container-registry-using-the-azure-cli"></a>Criar um registo de contentor do Azure privada utilizando a CLI do Azure

1. Criar um registo de contentor do Azure privada no seu grupo de recursos para alojar a imagem de Docker; Por exemplo:
   ```azurecli
   az acr create --admin-enabled --resource-group wingtiptoys-kubernetes --location westeurope --name wingtiptoysregistry --sku Basic
   ```
   Em que:  
      * *wingtiptoys kubernetes* é o nome do grupo de recursos de anteriormente no artigo  
      * *wingtiptoysregistry* é um nome exclusivo para o seu registo privado
      * *westeurope* é uma localização geográfica adequada para a sua aplicação  

   A CLI do Azure irá apresentar os resultados da criação do registo; Por exemplo:  

   ```json
   {
     "adminUserEnabled": true,
     "creationDate": "2017-09-15T01:00:00.000000+00:00",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/wingtiptoys-kubernetes/providers/Microsoft.ContainerRegistry/registries/wingtiptoysregistry",
     "location": "westeurope",
     "loginServer": "wingtiptoysregistry.azurecr.io",
     "name": "wingtiptoysregistry",
     "provisioningState": "Succeeded",
     "resourceGroup": "wingtiptoys-kubernetes",
     "sku": {
       "name": "Basic",
       "tier": "Basic"
     },
     "storageAccount": {
       "name": "wingtiptoysregistr000000"
     },
     "tags": {},
     "type": "Microsoft.ContainerRegistry/registries"
   }
   ```

1. Obter a palavra-passe para o registo de contentor a partir da CLI do Azure.
   ```azurecli
   az acr credential show --name wingtiptoysregistry --query passwords[0]
   ```

   A CLI do Azure irá apresentar a palavra-passe para o seu registo; Por exemplo:  

   ```json
   {
     "name": "password",
     "value": "AbCdEfGhIjKlMnOpQrStUvWxYz"
   }
   ```

1. Navegue para o diretório de configuração para a sua instalação Maven (predefinição ~/.m2/ ou C:\Users\username\.m2) e abra o *settings.xml* ficheiro com um editor de texto.

1. Adicionar o seu URL de registo de contentor do Azure, o nome de utilizador e a palavra-passe para um novo `<server>` coleção no *settings.xml* ficheiro.
O `id` e `username` são o nome do registo. Utilize o `password` valor do comando anterior (sem aspas).

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry.azurecr.io</id>
         <username>wingtiptoysregistry</username>
         <password>AbCdEfGhIjKlMnOpQrStUvWxYz</password>
      </server>
   </servers>
   ```

1. Navegue para o diretório de projeto concluída para a sua aplicação de arranque de mola (por exemplo, "*C:\SpringBoot\gs-spring-boot-docker\complete*"ou"*/home/GenaSoto/SpringBoot/gs-spring-boot-docker/complete* ") e abra o *pom.xml* ficheiro com um editor de texto.

1. Atualização do `<properties>` coleção no *pom.xml* ficheiro com o valor do servidor de início de sessão para o registo de contentor do Azure.

   ```xml
   <properties>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
   </properties>
   ```

1. Atualização do `<plugins>` coleção no *pom.xml* ficheiro para que o `<plugin>` contém o nome de registo e o endereço de servidor de início de sessão para o registo de contentor do Azure.

   ```xml
   <plugin>
     <groupId>com.spotify</groupId>
     <artifactId>dockerfile-maven-plugin</artifactId>
     <version>1.3.4</version>
     <configuration>
       <repository>${docker.image.prefix}/${project.artifactId}</repository>
       <serverId>${docker.image.prefix}</serverId>
       <registryUrl>https://${docker.image.prefix}</registryUrl>
     </configuration>
   </plugin>
   ```

1. Navegue para o diretório de projeto concluída para a sua aplicação de arranque de mola e execute o seguinte comando Maven para criar o contentor de Docker e enviar a imagem para o registo:

   ```shell
   mvn package dockerfile:build -DpushImage
   ```

   Maven irá apresentar os resultados da sua compilação; Por exemplo:  

   ```shell
   [INFO] ----------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ----------------------------------------------------
   [INFO] Total time: 38.113 s
   [INFO] Finished at: 2017-09-15T02:00:00-07:00
   [INFO] Final Memory: 47M/338M
   [INFO] ----------------------------------------------------
   ```


## <a name="configure-your-spring-boot-app-to-use-the-fabric8-maven-plugin"></a>Configure a sua aplicação de arranque a utilizar o plug-in de Fabric8 Maven

1. Navegue para o diretório de projeto concluída para a sua aplicação de arranque de mola (por exemplo: "*C:\SpringBoot\gs-spring-boot-docker\complete*"ou"*/home/GenaSoto/SpringBoot/gs-spring-boot-docker / completa*") e abra o *pom.xml* ficheiro com um editor de texto.

1. Atualização do `<plugins>` coleção no *pom.xml* ficheiro para adicionar o plug-in de Fabric8 Maven:

   ```xml
   <plugin>
     <groupId>io.fabric8</groupId>
     <artifactId>fabric8-maven-plugin</artifactId>
     <version>3.5.30</version>
     <configuration>
       <ignoreServices>false</ignoreServices>
       <registry>${docker.image.prefix}</registry>
     </configuration>
   </plugin>
   ```

1. Navegue para o diretório de origem principal para a sua aplicação de arranque de mola (por exemplo: "*C:\SpringBoot\gs-spring-boot-docker\complete\src\main*"ou"*/home/GenaSoto/SpringBoot/gs-spring-boot-docker / concluir/src/main*") e crie uma nova pasta com o nome"*fabric8*".

1. Criar três ficheiros de fragmento YAML na nova *fabric8* pasta:

   a. Crie um ficheiro denominado **deployment.yml** com o seguinte conteúdo:
      ```yaml
      apiVersion: extensions/v1beta1
      kind: Deployment
      metadata:
        name: ${project.artifactId}
        labels:
          run: gs-spring-boot-docker
      spec:
        replicas: 1
        selector:
          matchLabels:
            run: gs-spring-boot-docker
        strategy:
          rollingUpdate:
            maxSurge: 1
            maxUnavailable: 1
          type: RollingUpdate
        template:
          metadata:
            labels:
              run: gs-spring-boot-docker
          spec:
            containers:
            - image: ${docker.image.prefix}/${project.artifactId}:latest
              name: gs-spring-boot-docker
              imagePullPolicy: Always
              ports:
              - containerPort: 8080
            imagePullSecrets:
              - name: mysecrets
      ```

   b. Crie um ficheiro denominado **secrets.yml** com o seguinte conteúdo:
      ```yaml
      apiVersion: v1
      kind: Secret
      metadata:
        name: mysecrets
        namespace: default
        annotations:
          maven.fabric8.io/dockerServerId:  ${docker.image.prefix}
      type: kubernetes.io/dockercfg
      ```

   c. Crie um ficheiro denominado **service.yml** com o seguinte conteúdo:
      ```yaml
      apiVersion: v1
      kind: Service
      metadata:
        name: ${project.artifactId}
        labels:
          expose: "true"
      spec:
        ports:
        - port: 80
          targetPort: 8080
        type: LoadBalancer
      ```

1. Execute o seguinte comando Maven para criar o ficheiro de lista de recursos de Kubernetes:

   ```shell
   mvn fabric8:resource
   ```

   Este comando une todos os ficheiros de yaml de recurso Kubernetes sob o *src/main/fabric8* pasta para um ficheiro YAML que contém uma lista de recursos Kubernetes, que pode ser aplicada a Kubernetes cluster diretamente ou exportar um gráfico de helm.

   Maven irá apresentar os resultados da sua compilação; Por exemplo:  

   ```shell
   [INFO] ----------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ----------------------------------------------------
   [INFO] Total time: 16.744 s
   [INFO] Finished at: 2017-09-15T02:35:00-07:00
   [INFO] Final Memory: 30M/290M
   [INFO] ----------------------------------------------------
   ```

1. Execute o seguinte comando Maven para aplicar o ficheiro de lista de recursos para o cluster Kubernetes:

   ```shell
   mvn fabric8:apply
   ```

   Maven irá apresentar os resultados da sua compilação; Por exemplo:  

   ```shell
   [INFO] ----------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ----------------------------------------------------
   [INFO] Total time: 14.814 s
   [INFO] Finished at: 2017-09-15T02:41:00-07:00
   [INFO] Final Memory: 35M/288M
   [INFO] ----------------------------------------------------
   ```

1. Assim que a aplicação for implementada para o cluster, consultar o externo endereço IP com o `kubectl` aplicação; por exemplo:
   ```shell
   kubectl get svc -w
   ```

   `kubectl`irá apresentar os endereços IP internos e externos; Por exemplo:
   
   ```shell
   NAME                    CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
   kubernetes              10.0.0.1     <none>        443/TCP        19h
   gs-spring-boot-docker   10.0.242.8   13.65.196.3   80:31215/TCP   3m
   ```
   
   Pode utilizar o endereço IP externo para abrir a aplicação num web browser.

   ![Procure a aplicação de exemplo externamente][SB02]

## <a name="delete-your-kubernetes-cluster"></a>Elimina o cluster Kubernetes

Quando o cluster Kubernetes já não é necessário, pode utilizar o `az group delete` comando para remover o grupo de recursos, o que removerá todos os respetivos recursos relacionados; por exemplo:

   ```azurecli
   az group delete --name wingtiptoys-kubernetes --yes --no-wait
   ```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como utilizar aplicações de arranque de mola no Azure, consulte os artigos seguintes:

* [Implementar uma aplicação de arranque mola para o App Service do Azure](../../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)
* [Implementar uma aplicação de arranque de mola no Linux no serviço de contentor do Azure](container-service-deploy-spring-boot-app-on-linux.md)
* [Implementar uma aplicação de arranque mola num Cluster Kubernetes no serviço de contentor do Azure](container-service-deploy-spring-boot-app-on-kubernetes.md)

Para obter mais informações sobre como utilizar o Azure com o Java, veja o [Centro de Programadores de Java do Azure] e [Java Tools for Visual Studio Team Services] (Ferramentas de Java para o Visual Studio Team Services).

Para obter mais detalhes sobre o arranque de mola no projeto de exemplo do Docker, consulte [arranque mola no Docker introdução].

Para obter ajuda com a introdução com as suas próprias aplicações de mola arranque, consulte o **mola Initializr** em <https://start.spring.io/>.

Para obter mais informações sobre como começar a criar uma aplicação de arranque de mola simples, consulte o Initializr mola em <https://start.spring.io/>.

Para obter exemplos adicionais sobre como utilizar imagens personalizadas do Docker com o Azure, consulte [utilizando uma imagem personalizada do Docker para aplicação de Web do Azure no Linux].

<!-- URL List -->

[Interface de linha de comandos do Azure (CLI)]: /cli/azure/overview
[serviço de contentor do Azure (ACS)]: https://azure.microsoft.com/services/container-service/
[Centro de Programadores de Java do Azure]: https://azure.microsoft.com/develop/java/
[Azure portal]: https://portal.azure.com/
[Create a private Docker container registry using the Azure portal]: /azure/container-registry/container-registry-get-started-portal
[utilizando uma imagem personalizada do Docker para aplicação de Web do Azure no Linux]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[Fabric8]: https://fabric8.io/
[conta do Azure gratuita]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Kubernetes]: https://kubernetes.io/
[Maven]: http://maven.apache.org/
[benefícios de subscritor do MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[arranque mola no Docker introdução]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/container-service-deploy-spring-boot-app-using-fabric8-maven-plugin/SB01.png
[SB02]: ./media/container-service-deploy-spring-boot-app-using-fabric8-maven-plugin/SB02.png
