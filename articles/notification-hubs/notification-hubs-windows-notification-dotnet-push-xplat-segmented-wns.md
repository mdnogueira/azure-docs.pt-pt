---
title: "Utilizar Notification Hubs do Azure para enviar notícias de última hora (plataforma Universal do Windows)"
description: "Utilize Notification Hubs do Azure com etiquetas no registo para enviar notícias de última hora para uma aplicação plataforma Universal do Windows."
services: notification-hubs
documentationcenter: windows
author: ysxu
manager: erikre
editor: 
ms.assetid: 994d2eed-f62e-433c-bf65-4afebf1c0561
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: d510e7e665adec9607aeee80802c466b363d5d5b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-notification-hubs-to-send-breaking-news"></a>Utilizar Notification Hubs para enviar notícias de última hora
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Descrição geral
Este tópico mostra como utilizar Notification Hubs do Azure para difundir notificações de notícias de última para uma loja Windows ou a aplicação do Windows Phone 8.1 (não Silverlight). Se tiver como objetivo o Windows Phone 8.1 Silverlight, consulte o [Windows Phone](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) versão. 

Depois de concluir este processo, pode registar para as categorias de notícias de última que está interessado em e receberá notificações push para apenas nessas categorias. Este cenário é comum para muitas aplicações (por exemplo, leitores RSS ou aplicações para ventoinhas de música) onde devem ser enviadas notificações aos grupos de utilizadores que tenham declarado interesse nos mesmos. 

Pode ativar cenários de difusão, incluindo um ou mais *etiquetas* quando cria um registo no hub de notificação. Quando as notificações são enviadas para uma etiqueta, todos os dispositivos que registou a etiqueta de recebem a notificação. Uma vez que as etiquetas são simplesmente cadeias, tem de ser configurado com antecedência. Para obter mais informações sobre etiquetas, consulte [expressões de encaminhamento e a etiqueta de Notification Hubs](notification-hubs-tags-segment-push-message.md).

> [!NOTE]
> Versões de projeto loja Windows e Windows Phone 8.1 e anteriores não são suportadas no Visual Studio 2017. Para obter mais informações, veja [Visual Studio 2017 Platform Targeting and Compatibility](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs) (Segmentação e Compatibilidade de Plataformas do Visual Studio 2017) 

## <a name="prerequisites"></a>Pré-requisitos
Este tópico baseia-se a aplicação que criou no [introdução aos Hubs de notificação][get-started]. Antes de começar este tutorial, tem de concluir [introdução aos Hubs de notificação][get-started].

## <a name="add-category-selection-to-the-app"></a>Adicionar a seleção da categoria para a aplicação
O primeiro passo consiste em Adicionar elementos de IU para a sua página principal existente, para que os utilizadores podem selecionar categorias para registar. As categorias selecionadas são armazenadas no dispositivo. Quando a aplicação for iniciada, é criado um registo de dispositivos no seu hub de notificação, com as categorias selecionadas como etiquetas.

1. Abra o ficheiro de projeto MainPage.xaml e, em seguida, copie o seguinte código a **grelha** elemento:
   
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition/>
                <ColumnDefinition/>
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
        </Grid>

2. Clique com botão direito a **partilhados** projeto, adicione uma nova classe com o nome **notificações**, adicionar o **pública** modificador à definição de classe e, em seguida, adicione o seguinte **utilizando** instruções para o novo ficheiro de código:
   
        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.Storage;
        using System.Threading.Tasks;

3. Copie o seguinte código para o novo **notificações** classe:
   
        private NotificationHub hub;
   
        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }
   
        public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            return await SubscribeToCategories(categories);
        }
   
        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
            return categories != null ? categories.Split(','): new string[0];
        }
   
        public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
   
            if (categories == null)
            {
                categories = RetrieveCategories();
            }
   
            // Using a template registration to support notifications across platforms.
            // Any template notifications that contain messageParam and a corresponding tag expression
            // will be delivered for this registration.
   
            const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";
   
            return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
                    categories);
        }
   
    Esta classe utiliza o armazenamento local para armazenar as categorias de notícias que este dispositivo tem de receber. Em vez de chamar o *RegisterNativeAsync* método, chamada *RegisterTemplateAsync* para se registar para as categorias através da utilização de um registo de modelo. 
   
    Porque poderá pretender registar mais do que um modelo (por exemplo, um para notificações de alerta) e outro para mosaicos, fornecem também um nome de modelo (por exemplo, "simpleWNSTemplateExample"). Nome os modelos para que possa atualizar ou eliminá-los.
   
    >[!NOTE]
    >Se um dispositivo regista vários modelos com a mesma etiqueta, uma mensagem a receber que tenha como alvo a tag faz com que várias notificações ser entregue no dispositivo (um para cada modelo). Este comportamento é útil quando a mesma mensagem lógica têm de resultar em várias notificações visual (por exemplo, que mostra um destaque e um alerta numa aplicação loja Windows).
   
    Para obter mais informações, consulte [modelos](notification-hubs-templates-cross-platform-push-messages.md).

4. No ficheiro App.xaml.cs, adicione a seguinte propriedade para o **aplicação** classe:
   
        public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
   
    Utilize esta propriedade para criar e aceder a um **notificações** instância.
   
    No código, substitua o `<hub name>` e `<connection string with listen access>` marcadores de posição pelo nome do seu hub de notificação e a cadeia de ligação para *DefaultListenSharedAccessSignature*, que obteve anteriormente.
   
   > [!NOTE]
   > Porque as credenciais que são distribuídas com uma aplicação de cliente não são normalmente seguras, distribuir apenas a chave para *escutar* acesso com a sua aplicação de cliente. Com acesso de escuta, pode registar a aplicação para notificações, mas não podem ser modificados registos existentes e não não possível enviar notificações. A chave de acesso total é utilizada num serviço de back-end seguro para o envio de notificações e alterar os registos existentes.
   > 
   > 
5. No ficheiro de projeto MainPage.xaml.cs, adicione a seguinte linha:
   
        using Windows.UI.Popups;

6. No ficheiro de projeto MainPage.xaml.cs, adicione o método seguinte:
   
        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");
   
            var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);
   
            var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
   
    Este método cria uma lista de categorias e utiliza o **notificações** classe para armazenar a lista no armazenamento local. Também regista as etiquetas correspondentes com o notification hub. Quando as categorias são alteradas, o registo é recriado com as categorias de novo.

A aplicação agora pode armazenar um conjunto de categorias no armazenamento local no dispositivo. A aplicação regista com notification hub sempre que os utilizadores alterar a seleção da categoria.

## <a name="register-for-notifications"></a>Registar-se as notificações
Nesta secção, registar o notification hub no arranque utilizando as categorias que que tenha armazenado no armazenamento local.

> [!NOTE]
> Porque o URI que está atribuída pelo serviço de notificação do Windows (WNS) do canal pode alterar a qualquer momento, deve registar para notificações frequentemente evitar falhas de notificação. Neste exemplo regista a notificação sempre que a aplicação for iniciada. Para aplicações que são executados com frequência (mais do que uma vez por dia), provavelmente, pode ignorar o registo para preservar a largura de banda, se menos de um dia foi efectuada com êxito desde o registo anterior.
> 
> 

1. Para utilizar o `notifications` classe subscrever com base em categorias, abra o ficheiro App.xaml.cs e, em seguida, atualize o **InitNotificationsAsync** método.
   
        // *** Remove or comment out these lines *** 
        //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
        //var hub = new NotificationHub("your hub name", "your listen connection string");
        //var result = await hub.RegisterNativeAsync(channel.Uri);
   
        var result = await notifications.SubscribeToCategories();
   
    Este processo garante que quando a aplicação for iniciada, obtém as categorias de armazenamento local e os pedidos de registo destas categorias. Criou o **InitNotificationsAsync** método como parte do [introdução aos Hubs de notificação] [ get-started] tutorial.

2. No ficheiro de projeto MainPage.xaml.cs, adicione o seguinte código para o *OnNavigatedTo* método:
   
        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            var categories = ((App)Application.Current).notifications.RetrieveCategories();
   
            if (categories.Contains("World")) WorldToggle.IsOn = true;
            if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
            if (categories.Contains("Business")) BusinessToggle.IsOn = true;
            if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
            if (categories.Contains("Science")) ScienceToggle.IsOn = true;
            if (categories.Contains("Sports")) SportsToggle.IsOn = true;
        }
   
    Este código atualiza a página principal, com base no estado das categorias guardados anteriormente.

A aplicação está agora concluída. -Pode armazenar um conjunto de categorias no armazenamento local do dispositivo que é utilizado para registar com o hub de notificação quando os utilizadores alterar a seleção da categoria. Na secção seguinte, é possível definir um back end que pode enviar notificações de categoria para esta aplicação.

## <a name="send-tagged-notifications"></a>Enviar notificações marcadas
[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-app-and-generate-notifications"></a>Executar a aplicação e gerar notificações
1. No Visual Studio, selecione **F5** para compilar e iniciar a aplicação.  
    A IU da aplicação fornece um conjunto de mudanças de modo email que lhe permite escolher as categorias para subscrever. 
   
    ![Aplicação de notícias de última hora][1]

2. Ativar uma ou mais categoria mudanças de modo email e, em seguida, clique em **subscrever**.
   
    A aplicação converte as categorias selecionadas etiquetas e os pedidos de um novo registo de dispositivo para as etiquetas selecionados do hub de notificação. As categorias registadas são devolvidas e apresentadas numa caixa de diálogo.
   
    ![Mudanças de modo email categoria e subscrever botão][19]

3. Envie uma nova notificação de back-end de uma das seguintes formas:

   * **Aplicação de consola**: iniciar a aplicação de consola.
   * **Java/PHP**: executar a sua aplicação ou script.
     
     As notificações para as categorias selecionadas são apresentados como notificações de alerta.
     
     ![Notificações de alerta][14]

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a difusão notícias de última hora por categoria. Tenha em consideração quando concluir o tutorial seguinte, que realça outro cenário avançado de Hubs de notificação:

* [Utilizar Notification Hubs para difusão notícias de última localizada hora] este tutorial descreve como expandir a aplicação de notícias de última para ativar o envio de notificações localizadas.

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png

[14]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png


[19]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png

<!-- URLs.-->
[get-started]: /azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification
[Utilizar Notification Hubs para difusão notícias de última localizada hora]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
