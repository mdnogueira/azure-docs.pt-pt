---
title: CI/CD de Jenkins para as VMs do Azure com VSTS | Microsoft Docs
description: "Configurar a integração contínua (CI) e a implementação contínua (CD) de uma aplicação Node.js utilizando Jenkins para VMs do Azure da gestão de versão em serviços de equipa do Visual Studio (VSTS) ou o Microsoft Team Foundation Server (TFS)"
author: ahomer
manager: douge
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/19/2017
ms.author: ahomer
ms.custom: mvc
ms.openlocfilehash: d5c15d6ab36a8454d1c69bac498be89b990c7e33
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="deploy-your-app-to-linux-vms-using-jenkins-and-vsts"></a>Implementar a sua aplicação para VMs com Linux utilizando Jenkins e VSTS

A integração contínua (CI) e a implementação contínua (CD) é um pipeline através do qual pode criar, versão e implementar o seu código. Serviços de equipa do Visual Studio (VSTS) fornece um conjunto de terminar, excelentes das ferramentas de automatização de CI/CD para implementação no Azure. Jenkins é uma popular 3rd terceiros CI/CD baseadas em servidor ferramenta que também fornece a automatização de CI/CD. Pode utilizar ambos em conjunto para personalizar como fornecer o serviço ou aplicação na nuvem.

Neste tutorial, utilize Jenkins para criar um **aplicação web Node.js**e VSTS ou o Team Foundation Server (TFS) implementá-la para um [grupo de implementação](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) que contêm máquinas virtuais do Linux.

Irá:

> [!div class="checklist"]
> * Obter a aplicação de exemplo
> * Configurar o plug-ins de Jenkins
> * Configurar um projeto de Jenkins Freestyle para Node.js
> * Configurar Jenkins para integração VSTS
> * Criar um ponto final de serviço de Jenkins
> * Criar um grupo de implementação para as máquinas virtuais do Azure
> * Criar uma definição de versão VSTS
> * Executar manual e implementações de CI acionada

## <a name="before-you-begin"></a>Antes de começar

* Precisa de acesso a um servidor de Jenkins. Se ainda não criou um servidor de Jenkins, consulte [criar um principal de Jenkins uma Máquina Virtual no Azure](https://docs.microsoft.com/en-us/azure/jenkins/install-jenkins-solution-template). 

* Inicie sessão na sua conta VSTS (`https://{youraccount}.visualstudio.com`). 
  Pode obter um [VSTS conta gratuita](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Para obter mais informações, consulte [ligar ao VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).

*  Precisa de uma máquina virtual Linux para um destino de implementação.  Para obter mais informações, consulte [criar e gerir VMs com Linux com a CLI do Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/tutorial-manage-vm).

*  Abra a porta de entrada 80 para a máquina virtual.  Para obter mais informações, consulte [criar grupos de segurança de rede através do portal do Azure](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

## <a name="get-the-sample-app"></a>Obter a aplicação de exemplo

Precisa de uma aplicação a implementar armazenados num repositório de Git.
Para este tutorial, recomendamos que utilize [esta aplicação de exemplo disponível a partir do GitHub](https://github.com/azooinmyluggage/fabrikam-node).  Este tutorial contém um script de exemplo utilizado para instalar o Node.js e uma aplicação.  Se pretende trabalhar com o seus próprios repositório, deve configurar uma amostra semelhante.

1. Criar uma bifurcação desta aplicação e tome nota da localização (URL) para utilização em passos posteriores deste tutorial.  Para obter mais informações, consulte [Bifurcar um repositório](https://help.github.com/articles/fork-a-repo/)    

> [!NOTE]
> A aplicação foi construída utilizando [Yeoman](http://yeoman.io/learning/index.html); utiliza **Express**, **bower**, e **grunt**; e tem algumas **npm** pacotes como dependências.
> O exemplo contém também um script que configura Nginx e implementa a aplicação. Este é executado nas máquinas virtuais. Especificamente, o script instala o nó, Nginx e PM2; Configura Nginx e PM2; em seguida, inicia a aplicação de nó.

## <a name="configure-jenkins-plugins"></a>Configurar o plug-ins de Jenkins

Em primeiro lugar, tem de configurar dois Jenkins plug-ins para **NodeJS** e **VS serviços a equipa de implementação contínua**.

1. Abra a sua conta Jenkins e escolha **gerir Jenkins**.
2. No **gerir Jenkins** página, escolha **gerir plug-ins**.
3. Filtrar a lista para localizar o **NodeJS** Plug-in e escolha o **instalar sem reinício** opção.
    ![Adicionar o plug-in do NodeJS para Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)
4. Filtrar a lista para localizar o **VS serviços a equipa de implementação contínua** Plug-in e escolha o **instalar sem reinício** opção.
5. Navegue de volta para o dashboard Jenkins e escolha **gerir Jenkins**.
6. Escolha **Global ferramenta configuração**.  Localizar **NodeJS** e clique em **NodeJS instalações**.
7. Ativar o **instalar automaticamente** opção e, em seguida, introduza um **nome** valor.
8. Clique em **Guardar**.

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>Configurar um projeto de Jenkins Freestyle para Node.js

1. Clique em **Novo Item**.  Introduza um **nome do item**.
2. Escolha **projeto Freestyle**.  Clique em **OK**.
3. No **gestão da origem de código** separador, selecione **Git** e introduza os detalhes do repositório e ramo que contém o código de aplicação.    
    ![Adicione um repositório à sua compilação](media/tutorial-build-deploy-jenkins/jenkins-git.png)
4. No **criar Acionadores** separador, selecione **consulta SCM** e introduza a agenda `H/03 * * * *` para consultar o repositório de Git para alterações a cada três minutos. 
5. No **criar ambiente** separador, selecione **fornecer nó &amp; npm bin / caminho de pasta** e selecione o **NodeJS instalação** valor. Deixe **npmrc ficheiro** definido como "Utilizar predefinição do sistema."
6. No **criar** separador, escolha **executar shell** e introduza o comando `npm install` para garantir que todas as dependências são atualizadas.


## <a name="configure-jenkins-for-vsts-integration"></a>Configurar Jenkins para integração VSTS

  > [!NOTE]
  Certifique-se de que contém o token de acesso pessoal (TERESA) utiliza para os seguintes passos de **permissões de lançamento (ler, escrever, executar e gerir) no VSTS**.
 
1.  Crie uma TERESA na sua conta VSTS se ainda não tiver um. Jenkins requer estas informações para aceder à sua conta VSTS.  Certifique-se de **armazenar** as informações de tokens para os passos nesta secção.
  Leitura [como criar um token de acesso pessoal para VSTS e o TFS](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate) para saber como gerar um.
2. No **ações de pós-compilação de** separador, clique em **adicionar ação de pós-compilação de**. Escolha **arquivar os artefactos**.
3. Para **ficheiros para arquivar**, introduza `**/*` para incluir todos os ficheiros.
4. Para criar outra ação, clique em **adicionar ação de pós-compilação de**.
5. Escolha **acionar versão no TFS/Team Services**, introduza o uri para a sua conta VSTS, tais como: `https://{your-account-name}.visualstudio.com`.
6. Introduza o **projeto de equipa** nome.
7. Escolha um nome para o **definição da versão** (criar esta definição de lançamento mais tarde no VSTS).
8. Escolha as credenciais para ligar ao seu ambiente VSTS ou TFS.  Deixe o **Username** em branco se estiver a utilizar VSTS.
   Introduza um **nome de utilizador e palavra-passe** se estiver a utilizar uma versão no local do TFS.    
    ![Configurar as ações de pós-compilação de Jenkins](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. **Guardar** o projeto de jenkins.

## <a name="create-a-jenkins-service-endpoint"></a>Criar um ponto final de serviço de Jenkins

Um ponto final de serviço permite VSTS ligar ao Jenkins.

1. Abra o **serviços** página no VSTS, abra o **ponto final de serviço novo** lista e escolha **Jenkins**.
    ![Adicionar um ponto final Jenkins](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)
2. Introduza um nome para a ligação.
3. Introduza o URL do servidor de Jenkins e marcas de escala de **aceitar certificados SSL não fidedignos** opção.  Um exemplo é o URL:`http://{YourJenkinsURL}.westcentralus.cloudapp.azure.com`
4. Introduza o **nome de utilizador e palavra-passe** para a sua conta Jenkins.
5. Escolha **verificar ligação** para verificar que as informações estão corretas.
6. Escolha **OK** para criar o ponto final do serviço.

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>Criar um grupo de implementação de máquinas virtuais do Azure

É necessário um [grupo de implementação](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) para registar o agente VSTS, pelo que pode implementar a definição de versão para a máquina virtual.  Grupos de implementação tornam mais fácil definir grupos lógicos de máquinas de destino para a implementação e instale o agente necessárias em cada máquina.

   > [!NOTE]
   > Nos passos seguintes, certifique-se que instale os pré-requisitos e **não executar o script com privilégios sudo.**

1. Abra o **versões** separador do **criar &amp; versão** hub, em seguida, abra **grupos de implementação**e escolha **+ novo**.
2. Introduza um nome para o grupo de implementação e uma descrição opcional.
   Em seguida, escolha **criar**.
3. Escolha o sistema operativo para a máquina de virtual de destino da implementação.  Por exemplo, escolha **Ubuntu 16.04 +**.
4. Marcas de escala de **utilizam um token de acesso pessoal no script para a autenticação**.
5. Selecione o **pré-requisitos de sistema** ligação.  Instale as pré-requisitos para o sistema operativo.
6. Selecione o **copie o script para a área de transferência** para copiar o script.
7. **Inicie sessão no** à máquina virtual destino implementação e **executar** o script.  **Não** executar o script com privilégios sudo.
8. Após a instalação, é-lhe pedida etiquetas de grupo de implementação.  Aceite as predefinições.
9. Procurar no VSTS, a máquina virtual recentemente registada no **destinos** em **grupos de implementação**.

## <a name="create-a-vsts-release-definition"></a>Criar uma definição de versão VSTS

Uma definição de versão Especifica o processo VSTS executa para implementar a aplicação.  Neste exemplo, executar um script de shell.

Para criar a definição de versão no VSTS:

1. Abra o **versões** no **criar &amp; versão** hub e escolha **criar versão definição**. 
2. Selecione o **vazio** modelo escolhendo começar com uma **vazio processo**.
3. No **artefactos** secção, clique em **+ adicionar artefactos** e escolha **Jenkins** para **tipo de origem**. Selecione a ligação de ponto final de serviço Jenkins. Em seguida, selecione a tarefa de origem Jenkins e escolha **adicionar**.
4.  Clique no botão de reticências junto a **1 ambiente**.  Clique em **fase de grupo de implementação de adicionar**.
5.  Escolha o **grupo de implementação**.
5. Clique em  **+**  para adicionar uma tarefa para o **fase do grupo de implementação**.
6. Escolha o **Script de Shell** de tarefas e clique em **adicionar**.    
    O **Script de Shell** tarefa é utilizada para fornecer a configuração para um script seja executado em cada servidor para instalar o Node.js e iniciar a aplicação. Configure da seguinte forma:
8. **Caminho do script**:     
    `$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh`
9.  Clique em **avançadas**e, em seguida, ativar **especifique o diretório de trabalho**.
10.  **Diretório de trabalho**:`$(System.DefaultWorkingDirectory)/Fabrikam-Node`
11. Editar o nome da definição de versão para o nome especificado no **ações de pós-compilação de** separador de compilação no Jenkins. Jenkins requer este nome conseguir acionar uma nova versão, quando os artefactos de origem são atualizados.
12. Clique em **guardar**e clique em **OK** para guardar a definição de versão.

## <a name="execute-manual-and-ci-triggered-deployments"></a>Executar manual e implementações de CI acionada

1. Escolha **+ versão** e selecione **criar versão**.
2. Selecione a compilação concluiu a realçados na lista pendente e escolha **fila**.
3. Escolha a versão de ligação na mensagem de pop-up. Por exemplo: "versão **versão 1** foi criado."
4. Abra o **registos** separador para ver o resultado da consola de versão.
5. No seu browser, abra o URL de um dos servidores adicionados ao seu grupo de implementação. Por exemplo, introduza`http://{your-server-ip-address}`
6. Vá para o repositório de Git de origem e modificar o conteúdo do **h1** cabeçalho no app/views/index.jade ficheiro com algum texto alterado.
7. **Consolidar** a alteração.
8. Após alguns minutos, verá uma nova versão criada no **versões** página do VSTS ou TFS. Abra a versão para ver a implementação a decorrer. Parabéns!

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, automatizar a implementação de uma aplicação no Azure através da compilação de Jenkins e VSTS para versão. Aprendeu a:

> [!div class="checklist"]
> * Criar a sua aplicação no Jenkins
> * Configurar Jenkins para integração VSTS
> * Criar um grupo de implementação para as máquinas virtuais do Azure
> * Criar uma definição de versão que configura as VMs e implementa a aplicação

Avançadas para o próximo tutorial para saber mais sobre como implementar um LAMP pilha (Linux, Apache, MySQL e PHP).

> [!div class="nextstepaction"]
> [Implementar pilha LAMP](tutorial-lamp-stack.md)