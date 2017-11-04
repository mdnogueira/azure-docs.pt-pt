---
title: "Os Notification Hubs localizado Tutorial de notícias de última"
description: "Saiba como utilizar Notification Hubs do Azure para enviar notificações de notícias de última localizada."
services: notification-hubs
documentationcenter: windows
author: ysxu
manager: erikre
editor: 
ms.assetid: c454f5a3-a06b-45ac-91c7-f91210889b25
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 8f205188bd68e53b187b71981ed36dcf9129ec62
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-notification-hubs-to-send-localized-breaking-news"></a>Utilizar Notification Hubs para enviar notícias de última localizada hora
> [!div class="op_single_selector"]
> * [Loja Windows c#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)
> 
> 

## <a name="overview"></a>Descrição geral
Este tópico mostra como utilizar o **modelo** funcionalidade dos Hubs de notificação do Azure para difundir notificações de notícias de última que foram localizadas por idioma e o dispositivo. Este tutorial começa com a aplicação da loja do Windows criada no [utilizar Notification Hubs para enviar notícias de última hora]. Quando terminar, poderá registar categorias que está interessado, especifique um idioma na qual pretende receber as notificações e receber notificações de push apenas para as categorias selecionadas nesse idioma.

Existem duas partes para este cenário:

* a aplicação da loja do Windows permite aos clientes dispositivos para especificar um idioma e subscrever a categorias de notícias de última diferente;
* back-end difunde as notificações, utilizando o **tag** e **modelo** feautres dos Notification Hubs do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Tem já tiver concluído a [utilizar Notification Hubs para enviar notícias de última hora] tutorial e tem o código disponível, porque este tutorial de mensagens em fila baseia-se diretamente nesse código.

Também precisa de Visual Studio 2012 ou posterior.

## <a name="template-concepts"></a>Conceitos de modelo
No [utilizar Notification Hubs para enviar notícias de última hora] criou uma aplicação que utilizado **etiquetas** subscrever notificações para as categorias de notícias de última hora diferente.
Muitas aplicações, no entanto, vários mercados de destino e que necessitam de localização. Isto significa que o conteúdo das notificações próprios têm de estar localizadas e enviada para o conjunto correto de dispositivos.
Este tópico vamos mostrar como utilizar o **modelo** funcionalidade dos Hubs de notificação facilmente entregar notificações de notícias de última localizada.

Nota: uma forma para enviar notificações localizadas consiste em criar várias versões de cada etiqueta. Por exemplo, para suportar o inglês, francês e Mandarim, seria precisamos três etiquetas diferentes para notícias mundo: "world_en", "world_fr" e "world_ch". Vamos, em seguida, teria de enviar uma versão localizada das notícias de mundo para cada um destas etiquetas. Este tópico utilizamos modelos para evitar a proliferação de etiquetas e o requisito de várias mensagens a enviar.

Um nível elevado, os modelos são uma forma para especificar a forma como um dispositivo específico deverá receber uma notificação. O modelo especifica o formato de payload exato ao referir-se para propriedades que fazem parte da mensagem enviada pelo seu back-end da aplicação. No nosso caso, iremos enviar uma mensagem de região agnóstico que contém todas as linguagens de:

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Em seguida, iremos irá garantir que os dispositivos registar com um modelo que refere-se para a propriedade correta. Por exemplo, vai registar uma aplicação da loja Windows que pretende receber uma mensagem de alerta simples para o modelo seguinte, com as etiquetas correspondentes:

    <toast>
      <visual>
        <binding template=\"ToastText01\">
          <text id=\"1\">$(News_English)</text>
        </binding>
      </visual>
    </toast>



Os modelos são uma funcionalidade muito poderosa, pode saber mais sobre no nosso [modelos](notification-hubs-templates-cross-platform-push-messages.md) artigo. 

## <a name="the-app-user-interface"></a>A interface de utilizador de aplicação
Iremos agora modificar a aplicação de notícias de última hora que criou no tópico [utilizar Notification Hubs para enviar notícias de última hora] enviar localizado utilizando modelos de notícias de última hora.

Na sua aplicação da loja Windows:

Altere o seu MainPage.xaml para incluir uma combobox de região:

    <Grid Margin="120, 58, 120, 80"  
            Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>

## <a name="building-the-windows-store-client-app"></a>Criar a aplicação de cliente da loja Windows
1. Na sua classe de notificações, adicionar um parâmetro de região a sua *StoreCategoriesAndSubscribe* e *SubscribeToCateories* métodos.
   
        public async Task<Registration> StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            ApplicationData.Current.LocalSettings.Values["locale"] = locale;
            return await SubscribeToCategories(categories);
        }
   
        public async Task<Registration> SubscribeToCategories(string locale, IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
   
            if (categories == null)
            {
                categories = RetrieveCategories();
            }
   
            // Using a template registration. This makes supporting notifications across other platforms much easier.
            // Using the localized tags based on locale selected.
            string templateBodyWNS = String.Format("<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(News_{0})</text></binding></visual></toast>", locale);
   
            return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "localizedWNSTemplateExample", categories);
        }
   
    Tenha em atenção que, em vez de chamar o *RegisterNativeAsync* método chamamos *RegisterTemplateAsync*: está a registar um formato de notificação específico no qual o modelo depende da região. Também podemos fornecer um nome para o modelo ("localizedWNSTemplateExample"), porque poderá Queremos registar mais do que um modelo (por exemplo um para notificações de alerta) e outro para mosaicos e é necessário o nome-los para poder atualizar ou eliminá-los.
   
    Tenha em atenção que o se um dispositivo regista vários modelos com a mesma etiqueta, uma entrada mensagem filtragem que tag resultará em várias notificações entregue no dispositivo (um para cada modelo). Este comportamento é útil quando tem a mesma mensagem lógica resultará em várias notificações visual, para a instância que mostra um destaque e um alerta numa aplicação loja Windows.
2. Adicione o seguinte método para obter a região armazenada:
   
        public string RetrieveLocale()
        {
            var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
            return locale != null ? locale : "English";
        }
3. No seu MainPage.xaml.cs, o botão de atualização. clique em processador por obter o valor atual da caixa de combinação de região e fornecendo-lo para a chamada para a classe de notificações, conforme mostrado:
   
        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var locale = (string)Locale.SelectedItem;
   
            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");
   
            var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(locale,
                 categories);
   
            var dialog = new MessageDialog("Locale: " + locale + " Subscribed to: " + 
                string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
4. Por fim, no seu ficheiro App.xaml.cs, certifique-se de que atualiza o `InitNotificationsAsync` método para obter a região e utilizá-lo quando subscrever:
   
        private async void InitNotificationsAsync()
        {
            var result = await notifications.SubscribeToCategories(notifications.RetrieveLocale());
   
            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

## <a name="send-localized-notifications-from-your-back-end"></a>Enviar notificações localizadas do seu back-end
[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notify users with Notification Hubs: Mobile Services]: /manage/services/notification-hubs/notify-users
[utilizar Notification Hubs para enviar notícias de última hora]: /notification-hubs/notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
