---
title: "Adicionar a colocação em cache para melhorar o desempenho na Gestão de API do Azure | Microsoft Docs"
description: "Saiba como melhorar a latência, o consumo de largura de banda e a carga do serviço Web para chamadas de serviço da API Management."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 59c595f0d5ce849f44c46fdb6cab0b44d35fffa0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>Adicionar a colocação em cache para melhorar o desempenho na API Management do Azure
É possível configurar as operações da API Management para colocar as respostas em cache. A colocação de respostas em cache pode reduzir significativamente a latência da API, o consumo de largura de banda e a carga do serviço Web para os dados que não são alterados com frequência.

Este guia mostra como adicionar a colocação de respostas em cache à sua API e configurar políticas para as operações da API Eco de exemplo. Em seguida, pode chamar a operação a partir do portal do programador para ver a colocação em cache em ação.

> [!NOTE]
> Para obter informações sobre a colocação em cache de itens por chave utilizando expressões de política, consulte [Colocação em cache personalizada na API Management do Azure](api-management-sample-cache-by-key.md).
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Antes de seguir os passos neste guia, tem de ter uma instância de serviço de API Management com uma API e um produto configurado. Se ainda não criou uma instância de serviço de Gestão de API, consulte [Criar uma instância de serviço de Gestão de API][Create an API Management service instance] no tutorial [Introdução à Gestão de API do Azure][Get started with Azure API Management].

## <a name="configure-caching"> </a>Configurar uma operação para colocação em cache
Neste passo, irá rever as definições de colocação em cache da operação **Recurso GET (em cache)** da API Eco de exemplo.

> [!NOTE]
> Cada instância de serviço de API Management está pré-configurada com uma API Eco que pode ser utilizada para experimentar e saber mais sobre a API Management. Para obter mais informações, consulte [Introdução à Gestão de API do Azure][Get started with Azure API Management].
> 
> 

Para começar, clique em **Portal do editor** no Portal do Azure para o seu serviço de Gestão de API. Isto leva-o para o portal do publicador da API Management.

![Portal do publicador][api-management-management-console]

Clique em **APIs** no menu **API Management** à esquerda e, em seguida, clique em **API Eco**.

![API Eco][api-management-echo-api]

Clique no separador **Operações** e, em seguida, clique na operação **Recurso GET (em cache)** na lista **Operações**.

![Operações da API Eco][api-management-echo-api-operations]

Clique no separador **Colocação em cache** para ver as definições de colocação em cache para esta operação.

![Separador Colocação em cache][api-management-caching-tab]

Para ativar a colocação em cache para uma operação, selecione a caixa de verificação **Ativar**. Neste exemplo, a colocação em cache está ativada.

Cada resposta da operação é codificada, com base nos valores dos campos **Variação por parâmetros de cadeia de consulta** e **Variação por cabeçalhos**. Se pretender colocar em cache várias respostas com base nos parâmetros de cadeia de consulta ou nos cabeçalhos, pode configurá-las nestes dois campos.

**Duração** especifica o intervalo de expiração das respostas em cache. Neste exemplo, o intervalo é **3600** segundos, que é equivalente a uma hora.

Utilizando a configuração de colocação em cache deste exemplo, o primeiro pedido efetuado à operação **Recurso GET (em cache)** devolve uma resposta do serviço de back-end. Esta resposta será colocada em cache, codificada pelos cabeçalhos e pelos parâmetros de cadeia de consulta especificados. Para as chamadas subsequentes à operação que tenham parâmetros correspondentes, será devolvida a resposta em cache, até que o intervalo de duração da cache expire.

## <a name="caching-policies"> </a>Rever as políticas de colocação em cache
Neste passo, irá rever as definições de colocação em cache da operação **Recurso GET (em cache)** da API Eco de exemplo.

Quando as definições de colocação em cache são configuradas para uma operação no separador **Colocação em cache**, as políticas de colocação em cache são adicionadas para a operação. É possível ver e editar estas políticas no editor de políticas.

Clique em **Políticas** no menu **API Management** à esquerda e, em seguida, selecione **API Eco/Recurso GET (em cache)** na lista pendente **Operação**.

![Operação de âmbito da política][api-management-operation-dropdown]

Isto apresenta as políticas para esta operação no editor de políticas.

![Editor de políticas da API Management][api-management-policy-editor]

A definição de política para esta operação inclui as políticas que definem a configuração de colocação em cache que foram revistas utilizando o separador **Colocação em cache** no passo anterior.

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
            <vary-by-header>Accept</vary-by-header>
            <vary-by-header>Accept-Charset</vary-by-header>
        </cache-lookup>
        <rewrite-uri template="/resource" />
    </inbound>
    <outbound>
        <base />
        <cache-store caching-mode="cache-on" duration="3600" />
    </outbound>
</policies>
```

> [!NOTE]
> As alterações efetuadas às políticas de colocação em cache no editor de políticas refletir-se-ão no separador **Colocação em cache** de uma operação e vice-versa.
> 
> 

## <a name="test-operation"> </a>Chamar uma operação e testar a colocação em cache
Para ver a colocação em cache em ação, podemos chamar a operação a partir do portal do programador. Clique em **Portal do programador** no menu superior à direita.

![Portal do programador][api-management-developer-portal-menu]

Clique em **APIs** no menu superior e, em seguida, selecione **API Eco**.

![API Eco][api-management-apis-echo-api]

> Se tiver apenas uma API configurada ou visível para a sua conta, clicar em APIs leva-o diretamente para as operações dessa API.
> 
> 

Selecione a operação **Recurso GET (em cache)** e, em seguida, clique em **Abrir Consola**.

![Abrir consola][api-management-open-console]

A consola permite invocar operações diretamente a partir do portal do programador.

![Consola][api-management-console]

Mantenha os valores predefinidos para **param1** e **param2**.

Selecione a chave pretendida na lista pendente **subscription-key**. Se a sua conta tiver apenas uma subscrição, esta já estará selecionada.

Introduza **sampleheader:value1** na caixa de texto **Cabeçalhos de pedido**.

Clique em **HTTP Get** e tome nota dos cabeçalhos da resposta.

Introduza **sampleheader:value2** na caixa de texto **Cabeçalhos de pedido** e, em seguida, clique em **HTTP Get**.

Tenha em atenção que o valor de **sampleheader** continua a ser **value1** na resposta. Experimente alguns valores diferentes e observe que é devolvida a resposta em cache da primeira chamada.

Introduza **25** no campo **param2** e, em seguida, clique em **HTTP Get**.

Tenha em atenção que o valor de **sampleheader** na resposta é agora **value2**. Uma vez que os resultados da operação são codificados por cadeia de consulta, a resposta em cache anterior não foi devolvida.

## <a name="next-steps"> </a>Passos seguintes
* Para obter mais informações sobre as políticas de colocação em cache, consulte [Políticas de colocação em cache][Caching policies] na [Referência de política da Gestão de API][API Management policy reference].
* Para obter informações sobre a colocação em cache de itens por chave utilizando expressões de política, consulte [Colocação em cache personalizada na API Management do Azure](api-management-sample-cache-by-key.md).

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: api-management-get-started.md

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
