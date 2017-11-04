---
title: Gerir contas de programador, utilizar grupos na API Management do Azure | Microsoft Docs
description: Saiba como gerir contas de programador, utilizar grupos na API Management do Azure
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 33660b45-442f-44be-9a4a-1899d7f699b0
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 00322340d67fd064a0dc9149790e031b94390709
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Como criar e utilizar grupos para gerir contas de programador na API Management do Azure
Na API Management, os grupos são utilizados para gerir a visibilidade dos produtos para os programadores. Produtos primeiro ficam visíveis para os grupos, e, em seguida, os programadores esses grupos podem ver e subscrever os produtos que estão associados os grupos. 

A API Management tem os seguintes grupos de sistema imutáveis.

* **Administradores** – os administradores da subscrição do Azure são membros deste grupo. Os administradores gerem as instâncias de serviço de API Management, criando as APIs, as operações e os produtos que são utilizados pelos programadores.
* **Programadores** – os utilizadores autenticados do portal do programador enquadram-se neste grupo. Os programadores são os clientes que criam aplicações utilizando as suas APIs. Os programadores têm acesso ao portal do programador e criam aplicações que chamam as operações de uma API
* **Convidados** – os utilizadores não autenticados do portal do programador, tais como potenciais clientes que visitam o portal do programador de uma instância de API Management, enquadram-se neste grupo. Estes podem ter determinados acessos só de leitura, como a capacidade de ver APIs mas não de as chamar.

Para além destes grupos de sistema, os administradores podem criar grupos personalizados ou [tirar partido de grupos externos em inquilinos do Azure Active Directory associados][leverage external groups in associated Azure Active Directory tenants]. É possível utilizar grupos personalizados e externos em conjunto com grupos de sistema para dar aos programadores acesso e visibilidade a produtos de API. Por exemplo, pode criar um grupo personalizado para os programadores associados a uma organização parceira específica e permitir-lhes o acesso às APIs a partir de um produto que contém apenas APIs relevantes. Um utilizador pode ser membro de mais do que um grupo.

Este guia mostra como os administradores de uma instância de API Management podem adicionar novos grupos e associá-las com produtos e os programadores.

> [!NOTE]
> Além de criar e gerir grupos no portal do publicador, pode criar e gerir os grupos utilizando a API de REST de gestão de API [grupo](https://msdn.microsoft.com/library/azure/dn776329.aspx) entidade.
> 
> 

## <a name="create-group"></a>Criar um grupo
Para criar um novo grupo, clique em **portal do publicador** no Portal do Azure para o seu serviço de API Management. Isto leva-o para o portal do publicador da API Management.

![Portal do publicador][api-management-management-console]

> Se ainda não criou uma instância de serviço de Gestão de API, consulte [Criar uma instância de serviço de Gestão de API][Create an API Management service instance] no tutorial [Introdução à Gestão de API do Azure][Get started with Azure API Management].
> 
> 

Clique em **grupos** do **API Management** menu à esquerda e, em seguida, clique em **adicionar grupo**.

![Adicionar novo grupo][api-management-add-group]

Introduza um nome exclusivo para o grupo e uma descrição opcional e clique em **guardar**.

![Adicionar novo grupo][api-management-add-group-window]

O novo grupo é apresentado no separador de grupos. Para editar o **nome** ou **Descrição** do grupo, clique no nome do grupo na lista. Para eliminar o grupo, clique em **eliminar**.

![Grupo adicionado][api-management-new-group]

Agora que o grupo for criado, pode ser associado com produtos e os programadores.

## <a name="associate-group-product"></a>Associar um grupo com um produto
Para associar um grupo de um produto, clique em **produtos** do **API Management** menu à esquerda e, em seguida, clique no nome do produto pretendido.

![Visibilidade de conjunto][api-management-add-group-to-product]

Selecione o **visibilidade** separador para adicionar e remover grupos e ver os grupos para o produto atuais. Para adicionar ou remover grupos, selecione ou desmarque as caixas de verificação para os grupos de pretendida e clique em **guardar**.

![Visibilidade de conjunto][api-management-add-group-to-product-visibility]

> [!NOTE]
> Para adicionar grupos do Active Directory do Azure, consulte [como autorizar contas de programador na API Management do Azure utilizando o Azure Active Directory](api-management-howto-aad.md).
> 
> Para configurar grupos do **visibilidade** separador de um produto, clique em **gerir grupos**.
> 
> 

Depois de um produto é associado um grupo, os programadores nesse grupo podem ver e subscrever o produto.

## <a name="associate-group-developer"></a>Associar grupos a programadores
Associar grupos a programadores, clique em **utilizadores** do **API Management** menu à esquerda e, em seguida, selecione a caixa junto as programadores que pretende associar a um grupo.

![Adicionar programador ao grupo][api-management-add-group-to-developer]

Assim que os programadores pretendidos são verificados, clique no grupo pretendido no **adicionar ao grupo** pendente. Os programadores podem ser removidos do grupos utilizando o **remover do grupo** pendente. 

![Programadores][api-management-add-group-to-developer-saved]

Assim que é adicionada a associação entre o programador e o grupo, pode vê-la no **utilizadores** separador.

## <a name="next-steps"> </a>Passos seguintes
* Assim que um programador é adicionado a um grupo, pode ver e subscrever os produtos associados a esse grupo. Para obter mais informações, consulte [como criar e publicar um produto na API Management do Azure][How create and publish a product in Azure API Management],
* Além de criar e gerir grupos no portal do publicador, pode criar e gerir os grupos utilizando a API de REST de gestão de API [grupo](https://msdn.microsoft.com/library/azure/dn776329.aspx) entidade.

[api-management-management-console]: ./media/api-management-howto-create-groups/api-management-management-console.png
[api-management-add-group]: ./media/api-management-howto-create-groups/api-management-add-group.png
[api-management-add-group-window]: ./media/api-management-howto-create-groups/api-management-add-group-window.png
[api-management-new-group]: ./media/api-management-howto-create-groups/api-management-new-group.png
[api-management-add-group-to-product]: ./media/api-management-howto-create-groups/api-management-add-group-to-product.png
[api-management-add-group-to-product-visibility]: ./media/api-management-howto-create-groups/api-management-add-group-to-product-visibility.png
[api-management-add-group-to-developer]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer.png
[api-management-add-group-to-developer-saved]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer-saved.png

[api-management-]: ./media/api-management-howto-create-groups/api-management-.png

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md#how-to-add-an-external-azure-active-directory-group
