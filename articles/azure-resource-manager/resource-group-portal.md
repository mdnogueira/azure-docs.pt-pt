---
title: Utilize o portal do Azure para gerir recursos do Azure | Microsoft Docs
description: Utilize o portal do Azure e o Azure Resource Manager para gerir os recursos. Mostra como funcionar com dashboards para monitorizar os recursos.
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 0725bbf2-5913-4c07-af6e-24e11d957fbc
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2016
ms.author: tomfitz
ms.openlocfilehash: 27213482c3ef6b35e1e3f887c9a336b946850802
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="manage-azure-resources-through-portal"></a>Gerir recursos do Azure através do portal

Este artigo mostra como utilizar o [portal do Azure](https://portal.azure.com) com [do Azure Resource Manager](resource-group-overview.md) para gerir os recursos do Azure. Para saber mais sobre a implementação de recursos através do portal, consulte [implementar recursos com modelos do Resource Manager e o portal do Azure](resource-group-template-deploy-portal.md).

## <a name="manage-resource-groups"></a>Gerir grupos de recursos

Um grupo de recursos é um contentor que retém recursos relacionados para uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que pretende gerir como um grupo. Decida como pretende atribuir recursos a grupos de recursos com base no que é mais adequado para a sua organização. Geralmente, adicione recursos que partilham o mesmo ciclo de vida para o mesmo grupo de recursos para que possa facilmente implementar, atualizar e eliminá-los como um grupo. 

O grupo de recursos armazena metadados sobre os recursos. Por conseguinte, quando especifica uma localização para o grupo de recursos, está a especificar onde esses metadados estão armazenados. Por motivos de conformidade, poderá ter de certificar que os dados estão armazenados numa determinada região.

1. Para ver todos os grupos de recursos na sua subscrição, selecione **grupos de recursos**.
   
    ![procurar grupos de recursos](./media/resource-group-portal/browse-groups.png)
2. Para criar um grupo de recursos vazio, selecione **adicionar**.
   
    ![Adicionar grupo de recursos](./media/resource-group-portal/add-resource-group.png)
3. Forneça um nome e uma localização para o novo grupo de recursos. Selecione **Criar**.
   
    ![Criar grupo de recursos](./media/resource-group-portal/create-empty-group.png)
4. Poderá ter de selecionar **atualizar** para ver o grupo de recursos recentemente criado.
   
    ![Atualizar o grupo de recursos](./media/resource-group-portal/refresh-resource-groups.png)
5. Para personalizar as informações apresentadas para os grupos de recursos, selecione **colunas**.
   
    ![Personalizar colunas](./media/resource-group-portal/select-columns.png)
6. Selecionar as colunas para adicionar e, em seguida, selecione **atualização**.
   
    ![Adicionar colunas](./media/resource-group-portal/add-columns.png)
7. Para saber mais sobre a implementação de recursos para o novo grupo de recursos, consulte [implementar recursos com modelos do Resource Manager e o portal do Azure](resource-group-template-deploy-portal.md).
8. Para obter acesso rápido a um grupo de recursos, pode afixar o grupo de recursos ao seu dashboard.
   
    ![grupo de recursos de PIN](./media/resource-group-portal/pin-group.png)
9. O dashboard apresenta o grupo de recursos e os respetivos recursos. Pode selecionar os grupos de recursos ou qualquer um dos respetivos recursos para navegar para o item.
   
    ![grupo de recursos de PIN](./media/resource-group-portal/show-resource-group-dashboard.png)

## <a name="tag-resources"></a>Etiqueta de recursos
Pode aplicar etiquetas a grupos de recursos e recursos para organizar logicamente os recursos. Para obter informações sobre como trabalhar com etiquetas, consulte [utilizar etiquetas para organizar os recursos do Azure](resource-group-using-tags.md).

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="monitor-resources"></a>Monitorizar recursos
Quando seleciona um recurso, o portal apresenta gráficos predefinida e tabelas para a monitorização esse tipo de recurso.

1. Selecione um recurso e repare o **monitorização** secção. Inclui gráficos que são relevantes para o tipo de recurso. A imagem seguinte mostra as predefinições de monitorização de dados para uma conta de armazenamento.
   
    ![Mostrar a monitorização](./media/resource-group-portal/show-monitoring.png)
2. Pode afixar uma secção ao seu dashboard, selecionando o botão de reticências (…) acima da secção. Também pode personalizar o tamanho de secção ou remova-a completamente. A imagem seguinte mostra como afixar, personalizar ou remover a secção de CPU e memória.
   
    ![secção de PIN](./media/resource-group-portal/pin-cpu-section.png)
3. Após da afixação a secção para o dashboard, irá ver o resumo no dashboard. Além disso, selecionando-o imediatamente leva-o para obter mais detalhes sobre os dados.
   
    ![Veja o dashboard](./media/resource-group-portal/view-startboard.png)
4. Para personalizar por completo os dados monitorizar através do portal, navegue até ao seu dashboard predefinido e selecione **novo dashboard**.
   
    ![dashboard](./media/resource-group-portal/dashboard.png)
5. Dê um nome do seu novo dashboard e arraste mosaicos para o dashboard. Os mosaicos são filtrados por diferentes opções.
   
    ![dashboard](./media/resource-group-portal/create-dashboard.png)
   
     Para saber mais sobre como trabalhar com dashboards, consulte [criar e partilhar dashboards no portal do Azure](../azure-portal/azure-portal-dashboards.md).

## <a name="manage-resources"></a>Gerir recursos
Ao visualizar um recurso no portal, consulte as opções para gerir esse recurso específico.

![gerir recursos](./media/resource-group-portal/manage-resources.png)

Entre estas opções, pode efetuar operações como iniciar e parar uma máquina virtual ou reconfigurar as propriedades da máquina virtual.

## <a name="move-resources"></a>Mover recursos
Se precisar de mover recursos para outro grupo de recursos ou outra subscrição, consulte [mover recursos para o novo grupo de recursos ou subscrição](resource-group-move-resources.md).

## <a name="lock-resources"></a>Bloquear recursos
Pode bloquear uma subscrição, grupo de recursos ou recursos para impedir que outros utilizadores na sua organização acidentalmente eliminem ou modificar a recursos críticos. Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](resource-group-lock-resources.md).

[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="view-your-subscription-and-costs"></a>Ver a sua subscrição e os custos
Pode ver informações sobre a sua subscrição e os custos agregados para todos os seus recursos. Selecione **subscrições** e a subscrição que pretende ver. Só pode ter uma subscrição para selecionar.

![subscrição](./media/resource-group-portal/select-subscription.png)

Consulte a taxa de gravado.

![taxa de gravado](./media/resource-group-portal/burn-rate.png)

Além disso, uma repartição dos custos por tipo de recurso.

![custo de recursos](./media/resource-group-portal/cost-by-resource.png)

## <a name="export-template"></a>Exportar modelo
Depois de configurar o grupo de recursos, poderá ver o modelo do Resource Manager para o grupo de recursos. Exportar o modelo proporciona duas vantagens:

1. Pode automatizar facilmente implementações futuras da solução porque o modelo contém toda a infraestrutura completa.
2. Familiarize-com a sintaxe do modelo ao procurar em JavaScript Object Notation (JSON) que representa a sua solução.

Para obter orientações passo a passo, consulte [modelo de exportar o Azure Resource Manager a partir dos recursos existentes](resource-manager-export-template.md).

## <a name="delete-resource-group-or-resources"></a>Eliminar grupo de recursos ou recursos
Eliminar um grupo de recursos elimina todos os recursos contidos. Também pode eliminar recursos individuais dentro de um grupo de recursos. Tenha cuidado quando eliminar um grupo de recursos. Nesse grupo de recursos pode conter recursos que dependam de recursos de outros grupos de recursos.

![Eliminar grupo](./media/resource-group-portal/delete-group.png)

## <a name="next-steps"></a>Passos seguintes
* Para ver registos de atividade, consulte [auditar operações com o Resource Manager](resource-group-audit.md).
* Para ver detalhes sobre uma implementação, consulte [ver as operações de implementação](resource-manager-deployment-operations.md).
* Para implementar recursos através do portal, consulte [implementar recursos com modelos do Resource Manager e o portal do Azure](resource-group-template-deploy-portal.md).
* Para gerir o acesso aos recursos, consulte [utilize atribuições de funções para gerir o acesso aos recursos da sua subscrição do Azure](../active-directory/role-based-access-control-configure.md).
* Para obter documentação de orientação sobre como as empresas podem utilizar o Resource Manager para gerir subscrições de forma eficaz, consulte [Azure enterprise scaffold - prescriptive subscription governance (Andaime empresarial do Azure - governação de subscrições prescritivas)](resource-manager-subscription-governance.md).

