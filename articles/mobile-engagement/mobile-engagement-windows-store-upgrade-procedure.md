---
title: "Procedimentos de atualização de aplicações universais do Windows SDK"
description: "Procedimentos de atualização Windows Universal SDK de aplicações do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 4c898175-2cd6-43db-b350-bb408332f24d
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: fe85a99a92fb39082cafe7422b356de1f20f14bd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="windows-universal-apps-sdk-upgrade-procedures"></a>Procedimentos de atualização de aplicações universais do Windows SDK
Se já tiver integrado uma versão antiga do Engagement na sua aplicação, terá de considerar os seguintes pontos ao atualizar o SDK.

Terá de seguir vários procedimentos, se omitido várias versões do SDK. Por exemplo, se a migração do 0.10.1 para 0.11.0 que tem de primeiro siga o procedimento "de 0.9.0 para 0.10.1", em seguida, o procedimento "de 0.10.1 para 0.11.0".

## <a name="from-330-to-340"></a>De 3.3.0 para 3.4.0
### <a name="test-logs"></a>Registos do teste
Os registos de consola produzidos pelo SDK podem agora ser ativado/desativado/filtrados. Para personalizar esta, atualize a propriedade `EngagementAgent.Instance.TestLogEnabled` para um valor disponível no `EngagementTestLogLevel` enumeração, para a instância:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="resources"></a>Recursos
A sobreposição de alcance foi melhorada. Faz parte dos recursos de pacote NuGet do SDK.

Enquanto a atualização para a nova versão do SDK, pode escolher se pretende manter os ficheiros existentes na pasta sobreposição dos seus recursos ou não:

* Se a sobreposição anterior está a funcionar para si ou está a integrar o `WebView` elementos manualmente, em seguida, pode optar por manter os ficheiros exiting, irá continuem a funcionar. 
* Se pretender atualizar para a novo sobreposição, substitua apenas totalidade `overlay` pasta dos seus recursos pelo novo no pacote do SDK (aplicações UWP: após a atualização, pode aproveitar a nova pasta de sobreposição % USERPROFILE %\\.nuget\packages\ MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [!WARNING]
> Utilizar a sobreposição de nova irá substituir quaisquer personalizações efetuadas na versão anterior.
> 
> 

## <a name="from-320-to-330"></a>De 3.2.0 para 3.3.0
### <a name="resources"></a>Recursos
Este passo seja relativo apenas recursos personalizados. Se tiver personalizado os recursos fornecidos pelo SDK (html, imagens, sobreposição), em seguida, terá de cópia de segurança-los antes de atualizar e voltar a sua personalização em recursos atualizados.

## <a name="from-310-to-320"></a>De 3.1.0 para 3.2.0
### <a name="resources"></a>Recursos
Este passo seja relativo apenas recursos personalizados. Se tiver personalizado os recursos fornecidos pelo SDK (html, imagens, sobreposição), em seguida, terá de cópia de segurança-los antes de atualizar e voltar a sua personalização em recursos atualizados.

### <a name="webview-integration"></a>Integração de WebView
Alguns melhoramentos para corresponder fatores de forma diferente do dispositivo foram introduzidos nesta versão. Certifique-se de que a integração do webview corresponde o seguinte:

Na sua (de) para a página XAML:

            <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
            <WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

E no seu ficheiro CS associado:

    using Microsoft.Azure.Engagement;
    using System;
    using Windows.ApplicationModel.Core;
    using Windows.UI.ViewManagement;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Navigation;

    namespace My.Namespace.Example
    {
            /// <summary>
            /// An empty page that can be used on its own or navigated to within a Frame.
            /// </summary>
            public sealed partial class ExampleEngagementReachPage : EngagementPage
            {
              public ExampleEngagementReachPage()
              {
                this.InitializeComponent();

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

              #region to implement
              /* Attach events when page is navigated. */
              protected override void OnNavigatedTo(NavigationEventArgs e)
              {
                /* Update the webview when the app window is resized. */
                Window.Current.SizeChanged += DisplayProperties_OrientationChanged;

                /* Update the webview when the app/status bar is resized. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged += DisplayProperties_VisibleBoundsChanged; 
    #endif
                base.OnNavigatedTo(e);
              }

              /* When page is left ensure to detach SizeChanged handler. */
              protected override void OnNavigatedFrom(NavigationEventArgs e)
              {
                Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged -= DisplayProperties_VisibleBoundsChanged;
    #endif
                base.OnNavigatedFrom(e);
              }

              /* "width" and "height" are the current size of your application display. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
              double width = ApplicationView.GetForCurrentView().VisibleBounds.Width;
              double height = ApplicationView.GetForCurrentView().VisibleBounds.Height;
    #else
              double width =  Window.Current.Bounds.Width;
              double height =  Window.Current.Bounds.Height;
    #endif

              /// <summary>
              /// Set your webview elements to the correct size.
              /// </summary>
              /// <param name="width">The width of your current display.</param>
              /// <param name="height">The height of your current display.</param>
              private void SetWebView(double width, double height)
              {
                #pragma warning disable 4014
                CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
                        () =>
                        {
                          this.engagement_notification_content.Width = width;
                          this.engagement_announcement_content.Width = width;
                          this.engagement_announcement_content.Height = height;
                        });
              }

              /// <summary>
              /// Handler that takes the Windows.Current.SizeChanged and indicates that webviews have to be resized.
              /// </summary>
              /// <param name="sender">Original event trigger.</param>
              /// <param name="e">Window Size Changed Event arguments.</param>
              private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
              {
                double width = e.Size.Width;
                double height = e.Size.Height;

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

    #if WINDOWS_PHONE_APP || WINDOWS_UWP              
              /// <summary>
              /// Handler that takes the ApplicationView.VisibleBoundsChanged and indicates that webviews have to be resized
              /// </summary>
              /// <param name="sender">The related application view.</param>
              /// <param name="e">Related event arguments.</param>
              private void DisplayProperties_VisibleBoundsChanged(ApplicationView sender, Object e)
              {
                double width = sender.VisibleBounds.Width;
                double height = sender.VisibleBounds.Height;

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
    #endif
              #endregion
            }
    }

## <a name="from-200-to-300"></a>De 2.0.0 para 3.0.0
### <a name="resources"></a>Recursos
Este passo seja relativo apenas recursos personalizados. Se tiver personalizado os recursos fornecidos pelo SDK (html, imagens, sobreposição), em seguida, terá de cópia de segurança-los antes de atualizar e voltar a sua personalização em recursos atualizados.

## <a name="from-111-to-200"></a>De 1.1.1 para 2.0.0
O seguinte descreve como migrar uma integração do SDK do serviço Capptain oferecido pelo Capptain SAS numa aplicação ligada ao Azure Mobile Engagement. 

> [!IMPORTANT]
> Capptain e Mobile Engagement não são os mesmos serviços e o procedimento indicado abaixo apenas realça como migrar a aplicação de cliente. Migrar o SDK na aplicação de mensagens em fila não de irá migrar os dados saídos de servidores Capptain para os servidores de Mobile Engagement
> 
> 

Se estiver a migrar de uma versão anterior, consulte o web site do Capptain migrar para 1.1.1 pela primeira vez, em seguida, aplique o procedimento seguinte

### <a name="nuget-package"></a>Pacote Nuget
Substitua **Capptain.WindowsPhone** por **microsoftazure. Mobileengagement** pacote Nuget.

### <a name="applying-mobile-engagement"></a>Aplicação de Mobile Engagement
O SDK utiliza o termo `Engagement`. Tem de atualizar o seu projeto para fazer corresponder esta alteração.

Tem de desinstalar o pacote atual do nuget Capptain. Considere-se de que todas as alterações na pasta Capptain recursos serão removidas. Se pretender manter esses ficheiros, em seguida, faça uma cópia dos mesmos.

Depois disso, instale o novo pacote nuget Microsoft Azure Engagement no seu projeto. Pode encontrá-lo diretamente no [nuget site]. ou o índice aqui. Esta ação substitui todos os ficheiros de recursos utilizados pelo Engagement e adiciona a DLL do Engagement novo para as referências do projeto.

Terá de limpar as referências do projeto, eliminando Capptain DLL referências. Se não fizer isto, a versão do Capptain entram em conflito e acontecerá de erros.

Se tiver personalizado Capptain recursos, copie o conteúdo de ficheiros antigo e cole-os no novos ficheiros do Engagement. Tenha em atenção que os ficheiros xaml e cs tem de ser atualizadas.

Quando esses passos são concluídos só tem de substituir as referências antigas do Capptain por novas referências de envolvimento.

1. Todos os espaços de nomes Capptain tem de ser atualizadas.
   
    Antes da migração:
   
        using Capptain.Agent;
        using Capptain.Reach;
   
    Após a migração:
   
        using Microsoft.Azure.Engagement;
2. Todas as classes de Capptain que contêm "Capptain" devem conter ". o Engagement".
   
    Antes da migração:
   
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
   
    Após a migração:
   
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }
3. Para os ficheiros xaml Capptain espaço de nomes e atributos também alterar.
   
    Antes da migração:
   
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
   
    Após a migração:
   
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>
4. Alterações de página de sobreposição
   
   > [!IMPORTANT]
   > Também altera sobreposição. O novo espaço de nomes é `Microsoft.Azure.Engagement.Overlay`. Tem de ser usada em ficheiros xaml e o cs. Além `CapptainGrid` está a ser chamado `EngagementGrid`, `capptain_notification_content` e `capptain_announcement_content` são denominados `engagement_notification_content` e `engagement_announcement_content`.
   > 
   > 
   
    Para sobreposição:
   
        <capptain:CapptainPageOverlay
          xmlns:capptain="using:Capptain.Overlay"
          ...
        </capptain:CapptainPageOverlay>
   
    Torna-se:
   
        <EngagementPageOverlay
          engagement="using:Microsoft.Azure.Engagement.Overlay"
          ...
        </engagement:EngagementPageOverlay>
5. Para outros recursos, como imagens Capptain e ficheiros HTML, tenha em atenção que estes também cujo nome foi mudados para utilizar "Envolvimento".

### <a name="project-declaration"></a>Declaração de projeto
No Package. appxmanifest `File Type Associations` tiver sido atualizado a partir de:

* capptain\_alcançar\_conteúdo para o engagement\_alcançar\_conteúdo
* capptain\_registo\_ficheiro para o engagement\_registo\_ficheiro

### <a name="application-id--sdk-key"></a>ID da aplicação / chave do SDK
Utiliza o engagement uma cadeia de ligação. Não tem de especificar um ID de aplicação e uma chave SDK Mobile engagement, só tem de especificar uma cadeia de ligação. Pode configurá-lo no seu ficheiro EngagementConfiguration.

A configuração de envolvimento pode ser definida em seu `Resources\EngagementConfiguration.xml` ficheiro do seu projeto.

Edite este ficheiro para especificar:

* A cadeia de ligação de aplicação entre as etiquetas `<connectionString>` e `<\connectionString>`.

Se pretender especificá-la no tempo de execução, pode chamar o método seguinte antes da inicialização de agente o Engagement:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(args, engagementConfiguration);

A cadeia de ligação para a sua aplicação é apresentada no Portal clássico do Azure.

### <a name="items-name-change"></a>Alteração de nome de itens
Todos os itens com o nome *capptain* ter o nome *engagement*. Da mesma forma para *Capptain* para *Engagement*.

Exemplos de itens de Capptain frequentemente utilizadas:

* CapptainConfiguration agora o nome EngagementConfiguration
* CapptainAgent agora o nome EngagementAgent
* CapptainReach agora o nome EngagementReach
* CapptainHttpConfig agora o nome EngagementHttpConfig
* GetCapptainPageName agora o nome GetEngagementPageName

Tenha em atenção que mudar o nome também afeta substituído métodos.

