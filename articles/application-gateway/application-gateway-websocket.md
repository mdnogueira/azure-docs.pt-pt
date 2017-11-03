---
title: "Suporte de WebSocket no Gateway de aplicação do Azure | Microsoft Docs"
description: "Esta página fornece uma descrição geral sobre o suporte de WebSocket do Gateway de aplicação."
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: 8968dac1-e9bc-4fa1-8415-96decacab83f
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: amsriva
ms.openlocfilehash: 75b06ddd02da231b7813c609c848c75e42116da5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Descrição geral do suporte de WebSocket no Gateway de aplicação

Gateway de aplicação fornece suporte nativo para WebSocket em todos os tamanhos de gateway. Não há nenhuma definição configurável pelo utilizador para seletivamente ativar ou desativar o suporte de WebSocket. 

Protocolo de WebSocket padronizado nas [RFC6455](https://tools.ietf.org/html/rfc6455) permite uma comunicação completa duplex entre um servidor e um cliente através de uma ligação de TCP de longa execução. Esta funcionalidade permite uma mais interativa a comunicação entre o servidor web e o cliente, o que pode ser bidirecional sem a necessidade de consulta como implementações necessárias no baseado em HTTP. WebSocket baixa tem custos gerais ao contrário de HTTP e pode reutilizar a mesma ligação TCP de vários/respostas de pedidos que resulta numa utilização mais eficiente dos recursos. Protocolos de WebSocket foram concebidos para funcionar através de tradicionais HTTP as portas 80 e 443.

Pode continuar a utilizar um serviço de escuta HTTP padrão na porta 80 ou 443 para receber tráfego de WebSocket. Tráfego de WebSocket, em seguida, é direcionado para o servidor de back-end ativada de WebSocket utilizando o conjunto de back-end adequado conforme especificado nas regras de gateway de aplicação. O servidor de back-end tem de responder as sondas de gateway de aplicação, que são descritas no [descrição geral de sonda de estado de funcionamento](application-gateway-probe-overview.md) secção. Sondas de estado de funcionamento do gateway de aplicação só são HTTP/HTTPS. Cada servidor de back-end tem de responder para as pesquisas HTTP para o gateway de aplicação encaminhar o tráfego de WebSocket para o servidor.

## <a name="listener-configuration-element"></a>Elemento de configuração do serviço de escuta

Um serviço de escuta HTTP existente pode ser utilizado para suportar o tráfego de WebSocket. Segue-se um fragmento de um elemento de httpListeners de um ficheiro de modelo de exemplo. Terá de serviços de escuta de HTTP e HTTPS para suportar o WebSocket e proteger o tráfego de WebSocket. Da mesma forma, pode utilizar o [portal](application-gateway-create-gateway-portal.md) ou [PowerShell](application-gateway-create-gateway-arm.md) para criar um gateway de aplicação com os serviços de escuta na porta 80/443 para suportar o tráfego de WebSocket.

```json
"httpListeners": [
        {
            "name": "appGatewayHttpsListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/DefaultFrontendPublicIP"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort443'"
                },
                "Protocol": "Https",
                "SslCertificate": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/sslCertificates/appGatewaySslCert1'"
                },
            }
        },
        {
            "name": "appGatewayHttpListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/appGatewayFrontendIP'"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort80'"
                },
                "Protocol": "Http",
            }
        }
    ],
```

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>Configuração da regra BackendAddressPool, BackendHttpSetting e encaminhamento

Um BackendAddressPool é utilizado para definir um conjunto de back-end com servidores de WebSocket ativada. O backendHttpSetting está definida com uma back-end a porta 80 e 443. As propriedades de afinidade com base em cookies e requestTimeouts não são relevantes para o tráfego de WebSocket. Não há nenhuma alteração necessária na regra de encaminhamento, 'Basic' é utilizada para associar o serviço de escuta adequado para o conjunto de endereços de back-end correspondente. 

```json
"requestRoutingRules": [{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpsListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}, {
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }

    }
}]
```

## <a name="websocket-enabled-backend"></a>Back-end de WebSocket ativada

Back-end tem de ter um servidor de web HTTP/HTTPS em execução no configurada porta (normalmente, 80/443) para WebSocket funcione. Este requisito é porque protocolo WebSocket requer o handshake inicial estar HTTP com a atualização para o protocolo de WebSocket como um campo de cabeçalho. Segue-se um exemplo de um cabeçalho:

```
    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: http://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13
```

Outra razão para isto é que sonda de estado de funcionamento de back-end de gateway de aplicação suporta apenas protocolos HTTP e HTTPS. Se o servidor de back-end não responder para HTTP ou HTTPS sondas, é colocado fora do conjunto back-end.

## <a name="next-steps"></a>Passos seguintes

Depois de aprender mais sobre o suporte de WebSocket, aceda a [criar um gateway de aplicação](application-gateway-create-gateway.md) começar com um WebSocket ativar a aplicação web.

