---
title: "Portal do Azure para políticas de recursos | Microsoft Docs"
description: "Descreve como utilizar o portal do Azure para criar e gerir políticas de Gestor de recursos. Podem ser aplicadas políticas em grupos de subscrição ou recurso."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: tomfitz
ms.openlocfilehash: 868b2cc1559053057d17b34c03e2e31347f399bf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-portal-to-assign-and-manage-resource-policies"></a>Utilize o portal do Azure para atribuir e gerir políticas de recursos
O portal do Azure permite-lhe atribuir políticas de recursos a grupos de recursos e as subscrições. A interface de utilizador torna mais fácil selecionar a política que pretende atribuir e especifique os valores de parâmetros para essa política personalizar as definições de política. 

Para atribuir uma política através do portal, a definição de política tem de existir na sua subscrição. A subscrição tem várias definições de política incorporado que estão prontas para que possa atribuir aos grupos de recursos ou subscrições. Pode ver estas políticas incorporadas e as políticas personalizadas definidas quando utilizar o portal para atribuir políticas. Para uma introdução às políticas e como definir a política personalizada, consulte [descrição geral da política de recurso](resource-manager-policy.md).

As políticas são herdadas por todos os recursos subordinados. Por isso, se uma política é aplicada a um grupo de recursos, é aplicável a todos os recursos nesse grupo de recursos. Neste artigo, o termo **âmbito** refere-se para o grupo de recursos ou subscrição que está atribuída a política. 

As políticas são avaliadas quando criar e atualizar recursos (PUT e aplicar o PATCH operações).

## <a name="assign-a-policy"></a>Atribuir uma política

1. Para atribuir uma política a um grupo de recursos ou subscrição, selecione esse grupo de recursos ou subscrição. Nas definições, selecione **políticas**.

   ![Selecione as políticas](./media/resource-manager-policy-portal/select-policies.png)

2. Para criar uma atribuição de política para este âmbito, selecione **adicionar atribuição**.

   ![Adicionar atribuição](./media/resource-manager-policy-portal/add-assignment.png)

3. Selecione a política que pretende atribuir. Mesmo que não adicionou quaisquer definições de política para a sua subscrição, pode ver as políticas incorporadas que estão disponíveis para atribuição. Estas políticas incorporadas abrangem muitos cenários comuns.

   ![Selecione a definição](./media/resource-manager-policy-portal/select-definition.png)

4. Depois de selecionar uma política, pode ver uma descrição da política e quaisquer parâmetros para essa política. Por exemplo, a imagem seguinte mostra o **permitido localizações** parâmetro, que é necessário para a política que restringe as localizações disponíveis.

   ![Mostrar parâmetros](./media/resource-manager-policy-portal/show-parameters.png)

5. Através da interface de utilizador, selecione os valores para especificar para os parâmetros de política (por exemplo, as localizações que podem ser utilizadas para a implementação).

   ![Selecione os valores de parâmetros](./media/resource-manager-policy-portal/select-parameters.png)

6. Fornece valores para os outros parâmetros. O âmbito é atribuído automaticamente com base no painel selecionado ao iniciar a atribuição de política. Selecione **OK** quando terminar.

   ![definir os parâmetros](./media/resource-manager-policy-portal/define-parameters.png)

  Atribuiu a política para o âmbito especificado.

## <a name="view-policy-assignments"></a>Atribuições de política do Vista

Depois de atribuir uma política, vê-lo na lista de políticas para esse âmbito. O **detalhes** separador mostra um resumo da atribuição de política.

![Mostrar detalhes](./media/resource-manager-policy-portal/show-details.png)

O **regra de atribuição** separador mostra o JSON para a definição de política.

![Mostrar a regra de atribuição](./media/resource-manager-policy-portal/show-assignment-rule.png)

## <a name="change-an-existing-policy-assignment"></a>Alterar a atribuição de política existente

Para alterar uma política, selecione **editar atribuição** ou **eliminar**

![editar ou eliminar a atribuição](./media/resource-manager-policy-portal/edit-delete-policy.png)

## <a name="assign-custom-policies"></a>Atribuir as políticas personalizadas

Se as políticas personalizadas definidas na sua subscrição, essas políticas estão disponíveis para atribuição através do portal. Essas políticas são precedidas pela **[personalizada]**

![políticas personalizadas](./media/resource-manager-policy-portal/show-custom-policy.png)

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre a sintaxe JSON para definir políticas, consulte [descrição geral da política de recurso](resource-manager-policy.md).
* Para obter documentação de orientação sobre como as empresas podem utilizar o Resource Manager para gerir subscrições de forma eficaz, consulte [Azure enterprise scaffold - prescriptive subscription governance (Andaime empresarial do Azure - governação de subscrições prescritivas)](resource-manager-subscription-governance.md).
* O esquema de política está publicado no [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json). 

