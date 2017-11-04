



Enviar notificações de modelo que apenas tem de fornecer um conjunto de propriedades, no nosso caso iremos enviar o conjunto de propriedades que contém a versão localizada do notícias atuais, por exemplo:

    {
        "News_English": "World News in English!",
        "News_French": "World News in French!",
        "News_Mandarin": "World News in Mandarin!"
    }


Esta secção mostra como enviar notificações através de uma aplicação de consola

O código incluído difunde para dispositivos loja Windows e iOS, uma vez que o back-end pode difusão para qualquer um dos dispositivos suportados.

### <a name="to-send-notifications-using-a-c-console-app"></a>Para enviar notificações através de uma aplicação de consola c#
Modificar o `SendTemplateNotificationAsync` método na aplicação de consola que criou anteriormente com o seguinte código. Repare como neste caso, não é necessário para enviar notificações vários para diferentes regiões e plataformas.

        private static async void SendTemplateNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub = 
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");

            // Sending the notification as a template notification. All template registrations that contain 
            // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications. 
            // This includes APNS, GCM, WNS, and MPNS template registrations.
            Dictionary<string, string> templateParams = new Dictionary<string, string>();

            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
            var locales = new string[] { "English", "French", "Mandarin" };

            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";

                // Sending localized News for each tag too...
                foreach( var locale in locales)
                {
                    string key = "News_" + locale;

                    // Your real localized news content would go here.
                    templateParams[key] = "Breaking " + category + " News in " + locale + "!";
                }

                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
        }


Tenha em atenção que esta chamada simple irá fornecer a informação de notícias ao localizada **todos os** os seus dispositivos, irrespective da plataforma, como o Notification Hub baseia-se e fornece o payload nativo correto a todos os dispositivos que subscreveram específico etiqueta.

### <a name="sending-the-notification-with-mobile-services"></a>Enviar a notificação com os Mobile Services
No seu agendador do serviço móvel, pode utilizar o script seguinte:

    var azure = require('azure');
    var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
    var notification = {
            "News_English": "World News in English!",
            "News_French": "World News in French!",
            "News_Mandarin", "World News in Mandarin!"
    }
    notificationHubService.send('World', notification, function(error) {
        if (!error) {
            console.warn("Notification successful");
        }
    });


