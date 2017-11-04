---
title: "Como implementar um serviço Web em várias regiões | Microsoft Docs"
description: "Passos para implementar regiões (copiar) um novo serviço Web para si."
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: cgronlun
ms.assetid: 36c60411-f2db-4ee2-9b66-b1f1d77a8f44
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: v-donglo
ms.openlocfilehash: 8e616bd6f13c4f97886a3eae225e188afadb2305
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-deploy-a-web-service-to-multiple-regions"></a>Como implementar um serviço Web em várias regiões
Novos serviços Web do Azure permitem-lhe facilmente implementar um serviço web para várias regiões sem necessitar de várias subscrições ou áreas de trabalho. 

Preços é a região específico, por conseguinte, tem de definir um plano de faturação para cada região na qual irá implementar o serviço web.

## <a name="to-create-a-plan-in-another-region"></a>Para criar um plano noutra região
1. Inicie sessão no [do Microsoft Azure serviços Web Machine Learning](https://services.azureml.net/).
2. Clique em de **planos** opção do menu.
3. Os planos através de página vista, clique em **novo**.
4. Do **subscrição** lista pendente, selecione a subscrição na qual o novo plano irá residir.
5. Do **região** lista pendente, selecione uma região do novo plano. As opções de plano para a região selecionada será apresentado no **planear opções** secção da página.
6. Do **grupo de recursos** lista pendente, selecione um recurso de grupo para o plano. A partir de mais informações sobre grupos de recursos, consulte [descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
7. No **nome do plano** escreva o nome do plano.
8. Em **as opções do plano**, clique o nível para o novo plano de faturação.
9. Clique em **Criar**.

## <a name="deploying-the-web-service-to-another-region"></a>Implementar o serviço web noutra região
1. Clique em de **serviços Web** opção do menu.
2. Selecione o serviço Web estiver a implementar uma região de novo.
3. Clique em **cópia**.
4. No **nome do serviço Web**, escreva um novo nome para o serviço web.
5. No **Web descrição do serviço**, digite uma descrição para o serviço web.
6. Do **subscrição** lista pendente, selecione a subscrição na qual o novo serviço web irá residir.
7. Do **grupo de recursos** lista pendente, selecione um recurso de grupo para o serviço web. A partir de mais informações sobre grupos de recursos, consulte [descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
8. Do **região** lista pendente, selecione a região na qual pretende implementar o serviço web.
9. Do **conta de armazenamento** lista pendente, selecione o armazenamento da conta na qual pretende armazenar o serviço web.
10. Do **plano de preços** lista pendente, selecione um plano na região que selecionou no passo 8.
11. Clique em **cópia**.

