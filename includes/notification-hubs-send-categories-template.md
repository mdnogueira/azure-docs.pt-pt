
Nesta secção, pode enviar notícias de última hora marcadas como modelo notificações a partir de uma aplicação de consola .NET.

Se estiver a utilizar a funcionalidade de Mobile Apps do App Service do Microsoft Azure, consulte o [adicionar notificações push para Mobile Apps] tutorial e selecionar a plataforma na parte superior.

Se pretender utilizar o Java ou PHP, consulte [como utilizar os Notification Hubs de Java ou PHP]. Pode enviar notificações a partir de qualquer back-end utilizando a [interface REST de Hubs de notificação].

Se criou a aplicação de consola para enviar notificações quando concluído [introdução aos Hubs de notificação], ignorar os passos 1 a 3.

1. No Visual Studio, crie uma nova aplicação da consola Visual C#
   
      ![A ligação de aplicação de consola][13]

2. No menu principal do Visual Studio, selecione **ferramentas** > **Gestor de pacotes de biblioteca** > **consola do Gestor de pacotes** e, em seguida, na consola do janela, introduza a seguinte cadeia:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
3. Selecione **introduza**.  
    Esta ação adiciona uma referência ao SDK dos Hubs de Notificação do Azure mediante a utilização do [Pacote NuGet Microsoft.Azure.Notification Hubs].

4. Abra o ficheiro Program.cs e adicione o seguinte `using` instrução:
   
        using Microsoft.Azure.NotificationHubs;

5. No `Program` classe, adicione o seguinte método ou substituí-lo se já existir:
   
        private static async void SendTemplateNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub =
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");
   
            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business",
                                            "Technology", "Science", "Sports"};
   
            // Send the notification as a template notification. All template registrations that contain
            // "messageParam" and the proper tags will receive the notifications.
            // This includes APNS, GCM, WNS, and MPNS template registrations.
   
            Dictionary<string, string> templateParams = new Dictionary<string, string>();
   
            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";
                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
         }
   
    Este código envia uma notificação de modelo para cada uma das seis etiquetas na matriz da cadeia. A utilização de etiquetas assegura que os dispositivos recebem notificações apenas para as categorias registadas.

5. No código anterior, substitua o `<hub name>` e `<connection string with full access>` marcadores de posição pelo nome do seu hub de notificação e a cadeia de ligação para *DefaultFullSharedAccessSignature* a partir do dashboard do seu hub de notificação.

6. No método **Principal**, adicione as linhas seguintes:
   
         SendTemplateNotificationAsync();
         Console.ReadLine();

7. Crie a aplicação de consola.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[introdução aos Hubs de notificação]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[interface REST de Hubs de notificação]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[adicionar notificações push para Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[como utilizar os Notification Hubs de Java ou PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Pacote NuGet Microsoft.Azure.Notification Hubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
