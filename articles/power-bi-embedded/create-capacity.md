---
title: Criar a capacidade de Power BI Embedded no portal do Azure | Microsoft Docs
description: Este artigo explica como criar uma capacidade Power BI Embedded no Microsoft Azure.
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: 1902e5c18cd7083ceeda79e6b9e779e4baaf175a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-power-bi-embedded-capacity-in-the-azure-portal"></a>Criar a capacidade de Power BI Embedded no portal do Azure

Este artigo explica como criar uma capacidade Power BI Embedded no Microsoft Azure. Power BI Embedded simplifica as capacidades do Power BI, ajudando-o a adicionar rapidamente fantásticas visuais, relatórios e dashboards para as suas aplicações.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!VIDEO https://www.youtube.com/embed/aXrvFfg_iSk]

## <a name="before-you-begin"></a>Antes de começar

Para concluir este guia de início rápido, necessita de:

* **Subscrição do Azure:** visite [avaliação gratuita do Azure](https://azure.microsoft.com/free/) para criar uma conta.
* **Azure Active Directory:** sua subscrição tem de ser associada a um inquilino do Azure Active Directory (AAD). E, ***tem de ter sessão iniciada no Azure com uma conta nesse inquilino***. Não são suportadas contas Microsoft. Para obter mais informações, consulte as permissões de autenticação e utilizador.
* **Inquilino do Power BI:** pelo menos uma conta no seu inquilino do AAD tem de ter inscrito para o Power BI.
* **Grupo de recursos:** utilizar um grupo de recursos já tiver ou [criar um novo](../azure-resource-manager/resource-group-overview.md).

## <a name="create-a-capacity"></a>Criar uma capacidade

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. Selecione **+ (novo)** > **dados + análise**.

3. Na caixa de pesquisa, procure *Power BI Embedded*.

4. No Power BI Embedded, selecione **criar**.

5. Preencha as informações necessárias e, em seguida, selecione **criar**.

    ![Campos de preenchimento para criar nova capacidade](media/create-capacity/azure-portal-create-power-bi-embedded.png)

    |Definição |Descrição |
    |---------|---------|
    |**Nome do recurso**|Um nome para identificar a capacidade. O nome de recurso é apresentado no portal de administração do Power BI, além de portal do Azure.|
    |**Subscrição**|A subscrição que pretende criar a capacidade contra.|
    |**Grupo de recursos**|O grupo de recursos que contém esta nova capacidade. Escolha um grupo de recursos existente ou criar outro. Para obter mais informações, veja [Descrição geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).|
    |**Administrador de capacidade do Power BI**|Os administradores de capacidade do Power BI podem ver a capacidade no portal de administração do Power BI e atribua permissões de atribuição a outros utilizadores. Por predefinição, o administrador de capacidade está a sua conta. O administrador de capacidade tem de estar no seu inquilino do Power BI.|
    |**Localização**|A localização em que o Power BI está alojado para o seu inquilino. Esta definição é resolvida automaticamente, que não é possível selecionar uma localização diferente.|
    |**Escalão de preço**|Selecione o SKU (v core contagem e a memória tamanho) que satisfaça as suas necessidades.  Para obter mais informações, consulte [preços do Power BI Embedded](https://azure.microsoft.com/pricing/details/power-bi-embedded/)|

6. Selecione **Criar**.

Criação normalmente demora sob um minuto; muitas vezes, apenas alguns segundos. Se tiver selecionado **afixar ao dashboard**, navegue até ao seu dashboard para ver a capacidade de novo. Ou, navegue para **mais serviços** > **Power BI Embedded** para ver se a sua capacidade está pronta.

![Dashboard do portal do Azure com Power BI Embedded capacidade](media/create-capacity/azure-portal-dashboard.png)

## <a name="next-steps"></a>Passos seguintes

Para utilizar a nova capacidade de Power BI Embedded, navegue para o portal de administração do Power BI para atribuir áreas de trabalho. Para obter mais informações, veja [Gerir capacidades no Power BI Premium e no Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-admin-premium-manage/).

Se não precisa de utilizar esta capacidade, coloque em pausa para parar a faturação. Para obter mais informações, consulte [colocar em pausa e iniciar a sua capacidade de Power BI Embedded no portal do Azure](pause-start.md).

Para começar a incorporar o conteúdo do Power BI na sua aplicação, consulte [como incorporar dashboards do Power BI, relatórios e mosaicos](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

Mais perguntas? [Tente pedir a Comunidade do Power BI](http://community.powerbi.com/)