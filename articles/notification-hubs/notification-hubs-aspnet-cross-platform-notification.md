---
title: "Enviar notificações de plataforma para os utilizadores com os Hubs de notificação do Azure (ASP.NET)"
description: "Saiba como utilizar modelos de Notification Hubs para enviar num pedido único, uma notificação de plataforma agnóstico que tenha como destino de todas as plataformas."
services: notification-hubs
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: 3c6dde338cb154f0cbe02642e4ff0f81d070aa25
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Enviar notificações de plataforma para os utilizadores com os Notification Hubs
Um tutorial anterior, [notificar os utilizadores com os Notification Hubs], aprendeu como notificações push para todos os dispositivos que estão registados para um utilizador autenticado específico. Este tutorial, era necessário vários pedidos para enviar uma notificação para cada plataforma de cliente suportada. Os Hubs de notificação do Azure suporta modelos, com o qual pode especificar a forma como pretende receber notificações de um dispositivo específico. Este método simplifica o envio de notificações de várias plataformas. 

Este artigo demonstra como tirar partido de modelos para enviar num pedido único, uma notificação de plataforma agnóstico que tenha como destino de todas as plataformas. Para obter mais informações sobre modelos, consulte [descrição geral de Hubs de notificação do Azure][Templates].

> [!IMPORTANT]
> Projetos do Windows Phone 8.1 e anteriores não são suportados no Visual Studio 2017. Para obter mais informações, veja [Visual Studio 2017 Platform Targeting and Compatibility](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs) (Segmentação e Compatibilidade de Plataformas do Visual Studio 2017)

> [!NOTE]
> Com os Notification Hubs, um dispositivo pode registar vários modelos com a mesma etiqueta. Neste caso, uma mensagem a receber que tenha como destino os resultados de tag em vários notificações fornecido ao dispositivo, um para cada modelo. Este processo permite-lhe apresentar a mesma mensagem em vários notificações visual, por exemplo, como um destaque e como uma notificação de alerta numa aplicação loja Windows.
> 
> 

Para enviar notificações de plataforma utilizando os modelos, efetue o seguinte:

1. No Explorador de soluções no Visual Studio, expanda o **controladores** pasta e, em seguida, abra o ficheiro RegisterController.cs.

2. Localize o bloco de código no **colocar** método que cria um novo registo e, em seguida, substitua o `switch` conteúdo com o seguinte código:
   
        switch (deviceUpdate.Platform)
        {
            case "mpns":
                var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<wp:Notification xmlns:wp=\"WPNotification\">" +
                       "<wp:Toast>" +
                            "<wp:Text1>$(message)</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
                registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "wns":
                toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
                registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "apns":
                var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
                registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
                break;
            case "gcm":
                var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }
   
    Este código chama o método de específicos da plataforma para criar um registo de modelo em vez de um registo nativo. Porque os registos de modelo de derivar de registos nativos, não precisa de o modificar registos existentes.

3. No **notificações** controlador, substitua o **sendNotification** método com o seguinte código:
   
        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;
   
            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);
   
            return Request.CreateResponse(HttpStatusCode.OK);
        }
   
    Este código envia uma notificação para todas as plataformas em simultâneo, sem ter de especificar um payload nativo. Notification Hubs baseia-se e entrega o payload correto para todos os dispositivos com o fornecido *tag* valor, conforme especificado nos modelos registados.

4. Publica novamente o projeto de back-end end WebApi.

5. Volte a executar a aplicação de cliente e, em seguida, certifique-se de que o registo foi bem sucedida.

6. (Opcional) Implementar a aplicação de cliente para um segundo dispositivo e, em seguida, execute a aplicação.
    Tenha em atenção que é apresentada uma notificação em cada dispositivo.

## <a name="next-steps"></a>Passos seguintes
Agora que concluiu este tutorial, saber mais sobre os Notification Hubs e modelos nos seguintes tópicos:

* [Use Notification Hubs to send breaking news]: Demonstrates another scenario for using templates.
* [Descrição geral dos Notification Hubs do Azure][Templates]: contém informações mais detalhadas sobre os modelos.

<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Push to users ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Push to users Mobile Services]: /manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[notificar os utilizadores com os Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx
