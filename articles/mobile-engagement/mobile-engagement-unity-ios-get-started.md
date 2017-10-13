---
title: "Introdução ao Azure Mobile Engagement para implementação do Unity iOS"
description: "Saiba como utilizar o Azure Mobile Engagement com Notificações Push e de Análise para implementação de aplicações Unity em dispositivos iOS."
services: mobile-engagement
documentationcenter: unity
author: piyushjo
manager: erikre
editor: 
ms.assetid: 7ddfbac3-8d13-4ebe-b061-c865f357297f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-unity-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: c8f50404771965ec636065346ac04e059d264c3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-unity-ios-deployment"></a>Introdução ao Azure Mobile Engagement para implementação do Unity iOS
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como utilizar o Azure Mobile Engagement para compreender a utilização da aplicação e saber como enviar notificações push para utilizadores segmentados de uma aplicação Unity quando implementar num dispositivo iOS.
Este tutorial utiliza o tutorial clássico Unity Roll a Ball como ponto de partida. Deve seguir os passos neste [tutorial](mobile-engagement-unity-roll-a-ball.md) antes de continuar com a integração do Mobile Engagement que demonstramos no tutorial abaixo. 

Neste tutorial necessita do seguinte:

* [Unity Editor](http://unity3d.com/get-unity)
* [SDK Unity do Mobile Engagement](https://aka.ms/azmeunitysdk)
* XCode Editor

> [!NOTE]
> Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-ios-get-started).
> 
> 

## <a id="setup-azme"></a>Configurar o Mobile Engagement para a aplicação iOS
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
1. Abra o ficheiro de script **EngagementConfiguration** da pasta SDK e atualize a **IOS\_CONNECTION\_STRING** com a cadeia de ligação que obteve anteriormente a partir do Portal do Azure.  
   
    ![][73]
2. Guarde o ficheiro. 

### <a name="configure-the-app-for-basic-tracking"></a>Configurar a aplicação para controlo básico
1. Abra o script **PlayerController** anexado ao objeto Leitor para edição. 
2. Adicione o seguinte ao utilizar a instrução:
   
        using Microsoft.Azure.Engagement.Unity;
3. Adicione o seguinte ao método `Start()`
   
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

### <a name="deploy-and-run-the-app"></a>Implementar e executar a aplicação
1. Ligue um dispositivo iOS ao seu computador. 
2. Abra **Ficheiro -> Definições de Criação** 
   
    ![][40]
3. Selecione **iOS** e, em seguida, clique em **Alternar Plataforma**
   
    ![][41]
   
    ![][42]
4. Clique em **Definições de leitor** e faculte um Identificador de Pacote válido. 
   
    ![][53]
5. Finalmente, clique em **Criar e Executar**
   
    ![][54]
6. Poderá ser-lhe pedido para facultar um nome de pasta para armazenar o pacote iOS. 
   
    ![][43]
7. Se tudo funcionar bem, o projeto será compilado e deve abri-lo na sua aplicação XCode. 
8. Certifique-se de que o **Identificador de pacote** está correto no projeto.  
   
    ![][75]
9. Agora, execute a aplicação no XCode para que o pacote seja implementado no seu dispositivo ligado e, em seguida, deverá ver o seu jogo Unity no seu telemóvel! 

## <a id="monitor"></a>Ligar a aplicação com a monitorização em tempo real
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Ativar as notificações push e mensagens na aplicação
O Mobile Engagement permite interagir com os seus utilizadores e ALCANÇAR com notificações push e mensagens na aplicação no contexto das campanhas. Este módulo é designado ALCANCE no portal do Mobile Engagement.
Não tem de efetuar qualquer configuração adicional na sua aplicação para receber notificações, pois já está configurada para tal.

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[40]: ./media/mobile-engagement-unity-ios-get-started/40.png
[41]: ./media/mobile-engagement-unity-ios-get-started/41.png
[42]: ./media/mobile-engagement-unity-ios-get-started/42.png
[43]: ./media/mobile-engagement-unity-ios-get-started/43.png
[53]: ./media/mobile-engagement-unity-ios-get-started/53.png
[54]: ./media/mobile-engagement-unity-ios-get-started/54.png
[70]: ./media/mobile-engagement-unity-ios-get-started/70.png
[71]: ./media/mobile-engagement-unity-ios-get-started/71.png
[72]: ./media/mobile-engagement-unity-ios-get-started/72.png
[73]: ./media/mobile-engagement-unity-ios-get-started/73.png
[74]: ./media/mobile-engagement-unity-ios-get-started/74.png
[75]: ./media/mobile-engagement-unity-ios-get-started/75.png
