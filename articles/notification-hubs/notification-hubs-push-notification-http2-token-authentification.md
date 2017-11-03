---
title: "Autenticação baseada em tokens (HTTP/2) para APNS no Notification Hubs do Azure | Microsoft Docs"
description: "Este tópico explica como tirar partido da autenticação de token novo para APNS"
services: notification-hubs
documentationcenter: .net
author: kpiteira
manager: erikre
editor: 
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 05/17/2017
ms.author: kapiteir
ms.openlocfilehash: 5a21bcd9f12fc3f96b17a556ba15526c35ababe2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="token-based-http2-authentication-for-apns"></a>Autenticação baseada em tokens (HTTP/2) para APNS
## <a name="overview"></a>Descrição geral
Este artigo fornece detalhes sobre como utilizar o novo protocolo de APNS HTTP/2 com a autenticação baseada em token.

As principais vantagens de utilizar o novo protocolo incluem:
-   Relativamente a geração de token é hassle livre (em comparação comparada certificados)
-   Não existem mais datas de expiração – são no controlo dos seus tokens de autenticação e a respetiva revogação
-   Payloads podem agora ser até 4 KB
- Comentários síncrono
-   Está no protocolo mais recente da Apple – certificados continuam a utilizar o protocolo binário, que está marcado para preterição

Este mecanismo de novo a utilizar pode ser efetuada em dois passos dentro de alguns minutos:
1.  Obter as informações necessárias do portal de contas de programador da Apple
2.  Configurar o notification hub com novas informações

Hubs de notificação está tudo pronto a utilizar o novo sistema de autenticação com APNS. 

Tenha em atenção que se tiver migrado com as credenciais de certificado do APNS:
- as propriedades de token substituir o certificado no nosso sistema
- mas a aplicação continua a receber notificações de forma totalmente integrada.

## <a name="obtaining-authentication-information-from-apple"></a>Obter informações de autenticação da Apple
Para ativar a autenticação baseada em tokens, terá as seguintes propriedades da sua conta de programador da Apple:
### <a name="key-identifier"></a>Identificador da chave
O identificador da chave pode ser obtido a partir da página "Chaves" na sua conta de programador da Apple

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier--application-name"></a>Identificador de aplicação & nome da aplicação
O nome da aplicação está disponível através da página de IDs de aplicações na conta de programador. 
![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

O identificador da aplicação está disponível através da página de detalhes de associação na conta de programador.
![](./media/notification-hubs-push-notification-http2-token-authentification/app-id.png)


### <a name="authentication-token"></a>Token de autenticação
O token de autenticação pode ser transferido depois de gerar um token para a sua aplicação. Para obter detalhes sobre como gerar este token, consulte [documentação de programador da Apple](http://help.apple.com/xcode/mac/current/#/dev11b059073?sub=dev1eb5dfe65).

## <a name="configuring-your-notification-hub-to-use-token-based-authentication"></a>Configurar o notification hub para utilizar a autenticação baseada em tokens
### <a name="configure-via-the-azure-portal"></a>Configurar através do portal do Azure
Para ativar a autenticação baseada em token no portal, inicie sessão no portal do Azure e aceda ao seu Hub de notificação > Serviços de notificação > painel do APNS. 

Há uma nova propriedade – *modo de autenticação*. Selecionar Token permite-lhe atualizar o seu hub com todas as propriedades de token relevantes.

![](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

- Introduza as propriedades obtidos a partir da sua conta de programador da Apple 
- Escolha o modo de aplicação (produção ou Sandbox) 
- Clique em Guardar para atualizar as credenciais APNS. 

### <a name="configure-via-management-api-rest"></a>Configurar através da gestão de API (REST)

Pode utilizar o nosso [APIs de gestão](https://msdn.microsoft.com/library/azure/dn495827.aspx) para atualizar o hub de notificação para utilizar a autenticação baseada em tokens.
Dependendo se a aplicação que estiver a configurar é uma aplicação de Sandbox ou de produção (especificada na sua conta de programador Apple), utilize um dos pontos finais correspondentes:

- Ponto final de sandbox: [3/https://api.development.push.apple.com:443/dispositivo](https://api.development.push.apple.com:443/3/device)
- Ponto final de produção: [3/https://api.push.apple.com:443/dispositivo](https://api.push.apple.com:443/3/device)

> [!IMPORTANT]
> Autenticação baseada em tokens requer uma versão de API do: **2017-04 ou posterior**.
> 
> 

Eis um exemplo de um pedido PUT para atualizar um concentrador com a autenticação baseada em tokens:


        PUT https://{namespace}.servicebus.windows.net/{Notification Hub}?api-version=2017-04
          "Properties": {
            "ApnsCredential": {
              "Properties": {
                "KeyId": "<Your Key Id>",
                "Token": "<Your Authentication Token>",
                "AppName": "<Your Application Name>",
                "AppId": "<Your Application Id>",
                "Endpoint":"<Sandbox/Production Endpoint>"
              }
            }
          }
        

### <a name="configure-via-the-net-sdk"></a>Configurar através do SDK do .NET
Pode configurar o seu hub a utilizar a autenticação baseada em token utilizando a nossa [SDK de cliente mais recente](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/1.0.8). 

Eis um exemplo de código que ilustra a utilização correta:


        NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
        string token = "YOUR TOKEN HERE";
        string keyId = "YOUR KEY ID HERE";
        string appName = "YOUR APP NAME HERE";
        string appId = "YOUR APP ID HERE";
        NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
        desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
        desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
        nm.UpdateNotificationHubAsync(desc);

## <a name="reverting-to-using-certificate-based-authentication"></a>Reverter para utilizar a autenticação baseada em certificado
Pode reverter em qualquer altura para utilizar a autenticação baseada em certificados utilizando qualquer método anterior e passar o certificado em vez das propriedades de token. Essa ação substitui as credenciais armazenadas anteriormente.
