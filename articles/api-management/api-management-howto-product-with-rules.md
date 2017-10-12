---
title: "Proteger a sua API com a Gestão de API do Azure | Microsoft Docs"
description: "Saiba como proteger a sua API com quotas e políticas de limitação (limitação de taxas)."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 450dc368-d005-401d-ae64-3e1a2229b12f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 300b1d762a61c810dbffde5aaacd8a85f12c9fca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="protect-your-api-with-rate-limits-using-azure-api-management"></a>Proteger a sua API com limites de taxa utilizando a API Management do Azure
Este guia mostra como é fácil adicionar proteção para a sua API de back-end através da configuração de políticas de limite de taxa e quota com a API Management do Azure.

Neste tutorial, irá criar um produto de API de “Avaliação gratuita” que permite aos programadores efetuar até 10 chamadas por minuto e até um máximo de 200 chamadas por semana para a sua API utilizando as políticas [Limitar taxa de chamadas por subscrição](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) e [Definir quota de utilização por subscrição](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota). Em seguida, irá publicar a API e testar a política de limite de taxa.

Para cenários de limitação mais avançados com as políticas [rate-limit-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) e [quota-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey), consulte [Limitação de pedidos avançada com a API Management do Azure](api-management-sample-flexible-throttling.md).

## <a name="create-product"> </a>Para criar um produto
Neste passo, irá criar um produto de Avaliação Gratuita que não necessita de aprovação de subscrição.

> [!NOTE]
> Se já tiver um produto configurado e pretender utilizá-lo para este tutorial, pode ir diretamente para [Configurar políticas de limite de taxa de chamadas e quota][Configure call rate limit and quota policies] e seguir o tutorial a partir daí, utilizando o seu produto em vez do produto de Avaliação Gratuita.
> 
> 

Para começar, clique em **Portal do editor** no Portal do Azure para o seu serviço de Gestão de API.

![Portal do publicador][api-management-management-console]

> Se ainda não criou uma instância de serviço de Gestão de API, consulte [Criar uma instância de serviço de Gestão de API][Create an API Management service instance] no tutorial [Gerir a sua primeira API na Gestão de API do Azure][Manage your first API in Azure API Management].
> 
> 

Clique em **Produtos** no menu **API Management** à esquerda para apresentar a página **Produtos**.

![Adicionar produto][api-management-add-product]

Clique em **Adicionar produto** para apresentar a caixa de diálogo **Adicionar novo produto**.

![Adicionar novo produto][api-management-new-product-window]

Na caixa **Título**, escreva **Avaliação Gratuita**.

Na caixa **Descrição**, escreva o seguinte texto: **Os subscritores poderão efetuar 10 chamadas/minuto até um máximo de 200 chamadas/semana, após o qual o acesso é negado.**

Os produtos da API Management podem ser protegidos ou abertos. Os produtos protegidos têm de ser subscritos antes de poderem ser utilizados. Os produtos abertos podem ser utilizados sem uma subscrição. Certifique-se de que **Exigir subscrição** está selecionado para criar um produto protegido que necessita de uma subscrição. Esta é a predefinição.

Se pretender que um administrador reveja e aceite ou rejeite as tentativas de subscrição deste produto, selecione **Exigir aprovação da subscrição**. Se a caixa de verificação não estiver selecionada, as tentativas de subscrição serão aprovadas automaticamente. Neste exemplo, as subscrições são automaticamente aprovadas, pelo que não deve selecionar a caixa.

Para permitir que as contas de programador subscrevam várias vezes o novo produto, selecione a caixa de verificação **Permitir várias subscrições simultâneas**. Este tutorial não utiliza várias subscrições simultâneas, por isso deixe a opção desmarcada.

Depois de serem introduzidos todos os valores, clique em **Guardar** para criar o produto.

![Produto adicionado][api-management-product-added]

Por predefinição, os novos produtos são visíveis para os utilizadores no grupo **Administradores**. Vamos adicionar o grupo **Programadores**. Clique em **Avaliação Gratuita** e, em seguida, clique no separador **Visibilidade**.

> Na API Management, os grupos são utilizados para gerir a visibilidade dos produtos para os programadores. Os produtos concedem visibilidade aos grupos e os programadores podem ver e subscrever os produtos que estão visíveis para os grupos a que pertencem. Para obter mais informações, consulte [Como criar e utilizar grupos na Gestão de API do Azure][How to create and use groups in Azure API Management].
> 
> 

![Adicionar grupo de programadores][api-management-add-developers-group]

Selecione a caixa de verificação **Programadores** e, em seguida, clique em **Guardar**.

## <a name="add-api"> </a>Para adicionar uma API ao produto
Neste passo do tutorial, iremos adicionar a API Eco ao novo produto de Avaliação Gratuita.

> Cada instância de serviço de API Management está pré-configurada com uma API Eco que pode ser utilizada para experimentar e saber mais sobre a API Management. Para obter mais informações, consulte [Gerir a sua primeira API na Gestão de API do Azure][Manage your first API in Azure API Management].
> 
> 

Clique em **Produtos** no menu **API Management** à esquerda e, em seguida, clique em **Avaliação Gratuita** para configurar o produto.

![Configurar o produto][api-management-configure-product]

Clique em **Adicionar API ao produto**.

![Adicionar API ao produto][api-management-add-api]

Selecione **API Eco** e, em seguida, clique em **Guardar**.

![Adicionar API Eco][api-management-add-echo-api]

## <a name="policies"> </a>Para configurar as políticas de limite de taxa de chamadas e quota
Os limites de taxa e as quotas são configurados no editor de políticas. As duas políticas que vamos adicionar neste tutorial são as políticas [Limitar taxa de chamadas por subscrição](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) e [Definir quota de utilização por subscrição](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota). Estas políticas têm de ser aplicadas no âmbito do produto.

Clique em **Políticas** no menu **API Management** à esquerda. Na lista **Produto**, clique em **Avaliação Gratuita**.

![Política do produto][api-management-product-policy]

Clique em **Adicionar política** para importar o modelo de política e começar a criar as políticas de limite de taxa e quota.

![Adicionar política][api-management-add-policy]

As políticas de limite de taxa e quota são políticas de entrada, por isso posicione o cursor no elemento inbound.

![Editor de políticas][api-management-policy-editor-inbound]

Percorra a lista de políticas e localize a entrada **Limitar taxa de chamadas por subscrição**.

![Instruções de política][api-management-limit-policies]

Depois de o cursor estar posicionado no elemento da política **inbound**, clique na seta ao lado de **Limitar taxa de chamadas por subscrição** para inserir o respetivo modelo de política.

```xml
<rate-limit calls="number" renewal-period="seconds">
<api name="name" calls="number">
<operation name="name" calls="number" />
</api>
</rate-limit>
```

Como pode ver no fragmento, a política permite definir limites para as APIs e as operações do produto. Neste tutorial, não utilizaremos essa capacidade, por isso elimine os elementos **api** e **operation** do elemento **rate-limit**, de modo a que permaneça apenas o elemento exterior **rate-limit**, conforme mostrado no exemplo seguinte.

```xml
<rate-limit calls="number" renewal-period="seconds">
</rate-limit>
```

No produto de Avaliação Gratuita, a taxa de chamadas permitida máxima é de 10 chamadas por minuto, por isso escreva **10** como o valor para o atributo **calls** e **60** para o atributo **renewal-period**.

```xml
<rate-limit calls="10" renewal-period="60">
</rate-limit>
```

Para configurar a política **Definir quota de utilização por subscrição**, posicione o cursor imediatamente abaixo do elemento recém-adicionado **rate-limit** no elemento **inbound** e, em seguida, localize e clique na seta à esquerda de **Definir quota de utilização por subscrição**.

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
<api name="name" calls="number" bandwidth="kilobytes">
<operation name="name" calls="number" bandwidth="kilobytes" />
</api>
</quota>
```

De forma semelhante à política **Limitar taxa de chamadas por subscrição**, a política **Definir quota de utilização por subscrição** permite definir limites para as APIs e operações do produto. Neste tutorial, não utilizaremos essa capacidade, por isso elimine os elementos **api** e **operation** do elemento **quota**, conforme mostrado no exemplo seguinte.

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
</quota>
```

As quotas podem basear-se no número de chamadas por intervalo, por largura de banda ou ambos. Neste tutorial, não pretendemos criar uma limitação baseada na largura de banda, por isso elimine o atributo **bandwidth**.

```xml
<quota calls="number" renewal-period="seconds">
</quota>
```

No produto de Avaliação Gratuita, a quota é de 200 chamadas por semana. Especifique **200** como o valor para o atributo **calls** e, em seguida, especifique **604800** como o valor para o atributo **renewal-period**.

```xml
<quota calls="200" renewal-period="604800">
</quota>
```

> Os intervalos da política são especificados em segundos. Para calcular o intervalo para uma semana, pode multiplicar o número de dias (7) pelo número de horas num dia (24) pelo número de minutos numa hora (60) pelo número de segundos num minuto (60): 7 * 24 * 60 * 60 = 604800.
> 
> 

Quando terminar de configurar a política, esta deve corresponder ao exemplo seguinte.

```xml
<policies>
    <inbound>
        <rate-limit calls="10" renewal-period="60">
        </rate-limit>
        <quota calls="200" renewal-period="604800">
        </quota>
        <base />

</inbound>
<outbound>

    <base />

    </outbound>
</policies>
```

Uma vez configuradas as políticas pretendidas, clique em **Guardar**.

![Guardar política][api-management-policy-save]

## <a name="publish-product"> </a> Para publicar o produto
Agora que as APIs foram adicionadas e as políticas foram configuradas, o produto tem de ser publicado para que possa ser utilizado pelos programadores. Clique em **Produtos** no menu **API Management** à esquerda e, em seguida, clique em **Avaliação Gratuita** para configurar o produto.

![Configurar o produto][api-management-configure-product]

Clique em **Publicar** e, em seguida, clique em **Sim, publicar** para confirmar.

![Publicar o produto][api-management-publish-product]

## <a name="subscribe-account"> </a>Para subscrever o produto com uma conta de programador
Agora que o produto está publicado, está disponível para ser subscrito e utilizado pelos programadores.

> Os administradores de uma instância da API Management subscrevem automaticamente todos os produtos. Neste passo do tutorial, iremos subscrever o produto de Avaliação Gratuita com uma das contas de programador de não administrador. Se a sua conta de programador faz parte da função Administradores, pode seguir este passo, apesar de já ter uma subscrição.
> 
> 

Clique em **Utilizadores** no menu **API Management** à esquerda e, em seguida, clique no nome da sua conta de programador. Neste exemplo, vamos utilizar a conta de programador **Clayton Gragg**.

![Configurar o programador][api-management-configure-developer]

Clique em **Adicionar subscrição**.

![Adicionar subscrição][api-management-add-subscription-menu]

Selecione **Avaliação Gratuita** e, em seguida, clique em **Subscrever**.

![Adicionar subscrição][api-management-add-subscription]

> [!NOTE]
> Neste tutorial, não estão ativadas várias subscrições simultâneas para o produto de Avaliação Gratuita. Se estivessem, ser-lhe-ia pedido que atribuísse um nome à subscrição, conforme mostrado no exemplo seguinte.
> 
> 

![Adicionar subscrição][api-management-add-subscription-multiple]

Depois de clicar em **Subscrever**, o produto é apresentado na lista **Subscrição** do utilizador.

![Subscrição adicionada][api-management-subscription-added]

## <a name="test-rate-limit"> </a>Para chamar uma operação e testar o limite de taxa
Agora que o produto de Avaliação Gratuita está configurado e publicado, podemos chamar algumas operações e testar a política de limite de taxa.
Mude para o portal do programador ao clicar em **Portal do programador** no menu do canto superior direito.

![Portal do programador][api-management-developer-portal-menu]

Clique em **APIs** no menu superior e, em seguida, clique em **API Eco**.

![Portal do programador][api-management-developer-portal-api-menu]

Clique em **Recurso GET** e, em seguida, clique em **Experimente**.

![Abrir consola][api-management-open-console]

Mantenha os valores de parâmetros predefinidos e, em seguida, selecione a sua chave de subscrição para o produto de Avaliação Gratuita.

![Chave de subscrição][api-management-select-key]

> [!NOTE]
> Se tiver várias subscrições, certifique-se de que seleciona a chave para **Avaliação Gratuita**; caso contrário, as políticas que foram configuradas nos passos anteriores não terão efeito.
> 
> 

Clique em **Enviar** e, em seguida, veja a resposta. Tenha atenção ao **Estado de resposta** de **200 OK**.

![Resultados da operação][api-management-http-get-results]

Clique em **Enviar** a uma taxa superior à política de limite de taxa de 10 chamadas por minuto. Depois de a política de limite de taxa ser excedida, é devolvido um estado de resposta **429 Demasiados Pedidos**.

![Resultados da operação][api-management-http-get-429]

O **Conteúdo da resposta** indica o intervalo restante antes de as repetições serem efetuadas com êxito.

Quando a política de limite de taxa de 10 chamadas por minuto estiver em vigor, as chamadas subsequentes falharão até passarem 60 segundos desde a primeira das 10 chamadas bem-sucedidas para o produto antes de o limite de taxa ser excedido. Neste exemplo, o intervalo restante é 54 segundos.

## <a name="next-steps"> </a>Passos seguintes
* Veja uma demonstração sobre como definir limites de taxa e quotas no vídeo seguinte.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]
> 
> 

[api-management-management-console]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
[api-management-new-product-window]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
[api-management-product-added]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
[api-management-add-policy]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
[api-management-policy-editor-inbound]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
[api-management-limit-policies]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
[api-management-policy-save]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
[api-management-configure-product]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
[api-management-add-api]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
[api-management-add-echo-api]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
[api-management-developer-portal-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
[api-management-publish-product]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
[api-management-configure-developer]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
[api-management-add-subscription-menu]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
[api-management-add-subscription]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
[api-management-developer-portal-api-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
[api-management-open-console]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-product-with-rules/api-management-http-get.png
[api-management-http-get-results]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
[api-management-http-get-429]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
[api-management-product-policy]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
[api-management-add-developers-group]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
[api-management-select-key]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
[api-management-subscription-added]: ./media/api-management-howto-product-with-rules/api-management-subscription-added.png
[api-management-add-subscription-multiple]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-multiple.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Manage your first API in Azure API Management]: api-management-get-started.md
[How to create and use groups in Azure API Management]: api-management-howto-create-groups.md
[View subscribers to a product]: api-management-howto-add-products.md#view-subscribers
[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps

[Create a product]: #create-product
[Configure call rate limit and quota policies]: #policies
[Add an API to the product]: #add-api
[Publish the product]: #publish-product
[Subscribe a developer account to the product]: #subscribe-account
[Call an operation and test the rate limit]: #test-rate-limit

[Limit call rate]: https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate
[Set usage quota]: https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota
