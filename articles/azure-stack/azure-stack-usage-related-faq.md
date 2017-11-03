---
title: "Perguntas mais frequentes relacionadas com a API de utilização | Microsoft Docs"
description: "Lista de medidores de pilha do Azure, a comparação com a API de utilização do Azure, tempo de utilização e comunicadas hora, códigos de erro."
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: 847f18b2-49a9-4931-9c09-9374e932a071
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: alfredop
ms.openlocfilehash: 166147c8cb4949be1b23e0a06868e66c8a5844f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Perguntas mais frequentes sobre a utilização de pilha de Azure API do 
Este artigo responde a algumas perguntas mais frequentes sobre a API de utilização de pilha do Azure.

## <a name="what-meter-ids-can-i-see"></a>O IDs de medidor posso ver?
Atualmente, a utilização é comunicada para a rede, armazenamento e fornecedores de recursos de computação.

| **Fornecedor de recursos** | **ID de medidor** | **Nome do medidor** | **Unidade** | **Informações adicionais** |
| --- | --- | --- | --- | --- | 
| **Rede** |F271A8A388C44D93956A063E1D2FA80B |Utilização de endereços IP estáticos |Endereços IP|Endereços de contagem de IP utilizado | 
| |9E2739BA86744796B465F64674B822BA |Utilização de endereços IP dinâmico |Endereços IP|Endereços de contagem de IP utilizado | 
| **Armazenamento** |B4438D5D-453B-4EE1-B42A-DC72E377F1E4 |TableCapacity |GB\*horas |Capacidade total consumida pelos tabelas |
| | B5C15376-6C94-4FDD-B655-1A69D138ACA3 |PageBlobCapacity |GB\*horas |Capacidade total consumida pelos blobs de páginas |
| | B03C6AE7-B080-4BFA-84A3-22C800F315C6 |QueueCapacity |GB\*horas |Capacidade total consumida na fila |
| | 09F8879E-87E9-4305-A572-4B7BE209F857 |BlockBlobCapacity |GB\*horas |Capacidade total consumida pelos blobs de blocos |
| | B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90 |TableTransactions |Contagem de pedido em 10 000s |Pedidos de serviço tabela (em 10 000s) |
| | 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D |TableDataTransIn |Dados de entrada em GB |Entrada de dados de serviço tabela em GB |
| | 1B8C1DEC-EE42-414B-AA36-6229CF199370 |TableDataTransOut |Outgress em GB |Saída de dados do serviço tabela em GB |
| | 43DAF82B-4618-444A-B994-40C23F7CD438 |BlobTransactions |Contagem de pedidos em 10 000s |Pedidos de serviço blob (em 10 000s) |
| | 9764F92C-E44A-498E-8DC1-AAD66587A810 |BlobDataTransIn |Dados de entrada em GB |Entrada de dados de serviço blob em GB |
| | 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8 |BlobDataTransOut |Outgress em GB |Saída de dados do serviço blob em GB |
| | EB43DD12-1AA6-4C4B-872C-FAF15A6785EA |QueueTransactions |Contagem de pedidos em 10 000s |Pedidos de serviço de fila (em 10 000s) |
| | E518E809-E369-4A45-9274-2017B29FFF25 |QueueDataTransIn |Dados de entrada em GB |Entrada de dados de serviço fila em GB | 
| | DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2 |QueueDataTransOut |Outgress em GB |Saída de dados do serviço fila em GB |
| **Computação** |FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5 |Horas de tamanho base VM |Minutos de núcleos virtual | Número de vcores vezes minutos que a VM foi executada |
| |9CD92D4C-BAFD-4492-B278-BEDC2DE8232A |Horas de tamanho VM do Windows |Minutos de núcleos virtual | Número de vcores vezes minutos que a VM foi executada |
| |6DAB500F-A4FD-49C4-956D-229BB9C8C793 |Horas de tamanho VM |Horas VM |Captura VM Base e Windows. Não ajustar vcores |
| **Cofre de Chaves** | EBF13B9F-B3EA-46FE-BF54-396E93D48AB4 |Transações de Cofre de chaves | Contagem de pedido no 10000s| Número de pedidos de REST API recebidos pelo plane de dados do Cofre de chaves |

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Como fazer a utilização de pilha do Azure APIs comparam com a [utilização do Azure API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (atualmente em pré-visualização pública)?
* A API de utilização do inquilino é consistente com a API do Azure, com uma exceção: o *showDetails* sinalizador atualmente não é suportado na pilha do Azure.
* A API de utilização de fornecedor só se aplica a pilha do Azure.
* Atualmente, o [RateCard API](https://msdn.microsoft.com/en-us/library/azure/mt219004.aspx) que está disponível no Azure não está disponível na pilha do Azure.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>O que é a diferença entre a hora de utilização e tempo comunicado?
Relatórios de dados de utilização tem dois valores de hora principal:

* **Comunicado tempo**. A hora quando o evento de utilização introduziu o sistema de utilização
* **Tempo de utilização**. A hora quando o recurso de pilha do Azure foi consumido

Poderá ver uma discrepância valores de tempo de utilização e tempo comunicado para um evento de utilização específicos. O atraso pode ser tão longo quanto várias horas, em qualquer ambiente.

Atualmente, pode consultar *apenas por tempo comunicado*.

## <a name="what-do-these-usage-api-error-codes-mean"></a>O que estes códigos de erro de API de utilização significa?
| **Código de estado HTTP** | **Código de erro** | **Descrição** |
| --- | --- | --- |
| Pedido de 400/incorretos |*NoApiVersion* |O *api-version* parâmetro de consulta está em falta. |
| Pedido de 400/incorretos |*InvalidProperty* |Uma propriedade está em falta ou tem um valor inválido. A mensagem com o código de erro no corpo da resposta identifica a propriedade em falta. |
| Pedido de 400/incorretos |*RequestEndTimeIsInFuture* |O valor para *ReportedEndTime* é no futuro. No futuro não são permitidos valores para este argumento. |
| Pedido de 400/incorretos |*SubscriberIdIsNotDirectTenant* |Uma chamada à API do fornecedor utilizado um ID de subscrição não é um inquilino válidos do chamador. |
| Pedido de 400/incorretos |*SubscriptionIdMissingInRequest* |O ID de subscrição do chamador está em falta. |
| Pedido de 400/incorretos |*InvalidAggregationGranularity* |Foi pedida uma granularidade de agregação inválido. Os valores válidos são diária e por hora. |
| 503 |*ServiceUnavailable* |Ocorreu um erro repetível porque o serviço está ocupado ou está a ser limitada a chamada. |

## <a name="next-steps"></a>Passos Seguintes
[Cliente de faturação e encargos na pilha do Azure](azure-stack-billing-and-chargeback.md)

[API de utilização de recursos do fornecedor](azure-stack-provider-resource-api.md)

[API de utilização de recursos de inquilino](azure-stack-tenant-resource-usage-api.md)

