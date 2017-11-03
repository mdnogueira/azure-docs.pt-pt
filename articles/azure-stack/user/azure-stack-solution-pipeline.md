---
title: "Implementar a aplicação no Azure e Azure pilha | Microsoft Docs"
description: "Saiba como implementar aplicações do Azure e pilha do Azure com um pipeline de CI/CD híbrida."
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: helaw
ms.custom: mvc
ms.openlocfilehash: 83bb401d5d65cd2c34015a1a14673363aeee81d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-apps-to-azure-and-azure-stack"></a>Implementar aplicações do Azure e Azure pilha
*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Uma versão híbrida [integração contínua](https://www.visualstudio.com/learn/what-is-continuous-integration/)/[entrega contínua](https://www.visualstudio.com/learn/what-is-continuous-delivery/)pipeline (CI/CD) permite-lhe criar, testar e implementar a sua aplicação para várias nuvens.  Neste tutorial, pode criar um ambiente de exemplo para saber como um pipeline de CI/CD híbrida pode ajudá-lo:
 
> [!div class="checklist"]
> * Inicie uma nova compilação com base no código consolidações para o seu repositório de serviços de equipa do Visual Studio (VSTS).
> * Implemente automaticamente o código incorporado recentemente para o Azure para testes de aceitação de utilizadores.
> * Assim que o seu código passou a testar, implemente automaticamente a pilha do Azure. 


## <a name="prerequisites"></a>Pré-requisitos
Alguns componentes são necessários para criar um pipeline de CI/CD híbrida e podem demorar algum tempo para preparar.  Se já tiver alguns destes componentes, certifique-se de que cumprem os requisitos antes de começar.

Este tópico também parte do princípio que tiver algum conhecimento do Azure e a pilha do Azure. Se pretender obter mais informações antes de continuar, é necessário começar com estes tópicos:

- [Introdução ao Azure](https://docs.microsoft.com/azure/fundamentals-introduction-to-azure)
- [Conceitos chave de pilha do Azure](../azure-stack-key-features.md)

### <a name="azure"></a>Azure
 - Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
 - Criar um [aplicação Web](../../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md)e configurá-la para [a publicação FTP](../../app-service/app-service-deploy-ftp.md).  Anote o URL da aplicação Web novas, porque é utilizado mais tarde.


### <a name="azure-stack"></a>Azure Stack
 - [Implementar a pilha do Azure](../azure-stack-run-powershell-script.md).  A instalação normalmente demora algumas horas a concluir, por isso planeie em conformidade.
 - Implementar [do serviço de aplicações](../azure-stack-app-service-deploy.md) PaaS serviços à pilha do Azure.
 - Criar uma aplicação Web e configurá-la para [a publicação FTP](../azure-stack-app-service-enable-ftp.md).  Anote o URL da aplicação Web novas, porque é utilizado mais tarde.  

### <a name="developer-tools"></a>Ferramentas de programador
 - Criar um [área de trabalho VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services).  O processo de inscrição cria um projeto com o nome "MyFirstProject."  
 - [Instalar Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) e [início de sessão para VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services#connect-and-share-code-from-visual-studio)
 - Ligar para o projeto e [clonar localmente](https://www.visualstudio.com/docs/git/gitquickstart).
 - Criar um [agrupamento de agentes](https://www.visualstudio.com/docs/build/concepts/agents/pools-queues#creating-agent-pools-and-queues) no VSTS.
 - Instalar o Visual Studio e implementar uma [VSTS Criar agente](https://www.visualstudio.com/docs/build/actions/agents/v2-windows) a uma máquina virtual na pilha do Azure. 
 

## <a name="create-app--push-to-vsts"></a>Criar aplicação & push VSTS

### <a name="create-application"></a>Criar aplicação
Nesta secção, pode criar uma aplicação ASP.NET simples e enviá-lo para VSTS.  Estes passos representam o fluxo de trabalho de programador normal e podem ser adaptados para idiomas e ferramentas de programador. 

1.  Abra o Visual Studio.
2.  Do espaço de Explorador de equipa e **soluções...**  área, clique em **novo**.
3.  Selecione **Visual c#** > **Web** > **aplicação ASP.NET Web (.NET Framework)**.
4.  Forneça um nome para a aplicação e clique em **OK**.
5.  No ecrã seguinte, mantenha as predefinições (formulários Web) e clique em **OK**.

### <a name="commit-and-push-changes-to-vsts"></a>Consolide e emita as alterações para VSTS
1.  Utilizar o Explorador de equipa no Visual Studio, selecione a menu pendente e clique em **alterações**.
2.  Forneça uma mensagem de consolidação e selecione **consolidar todas**. Poderá ser-lhe pedido para guardar o ficheiro de solução, clique em Sim para guardar todas as.
3.  Depois de consolidado, o Visual Studio oferece sincronizar as alterações ao seu projeto. Selecione **sincronização**.

    ![imagem que mostra o ecrã de confirmação depois de concluída a consolidação](./media/azure-stack-solution-pipeline/image1.png)

4.  No separador Sincronização, em *enviar*, consulte a consolidação de novo.  Selecione **Push** para sincronizar a alteração VSTS.

    ![passos de sincronização de apresentação de imagem](./media/azure-stack-solution-pipeline/image2.png)

### <a name="review-code-in-vsts"></a>Rever o código no VSTS
Uma vez que já consolidada uma alteração e instalada no VSTS, verifique o seu código a partir do portal do VSTS.  Selecione **código**e, em seguida, **ficheiros** no menu de lista pendente.  Pode ver a solução que criou.

## <a name="create-build-definition"></a>Criar definição de compilação
O processo de compilação define como a aplicação está criada e em pacote para implementação em cada consolidação de alterações de código. No nosso exemplo, utilizamos o modelo incluído para configurar o processo de compilação para uma aplicação ASP.NET, apesar desta configuração pode ser adaptada dependendo da sua aplicação.

1.  Inicie sessão na sua área de trabalho VSTS num browser da web.
2.  A partir da faixa, selecione **compilar & versão** e, em seguida, **compilações**.
3.  Clique em **+ nova definição**.
4.  Na lista de modelos, selecione **ASP.NET (pré-visualização)** e selecione **aplicar**.
5.  Modificar o *argumentos de MSBuild* campo no *compilar solução* passo para:

    `/p:DeployOnBuild=True /p:WebPublishMethod=FileSystem /p:DeployDefaultTarget=WebPublish /p:publishUrl="$(build.artifactstagingdirectory)\\"`

6.  Selecione o **opções** separador e seleccione a fila de agente para o agente de compilação que implementou a uma máquina virtual na pilha do Azure. 
7.  Selecione o **Acionadores** separador e ativar **integração contínua**.
7.  Clique em **Guardar & fila** e, em seguida, selecione **guardar** na lista pendente. 

## <a name="create-release-definition"></a>Criar a definição da versão
O processo de lançamento define como as compilações do passo anterior são implementadas para um ambiente.  Neste tutorial, vamos publicar nossa aplicação ASP.NET com FTP para uma aplicação Web do Azure. Para configurar uma versão para o Azure, utilize os seguintes passos:

1.  A partir da faixa VSTS, selecione **compilar & versão** e, em seguida, **versões**.
2.  Clique na verde **+ nova definição**.
3.  Selecione **vazio** e clique em **seguinte**.
4.  Marque a caixa de *a implementação contínua*e, em seguida, clique em **criar**.

Agora que já criou uma definição de versão vazio e vinculada-lo para a compilação, iremos adicionar os passos para o ambiente do Azure:

1.  Clique na verde  **+**  para adicionar tarefas.
2.  Selecione **todos os**e, em seguida, na lista, adicionar **FTP carregar** e selecione **fechar**.
3.  Selecione o **FTP carregar** de tarefas que acabou de adicionar e configurar os seguintes parâmetros:
    
    | Parâmetro | Valor |
    | ----- | ----- |
    |Método de Autenticação| Introduza as credenciais|
    |URL do servidor | Obter o URL de FTP de aplicação Web do portal do Azure |
    |Nome de utilizador | Nome de utilizador que configurou ao criar as credenciais de FTP para a aplicação Web |
    |Palavra-passe | Palavra-passe que criou ao estabelecer as credenciais de FTP para a aplicação Web|
    |Diretório de origem | $(System.DefaultWorkingDirectory)\**\ |
    |Diretório remoto | /site/wwwroot / |
    |Preservar caminhos de ficheiro | Ativada (marcada)|

4.  Clique em **guardar**

Por fim, configure a definição de versão a utilizar o agrupamento de agentes que contém o agente implementado utilizando os seguintes passos:
1.  Selecione a definição de versão e clique em **editar**.
2.  Selecione **executar no agente** da coluna central.  Na coluna da direita, selecione a fila de agente que contém o agente de compilação em execução na pilha do Azure.  
    ![configuração de apresentação de imagem da definição de versão a utilizar fila específica](./media/azure-stack-solution-pipeline/image3.png)


## <a name="deploy-your-app-to-azure"></a>Implementar a aplicação no Azure
Este passo utiliza o pipeline de CI/CD recentemente integrado para implementar a aplicação ASP.NET numa aplicação Web no Azure. 

1.  A partir da faixa de VSTS, selecione **compilar & versão**e, em seguida, selecione **compilações**.
2.  Clique em **...**  na definição da compilação criada anteriormente e selecione **fila nova compilação**.
3.  Aceite as predefinições e clique em **Ok**.  A compilação começa e apresenta o progresso.
4.  Depois de concluída a compilação, pode controlar o estado selecionando **compilar & versão** e selecionando **versões**.
5.  Depois de concluída a compilação, visite o site utilizando o URL indicado ao criar a aplicação Web.    


## <a name="add-azure-stack-to-pipeline"></a>Adicionar a pilha do Azure para o pipeline
Agora que tenha testado o pipeline de CI/CD com a implementação para o Azure, está na altura de adicionar a pilha do Azure para o pipeline.  Nos passos seguintes, pode criar um novo ambiente e adicionar uma tarefa de carregar de FTP para implementar a aplicação no Azure pilha.  Como adicionar um aprovador de versão, o que funciona como uma forma para simular "off" assinatura numa versão de código à pilha do Azure.  

1.  Na definição da versão, selecione **+ adicionar ambiente** e **criar novo ambiente**.
2.  Selecione **vazio**, clique em **seguinte**.
3.  Selecione **utilizadores específicos** e especificar a sua conta.  Selecione **Criar**.
4.  Mudar o nome do ambiente, selecionar o nome existente e digitar *Azure pilha*.
5.  Agora, seleção de ambiente de pilha do Azure, em seguida, selecione **adicionar tarefas**.
6.  Selecione o **FTP carregar** de tarefas e selecione **adicionar**, em seguida, selecione **fechar**.


### <a name="configure-ftp-task"></a>Configurar tarefas FTP
Agora que criou uma versão, irá configurar os passos necessários para publicação para a aplicação Web na pilha do Azure.  Tal como tiver configurado a tarefa de FTP carregar para o Azure, configurar a tarefa de pilha do Azure:

1.  Selecione o **FTP carregar** de tarefas que acabou de adicionar e configurar os seguintes parâmetros:
    
    | Parâmetro | Valor |
    | -----     | ----- |
    |Método de Autenticação| Introduza as credenciais|
    |URL do servidor | URL de FTP de aplicação Web obtidos a partir do portal de pilha do Azure |
    |Nome de utilizador | Nome de utilizador que configurou ao criar as credenciais de FTP para a aplicação Web |
    |Palavra-passe | Palavra-passe que criou ao estabelecer as credenciais de FTP para a aplicação Web|
    |Diretório de origem | $(System.DefaultWorkingDirectory)\**\ |
    |Diretório remoto | /site/wwwroot /|
    |Preservar caminhos de ficheiro | Ativada (marcada)|

2.  Clique em **guardar**

Por fim, configure a definição de versão a utilizar o agrupamento de agentes que contém o agente implementado utilizando os seguintes passos:
1.  Selecione a definição de versão e clique em **editar**
2.  Selecione **executar no agente** da coluna central. Na coluna da direita, selecione a fila de agente que contém o agente de compilação em execução na pilha do Azure.  
    ![configuração de apresentação de imagem da definição de versão a utilizar fila específica](./media/azure-stack-solution-pipeline/image3.png)

## <a name="deploy-new-code"></a>Implementar o novo código
Pode agora testar o pipeline de CI/CD híbrida, com o passo final, publicação de pilha do Azure.  Nesta secção, modifique o rodapé do site e iniciar a implementação através do pipeline.  Depois de concluído, verá as suas alterações implementadas no Azure para revisão e, em seguida, depois de aprovar o lançamento, são publicadas com pilha do Azure.

1. No Visual Studio, abra o *site.master* de ficheiros e alterar esta linha:
    
    `
        <p>&copy; <%: DateTime.Now.Year %> - My ASP.NET Application</p>
    `

    para este:

    `
        <p>&copy; <%: DateTime.Now.Year %> - My ASP.NET Application delivered by VSTS, Azure, and Azure Stack</p>
    `
3.  Confirmar as alterações e sincronizar a VSTS.  
4.  Área de trabalho VSTS, verifique o estado de compilação selecionando **compilar & versão** > **criar**
5.  Verá uma compilação em curso.  Faça duplo clique o estado e pode ver o progresso de compilação.  Depois de ver "Compilação concluída" na consola, avançar para verificar a versão do **compilar & versão** > **versão**.  Faça duplo clique a versão.
6.  Irá receber a notificação que uma versão requer revisão. Verifique o URL da aplicação Web e verifique se existem novas alterações.  Aprove a versão no VSTS.
    ![configuração de apresentação de imagem da definição de versão a utilizar fila específica](./media/azure-stack-solution-pipeline/image4.png)
    
7.  Certifique-se de que a publicação de pilha do Azure está concluída, visitando o Web site utilizando o URL indicado ao criar a aplicação Web.
    ![imagem que mostra o ASP. Aplicação nEt com rodapé alterado](./media/azure-stack-solution-pipeline/image5.png)


Agora pode utilizar o novo pipeline de CI/CD híbrida como um bloco modular para outros padrões de nuvem híbrida.

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a criar uma versão híbrida CI/CD pipeline que:

> [!div class="checklist"]
> * Inicia uma nova compilação com base no código consolida para o seu repositório de serviços de equipa do Visual Studio (VSTS).
> * Implementa automaticamente o seu código recentemente criado para o Azure para testes de aceitação de utilizadores.
> * Assim que o seu código passou a testar, implementa automaticamente a pilha do Azure. 

Agora que tem um pipeline de CI/CD híbrida, continue a aprender a desenvolver aplicações para a pilha do Azure.

> [!div class="nextstepaction"]
> [Desenvolver para o Azure Stack](azure-stack-developer.md)


