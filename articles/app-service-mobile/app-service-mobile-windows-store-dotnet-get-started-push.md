---
title: "Adicionar notificações push à aplicação plataforma Universal do Windows (UWP) | Microsoft Docs"
description: "Saiba como utilizar Mobile Apps do Azure App Service e Notification Hubs do Azure para enviar notificações push à aplicação plataforma Universal do Windows (UWP)."
services: app-service\mobile,notification-hubs
documentationcenter: windows
author: ysxu
manager: syntaxc4
editor: 
ms.assetid: 6de1b9d4-bd28-43e4-8db4-94cd3b187aa3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
ms.openlocfilehash: a14bb0320c1f6a563f766a6a0fad5cf556fe7b70
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-push-notifications-to-your-windows-app"></a>Adicionar notificações push à aplicação do Windows
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Descrição geral
Neste tutorial, adicionar notificações push para o [início rápido do Windows](app-service-mobile-windows-store-dotnet-get-started.md) projeto para que uma notificação push é enviada para o dispositivo sempre que é inserido um registo.

Se utilizar o projeto de servidor de início rápido transferido, terá do pacote de extensão de notificação push. Consulte [trabalhar com o servidor de back-end .NET SDK de Mobile Apps do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para obter mais informações.

## <a name="configure-hub"></a>Configurar um Hub de notificação
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>Registar a aplicação para notificações push
Tem de submeter a sua aplicação para a loja Windows, em seguida, configurar o seu projeto de servidor para integrar com serviços de notificação de Windows (WNS) para enviar push.

1. No Explorador de soluções do Visual Studio, clique com botão direito no projeto de aplicação UWP, clique em **arquivo** > **associar aplicação à loja...** .

    ![Associar aplicação à loja Windows](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)
2. No assistente, clique em **seguinte**, inicie sessão com a sua conta Microsoft, escreva um nome para a sua aplicação no **reservar um novo nome de aplicação**, em seguida, clique em **reserva**.
3. Após o registo de aplicação é criado com êxito, selecione o novo nome de aplicação, clique em **seguinte**e, em seguida, clique em **associar**. Esta ação adiciona ao manifesto da aplicação as informações de registo da Loja Windows necessárias.  
4. Navegue para o [Windows Dev Center](https://dev.windows.com/en-us/overview), inicie sessão com a sua conta Microsoft, clique em novo registo de aplicação no **as minhas aplicações**, em seguida, expanda **serviços** > **notificações Push**.
5. No **notificações Push** página, clique em **site dos Serviços Live** em **dos Mobile Services do Microsoft Azure**.
6. Na página de registo, tome nota do valor em **segredos de aplicação** e **SID do pacote**, que irá utilizar seguinte para configurar o back-end da aplicação móvel.

    ![Associar aplicação à loja Windows](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > O segredo de cliente e o SID do pacote são credenciais de segurança importantes. Não partilhe estes valores com ninguém e não os distribua com a aplicação. O **Id da aplicação** é utilizado com o segredo para configurar a autenticação de Account Microsoft.
   >
   >

## <a name="configure-the-backend-to-send-push-notifications"></a>Configurar o back-end para enviar notificações push
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a id="update-service"></a>Atualizar o servidor para enviar notificações push
Utilize o procedimento abaixo que corresponda ao seu tipo de projeto de back-end&mdash;ou [back-end .NET](#dotnet) ou [back-end Node.js](#nodejs).

### <a name="dotnet"></a>Projeto de back-end do .NET
1. No Visual Studio, clique com o botão direito no projeto de servidor e clique em **gerir pacotes NuGet**, procure notificationhubs, em seguida, clique em **instalar**. Esta ação instala a biblioteca de clientes de Notification Hubs.
2. Expanda **controladores**, abra TodoItemController.cs e adicione as seguintes instruções de utilização:

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

        // Create the notification hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Define a WNS payload
        var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
                                + item.Text + @"</text></binding></visual></toast>";
        try
        {
            // Send the push notification.
            var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    Este código informa o hub de notificação para enviar uma notificação push, depois de um novo item de inserção.
4. Voltar a publicar o projeto de servidor.

### <a name="nodejs"></a>Projeto de back-end do node.js
1. Se ainda não o tiver feito deste modo, [transferir o projeto de início rápido](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) ou utilize outro o [editor online no portal do Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
2. Substitua o código existente no ficheiro todoitem.js com o seguinte:

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');

        var table = azureMobileApps.table();

        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK,
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');

        // Define the WNS payload that contains the new item Text.
        var payload = "<toast><visual><binding template=\ToastText01\><text id=\"1\">"
                                    + context.item.text + "</text></binding></visual></toast>";

        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a WNS native toast notification.
                    context.push.wns.sendToast(null, payload, function (error) {
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

    Esta ação envia uma notificação de alerta do WNS que contém o item.text Quando é inserido um novo item de todo.
3. Ao editar o ficheiro no seu computador local, voltar a publicar o projeto de servidor.

## <a id="update-app"></a>Adicionar notificações push à aplicação
Em seguida, a aplicação tem de se registar para notificações push no arranque. Quando já tiver ativado a autenticação, certifique-se de que o utilizador inicia sessão antes de tentar registar para notificações push.

1. Abra o **App.xaml.cs** ficheiros do projeto e adicione o seguinte `using` instruções:

        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
2. No mesmo ficheiro, adicione a seguinte **InitNotificationsAsync** definição de método para o **aplicação** classe:

        private async Task InitNotificationsAsync()
        {
            // Get a channel URI from WNS.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register the channel URI with Notification Hubs.
            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }

    Este código obtém o ChannelURI para a aplicação a partir do WNS e, em seguida, regista esse ChannelURI com a sua aplicação de Mobile do serviço de aplicações.
3. Na parte superior a **OnLaunched** processador de eventos no **App.xaml.cs**, adicionar o **async** modificador à definição de método e adicione a seguinte chamada ao novo **InitNotificationsAsync** método, como no exemplo seguinte:

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

            // ...
        }

    Esta ação garante que o ChannelURI de curta duração é registado sempre que a aplicação for iniciada.
4. Reconstrua o projeto de aplicação UWP. A aplicação já está pronta para receber notificações de alerta.

## <a id="test"></a>Notificações de push de teste na sua aplicação
[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a id="more"></a>Passos seguintes
Saiba mais sobre as notificações push:

* [Como utilizar o cliente gerido para Mobile Apps do Azure](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications)  
  Modelos dão-lhe a flexibilidade para enviar pushes de plataforma e pushes localizadas. Saiba como registar modelos.
* [Diagnosticar problemas de notificação push](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Existem vários motivos por que razão as notificações podem obter removidas ou não terminar em dispositivos. Este tópico mostra como analisar e descobrir a causa de raiz de falhas de notificação push.

Considere continuar para um dos seguintes tutoriais:

* [Adicionar autenticação à aplicação](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  Saiba como autenticar os utilizadores da aplicação com um fornecedor de identidade.
* [Permitir sincronização offline para a sua aplicação](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Saiba como adicionar suporte offline à aplicação utilizando um back-end de Aplicação Móvel. A sincronização offline permite que os utilizadores finais interajam com uma aplicação móvel &mdash; visualizar, adicionar ou modificar dados &mdash;, mesmo quando não existe qualquer ligação de rede.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
