---
title: "Utilizar Jenkins para implementar as suas aplicações web no Azure | Microsoft Docs"
description: "Defina a integração contínua a partir do GitHub App Service do Azure para as suas aplicações web de Java utilizando Jenkins e Docker."
author: rloutlaw
manager: douge
ms.service: jenkins
ms.search.scope: 
ms.devlang: java
ms.topic: article
ms.workload: web
ms.date: 08/02/2017
ms.author: routlaw
ms.custom: Jenkins, devcenter
ms.openlocfilehash: 40d7e822b586e6f6b4addcd7d4e107eda9f4ab11
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="set-up-continuous-integration-and-deployment-to-azure-app-service-with-jenkins"></a>Configurar a integração contínua e implementação para o serviço de aplicações do Azure com Jenkins

Este tutorial define a integração contínua e implementação (CI/CD) de uma aplicação de web de Java de exemplo desenvolvidas com o [mola arranque](http://projects.spring.io/spring-boot/) framework [Azure aplicação Web do App Service no Linux](/azure/app-service/containers/app-service-linux-intro) Jenkins a utilizar.

Irá efetuar as seguintes tarefas neste tutorial:

> [!div class="checklist"]
> * Instale os plug-ins Jenkins necessários para implementar no App Service do Azure.
> * Defina uma tarefa de Jenkins para criar imagens de Docker de um repositório do GitHub, quando é feito o Push de consolidação de novo.
> * Definir uma nova aplicação de Web do Azure para Linux e configurá-lo para implementar imagens do Docker instaladas no registo de contentor do Azure.
> * Configure o Azure App Service Jenkins Plug-in.
> * Implemente a aplicação de exemplo para o App Service do Azure com uma compilação manual.
> * Acionar uma compilação Jenkins e atualizar a aplicação web ao enviar as alterações no GitHub.

## <a name="before-you-begin"></a>Antes de começar

Para concluir este tutorial, precisa de:

* [Jenkins](https://jenkins.io/) com ferramentas JDK e Maven configuradas. Se não tiver um sistema Jenkins, crie uma agora no Azure a partir de [modelo de solução Jenkins](/azure/jenkins/install-jenkins-solution-template).
* A [GitHub](https://github.com) conta.
* [Azure CLI 2.0](/cli/azure/overview), da sua linha de comandos local ou no [Shell de nuvem do Azure](/azure/cloud-shell/overview)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-jenkins-plug-ins"></a>Instalar Jenkins plug-ins

1. Abra um browser para a consola da web de Jenkins e selecione **gerir Jenkins** no menu da esquerda, em seguida, selecione **gerir plug-ins**.
2. Selecione o **disponível** separador.
3. Procure e selecione a caixa de verificação junto aos plug-ins seguintes:   

    - [App Service do Azure Plug-in](https://plugins.jenkins.io/azure-app-service)
    - [Origem de sucursal de GitHub Plug-in](https://plugins.jenkins.io/github-branch-source)

    Se o plug-ins não aparecerem, certifique-se de que ainda não estiverem instalados, verificando o **instalada** separador.

1. Selecione **agora transferir e instalar após o reinício** para ativar o plug-ins na configuração Jenkins.

## <a name="configure-github-and-jenkins"></a>Configurar o GitHub e Jenkins

Configurar Jenkins para receber [GitHub webhooks](https://developer.github.com/webhooks/) quando consolidações novo são enviadas por push para um repositório na sua conta.

1. Selecione **gerir Jenkins**, em seguida, **Configurar sistema**. No **GitHub** secção, certifique-se **gerir hooks** está selecionada e, em seguida, selecione **gerir ações adicionais do GitHub** e escolha **converter início de sessão e palavra-passe para o token**.
2. Selecione o **de início de sessão e palavra-passe** botão de opção e introduza o nome de utilizador do GitHub e a palavra-passe. Selecione **criar token credenciais** para criar uma nova [GitHub pessoais acesso Token](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).   
   ![Criar o GitHub PAT de início de sessão e palavra-passe](media/jenkins-java-quickstart/create_github_credentials.png)
3.  Selecione o token de recentemente criado o **credenciais** pendente na configuração de GitHub servidores. Selecione **Testar ligação** para verificar se a autenticação está a funcionar.   
   ![Verificar a ligação ao GitHub depois de ter configurada TERESA](media/jenkins-java-quickstart/verify_github_connection.png)

> [!NOTE]
> Se a sua conta GitHub tiver autenticação de dois fatores ativada, crie o token no GitHub e configure Jenkins utilizá-la. Reveja o [Jenkins GitHub Plug-in](https://wiki.jenkins.io/display/JENKINS/Github+Plugin) documentação para obter detalhes completos.

## <a name="fork-the-sample-repo-and-create-a-jenkins-job"></a>Copiar o repositório de exemplo e criar uma tarefa de Jenkins 

1. Abra o [repositório de aplicação de exemplo de arranque de mola](https://github.com/spring-guides/gs-spring-boot-docker) e bifurcá-lo à sua própria conta GitHub selecionando **bifurcação** no canto superior direito.   
    ![Bifurcação a partir do GitHub](media/jenkins-java-quickstart/fork_github_repo.png)
1. Na consola web do Jenkins, selecione **Novo Item**, atribua um nome **MyJavaApp**, selecione **projeto Freestyle**, em seguida, selecione **OK**.   
    ![Novo projeto de Jenkins Freestyle](media/jenkins-java-quickstart/jenkins_freestyle.png)
2. Sob o **geral** secção, selecione **GitHub** projeto e introduza o URL do repositório escolhido como https://github.com/raisa/gs-spring-boot-docker
3. Sob o **gestão de código de origem** secção, selecione **Git**, introduza o seu repositório forked `.git` URL como https://github.com/raisa/gs-spring-boot-docker.git
4. Sob o **criar Acionadores** secção, selecione **acionador de rotina do GitHub para consulta GITscm**.
5. Sob o **criar** secção, selecione **Adicionar passo de compilação** e escolha **invocar destinos nível superior do Maven**. Introduza `package` no **objetivos** campo.
6. Selecione **Guardar**. Pode testar a sua tarefa selecionando **compilar agora** da página de projeto.

## <a name="configure-azure-app-service"></a>Configurar o App Service do Azure 

1. Utilizar a CLI do Azure ou [nuvem Shell](/azure/cloud-shell/overview), crie um novo [aplicação Web no Linux](/azure/app-service/containers/app-service-linux-intro). É o nome da aplicação web neste tutorial `myJavaApp`, mas tem de utilizar um nome exclusivo para a sua própria aplicação.
   
    ```azurecli-interactive
    az group create --name myResourceGroupJenkins --location westus
    az appservice plan create --is-linux --name myLinuxAppServicePlan --resource-group myResourceGroupJenkins 
    az webapp create --name myJavaApp --resource-group myResourceGroupJenkins --plan myLinuxAppServicePlan --runtime "java|1.8|Tomcat|8.5"
    ```

2. Criar um [registo de contentor do Azure](/azure/container-registry/container-registry-intro) para armazenar as imagens de Docker por Jenkins. É o nome do registo de contentor utilizado neste tutorial `jenkinsregistry`, mas tem de utilizar um nome exclusivo para a sua própria registo de contentor. 

    ```azurecli-interactive
    az acr create --name jenkinsregistry --resource-group myResourceGroupJenkins --sku Basic --admin-enabled
    ```
3. Configurar a aplicação web para executar as imagens de Docker enviadas para o registo de contentor e especifique que a aplicação em execução no contentor escuta pedidos na porta 8080.   

    ```azurecli-interactive
    az webapp config container set -c jenkinsregistry/webapp --resource-group myResourceGroupJenkins --name myJavaApp
    az webapp config appsettings set --resource-group myResourceGroupJenkins --name myJavaApp --settings PORT=8080
    ```

## <a name="configure-the-azure-app-service-jenkins-plug-in"></a>Configurar o Azure App Service Jenkins Plug-in

1. Na consola web do Jenkins, selecione o **MyJavaApp** da tarefa que criou e, em seguida, selecione **configurar** no lado esquerdo da página.
2. Desloque para baixo até **ações de pós-compilação de**, em seguida, selecione **adicionar ação de pós-compilação de** e escolha **publicar uma aplicação Web do Azure**.
3. Em **Azure perfil de configuração**, selecione **adicionar** junto a **credenciais do Azure** e escolha **Jenkins**.
4. No **adicionar credenciais** caixa de diálogo, selecione **Principal de serviço do Microsoft Azure** do **tipo** pendente.
5. Criar um principal de serviço do Active Directory a partir da CLI do Azure ou [nuvem Shell](/azure/cloud-shell/overview).
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
6. Introduza as credenciais do serviço principal para o **adicionar credenciais** caixa de diálogo. Se não souber o ID de subscrição do Azure, pode consultá-lo a partir da CLI do:
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    ![Configurar o Principal de serviço do Azure](media/jenkins-java-quickstart/azure_service_principal.png)
6. Certifique-se de que o principal de serviço efetua a autenticação com o Azure ao selecionar **verificar Principal de serviço**. 
7. Selecione **adicionar** para guardar as credenciais.
8. Selecionar a credencial de principal de serviço que acabou de adicionar a partir de **credenciais do Azure** pendente quando estiver novamente para o **publicar uma aplicação Web do Azure** configuração.
9. No **configuração de aplicação**, escolha o grupo de recursos e nome da aplicação de lista pendente web.
10. Selecione o **publicar através do Docker** botão de opção.
11. Introduza `complete/Dockerfile` para **Dockerfile caminho**.
12. Introduza `https://jenkinsregistry.azurecr.io` no **URL de registo do Docker** campo.
13. Selecione **adicionar** junto a **registo credenciais**. 
14. Introduza o nome de utilizador de administrador para o registo de contentor do Azure que criou para o **Username**.
15. Introduza a palavra-passe para o registo de contentor do Azure no **palavra-passe** campo. Pode obter o nome de utilizador e palavra-passe do portal do Azure ou através do comando CLI seguinte:

    ```azurecli-interactive
    az acr credential show -n jenkinsregistry
    ```
    ![Adicione as credenciais de registo do contentor](media/jenkins-java-quickstart/enter_acr_credentials.png)
15. Selecione **adicionar** para guardar a credencial.
16. Selecione a credencial recentemente criada do **credenciais do registo** pendente no **configuração de aplicação** painel para o **publicar uma aplicação Web do Azure**. A ação de pós-compilação de terminar deverá ser semelhante a imagem seguinte:   
    ![Configuração de ação de compilação de POST para implementação de serviço de aplicações do Azure](media/jenkins-java-quickstart/appservice_plugin_configuration.png)
17. Selecione **guardar** para guardar a configuração da tarefa.

## <a name="deploy-the-app-from-github"></a>Implementar a aplicação a partir do GitHub

1. A partir do projeto Jenkins, selecione **compilar agora** para implementar a aplicação de exemplo no Azure.
2. Uma vez concluída a compilação, a aplicação está em direto no Azure de publicação URL, por exemplo http://myjavaapp.azurewebsites.net.   
   ![Ver a sua aplicação implementada no Azure](media/jenkins-java-quickstart/hello_docker_world_unedited.png)

## <a name="push-changes-and-redeploy"></a>Emitir alterações e volte a implementar

1. Partir do seu bifurcação do Github, navegar na web para `complete/src/main/java/Hello/Application.java`. Selecione o **editar este ficheiro** ligação do lado direito da interface do GitHub.
2. Efetue a alteração seguinte para o `home()` método e confirmar a alteração para o ramo principal do repositório.
   
    ```java
    return "Hello Docker World on Azure";
    ```
3. Inicia uma nova compilação no Jenkins, é acionada pela consolidação de novo no `master` ramo do repositório. Depois de terminar, volte a carregar a aplicação no Azure.     
      ![Ver a sua aplicação implementada no Azure](media/jenkins-java-quickstart/hello_docker_world.png)
  
## <a name="next-steps"></a>Passos seguintes

- [Utilize as VMs do Azure como agentes de compilação](/azure/jenkins/jenkins-azure-vm-agents)
- [Gerir os recursos nas tarefas e pipelines com a CLI do Azure](/azure/jenkins/execute-cli-jenkins-pipeline)
 
