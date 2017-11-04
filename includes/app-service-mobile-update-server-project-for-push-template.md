Nesta secção, atualize o código no seu projeto de back-end das Mobile Apps existente para enviar uma notificação push sempre que é adicionado um novo item. Esta utiliza a tecnologia do [modelo](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) funcionalidade dos Notification Hubs do Azure, permitindo pushes de várias plataformas. Vários clientes estão registados para notificações push através de modelos e pode obter um push universal único a todas as plataformas de cliente.

Escolha um dos seguintes procedimentos que corresponde ao seu tipo de projeto de back-end&mdash;ou [.NET back-end](#dotnet) ou [back-end Node.js](#nodejs).

### <a name="dotnet"></a>Projeto de back-end do .NET
1. No Visual Studio, clique com o botão direito no projeto de servidor e clique em **gerir pacotes NuGet**. Procurar `Microsoft.Azure.NotificationHubs`e, em seguida, clique em **instalar**. Esta ação instala a biblioteca de Notification Hubs para enviar notificações a partir do seu back-end.
2. No projeto de servidor, abra **controladores** > **TodoItemController.cs**e adicione as seguintes instruções de utilização:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;
3. No **PostTodoItem** método, adicione o seguinte código após a chamada para **InsertAsync**:  

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Sending the message so that all template registrations that contain "messageParam"
        // will receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
        Dictionary<string,string> templateParams = new Dictionary<string,string>();
        templateParams["messageParam"] = item.Text + " was added to the list.";

        try
        {
            // Send the push notification and log the results.
            var result = await hub.SendTemplateNotificationAsync(templateParams);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    Esta ação envia uma notificação de modelo que contém o item. Texto quando é inserido um novo item.
4. Voltar a publicar o projeto de servidor.

### <a name="nodejs"></a>Projeto de back-end do node.js
1. Se ainda não o tiver feito deste modo, [transferir o projeto de back-end de início rápido](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart), ou utilize outro o [editor online no portal do Azure](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
2. Substitua o código existente no todoitem.js com o seguinte:

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');

        var table = azureMobileApps.table();

        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK,
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');

        // Define the template payload.
        var payload = '{"messageParam": "' + context.item.text + '" }';  

        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a template notification.
                    context.push.send(null, payload, function (error) {
                        if (error) {
                            logger.error('Error while sending push notification: ', error);
                        } else {
                            logger.info('Push notification sent successfully!');
                        }
                    });
                }
                // Don't forget to return the results from the context.execute()
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
        });

        module.exports = table;  

    Esta ação envia uma notificação de modelo que contém o item.text Quando é inserido um novo item.
3. Ao editar o ficheiro no seu computador local, voltar a publicar o projeto de servidor.
