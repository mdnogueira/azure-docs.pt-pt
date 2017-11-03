---
title: "Implementação contínua do serviço de aplicações do Azure | Microsoft Docs"
description: "Saiba como ativar a implementação contínua para o Serviço de Aplicações do Azure."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2016
ms.author: dariagrigoriu
ms.openlocfilehash: ef5924607868bcb3dc35e279539b78d5a0e17c1a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="continuous-deployment-to-azure-app-service"></a>Implementação Contínua para o Serviço de Aplicações do Azure
Este tutorial mostra como configurar um fluxo de trabalho de implementação contínua para sua [Web Apps do Azure](app-service-web-overview.md). Integração de serviço de aplicações com BitBucket, GitHub e [serviços de equipa do Visual Studio (VSTS)](https://www.visualstudio.com/team-services/) permite que um fluxo de trabalho de implementação contínua onde Azure obtém nas atualizações mais recentes do seu projeto publicado destes serviços. A implementação contínua é uma excelente opção para projetos em que várias e frequentes contribuições estão a ser integradas.

Para saber como configurar a implementação contínua manualmente a partir de um repositório de nuvem não são listado pelo Portal do Azure (tais como [GitLab](https://gitlab.com/)), consulte [como configurar a implementação contínua utilizando os passos manuais](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="overview"></a>Ativar a implementação contínua
Para ativar a implementação contínua,

1. Publique o conteúdo da aplicação no repositório que será utilizado para a implementação contínua.  
    Para mais informações sobre como publicar o seu projeto nestes serviços, veja [Criar um repositório (GitHub)], [Criar um repositório (BitBucket)], e [Introdução ao VSTS].
2. No painel de menu da aplicação no [Portal do Azure], clique em **IMPLEMENTAÇÃO DA APLICAÇÃO > Opções de implementação**. Clique em **Escolher Origem** e, em seguida, clique na origem da implementação.  
   
    ![](./media/app-service-continuous-deployment/cd_options.png)
   
   > [!NOTE]
   > Para configurar uma conta VSTS para implementação do Serviço de Aplicações, veja este [tutorial](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).
   > 
   > 
3. Conclua o fluxo de trabalho de autorização.
4. No painel **Origem da implementação**, escolha o projeto e ramo a partir dos quais implementar. Quando tiver terminado, clique em **OK**.
   
    ![](./media/app-service-continuous-deployment/github_option.png)
   
   > [!NOTE]
   > Ao ativar a implementação contínua com o GitHub ou o BitBucket, serão apresentados projetos públicos e privados.
   > 
   > 
   
    O Serviço de Aplicações cria uma associação ao repositório selecionado, obtém os ficheiros do ramo especificado e mantém um clone do repositório da sua aplicação Serviço de Aplicações. Ao configurar a implementação contínua no VSTS a partir do portal do Azure, a integração utiliza o Serviço de Aplicações [Motor de implementação Kudu](https://github.com/projectkudu/kudu/wiki), que já automatiza tarefas de compilação e implementação com cada `git push`. Não é necessário configurar separadamente a implementação contínua no VSTS. Após a conclusão deste processo, o painel **Opções de implementação** da aplicação mostrará uma implementação ativa, indicando que a implementação foi bem sucedida.
5. Para verificar se a aplicação for implementada com êxito, clique no **URL** na parte superior do painel da aplicação no portal do Azure.
6. Para verificar se a implementação contínua está a ocorrer a partir do repositório de sua escolha, emita uma alteração para o repositório. A aplicação deve atualizar para refletir as alterações, pouco tempo depois de a emissão via push para o repositório estar concluída. Pode verificar se obteve a atualização no painel **Opções de implementação** da sua aplicação.

## <a name="VSsolution"></a>Implementação contínua de uma solução Visual Studio
Emitir uma solução Visual Studio para o Serviço de Aplicações do Azure é tão fácil como emitir um ficheiro index.html simples. O processo de implementação do Serviço de Aplicações simplifica todos os detalhes, incluindo o restauro de dependências NuGet e a criação dos binários da aplicação. Pode seguir as melhores práticas de controlo da origem do código de manutenção apenas no repositório Git, e deixar que a implementação do Serviço de Aplicações faça o resto.

Os passos para emitir a sua solução Visual Studio para o Serviço de Aplicações são iguais aos da [secção anterior](#overview), desde que configure a solução e o repositório da seguinte forma:

* Utilize a opção de controlo de origem do Visual Studio para gerar um ficheiro `.gitignore`, como na imagem abaixo, ou adicione manualmente um ficheiro `.gitignore` à raiz do repositório, com conteúdo semelhante a este [exemplo .gitignore](https://github.com/github/gitignore/blob/master/VisualStudio.gitignore).
  
  ![](./media/app-service-continuous-deployment/VS_source_control.png)
* Adicione a árvore completa de diretórios da solução ao repositório, com o ficheiro .sln na raiz do repositório.

Depois de configurar o seu repositório conforme descrito e de ter configurado a aplicação no Azure para publicação contínua a partir de um dos repositórios Git online, pode desenvolver a sua aplicação ASP.NET localmente no Visual Studio e implementar continuamente o seu código de forma simples, ao emitir as suas alterações para o repositório Git online.

## <a name="disableCD"></a>Desativar a implementação contínua
Para desativar a implementação contínua,

1. No painel de menu da aplicação no [Portal do Azure], clique em **IMPLEMENTAÇÃO DA APLICAÇÃO > Opções de implementação**. Em seguida, clique em **Desligar** no painel **Opções de implementação**.
   
    ![](./media/app-service-continuous-deployment/cd_disconnect.png)
2. Depois de responder **Sim** à mensagem de confirmação, pode voltar ao painel da aplicação e clicar em **IMPLEMENTAÇÃO DA APLICAÇÃO > Opções de implementação**, se quiser configurar a publicação a partir de outra origem.

## <a name="additional-resources"></a>Recursos Adicionais
* [Como investigar problemas comuns da implementação contínua](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Como utilizar o PowerShell para o Azure]
* [Como utilizar as Ferramentas de Linha de Comandos do Azure para Mac e Linux]
* [Documentação do Git]
* [Kudu do projeto](https://github.com/projectkudu/kudu/wiki)
* [Utilizar o Azure para gerar automaticamente um pipeline de CI/CD para implementar uma aplicação ASP.NET 4](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic)

> [!NOTE]
> Se pretender começar a utilizar o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [Experimentar o App Service](https://azure.microsoft.com/try/app-service/), onde pode criar de imediato uma aplicação Web de arranque de curta duração no App Service. Sem cartões de crédito; sem compromissos.
> 
> 

[Portal do Azure]: https://portal.azure.com
[VSTS Portal]: https://www.visualstudio.com/en-us/products/visual-studio-team-services-vs.aspx
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Como utilizar o PowerShell para o Azure]: /powershell/azureps-cmdlets-docs
[Como utilizar as Ferramentas de Linha de Comandos do Azure para Mac e Linux]:../cli-install-nodejs.md
[Documentação do Git]: http://git-scm.com/documentation

[Criar um repositório (GitHub)]: https://help.github.com/articles/create-a-repo
[Criar um repositório (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[Introdução ao VSTS]: https://www.visualstudio.com/docs/vsts-tfs-overview
