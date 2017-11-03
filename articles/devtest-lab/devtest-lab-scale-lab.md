---
title: "Quotas e limites de escala no seu laboratório no Azure DevTest Labs | Microsoft Docs"
description: "Saiba como dimensionar um laboratório no Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: ae624155-9181-45fa-bd2b-1983339b7e0e
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: tarcher
ms.openlocfilehash: f11ed42b474e4f208eac92689bfa33fb188d15a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Quotas e limites de escala no DevTest Labs
À medida que trabalha no DevTest Labs, poderá reparar que existem determinados limites predefinido a alguns recursos do Azure, o que podem afetar o serviço DevTest Labs. Estes limites são referidas como **quotas**.

> [!NOTE]
> O serviço DevTest Labs não impõe quaisquer quotas. Quaisquer quotas que podem surgir são restrições predefinidas da subscrição global do Azure.

Pode utilizar cada recurso do Azure, até atingir a quota. Cada subscrição tem quotas separadas e utilização é controlada por subscrição.

Por exemplo, cada subscrição tem uma quota de predefinição de 20 núcleos. Por isso, se estiver a criar VMs no seu laboratório com quatro núcleos, em seguida, pode criar apenas cinco VMs. 

[Subscrição e limites de serviço da Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits) lista algumas das quotas mais comuns para recursos do Azure. Os recursos normalmente utilizadas num laboratório e para que poderá encontrar quotas incluem núcleos VM, endereços IP públicos, interface de rede, discos geridos, atribuição de função RBAC e circuitos do ExpressRoute.

## <a name="view-your-usage-and-quotas"></a>Ver a sua utilização e quotas
Estes passos mostram como para ver as quotas atuais na sua subscrição para recursos do Azure específicos e para ver que percentagem de cada quota que utilizou.

1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **mais serviços**e, em seguida, selecione **faturação** da lista.
1. No painel de faturação, selecione uma subscrição.
4. Selecione **utilização + quotas**.

   ![Botão de utilização e quotas](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas.png)

   A utilização + quotas é apresentado o painel, listagem de diferentes recursos disponíveis nessa subscrição e a percentagem de quota que está a ser utilizado por recursos.

   ![Quotas e utilização](./media/devtest-lab-scale-lab/devtestlab-view-quotas.png)

## <a name="requesting-more-resources-in-your-subscription"></a>Pedir mais recursos na sua subscrição
Se atingir um limite de quota, o limite predefinido de um recurso de uma subscrição pode ser aumentado até um limite máximo, conforme descrito em [subscrição do Azure e limites de serviço](https://docs.microsoft.com/azure/azure-subscription-service-limits).

Estes passos mostram como pedir um aumento de quota através de [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **mais serviços**, selecione **faturação**e, em seguida, selecione **utilização + quotas**.
1. Na utilização + quotas painel, selecione o **pedido aumentar** botão.

   ![Botão de aumento de pedido](./media/devtest-lab-scale-lab/devtestlab-request-increase.png)

1. Para concluir e submeter o pedido, preencha as informações necessárias em todos os três separadores do **novo pedido de suporte** formulário.

   ![Formulário de pedido de aumento](./media/devtest-lab-scale-lab/devtestlab-support-form.png)

[Noções sobre Azure limites e aumenta](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) fornece mais informações sobre como contactar o suporte do Azure para pedir um aumento de quota.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Passos seguintes
* Explorar o [Galeria de modelo de início rápido do DevTest Labs do Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
