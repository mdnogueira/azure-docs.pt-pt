---
title: Criar uma oferta no Azure pilha | Microsoft Docs
description: Como um administrador da nuvem, saiba como criar uma oferta para os utilizadores na pilha do Azure.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 96b080a4-a9a5-407c-ba54-111de2413d59
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/10/2017
ms.author: erikje
ms.openlocfilehash: 269a6106f657536ba74be366f842b2f9cd86c5dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-offer-in-azure-stack"></a>Criar uma oferta no Azure Stack

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

[Oferece](azure-stack-key-features.md) são grupos de um ou mais planos que Fornecedores aos utilizadores para comprar ou subscrever. Este documento mostra como criar uma oferta, que inclui o [plano que criou](azure-stack-create-plan.md) no último passo. Esta oferta proporciona a capacidade para aprovisionar as máquinas virtuais de subscritores.

1. Inicie sessão no portal de administrador a pilha do Azure (https://adminportal.local.azurestack.external) > clique **novo** > **inquilino oferece + planos**  >   **Oferecer**.

   ![](media/azure-stack-create-offer/image01.png)
2. No **oferecem novo** painel, preencha **nome a apresentar** e **nome do recurso**e, em seguida, selecione o novo ou existente **grupo de recursos**. O nome a apresentar é o nome amigável a oferta e é a única informação sobre a oferta que os utilizadores veem quando subscrever. Por conseguinte, não se esqueça de utilizar um nome intuitivo que ajuda o utilizador compreende que vem com a oferta. Apenas o administrador pode ver o Nome do Recurso. Trata-se do nome que o administrador utiliza para trabalhar com a oferta como um recurso do Azure Resource Manager.

   ![](media/azure-stack-create-offer/image01a.png)
3. Clique em **Planos base** e, no painel **Plano**, selecione os planos que pretende incluir na oferta e, em seguida, clique em **Selecionar**. Clique em **Criar** para criar a oferta.

   ![](media/azure-stack-create-offer/image02.png)
4. Clique em **todos os recursos**, procure a sua oferta novo, clique na oferta da nova, clique em **alteração de estado**e, em seguida, clique em **pública**.

   ![](media/azure-stack-create-offer/image03.png)

As ofertas têm de ser efetuadas públicas para os utilizadores obter a vista completa, quando subscrever. Ofertas podem ser:

* **Pública**: visíveis para os utilizadores.
* **Privada**: apenas visíveis para os administradores de nuvem. Útil ao drafting o plano ou a oferta, ou se o administrador da nuvem que pretende aprovar cada subscrição.
* **Fora de Utilização**: fechados a novos subscritores. Pode utilizar o administrador da nuvem desativado impedir futuras subscrições, mas deixe os subscritores atuais inalterados.

As alterações para a oferta não são imediatamente visíveis para o utilizador. Para ver as alterações, poderá ter de terminar sessão/início de sessão para ver a nova subscrição no "seleccionador de subscrição" quando criar grupos de recursos/recursos.

> [!NOTE]
>Também pode criar ofertas predefinido, esquemas e quotas através do PowerShell conforme explicado no [Leia-me do administrador de serviços do Azure pilha](https://github.com/Azure/AzureStack-Tools/tree/master/ServiceAdmin).
>


### <a name="next-steps"></a>Passos seguintes
[Subscrever uma oferta e aprovisionar uma VM](azure-stack-subscribe-plan-provision-vm.md)
