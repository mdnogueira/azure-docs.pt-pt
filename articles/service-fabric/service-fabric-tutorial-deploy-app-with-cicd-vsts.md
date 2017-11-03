---
title: "Implementar uma aplicação de Service Fabric do Azure com a integração contínua (Team Services) | Microsoft Docs"
description: "Saiba como configurar a integração contínua e implementação para uma aplicação de Service Fabric com o Visual Studio Team Services.  Implemente uma aplicação para um cluster do Service Fabric no Azure."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: d0f67b1a63c36e878ed5f7c9aa1c45267e5b156c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>Implementar uma aplicação com CI/CD para um cluster do Service Fabric
Este tutorial faz parte três de uma série e descreve como configurar a integração contínua e implementação para uma aplicação de Service Fabric do Azure utilizando o Visual Studio Team Services.  É necessária uma aplicação de Service Fabric existente, a aplicação criada na [compilar uma aplicação .NET](service-fabric-tutorial-create-dotnet-app.md) é utilizada como exemplo.

Na parte três da série, saiba como:

> [!div class="checklist"]
> * Adicionar controlo de origem ao seu projeto
> * Criar uma definição de compilação nos serviços de equipa
> * Criar uma definição de versão no Team Services
> * Implementar e atualizar uma aplicação automaticamente

Este tutorial série, a saber como:
> [!div class="checklist"]
> * [Criar uma aplicação .NET Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * [Implementar a aplicação para um cluster remoto](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * Configurar CI/CD utilizando o Visual Studio Team Services
> * [Configurar a monitorização e diagnóstico para a aplicação](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial:
- Se não tiver uma subscrição do Azure, crie um [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Instalar Visual Studio 2017](https://www.visualstudio.com/) e instalar o **programação do Azure** e **desenvolvimento ASP.NET e web** cargas de trabalho.
- [Instalar o SDK do Service Fabric](service-fabric-get-started.md)
- Criar uma aplicação de Service Fabric, por exemplo, [seguir este tutorial](service-fabric-tutorial-create-dotnet-app.md). 
- Criar um cluster do Windows Service Fabric no Azure, por exemplo, [seguir este tutorial](service-fabric-tutorial-create-cluster-azure-ps.md)
- Criar um [conta Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services).

## <a name="download-the-voting-sample-application"></a>Transferir a aplicação de exemplo de voto
Se não foi possível criar a aplicação de exemplo de voto [parte de um desta série tutorial](service-fabric-tutorial-create-dotnet-app.md), poderá transferi-lo. Na janela da linha de comandos, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o computador local.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="prepare-a-publish-profile"></a>Preparar um perfil de publicação
Agora que já [criado uma aplicação](service-fabric-tutorial-create-dotnet-app.md) e ter [implementado a aplicação no Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md), está pronto para configurar a integração contínua.  Em primeiro lugar, prepare um perfil de publicação na sua aplicação para utilização pelo processo de implementação que é executada num Team Services.  O perfil de publicação deve ser configurado para segmentar o cluster que criou anteriormente.  Inicie o Visual Studio e abrir um projeto de aplicação de Service Fabric existente.  No **Explorador de soluções**, a aplicação com o botão direito e selecione **publicar...** .

Escolha um perfil de destino no seu projeto de aplicação para utilizar para o fluxo de trabalho de integração contínua, por exemplo na nuvem.  Especifique o ponto final de ligação de cluster.  Verifique o **atualizar a aplicação** caixa de verificação para que a aplicação atualiza para cada implementação nos serviços de equipa.  Clique em de **guardar** hiperligação para guardar as definições para o perfil de publicação e, em seguida, clique em **Cancelar** para fechar a caixa de diálogo.  

![Perfil de push][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-team-services-git-repo"></a>Partilhar a sua solução Visual Studio para um novo repositório de Git de serviços da equipa
Partilhe os ficheiros de origem da aplicação para um projeto de equipa no Team Services, pelo que pode gerar compilações.  

Criar um novo repositório de Git local para o seu projeto, selecionando **adicionar ao controlo de origem** -> **Git** na barra de estado no canto inferior direito do Visual Studio. 

No **Push** ver no **equipa Explorer**, selecione o **publicar repositório de Git** botão em **Push para o Visual Studio Team Services**.

![Repositório de Git de push][push-git-repo]

Verifique o seu e-mail e selecione a sua conta na **equipa dos serviços de domínio** pendente. Introduza o nome do seu repositório e selecione **publicar repositório**.

![Repositório de Git de push][publish-code]

O repositório de publicação cria um novo projeto de equipa na sua conta com o mesmo nome que o repositório local. Para criar o repositório num projeto de equipa existente, clique em **avançadas** junto a **repositório** nome e selecione um projeto de equipa. Pode ver o código na web, selecionando **vê-la na web**.

## <a name="configure-continuous-delivery-with-vsts"></a>Configurar a entrega contínua com VSTS
Uma definição de compilação Team Services descreve um fluxo de trabalho é composto por um conjunto de passos de compilação que são executados sequencialmente. Crie uma definição de compilação que produz um pacote de aplicação de Service Fabric e outros objetos, para implementar um cluster do Service Fabric. Saiba mais sobre [Team Services criar definições](https://www.visualstudio.com/docs/build/define/create). 

Uma definição de versão Team Services descreve um fluxo de trabalho que implementa um pacote de aplicação para um cluster. Quando utilizado em conjunto, a definição de compilação e a definição de versão executar o fluxo de trabalho completo iniciar com os ficheiros de origem que termina com uma aplicação em execução no seu cluster. Saiba mais sobre os serviços da equipa [versão definições](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition).

### <a name="create-a-build-definition"></a>Criar uma definição de compilação
Abra um browser e navegue para o novo projeto de equipa em: https://myaccount.visualstudio.com/Voting/Voting%20Team/_git/Voting. 

Selecione o **compilar & versão** separador, em seguida, **baseia-se**, em seguida, **+ nova definição**.  No **selecionar um modelo**, selecione o **aplicação do Azure Service Fabric** modelo e clique em **aplicar**. 

![Escolha o modelo de compilação][select-build-template] 

A aplicação de voto contém um projeto de .NET Core, por isso, adicione uma tarefa que restaura as dependências. No **tarefas** visualizar, selecione **+ adicionar tarefa** na parte inferior esquerda. Procura na "Linha de comandos" para localizar a tarefa da linha de comandos, em seguida, clique em **adicionar**. 

![Adicionar tarefa][add-task] 

A tarefa de novo, introduza "Executar dotnet.exe" **nome a apresentar**, "dotnet.exe" no **ferramenta**e "restaurar" na **argumentos**. 

![Nova tarefa][new-task] 

No **Acionadores** ver, clique em de **ativar este acionador** comutador em **integração contínua**. 

Selecione **Guardar & fila** e introduza "VS2017 alojado" como o **fila agente**. Selecione **fila** para iniciar manualmente a compilação.  Baseia-se também acionadores na emissão ou entrada.

Para verificar o progresso de compilação, mude para o **compilações** separador.  Depois de verificar que a compilação executa com êxito, defina uma definição de versão que implementa a aplicação para um cluster. 

### <a name="create-a-release-definition"></a>Criar uma definição de versão  

Selecione o **compilar & versão** separador, em seguida, **versões**, em seguida, **+ nova definição**.  No **criar versão definição**, selecione o **implementação de recursos de infraestrutura do serviço de Azure** modelo da lista e clique em **seguinte**.  Selecione o **criar** origem, verifique o **a implementação contínua** caixa e clique em **criar**. 

No **ambientes** ver, clique em **adicionar** à direita do **ligação de Cluster**.  Especifique um nome de ligação de "mysftestcluster", um ponto final de cluster do "tcp://mysftestcluster.westus.cloudapp.azure.com:19000" e o Azure Active Directory ou credenciais de certificado para o cluster. As credenciais do Azure Active Directory, defina as credenciais que pretende utilizar para ligar ao cluster o **Username** e **palavra-passe** campos. Para autenticação baseada em certificado, defina a codificação Base64 do ficheiro de certificado de cliente no **certificado de cliente** campo.  Consulte a ajuda de pop-up sobre esse campo para informações sobre como obter esse valor.  Se o certificado é protegido por palavra-passe, defina a palavra-passe a **palavra-passe** campo.  Clique em **guardar** para guardar a definição de versão.

![Adicionar ligação de cluster][add-cluster-connection] 

Clique em **executar no agente**, em seguida, selecione **alojado VS2017** para **fila implementação**. Clique em **guardar** para guardar a definição de versão.

![Executar no agente][run-on-agent]

Selecione **+ versão** -> **criar versão** -> **criar** para criar manualmente uma versão.  Certifique-se de que a implementação concluída com êxito e a aplicação está em execução no cluster.  Abra um browser e navegue para [http://mysftestcluster.westus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.westus.cloudapp.azure.com:19080/Explorer/).  Tenha em atenção a versão da aplicação, neste exemplo é "1.0.0.20170616.3". 

## <a name="commit-and-push-changes-trigger-a-release"></a>Consolide e emita consolidações de alterações, acionar uma versão
Para verificar que o pipeline de integração contínua está a funcionar, verificando em algumas alterações de código a equipa de serviços.    

À medida que escreve o seu código, as alterações são automaticamente registadas pelo Visual Studio. Confirmar as alterações para o repositório de Git local selecionando o (de ícone de alterações pendentes![Pendente][pending]) na barra de estado na parte inferior direita.

No **alterações** ver no Explorador de equipa, adicione uma mensagem que descrevem a atualização e consolidar as alterações.

![Consolidar todas][changes]

Selecione o ícone de barra de estado das alterações não publicadas (![anulou a publicação de alterações][unpublished-changes]) ou a vista de sincronização no Explorador de equipa. Selecione **Push** para atualizar o seu código na equipa de serviços/do TFS.

![Envie as alterações][push]

Enviar automaticamente as alterações aos serviços da equipa aciona uma compilação.  Quando a definição de compilação for concluída com êxito, uma versão é criada automaticamente e inicia a atualizar a aplicação no cluster.

Para verificar o progresso de compilação, mude para o **compilações** separador **equipa Explorer** no Visual Studio.  Depois de verificar que a compilação executa com êxito, defina uma definição de versão que implementa a aplicação para um cluster.

Certifique-se de que a implementação concluída com êxito e a aplicação está em execução no cluster.  Abra um browser e navegue para [http://mysftestcluster.westus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.westus.cloudapp.azure.com:19080/Explorer/).  Tenha em atenção a versão da aplicação, neste exemplo é "1.0.0.20170815.3".

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>Atualizar a aplicação
Efetue alterações de código na aplicação.  Guarde e consolidar as alterações, seguindo os passos anteriores.

Depois de inicia a atualização da aplicação, pode ver o progresso da atualização no Service Fabric Explorer:

![Service Fabric Explorer][sfx2]

A atualização da aplicação poderá demorar vários minutos. Quando a atualização estiver concluída, a aplicação será executada a próxima versão.  Neste exemplo, "1.0.0.20170815.4".

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicionar controlo de origem ao seu projeto
> * Criar uma definição de compilação
> * Criar uma definição de versão
> * Implementar e atualizar uma aplicação automaticamente

Avançar para o próximo tutorial:
> [!div class="nextstepaction"]
> [Configurar a monitorização e diagnóstico para a aplicação](service-fabric-tutorial-monitoring-aspnet.md) 


<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[add-task]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddTask.png
[new-task]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewTask.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
[sfx1]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Push.png
[continuous-delivery-with-VSTS]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpointDialog.png
[run-on-agent]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/RunOnAgent.png