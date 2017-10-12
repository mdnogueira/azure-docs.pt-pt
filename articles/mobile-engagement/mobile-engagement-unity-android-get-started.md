---
title: "Introdução ao Azure Mobile Engagement para implementação do Unity Android"
description: "Saiba como utilizar o Azure Mobile Engagement com Notificações Push e de Análise para implementação de aplicações Unity em dispositivos iOS."
services: mobile-engagement
documentationcenter: unity
author: piyushjo
manager: erikre
editor: 
ms.assetid: d5f0ef79-be00-4cec-97a5-a0b2fdaa380e
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-unity-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: bf0b758159d475b4ed7eadb84227e4824e11ba86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-unity-android-deployment"></a>Introdução ao Azure Mobile Engagement para implementação do Unity Android
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como utilizar o Azure Mobile Engagement para compreender a utilização da aplicação e saber como enviar notificações push para utilizadores segmentados de uma aplicação Unity quando implementar num dispositivo Android.
Este tutorial utiliza o tutorial clássico Unity Roll a Ball como ponto de partida. Deve seguir os passos neste [tutorial](mobile-engagement-unity-roll-a-ball.md) antes de continuar com a integração do Mobile Engagement que demonstramos no tutorial abaixo. 

Neste tutorial necessita do seguinte:

* [Unity Editor](http://unity3d.com/get-unity)
* [SDK Unity do Mobile Engagement](https://aka.ms/azmeunitysdk)
* SDK Google Android

> [!NOTE]
> Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-android-get-started).
> 
> 

## <a id="setup-azme"></a>Configurar o Mobile Engagement para a aplicação Android
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Ligar a aplicação ao back-end do Mobile Engagement
### <a name="import-the-unity-package"></a>Importar o pacote Unity
1. Transfira o [pacote do Unity do Mobile Engagement](https://aka.ms/azmeunitysdk) e guarde-o no seu computador local. 
2. Aceda a **Elementos -> Importar Pacote -> Pacote Personalizado** e selecione o pacote que transferiu no passo anterior. 
   
    ![][70] 
3. Certifique-se de que todos os ficheiros estão selecionados e clique no botão **Importar**. 
   
    ![][71] 
4. Assim que Importar com êxito, verá os ficheiros SDK importados no seu projeto.  
   
    ![][72] 

### <a name="update-the-engagementconfiguration"></a>Atualizar o EngagementConfiguration
1. Abra o ficheiro de script **EngagementConfiguration** da pasta SDK e atualize a **ANDROID\_CONNECTION\_STRING** com a cadeia de ligação que obteve anteriormente a partir do Portal do Azure.  
   
    ![][73]
2. Guarde o ficheiro 
3. Execute **Ficheiro -> Envolvimento -> Gerar Manifesto Android**. Este é o plug-in adicionado pelo SDK do Mobile Engagement e, ao clicar no mesmo, as definições do projeto serão automaticamente atualizadas. 
   
    ![][74]

> [!IMPORTANT]
> Certifique-se de que executa este procedimento sempre que atualizar o ficheiro **EngagementConfiguration**, caso contrário, as suas alterações não serão refletidas na aplicação. 
> 
> 

### <a name="configure-the-app-for-basic-tracking"></a>Configurar a aplicação para controlo básico
1. Abra o script **PlayerController** anexado ao objeto Leitor para edição. 
2. Adicione o seguinte ao utilizar a instrução:
   
        using Microsoft.Azure.Engagement.Unity;
3. Adicione o seguinte ao método `Start()`
   
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

### <a name="deploy-and-run-the-app"></a>Implementar e executar a aplicação
Certifique-se de que tem o SDK Android instalado no seu computador antes de tentar implementar esta aplicação Unity no seu dispositivo. 

1. Ligue um dispositivo Android ao seu computador. 
2. Abra **Ficheiro -> Definições de Criação** 
   
    ![][40]
3. Selecione **Android** e, em seguida, clique em **Alternar Plataforma**
   
    ![][51]
   
    ![][52]
4. Clique em **Definições de leitor** e faculte um Identificador de Pacote válido. 
   
    ![][53]
5. Finalmente, clique em **Criar e Executar**
   
    ![][54]
6. Poderá ser-lhe pedido para facultar um nome de pasta para armazenar o pacote Android. 
7. Se tudo correr bem, o pacote será implementado no seu dispositivo ligado e, em seguida, deverá ver o seu jogo Unity no seu telemóvel! 

## <a id="monitor"></a>Ligar a aplicação com a monitorização em tempo real
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Ativar as notificações push e mensagens na aplicação
[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

### <a name="update-the-engagementconfiguration"></a>Atualizar o EngagementConfiguration
1. Abra o ficheiro de script **EngagementConfiguration** da pasta SDK e atualize o **ANDROID\_GOOGLE\_NUMBER** com o **Número de Projeto Google** que obteve anteriormente a partir do portal do Google Cloud Developer. Trata-se de um valor de cadeia, por isso certifique-se de que o coloca entre aspas duplas. 
   
    ![][75]
2. Guarde o ficheiro. 
3. Execute **Ficheiro -> Envolvimento -> Gerar Manifesto Android**. Este é o plug-in adicionado pelo SDK do Mobile Engagement e, ao clicar no mesmo, as definições do projeto serão automaticamente atualizadas. 
   
    ![][74]

### <a name="configure-the-app-to-receive-notifications"></a>Configurar a aplicação para receber notificações
1. Abra o script **PlayerController** anexado ao objeto Leitor para edição. 
2. Adicione o seguinte ao método `Start()`
   
        EngagementReachAgent.Initialize();
3. Agora que a aplicação está atualizada, implemente e execute a aplicação num dispositivo de acordo com as instruções indicadas abaixo. 

[!INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[40]: ./media/mobile-engagement-unity-android-get-started/40.png
[70]: ./media/mobile-engagement-unity-android-get-started/70.png
[71]: ./media/mobile-engagement-unity-android-get-started/71.png
[72]: ./media/mobile-engagement-unity-android-get-started/72.png
[73]: ./media/mobile-engagement-unity-android-get-started/73.png
[74]: ./media/mobile-engagement-unity-android-get-started/74.png
[75]: ./media/mobile-engagement-unity-android-get-started/75.png
[51]: ./media/mobile-engagement-unity-android-get-started/51.png
[52]: ./media/mobile-engagement-unity-android-get-started/52.png
[53]: ./media/mobile-engagement-unity-android-get-started/53.png
[54]: ./media/mobile-engagement-unity-android-get-started/54.png
