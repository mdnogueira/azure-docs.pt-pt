---
title: Transferir itens do marketplace a partir do Azure | Microsoft Docs
description: "Posso pode transferir itens do marketplace a partir do Azure para a minha implementação da pilha do Azure."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/03/2017
ms.author: erikje
ms.openlocfilehash: 4d7c335a3c68cc9bb8cb0c823883716a3dd6620a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Transferir os itens do marketplace a partir do Azure com a pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Como decidir o conteúdo que pretende incluir na sua marketplace de pilha do Azure, deve considerar o conteúdo disponível no Azure Marketplace. Pode transferir a partir de uma lista organizada de itens do marketplace do Azure que tenham sido previamente testada para executar na pilha do Azure. Novos itens frequentemente são adicionados a esta lista, por isso certifique-se de verificação para o novo conteúdo.

Para transferir os itens do marketplace, deve primeiro [registar pilha do Azure com o Azure](azure-stack-register.md). 

## <a name="download"></a>Transferência
1. Inicie sessão no portal de administrador do Azure pilha (https://portal.local.azurestack.external).
2. Alguns itens do marketplace podem ser muito grandes.  Certifique-se de que tem espaço suficiente no seu sistema clicando **fornecedores de recursos** > **armazenamento**.

    ![](media/azure-stack-download-azure-marketplace-item/image01.png)

3. Clique em **mais serviços** > **Marketplace gestão**.

    ![](media/azure-stack-download-azure-marketplace-item/image02.png)

4. Clique em **adicionar a partir do Azure** para ver uma lista de itens disponíveis para transferência. Pode clicar em cada item na lista para visualizar a descrição e tamanho de transferência.

    ![](media/azure-stack-download-azure-marketplace-item/image03.png)

5. Selecione o item que pretende na lista e, em seguida, clique em **transferir**. Esta ação inicia a transferência da imagem VM para o item selecionado. Tempo de transferência variar.

    ![](media/azure-stack-download-azure-marketplace-item/image04.png)

6. Depois de concluída a transferência, pode implementar o novo item do marketplace como um operador de pilha do Azure ou o utilizador. Clique em **+ novo**, pesquisar entre as categorias para o novo item do marketplace e, em seguida, selecione o item.
7. Clique em **criar** para abrir a experiência de criação para o item recentemente transferido. Siga as instruções passo a passo para implementar o item.

## <a name="next-steps"></a>Passos seguintes

[Criar e publicar um item do Marketplace](azure-stack-create-and-publish-marketplace-item.md)
