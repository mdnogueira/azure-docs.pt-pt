---
title: "Perguntas mais frequentes de implementação para aplicações web do Azure | Microsoft Docs"
description: "Obtenha respostas às perguntas mais frequentes sobre a implementação para a funcionalidade Web Apps do App Service do Azure."
services: app-service\web
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 318a236652229c4e093ca33886ac1831686aed73
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>Perguntas mais frequentes de implementação para aplicações Web no Azure

Este artigo tem respostas a perguntas mais frequentes (FAQ) sobre problemas de implementação para o [funcionalidade Web Apps do App Service do Azure](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>Posso estou a começar com as web apps do App Service. Como posso publicar o meu código?

Seguem-se algumas opções para publicar o código de aplicação web:

*   Implemente utilizando o Visual Studio. Se tiver a solução do Visual Studio, clique com o botão direito no projeto de aplicação web e, em seguida, selecione **publicar**.
*   Implemente utilizando um cliente de FTP. No portal do Azure, transferem o perfil de publicação da aplicação web que pretende implementar o código para. Em seguida, carregue ficheiros \site\wwwroot utilizando as mesmas credenciais FTP do perfil de publicação.

Para obter mais informações, consulte [implementar a aplicação no App Service](app-service-deploy-local-git.md).

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this"></a>Vejo uma mensagem de erro ao tentar implementar a partir do Visual Studio. Como resolver isto?

Se vir a mensagem seguinte, pode estar a utilizar uma versão mais antiga do SDK: "erro durante a implementação para o recurso 'YourResourceName' no grupo de recursos 'YourResourceGroup': MissingRegistrationForLocation: A subscrição não está registada para o tipo de recurso componentes na localização 'EUA Central'. Volte a registar para este fornecedor para ter acesso a esta localização." 

Para resolver este erro, atualize para o [SDK mais recente](https://azure.microsoft.com/downloads/). Se vir esta mensagem e tem o SDK mais recente, submeta um pedido de suporte.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Como posso implementar uma aplicação ASP.NET do Visual Studio para o App Service?
<a id="deployasp"></a>

O tutorial [criar a sua primeira aplicação web ASP.NET no Azure em cinco minutos](app-service-web-get-started-dotnet.md) mostra-lhe como implementar uma aplicação web ASP.NET numa aplicação web no App Service utilizando o Visual Studio 2017.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>Quais são os diferentes tipos de credenciais de implementação?

Serviço de aplicações suportam dois tipos de credenciais para a implementação de FTP/S e implementação de Git local. Para obter mais informações sobre como configurar as credenciais de implementação, consulte [Configure as credenciais de implementação de aplicação do serviço](app-service-deployment-credentials.md).

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>O que é a estrutura do ficheiro ou diretório da minha aplicação web do app Service?

Para obter informações sobre a estrutura de ficheiros da sua aplicação de serviço de aplicações, consulte [estrutura de ficheiros no Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure).

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>Como posso resolver "FTP erro 550 - existem não espaço suficiente no disco" quando tentam para os meus ficheiros FTP?

Se vir esta mensagem, é provável que está a executar para uma quota de disco no plano de serviço para a sua aplicação web. Poderá ter de aumentar verticalmente para um escalão superior de serviço com base nas necessidades de espaço em disco. Para obter mais informações sobre limites de recursos e planos de preços, consulte [preços do serviço de aplicações](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>Como posso configurar a implementação contínua para a minha aplicação web do app Service?

Pode configurar a implementação contínua provém de vários recursos, incluindo o Visual Studio Team Services, OneDrive, GitHub, Bitbucket, Dropbox e outros repositórios de Git. Estas opções estão disponíveis no portal. [Implementação contínua do serviço de aplicações](app-service-continuous-deployment.md) é um tutorial útil que explica como configurar a implementação contínua.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>Como posso resolver problemas com a implementação contínua do GitHub e Bitbucket?

Para ajudar a investigar problemas com a implementação contínua do GitHub ou o Bitbucket, consulte [investigar a implementação contínua](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment).

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this"></a>Posso não é possível de FTP para os meus sites e publicar o meu código. Como resolver isto?

Para resolver problemas FTP:

1. Certifique-se de que está a introduzir o nome de anfitrião correta e as credenciais. Para obter informações detalhadas sobre os diferentes tipos de credenciais e como utilizá-los, consulte [as credenciais de implementação](https://github.com/projectkudu/kudu/wiki/Deployment-credentials).
2. Certifique-se de que as portas FTP não são bloqueadas por uma firewall. As portas devem ter estas definições:
    * Porta de ligação de controlo de FTP: 21
    * Porta de ligação de dados FTP: 989, 10001 10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>Como publicar o meu código do serviço de aplicações?

O início rápido do Azure foi concebido para ajudar a implementar a aplicação utilizando a pilha de implementação e o método à sua escolha. Para utilizar o guia de introdução, no portal do Azure, aceda a **definições** > **implementação da aplicação**.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>Por que motivo a minha aplicação, por vezes, reiniciar após a implementação do serviço de aplicações?

Para saber mais sobre as circunstâncias em que uma implementação de aplicações poderá resultar no reinício, consulte [implementação vs. problemas de tempo de execução](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts"). Como o artigo descreve, serviço de aplicações implementa ficheiros na pasta wwwroot. Esta nunca diretamente reiniciar a aplicação.

## <a name="how-do-i-integrate-visual-studio-team-services-code-with-app-service"></a>Como integrar o código do Visual Studio Team Services com o App Service?

Tem duas opções para utilizar a implementação contínua com o Visual Studio Team Services:

*   Utilize um projeto de Git. Liga através do serviço de aplicações utilizando as opções de implementação para esse repositório.
*   Utilize um projeto de equipa Foundation versão controlo (TFVC). Implemente utilizando o agente de compilação para o App Service.

Implementação contínua do código para ambos os estas opções depende em fluxos de trabalho de programador existentes e procedimentos de verificação. Para obter mais informações, consulte estes artigos: 

*   [Implementar a implementação contínua da aplicação para um Web site do Azure](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Configurar uma conta do Visual Studio Team Services, pelo que pode implementar uma aplicação web](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>Como utilizar FTP ou FTPS para implementar a minha aplicação no App Service?

Para obter informações sobre como utilizar o FTP ou FTPS para implementar a aplicação web no App Service, consulte [implementar a aplicação no serviço de aplicações através da utilização de FTP/S](app-service-deploy-ftp.md).
