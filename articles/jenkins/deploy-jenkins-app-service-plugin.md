---
title: Implementar no App Service do Azure utilizando o plug-in de Jenkins | Microsoft Docs
description: "Saiba como utilizar o plug-in de Jenkins de serviço de aplicações do Azure para implementar uma aplicação web Java no Azure no Jenkins"
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 7/24/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: e38c69ec55d894053792fbf284d07944d7f44dc0
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="deploy-to-azure-app-service-by-using-the-jenkins-plugin"></a>Implementar no App Service do Azure utilizando o plug-in de Jenkins 

Para implementar uma aplicação web Java no Azure, pode utilizar a CLI do Azure no [Jenkins Pipeline](/azure/jenkins/execute-cli-jenkins-pipeline) ou pode utilizar o [Plug-in do Azure App Service Jenkins](https://plugins.jenkins.io/azure-app-service). O plug-in versão 1.0 do Jenkins suporta a implementação contínua utilizando a funcionalidade de aplicações Web do serviço de aplicações do Azure através de:
* Git e FTP.
* Docker para aplicações Web no Linux.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Configure Jenkins para implementar aplicações Web através do Git e FTP.
> * Configure Jenkins para implementar a aplicação Web para contentores.

## <a name="create-and-configure-a-jenkins-instance"></a>Criar e configurar uma instância de Jenkins

Se ainda não tiver um mestre Jenkins, começar a utilizar o [modelo de solução](install-jenkins-solution-template.md), que inclui o Java Development Kit (JDK) versão 8 e os seguintes necessários plug-ins de Jenkins:

* [Plug-in do Jenkins Git cliente](https://plugins.jenkins.io/git-client) versão 2.4.6 
* [Plug-in do docker Commons](https://plugins.jenkins.io/docker-commons) versão 1.4.0
* [Credenciais do Azure](https://plugins.jenkins.io/azure-credentials) versão 1.2
* [App Service do Azure](https://plugins.jenkins.io/azure-app-server) versão 0.1

Pode utilizar o plug-in de Jenkins para implementar uma aplicação web em qualquer idioma que é suportado pelas aplicações Web, como c#, PHP, Java e Node.js. Neste tutorial, utilizamos uma [simple aplicação de web de Java do Azure](https://github.com/azure-devops/javawebappsample). Para copiar o repositório à sua própria conta do GitHub, selecione o **bifurcação** botão no canto superior direito da interface do GitHub.  
> [!NOTE]
> É necessário o Java JDK e o Maven para criar o projeto de Java. Instale estes componentes no mestre de Jenkins, ou o agente da VM, se utilizar o agente de integração contínua. 

Para instalar os componentes, inicie sessão para a instância de Jenkins com SSH e execute os seguintes comandos:

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

Para implementar a aplicação Web para contentores, instale Docker no mestre de Jenkins ou o agente da VM que é utilizado para a compilação. Para obter instruções, consulte [instalar Docker no Ubuntu](https://docs.docker.com/engine/installation/linux/ubuntu/).

##<a name="service-principal"></a>Adicionar um principal de serviço do Azure para as credenciais de Jenkins

Precisa de um principal de serviço do Azure para implementar no Azure. 


1. Para criar um Azure principal de serviço, utilize o [CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) ou [portal do Azure](/azure/azure-resource-manager/resource-group-create-service-principal-portal).
2. No dashboard do Jenkins, selecione **credenciais** > **sistema**. Em seguida, selecione **Global credentials(unrestricted)**.
3. Para adicionar um principal de serviço do Microsoft Azure, selecione **adicionar credenciais**. Fornecer valores para o **ID de subscrição**, **ID de cliente**, **segredo do cliente**, e **ponto final de tokens do OAuth 2.0** campos. Definir o **ID** campo para **mySp**. Podemos utilizar este ID nos passos subsequentes neste artigo.


## <a name="configure-jenkins-to-deploy-web-apps-through-git-and-ftp"></a>Configurar Jenkins para implementar aplicações Web através do Git e FTP

Para implementar o projeto Web Apps, pode carregar os artefactos de compilação (por exemplo, um ficheiro WAR em Java) utilizando o Git ou FTP.

Antes de configurar a tarefa no Jenkins, precisará de um plano do App Service do Azure e uma aplicação web para executar a aplicação de Java.


1. Criar um plano do App Service do Azure com o **livres** escalão de preço, utilizando o `az appservice plan create` [comando da CLI do Azure](/cli/azure/appservice/plan#create). Plano do App Service define os recursos físicos que são utilizados para alojar as suas aplicações. Todas as aplicações que estão atribuídas a um plano do App Service partilharem esses recursos. Recursos partilhados ajudam-na guardar em custos quando várias aplicações de alojamento.
2. Crie uma aplicação Web. Pode utilizar o [portal do Azure](/azure/app-service-web/web-sites-configure) ou o seguinte `az` comando da CLI do Azure:
    ```azurecli-interactive 
    az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
    ```
    
3. Defina a configuração de tempo de execução do Java que necessita da sua aplicação. O seguinte comando da CLI do Azure configura a aplicação web para ser executada com 8 JDK recentes e [Apache Tomcat](http://tomcat.apache.org/) versão 8.0:
    ```azurecli-interactive
    az webapp config set \
    --name <myAppName> \
    --resource-group <myResourceGroup> \
    --java-version 1.8 \
    --java-container Tomcat \
    --java-container-version 8.0
    ```

### <a name="set-up-the-jenkins-job"></a>Configurar a tarefa de Jenkins

1. Crie um novo **freestyle** projeto no Dashboard do Jenkins.
2. Configurar o **gestão da origem de código** campo a utilizar o repositório de cópia local do [simple aplicação de web de Java do Azure](https://github.com/azure-devops/javawebappsample). Forneça o **URL do repositório** valor. Por exemplo: http://github.com/&lt;your_ID > / javawebappsample.
3. Adicione um passo para compilar o projeto com o Maven, adicionando o **executar shell** comando. Neste exemplo, é necessário um comando adicional para mudar o nome de \*.war ficheiro na pasta de destino para **ROOT.war**:   
    ```bash
    mvn clean package
    mv target/*.war target/ROOT.war
    ```

4. Adicionar uma ação de pós-compilação de selecionando **publicar uma aplicação Web do Azure**.
5. Fornecer **mySp** como o principal de serviço do Azure. Este principal era armazenada como o [credenciais do Azure](#service-principal) num passo anterior.
6. No **configuração de aplicação** secção, escolha a aplicação web e de grupo de recursos na sua subscrição. O plug-in de Jenkins Deteta automaticamente se a aplicação web é baseada no Windows ou Linux. Para uma aplicação web do Windows, o **publicar ficheiros** opção é apresentada.
7. Preencha os ficheiros que pretende implementar. Por exemplo, especifique o pacote WAR se estiver a utilizar o Java. Utilize a opção **diretório de origem** e **diretório de destino** parâmetros para especificar as pastas de origem e de destino a utilizar para carregamento de ficheiros. Aplicação web Java no Azure é executada num servidor Tomcat. Por isso, para Java, carregar o pacote WAR para a pasta webapps. Para este exemplo, definir o **diretório de origem** valor **destino** e **diretório de destino** valor **webapps**.
8. Se pretender implementar para uma ranhura diferente de produção, também pode definir o **ranhura** nome.
9. Guardar o projeto e crie-o. A aplicação web está implementada no Azure, quando a compilação for concluída.

### <a name="deploy-web-apps-through-ftp-by-using-jenkins-pipeline"></a>Implementar aplicações Web através de FTP através do Jenkins Pipeline

O plug-in Azure App Service Jenkins está pronto para pipeline. Pode consultar o exemplo seguinte no repositório GitHub.

1. Na interface do GitHub, abra o **Jenkinsfile_ftp_plugin** ficheiro. Para editar o ficheiro, selecione o ícone de lápis. Atualização do **resourceGroup** e **webAppName** definições para a sua aplicação web no e linhas de 11 12, respetivamente:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Definir o **withCredentials** definição na linha 14 para o ID de credenciais na sua instância Jenkins:
    ```java
    withCredentials([azureServicePrincipal('<mySp>')]) {
    ```

### <a name="create-a-jenkins-pipeline"></a>Criar um pipeline Jenkins

1. Abra Jenkins num web browser. Selecione **Novo Item**.
2. Forneça um nome para a tarefa e selecione **Pipeline**. Selecione **OK**.
3. Selecione o **Pipeline** separador.
4. Para o **definição** valor, selecione **de Pipeline de script no SCM**.
5. Para o **SCM** valor, selecione **Git**. Introduza o URL do GitHub para o repositório forked. Por exemplo: https://&lt;your_forked_repo > .git.
6. Atualização do **caminho do Script** valor **Jenkinsfile_ftp_plugin**.
7. Selecione **guardar** e executar a tarefa.

## <a name="configure-jenkins-to-deploy-web-app-for-containers"></a>Configurar Jenkins para implementar a aplicação Web para contentores

As Web Apps no Linux suporta implementação utilizando o Docker. Para implementar a aplicação web utilizando o Docker, terá de fornecer um Dockerfile que a aplicação web com um tempo de execução do serviço de pacotes para uma imagem de Docker. Em seguida, o plug-in de Jenkins baseia-se a imagem, pushes-lo para um registo de Docker e implementa a imagem à sua aplicação web.

As Web Apps no Linux também suporta métodos de implementação tradicionais, como o Git e FTP, mas apenas para idiomas incorporados (.NET Core, Node.js, PHP, Ruby e o). Para outros idiomas, terá de criar o pacote do tempo de execução do código e o serviço de aplicações em conjunto para uma imagem de Docker e utilizar o Docker para implementar.

Antes de configurar a tarefa no Jenkins, precisa de uma aplicação web no Linux. Também precisa de um registo de contentor para armazenar e gerir as imagens de contentor do Docker privadas. Pode utilizar DockerHub para criar o registo de contentor. Neste exemplo, utilizamos o registo de contentor do Azure.

* [Criar a sua aplicação web no Linux](../app-service/containers/quickstart-nodejs.md).
* Registo de contentor do Azure é um gerido [Docker registo](https://docs.docker.com/registry/) serviço com base na origem de abrir o registo de Docker versão 2.0. [Criar um registo de contentor do Azure](/azure/container-registry/container-registry-get-started-azure-cli). Também pode utilizar DockerHub.

### <a name="set-up-the-jenkins-job-for-docker"></a>Configurar a tarefa de Jenkins para Docker

1. Crie um novo **freestyle** projeto no Dashboard do Jenkins.
2. Configurar o **gestão da origem de código** campo a utilizar o repositório de cópia local do [simple aplicação de web de Java do Azure](https://github.com/azure-devops/javawebappsample). Forneça o **URL do repositório** valor. Por exemplo: http://github.com/&lt;your_ID > / javawebappsample.
3. Adicione um passo para compilar o projeto com o Maven, adicionando um **executar shell** comando. Inclua a seguinte linha de comando:
    ```bash
    mvn clean package
    ```

4. Adicionar uma ação de pós-compilação de selecionando **publicar uma aplicação Web do Azure**.
5. Fornecer **mySp** como o principal de serviço do Azure. Este principal era armazenada como o [credenciais do Azure](#service-principal) num passo anterior.
6. No **configuração de aplicação** secção, selecione o grupo de recursos e uma aplicação web do Linux na sua subscrição.
7. Escolha **publicar através do Docker**.
8. Preencha o **Dockerfile** valor de caminho. Pode manter o /Dockerfile de valor predefinido.
Para o **URL de registo do Docker** valor, forneça o URL utilizando o formato https://&lt;yourRegistry >. azurecr.io se utilizar o registo de contentor do Azure. Se utilizar DockerHub, deixe o valor em branco.
9. Para o **credenciais do registo** valor, adicione a credencial para o registo de contentor. Pode obter o ID de utilizador e palavra-passe, executando os comandos seguintes na CLI do Azure. O primeiro comando permite que a conta de administrador:
    ```azurecli-interactive
    az acr update -n <yourRegistry> --admin-enabled true
    az acr credential show -n <yourRegistry>
    ```

10. O valor de nome e a tag de imagem de Docker no **avançadas** separador são opcionais. Por predefinição, o valor para o nome de imagem é obtido a partir do nome da imagem que configurou no portal do Azure no **contentor de Docker** definição. A etiqueta é gerada a partir $BUILD_NUMBER.
    > [!NOTE]
    > É necessário especificar o nome de imagem no portal do Azure ou forneça um **Docker imagem** valor o **avançadas** separador. Neste exemplo, definir o **imagem Docker** valor &lt;your_Registry >.azurecr.io/calculator e deixe o **Tag de imagem de Docker** valor em branco.

11. A implementar falha se utilizar uma definição de imagem incorporada do Docker. Alterar a configuração do Docker para utilizar uma imagem personalizada no **contentor de Docker** definição no portal do Azure. Para uma imagem incorporada, utilize a abordagem de carregamento de ficheiros para implementar.
12. Semelhante para a abordagem de carregamento de ficheiros, pode escolher outro **ranhura** nome diferente de **produção**.
13. Guarde e compilar o projeto. A imagem do contentor é enviada para o registo e a aplicação web é implementada.

### <a name="deploy-web-app-for-containers-by-using-jenkins-pipeline"></a>Implementar a aplicação Web de contentores através do Jenkins Pipeline

1. Na interface do GitHub, abra o **Jenkinsfile_container_plugin** ficheiro. Para editar o ficheiro, selecione o ícone de lápis. Atualização do **resourceGroup** e **webAppName** definições para a sua aplicação web no e linhas de 11 12, respetivamente:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Altere linha 13 ao seu servidor de registo do contentor:
    ```java
    def registryServer = '<registryURL>'
    ```

3. Altere linha 16 ao utilizar o ID de credenciais na sua instância Jenkins:
    ```java
    azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
    ```

### <a name="create-a-jenkins-pipeline"></a>Criar um pipeline Jenkins    

1. Abra Jenkins num web browser. Selecione **Novo Item**.
2. Forneça um nome para a tarefa e selecione **Pipeline**. Selecione **OK**.
3. Selecione o **Pipeline** separador.
4. Para o **definição** valor, selecione **de Pipeline de script no SCM**.
5. Para o **SCM** valor, selecione **Git**. Introduza o URL do GitHub para o repositório forked. Por exemplo: https://&lt;your_forked_repo > .git.
7. Atualização do **caminho do Script** valor **Jenkinsfile_container_plugin**.
8. Selecione **guardar** e executar a tarefa.

## <a name="verify-your-web-app"></a>Certifique-se a sua aplicação web

1. Para verificar se o ficheiro WAR está implementado com êxito para a sua aplicação web, abra um browser.
2. Aceda a http://&lt;your_app_name >.azurewebsites.net/api/calculator/ping. Substitua &lt;your_app_name > com o nome da sua aplicação web. Consulte a mensagem:
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jun 17 16:39:10 UTC 2017
    ```

3. Aceda a http://&lt;your_app_name >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y >. Substitua &lt;x > e &lt;y > com os números para obter a soma de x + y. A Calculadora mostra a soma: ![Calculadora: adicionar](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-azure-app-service-on-linux"></a>Para o App Service do Azure no Linux

1. Para verificar a sua aplicação web, execute o seguinte comando na CLI do Azure:
    ```CLI
    az acr repository list -n <myRegistry> -o json
    ```
    É apresentada a seguinte mensagem:
    ```CLI
    ["calculator"]
    ```
    
2. Aceda a http://&lt;your_app_name >.azurewebsites.net/api/calculator/ping. Substitua &lt;your_app_name > com o nome da sua aplicação web. Consulte a mensagem: 
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jul 09 16:39:10 UTC 2017
    ```

3. Aceda a http://&lt;your_app_name >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y >. Substitua &lt;x > e &lt;y > com os números para obter a soma de x + y.
    
## <a name="next-steps"></a>Passos seguintes

Neste tutorial, utilizou o plug-in de Jenkins de serviço de aplicações do Azure para implementar no Azure.

Aprendeu a:

> [!div class="checklist"]
> * Configurar Jenkins para implementar o serviço de aplicações do Azure através de FTP 
> * Configurar Jenkins para implementar a aplicação Web para contentores 