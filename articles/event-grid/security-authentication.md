---
title: "Autenticação e segurança de grelha de eventos do Azure"
description: Descreve os conceitos e grelha de eventos do Azure.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 09/18/2017
ms.author: babanisa
ms.openlocfilehash: e2f48b6e72072ce6bf019b3adc138ae83c162f25
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="event-grid-security-and-authentication"></a>Segurança de grelha de eventos e autenticação 

Grelha de eventos do Azure tem três tipos de autenticação:

* Subscrições de eventos
* Publicação de eventos
* Entrega de eventos do WebHook

## <a name="webhook-event-delivery"></a>Entrega de eventos do WebHook

Webhooks são uma das várias formas para receber eventos em tempo real da grelha de eventos do Azure. Sempre que há um novo evento pronto para ser entregue, o Webhook de grelha de evento implementa um pedido HTTP para o ponto final de HTTP configurado com o evento no corpo.

Quando registar o próprio ponto final de WebHook a grelha de evento, envia-lhe um pedido POST com um código de validação simples para provar a propriedade de ponto final. A aplicação tem de responder ao echoing novamente o código de validação. Grelha de eventos não fornecer eventos para os pontos finais de WebHook que não passaram a validação.

### <a name="validation-details"></a>Detalhes de validação

* No momento da criação/atualização da subscrição do evento, o evento grelha mensagens um evento de "SubscriptionValidationEvent" para o ponto final de destino.
* O evento contém um valor de cabeçalho "Tipo de evento Aeg: SubscriptionValidation".
* O corpo de evento tem o mesmo esquema que outros eventos de grelha de eventos.
* Os dados do evento incluem uma propriedade de "validationCode" com uma cadeia gerada aleatoriamente. Por exemplo, "validationCode: acb13...".

Um exemplo SubscriptionValidationEvent é apresentado no exemplo seguinte:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2017-08-06T22:09:30.740323Z"
}]
```

Para provar a propriedade de ponto final, escreverá novamente o código de validação na propriedade validationResponse, conforme mostrado no exemplo seguinte:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Por fim, é importante ter em atenção que a grelha de eventos do Azure suporta apenas pontos finais de webhook HTTPS.

## <a name="event-subscription"></a>Subscrição de evento

Para subscrever um evento, tem de ter o **Microsoft.EventGrid/EventSubscriptions/Write** permissão no recurso necessário. Tem esta permissão porque estiver a escrever uma nova subscrição no âmbito do recurso. O recurso necessário difere com base em se estão a subscrição para um tópico de sistema ou um tópico personalizado. Ambos os tipos são descritos nesta secção.

### <a name="system-topics-azure-service-publishers"></a>Tópicos de sistema (editores de serviço do Azure)

Tópicos de sistema, necessita de permissão para escrever uma nova subscrição de eventos no âmbito do recurso publicar o evento. O formato do recurso é:`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Por exemplo subscrever um evento de uma conta de armazenamento com o nome **myacct**, é preciso permissão Microsoft.EventGrid/EventSubscriptions/Write em:`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Tópicos personalizados

Tópicos personalizado, terá permissão para escrever uma nova subscrição de eventos no âmbito do tópico grelha de eventos. O formato do recurso é:`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Por exemplo subscrever um tópico personalizado denominado **mytopic**, é preciso permissão Microsoft.EventGrid/EventSubscriptions/Write em:`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="topic-publishing"></a>Publicação de tópico

Tópicos sobre utiliza a assinatura de acesso partilhado (SAS) ou autenticação de chave. Recomendamos SAS, mas a autenticação de chave fornece programação simple, não sendo compatível com muitos editores de webhook existente. 

Incluir o valor de autenticação no cabeçalho de HTTP. Para SAS, utilize **token de sas aeg** para o valor do cabeçalho. Para autenticação de chave, utilize **chave de sas aeg** para o valor do cabeçalho.

### <a name="key-authentication"></a>Autenticação de chave

Autenticação de chave é a forma mais simples de autenticação. Utilize o formato:`aeg-sas-key: <your key>`

Por exemplo, transmitir uma chave com:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Tokens SAS

Os tokens SAS de grelha de eventos incluem o recurso, uma hora de expiração e uma assinatura. O formato do SAS token é: `r={resource}&e={expiration}&s={signature}`.

O recurso é o caminho para o tópico para o qual está a enviar eventos. Por exemplo, é um caminho de recurso válido:`https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

Gerar a assinatura de uma chave.

Por exemplo, um válido **token de sas aeg** valor é:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

O exemplo seguinte cria um token SAS para utilização com grelha de evento:

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

## <a name="management-access-control"></a>Controlo de acesso de gestão

Grelha de eventos do Azure permite-lhe controlar o nível de acesso aos diferentes utilizadores várias operações de gestão, tais como as subscrições de eventos de lista, criar novos, e gerar as chaves. Utiliza a grelha de eventos função de acesso baseado em verificar (RBAC do Azure).

### <a name="operation-types"></a>Tipos de operação

Grelha de eventos do Azure suporta as seguintes ações:

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

As últimas três operações devolvem informações potencialmente secretas, que obtém filtradas normais operações de leitura. É recomendado para que possa restringir o acesso a estas operações. Funções personalizadas podem ser criadas utilizando [Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md), [Interface de linha de comandos do Azure (CLI)](../active-directory/role-based-access-control-manage-access-azure-cli.md)e o [REST API](../active-directory/role-based-access-control-manage-access-rest.md).

### <a name="enforcing-role-based-access-check-rbac"></a>Impor a função com base em verificação de acesso (RBAC)

Utilize os seguintes passos para impor o RBAC para diferentes utilizadores:

#### <a name="create-a-custom-role-definition-file-json"></a>Criar um ficheiro de definição de função personalizada (. JSON)

Seguem-se as definições de funções de grelha de eventos de exemplo que permitem aos utilizadores efetuar diferentes conjuntos de ações.

**EventGridReadOnlyRole.json**: permitir apenas operações só de leitura.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.json**: permitir ações post restrito, mas não o permitir ações de eliminação.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.json**: permite que todas as ações de grelha de eventos.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

#### <a name="create-and-assign-custom-role-with-azure-cli"></a>Criar e atribuir uma função personalizada com o CLI do Azure

Para criar uma função personalizada, utilize:

```azurecli
az role definition create --role-definition @<file path>
```

Para atribuir a função a um utilizador, utilize:

```azurecli
az role assignment create --assignee <user name> --role "<name of role>"
```

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à grelha de eventos, consulte [sobre eventos grelha](overview.md)
