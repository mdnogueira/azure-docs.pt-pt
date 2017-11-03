---
title: "Utilização de recursos do fornecedor API | Microsoft Docs"
description: "Referência para a utilização de recursos API, que obtém as informações de utilização de pilha do Azure"
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: b6055923-b6a6-45f0-8979-225b713150ae
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: alfredop
ms.openlocfilehash: 0c45ce3bc93945ed8700464beebabcda07e8d77c
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="provider-resource-usage-api"></a>API de utilização do recurso do fornecedor
O termo *fornecedor* aplica-se para o administrador de serviço e para quaisquer fornecedores de delegado. Operadores de pilha do Azure e os fornecedores de delegado podem utilizar a API de utilização do fornecedor para ver a utilização dos respetivos inquilinos diretas. Por exemplo, conforme mostrado no diagrama, P0 pode chamar o fornecedor de API para obter informações de utilização sobre do P1 e utilização direta do P2 e P1 podem chamar para informações de utilização sobre P3 e P4.

![Modelo concetual da hierarquia de fornecedor](media/azure-stack-provider-resource-api/image1.png)

## <a name="api-call-reference"></a>Referência da chamada de API
### <a name="request"></a>Pedir
O pedido obtém os detalhes de consumo para as subscrições pedidas e para o intervalo de tempo de pedido. Não há nenhum corpo do pedido.

Esta utilização API é um fornecedor de API, pelo que o chamador tem de ser atribuído uma função de proprietário, Contribuidor ou leitor na subscrição do fornecedor.

| **Método** | **URI do pedido** |
| --- | --- |
| INTRODUÇÃO |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}& subscriberId = {sub1.1} & api-version = 2015-06-01-preview & continuationToken = {token value} |

### <a name="arguments"></a>Argumentos
| **Argumento** | **Descrição** |
| --- | --- |
| *armendpoint* |Azure ponto final do Gestor de recursos do ambiente de pilha do Azure. A Convenção de pilha do Azure é que o nome do ponto final do Azure Resource Manager está no formato `https://adminmanagement.{domain-name}`. Por exemplo, para o kit de desenvolvimento, se o nome de domínio for *local.azurestack.external*, em seguida, o ponto final do Gestor de recursos é `https://adminmanagement.local.azurestack.external`. |
| *subId* |ID de subscrição do utilizador que efetua a chamada. |
| *reportedStartTime* |A hora de início da consulta. O valor para *DateTime* deve estar no início da hora, por exemplo, 13:00 e na hora Universal Coordenada (UTC). Para agregação diária, defina este valor como meia-noite do UTC. O formato é *escape* ISO 8601. Por exemplo, *2015-06-16T18% 3a53% 3a11% 2b00% 3a00Z*, em que a vírgula é escape para *% 3a* e o sinal de adição é escape para *% 2b* para que seja URI amigável. |
| *reportedEndTime* |Hora de fim da consulta. As restrições que se aplicam ao *reportedStartTime* também se aplicam para este argumento. O valor para *reportedEndTime* não pode estar no futuro ou a data atual. Se estiver, o resultado é definido para "processar não concluída." |
| *aggregationGranularity* |O parâmetro opcional que tem dois valores discretos de potenciais: diários e hora a hora. Como os valores sugerem, um devolve os dados na granularidade diária e o outro é uma resolução por hora. A diária é a opção predefinida. |
| *subscriberId* |ID da subscrição. Para obter dados filtrados, é necessário o ID de subscrição de um inquilino direto do fornecedor. Não se for especificado nenhum parâmetro de ID de subscrição, a chamada devolve dados de utilização para os inquilinos direta do fornecedor. |
| *versão de API* |Versão do protocolo que é utilizado para efetuar este pedido. Este valor é definido como *2015-06-01-preview*. |
| *continuationToken* |Obter o token da última chamada para o fornecedor de API de utilização. Este token é necessário quando uma resposta é maior do que 1000 linhas e funciona como um marcador para conhecer o progresso. Se o token não estiver presente, os dados são obtidos a partir do início do dia ou a hora, com base na granularidade transmitido. |

### <a name="response"></a>Resposta
OBTER /subscriptions/sub1/providers/Microsoft.Commerce/subscriberUsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00 & reportedEndTime = 2015-06-01T00% 3a00% 3a00% 2b00% 3a00 & aggregationGranularity = diariamente & subscriberId = sub1.1 & api-version = 1.0

```json
{
"value": [
{

"id":
"/subscriptions/sub1.1/providers/Microsoft.Commerce/UsageAggregate/sub1.1-

meterID1",
"name": "sub1.1-meterID1",
"type": "Microsoft.Commerce/UsageAggregate",

"properties": {
"subscriptionId":"sub1.1",
"usageStartTime": "2015-03-03T00:00:00+00:00",
"usageEndTime": "2015-03-04T00:00:00+00:00",
"instanceData":"{\"Microsoft.Resources\":{\"resourceUri\":\"resourceUri1\",\"location\":\"Alaska\",\"tags\":null,\"additionalInfo\":null}}",
"quantity":2.4000000000,
"meterId":"meterID1"

}
},

…
```

### <a name="response-details"></a>Detalhes de resposta
| **Argumento** | **Descrição** |
| --- | --- |
| *ID* |ID exclusivo do agregado de utilização. |
| *nome* |Nome do agregado de utilização. |
| *tipo* |Definição do recurso. |
| *subscriptionId* |Identificador de subscrição de utilizador a pilha do Azure. |
| *usageStartTime* |UTC hora de início do registo de utilização a que pertence este agregado de utilização.|
| *usageEndTime* |Hora de fim de UTC do registo de utilização a que pertence este agregado de utilização. |
| *instanceData* |Pares chave-valor dos detalhes da instância (por um novo formato):<br> *resourceUri*: totalmente qualificado ID de recurso, o que inclui os grupos de recursos e o nome da instância. <br> *localização*: região no qual este serviço foi executado. <br> *etiquetas*: os sinalizadores de recursos que são especificados pelo utilizador. <br> *additionalInfo*: mais detalhes sobre o recurso que foi consumido, por exemplo, o tipo de imagem ou versão do SO. |
| *quantidade* |Quantidade de consumo de recursos que ocorreram neste período de tempo. |
| *meterId* |ID exclusivo para o recurso que foi utilizado (também denominado *ResourceID*). |

## <a name="next-steps"></a>Passos seguintes
[Utilização de recursos de inquilino referência da API](azure-stack-tenant-resource-usage-api.md)

[FAQ relacionados com a utilização](azure-stack-usage-related-faq.md)
