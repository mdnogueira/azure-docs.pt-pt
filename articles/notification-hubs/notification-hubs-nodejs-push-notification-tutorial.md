---
title: "Enviar notificações push com Notification Hubs do Azure e o Node.js"
description: "Saiba como utilizar os Notification Hubs para enviar notificações push a partir de uma aplicação Node.js."
keywords: "notificação push, push notifications,node.js push, push de ios"
services: notification-hubs
documentationcenter: nodejs
author: ysxu
manager: erikre
editor: 
ms.assetid: ded4749c-6c39-4ff8-b2cf-1927b3e92f93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 10/25/2016
ms.author: yuaxu
ms.openlocfilehash: dc4987b16b2e930641c6c90eff8b65c1bf8d573c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Enviar notificações push com Notification Hubs do Azure e o Node.js
[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Descrição geral
> [!IMPORTANT]
> Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).
> 
> 

Este guia irá mostrar como enviar notificações push com a ajuda dos Notification Hubs do Azure diretamente a partir de uma aplicação Node.js. 

Os cenários abrangidos incluem a enviar notificações push para aplicações nas seguintes plataformas:

* Android
* iOS
* Windows Phone
* Plataforma universal do Windows 

Para obter mais informações sobre os notification hubs, consulte o [passos](#next) secção.

## <a name="what-are-notification-hubs"></a>O que são os Hubs de Notificação?
Os Hubs de notificação do Azure fornecem uma infraestrutura de fácil utilização, várias plataforma, dimensionável para enviar notificações push para dispositivos móveis. Para obter detalhes sobre a infraestrutura de serviço, consulte o [Notification Hubs do Azure](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx) página.

## <a name="create-a-nodejs-application"></a>Criar uma aplicação Node.js
Neste tutorial, o primeiro passo é criar uma nova aplicação Node.js em branco. Para obter instruções sobre como criar uma aplicação Node.js, consulte [criar e implementar uma aplicação Node.js para o Azure Web Site][nodejswebsite], [serviço em nuvem de Node.js] [ Node.js Cloud Service] através do Windows PowerShell, ou [Web Site com o WebMatrix].

## <a name="configure-your-application-to-use-notification-hubs"></a>Configurar a sua aplicação para utilizar os Notification Hubs
Para utilizar Notification Hubs do Azure, tem de transferir e utilizar o Node.js [pacote do azure](https://www.npmjs.com/package/azure), que inclui um conjunto de bibliotecas de programa auxiliar que comunicam com os serviços REST de notificação push incorporado.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Utilize o Gestor de pacote de nó (NPM) para obter o pacote
1. Utilize uma interface de linha de comandos, como **PowerShell** (Windows), **Terminal** (Mac), ou **Bash** (Linux) e navegue para a pasta onde criou a aplicação em branco.
2. Tipo **npm instalar azure sb** na janela de comandos.
3. Pode executar manualmente o **ls** ou **dir** comando para verificar se um **nó\_módulos** pasta foi criada. Dentro dessa pasta, localizar o **azure** pacote, que contém as bibliotecas que precisa de aceder aos Notification Hub.

> [!NOTE]
> Pode saber mais sobre a instalação de NPM no oficial [NPM blogue](http://blog.npmjs.org/post/85484771375/how-to-install-npm). 
> 
> 

### <a name="import-the-module"></a>Importe o módulo
Com um editor de texto, adicione o seguinte na parte superior do **server.js** ficheiro da aplicação:

    var azure = require('azure');

### <a name="setup-an-azure-notification-hub-connection"></a>Configurar uma ligação do Hub de notificação do Azure
O **NotificationHubService** objeto permite-lhe trabalhar com os notification hubs. O código seguinte cria um **NotificationHubService** objeto para o hub de nofication denominado **hubname**. Adicione-a junto ao topo do **server.js** ficheiro, após a declaração a importar o módulo do azure:

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

A ligação **connectionstring** valor pode ser obtido a partir de [Portal do Azure] efetuando os seguintes passos:

1. No painel de navegação esquerdo, clique em **procurar**.
2. Selecione **Notification Hubs**e, em seguida, localize o hub de que pretende utilizar para o exemplo. Pode consultar o [Windows Store introdução tutorial](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) se precisar de ajuda para criar um novo Hub de notificação.
3. Selecione **definições**.
4. Clique em **políticas de acesso**. Verá ambas as cadeias de ligação de acesso partilhado e completa.

![Portal do Azure – os Hubs de notificação](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> Também pode obter a cadeia de ligação utilizando o **Get-AzureSbNamespace** cmdlet fornecida pelo [Azure PowerShell](/powershell/azureps-cmdlets-docs) ou o **mostrar de espaço de nomes do azure sb** comando com o [Interface de linha de comandos do Azure (CLI do Azure)](../cli-install-nodejs.md).
> 
> 

## <a name="general-architecture"></a>Arquitetura geral
O **NotificationHubService** objeto expõe as seguintes instâncias de objeto para envio notificações push para aplicações e dispositivos específicos:

* **Android** -utilize o **GcmService** objeto que está disponível em **notificationHubService.gcm**
* **iOS** -utilize o **ApnsService** objeto que está acessível na **notificationHubService.apns**
* **Windows Phone** -utilize o **MpnsService** objeto que está disponível em **notificationHubService.mpns**
* **Plataforma universal do Windows** -utilize o **WnsService** objeto que está disponível em **notificationHubService.wns**

### <a name="how-to-send-push-notifications-to-android-applications"></a>Como: enviar notificações de push para aplicações Android
O **GcmService** objeto fornece um **enviar** método que pode ser utilizado para enviar notificações push para aplicações Android. O **enviar** método aceita os seguintes parâmetros:

* **Etiquetas** -o identificador de etiqueta. Se nenhuma tag for fornecido, a notificação será enviada a todos os clientes.
* **Payload** -JSON ou payload de cadeia não processados a mensagem.
* **Chamada de retorno** -a função de chamada de retorno.

Para obter mais informações sobre o formato de payload, consulte o **Payload** secção o [implementar o servidor do GCM](http://developer.android.com/google/gcm/server.html#payload) documento.

O seguinte código utiliza o **GcmService** instância exposta pelo **NotificationHubService** para enviar uma notificação push para todos os clientes registados.

    var payload = {
      data: {
        message: 'Hello!'
      }
    };
    notificationHubService.gcm.send(null, payload, function(error){
      if(!error){
        //notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Como: enviar notificações de push para aplicações iOS
Mesmo que com as aplicações Android descritas acima, o **ApnsService** objeto fornece um **enviar** método que pode ser utilizado para enviar notificações push para aplicações iOS. O **enviar** método aceita os seguintes parâmetros:

* **Etiquetas** -o identificador de etiqueta. Se nenhuma tag for fornecido, a notificação será enviada a todos os clientes.
* **Payload** -payload da mensagem JSON ou cadeia.
* **Chamada de retorno** -a função de chamada de retorno.

Para obter mais informações o formato de payload, consulte o **notificação Payload** secção o [guia de programação de notificações de Push e Local](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) documento.

O seguinte código utiliza o **ApnsService** instância exposta pelo **NotificationHubService** para enviar uma mensagem de alerta para todos os clientes:

    var payload={
        alert: 'Hello!'
      };
    notificationHubService.apns.send(null, payload, function(error){
      if(!error){
         // notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Como: enviar notificações de push para aplicações do Windows Phone
O **MpnsService** objeto fornece um **enviar** método que pode ser utilizado para enviar notificações push para aplicações do Windows Phone. O **enviar** método aceita os seguintes parâmetros:

* **Etiquetas** -o identificador de etiqueta. Se nenhuma tag for fornecido, a notificação será enviada a todos os clientes.
* **Payload** -payload XML da mensagem.
* **TargetName**  -  `toast` para notificações de alerta. `token`Para obter notificações do mosaico.
* **NotificationClass** -a prioridade da notificação. Consulte o **elementos de cabeçalho de HTTP** secção o [notificações Push de um servidor](http://msdn.microsoft.com/library/hh221551.aspx) documento para os valores válidos.
* **Opções de** - opcional cabeçalhos de pedido.
* **Chamada de retorno** -a função de chamada de retorno.

Para obter uma lista válida de **TargetName**, **NotificationClass** e opções de cabeçalho, veja o [notificações Push de um servidor](http://msdn.microsoft.com/library/hh221551.aspx) página.

O seguinte exemplo de código utiliza o **MpnsService** instância exposta pelo **NotificationHubService** para enviar uma notificação push de alerta:

    var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
    notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
      if(!error){
        //notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Como: enviar notificações de push para aplicações da plataforma Universal do Windows (UWP)
O **WnsService** objeto fornece um **enviar** método que pode ser utilizado para enviar notificações push para aplicações da plataforma Universal do Windows.  O **enviar** método aceita os seguintes parâmetros:

* **Etiquetas** -o identificador de etiqueta. Se nenhuma tag for fornecido, a notificação será enviada a todos os clientes registados.
* **Payload** -o payload XML de mensagem.
* **Tipo** -o tipo de notificação.
* **Opções de** - opcional cabeçalhos de pedido.
* **Chamada de retorno** -a função de chamada de retorno.

Para obter uma lista de tipos válidos e cabeçalhos de pedido, consulte [Push cabeçalhos de pedido e resposta do serviço de notificação](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

O seguinte código utiliza o **WnsService** instância exposta pelo **NotificationHubService** para enviar uma alerta de notificação push para uma aplicação UWP:

    var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
    notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
      if(!error){
         // notification sent
      }
    });

## <a name="next-steps"></a>Passos Seguintes
Os fragmentos de exemplo acima permitem-lhe criar facilmente a infraestrutura de serviço para a entrega de notificações push para uma grande variedade de dispositivos. Agora que aprendeu as noções básicas de utilizar os Notification Hubs com o node.js, siga estas ligações para saber mais sobre como pode expandir ainda mais estas capacidades.

* Consulte a referência do MSDN para [Notification Hubs do Azure](https://msdn.microsoft.com/library/azure/jj927170.aspx).
* Visite o [Azure SDK para o nó] repositório no GitHub para obter mais exemplos e detalhes de implementação.

[Azure SDK para o nó]: https://github.com/WindowsAzure/azure-sdk-for-node
[Next Steps]: #nextsteps
[What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
[Create a Service Namespace]: #create-a-service-namespace
[Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
[Create a Node.js Application]: #Create_a_Nodejs_Application
[Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
[How to: Create a Topic]: #How_to_Create_a_Topic
[How to: Create Subscriptions]: #How_to_Create_Subscriptions
[How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
[How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
[How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
[How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
[1]: #Next_Steps
[Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
[Azure Classic Portal]: http://manage.windowsazure.com
[image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
[2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
[3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
[4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
[5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
[SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Azure Service Bus Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/jj927170.aspx
[SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
[Web Site com o WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
[nodejswebsite]: /develop/nodejs/tutorials/create-a-website-(mac)/
[Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
[Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
[Portal do Azure]: https://portal.azure.com
