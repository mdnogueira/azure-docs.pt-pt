---
title: Azure notificar utilizadores dos Notification Hubs com back-end .NET
description: "Saiba como enviar notificações push segura no Azure. Exemplos de código escrito em C# utilizando a API .NET."
documentationcenter: windows
author: ysxu
manager: erikre
services: notification-hubs
editor: 
ms.assetid: 012529f2-fdbc-43c4-8634-2698164b5880
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: c0b963ef661612b1a176dd8e5f01d56e61eb5acb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-notification-hubs-notify-users-with-net-backend"></a>Azure notificar utilizadores dos Notification Hubs com back-end .NET
[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

## <a name="overview"></a>Descrição geral
Suporte de notificação push no Azure permite-lhe aceder a uma fácil de utilizar, multiplatform e infraestrutura push ampliada, o que simplifica bastante a implementação de notificações push para aplicações de consumidor e para empresas para plataformas móveis. Este tutorial mostra-lhe como pode utilizar os Hubs de Notificação do Azure para enviar notificações push para um utilizador da aplicação específico num dispositivo específico. Um back-end de ASP.NET WebAPI end é utilizado para autenticar clientes. Utilizar o utilizador de cliente autenticado e etiqueta serão automaticamente adicionada ao back-end ao registo de notificação. Esta etiqueta será utilizada para enviar pelo back-end para gerar notificações para um utilizador específico. Para obter mais informações sobre a registar notificações utilizando um back-end da aplicação, consulte o tópico de documentação de orientação [registar de back-end da aplicação](http://msdn.microsoft.com/library/dn743807.aspx). Este tutorial baseia-se o hub de notificação e o projeto que criou no [introdução aos Hubs de notificação] tutorial.

Este tutorial também é o pré-requisito para a [Secure Push] tutorial. Depois de concluir os passos neste tutorial, pode avançar para o [Secure Push] tutorial, que mostra como modificar o código neste tutorial para enviar uma notificação push de forma segura.

## <a name="before-you-begin"></a>Antes de começar
Levamos muito a sério os seus comentários. Se tiver dificuldades em concluir este tópico ou tiver recomendações para melhorar este conteúdo, agradecemos os seus comentários na parte inferior da página.

O código de conclusão para este tutorial pode ser encontrado [aqui](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers) no GitHub. 

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter já concluído estes tutoriais de Mobile Services:

* [introdução aos Hubs de notificação]<br/>Criar o seu hub de notificação e reservar o nome da aplicação e registe para receber notificações neste tutorial. Este tutorial parte do princípio de que já ter concluído estes passos. Se não estiver, siga os passos no [introdução aos Notification Hubs (loja Windows)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md); especificamente, as secções [registar a aplicação na loja Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md#register-your-app-for-the-windows-store) e [configurar o Hub de notificação](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md#configure-your-notification-hub). Em particular, certifique-se de que introduziu o **SID do pacote** e **segredo do cliente** valores no portal, no **configurar** separador para o seu hub de notificação. Este procedimento de configuração está descrito na secção [configurar o Notification Hub](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md#configure-your-notification-hub). Este é um passo importante: se as credenciais no portal não correspondem às especificadas para o nome da aplicação que escolher, a notificação push não será concluída com êxito.

> [!NOTE]
> Se estiver a utilizar Mobile Apps no App Service do Azure como o serviço de back-end, consulte o [versão Mobile Apps](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md) deste tutorial.
> 
> 

&nbsp;

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="update-the-code-for-the-client-project"></a>Atualize o código para o projeto de cliente
Nesta secção, atualize o código no projeto concluído para o [introdução aos Hubs de notificação] tutorial. O já deve ser associado à loja e configurado para o seu hub de notificação. Nesta secção, irá adicionar código para chamar o novo back-end do end WebAPI e utilizá-lo para registar e enviar notificações.

1. No Visual Studio, abra a solução que criou para o [introdução aos Hubs de notificação] tutorial.
2. No Explorador de soluções, clique com botão direito do **(Windows 8.1)** projeto e, em seguida, clique em **gerir pacotes NuGet**.
3. No lado esquerdo, clique em **Online**.
4. No **pesquisa** caixa, escreva **cliente Http**.
5. Na lista de resultados, clique em **bibliotecas de cliente do Microsoft HTTP**e, em seguida, clique em **instalar**. Conclua a instalação.
6. Regresso do NuGet **pesquisa** caixa, escreva **Json.net**. Instalar o **Json.NET** do pacote e, em seguida, feche a janela do Gestor de pacotes NuGet.
7. Repita os passos acima para o **(Windows Phone 8.1)** projeto para instalar o **JSON.NET** pacote NuGet para o projeto Windows Phone.
8. No Explorador de soluções, no **(Windows 8.1)** do projeto, faça duplo clique **MainPage.xaml** para abri-lo no editor do Visual Studio.
9. No **MainPage.xaml** código XML, substitua o `<Grid>` secção pelo código seguinte. Este código adiciona uma caixa de nome de utilizador e palavra-passe de texto que irá autenticar o utilizador com. Também adiciona caixas de texto para a mensagem de notificação e a etiqueta de nome de utilizador que deve receber a notificação:
   
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="*"/>
            </Grid.RowDefinitions>
   
            <TextBlock Grid.Row="0" Text="Notify Users" HorizontalAlignment="Center" FontSize="48"/>
   
            <StackPanel Grid.Row="1" VerticalAlignment="Center">
                <Grid>
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition></ColumnDefinition>
                        <ColumnDefinition></ColumnDefinition>
                        <ColumnDefinition></ColumnDefinition>
                    </Grid.ColumnDefinitions>
                    <TextBlock Grid.Row="0" Grid.ColumnSpan="3" Text="Username" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="UsernameTextBox" Grid.Row="1" Grid.ColumnSpan="3" Margin="20,0,20,0"/>
                    <TextBlock Grid.Row="2" Grid.ColumnSpan="3" Text="Password" FontSize="24" Margin="20,0,20,0" />
                    <PasswordBox Name="PasswordTextBox" Grid.Row="3" Grid.ColumnSpan="3" Margin="20,0,20,0"/>
   
                    <Button Grid.Row="4" Grid.ColumnSpan="3" HorizontalAlignment="Center" VerticalAlignment="Center"
                                Content="1. Login and register" Click="LoginAndRegisterClick" Margin="0,0,0,20"/>
   
                    <ToggleButton Name="toggleWNS" Grid.Row="5" Grid.Column="0" HorizontalAlignment="Right" Content="WNS" IsChecked="True" />
                    <ToggleButton Name="toggleGCM" Grid.Row="5" Grid.Column="1" HorizontalAlignment="Center" Content="GCM" />
                    <ToggleButton Name="toggleAPNS" Grid.Row="5" Grid.Column="2" HorizontalAlignment="Left" Content="APNS" />
   
                    <TextBlock Grid.Row="6" Grid.ColumnSpan="3" Text="Username Tag To Send To" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="ToUserTagTextBox" Grid.Row="7" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                    <TextBlock Grid.Row="8" Grid.ColumnSpan="3" Text="Enter Notification Message" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="NotificationMessageTextBox" Grid.Row="9" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                    <Button Grid.Row="10" Grid.ColumnSpan="3" HorizontalAlignment="Center" Content="2. Send push" Click="PushClick" Name="SendPushButton" />
                </Grid>
            </StackPanel>
        </Grid>
10. No Explorador de soluções, no **(Windows Phone 8.1)** projeto, abra **MainPage.xaml** e substitua o Windows Phone 8.1 `<Grid>` secção com esse mesmo código acima. A interface deve ter um aspeto semelhante whats mostrado abaixo.
    
    ![][13]
11. No Explorador de soluções, abra o **MainPage.xaml.cs** de ficheiros para o **(Windows 8.1)** e **(Windows Phone 8.1)** projetos. Adicione o seguinte `using` declarações na parte superior de ambos os ficheiros:
    
        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;
        using System.Threading.Tasks;
12. No **MainPage.xaml.cs** para o **(Windows 8.1)** e **(Windows Phone 8.1)** projetos, adicione o seguinte membro para o `MainPage` classe. Não se esqueça de substituir `<Enter Your Backend Endpoint>` com o seu ponto final de back-end real obtido anteriormente. Por exemplo, `http://mybackend.azurewebsites.net`.
    
        private static string BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";
13. Adicione o código abaixo para a classe de MainPage no **MainPage.xaml.cs** para o **(Windows 8.1)** e **(Windows Phone 8.1)** projetos.
    
    O `PushClick` método é o processador de clique para o **enviar Push** botão. Chama o back-end para acionar uma notificação a todos os dispositivos com uma etiqueta de nome de utilizador que corresponda a `to_tag` parâmetro. A mensagem de notificação é enviada como conteúdo JSON no corpo do pedido.
    
    O `LoginAndRegisterClick` método é o processador de clique para o **iniciar sessão e registar** botão. Armazena o basic token de autenticação no armazenamento local (tenha em atenção que isto representa qualquer token utiliza o seu esquema de autenticação), em seguida, utiliza `RegisterClient` para se registar para notificações utilizando o back-end.

        private async void PushClick(object sender, RoutedEventArgs e)
        {
            if (toggleWNS.IsChecked.Value)
            {
                await sendPush("wns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
            }
            if (toggleGCM.IsChecked.Value)
            {
                await sendPush("gcm", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
            }
            if (toggleAPNS.IsChecked.Value)
            {
                await sendPush("apns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);

            }
        }

        private async Task sendPush(string pns, string userTag, string message)
        {
            var POST_URL = BACKEND_ENDPOINT + "/api/notifications?pns=" +
                pns + "&to_tag=" + userTag;

            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                try
                {
                    await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                        System.Text.Encoding.UTF8, "application/json"));
                }
                catch (Exception ex)
                {
                    MessageDialog alert = new MessageDialog(ex.Message, "Failed to send " + pns + " message");
                    alert.ShowAsync();
                }
            }
        }

        private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
        {
            SetAuthenticationTokenInLocalStorage();

            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            // The "username:<user name>" tag gets automatically added by the message handler in the backend.
            // The tag passed here can be whatever other tags you may want to use.
            try
            {
                // The device handle used will be different depending on the device and PNS. 
                // Windows devices use the channel uri as the PNS handle.
                await new RegisterClient(BACKEND_ENDPOINT).RegisterAsync(channel.Uri, new string[] { "myTag" });

                var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
                SendPushButton.IsEnabled = true;
            }
            catch (Exception ex)
            {
                MessageDialog alert = new MessageDialog(ex.Message, "Failed to register with RegisterClient");
                alert.ShowAsync();
            }
        }

        private void SetAuthenticationTokenInLocalStorage()
        {
            string username = UsernameTextBox.Text;
            string password = PasswordTextBox.Password;

            var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
            ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
        }



1. No Explorador de soluções, sob o **partilhados** projeto, abra o **App.xaml.cs** ficheiro. Localizar a chamada para `InitNotificationsAsync()` no `OnLaunched()` processador de eventos. Comente ou eliminar a chamada para `InitNotificationsAsync()`. O processador de botão adicionado acima inicializará registos de notificação.

        protected override void OnLaunched(LaunchActivatedEventArgs e)
        {
            //InitNotificationsAsync();


1. No Explorador de soluções, clique com botão direito do **partilhados** do projeto, em seguida, clique em **adicionar**e, em seguida, clique em **classe**. Designar a classe **RegisterClient.cs**, em seguida, clique em **OK** para gerar a classe.
   
   Esta classe irá encapsular as chamadas REST necessárias para contactar o back-end da aplicação, para se registar para notificações push. Localmente também armazena o *registrationIds* criados através do Hub de notificação, conforme detalhado no [registar de back-end da aplicação](http://msdn.microsoft.com/library/dn743807.aspx). Tenha em atenção que utiliza um token de autorização armazenado no armazenamento local quando clicar no **iniciar sessão e registar** botão.
2. Adicione o seguinte `using` declarações na parte superior do ficheiro RegisterClient.cs:
   
       using Windows.Storage;
       using System.Net;
       using System.Net.Http;
       using System.Net.Http.Headers;
       using Newtonsoft.Json;
       using System.Threading.Tasks;
       using System.Linq;
3. Adicione o código seguinte dentro da definição de classe `RegisterClient`.
   
       private string POST_URL;
   
       private class DeviceRegistration
       {
           public string Platform { get; set; }
           public string Handle { get; set; }
           public string[] Tags { get; set; }
       }
   
       public RegisterClient(string backendEndpoint)
       {
           POST_URL = backendEndpoint + "/api/register";
       }
   
       public async Task RegisterAsync(string handle, IEnumerable<string> tags)
       {
           var regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
   
           var deviceRegistration = new DeviceRegistration
           {
               Platform = "wns",
               Handle = handle,
               Tags = tags.ToArray<string>()
           };
   
           var statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
   
           if (statusCode == HttpStatusCode.Gone)
           {
               // regId is expired, deleting from local storage & recreating
               var settings = ApplicationData.Current.LocalSettings.Values;
               settings.Remove("__NHRegistrationId");
               regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
               statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
           }
   
           if (statusCode != HttpStatusCode.Accepted)
           {
               // log or throw
               throw new System.Net.WebException(statusCode.ToString());
           }
       }
   
       private async Task<HttpStatusCode> UpdateRegistrationAsync(string regId, DeviceRegistration deviceRegistration)
       {
           using (var httpClient = new HttpClient())
           {
               var settings = ApplicationData.Current.LocalSettings.Values;
               httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);
   
               var putUri = POST_URL + "/" + regId;
   
               string json = JsonConvert.SerializeObject(deviceRegistration);
                               var response = await httpClient.PutAsync(putUri, new StringContent(json, Encoding.UTF8, "application/json"));
               return response.StatusCode;
           }
       }
   
       private async Task<string> RetrieveRegistrationIdOrRequestNewOneAsync()
       {
           var settings = ApplicationData.Current.LocalSettings.Values;
           if (!settings.ContainsKey("__NHRegistrationId"))
           {
               using (var httpClient = new HttpClient())
               {
                   httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);
   
                   var response = await httpClient.PostAsync(POST_URL, new StringContent(""));
                   if (response.IsSuccessStatusCode)
                   {
                       string regId = await response.Content.ReadAsStringAsync();
                       regId = regId.Substring(1, regId.Length - 2);
                       settings.Add("__NHRegistrationId", regId);
                   }
                   else
                   {
                       throw new System.Net.WebException(response.StatusCode.ToString());
                   }
               }
           }
           return (string)settings["__NHRegistrationId"];
   
       }
4. Guarde todas as alterações.

## <a name="testing-the-application"></a>Testar a aplicação
1. Inicie a aplicação no Windows 8.1 e Windows Phone 8.1. Para Windows Phone 8.1 pode executar a instância no emulador ou um dispositivo real.
2. Na instância da aplicação do Windows 8.1, introduza um **Username** e **palavra-passe** conforme mostrado no ecrã abaixo. Deve ser é diferente do nome de utilizador e palavra-passe que introduzir no Windows Phone.
3. Clique em **iniciar sessão e registar** e certifique-se de uma caixa de diálogo mostra de que iniciou sessão. Isto também irá ativar o **enviar Push** botão.
   
    ![][14]
4. Na instância do Windows Phone 8.1, introduza uma cadeia de nome de utilizador em ambas as **Username** e **palavra-passe** campos, em seguida, clique em **início de sessão e registar**.
5. Em seguida, no **etiqueta de nome de utilizador de destinatário** campo, introduza o nome de utilizador registado no Windows 8.1. Introduza uma mensagem de notificação e clique em **enviar Push**.
   
    ![][16]
6. Apenas os dispositivos que registou com a etiqueta de nome de utilizador correspondente recebem a mensagem de notificação.
   
    ![][15]

## <a name="next-steps"></a>Passos Seguintes
* Se pretende segmentar os utilizadores por grupos de interesses, consulte [Utilizar Notification Hubs para enviar notícias de última hora].
* Para obter mais informações sobre como utilizar os Notification Hubs, consulte [documentação de orientação dos Notification Hubs].

[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-ui.png
[14]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-windows-instance-username.png
[15]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-notification-received.png
[16]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-send-message.png



<!-- URLs. -->
[introdução aos Hubs de notificação]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Secure Push]: notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md
[Utilizar Notification Hubs para enviar notícias de última hora]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[documentação de orientação dos Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
