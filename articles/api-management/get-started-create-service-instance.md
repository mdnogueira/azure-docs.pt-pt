---
title: "Criar uma instância de API Management do Azure | Microsoft Docs"
description: "Siga os passos deste tutorial para criar uma nova instância de API Management do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/17/2017
ms.author: apimpm
ms.openlocfilehash: 6433ea1f0eb6ad375402b998b4dfa80bded35c4b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-new-azure-api-management-service-instance"></a>Criar uma nova instância de serviço de API Management do Azure

Este tutorial descreve os passos para criar uma nova API de gestão instância utilizando o [portal do Azure](https://portal.azure.com/).

## <a name="prerequisites"></a>Pré-requisitos

+ Uma subscrição ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-new-service"></a>Criar um novo serviço

1. No [portal do Azure](https://portal.azure.com/), selecione **novo** > **integração empresarial com** > **gestão de API**.

    Em alternativa, optar **novo**, tipo `API management` na caixa de pesquisa e prima Enter. Clique em **Criar**.

2. No **serviço de API Management** janela, introduza um único **nome** para o seu serviço de API Management. Este nome não é possível alterar mais tarde.

    > [!TIP]
    > Nome de serviço é utilizado para gerar um nome de domínio predefinido sob a forma de *{name} .azure-api.net.* Se gostaria de utilizar um nome de domínio personalizado, consulte [configurar um domínio personalizado](configure-custom-domain.md). <br/>
    > Nome de serviço é utilizado para fazer referência para o serviço e o recurso do Azure correspondente.

5. Selecione um **subscrição** entre as diferentes subscrições do Azure que tem acesso.
6. Em **Grupo de Recursos**, selecione o recurso novo ou existente.  Um grupo de recursos é uma coleção de recursos que partilham o ciclo de vida, as permissões e as políticas. Sabia mais [aqui](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. No **localização**, selecione a região geográfica onde o serviço de API Management é criado. Apenas as regiões de serviço de API Management disponíveis são apresentadas na caixa de lista pendente. 
9. Introduza um **nome da organização**. Este nome é utilizado em vários locais. Por exemplo, o título do portal do programador e remetente de e-mails de notificação.
10. No **e-mail do administrador**, defina endereço de e-mail que todas as notificações de **API Management** será enviado.
11. No **escalão de preço**, defina **programador** camada para avaliar o serviço. Esta camada não é para utilização em produção. Para obter mais informações sobre dimensionamento as camadas de API Management, consulte [atualizar e dimensionar](upgrade-and-scale.md).
12. Escolha **Criar**.

    > [!TIP]
    > Normalmente, demora entre 30 e 20 minutos para criar um serviço de API Management. Selecionar **afixar ao dashboard** torna localizar um serviço criado recentemente mais fácil.

## <a name="next-steps"></a>Passos seguintes

[Publicar uma API com a API Management do Azure](#api-management-getstarted-publish-api.md)