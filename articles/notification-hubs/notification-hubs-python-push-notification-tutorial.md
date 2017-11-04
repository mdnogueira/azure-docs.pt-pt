---
title: Como utilizar os Notification Hubs com o Python
description: Saiba como utilizar Notification Hubs do Azure de um back-end do Python.
services: notification-hubs
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 5640dd4a-a91e-4aa0-a833-93615bde49b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: python
ms.devlang: php
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 9ceedb9940759427fc8cec74a1307e42472563a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-notification-hubs-from-python"></a>Como utilizar os Hubs de notificação do Python
[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Pode aceder a todas as funcionalidades de Notification Hubs a partir de um back-end Java/PHP/Python/Ruby utilizando a interface REST do Notification Hub, conforme descrito no tópico do MSDN [APIs REST do Notification Hubs](http://msdn.microsoft.com/library/dn223264.aspx).

> [!NOTE]
> Esta é uma implementação de referência de exemplo para implementar envia a notificação no Python e não é o SDK Python do Hub de notificações oficialmente suportado.
> 
> Este exemplo foi escrito utilizando o Python 3.4.
> 
> 

Neste tópico mostram como:

* Crie um cliente REST para funcionalidades de Hubs de notificação no Python.
* Envie notificações através da interface do Python para as APIs REST do Notification Hub. 
* Obtenha uma captura de pedido/resposta REST de HTTP para fins de depuração/educational. 

Pode seguir o [tutorial de introdução](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) para a sua plataforma móvel escolhidas, implementar a parte de back-end no Python.

> [!NOTE]
> O âmbito do exemplo é limitado apenas para enviar notificações e não faz qualquer gestão do registo.
> 
> 

## <a name="client-interface"></a>Interface do cliente
A interface de principal do cliente pode fornecer os mesmos métodos que estão disponíveis no [SDK .NET Dosnotification Hubs](http://msdn.microsoft.com/library/jj933431.aspx). Isto permite-lhe diretamente traduzir os tutoriais e amostras atualmente disponíveis neste site e contribuíram pela Comunidade na internet.

Pode encontrar o código disponível no [exemplo de wrapper do Python REST].

Por exemplo, para criar um cliente:

    isDebug = True
    hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)

Para enviar uma notificação de alerta do Windows:

    wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
    hub.send_windows_notification(wns_payload)

## <a name="implementation"></a>Implementação
Se ainda não fez, siga a nossa [tutorial de introdução] configurar para a última secção onde tem de implementar o back-end.

Todos os detalhes para implementar um dispositivo de moldagem REST completo podem ser encontrados no [MSDN](http://msdn.microsoft.com/library/dn530746.aspx). Nesta secção dita a implementação de Python dos principais passos necessários para aceder a pontos finais REST de Hubs de notificação e enviar notificações

1. Analisar a cadeia de ligação
2. Gerar o token de autorização
3. Enviar uma notificação utilizando a API de REST de HTTP

### <a name="parse-the-connection-string"></a>Analisar a cadeia de ligação
Segue-se a classe principal de implementar o cliente, cujo construtor analisa a cadeia de ligação:

    class NotificationHub:
        API_VERSION = "?api-version=2013-10"
        DEBUG_SEND = "&test"

        def __init__(self, connection_string=None, hub_name=None, debug=0):
            self.HubName = hub_name
            self.Debug = debug

            # Parse connection string
            parts = connection_string.split(';')
            if len(parts) != 3:
                raise Exception("Invalid ConnectionString.")

            for part in parts:
                if part.startswith('Endpoint'):
                    self.Endpoint = 'https' + part[11:]
                if part.startswith('SharedAccessKeyName'):
                    self.SasKeyName = part[20:]
                if part.startswith('SharedAccessKey'):
                    self.SasKeyValue = part[16:]


### <a name="create-security-token"></a>Criar o token de segurança
Os detalhes da criação de token de segurança estão disponíveis [aqui](http://msdn.microsoft.com/library/dn495627.aspx).
Os métodos seguintes têm de ser adicionado à **NotificationHub** classe para criar o token com base no URI do pedido atual e as credenciais extraídas da cadeia de ligação.

    @staticmethod
    def get_expiry():
        # By default returns an expiration of 5 minutes (=300 seconds) from now
        return int(round(time.time() + 300))

    @staticmethod
    def encode_base64(data):
        return base64.b64encode(data)

    def sign_string(self, to_sign):
        key = self.SasKeyValue.encode('utf-8')
        to_sign = to_sign.encode('utf-8')
        signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
        digest = signed_hmac_sha256.digest()
        encoded_digest = self.encode_base64(digest)
        return encoded_digest

    def generate_sas_token(self):
        target_uri = self.Endpoint + self.HubName
        my_uri = urllib.parse.quote(target_uri, '').lower()
        expiry = str(self.get_expiry())
        to_sign = my_uri + '\n' + expiry
        signature = urllib.parse.quote(self.sign_string(to_sign))
        auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
        sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
        return sas_token

### <a name="send-a-notification-using-http-rest-api"></a>Enviar uma notificação utilizando a API de REST de HTTP
Primeiro, permita que utilize definir uma classe que representa uma notificação.

    class Notification:
        def __init__(self, notification_format=None, payload=None, debug=0):
            valid_formats = ['template', 'apple', 'gcm', 'windows', 'windowsphone', "adm", "baidu"]
            if not any(x in notification_format for x in valid_formats):
                raise Exception(
                    "Invalid Notification format. " +
                    "Must be one of the following - 'template', 'apple', 'gcm', 'windows', 'windowsphone', 'adm', 'baidu'")

            self.format = notification_format
            self.payload = payload

            # array with keynames for headers
            # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
            # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
            # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
            self.headers = None

Esta classe é um contentor para um corpo de notificação nativo ou um conjunto de propriedades em caso de uma notificação de modelo, um conjunto de cabeçalhos que contém o formato (plataforma nativa ou modelo) e propriedades específicos da plataforma (como a propriedade de expiração da Apple e cabeçalhos WNS).

Consulte o [documentação de APIs de REST do Notification Hubs](http://msdn.microsoft.com/library/dn495827.aspx) e formatos das plataformas específicas de notificação para todas as opções disponíveis.

Agora com esta classe, pode escrever o envio métodos de notificação dentro do **NotificationHub** classe.

    def make_http_request(self, url, payload, headers):
        parsed_url = urllib.parse.urlparse(url)
        connection = http.client.HTTPSConnection(parsed_url.hostname, parsed_url.port)

        if self.Debug > 0:
            connection.set_debuglevel(self.Debug)
            # adding this querystring parameter gets detailed information about the PNS send notification outcome
            url += self.DEBUG_SEND
            print("--- REQUEST ---")
            print("URI: " + url)
            print("Headers: " + json.dumps(headers, sort_keys=True, indent=4, separators=(' ', ': ')))
            print("--- END REQUEST ---\n")

        connection.request('POST', url, payload, headers)
        response = connection.getresponse()

        if self.Debug > 0:
            # print out detailed response information for debugging purpose
            print("\n\n--- RESPONSE ---")
            print(str(response.status) + " " + response.reason)
            print(response.msg)
            print(response.read())
            print("--- END RESPONSE ---")

        elif response.status != 201:
            # Successful outcome of send message is HTTP 201 - Created
            raise Exception(
                "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

        connection.close()

    def send_notification(self, notification, tag_or_tag_expression=None):
        url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

        json_platforms = ['template', 'apple', 'gcm', 'adm', 'baidu']

        if any(x in notification.format for x in json_platforms):
            content_type = "application/json"
            payload_to_send = json.dumps(notification.payload)
        else:
            content_type = "application/xml"
            payload_to_send = notification.payload

        headers = {
            'Content-type': content_type,
            'Authorization': self.generate_sas_token(),
            'ServiceBusNotification-Format': notification.format
        }

        if isinstance(tag_or_tag_expression, set):
            tag_list = ' || '.join(tag_or_tag_expression)
        else:
            tag_list = tag_or_tag_expression

        # add the tags/tag expressions to the headers collection
        if tag_list != "":
            headers.update({'ServiceBusNotification-Tags': tag_list})

        # add any custom headers to the headers collection that the user may have added
        if notification.headers is not None:
            headers.update(notification.headers)

        self.make_http_request(url, payload_to_send, headers)

    def send_apple_notification(self, payload, tags=""):
        nh = Notification("apple", payload)
        self.send_notification(nh, tags)

    def send_gcm_notification(self, payload, tags=""):
        nh = Notification("gcm", payload)
        self.send_notification(nh, tags)

    def send_adm_notification(self, payload, tags=""):
        nh = Notification("adm", payload)
        self.send_notification(nh, tags)

    def send_baidu_notification(self, payload, tags=""):
        nh = Notification("baidu", payload)
        self.send_notification(nh, tags)

    def send_mpns_notification(self, payload, tags=""):
        nh = Notification("windowsphone", payload)

        if "<wp:Toast>" in payload:
            nh.headers = {'X-WindowsPhone-Target': 'toast', 'X-NotificationClass': '2'}
        elif "<wp:Tile>" in payload:
            nh.headers = {'X-WindowsPhone-Target': 'tile', 'X-NotificationClass': '1'}

        self.send_notification(nh, tags)

    def send_windows_notification(self, payload, tags=""):
        nh = Notification("windows", payload)

        if "<toast>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/toast'}
        elif "<tile>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/tile'}
        elif "<badge>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/badge'}

        self.send_notification(nh, tags)

    def send_template_notification(self, properties, tags=""):
        nh = Notification("template", properties)
        self.send_notification(nh, tags)

Os métodos acima enviam um pedido POST de HTTP para o ponto final de /messages do notification hub, com o corpo correto e cabeçalhos para enviar a notificação.

### <a name="using-debug-property-to-enable-detailed-logging"></a>Utilizando a propriedade de depuração para ativar o registo detalhado
Ativar depuração propriedade ao inicializar o Hub de notificação irá escrever informações de registo detalhado sobre o pedido HTTP e informação de resposta, bem como a mensagem de notificação detalhada enviar o resultado. Adicionámos recentemente esta propriedade denominada [TestSend de Hubs de notificação propriedade](http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx) que devolve informações detalhadas sobre o resultado de envio de notificações. Utilizá-la - inicializar utilizando o seguinte:

    hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)

O pedido de envio de Hub de notificação do URL de HTTP obtém anexado como resultado com uma cadeia de consulta de "teste". 

## <a name="complete-tutorial"></a>Concluir o tutorial
Agora pode concluir o tutorial de introdução ao enviar a notificação de um back-end do Python.

Inicializar o cliente de Notification Hubs (substitua o nome de hub e a cadeia de ligação com as instruções no [tutorial de introdução]):

    hub = NotificationHub("myConnectionString", "myNotificationHubName")

Em seguida, adicione o código de envio dependendo da sua plataforma móvel de destino. Este exemplo também adiciona métodos de nível superiores para ativar as notificações de envio com base na plataforma por exemplo, send_windows_notification para windows; send_apple_notification (para apple) etc. 

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Loja Windows e Windows Phone 8.1 (não Silverlight)
    wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
    hub.send_windows_notification(wns_payload)

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 e 8.1 Silverlight
    hub.send_mpns_notification(toast)

### <a name="ios"></a>iOS
    alert_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_apple_notification(alert_payload)

### <a name="android"></a>Android
    gcm_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_gcm_notification(gcm_payload)

### <a name="kindle-fire"></a>Kindle Fire
    adm_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_adm_notification(adm_payload)

### <a name="baidu"></a>Baidu
    baidu_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_baidu_notification(baidu_payload)

Executar o seu código Python deveria produzir uma notificação de apresentação no seu dispositivo de destino.

## <a name="examples"></a>Exemplos:
### <a name="enabling-debug-property"></a>Ativar a propriedade de depuração
Quando ativar o sinalizador de depuração ao inicializar o NotificationHub, em seguida, verá detalhado pedido HTTP e informação de resposta, bem como NotificationOutcome semelhante ao seguinte em que possa compreender os cabeçalhos HTTP são passados no pedido e resposta de HTTP que foi recebida do Notification Hub:![][1]

Verá detalhadas por exemplo, o resultado de Hub de notificação 

* Quando a mensagem é enviada com êxito para o serviço de notificação Push. 
  
        <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>
* Se ocorreram não destinos encontrados para as notificações push, em seguida, provavelmente pretender ver o seguinte na resposta (o que indica que ocorreram não encontrados para fornecer a notificação provavelmente porque os registos tinham algumas tags sem correspondência de registos)
  
        '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="http://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'

### <a name="broadcast-toast-notification-to-windows"></a>Notificação de alerta para o Windows de difusão
Tenha em atenção os cabeçalhos que obterem enviados quando estiver a enviar uma notificação de alerta de difusão para cliente Windows. 

    hub.send_windows_notification(wns_payload)

![][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Enviar notificação de especificação de uma tag (ou expressão da etiqueta)
Tenha em atenção o cabeçalho de HTTP de etiquetas que é adicionado ao pedido de HTTP (no exemplo abaixo, iremos estiver a enviar a notificação apenas para registos com payload 'Desporto')

    hub.send_windows_notification(wns_payload, "sports")

![][3]

### <a name="send-notification-specifying-multiple-tags"></a>Enviar notificação especificar várias etiquetas
Repare como o cabeçalho de HTTP de etiquetas é alterado quando várias etiquetas são enviadas. 

    tags = {'sports', 'politics'}
    hub.send_windows_notification(wns_payload, tags)

![][4]

### <a name="templated-notification"></a>Notificação transformada em modelo
Tenha em atenção que as alterações de cabeçalho de formato HTTP e corpo do payload é enviada como parte do corpo do pedido HTTP:

**Do lado do cliente - modelo registado**

        var template =
                        @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";

**Lado do servidor - enviar o payload**

        template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
        hub.send_template_notification(template_payload)

![][5]

## <a name="next-steps"></a>Passos Seguintes
Neste tópico, vamos mostrou como criar um cliente de REST de Python simple para os Notification Hubs. A partir daqui, pode:

* Transferir o completo [exemplo de wrapper do Python REST], que contém o código acima.
* Saber mais sobre os Notification Hubs funcionalidade de etiquetagem o [tutorial notícias de última hora]
* Saber mais sobre a funcionalidade de modelos de Hubs de notificação no [tutorial Localizing notícias de última hora]

<!-- URLs -->
[exemplo de wrapper do Python REST]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[tutorial de introdução]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[tutorial notícias de última hora]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[tutorial Localizing notícias de última hora]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png

