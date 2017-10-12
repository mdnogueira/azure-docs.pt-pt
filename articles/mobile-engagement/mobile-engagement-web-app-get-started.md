---
title: "Introdução ao Azure Mobile Engagement para Aplicações Web | Microsoft Docs"
description: "Saiba como utilizar o Azure Mobile Engagement com notificações push e análise para Aplicações Web."
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 04afe53a-4caf-4c80-bd75-20cc630cd75c
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: js
ms.topic: hero-article
ms.date: 06/01/2016
ms.author: piyushjo
ms.openlocfilehash: abcb04e4e0a3ae4fdba3a4ded20b3846ac3b21e6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-web-apps"></a>Introdução ao Azure Mobile Engagement para Aplicações Web
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra-lhe como utilizar o Azure Mobile Engagement para compreender a sua utilização de Aplicações Web.

> [!NOTE]
> O serviço Azure Mobile Engagement será extinto em março de 2018 e, atualmente, apenas está disponível para os clientes existentes. Para obter mais informações, veja [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

Para este tutorial, necessita do seguinte:

* Visual Studio 2015 ou outro editor à sua escolha
* [SDK Web](http://aka.ms/P7b453)

Este SDK Web encontra-se em modo de Pré-visualização e, de momento, só suporta Análise e ainda não suporta o envio de notificações push por browser ou na aplicação. 

> [!NOTE]
> Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-web-app-get-started).
> 
> 

## <a name="setup-mobile-engagement-for-your-web-app"></a>Configurar o Mobile Engagement para a aplicação Web
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Ligar a aplicação ao back-end do Mobile Engagement
Este tutorial apresenta uma "integração básica", que é o conjunto mínimo necessário para recolher dados.

Iremos criar uma aplicação Web básica com o Visual Studio para demonstrar a integração, apesar de também poder seguir os passos com qualquer aplicação Web criada fora do Visual Studio. 

### <a name="create-a-new-web-app"></a>Criar uma nova Aplicação Web
Os seguintes passos assumem a utilização do Visual Studio 2015, apesar de os passos serem semelhantes em versões anteriores do Visual Studio. 

1. Inicie o Visual Studio e, no ecrã **Base**, selecione **Novo Projeto**.
2. No pop-up, selecione **Web** -> **Aplicação Web ASP.Net**. Introduza o **Nome** da aplicação, a **Localização** e o **Nome da solução** e, em seguida, clique em **OK**.
3. No pop-up **Selecionar um modelo**, selecione **Vazio** em **Modelos ASP.Net 4.5** e clique em **OK**. 

Criou um novo projeto de Aplicação Web em branco no qual vamos integrar o SDK Web do Azure Mobile Engagement.

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Ligar a aplicação ao back-end do Mobile Engagement
1. Crie uma nova pasta chamada **javascript** na sua solução e adicione o ficheiro Web SDK JS **azure-engagement.js** à mesma. 
2. Adicione um novo ficheiro chamado **main.js** nesta pasta javascript com o código seguinte. Certifique-se de que atualiza a cadeia de ligação. Este objeto `azureEngagement` será utilizado para aceder aos métodos de SDK Web. 
   
        var azureEngagement = {
            debug: true,
            connectionString: 'xxxxx'
        };
   
    ![Visual Studio com ficheiros js][1]

## <a name="enable-real-time-monitoring"></a>Ativar a monitorização em tempo real
Para iniciar o envio de dados e garantir que os utilizadores estão ativos, terá de enviar, pelo menos, uma Atividade para o back-end do Mobile Engagement. Uma atividade no contexto de uma aplicação Web é uma página Web. 

1. Crie uma nova página denominada **home.html** na sua solução e defina-a como a página inicial da sua aplicação Web. 
2. Inclua os dois javascripts adicionados anteriormente nesta página ao adicionar o seguinte na etiqueta body. 
   
        <script type="text/javascript" src="javascript/main.js"></script>
        <script type="text/javascript" src="javascript/azure-engagement.js"></script>
3. Atualize a etiqueta body para chamar o método `startActivity` do EngagementAgent
   
        <body onload="engagement.agent.startActivity('Home')">
4. O seu **home.html** terá este aspeto
   
        <html>
        <head>
            ...
        </head>
        <body onload="engagement.agent.startActivity('Home')">
            <script type="text/javascript" src="javascript/main.js"></script>
            <script type="text/javascript" src="javascript/azure-engagement.js"></script>
        </body>
        </html>

## <a name="connect-app-with-real-time-monitoring"></a>Ligar a aplicação com a monitorização em tempo real
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

  ![][2]

## <a name="extend-analytics"></a>Expandir a análise
Seguem-se todos os métodos atualmente disponíveis no SDK Web que pode utilizar para a análise:

1. Atividades/páginas Web:
   
        engagement.agent.startActivity(name);
        engagement.agent.endActivity();
2. Eventos
   
        engagement.agent.sendEvent(name, extras);
3. Erros
   
        engagement.agent.sendError(name, extras);
4. Tarefas
   
        engagement.agent.startJob(name);
        engagement.agent.endJob(name);

<!-- Images. -->
[1]: ./media/mobile-engagement-web-app-get-started/visual-studio-solution-js.png
[2]: ./media/mobile-engagement-web-app-get-started/session.png

