---
title: "Introdução ao Azure Mobile Engagement para Aplicações Universais do Windows"
description: "Saiba como utilizar o Azure Mobile Engagement com notificações push e de análise para Aplicações Universais do Windows."
services: mobile-engagement
documentationcenter: windows
author: piyushjo
manager: erikre
editor: 
ms.assetid: 48103867-7f64-4646-b019-42bd797d38e2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 40db7e4dd151ec391c754dc6d4145aeeb8058eca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-windows-universal-apps"></a>Introdução ao Azure Mobile Engagement para Aplicações Universais do Windows
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como utilizar o Azure Mobile Engagement para compreender a utilização da aplicação e o envio de notificações push para utilizadores segmentados de uma aplicação Universal do Windows.
Este tutorial demonstra o cenário de difusão simples utilizando o Mobile Engagement. Irá criar uma Aplicação Universal do Windows em branco que recolhe dados de utilização de aplicação básicas e recebe notificações push através do Serviço de Notificações do Windows (WNS).

> [!NOTE]
> O serviço Azure Mobile Engagement será extinto em março de 2018 e, atualmente, apenas está disponível para os clientes existentes. Para obter mais informações, veja [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

## <a name="prerequisites"></a>Pré-requisitos
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

## <a name="set-up-mobile-engagement-for-your-windows-universal-app"></a>Configurar o Mobile Engagement para a aplicação Universal do Windows
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Ligar a aplicação ao back-end do Mobile Engagement
Este tutorial apresenta uma "integração básica", o conjunto mínimo necessário para recolher dados e enviar uma notificação push. É possível encontrar toda a documentação da integração na página [Integração do SDK Windows Universal do Mobile Engagement](mobile-engagement-windows-store-sdk-overview.md).

Irá criar uma aplicação básica com o Visual Studio para demonstrar a integração.

### <a name="create-a-windows-universal-app-project"></a>Criar um projeto da Aplicação Universal do Windows
Os seguintes passos assumem a utilização do Visual Studio 2015, apesar de os passos serem semelhantes em versões anteriores do Visual Studio.

1. Inicie o Visual Studio e, no ecrã **Base**, selecione **Novo Projeto**.
2. No pop-up, selecione **Windows**  -> **Universal** -> **Aplicação em Branco (Universal do Windows)**. Introduza o **Nome** da aplicação, o nome da **Solução** e, em seguida, clique em **OK**.

    ![][1]

Criou um projeto de Aplicação Universal do Windows no qual irá integrar depois o SDK do Azure Mobile Engagement.

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Ligar a aplicação ao back-end do Mobile Engagement
1. Instale o pacote Nuget [MicrosoftAzure.MobileEngagement] no seu projeto. Se tiver como objetivo as plataformas Windows e Windows Phone, terá de efetuar este procedimento para ambos os projetos. Para o Windows 8.x e o Windows Phone 8.1, o mesmo pacote Nuget coloca os binários específicos da plataforma corretos em cada projeto.
2. Abra **Package.appxmanifest** e certifique-se de que a capacidade seguinte está adicionada aí:

        Internet (Client)

    ![][2]
3. Agora copie a cadeia de ligação que copiou anteriormente para a sua Aplicação de Mobile Engagement e cole-a no ficheiro `Resources\EngagementConfiguration.xml`, entre as etiquetas `<connectionString>` e `</connectionString>`:

    ![][3]

    > [!TIP]
    > Se a sua Aplicação visar as plataformas Windows e Windows Phone, deve criar ainda duas Aplicações Mobile Engagement – uma para cada plataforma suportada. Dispor de duas aplicações permite garantir que consegue criar a segmentação correta do público-alvo e que consegue enviar notificações adequadamente direcionadas para cada plataforma.

    > [!IMPORTANT]
    > O NuGet não copia automaticamente os recursos do SDK para a aplicação UWP do Windows 10. Deve fazê-lo manualmente. Para tal, siga os passos que aparecem (readme.txt) quando o pacote de Nuget é instalado.  

1. No ficheiro `App.xaml.cs`:

    a. Adicionar a instrução `using`:

            using Microsoft.Azure.Engagement;

    b. Adicione um método que inicializa o Mobile Engagement:

           private void InitEngagement(IActivatedEventArgs e)
           {
             EngagementAgent.Instance.Init(e);

             //... rest of the code
           }

    c. Inicializar o SDK no método **OnLaunched**:

            protected override void OnLaunched(LaunchActivatedEventArgs e)
            {
              InitEngagement(e);

              //... rest of the code
            }

    c. Insira o seguinte no método **OnActivated** e adicione o método se não estiver já presente:

            protected override void OnActivated(IActivatedEventArgs e)
            {
              InitEngagement(e);

              //... rest of the code
            }

## <a id="monitor"></a>Ativar a monitorização em tempo real
Para iniciar o envio de dados e garantir que os utilizadores estão ativos, terá de enviar, pelo menos, um ecrã (Atividade) para o back-end do Mobile Engagement.

1. No **MainPage.xaml.cs**, adicione a seguinte instrução `using`:

    utilizar o Microsoft.Azure.Engagement.Overlay;
2. Altere a classe base da **MainPage** de **Page** para **EngagementPageOverlay**:

        class MainPage : EngagementPageOverlay
3. No ficheiro `MainPage.xaml`:

    a. Adicione às suas instruções de espaços de nomes:

        xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

    b. Substitua a **Page** no nome de etiqueta XML por **engagement:EngagementPageOverlay**

> [!IMPORTANT]
> Se a sua página substitui o método `OnNavigatedTo`, certifique-se de que chama `base.OnNavigatedTo(e)`. Caso contrário, a atividade não é comunicada (o `EngagementPage` chama `StartActivity` dentro do respetivo método `OnNavigatedTo`). Isto é especialmente importante num projeto Windows Phone onde o modelo predefinido tem um método `OnNavigatedTo`.
>
> Em **aplicações Windows 10 Universal**, utilize o método recomendado na secção “Recommended method: overload your Page classes” (“Método recomendado: sobrecarregar as classes de Página”) de [Advanced Reporting with the Windows Universal Apps Engagement SDK (Relatórios Avançados com o SDK Engagement das Aplicações Universais do Windows)](mobile-engagement-windows-store-advanced-reporting.md) em vez do método mencionado acima.

## <a id="monitor"></a>Ligar a aplicação com a monitorização em tempo real
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Ativar as notificações push e mensagens na aplicação
O Mobile Engagement permite interagir e alcançar os seus utilizadores com notificações push e mensagens na aplicação no contexto das campanhas. Este módulo é designado ALCANCE no portal do Mobile Engagement.
As secções seguintes configuram a aplicação para as receber.

### <a name="enable-your-app-to-receive-wns-push-notifications"></a>Permitir que a aplicação receba Notificações Push do WNS
1. No ficheiro `Package.appxmanifest`, no separador **Aplicação**, em **Notificações**, defina **Com capacidade de alerta:** para **Sim**

    ![][5]

### <a name="initialize-the-reach-sdk"></a>Inicializar o SDK do ALCANCE
Em `App.xaml.cs`, chame **EngagementReach.Instance.Init(e);** na função **InitEngagement** logo após a inicialização do agente:

        private void InitEngagement(IActivatedEventArgs e)
        {
           EngagementAgent.Instance.Init(e);
           EngagementReach.Instance.Init(e);
        }

Está pronto para enviar um alerta. A seguir, vamos confirmar que realizou corretamente esta integração básica.

### <a name="grant-access-to-mobile-engagement-to-send-notifications"></a>Conceder acesso ao Mobile Engagement para enviar notificações
1. Abra o [Dev Center da Loja Windows] no seu browser, inicie sessão e crie uma conta, se necessário.
2. Clique em **Dashboard** no canto superior direita e, em seguida, clique em **Criar uma nova aplicação** no menu do painel esquerdo.

    ![][9]
3. Crie a sua aplicação ao reservar o respetivo nome.

    ![][10]
4. Quando a aplicação tiver sido criada, navegue para **Serviços -> Notificações push** no menu à esquerda.

    ![][11]
5. Na secção Notificações push, clique na ligação **site dos Serviços Live**.

    ![][12]
6. Será direcionado para a secção Credenciais de push. Certifique-se de que está a utilizar a secção **Definições da Aplicação** e, em seguida, copie o **SID do Pacote** e o **Segredo do cliente**

    ![][13]
7. Navegue para as **Definições** do seu portal Mobile Engagement e clique na secção **Push Nativo** à esquerda. Em seguida, clique no botão **Editar** para introduzir o **Identificador de segurança do pacote (SID)** e a sua **Chave Secreta**, conforme apresentado:

    ![][6]
8. Finalmente, certifique-se de que associou a sua aplicação do Visual Studio a esta aplicação criada na Loja de aplicações. Clique em **Associar a Aplicação à Loja** no Visual Studio.

    ![][7]

## <a id="send"></a>Enviar uma notificação à aplicação
[!INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

Se a aplicação estiver a ser executada, verá uma notificação na aplicação. Caso contrário, se a aplicação for fechada, verá uma notificação de alerta.
Se vir uma notificação na aplicação, mas não uma notificação de alerta, e estiver a executar a aplicação em modo de depuração no Visual Studio, experimente **Eventos de ciclo de vida -> Suspender** na barra de ferramentas para se certificar de que a aplicação está suspensa. Se clicou no botão Base durante a depuração da aplicação no Visual Studio, esta não ficará sempre suspensa e, apesar de ver a notificação como na aplicação, não aparecerá como notificação de alerta.  

![][8]

<!-- URLs. -->
[Mobile Engagement Windows Universal SDK documentation]: ../mobile-engagement-windows-store-integrate-engagement/
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9864592
[Dev Center da Loja Windows]: https://dev.windows.com
[Windows Universal Apps - Overlay integration]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-store-dotnet-get-started/universal-app-creation.png
[2]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[3]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[5]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[6]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/associate-app-store.png
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/vs-suspend.png
[9]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_create_app.png
[10]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_app_name.png
[11]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push.png
[12]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_1.png
[13]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_creds.png
