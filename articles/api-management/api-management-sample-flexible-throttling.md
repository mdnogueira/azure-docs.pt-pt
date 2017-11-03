---
title: "Limitação com a API Management do Azure de pedidos avançada"
description: "Saiba como criar e aplicar quota flexível e a taxa de limitação de políticas de API Management do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 356f98aec072a1295915ae0701a3e3cd793aba07
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Limitação com a API Management do Azure de pedidos avançada
A capacidade para limitar os pedidos recebidos é uma função de chave de API Management do Azure. Ou ao controlar a taxa de pedidos ou os dados/pedidos totais transferidos, gestão de API permite que os fornecedores de API proteger as respetivas APIs de abuso e criar o valor para diferentes camadas de produto de API.

## <a name="product-based-throttling"></a>Limitação com base do produto
Até à data, a limitação de velocidade capacidades foram limitadas a que está a ser confinadas a uma subscrição de produto específica (essencialmente uma chave), definida no portal do publicador da API Management. Isto é útil para o fornecedor de API aplicar limites no programadores que tenham efetuado a inscrição para utilizar a sua API, no entanto, é que não ajuda, por exemplo, no limitação individuais dos utilizadores finais da API. É possível que para o único utilizador da aplicação do programador para consumir a quota completa e, em seguida, impedem a outros clientes do programador capazes de utilizar a aplicação. Além disso, vários clientes que podem gerar um grande volume de pedidos poderão limitar o acesso a utilizadores ocasionais.

## <a name="custom-key-based-throttling"></a>Chave personalizada baseada em limitação
A nova [taxa limite por chave](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) e [quota-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) políticas fornecem uma solução significativamente mais flexível para controlo de tráfego. Estas novas políticas permitem-lhe definir as expressões para identificar as chaves que serão utilizadas para controlar a utilização de tráfego. A forma como isto funciona easiest é ilustrado com um exemplo. 

## <a name="ip-address-throttling"></a>Limitação de endereço IP
As seguintes políticas restringir a um endereço IP do cliente única para apenas 10 chamadas cada minuto, com um total de 1.000.000 chamadas e 10 000 quilobytes da largura de banda por mês. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Se todos os clientes na Internet utilizado um endereço IP exclusivo, pode ser uma forma eficaz de limitação de utilização por utilizador. No entanto, é bastante provável que vários utilizadores, poderá partilhar um único endereço IP público devido a-lhes aceder à Internet através de um dispositivo NAT. Apesar de isto, APIs que permitem o acesso não autenticado o `IpAddress` poderá ser a melhor opção.

## <a name="user-identity-throttling"></a>Limitação de identidade do utilizador
Se um utilizador final é autenticado e uma chave de limitação pode ser gerada com base nas informações que identificam de forma que um utilizador.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

Neste exemplo, extrair o cabeçalho de autorização, converta-a em `JWT` objeto e utilizar o assunto do token para identificar o utilizador e utilizá-lo como a taxa de limitação de chave. Se a identidade do utilizador é armazenada no `JWT` como uma das outras afirmações, em seguida, que o valor pode ser utilizado no seu lugar.

## <a name="combined-policies"></a>Políticas combinadas
Apesar das novas políticas de limitação oferecem mais controlo que as políticas de limitação existentes, existe ainda está a combinar as capacidades de valor. Limitação pela chave de subscrição de produto ([limitar taxa de chamadas por subscrição](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) e [definir quota de utilização por subscrição](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota)) é uma excelente forma de ativar monetizing de uma API por charging com base nos níveis de utilização. O controlo detalhado finer de conseguir limitar pelo utilizador é complementares e impede que o comportamento de um utilizador afetar negativamente a experiência de outro. 

## <a name="client-driven-throttling"></a>Cliente suscitada pelo departamento de limitação
Quando a chave de limitação é definida utilizando um [expressão de política](https://msdn.microsoft.com/library/azure/dn910913.aspx), em seguida, é o fornecedor de API que está a escolher a forma como a limitação tem um âmbito. No entanto, um programador querer controlar a forma como estes limite de velocidade os seus próprios clientes. Isto foi possível ativar pelo fornecedor de API, introduzindo um cabeçalho personalizado para permitir que a aplicação de cliente do programador comunicar a chave para a API.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Isto permite que a aplicação de cliente o programador escolher como pretende criar a taxa de limitação de chave. Com um pouco de ingenuity um programador de cliente foi possível criar as seus próprios camadas taxa pela alocação de conjuntos de chaves para os utilizadores e rodar a utilização de chave.

## <a name="summary"></a>Resumo
Gestão de API do Azure fornece taxa e quota limitação para proteger e adicionar valor ao seu serviço de API. As novas políticas de limitação com regras de âmbito personalizadas permitem-lhe finer detalhado controlo sobre as políticas para ativar os seus clientes criar aplicações ainda mais. Os exemplos neste artigo demonstram a utilização destas políticas novas pela limitação de chaves com endereços IP do cliente, a identidade do utilizador e os valores de cliente gerado de taxa de fabrico. No entanto, existem muitas outras partes da mensagem que podem ser utilizados como agente de utilizador, fragmentos do caminho de URL, o tamanho da mensagem.

## <a name="next-steps"></a>Passos seguintes
Dê-nos seus comentários no Disqus thread para este tópico. Seria excelente ouvi sobre outros potenciais os valores da chave tem sido uma escolha lógica no seus cenários.

## <a name="watch-a-video-overview-of-these-policies"></a>Veja uma descrição geral do vídeo destas políticas
Para mais informações sobre o [taxa limite por chave](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) e [quota-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) abordadas neste artigo, as políticas, veja o vídeo seguinte.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Advanced-Request-Throttling-with-Azure-API-Management/player]
> 
> 

