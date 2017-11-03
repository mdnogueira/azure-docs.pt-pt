---
title: "Utilizar Notification Hubs para enviar notícias de última hora (Windows Phone)"
description: "Utilize Notification Hubs do Azure para utilizar tag registos para enviar notícias de última hora para uma aplicação do Windows Phone."
services: notification-hubs
documentationcenter: windows
author: ysxu
manager: erikre
editor: 
ms.assetid: 42726bf5-cc82-438d-9eaa-238da3322d80
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 3a6a69bf555c7267d3fbeb03ff6c03054991960f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-notification-hubs-to-send-breaking-news"></a>Utilizar Notification Hubs para enviar notícias de última hora
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Descrição geral
Este tópico mostra como utilizar Notification Hubs do Azure para difundir notificações de notícias de última para uma aplicação Windows Phone 8.0/8.1 Silverlight. Se estiver a filtrar a loja Windows ou a aplicação do Windows Phone 8.1, consulte ao [Windows Universal](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md) versão. Quando terminar, irá conseguir registar-se de que está interessado de categorias de notícias de última hora e receber notificações de push apenas para as categorias. Este cenário é um padrão comum para muitas aplicações onde notificações devem ser enviadas para grupos de utilizadores que tenham sido anteriormente declarado interesse nos mesmos, por exemplo, leitor de RSS, as aplicações para ventoinhas de música, etc.

Cenários de difusão estão ativados, incluindo um ou mais *etiquetas* quando criar um registo no hub de notificação. Quando as notificações são enviadas para uma etiqueta, todos os dispositivos que registou da etiqueta irão receber a notificação. Uma vez que as etiquetas são simplesmente cadeias, tem de ser aprovisionados com antecedência. Para obter mais informações sobre etiquetas, consulte [encaminhamento de Hubs de notificação e expressões de etiqueta](notification-hubs-tags-segment-push-message.md).

## <a name="prerequisites"></a>Pré-requisitos
Este tópico baseia-se a aplicação que criou no [introdução aos Hubs de notificação]. Antes de começar este tutorial, tem de ter já concluído [introdução aos Hubs de notificação].

## <a name="add-category-selection-to-the-app"></a>Adicionar a seleção da categoria para a aplicação
O primeiro passo consiste em adicionar os elementos de IU para a sua página principal existente que permitem ao utilizador selecionar categorias para registar. As categorias selecionadas por um utilizador são armazenadas no dispositivo. Quando a aplicação for iniciada, um registo do dispositivo é criado no notification hub com as categorias selecionadas como etiquetas.

1. Abra o ficheiro de projeto MainPage.xaml, em seguida, substitua o **grelha** elementos com o nome `TitlePanel` e `ContentPanel` com o seguinte código:
   
        <StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
            <TextBlock Text="Breaking News" Style="{StaticResource PhoneTextNormalStyle}" Margin="12,0"/>
            <TextBlock Text="Categories" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
        </StackPanel>
   
        <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
            <Grid.RowDefinitions>
                <RowDefinition Height="auto"/>
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition />
                <ColumnDefinition />
            </Grid.ColumnDefinitions>
            <CheckBox Name="WorldCheckBox" Grid.Row="0" Grid.Column="0">World</CheckBox>
            <CheckBox Name="PoliticsCheckBox" Grid.Row="1" Grid.Column="0">Politics</CheckBox>
            <CheckBox Name="BusinessCheckBox" Grid.Row="2" Grid.Column="0">Business</CheckBox>
            <CheckBox Name="TechnologyCheckBox" Grid.Row="0" Grid.Column="1">Technology</CheckBox>
            <CheckBox Name="ScienceCheckBox" Grid.Row="1" Grid.Column="1">Science</CheckBox>
            <CheckBox Name="SportsCheckBox" Grid.Row="2" Grid.Column="1">Sports</CheckBox>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="3" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
        </Grid>
2. No projeto, crie uma nova classe com o nome **notificações**, adicione o **pública** modificador à definição de classe, em seguida, adicione o seguinte **utilizando** instruções para o novo ficheiro de código :
   
        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
        using System.IO.IsolatedStorage;
        using System.Windows;
3. Copie o seguinte código para o novo **notificações** classe:
   
        private NotificationHub hub;
   
        // Registration task to complete registration in the ChannelUriUpdated event handler
        private TaskCompletionSource<Registration> registrationTask;
   
        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }
   
        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
            return categories != null ? categories.Split(',') : new string[0];
        }
   
        public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            var categoriesAsString = string.Join(",", categories);
            var settings = IsolatedStorageSettings.ApplicationSettings;
            if (!settings.Contains("categories"))
            {
                settings.Add("categories", categoriesAsString);
            }
            else
            {
                settings["categories"] = categoriesAsString;
            }
            settings.Save();
   
            return await SubscribeToCategories();
        }
   
        public async Task<Registration> SubscribeToCategories()
        {
            registrationTask = new TaskCompletionSource<Registration>();
   
            var channel = HttpNotificationChannel.Find("MyPushChannel");
   
            if (channel == null)
            {
                channel = new HttpNotificationChannel("MyPushChannel");
                channel.Open();
                channel.BindToShellToast();
                channel.ChannelUriUpdated += channel_ChannelUriUpdated;
   
                // This is optional, used to receive notifications while the app is running.
                channel.ShellToastNotificationReceived += channel_ShellToastNotificationReceived;
            }
   
            // If channel.ChannelUri is not null, we will complete the registrationTask here.  
            // If it is null, the registrationTask will be completed in the ChannelUriUpdated event handler.
            if (channel.ChannelUri != null)
            {
                await RegisterTemplate(channel.ChannelUri);
            }
   
            return await registrationTask.Task;
        }
   
        async void channel_ChannelUriUpdated(object sender, NotificationChannelUriEventArgs e)
        {
            await RegisterTemplate(e.ChannelUri);
        }
   
        async Task<Registration> RegisterTemplate(Uri channelUri)
        {
            // Using a template registration to support notifications across platforms.
            // Any template notifications that contain messageParam and a corresponding tag expression
            // will be delivered for this registration.
   
            const string templateBodyMPNS = "<wp:Notification xmlns:wp=\"WPNotification\">" +
                                                "<wp:Toast>" +
                                                    "<wp:Text1>$(messageParam)</wp:Text1>" +
                                                "</wp:Toast>" +
                                            "</wp:Notification>";
   
            // The stored categories tags are passed with the template registration.
   
            registrationTask.SetResult(await hub.RegisterTemplateAsync(channelUri.ToString(), 
                templateBodyMPNS, "simpleMPNSTemplateExample", this.RetrieveCategories()));
   
            return await registrationTask.Task;
        }
   
        // This is optional. It is used to receive notifications while the app is running.
        void channel_ShellToastNotificationReceived(object sender, NotificationEventArgs e)
        {
            StringBuilder message = new StringBuilder();
            string relativeUri = string.Empty;
   
            message.AppendFormat("Received Toast {0}:\n", DateTime.Now.ToShortTimeString());
   
            // Parse out the information that was part of the message.
            foreach (string key in e.Collection.Keys)
            {
                message.AppendFormat("{0}: {1}\n", key, e.Collection[key]);
   
                if (string.Compare(
                    key,
                    "wp:Param",
                    System.Globalization.CultureInfo.InvariantCulture,
                    System.Globalization.CompareOptions.IgnoreCase) == 0)
                {
                    relativeUri = e.Collection[key];
                }
            }
   
            // Display a dialog of all the fields in the toast.
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() => 
            { 
                MessageBox.Show(message.ToString()); 
            });
        }

    Esta classe utiliza o armazenamento isolado, para armazenar as categorias de notícias que este dispositivo está a receber. Também contém métodos para se registar para estas categorias utilizando um [modelo](notification-hubs-templates-cross-platform-push-messages.md) do registo da notificação.


1. No ficheiro App.xaml.cs, adicione a seguinte propriedade para o **aplicação** classe. Substitua o `<hub name>` e `<connection string with listen access>` marcadores de posição pelo nome do seu hub de notificação e a cadeia de ligação para *DefaultListenSharedAccessSignature* que obteve anteriormente.
   
        public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
   
   > [!NOTE]
   > Porque as credenciais que são distribuídas com uma aplicação de cliente não são geralmente seguras, só deve de distribuir a chave de acesso escuta com a sua aplicação de cliente. Escutar permite o acesso não é possível modificar a sua aplicação para se registar para notificações, mas os registos existentes e não não possível enviar notificações. A chave de acesso total é utilizada num serviço protegidos back-end para envio de notificações e alterar os registos existentes.
   > 
   > 
2. No seu MainPage.xaml.cs, adicione a seguinte linha:
   
        using Windows.UI.Popups;
3. No ficheiro de projeto MainPage.xaml.cs, adicione o método seguinte:
   
        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
          var categories = new HashSet<string>();
          if (WorldCheckBox.IsChecked == true) categories.Add("World");
          if (PoliticsCheckBox.IsChecked == true) categories.Add("Politics");
          if (BusinessCheckBox.IsChecked == true) categories.Add("Business");
          if (TechnologyCheckBox.IsChecked == true) categories.Add("Technology");
          if (ScienceCheckBox.IsChecked == true) categories.Add("Science");
          if (SportsCheckBox.IsChecked == true) categories.Add("Sports");
   
          var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);
   
          MessageBox.Show("Subscribed to: " + string.Join(",", categories) + " on registration id : " +
             result.RegistrationId);
        }
   
    Este método cria uma lista de categorias e utiliza o **notificações** etiquetas de classe para armazenar a lista no armazenamento local e registar correspondente com o notification hub. Quando são alteradas categorias, o registo é recriado com as categorias de novo.

A aplicação agora é capaz de armazenar um conjunto de categorias no armazenamento local no dispositivo e registar o notification hub sempre que o utilizador altera a seleção das categorias.

## <a name="register-for-notifications"></a>Registar-se as notificações
Estes passos registar com o hub de notificação no arranque utilizando as categorias que foram armazenadas no armazenamento local.

> [!NOTE]
> Porque o URI atribuído pelo Push notificação serviço Microsoft (MPNS) do canal pode alterar a qualquer momento, deve registar para notificações frequentemente evitar falhas de notificação. Neste exemplo se regista as notificações sempre que a aplicação for iniciada. Para aplicações que são executadas com frequência, mais do que uma vez por dia, pode provavelmente ignorar registo para preservar a largura de banda, se menos de um dia foi efectuada com êxito desde o registo anterior.
> 
> 

1. Abra o ficheiro App.xaml.cs e adicione o **async** modificador para **Application_Launching** método e substitua o registo dos Notification Hubs de código que é adicionado no [introdução aos Hubs de notificação] com o seguinte código:
   
        private async void Application_Launching(object sender, LaunchingEventArgs e)
        {
            var result = await notifications.SubscribeToCategories();
   
            if (result != null)
                System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
                {
                    MessageBox.Show("Registration Id :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
                });
        }
   
    Isto certifica-se de que sempre que a aplicação for iniciada, obtém as categorias de armazenamento local e os pedidos de um registo para estas categorias.
2. No ficheiro de projeto MainPage.xaml.cs, adicione o seguinte código que implementa o **OnNavigatedTo** método:
   
        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            var categories = ((App)Application.Current).notifications.RetrieveCategories();
   
            if (categories.Contains("World")) WorldCheckBox.IsChecked = true;
            if (categories.Contains("Politics")) PoliticsCheckBox.IsChecked = true;
            if (categories.Contains("Business")) BusinessCheckBox.IsChecked = true;
            if (categories.Contains("Technology")) TechnologyCheckBox.IsChecked = true;
            if (categories.Contains("Science")) ScienceCheckBox.IsChecked = true;
            if (categories.Contains("Sports")) SportsCheckBox.IsChecked = true;
        }
   
    Isto atualiza a página principal com base no estado das categorias guardados anteriormente.

A aplicação está agora concluída e pode armazenar um conjunto de categorias no armazenamento local do dispositivo utilizado para registar o notification hub sempre que o utilizador altera a seleção das categorias. Em seguida, iremos definir um back-end que pode enviar notificações de categoria para esta aplicação.

## <a name="sending-tagged-notifications"></a>Enviar notificações marcadas
[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-app-and-generate-notifications"></a>Executar a aplicação e gerar notificações
1. No Visual Studio, prima F5 para compilar e iniciar a aplicação.
   
    ![][1]
   
    Tenha em atenção que a aplicação IU fornece um conjunto de mudanças de modo email que lhe permite escolher as categorias para subscrever.
2. Ativar uma ou mais categorias mudanças de modo email, em seguida, clique em **subscrever**.
   
    A aplicação converte as categorias selecionadas etiquetas e os pedidos de um novo registo de dispositivo para as etiquetas selecionados do hub de notificação. As categorias registadas são devolvidas e apresentadas numa caixa de diálogo.
   
    ![][2]
3. Depois de receber uma confirmação de que as categorias foram subscrição foi concluída, execute a aplicação de consola para enviar notificações para cada categorias. Certifique-se que apenas a receber uma notificação para as categorias que tem subscritos.
   
    ![][3]

Concluiu neste tópico.

<!--##Next steps

In this tutorial we learned how to broadcast breaking news by category. Consider completing one of the following tutorials that highlight other advanced Notification Hubs scenarios:

+ [Use Notification Hubs to broadcast localized breaking news]

    Learn how to expand the breaking news app to enable sending localized notifications.

+ [Notify users with Notification Hubs]

    Learn how to push notifications to specific authenticated users. This is a good solution for sending notifications only to specific users.
-->

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
[2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
[3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png



<!-- URLs.-->
[introdução aos Hubs de notificação]: /manage/services/notification-hubs/get-started-notification-hubs-wp8/
[Use Notification Hubs to broadcast localized breaking news]: ../breakingnews-localized-wp8.md
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users/
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Phone]: ??

