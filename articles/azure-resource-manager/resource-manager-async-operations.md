---
title: "As operações assíncronas do Azure | Microsoft Docs"
description: "Descreve como controlar as operações assíncronas no Azure."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2017
ms.author: tomfitz
ms.openlocfilehash: 9fe3d98cd345aae45722295b6c1b7fc3e9036e95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="track-asynchronous-azure-operations"></a>Controlar assíncronas operações do Azure
Algumas operações de REST de Azure executam de forma assíncrona porque a operação não é possível concluir o mais rapidamente. Este tópico descreve como monitorizar o estado de operações assíncronas através de valores obtidos na resposta.  

## <a name="status-codes-for-asynchronous-operations"></a>Códigos de estado de operações assíncronas
Uma operação assíncrona inicialmente devolve um código de estado HTTP do:

* 201 (criado)
* 202 (aceite) 

Quando a operação for concluída com êxito, devolverá uma:

* 200 (OK)
* 204 (nenhum conteúdo) 

Consulte o [documentação da REST API](/rest/api/) para ver as respostas para a operação a que se encontram em execução. 

## <a name="monitor-status-of-operation"></a>Monitor de estado de funcionamento
As operações REST assíncronas devolvem valores de cabeçalho, o que utilizar para determinar o estado da operação. Existem potencialmente três os valores de cabeçalho para examinar:

* `Azure-AsyncOperation`-URL para verificar o estado da operação em curso. Se a operação devolve este valor, utilizá-lo (em vez de localização) sempre controlar o estado da operação.
* `Location`-URL para determinar quando uma operação foi concluída. Utilize este valor apenas quando não é devolvido AsyncOperation do Azure.
* `Retry-After`-O número de segundos a aguardar antes de a verificar o estado da operação assíncrona.

No entanto, não cada operação assíncrona devolve todos estes valores. Por exemplo, terá de avaliar o valor do cabeçalho AsyncOperation do Azure para uma operação e o valor de cabeçalho de localização para outra operação. 

Obter os valores de cabeçalho como seria obter qualquer valor de cabeçalho para um pedido. Por exemplo, em c#, é possível obter o valor do cabeçalho de uma `HttpWebResponse` objeto com o nome `response` com o seguinte código:

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

## <a name="azure-asyncoperation-request-and-response"></a>Azure AsyncOperation pedido e resposta

Para obter o estado da operação assíncrona, envie um pedido GET para o URL no valor do cabeçalho AsyncOperation do Azure.

O corpo da resposta desta operação contém informações sobre a operação. O exemplo seguinte mostra os valores possíveis devolvidos pela operação de:

```json
{
    "id": "{resource path from GET operation}",
    "name": "{operation-id}", 
    "status" : "Succeeded | Failed | Canceled | {resource provider values}", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "percentComplete": {double between 0 and 100 },
    "properties": {
        /* Specific resource provider values for successful operations */
    },
    "error" : { 
        "code": "{error code}",  
        "message": "{error description}" 
    }
}
```

Apenas `status` é devolvido para todas as respostas. O objeto de erro é devolvido quando o estado com falhas ou cancelado. Todos os outros valores são opcionais; Por conseguinte, a resposta que receberá poderá ser diferente do exemplo.

## <a name="provisioningstate-values"></a>valores de provisioningState

As operações que criar, atualizarem ou eliminar (PUT, PATCHES, eliminação) um recurso normalmente devolver um `provisioningState` valor. Quando concluir uma operação, é devolvido um de três valores seguintes: 

* Bem-sucedido
* Falha
* Cancelado

Todos os outros valores indicam que a operação ainda está em execução. O fornecedor de recursos pode devolver um valor personalizado que indica o estado. Por exemplo, poderá receber **aceites** quando o pedido é recebido e em execução.

## <a name="example-requests-and-responses"></a>Exemplo de pedidos e respostas

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Iniciar a máquina virtual (202 com AsyncOperation do Azure)
Este exemplo mostra como determinar o estado de **iniciar** operação para máquinas virtuais. O pedido inicial é o seguinte formato:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

Devolve o código de estado 202. Entre os valores de cabeçalho, consulte:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Para verificar o estado da operação assíncrona, enviar outro pedido para esse URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

O corpo da resposta contém o estado da operação:

```json
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>Implementar recursos (201 com AsyncOperation do Azure)

Este exemplo mostra como determinar o estado de **implementações** operação para a implementação de recursos no Azure. O pedido inicial é o seguinte formato:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2016-09-01
```

Devolve o código de estado 201. O corpo da resposta inclui:

```json
"provisioningState":"Accepted",
```

Entre os valores de cabeçalho, consulte:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Para verificar o estado da operação assíncrona, enviar outro pedido para esse URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

O corpo da resposta contém o estado da operação:

```json
{"status":"Running"}
```

Quando a implementação estiver concluída, a resposta contém:

```json
{"status":"Succeeded"}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Criar conta de armazenamento (202 com a localização e tente novamente após)

Este exemplo mostra como determinar o estado do **criar** operação para contas de armazenamento. O pedido inicial é o seguinte formato:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2016-01-01
```

E corpo do pedido contém as propriedades para a conta de armazenamento:

```json
{ "location": "South Central US", "properties": {}, "sku": { "name": "Standard_LRS" }, "kind": "Storage" }
```

Devolve o código de estado 202. Entre os valores de cabeçalho, consulte os seguintes dois valores:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
Retry-After: 17
```

Depois de aguardar o número de segundos o estado da operação assíncrona a verificação de repetição-após, especificada no enviando outro pedido para esse URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

Se o pedido ainda está em execução, receberá um código de estado 202. Se o pedido tiver sido concluída, a receber um código de estado 200 e o corpo da resposta contém as propriedades da conta de armazenamento que foi criado.

## <a name="next-steps"></a>Passos seguintes

* Para obter documentação sobre cada operação REST, consulte [documentação da REST API](/rest/api/).
* Para obter informações sobre a gestão de recursos através da API de REST do Resource Manager, consulte [utilizando a API de REST do Resource Manager](resource-manager-rest-api.md).
* Para obter informações sobre a implementação de modelos através da API de REST do Resource Manager, consulte [implementar recursos com modelos do Resource Manager e a API de REST do Resource Manager](resource-group-template-deploy-rest.md).