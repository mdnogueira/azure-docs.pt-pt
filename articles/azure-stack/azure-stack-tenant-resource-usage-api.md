---
title: "API de utilização de recursos de inquilino | Microsoft Docs"
description: "Referência de API, a utilização de recursos que obter informações de utilização de pilha do Azure."
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: b9d7c7ee-e906-4978-92a3-a2c52df16c36
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2016
ms.author: alfredop
ms.openlocfilehash: f2eaf1c766d6c86741cf0fd561c131eacb34d782
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tenant-resource-usage-api"></a>API de utilização de recursos de inquilino
Um inquilino pode utilizar a API de inquilino para ver os dados de utilização de recursos do inquilino. Esta API está consistente com a API de utilização do Azure (atualmente em pré-visualização privada).

Pode utilizar o cmdlet Windows PowerShell **Get-UsageAggregates** para obter dados de utilização semelhante no Azure.

## <a name="api-call"></a>Chamada de API
### <a name="request"></a>Pedir
O pedido obtém os detalhes de consumo para as subscrições pedidas e para o intervalo de tempo de pedido. Não há nenhum corpo do pedido.

| **Método** | **URI do pedido** |
| --- | --- |
| INTRODUÇÃO |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/usageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&api-version= 2015-06-01-preview & continuationToken = {token value} |

### <a name="arguments"></a>Argumentos
| **Argumento** | **Descrição** |
| --- | --- |
| *Armendpoint* |Azure ponto final do Gestor de recursos do ambiente de pilha do Azure. A Convenção de pilha do Azure é que o nome do ponto final do Gestor de recursos do Azure está no formato `https://management.{domain-name}`. Por exemplo, para o kit de desenvolvimento, o nome de domínio é local.azurestack.external, então, o ponto final do Gestor de recursos é `https://management.local.azurestack.external`. |
| *subId* |ID de subscrição do utilizador que está a efetuar a chamada. Pode utilizar esta API apenas a consulta para a utilização de uma única subscrição. Fornecedores podem utilizar a API de utilização de recursos do fornecedor para utilização de consulta para todos os inquilinos. |
| *reportedStartTime* |A hora de início da consulta. O valor para *DateTime* deve ser em UTC e no início da hora, por exemplo, 13:00. Para agregação diária, defina este valor como meia-noite do UTC. O formato é *escape* ISO 8601, por exemplo, 2015-06-16T18% 3a53% 3a11% 2b00% 3a00Z, onde a vírgula é escape para % 3a e plus escape para % 2b para que seja URI amigável. |
| *reportedEndTime* |Hora de fim da consulta. As restrições que se aplicam ao *reportedStartTime* também se aplicam para este argumento. O valor para *reportedEndTime* não pode estar no futuro. |
| *aggregationGranularity* |O parâmetro opcional que tem dois valores discretos de potenciais: diários e hora a hora. Como os valores sugerem, um devolve os dados na granularidade diária e o outro é uma resolução por hora. A diária é a opção predefinida. |
| *versão de API* |Versão do protocolo que é utilizado para efetuar este pedido. Tem de utilizar 2015-06-01-pré-visualização. |
| *continuationToken* |Obter o token da última chamada para o fornecedor de API de utilização. Este token é necessário quando uma resposta é maior do que 1000 linhas e funciona como um marcador para progresso. Se não estiver presente, os dados são obtidos a partir do início do dia ou a hora, com base na granularidade transmitido. |

### <a name="response"></a>Resposta
OBTER /subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00 & reportedEndTime = 2015-06-01T00% 3a00% 3a00% 2b00% 3a00 & aggregationGranularity = diária & api-version = 1.0

```json
{
"value": [
{

"id":
"/subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregate/sub1-meterID1",
"name": "sub1-meterID1",
"type": "Microsoft.Commerce/UsageAggregate",

"properties": {
"subscriptionId":"sub1",
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
| *ID* |ID exclusivo do agregado de utilização |
| *nome* |Nome do agregado de utilização |
| *tipo* |Definição do recurso |
| *subscriptionId* |Identificador de subscrição de utilizador do Azure |
| *usageStartTime* |Hora UTC do registo de utilização a que pertence este agregado de utilização |
| *usageEndTime* |Hora de fim de UTC do registo de utilização a que pertence este agregado de utilização |
| *instanceData* |Pares chave-valor dos detalhes da instância (por um novo formato):<br>  *resourceUri*: totalmente qualificado ID de recurso, incluindo grupos de recursos e o nome de instância <br>  *localização*: região na qual foi executado a este serviço <br>  *etiquetas*: os sinalizadores de recursos que o utilizador Especifica <br>  *additionalInfo*: mais detalhes sobre o recurso que foi consumido, por exemplo, tipo de imagem ou versão do SO |
| *quantidade* |Quantidade de consumo de recursos que ocorreram neste período de tempo |
| *meterId* |ID exclusivo para o recurso que foi utilizado (também denominado *ResourceID*) |

## <a name="next-steps"></a>Passos seguintes
[API de utilização do recurso do fornecedor](azure-stack-provider-resource-api.md)

[FAQ relacionados com a utilização](azure-stack-usage-related-faq.md)

