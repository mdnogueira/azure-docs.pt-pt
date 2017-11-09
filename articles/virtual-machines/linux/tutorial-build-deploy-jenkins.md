---
title: "CI/CD de Jenkins para as VMs do Azure com os serviços da equipa | Microsoft Docs"
description: "Configurar a integração contínua (CI) e a implementação contínua (CD) de uma aplicação Node.js utilizando Jenkins para VMs do Azure da gestão de versão no Visual Studio Team Services ou o Microsoft Team Foundation Server"
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
ms.openlocfilehash: c96aafeb05293ccdc4c30c2b828cead1dfdb157c
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="deploy-your-app-to-linux-vms-by-using-jenkins-and-team-services"></a>Implementar a sua aplicação para VMs com Linux utilizando Jenkins e serviços da equipa

A integração contínua (CI) e a implementação contínua (CD) formam um pipeline através do qual pode criar, versão e implementar o seu código. Visual Studio Team Services fornece um conjunto de terminar, excelentes das ferramentas de automatização de CI/CD para implementação no Azure. Jenkins é uma popular terceiros CI/CD baseadas em servidor ferramenta que também fornece a automatização de CI/CD. Pode utilizar os serviços de equipa e Jenkins em conjunto para personalizar como fornecer o serviço ou aplicação na nuvem.

Neste tutorial, utilize Jenkins para criar uma aplicação web Node.js. Em seguida, utiliza Team Services ou o Team Foundation Server implementá-la para um [grupo de implementação](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) que contém máquinas virtuais do Linux (VMs).

Irá:

> [!div class="checklist"]
> * Obter a aplicação de exemplo.
> * Configure Jenkins plug-ins.
> * Configure um projeto de Jenkins Freestyle para Node.js.
> * Configure Jenkins para integração com o Team Services.
> * Crie um ponto final de serviço de Jenkins.
> * Crie um grupo de implementação para as máquinas virtuais do Azure.
> * Crie uma definição de versão Team Services.
> * Execute implementações manuais e acionada de CI.

## <a name="before-you-begin"></a>Antes de começar

* Precisa de acesso a um servidor de Jenkins. Se ainda não criou um servidor de Jenkins, consulte [criar um principal de Jenkins numa máquina virtual do Azure](https://docs.microsoft.com/en-us/azure/jenkins/install-jenkins-solution-template). 

* Inicie sessão na sua conta Team Services (**https://{youraccount}.visualstudio.com**). 
  Pode obter um [Team Services conta gratuita](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Para obter mais informações, consulte [ligar aos serviços da equipa](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).

*  Precisa de uma máquina virtual Linux para um destino de implementação.  Para obter mais informações, consulte [criar e gerir VMs com Linux com a CLI do Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/tutorial-manage-vm).

*  Abra a porta de entrada 80 para a máquina virtual. Para obter mais informações, consulte [criar grupos de segurança de rede através do portal do Azure](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

## <a name="get-the-sample-app"></a>Obter a aplicação de exemplo

Precisa de uma aplicação para implementar, armazenados num repositório de Git.
Para este tutorial, recomendamos que utilize [esta aplicação de exemplo disponível a partir do GitHub](https://github.com/azooinmyluggage/fabrikam-node). Este tutorial contém um script de exemplo que é utilizado para instalar o Node.js e uma aplicação. Se pretende trabalhar com o seus próprios repositório, deve configurar uma amostra semelhante.

Criar uma bifurcação desta aplicação e tome nota da localização (URL) para utilização em passos posteriores deste tutorial. Para obter mais informações, consulte [Bifurcar um repositório](https://help.github.com/articles/fork-a-repo/).    

> [!NOTE]
> A aplicação foi criada através da [Yeoman](http://yeoman.io/learning/index.html). Utiliza rápida, bower e grunt. E tem alguns pacotes de npm como dependências.
> O exemplo contém também um script que configura Nginx e implementa a aplicação. Este é executado nas máquinas virtuais. Especificamente, o script:
> 1. Instala o nó, Nginx e PM2.
> 2. Configura Nginx e PM2.
> 3. Inicia a aplicação de nó.

## <a name="configure-jenkins-plug-ins"></a>Configurar Jenkins plug-ins

Em primeiro lugar, tem de configurar dois plug-ins Jenkins: **NodeJS** e **VS serviços a equipa de implementação contínua**.

1. Abra a sua conta Jenkins e selecione **gerir Jenkins**.
2. No **gerir Jenkins** página, selecione **gerir plug-ins**.
3. Filtrar a lista para localizar o **NodeJS** Plug-in e selecione o **instalar sem reinício** opção.
    ![Adicionar o plug-in do NodeJS para Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)
4. Filtrar a lista para localizar o **VS serviços a equipa de implementação contínua** Plug-in e selecione o **instalar sem reinício** opção.
5. Vá para o dashboard de Jenkins e selecione **gerir Jenkins**.
6. Selecione **Global ferramenta configuração**. Localizar **NodeJS** e selecione **NodeJS instalações**.
7. Selecione o **instalar automaticamente** opção e, em seguida, introduza um **nome** valor.
8. Selecione **Guardar**.

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>Configurar um projeto de Jenkins Freestyle para Node.js

1. Selecione **Novo Item**. Introduza um nome de item.
2. Selecione **projeto Freestyle**. Selecione **OK**.
3. No **gestão da origem de código** separador, selecione **Git** e introduza os detalhes do repositório e ramo que contém o código de aplicação.    
    ![Adicione um repositório à sua compilação](media/tutorial-build-deploy-jenkins/jenkins-git.png)
4. No **criar Acionadores** separador, selecione **consulta SCM** e introduza a agenda `H/03 * * * *` para consultar o repositório de Git para alterações a cada três minutos. 
5. No **criar ambiente** separador, selecione **fornecer nó &amp; npm bin / caminho de pasta** e selecione o **NodeJS instalação** valor. Deixe **npmrc ficheiro** definido como **utilizar predefinido do sistema**.
6. No **criar** separador, selecione **executar shell** e introduza o comando `npm install` para se certificar de que todas as dependências são atualizadas.


## <a name="configure-jenkins-for-team-services-integration"></a>Configurar Jenkins para integração dos serviços de equipa

> [!NOTE]
> Certifique-se de que contém o token de acesso pessoal (TERESA) utiliza para os seguintes passos de *versão* (ler, escrever, executar e gerir) permissão nos serviços de equipa.
 
1.  Crie uma TERESA na sua conta do Team Services, se ainda não tiver um. Jenkins requer estas informações para aceder à sua conta de serviços da equipa. Lembre-se de que pretende armazenar as informações de tokens para os passos nesta secção.
  
    Para saber como gerar um token, leia [como criar um token de acesso pessoal para VSTS e o TFS?](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate).
2. No **ações de pós-compilação de** separador, selecione **adicionar ação de pós-compilação de**. Selecione **arquivar os artefactos**.
3. Para **ficheiros para arquivar**, introduza `**/*` para incluir todos os ficheiros.
4. Para criar outra ação, selecione **adicionar ação de pós-compilação de**.
5. Selecione **acionar versão no TFS/Team Services**. Introduza o URI para a sua conta do Team Services, tal como **https://{your-account-name}.visualstudio.com**.
6. Introduza o **projeto de equipa** nome.
7. Escolha um nome para a definição de versão. (Pode cria esta definição de lançamento mais tarde Team Services.)
8. Escolha as credenciais para ligar ao seu ambiente Team Services ou o Team Foundation Server:
   - Deixe **Username** em branco se estiver a utilizar Team Services. 
   - Introduza um nome de utilizador e palavra-passe, se estiver a utilizar uma versão no local do Team Foundation Server.    
   ![Configurar as ações de pós-compilação de Jenkins](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. Guarde o projeto de Jenkins.


## <a name="create-a-jenkins-service-endpoint"></a>Criar um ponto final de serviço de Jenkins

Um ponto final de serviço permite que os serviços de equipa ligar ao Jenkins.

1. Abra o **serviços** página nos serviços de equipa, abra o **ponto final de serviço novo** lista e selecione **Jenkins**.
   ![Adicionar um ponto final Jenkins](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)
2. Introduza um nome para a ligação.
3. Introduza o URL do seu servidor Jenkins e selecione o **aceitar certificados SSL não fidedignos** opção. Um exemplo de URL é **http://{YourJenkinsURL}.westcentralus.cloudapp.azure.com**.
4. Introduza o nome de utilizador e palavra-passe para a sua conta Jenkins.
5. Selecione **verificar ligação** para verificar que as informações estão corretas.
6. Selecione **OK** para criar o ponto final do serviço.

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>Criar um grupo de implementação de máquinas virtuais do Azure

É necessário um [grupo de implementação](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) para registar o agente dos serviços de equipa, para a definição de versão possa ser implementada para a máquina virtual. Os grupos de implementação tornam mais fácil para definir grupos lógicos de máquinas de destino para a implementação e para instalar o agente necessárias em cada máquina.

   > [!NOTE]
   > No procedimento seguinte, é necessário instalar os pré-requisitos e *não executar o script com privilégios sudo.*

1. Abra o **versões** separador do **criar &amp; versão** hub, abra **grupos de implementação**e selecione **+ novo**.
2. Introduza um nome para o grupo de implementação e uma descrição opcional. Em seguida, selecione **criar**.
3. Escolha o sistema operativo para a máquina de virtual de destino da implementação. Por exemplo, seleccione **Ubuntu 16.04 +**.
4. Selecione **utilizam um token de acesso pessoal no script para a autenticação**.
5. Selecione o **pré-requisitos de sistema** ligação. Instale as pré-requisitos para o sistema operativo.
6. Selecione **copie o script para a área de transferência** para copiar o script.
7. Inicie sessão na máquina virtual de destino da implementação e execute o script. Não execute o script com privilégios sudo.
8. Após a instalação, é-lhe pedida etiquetas de grupo de implementação. Aceite as predefinições.
9. Procurar nos serviços de equipa, a máquina virtual recentemente registada no **destinos** em **grupos de implementação**.

## <a name="create-a-team-services-release-definition"></a>Criar uma definição de versão Team Services

Uma definição de versão Especifica o processo que Team Services utiliza para implementar a aplicação. Neste exemplo, executar um script de shell.

Para criar a definição de versão no Team Services:

1. Abra o **versões** separador do **criar &amp; versão** hub e selecione **criar versão definição**. 
2. Selecione o **vazio** modelo escolhendo começar com uma **vazio processo**.
3. No **artefactos** secção, selecione **+ adicionar artefactos** e escolha **Jenkins** para **tipo de origem**. Selecione a ligação de ponto final de serviço Jenkins. Em seguida, selecione a tarefa de origem Jenkins e selecione **adicionar**.
4. Selecione o botão de reticências junto a **1 ambiente**. Selecione **fase de grupo de implementação de adicionar**.
5. Escolha o grupo de implementação.
5. Selecione  **+**  para adicionar uma tarefa para **fase do grupo de implementação**.
6. Selecione o **Script de Shell** de tarefas e selecione **adicionar**. O **Script de Shell** tarefa fornece a configuração para um script seja executado em cada servidor para instalar o Node.js e iniciar a aplicação.
8. Para **caminho do Script**, introduza **$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh**.
9. Selecione **avançadas**e, em seguida, ativar **especifique o diretório de trabalho**.
10. Para **diretório de trabalho**, introduza **$(System.DefaultWorkingDirectory) / Fabrikam nó**.
11. Editar o nome da definição de versão para o nome que especificou no **ações de pós-compilação de** separador de compilação no Jenkins. Jenkins requer este nome conseguir acionar uma nova versão, quando os artefactos de origem são atualizados.
12. Selecione **guardar** e selecione **OK** para guardar a definição de versão.

## <a name="execute-manual-and-ci-triggered-deployments"></a>Executar implementações manuais e acionada CI

1. Selecione **+ versão** e selecione **criar versão**.
2. Selecione a compilação que concluiu a realçados na lista pendente e selecione **fila**.
3. Escolha a versão de ligação na mensagem de pop-up. Por exemplo: "versão **versão 1** foi criado."
4. Abra o **registos** separador para ver o resultado da consola de versão.
5. No seu browser, abra o URL de um dos servidores que adicionou ao seu grupo de implementação. Por exemplo, introduza **http://{your-server-ip-address}**.
6. Vá para o repositório de Git de origem e modificar o conteúdo do **h1** cabeçalho no app/views/index.jade ficheiro com algum texto alterado.
7. Confirme a alteração.
8. Após alguns minutos, verá uma nova versão criada no **versões** página dos serviços de equipa ou Team Foundation Server. Abra a versão para ver a implementação a decorrer. Parabéns!

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, automatizar a implementação de uma aplicação no Azure utilizando Jenkins compilação equipa dos serviços de e para a versão. Aprendeu a:

> [!div class="checklist"]
> * Crie a sua aplicação no Jenkins.
> * Configure Jenkins para integração com o Team Services.
> * Crie um grupo de implementação para as máquinas virtuais do Azure.
> * Crie uma definição de versão que configura as VMs e implementa a aplicação.

Para saber mais sobre como implementar um LAMP (Linux, Apache, MySQL e PHP) da pilha, avançar para o próximo tutorial.

> [!div class="nextstepaction"]
> [Implementar pilha LAMP](tutorial-lamp-stack.md)
