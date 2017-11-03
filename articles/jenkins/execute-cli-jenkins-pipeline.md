---
title: Executar a CLI do Azure com Jenkins | Microsoft Docs
description: "Saiba como utilizar a CLI do Azure para implementar uma aplicação web Java no Azure no Pipeline de Jenkins"
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: jenkins
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: b93e787050613b241ea116e7263f63835bd211fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>Implementar no App Service do Azure com Jenkins e a CLI do Azure
Para implementar uma aplicação web Java no Azure, pode utilizar o CLI do Azure no [Jenkins Pipeline](https://jenkins.io/doc/book/pipeline/). Neste tutorial, vai criar um pipeline de CI/CD numa VM do Azure incluindo como:

> [!div class="checklist"]
> * Criar uma VM Jenkins
> * Configurar Jenkins
> * Criar uma aplicação web no Azure
> * Preparar um repositório do GitHub
> * Criar Jenkins pipeline
> * Executar o pipeline e certifique-se a aplicação web

Este tutorial requer a versão do módulo 2.0.4 ou posterior da CLI do Azure. Para localizar a versão, execute `az --version`. Se precisar de atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-configure-jenkins-instance"></a>Criar e configurar Jenkins instância
Se ainda não tiver um mestre Jenkins, começar a utilizar o [modelo de solução](install-jenkins-solution-template.md), que inclui o necessário [credenciais do Azure](https://plugins.jenkins.io/azure-credentials) Plug-in por predefinição. 

O plug-in de credencial do Azure permite-lhe armazenar as credenciais de principais de serviço do Microsoft Azure no Jenkins. Na versão 1.2, adicionámos o suporte para que esse Jenkins Pipeline pode obter as credenciais do Azure. 

Certifique-se de que tem a versão 1.2 ou posterior:
* No dashboard do Jenkins, clique em **Jenkins gerir -> Gestor de plug-in ->** e procure **credencial do Azure**. 
* Se a versão é anterior ao 1.2, atualize o plug-in.

Também é necessário no mestre de Jenkins JDK de Java e Maven. Para instalar, inicie sessão no mestre de Jenkins ao utilizar o SSH e execute os seguintes comandos:
```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Adicionar principal de serviço do Azure à credencial Jenkins

É necessária uma credencial do Azure para executar a CLI do Azure.

* No dashboard do Jenkins, clique em **credenciais -> sistema ->**. Clique em **Global credentials(unrestricted)**.
* Clique em **adicionar credenciais** para adicionar um [principal de serviço do Microsoft Azure](https://docs.microsoft.com/en-us/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) ao preencher o ID de subscrição, o ID de cliente, o segredo do cliente e o ponto final de tokens do OAuth 2.0. Forneça um ID para utilização num passo subsequente.

![Adicione as credenciais](./media/execute-cli-jenkins-pipeline/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Criar um serviço de aplicações do Azure para implementar a aplicação web de Java

Criar um plano do App Service do Azure com o **livres** utilizando o escalão de preço de [criar plano de serviço aplicacional az](/cli/azure/appservice/plan#create) comando da CLI. O plano de serviço aplicacional define os recursos físicos utilizados para alojar as suas aplicações. Todas as aplicações atribuídas a um plano de serviço aplicacional partilham destes recursos, permitindo-lhe guardar custo quando várias aplicações de alojamento. 

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \ 
    --resource-group myResourceGroup \
    --sku FREE
```

Quando estiver pronto o plano, a CLI do Azure mostra um resultado semelhante ao seguinte exemplo:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>Criar uma aplicação Web do Azure

 Utilize o [az webapp criar](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) comando da CLI para criar uma definição de aplicação web no `myAppServicePlan` plano do App Service. A definição da aplicação web fornece um URL para aceder à sua aplicação com e configura várias opções para implementar o código para o Azure. 

```azurecli-interactive
az webapp create \
    --name <app_name> \ 
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

Substitui o `<app_name>` marcador de posição com o seu próprio nome de aplicação único. Este nome exclusivo faz parte do nome de domínio predefinido para a aplicação web, para que o nome tem de ser exclusivo em todas as aplicações no Azure. Pode mapear uma entrada de nome de domínio personalizado para a aplicação web antes de expô-la aos seus utilizadores.

Quando a definição da aplicação web estiver pronta, a CLI do Azure mostra as informações semelhante ao seguinte exemplo: 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
   ...
  < Output has been truncated for readability >
}
```

### <a name="configure-java"></a>Configurar o Java 

Definir a configuração de tempo de execução do Java que precisa da aplicação com o [atualização az da configuração de web de serviço aplicacional](/cli/azure/appservice/web/config#update) comando.

O seguinte comando configura a aplicação web com um 8 JDK recentes do Java e [Apache Tomcat](http://tomcat.apache.org/) 8.0.

```azurecli-interactive
az webapp config set \ 
    --name <app_name> \
    --resource-group myResourceGroup \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>Preparar um repositório do GitHub
Abra o [simples aplicação de Web de Java do Azure](https://github.com/azure-devops/javawebappsample) repositório. Para copiar o repositório à sua própria conta do GitHub, clique em de **bifurcação** botão no canto superior direito.

* Na IU do GitHub web, abra **Jenkinsfile** ficheiro. Clique no ícone de lápis para editar este ficheiro para atualizar o grupo de recursos e o nome da sua aplicação web na linha 20 e 21, respetivamente.

```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<app_name>'
```

* Alterar linha 23 para atualizar o ID de credenciais na sua instância Jenkins

```java
withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
```

## <a name="create-jenkins-pipeline"></a>Criar Jenkins pipeline
Abra Jenkins num browser, clique em **Novo Item**. 

* Forneça um nome para a tarefa e selecione **Pipeline**. Clique em **OK**.
* Clique em de **Pipeline** separador seguinte. 
* Para **definição**, selecione **de Pipeline de script no SCM**.
* Para **SCM**, selecione **Git**.
* Introduza o URL do GitHub para o repositório forked: https:\<seu repositório forked\>.git
* Clique em **guardar**

## <a name="test-your-pipeline"></a>Testar o pipeline
* Vá para o pipeline que criou, clique em **compilar agora**
* Uma compilação deve ter êxito dentro de alguns segundos e pode ir para a compilação e clique em **resultado da consola** para ver os detalhes

## <a name="verify-your-web-app"></a>Certifique-se a sua aplicação web
Para verificar o WAR o ficheiro é implementado com êxito à sua aplicação web. Abra um browser:

* Aceda a http://&lt;APP_NAME>.azurewebsites.NET >.azurewebsites.net/api/calculator/ping  
Consulte:

        Welcome to Java Web App!!! This is updated!
        Sun Jun 17 16:39:10 UTC 2017

* Aceda a http://&lt;APP_NAME>.azurewebsites.NET >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y > (substituir &lt;x > e &lt;y > com os números) para obter a soma de x e y

![Calculadora: adicionar](./media/execute-cli-jenkins-pipeline/calculator-add.png)

## <a name="deploy-to-azure-web-app-on-linux"></a>Implementar a aplicação Web do Azure no Linux
Agora que sabe como utilizar a CLI do Azure no seu pipeline Jenkins, pode modificar o script para implementar uma aplicação de Web do Azure no Linux.

Web App no Linux suporta de forma diferente para efetuar a implementação, que consiste em utilizar o Docker. Para implementar, tem de fornecer um Dockerfile que a aplicação web com o tempo de execução do serviço de pacotes para uma imagem de Docker. O plug-in, em seguida, irá criar a imagem, enviá-lo para um registo de Docker e implementar a imagem à sua aplicação web.

* Siga os passos [aqui](../app-service/containers/quickstart-nodejs.md) para criar uma aplicação de Web do Azure em execução no Linux.
* Instalar Docker na sua instância Jenkins ao seguir as instruções deste [artigo](https://docs.docker.com/engine/installation/linux/ubuntu/).
* Criar um registo de contentor no portal do Azure, utilizando os passos [aqui](/azure/container-registry/container-registry-get-started-azure-cli).
* Da mesma [simples aplicação de Web de Java do Azure](https://github.com/azure-devops/javawebappsample) repositório escolhido, edite o **Jenkinsfile2** ficheiro:
    * Linha 18 21, atualizar para os nomes do seu grupo de recursos, a aplicação web e o ACR respetivamente. 
        ```
        def webAppResourceGroup = '<myResourceGroup>'
        def webAppName = '<app_name>'
        def acrName = '<myRegistry>'
        ```

    * Linha 24, atualizar \<azsrvprincipal\> ao seu ID de credenciais
        ```
        withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
        ```

* Criar um novo pipeline Jenkins como quando implementar a aplicação web do Azure no Windows, apenas neste momento, utilizou **Jenkinsfile2** em vez disso.
* Execute a tarefa de novo.
* Para verificar, na CLI do Azure, execute:

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    Obter o resultado seguinte:
    
    ```
    [
    "calculator"
    ]
    ```
    
    Aceda a http://&lt;APP_NAME>.azurewebsites.NET >.azurewebsites.net/api/calculator/ping. Consulte a mensagem: 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    Aceda a http://&lt;APP_NAME>.azurewebsites.NET >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y > (substituir &lt;x > e &lt;y > com os números) para obter a soma de x e y
    
## <a name="next-steps"></a>Passos seguintes
Neste tutorial, configurou um pipeline de Jenkins que verifica o código de origem no repositório do GitHub. Executa o Maven para criar um ficheiro war e, em seguida, utiliza a CLI do Azure para implementar no App Service do Azure. Aprendeu a:

> [!div class="checklist"]
> * Criar uma VM Jenkins
> * Configurar Jenkins
> * Criar uma aplicação web no Azure
> * Preparar um repositório do GitHub
> * Criar Jenkins pipeline
> * Executar o pipeline e certifique-se a aplicação web
