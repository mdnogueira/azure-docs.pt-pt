---
title: "Introdução ao Azure Mobile Engagement para Cordova/Phonegap"
description: "Saiba como utilizar o Azure Mobile Engagement com Notificações Push e de Análise para aplicações Cordova/Phonegap."
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 54fe9113-e239-4ed7-9fd1-a502d7ac7f47
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-phonegap
ms.devlang: js
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: d7a761310782faab1dda023785f93cf90742e2ae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-cordovaphonegap"></a>Introdução ao Azure Mobile Engagement para Cordova/Phonegap
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como utilizar o Azure Mobile Engagement para compreender a utilização da sua aplicação e saber como enviar notificações push para utilizadores segmentados para uma aplicação móvel desenvolvida com Cordova.

Neste tutorial, iremos criar uma aplicação Cordova em branco utilizando um Mac e, em seguida, integrar o SDK do Mobile Engagement. Este recolhe dados de análise básicos e recebe as notificações push através do Sistema de Notificações Push da Apple (APNS) para iOS e o Google Cloud Messaging (GCM) para Android. Iremos implementar a aplicação num dispositivo iOS ou Android para fins de teste. 

> [!NOTE]
> Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-cordova-get-started).
> 
> 

Para este tutorial, necessita do seguinte:

* XCode, que pode instalar a partir da Mac App Store (para implementar em iOS)
* [SDK e emulador Android](http://developer.android.com/sdk/installing/index.html) (para a implementação para Android)
* Certificado de notificação push (.p12) que pode obter no Apple Dev Center para APNS
* Número de Projeto GCM que pode obter a partir da sua Consola de Programador da Google para GCM
* [Plug-in Cordova do Mobile Engagement](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [!NOTE]
> Pode encontrar o código de origem e o ficheiro Leia-me para o plug-in Cordova na [GitHub](https://github.com/Azure/azure-mobile-engagement-cordova)
> 
> 

## <a id="setup-azme"></a>Configurar o Mobile Engagement para a aplicação Cordova
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Ligar a aplicação ao back-end do Mobile Engagement
Este tutorial apresenta uma “integração básica”, o conjunto mínimo necessário para recolher dados e enviar uma notificação push. 

Iremos criar uma aplicação básica com Cordova para demonstrar a integração:

### <a name="create-a-new-cordova-project"></a>Criar um novo projeto Cordova
1. Inicie a janela *Terminal* no seu computador Mac e digite o seguinte, que irá criar um novo projeto Cordova a partir do modelo predefinido. Certifique-se de que o perfil de publicação que acaba por utilizar para implementar a sua aplicação iOS está a utilizar 'com.mycompany.myapp' como o ID da Aplicação. 
   
        $ cordova create azme-cordova com.mycompany.myapp
        $ cd azme-cordova
2. Execute o seguinte para configurar o seu projeto para **iOS** e executá-lo no Simulador iOS:
   
        $ cordova platform add ios 
        $ cordova run ios
3. Execute o seguinte para configurar o seu projeto para **Android** e executá-lo no emulador Android. Certifique-se de que as definições do Emulador do SDK Android têm o seu Destino definido como Google APIs (Google Inc.), com a CPU/ABI como Google APIs ARM.  
   
        $ cordova platform add android
        $ cordova run android
4. Adicione o plug-in da Consola Cordova. 

    ```
    $ cordova plugin add cordova-plugin-console
    ``` 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Ligar a aplicação ao back-end do Mobile Engagement
1. Instale o plug-in do Azure Mobile Engagement Cordova ao fornecer os valores das variáveis para configurar o plug-in:
   
        cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
             --variable AZME_IOS_CONNECTION_STRING=<iOS Connection String> 
            --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
            --variable AZME_ANDROID_CONNECTION_STRING=<Android Connection String> 
            --variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
            --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
            --variable AZME_ACTION_URL =... (URL scheme which triggers the app for deep linking)
            --variable AZME_ENABLE_NATIVE_LOG=true|false
            --variable AZME_ENABLE_PLUGIN_LOG=true|false

*Android Reach Icon* : tem de ser o nome do recurso sem qualquer extensão, nem prefixo sorteável (por exemplo, mynotificationicon), e o ficheiro do ícone deve ser copiado para o seu projeto android (platforms/android/res/drawable)

*iOS Reach Icon*: tem de ser o nome do recurso com a extensão (por exemplo, mynotificationicon.png), e o ficheiro de ícone têm de ser adicionado no projeto iOS com XCode (utilizando o Menu para adicionar ficheiros)

## <a id="monitor"></a>Ativar a monitorização em tempo real
1. No projeto Cordova - edite **www/js/index.js** para adicionar a chamada para Mobile Engagement para declarar uma nova atividade, assim que o evento *deviceReady* é recebido.
   
         onDeviceReady: function() {
                Engagement.startActivity("myPage",{});
            }
2. Execute a aplicação:
   
   * **Para iOS**
     
       Na janela `Terminal`, inicie a sua aplicação numa nova instância de Simulator executando o seguinte:
     
           cordova run ios
   * **Para Android**
     
       Na janela `Terminal`, inicie a sua aplicação numa nova instância de emulador executando o seguinte:
     
           cordova run android
3. Pode ver o seguinte nos registos da consola:
   
        [Engagement] Agent: Session started
        [Engagement] Agent: Activity 'myPage' started
        [Engagement] Connection: Established
        [Engagement] Connection: Sent: appInfo
        [Engagement] Connection: Sent: startSession
        [Engagement] Connection: Sent: activity name='myPage'

## <a id="monitor"></a>Ligar a aplicação com a monitorização em tempo real
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Ativar Notificações Push e mensagens na aplicação
O Mobile Engagement permite interagir com os seus utilizadores com Notificações Push e mensagens na aplicação no contexto das campanhas. Este módulo é designado ALCANCE no portal do Mobile Engagement.
As secções seguintes irão configurar a aplicação para as receber.

### <a name="configure-push-credentials-for-mobile-engagement"></a>Configurar as credenciais de Push para Mobile Engagement
Para permitir que o Mobile Engagement envie Notificações Push em seu nome, tem de conceder acesso ao seu certificado iOS da Apple ou Chave de API do Servidor GCM. 

1. Navegue até ao seu portal Mobile Engagement. Certifique-se de que está na aplicação que estamos a utilizar para este projeto e, em seguida, clique no botão **Acionar** na parte inferior:
   
    ![][1]
2. Será encaminhado para a página de definições no seu Portal Engagement. A partir daqui, clique na secção **Push Nativo**:
   
    ![][2]
3. Configure a Chave de API do Servidor GCM/Certificado iOS
   
    **[iOS]**
   
    a. Selecione o seu .p12, carregue-o e escreva a palavra-passe:
   
    ![][3]
   
    **[Android]**
   
    a. Clique no ícone de edição à frente da **Chave de API** na secção Definições de GCM e no pop-up que aparece, cole a Chave de Servidor GCM e clique em **OK**. 
   
    ![][4]

### <a name="enable-push-notifications-in-the-cordova-app"></a>Ativar as notificações push na aplicação Cordova
Edite **www/js/index.js** para adicionar a chamada para Mobile Engagement para pedir notificações push e declarar um processador:

     onDeviceReady: function() {
           Engagement.initializeReach(  
                 // on OpenUrl  
                 function(_url) {   
                 alert(_url);   
                 });  
            Engagement.startActivity("myPage",{});  
        }

### <a name="run-the-app"></a>Executar a aplicação
**[iOS]**

1. Utilizaremos XCode para compilar e implementar a aplicação no dispositivo para testar as notificações push, uma vez que o iOS apenas permite notificações push para um dispositivo real. Vá ao local onde criou o seu projeto Cordova e navegue até à localização **...\platforms\ios**. Abra o ficheiro .xcodeproj nativo no XCode. 
2. Compile e implemente a aplicação Cordova para o dispositivo iOS com a conta que possui o perfil de aprovisionamento que contém o certificado que acabou de carregar para o portal de Mobile Engagement e o ID da Aplicação que corresponde ao que forneceu ao criar a aplicação Cordova. Pode consultar o *Identificador de Pacote* no seu ficheiro **Resources\*-info.plist** em XCode para comparar. 
3. Verá o pop-up de iOS standard no seu dispositivo a indicar que a aplicação está a solicitar permissão para enviar notificações. Conceda a permissão. 

**[Android]**

Pode simplesmente utilizar o emulador e executar a aplicação Android, uma vez que as notificações GCM são suportadas no emulador Android. 

    cordova run android

## <a id="send"></a>Enviar uma notificação à aplicação
Iremos agora criar uma campanha de Notificação Push simples que irá enviar um push para a sua aplicação em execução no dispositivo:

1. Navegue até ao separador **Alcance** no seu portal Mobile Engagement.
2. Clique em **Novo Anúncio** para criar a sua campanha de emissão
   
    ![][6]
3. Forneça entradas para criar a sua campanha **[Android]**
   
   * Forneça um **Nome** para a sua campanha. 
   * Selecione o **Tipo de Entrega** como *Notificação de Sistema* *Simples*
   * Selecione o **Tempo de entrega** como *"Qualquer altura"*
   * Forneça um **Título** para a sua notificação que será a primeira linha no push.
   * Forneça uma **Mensagem** para a sua notificação que irá servir como corpo da mensagem. 
     
     ![][11]
4. Forneça entradas para criar a sua campanha **[iOS]**
   
   * Forneça um **Nome** para a sua campanha. 
   * Selecione o **Tempo de entrega** como *"Apenas fora da aplicação"*
   * Forneça um **Título** para a sua notificação que será a primeira linha no push.
   * Forneça uma **Mensagem** para a sua notificação que irá servir como corpo da mensagem. 
     
     ![][12]
5. Desloque-se para baixo e, na secção de conteúdos, selecione **Apenas Notificação**
   
    ![][8]
6. [Opcional] Também pode fornecer um URL de Ação. Certifique-se de que utiliza um esquema de URL fornecido ao configurar a variável **AZME\_REDIRECIONAR\_URL** do plug-in, por exemplo, *myapp://test*.  
7. Terminou a configuração mais básica possível da campanha. Agora, desloque-se para baixo novamente e clique no botão **Criar** para guardar a sua campanha.
8. Por fim **Ative** a sua campanha
   
    ![][10]
9. Agora, deve uma notificação push no seu dispositivo ou emulador como parte desta campanha. 

## <a id="next-steps"></a>Passos Seguintes
[Descrição geral de todos os métodos disponíveis com o SDK Cordova do Mobile Engagement](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- Images. -->

[1]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[2]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[3]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[4]: ./media/mobile-engagement-cordova-get-started/api-key.png
[6]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[8]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[10]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[11]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-android.png
[12]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-ios.png

