---
ms.assetid: 
title: "Limitação orientações para o Cofre de chaves do Azure | Microsoft Docs"
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 06/21/2017
ms.openlocfilehash: fe700e22c5323c2a0bdc315e349cd119798bcf40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-key-vault-throttling-guidance"></a>Limitação orientações para o Cofre de chaves do Azure

Limitação é um processo iniciar que limita o número de chamadas simultâneas para o serviço do Azure para prevenir overuse de recursos. Cofre de chaves do Azure (AKV) foi concebido para processar um grande volume de pedidos. Se ocorrer um número muito confuso de pedidos, a limitação de pedidos do cliente ajuda a manter um desempenho ideal e fiabilidade do serviço AKV.

Os limites de limitação variam com base no cenário. Por exemplo, se estiver a executar um grande volume de operações de escrita, a possibilidade de limitação é superior ao se estiver a efetuar apenas operações de leitura.

## <a name="how-does-key-vault-handle-its-limits"></a>Como é que o Cofre de chaves processar os respetivos limites?

Limites de serviço no Cofre de chaves existem impedir a utilização indevida dos recursos e certifique-se de qualidade de serviço para todos os clientes do Cofre de chaves. Quando um serviço limiar for excedido, o Cofre de chaves limita quaisquer pedidos de mais do que o cliente durante um período de tempo. Quando isto acontecer, o Cofre de chaves devolve o código de estado HTTP 429 (demasiados muitos pedidos), e falharem os pedidos. Além disso, não foi possível pedidos que devolvem uma contagem 429 para os limites de limitação controlados pelo Cofre de chaves. 

Se tiver um caso de negócio válido para limites de limitação superiores, contacte-nos.


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Como limitar a aplicação em resposta a limites de serviço

Seguem-se **melhores práticas** para a limitação da sua aplicação:
- Reduza o número de operações por pedido.
- Reduza a frequência de pedidos.
- Evite tentativas imediatas. 
    - Todos os pedidos acumular contra os limites de utilização.

Quando implementar o processamento de erros da sua aplicação, utilize o código de erro HTTP 429 para detetar a necessidade de limitação do lado do cliente. Se o pedido novamente falhar com um código de erro HTTP 429, pode ainda encontrar um limite de serviço do Azure. Continue a utilizar o recomendada do lado do cliente limitação método, repetir o pedido até ter êxito.

### <a name="recommended-client-side-throttling-method"></a>Método de limitação do lado do cliente recomendado

No código de erro HTTP 429, iniciar o cliente utilizar uma abordagem de término exponencial de limitação:

1. Aguarde 1 segundo, repita o pedido
2. Se ainda limitadas espera segundos 2, repita o pedido
3. Se ainda limitadas espera 4 segundos, repita o pedido
4. Se ainda limitadas espera segundos 8, repita o pedido
5. Se ainda limitadas espera segundos 16, repita o pedido

Neste momento, a deve não ser obter códigos de resposta de HTTP 429.

## <a name="see-also"></a>Consultar também

Para uma orientação mais aprofundada de limitação no Microsoft Cloud, consulte [limitação padrão](https://docs.microsoft.com/azure/architecture/patterns/throttling).

