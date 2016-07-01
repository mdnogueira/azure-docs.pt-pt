<properties
    pageTitle="Introdução ao Azure Mobile Engagement para aplicações do Windows Phone Silverlight"
    description="Saiba como utilizar o Azure Mobile Engagement com notificações push e de análise para aplicações do Windows Phone Silverlight."
    services="mobile-engagement"
    documentationCenter="windows"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="05/03/2016"
    ms.author="piyushjo" />

# Introdução ao Azure Mobile Engagement para aplicações do Windows Phone Silverlight

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como utilizar o Azure Mobile Engagement para compreender a utilização da aplicação e o envio de notificações push para utilizadores segmentados de uma aplicação do Windows Phone Silverlight.
Este tutorial demonstra o cenário de difusão simples utilizando o Mobile Engagement. Aí, pode criar uma aplicação do Windows Phone Silverlight em branco que recolhe dados básicos e recebe notificações push através do Serviço de Notificações Push da Microsoft (MPNS).

> [AZURE.NOTE] Se tem como objetivo o Windows Phone 8.1 (não Silverlight), consulte o [tutorial Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md).

Neste tutorial necessita do seguinte:

+ Visual Studio 2013
+ Pacote NuGet [MicrosoftAzure.MobileEngagement]

> [AZURE.NOTE] Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-windows-phone-get-started).

##<a id="setup-azme"></a>Configurar Mobile Engagement para a sua aplicação do Windows Phone

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Ligar a aplicação ao back-end do Mobile Engagement

Este tutorial apresenta uma “integração básica”, o conjunto mínimo necessário para recolher dados e enviar uma notificação push. É possível encontrar toda a documentação da integração na página [Integração do Windows Phone SDK do Mobile Engagement](mobile-engagement-windows-phone-sdk-overview.md).

Iremos criar uma aplicação básica com o Visual Studio para demonstrar a integração.

###Criar um novo projeto do Windows Phone Silverlight

Os seguintes passos assumem a utilização do Visual Studio 2015, apesar de os passos serem semelhantes em versões anteriores do Visual Studio. 

1. Inicie o Visual Studio e, no ecrã **Base**, selecione **Novo Projeto**.

2. No pop-up, selecione **Windows 8** -> **Windows Phone** -> **Aplicação em Branco (Windows Phone Silverlight)**. Introduza o **Nome** da aplicação, o nome da **Solução** e, em seguida, clique em **OK**.

    ![][1]

3. Pode optar por ter como destino o **Windows Phone 8.0** ou o **Windows Phone 8.1**.

Criou uma nova aplicação do Windows Phone Silverlight na qual vamos integrar o SDK do Azure Mobile Engagement.

###Ligar a aplicação ao back-end do Mobile Engagement

1. Instale o pacote nuget [MicrosoftAzure.MobileEngagement] no seu projeto.

2. Abra `WMAppManifest.xml` (na pasta Propriedades) e certifique-se de que o seguinte está declarado (adicione se não estiver) na etiqueta `<Capabilities />`:

        <Capability Name="ID_CAP_NETWORKING" />
        <Capability Name="ID_CAP_IDENTITY_DEVICE" />

    ![][2]

3. Agora cole a cadeia de ligação que copiou anteriormente para a sua aplicação de Mobile Engagement e cole-a no ficheiro `Resources\EngagementConfiguration.xml`, entre as etiquetas `<connectionString>` e `</connectionString>`:

    ![][3]

4. No ficheiro `App.xaml.cs`:

    a. Adicionar a instrução `using`:

            using Microsoft.Azure.Engagement;

    b. Inicializar o SDK no método `Application_Launching`:

            private void Application_Launching(object sender, LaunchingEventArgs e)
            {
              EngagementAgent.Instance.Init();
            }

    c. Inserir o seguinte no `Application_Activated`:

            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
               EngagementAgent.Instance.OnActivated(e);
            }

##<a id="monitor"></a>Ativar a monitorização em tempo real

Para iniciar o envio de dados e garantir que os utilizadores estão ativos, terá de enviar, pelo menos, um ecrã (Atividade) para o back-end do Mobile Engagement.

1. No MainPage.xaml.cs, adicione a instrução `using`:

        using Microsoft.Azure.Engagement;

2. Substitua a classe base da **MainPage**, que está antes de **PhoneApplicationPage**, por **EngagementPage**.

        class MainPage : EngagementPage 
    
3. No seu ficheiro `MainPage.xml`:

    a. Adicione às suas instruções de espaços de nomes:

         xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

    b. Substitua `phone:PhoneApplicationPage` no nome da etiqueta XML por `engagement:EngagementPage`.

##<a id="monitor"></a>Ligar a aplicação com a monitorização em tempo real

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Ativar as notificações push e mensagens na aplicação

O Mobile Engagement permite interagir e alcançar os seus utilizadores com Notificações Push e Mensagens na aplicação no contexto das campanhas. Este módulo é designado ALCANCE no portal do Mobile Engagement.
As secções seguintes configuram a aplicação para as receber.

###Permitir que a aplicação receba Notificações Push do MPNS

Adicionar novas Capacidades ao seu ficheiro `WMAppManifest.xml`:

        ID_CAP_PUSH_NOTIFICATION
        ID_CAP_WEBBROWSERCOMPONENT

   ![][5]

###Inicializar o SDK do ALCANCE

1. Em `App.xaml.cs`, chame `EngagementReach.Instance.Init();` na função **Application_Launching**, logo após a inicialização do agente:

        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }

2. Em `App.xaml.cs`, chame `EngagementReach.Instance.OnActivated(e);` na função **Application_Activated**, logo após a inicialização do agente:

        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

Está tudo pronto. Agora iremos confirmar que realizou corretamente esta integração básica.

##<a id="send"></a>Enviar uma notificação à sua aplicação

[AZURE.INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

Agora deverá ver uma notificação no seu dispositivo que irá aparecer como uma notificação na aplicação se a aplicação estiver aberta, caso contrário, como uma notificação de alerta conforme o seguinte: 

![][6]

<!-- URLs. -->
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9874664
[Documentação do Windows Phone SDK do Mobile Engagement]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[2]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[3]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[5]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[6]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png



<!--HONumber=Jun16_HO2-->


