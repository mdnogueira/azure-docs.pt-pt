---
title: "Descrição geral do modelo de autenticação e segurança de Event Hubs do Azure | Microsoft Docs"
description: "Event Hubs autenticação e segurança descrição geral do modelo."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 93841e30-0c5c-4719-9dc1-57a4814342e7
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2017
ms.author: sethm;clemensv
ms.openlocfilehash: ffab5b058420d61be17d386a46a29391d5728859
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="event-hubs-authentication-and-security-model-overview"></a>Event Hubs autenticação e segurança descrição geral do modelo

O modelo de segurança de Event Hubs do Azure cumpre os seguintes requisitos:

* Apenas os clientes que apresentam credenciais válidas podem enviar dados para um hub de eventos.
* Um cliente não é possível representar outro cliente.
* Um cliente não autorizado pode ser impedido de enviar dados para um hub de eventos.

## <a name="client-authentication"></a>Autenticação de cliente

O modelo de segurança de Event Hubs baseia-se numa combinação de [assinatura de acesso partilhado (SAS)](../service-bus-messaging/service-bus-sas.md) tokens e *publicadores de eventos*. Um publicador de eventos define um ponto de final virtual para um hub de eventos. O publicador só pode ser utilizado para enviar mensagens para um hub de eventos. Não é possível receber mensagens de um publicador.

Normalmente, um hub de eventos emprega um publicador por cliente. Todas as mensagens que são enviadas para qualquer um dos publicadores de um hub de eventos são colocados em fila dentro desse hub de eventos. Editores de ativar a limitação e controlo de acesso detalhado.

Cada cliente dos Event Hubs é atribuído um token exclusivo, que é carregado para o cliente. Os tokens são produzidos de forma a que cada token exclusivo concede acesso a um publicador exclusivo diferente. Um cliente que tiver um token só pode enviar para um publicador, mas nenhum outro publicador. Se vários clientes partilham o mesmo token, em seguida, cada um deles partilhas de um publicador.

Apesar de não recomendado, é possível equipar dispositivos com tokens que conceda acesso direto para um hub de eventos. Qualquer dispositivo que detém este token pode enviar mensagens diretamente para esse hub de eventos. Essa um dispositivo não serão sujeitos a limitação. Além disso, o dispositivo não pode ser blacklisted de enviar para esse hub de eventos.

Todos os tokens são assinados com uma chave SAS. Normalmente, todos os tokens são assinados com a mesma chave. Os clientes não têm conhecimento da chave; Isto impede que outros clientes de tokens de fabrico.

### <a name="create-the-sas-key"></a>Criar a chave SAS

Ao criar um espaço de nomes de Event Hubs, o serviço gera automaticamente uma chave SAS de 256 bits denominada **RootManageSharedAccessKey**. Esta regra tem um associado par de chaves primárias e secundárias que conceda envio, escutar e gerir direitos para o espaço de nomes. Também pode criar chaves adicionais. Recomenda-se que produz uma chave que concede enviar permissões para o hub de eventos específico. Para o resto deste tópico, presume-se com o nome desta chave **EventHubSendKey**.

O exemplo seguinte cria uma chave apenas de envio, ao criar o hub de eventos:

```csharp
// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, namespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create event hub with a SAS rule that enables sending to that event hub
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, eventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);
```

### <a name="generate-tokens"></a>Gerar tokens

Pode gerar tokens utilizando a chave SAS. Tem a produzir apenas um token por cliente. Em seguida, podem ser produzidos tokens utilizando o método seguinte. Todos os tokens são gerados utilizando o **EventHubSendKey** chave. Cada token é atribuído um URI exclusivo.

```csharp
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

Quando chamar este método, o URI deve ser especificado como `//<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`. Para todos os tokens, o URI é idêntico, com exceção do `PUBLISHER_NAME`, que deve ser diferente para cada token. Idealmente, `PUBLISHER_NAME` representa o ID de cliente que recebe esse token.

Este método gera um token com a estrutura seguinte:

```csharp
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

A hora de expiração do token é especificada em segundos, de 1 de Janeiro de 1970. Segue-se um exemplo de um token:

```csharp
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

Normalmente, os tokens têm um tempo de vida que se assemelha ou excede o tempo de vida do cliente. Se o cliente tem a capacidade de obter um novo token, tokens com um tempo de vida mais curto podem ser utilizados.

### <a name="sending-data"></a>Envio de dados

Quando os tokens de tem sido criados, cada cliente é aprovisionado com a suas próprias token exclusivo.

Quando o cliente envia dados para um hub de eventos, etiquetas o pedido de envio com o token. Para impedir que um atacante de escutas e roubo o token, a comunicação entre o cliente e o hub de eventos tem de ocorrer num canal encriptado.

### <a name="blacklisting-clients"></a>Clientes blacklisting

Se um token é roubado por um atacante, o atacante pode representar o cliente cujo token foi roubado. Blacklisting um cliente compõe esse cliente não utilizável até receber um novo token que utiliza um editor diferente.

## <a name="authentication-of-back-end-applications"></a>Autenticação de aplicações back-end

Para autenticar as aplicações de back-end que consomem dados gerados por clientes de Event Hubs, os Event Hubs utiliza um modelo de segurança que é semelhante ao modelo que é utilizado para tópicos do Service Bus. Um grupo de consumidores de Event Hubs é equivalente a uma subscrição para um tópico de barramento de serviço. Um cliente pode criar um grupo de consumidores, se o pedido para criar o grupo de consumidores é acompanhado por um token que concede gerir privilégios para o hub de eventos ou para o espaço de nomes ao qual pertence o hub de eventos. Um cliente tem permissão para consumir dados a partir de um grupo de consumidores, se o pedido de receção é acompanhado por um token que atribui direitos de receção nesse grupo de consumidores, o hub de eventos ou o espaço de nomes ao qual pertence o hub de eventos.

A versão atual do barramento de serviço não suporta regras SAS para subscrições individuais. O mesmo se aplica a grupos de consumidores de Event Hubs. SAS será possível adicionar suporte para ambas as funcionalidades no futuro.

Na ausência de autenticação SAS para grupos de consumidores individuais, pode utilizar as chaves SAS para proteger todos os grupos de consumidores com uma chave comuns. Esta abordagem permite que uma aplicação consumir dados a partir de qualquer um dos grupos de consumidores de um hub de eventos.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os Event Hubs, consulte os tópicos seguintes:

* [Descrição geral dos Hubs de Eventos]
* [Descrição geral de assinaturas de acesso partilhado]
* [Aplicações de exemplo que utilizam Hubs de Eventos]

[Descrição geral dos Hubs de Eventos]: event-hubs-what-is-event-hubs.md
[Aplicações de exemplo que utilizam Hubs de Eventos]: https://github.com/Azure/azure-event-hubs/tree/master/samples
[Descrição geral de assinaturas de acesso partilhado]: ../service-bus-messaging/service-bus-sas.md

