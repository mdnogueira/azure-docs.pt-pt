---
title: "Azure baseada em funções controlo de acesso (RBAC) para controlar direitos de acesso para criar e gerir pedidos de suporte | Microsoft Docs"
description: "Azure baseada em funções controlo de acesso (RBAC) para controlar direitos de acesso para criar e gerir pedidos de suporte"
author: ganganarayanan
ms.author: gangan
ms.date: 1/31/2017
ms.topic: article
ms.service: microsoft-docs
ms.assetid: 58a0ca9d-86d2-469a-9714-3b8320c33cf5
ms.openlocfilehash: 20ebd324cbf379980b43d255d468673de2b6d950
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-role-based-access-control-rbac-to-control-access-rights-to-create-and-manage-support-requests"></a>Azure baseada em funções controlo de acesso (RBAC) para controlar direitos de acesso para criar e gerir pedidos de suporte

[Controlo de acesso baseado em funções (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is) permite uma gestão pormenorizada de acesso para o Azure.
Suporta a criação de pedido no portal do Azure, [portal.azure.com](https://portal.azure.com), utiliza o modelo RBAC do Azure para definir quem pode criar e gerir pedidos de suporte.
É concedido acesso ao atribuir a função RBAC adequada aos utilizadores, grupos e aplicações num determinado âmbito, que pode ser uma subscrição, o grupo de recursos ou um recurso.

Vejamos um exemplo: como um proprietário do grupo de recursos com permissões de leitura no âmbito de subscrição, pode gerir todos os recursos sob o grupo de recursos, como Web sites, sub-redes e máquinas virtuais.
No entanto, quando tentar criar um pedido de suporte contra o recurso de máquina virtual, ocorrer o seguinte erro

![Erro de subscrição](./media/create-manage-support-requests-using-access-control/subscription-error.png)

Na gestão de pedidos de suporte, tem de escrever permissão ou uma função que tenha a ação de suporte Microsoft.Support/* no âmbito de subscrição para conseguir criar e gerir pedidos de suporte.

O seguinte artigo explica como pode utilizar personalizado baseada em funções controlo de acesso do Azure (RBAC) para criar e gerir pedidos de suporte no portal do Azure.

## <a name="getting-started"></a>Introdução

Utilizando o exemplo acima, será capaz de criar um pedido de suporte para o seu recurso se foram atribuída uma função RBAC personalizada na subscrição, o proprietário da subscrição.
[Funções do RBAC personalizadas](https://azure.microsoft.com/documentation/articles/role-based-access-control-custom-roles/) podem ser criadas utilizando o Azure PowerShell, a Interface de linha de comandos do Azure (CLI) e a API REST.

A propriedade de ações de uma função personalizada especifica as operações do Azure para os quais a função concede acesso.
Para criar uma função personalizada para gestão de pedidos de suporte, a função tem de ter a ação Microsoft.Support/*

Eis um exemplo de uma função personalizada, que pode utilizar para criar e gerir pedidos de suporte.
Iremos tiver com o nome desta função de "Contribuinte de pedido de suporte" e que é como podemos para a função personalizada neste artigo.

``` Json
{
    "Name":  "Support Request Contributor",
    "Id":  "1f2aad59-39b0-41da-b052-2fb070bd7942",
    "IsCustom":  true,
    "Description":  "Lets you create and manage support tickets.",
    "Actions":  [
                    "Microsoft.Support/*"
                ],
    "NotActions":  [
                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

Siga os passos descritos em [este vídeo](https://www.youtube.com/watch?v=-PaBaDmfwKI) para saber como criar uma função personalizada para a sua subscrição.

## <a name="create-and-manage-support-requests-in-the-azure-portal"></a>Criar e gerir pedidos de suporte no portal do Azure

Vejamos um exemplo – é o proprietário da subscrição "Visual Studio subscrição do MSDN."
João é o elemento de rede que é um proprietário de recursos para alguns dos grupos de recursos nesta subscrição e tem permissão de leitura para a subscrição.
Pretende conceder acesso à sua ponto a ponto, João, a capacidade de criar e gerir pedidos de suporte para os recursos pertencentes a esta subscrição.

1. O primeiro passo é para ir para a subscrição e, em "Definições" é apresentada uma lista de utilizadores. Clique o utilizador João quem tem acesso de leitor na subscrição e vamos atribuir uma nova função de personalizada a ele.

    ![Adicionar função](./media/create-manage-support-requests-using-access-control/add-role.png)

2. Clique em "Adicionar" sob o painel "Utilizadores". Selecione a função personalizada "Contribuinte de pedido de suporte" da lista de funções

    ![Adicionar a função de contribuinte de suporte](./media/create-manage-support-requests-using-access-control/add-support-contributor-role.png)

3. Depois de selecionar o nome da função, clique em "Adicionar utilizadores" e introduza as credenciais de correio eletrónico a Joe. Clique em "Selecionar"

    ![Adicionar utilizadores](./media/create-manage-support-requests-using-access-control/add-users.png)

4. Clique em "Ok" para continuar

    ![Adicionar acesso](./media/create-manage-support-requests-using-access-control/add-access.png)

5. Agora, pode ver o utilizador com a função personalizada recém-adicionada "Suporte contribuinte de pedido" sob a subscrição para o qual é o proprietário

    ![Utilizador adicionado](./media/create-manage-support-requests-using-access-control/user-added.png)

    Quando Joe inicia sessão no portal, João vê a subscrição à qual foi adicionado.

7. João clica em "Novo pedido de suporte" a partir do painel "Ajuda e suporte" e pode criar pedidos de suporte para "Visual Studio Ultimate com MSDN"

    ![Novo pedido de suporte](./media/create-manage-support-requests-using-access-control/new-support-request.png)

8. Clicar em "Todos os pedidos de suporte" João pode ver a lista de pedidos de suporte criado para esta subscrição ![vista de detalhes de maiúsculas e minúsculas](./media/create-manage-support-requests-using-access-control/case-details-view.png)

## <a name="remove-support-request-access-in-the-azure-portal"></a>Remover o acesso de pedido de suporte no portal do Azure

Tal como é possível conceder acesso a um utilizador para criar e gerir pedidos de suporte, é possível remover o acesso do utilizador, bem como.
Para remover a capacidade de criar e gerir pedidos de suporte, aceda à subscrição, clique em "Definições" e clique de utilizador (neste caso, o João).
O nome da função, "Contribuinte de pedido de suporte" com o botão direito e clique em "Remover"

![Remover o acesso de pedido de suporte](./media/create-manage-support-requests-using-access-control/remove-support-request-access.png)

Quando Joe inicia sessão no portal e tenta criar um pedido de suporte, ele encontra o seguinte erro

![Erro-2 de subscrição](./media/create-manage-support-requests-using-access-control/subscription-error-2.png)

Não é possível ver Joe suportar pedidos de quando clica "Todos os pedidos de suporte"

![Vista de detalhes maiúsculas-2](./media/create-manage-support-requests-using-access-control/case-details-view-2.png)
