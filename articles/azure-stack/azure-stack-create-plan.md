---
title: Criar um plano na pilha do Azure | Microsoft Docs
description: "Como um administrador da nuvem, crie um plano que permite que os subscritores aprovisionar máquinas virtuais."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/10/2017
ms.author: erikje
ms.openlocfilehash: 30759dca746fd7fd02653556cb105f419f5bf854
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-plan-in-azure-stack"></a>Criar um plano no Azure Stack

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Os [Planos](azure-stack-key-features.md) consistem em agrupamentos de um ou mais serviços. Como um fornecedor, pode criar planos para oferecer aos seus utilizadores. Por sua vez, os utilizadores subscrevem as ofertas para utilizar os serviços incluem e planos. Este exemplo mostra como criar um plano que inclui computação, rede e os fornecedores de recursos de armazenamento. Este plano permite subscritores aprovisionar as máquinas virtuais.

1. Inicie sessão no portal de administrador do Azure pilha (https://adminportal.local.azurestack.external). Introduza as credenciais da conta que criou durante o passo 5 do [executar o script PowerShell](azure-stack-run-powershell-script.md) secção.

2. Para criar um plano e a oferta que os utilizadores podem subscrever, clique em **novo** > **inquilino oferece + planos** > **plano**.

   ![](media/azure-stack-create-plan/image01.png)
3. No **planear novo** painel, preencha **nome a apresentar** e **nome do recurso**. O nome de apresentação é o nome amigável do plano e que os utilizadores veem. Apenas o administrador pode ver o Nome do Recurso. É o nome que administradores utilizam para trabalhar com o plano como um recurso do Azure Resource Manager.

   ![](media/azure-stack-create-plan/image02.png)
4. Crie um novo **grupo de recursos**, ou selecione um existente, como um contentor para o plano.

   ![](media/azure-stack-create-plan/image02a.png)
5. Clique em **serviços**, selecione **Microsoft. Compute**, **Network**, e **Microsoft**e, em seguida, clique em **Selecione**.

   ![](media/azure-stack-create-plan/image03.png)
6. Clique em **Quotas**, clique em **Microsoft (local)**e, em seguida, selecione a quota predefinida ou clique em **criar nova quota** para personalizar a quota.

   ![](media/azure-stack-create-plan/image04.png)
7. Se estiver a criar uma nova quota, introduza um nome para a quota > definir os valores de quota > clique **OK** > clique no nome da nova quota.

   ![](media/azure-stack-create-plan/image06.png)
8. Clique em **Network (local)**e, em seguida, selecione a quota predefinida ou clique em **criar nova quota** para personalizar a quota.

    ![](media/azure-stack-create-plan/image07.png)
9. Se estiver a criar uma quota de novo, escreva um nome para a quota > definir os valores de quota > clique **OK** > clique no nome da nova quota.

    ![](media/azure-stack-create-plan/image08.png)
10. Clique em **Microsoft. Compute (local)**e, em seguida, selecione a quota predefinida ou clique em **criar nova quota** para personalizar a quota.

    ![](media/azure-stack-create-plan/image09.png)
11. Se estiver a criar uma quota de novo, escreva um nome para a quota > definir os valores de quota > clique **OK** > clique no nome da nova quota.

    ![](media/azure-stack-create-plan/image10.png)
12. No **Quotas** painel, clique em **OK**e, em seguida, no **novo plano** painel, clique em **criar** para criar o plano.

    ![](media/azure-stack-create-plan/image11.png)
13. Para ver o novo plano, clique em **todos os recursos**, em seguida, procure o plano e clique no respetivo nome.

    ![](media/azure-stack-create-plan/image12.png)

### <a name="next-steps"></a>Passos seguintes
[Criar uma oferta](azure-stack-create-offer.md)
