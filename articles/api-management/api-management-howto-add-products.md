---
title: Como criar e publicar um produto na API Management do Azure
description: Saiba como criar e publicar produtos na API Management do Azure.
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 31de55cb-9384-490b-a2f2-6dfcf83da764
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 2cf905967f26de97613ff7d5fc495c9223e11390
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-create-and-publish-a-product-in-azure-api-management"></a>Como criar e publicar um produto na API Management do Azure
Na API Management do Azure, um produto contém uma ou mais APIs, bem como uma quota de utilização e os termos de utilização. Depois de um produto é publicado, os programadores podem subscrever o produto e começar a utilizar as APIs do produto. O tópico fornece um guia de criação de um produto, adicionar uma API e publicar para programadores.

## <a name="create-product"></a>Criar um produto
As operações são adicionadas e configuradas a uma API no portal do publicador. Para aceder ao portal do publicador, clique em **portal do publicador** no Portal do Azure para o seu serviço de API Management.

![Portal do publicador][api-management-management-console]

> Se ainda não criou uma instância de serviço de Gestão de API, consulte [Criar uma instância de serviço de Gestão de API][Create an API Management service instance] no tutorial [Introdução à Gestão de API do Azure][Get started with Azure API Management].
> 
> 

Clique em **produtos** no menu à esquerda para apresentar o **produtos** página e clique em **adicionar produto**.

![Produtos][api-management-products]

![Novo produto][api-management-add-new-product]

Introduza um nome descritivo para o produto no **nome** campo e uma descrição do produto no **Descrição** campo.

Os produtos na API Management podem ser **abra** ou **protegidos**. Os produtos protegidos têm de ser subscritos antes de poderem ser utilizados, enquanto os produtos abertos podem ser utilizados sem uma subscrição. Verifique **exigir subscrição** para criar um produto protegido que necessita de uma subscrição. Esta é a predefinição.

Verifique **exigir a aprovação de subscrição** se pretender que um administrador reveja e aceite ou rejeite as tentativas de subscrição para este produto. Se a caixa está desmarcada, as tentativas de subscrição serão aprovadas automaticamente. Para obter mais informações sobre as subscrições, consulte [ver os subscritores de um produto][View subscribers to a product].

Para permitir contas de programador subscrevam várias vezes para o produto, verifique o **permitir várias subscrições** caixa de verificação. Se esta caixa não está selecionada, cada conta de programador pode subscrever uma única vez para o produto.

![Ilimitados várias subscrições][api-management-unlimited-multiple-subscriptions]

Para limitar a contagem de várias subscrições simultâneas, verifique o **limitar o número de subscrições simultâneas para** caixa de verificação e introduza o limite de subscrição. No exemplo seguinte, subscrições simultâneas estão limitadas a quatro por conta de programador.

![Quatro várias subscrições][api-management-four-multiple-subscriptions]

Depois de todas as novas opções de produto são configuradas, clique em **guardar** para criar o novo produto.

![Produtos][api-management-products-page]

> Por predefinição novos produtos são anular a publicação e são visíveis apenas para o **administradores** grupo.
> 
> 

Para configurar um produto, clique no nome do produto no **produtos** separador.

## <a name="add-apis"></a>Adicionar APIs a um produto
O **produtos** página contém quatro ligações para a configuração: **resumo**, **definições**, **visibilidade**, e **subscritores**. O **resumo** separador é onde pode adicionar APIs e publicar ou anular a publicação de um produto.

![Resumo][api-management-new-product-summary]

Antes de publicar o produto tem de adicionar uma ou mais APIs. Para tal, clique em **adicionar API ao produto**.

![Adicionar APIs][api-management-add-apis-to-product]

Selecione as APIs pretendidas e clique em **guardar**.

## <a name="add-description"></a>Adicionar informações descritivas para um produto
O **definições** separador permite-lhe fornecem informações detalhadas sobre o produto como o seu objetivo, as APIs que fornece acesso ao e outras informações úteis. O conteúdo é direcionado para programadores que irão chamar a API e podem ser escritos em texto simples ou markup HTML.

![Definições do produto][api-management-product-settings]

Verifique **exigir subscrição** para criar um produto protegido que necessita de uma subscrição para ser utilizado ou desmarque a caixa de verificação para criar um produto aberto que pode ser chamado sem uma subscrição.

Selecione **exigir a aprovação de subscrição** se pretende aprovar manualmente todos os pedidos de subscrição de produto. Por predefinição, todas as subscrições de produto são concedidas automaticamente.

Para permitir contas de programador subscrevam várias vezes para o produto, verifique o **permitir várias subscrições** caixa de verificação e, opcionalmente, especifique um limite. Se esta caixa não está selecionada, cada conta de programador pode subscrever uma única vez para o produto.

Opcionalmente, preencha o **termos de utilização** campo que descreve os termos de utilização para o produto tem de aceitar os subscritores para poder utilizar o produto.

## <a name="publish-product"></a>Publicar um produto
Antes de poder ser chamadas as APIs de um produto, o produto tem de ser publicado. No **resumo** separador para o produto, clique em **publicar**e, em seguida, clique em **Sim, publicar** para confirmar. Para tornar um produto publicado anteriormente privado, clique em **Unpublish**.

![Publicar o produto][api-management-publish-product]

## <a name="make-visible"></a>Tornar um produto visíveis para os programadores
O **visibilidade** separador permite-lhe escolher que funções são capazes de ver o produto no portal de programador e subscrever o produto.

![Visibilidade de produto][api-management-product-visiblity]

Para ativar ou desativar visibilidade de um produto para os programadores de um grupo, verifique ou desmarque a caixa de verificação ao lado de grupo e, em seguida, clique em **guardar**.

> Para obter mais informações, consulte [como criar e utilizar grupos para gerir contas de programador na API Management do Azure][How to create and use groups to manage developer accounts in Azure API Management].
> 
> 

## <a name="view-subscribers"></a>Ver os subscritores de um produto
O **subscritores** separador lista programadores que tem subscritos do produto. Os detalhes e as definições para cada programador podem ser visualizadas ao clicar no nome do programador. Neste exemplo sem que os programadores têm ainda subscrever o produto.

![Programadores][api-management-developer-list]

## <a name="next-steps"> </a>Passos seguintes
Depois das APIs pretendidas são adicionadas e o produto publicado, os programadores podem subscrever o produto e começar a chamar as APIs. Para um tutorial que demonstra estes itens, bem como a configuração de produto avançadas consulte [como criar e configurar definições de produto avançadas na API Management do Azure][How create and configure advanced product settings in Azure API Management].

Para obter mais informações sobre como trabalhar com os produtos, veja o vídeo seguinte.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Using-Products/player]
> 
> 

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[View subscribers to a product]: #view-subscribers
[Next steps]: #next-steps

[api-management-management-console]: ./media/api-management-howto-add-products/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-add-products/api-management-add-product.png
[api-management-add-new-product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
[api-management-unlimited-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-unlimited-multiple-subscriptions.png
[api-management-four-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-four-multiple-subscriptions.png
[api-management-products-page]: ./media/api-management-howto-add-products/api-management-products-page.png
[api-management-new-product-summary]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
[api-management-add-apis-to-product]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
[api-management-product-settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
[api-management-publish-product]: ./media/api-management-howto-add-products/api-management-publish-product.png
[api-management-product-visiblity]: ./media/api-management-howto-add-products/api-management-product-visibility.png
[api-management-developer-list]: ./media/api-management-howto-add-products/api-management-developer-list.png



[api-management-products]: ./media/api-management-howto-add-products/api-management-products.png
[api-management-]: ./media/api-management-howto-add-products/
[api-management-]: ./media/api-management-howto-add-products/


[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps
[How to create and use groups to manage developer accounts in Azure API Management]: api-management-howto-create-groups.md
[How create and configure advanced product settings in Azure API Management]: api-management-howto-product-with-rules.md 
