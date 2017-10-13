---
title: "Introdução ao Azure Mobile Engagement para Xamarin.Android"
description: "Saiba como utilizar o Azure Mobile Engagement com Notificações Push e de Análise para Aplicações Xamarin.Android."
services: mobile-engagement
documentationcenter: xamarin
author: piyushjo
manager: erikre
editor: 
ms.assetid: fb68cf98-08a2-41b5-8e59-757469de3fe7
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 06/16/2016
ms.author: piyushjo
ms.openlocfilehash: 7b3d01b32c2d5a40448fc22861cd45f612238f2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-xamarinandroid-apps"></a>Introdução ao Azure Mobile Engagement para Aplicações Xamarin.Android
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como utilizar o Azure Mobile Engagement para compreender a utilização da aplicação e saber como enviar notificações push para utilizadores segmentados de uma aplicação Xamarin.Android.
Este tutorial demonstra o cenário de difusão simples utilizando o Mobile Engagement. Aqui, vai criar uma aplicação Xamarin.Android em branco que recolhe dados básicos e recebe notificações push através Google Cloud Messaging (GCM).

> [!NOTE]
> O serviço Azure Mobile Engagement será extinto em março de 2018 e, atualmente, apenas está disponível para os clientes existentes. Para obter mais informações, veja [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

Para este tutorial, necessita do seguinte:

* [Xamarin Studio](http://xamarin.com/studio). Também pode utilizar o Visual Studio com Xamarin, mas este tutorial utiliza o Xamarin Studio. Para obter instruções de instalação, consulte [Configuração e Instalação do Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
* [SDK Xamarin do Mobile Engagement](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [!NOTE]
> Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-android-get-started).
> 
> 

## <a id="setup-azme"></a>Configurar o Mobile Engagement para a aplicação Android
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Ligar a aplicação ao back-end do Mobile Engagement
Este tutorial apresenta uma “integração básica”, o conjunto mínimo necessário para recolher dados e enviar uma notificação push. 

Iremos criar uma aplicação básica com o Xamarin Studio para demonstrar a integração.

### <a name="create-a-new-xamarinandroid-project"></a>Criar um novo projeto Xamarin.Android
1. Inicie o **Xamarin Studio** Aceda a **Ficheiro** -> **Novo** -> **Solução** 
   
    ![][1]
2. Selecione **Aplicação Android**, em seguida certifique-se de que a linguagem selecionada é **C#** e, em seguida, clique em **Seguinte**.
   
    ![][2]
3. Preencha o **Nome da Aplicação** e o **Identificador de Organização**. Certifique-se de que assinala a opção **Serviços Google Play** e, em seguida, clique em **Seguinte**. 
   
    ![][3]
4. Atualize o **Nome do Projeto**, o **Nome da Solução** e a **Localização** se necessário e clique em **Criar**.
   
    ![][4]

O Xamarin Studio criará a aplicação na qual iremos integrar o Mobile Engagement. 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Ligar a aplicação ao back-end do Mobile Engagement
1. Clique com o botão direito do rato na pasta **Pacotes** nas janelas Solução e selecione **Adicionar Pacotes...**
   
    ![][5]
2. Procure o **SDK Xamarin do Microsoft Azure Mobile Engagement** e adicione-o à sua solução.  
   
    ![][6]
3. Abra **MainActivity.cs** e adicione o seguinte ao utilizar as instruções:
   
        using Microsoft.Azure.Engagement;
        using Microsoft.Azure.Engagement.Activity;
4. No método `OnCreate`, adicione o seguinte ao inicializar a ligação com o back-end do Mobile Engagement. Certifique-se de que adiciona a sua **ConnectionString**. 
   
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.ConnectionString = "YourConnectionStringFromAzurePortal";
        EngagementAgent.Init(engagementConfiguration);

### <a name="add-permissions-and-a-service-declaration"></a>Adicionar permissões e uma declaração de serviço
1. Abra o ficheiro **Manifest.xml** na pasta Propriedades. Selecione o separador Origem para atualizar diretamente a origem de XML.
2. Adicione estas permissões ao Manifest.xml (que pode ser encontrado na pasta **Propriedades**) do projeto imediatamente antes ou depois da etiqueta `<application>`:
   
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
3. Adicione o seguinte entre as etiquetas `<application>` e `</application>` para declarar o serviço do agente:
   
        <service
             android:name="com.microsoft.azure.engagement.service.EngagementService"
             android:exported="false"
             android:label="<Your application name>"
             android:process=":Engagement"/>
4. No código que acabou de colar, substitua `"<Your application name>"` na etiqueta. Isto é apresentado no menu **Definições**, onde os utilizadores podem ver os serviços em execução no dispositivo. Por exemplo, pode adicionar a palavra “Serviço” nessa etiqueta.

### <a name="send-a-screen-to-mobile-engagement"></a>Enviar um ecrã para o Mobile Engagement
Para iniciar o envio de dados e garantir que os utilizadores estão ativos, terá de enviar, pelo menos, um ecrã para o back-end do Mobile Engagement. Para fazê-lo, certifique-se de que `MainActivity` herda de `EngagementActivity` em vez de `Activity`.

    public class MainActivity : EngagementActivity

Em alternativa, se não for possível herdar de `EngagementActivity`, deve adicionar os métodos `.StartActivity` e `.EndActivity` em `OnResume` e `OnPause`, respetivamente.  

        protected override void OnResume()
            {
                EngagementAgent.StartActivity(EngagementAgentUtils.BuildEngagementActivityName(Java.Lang.Class.FromType(this.GetType())), null);
                base.OnResume();             
            }

            protected override void OnPause()
            {
                EngagementAgent.EndActivity();
                base.OnPause();            
            }

## <a id="monitor"></a>Ligar a aplicação com a monitorização em tempo real
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Ativar as notificações push e mensagens na aplicação
O Mobile Engagement permite interagir e ALCANÇAR os seus utilizadores com notificações push e mensagens na aplicação no contexto das campanhas. Este módulo é designado ALCANCE no portal do Mobile Engagement.
As secções seguintes configuram a aplicação para as receber.

[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[!INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[!INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[1]: ./media/mobile-engagement-xamarin-android-get-started/1.png
[2]: ./media/mobile-engagement-xamarin-android-get-started/2.png
[3]: ./media/mobile-engagement-xamarin-android-get-started/3.png
[4]: ./media/mobile-engagement-xamarin-android-get-started/4.png
[5]: ./media/mobile-engagement-xamarin-android-get-started/5.png
[6]: ./media/mobile-engagement-xamarin-android-get-started/6.png
