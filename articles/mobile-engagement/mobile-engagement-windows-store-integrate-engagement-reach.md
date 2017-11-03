---
title: "Integração do SDK Windows Universal alcance de aplicações"
description: "Como integrar o alcance do Azure Mobile Engagement com aplicações universais do Windows"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a31ca1d6-856f-4aec-898a-07969ae5f7ec
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 9311e998e67d8d0d56da68fc9460df32ce7ce5a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="windows-universal-apps-reach-sdk-integration"></a>Integração do SDK Windows Universal alcance de aplicações
Tem de seguir o procedimento de integração descrito no [Windows Universal integração SDK do Engagement](mobile-engagement-windows-store-integrate-engagement.md) antes de seguir este guia.

## <a name="embed-the-engagement-reach-sdk-into-your-windows-universal-project"></a>Incorporar o SDK de alcançar o Engagement no seu projeto Windows Universal
Não tem nada para adicionar. `EngagementReach`as referências e recursos já estão no seu projeto.

> [!TIP]
> Pode personalizar as imagens localizadas no `Resources` pasta do projeto, especialmente no ícone de marca (que predefinido para o ícone do Engagement). Nas aplicações universais também pode mover o `Resources` pasta no seu projeto partilhado, partilhar o respetivo conteúdo entre as aplicações, mas terá de manter o `Resources\EngagementConfiguration.xml` de ficheiros na localização predefinida é plataforma dependente.
> 
> 

## <a name="enable-the-windows-notification-service"></a>Ativar o serviço de notificação do Windows
### <a name="windows-8x-and-windows-phone-81-only"></a>Windows 8. x e Windows Phone 8.1 apenas
Para poder utilizar o **serviço de notificação do Windows** (referido como WNS) na sua `Package.appxmanifest` o ficheiro `Application UI` clique em `All Image Assets` na caixa de bot esquerdo. À direita da caixa numa `Notifications`, alterar `toast capable` de `(not set)` para `(Yes)`.

### <a name="all-platforms"></a>Todas as plataformas
Tem de sincronizar a sua aplicação na sua conta Microsoft e para a plataforma de envolvimento. Para este tem de criar uma conta ou iniciar sessão em [dev center do windows](https://dev.windows.com). Depois disso, crie uma nova aplicação e localizar o SID e a chave secreta. No frontend engagement, aceda a definição de aplicação no `native push` e cole as suas credenciais. Depois disso, o botão direito do rato clique no seu projeto, selecione `store` e `Associate App with the Store...`. Terá de selecionar a aplicação tem de criar antes para a sincronizar.

## <a name="initialize-the-engagement-reach-sdk"></a>Inicializar o alcance do Engagement SDK
Modificar o `App.xaml.cs`:

* Inserir `EngagementReach.Instance.Init` apenas após `EngagementAgent.Instance.Init` no seu `InitEngagement` método:
  
      private void InitEngagement(IActivatedEventArgs e)
      {
        EngagementAgent.Instance.Init(e);
        EngagementReach.Instance.Init(e);
      }
  
  O `EngagementReach.Instance.Init` é executado num thread dedicado. Não é necessário fazê-lo por si.

> [!NOTE]
> Se estiver a utilizar notificações push noutro local na sua aplicação, em seguida, terá de [partilhar o canal de push](#push-channel-sharing) com o alcance do Engagement.
> 
> 

## <a name="integration"></a>Integração
Engagement proporciona duas formas de adicionar vistas interstitial para anúncios e inquéritos de faixas na aplicação de alcance e na sua aplicação: a integração de sobreposição e a integração de manual de vistas web. Não deve combinar ambos os abordagem na mesma página.

A escolha entre a integração de dois pode ser resumida desta forma:

* Pode escolher a integração de sobreposição se já herda as páginas do agente `EngagementPage`, é apenas um fim de substituir `EngagementPage` por `EngagementPageOverlay` e `xmlns:engagement="using:Microsoft.Azure.Engagement"` por `xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"` nas suas páginas.
* Pode escolher as vistas de web integração manual se pretender precisamente coloque IU alcançar dentro do seu páginas ou se não quiser adicionar outro nível de herança para as páginas. 

### <a name="overlay-integration"></a>Integração de sobreposição
Sobreposição o Engagement dinamicamente adiciona os elementos de IU utilizados para apresentar as campanhas de alcance na sua página. Se a sobreposição não de acordo com a esquema deve considerar as vistas de web integração manual em vez disso.

Na sua alteração de ficheiro primeiro `EngagementPage` referência ao`EngagementPageOverlay`

* Adicione às suas instruções de espaços de nomes:
  
      xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"
* Substitua `engagement:EngagementPage` com `engagement:EngagementPageOverlay`:

**Com EngagementPage:**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">

            <!-- Your layout -->
        </engagement:EngagementPage>

**Com EngagementPageOverlay:**

        <engagement:EngagementPageOverlay 
            xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">

            <!-- Your layout -->
        </engagement:EngagementPageOverlay>

Em seguida, no seu ficheiro CS marcar a página no `EngagementPageOverlay` em vez de `EngagementPage` e importar `Microsoft.Azure.Engagement.Overlay`.

            using Microsoft.Azure.Engagement.Overlay;

* Substitua `EngagementPage` com `EngagementPageOverlay`:

**Com EngagementPage:**

            using Microsoft.Azure.Engagement;

            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPage
              {
                [...]
              }
            }

**Com EngagementPageOverlay:**

            using Microsoft.Azure.Engagement.Overlay;

            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPageOverlay 
              {
                [...]
              }
            }


Sobreposição o Engagement adiciona um `Grid` elemento na parte superior da página é composto por dois e a esquema `WebView` elementos um para a faixa e a outros uma para a vista interstitial.

Pode personalizar os elementos de sobreposição diretamente no `EngagementPageOverlay.cs` ficheiro.

### <a name="web-views-manual-integration"></a>Integração de manual de vistas Web
Será possível pesquisar alcance as páginas para as duas `WebView` elementos responsáveis por apresentar a faixa e a vista interstitial. A única coisa que precisa fazer consiste em adicionar esses dois `WebView` elementos algures no seu páginas, este é um exemplo:

    <Grid x:Name="engagementGrid">

      <!-- Your layout -->

      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Stretch" VerticalAlignment="Top"/>
      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed"  HorizontalAlignment="Stretch"  VerticalAlignment="Stretch"/>
    </Grid>


Neste exemplo de `WebView` elementos são transferidos para ajustar o respetivo contentor que automaticamente tamanhos-las novamente em caso de alteração de tamanho de rotação ou janela do ecrã.

> [!WARNING]
> É importante manter a mesma nomenclatura `engagement_notification_content` e `engagement_announcement_content` para o `WebView` elementos. Alcance está a identificá-las pelo respetivo nome. 
> 
> 

## <a name="handle-datapush-optional"></a>Processar datapush (opcional)
Se pretender que a aplicação para poder receber alcance pushes de dados, terá de implementar dois eventos da classe EngagementReach:

No App.xaml.cs no construtor App() adicione:

            EngagementReach.Instance.DataPushStringReceived += (body) =>
            {
              Debug.WriteLine("String data push message received: " + body);
              return true;
            };

            EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
            {
              Debug.WriteLine("Base64 data push message received: " + encodedBody);
              // Do something useful with decodedBody like updating an image view
              return true;
            };

Pode ver que a chamada de retorno de cada método devolve um valor booleano. Engagement envia um comentários para o back-end após a emissão de push de dados. Se a chamada de retorno devolve false, o `exit` comentários serão envio. Caso contrário, será `action`. Se nenhuma chamada de retorno está definida para os eventos, o `drop` comentários vai ser devolvido para o Engagement.

> [!WARNING]
> Engagement não é capaz de receber comentário múltiplos para um push de dados. Se pretender definir vários processadores por um evento, lembre-se de que os comentários serão a corresponder para a última enviada uma. Neste caso, é recomendável devolve sempre o mesmo valor para evitar que confuso comentários no front-end.
> 
> 

## <a name="customize-ui-optional"></a>Personalizar IU (opcional)
### <a name="first-step"></a>Primeiro passo
Iremos permitem-lhe personalizar o alcance da IU.

Para tal, tem de criar uma subclasse do `EngagementReachHandler` classe.

**Código de exemplo:**

            using Microsoft.Azure.Engagement;

            namespace Example
            {
              internal class ExampleReachHandler : EngagementReachHandler
              {
               // Override EngagementReachHandler methods depending on your needs
              }
            }

Em seguida, definir o conteúdo do `EngagementReach.Instance.Handler` campo com o seu objeto personalizado no sua `App.xaml.cs` classe dentro o `App()` método.

**Código de exemplo:**

            protected override void OnLaunched(LaunchActivatedEventArgs args)
            {
              // your app initialization 
              EngagementReach.Instance.Handler = new ExampleReachHandler();
              // Engagement Agent and Reach initialization
            }

> [!NOTE]
> Por predefinição, o Engagement utiliza a suas próprias implementação `EngagementReachHandler`.
> Não tem de criar os seus próprios, e se o fizer, não tem de substituir cada método. É o comportamento predefinido para selecionar o objeto de base do Engagement.
> 
> 

### <a name="web-view"></a>Vista Web
Por predefinição, o alcance irá utilizar os recursos incorporados de DLL para apresentar as notificações e páginas.

Para fornecer uma personalização completa possibilidades utilizamos apenas vista web. Se pretender personalizar os esquemas, diretamente substituir os ficheiros de recursos `EngagementAnnouncement.html` e `EngagementNotification.html`. Tem de envolvimento todo o código no `<body></body>` seja corretamente executado. Mas pode adicionar tag externa `engagement_webview_area`.

No entanto, pode optar por utilizar os seus próprios recursos.

Pode substituir `EngagementReachHandler` métodos no seu subclasse para saber se o Engagement ao utilizar as esquemas, mas asseguramos para incorporado mecanismo o engagement:

**Código de exemplo:**

            // In your subclass of EngagementReachHandler

            public override string GetAnnouncementHTML()
            {
              return base.GetAnnouncementHTML();
            }
            public override string GetAnnouncementName()
            {
              return base.GetAnnouncementName();
            }
            public override string GetNotfificationHTML()
            {
              return base.GetNotfificationHTML();
            }
            public override string GetNotfificationName()
            {
              return base.GetNotfificationName();
            }


Por predefinição, é AnnouncementHTML `ms-appx-web:///Resources/EngagementAnnouncement.html`. Representa o ficheiro html que o conteúdo de uma mensagem push (anúncio de texto, Web anoucement e anúncio de consulta) de design. É AnnouncementName `engagement_announcement_content`. É o nome do design webview na sua página de xaml.

É NotfificationHTML `ms-appx-web:///Resources/EngagementNotification.html`. Representa o ficheiro html que criar a notificação de uma mensagem de push. É NotfificationName `engagement_notification_content`. É o nome do design webview na sua página de xaml.

### <a name="customization"></a>Personalização
Pode personalizar a notificação e anúncio de vista web tem que pretende se preservar o objeto do Engagement. Tenha atenção esse webview objeto descrito três vezes - pela primeira vez no seu xaml, a segunda vez no seu ficheiro CS no método "setwebview()" e a terceira hora no ficheiro html.

* No seu xaml descrevem o componente de webview gráfica de esquema atual.
* No seu ficheiro CS pode definir "setwebview()" em que definiu a dimensão de webview dois (notificação, anúncio). É muito eficaz quando a aplicação é redimensionamento.
* No ficheiro de html Engagement dita o conteúdo de webview, design e as posições de elementos entre si.

### <a name="launch-message"></a>Inicie a mensagem
Quando um utilizador clica numa notificação do sistema (um alerta), o Engagement inicia a aplicação, carregar o conteúdo das mensagens push e apresentar a página da campanha correspondente.

Não há um atraso entre a iniciação da aplicação e a apresentação da página (consoante a velocidade da sua rede).

Para indicar ao utilizador que algo está a carregar, deve fornecer informações de um visual, como uma barra de progresso ou um indicador de progresso. Engagement não consegue processar que ela própria, mas fornece alguns processadores para si.

Para implementar a chamada de retorno, no App.xaml.cs na "App() pública {}" Adicione:

            /* The application has launched and the content is loading.
             * You should display an indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };

            /* The application has finished loading the content and the page
             * is about to be displayed.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };

            /* The content has been loaded, but an error has occurred.
             * You can provide an information to the user.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

Pode definir a chamada de retorno no seu método de "App() pública {}" do seu `App.xaml.cs` , preferencialmente, antes de ficheiros a `EngagementReach.Instance.Init()` chamada.

> [!TIP]
> Cada processador é chamado pelo Thread de IU. Não é necessário se preocupe quando utilizar uma MessageBox ou algo relacionadas com a IU.
> 
> 

## <a id="push-channel-sharing"></a>Partilha de canal de push
Se estiver a utilizar notificações push para outra finalidade na sua aplicação, em seguida, tem de utilizar o canal de push partilha funcionalidade do SDK do Engagement. Isto é evitar push em falta.

* Pode fornecer a suas próprias canal de push para a inicialização de alcance do Engagement. O SDK utilizá-lo em vez de solicitar um novo.

Atualizar a inicialização do Engagement alcançar com o canal de push no `InitEngagement` método o `App.xaml.cs` ficheiro:

    /* Your own push channel logic... */
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    /*...Engagement initialization */
    EngagementAgent.Instance.Init(e);
    EngagementReach.Instance.Init(e,pushChannel);

* Em alternativa, se pretender consumir o canal de push após a inicialização de alcance, em seguida, que pode definir uma chamada de retorno no alcance do Engagement para obter uma vez o canal de push é criado pelo SDK.

Definir a chamada de retorno em qualquer lugar **depois** a inicialização de alcance:

    /* Set action on the SDK push channel. */
    EngagementReach.Instance.SetActionOnPushChannel((PushNotificationChannel channel) => 
    {
      /* The forwarded channel can be null if its creation fails for any reason. */
      if (channel != null)
      {
        /* Your own push channel logic... */
      });
    }

## <a name="custom-scheme-tip"></a>Sugestão de esquema personalizado
Podemos fornecer a utilização de esquema personalizado. Pode enviar um tipo diferente do URI de front-end de envolvimento a ser utilizado na sua aplicação do engagement. Esquema predefinido como `http, ftp, ...` são gerir pelo Windows, uma janela irá perguntar, se nenhuma aplicação predefinida instalada no dispositivo. Também pode criar o seu próprio esquema personalizado para a sua aplicação.

A forma simple para definir um esquema personalizado na sua aplicação consiste em abrir o `Package.appxmanifest` aceda no `Declarations` painel. Selecione `Protocol` em declarações de disponíveis se deslocar para a caixa e adicioná-la. Editar o `Name` campo com o novo protocolo pretendido nome.

Agora para utilizar este protocolo, edite o `App.xaml.cs` com o `OnActivated` método e não se esqueça de inicializar o engagement aqui também:

            /// <summary>
            /// Enter point when app his called by another way than user click
            /// </summary>
            /// <param name="args">Activation args</param>
            protected override void OnActivated(IActivatedEventArgs args)
            {
              /* Init engagement like it was launch by a custom uri scheme */
              EngagementAgent.Instance.Init(args);
              EngagementReach.Instance.Init(args);

              //TODO design action to do when app is launch

              #region Custom scheme use
              if (args.Kind == ActivationKind.Protocol)
              {
                ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;

                if (myProtocol.Uri.Scheme.Equals("protocolName"))
                {
                  string path = myProtocol.Uri.AbsolutePath;
                }
              }
              #endregion

