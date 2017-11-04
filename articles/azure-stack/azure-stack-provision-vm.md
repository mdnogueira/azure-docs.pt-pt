---
title: Criar uma VM de teste na pilha do Azure | Microsoft Docs
description: Saiba como aprovisionar um teste de VM na pilha do Azure como um operador da nuvem.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: c86646e1-a12e-493f-b396-f17bfacd60c2
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: erikje
ms.openlocfilehash: 233cf4df53af6a49e5fe4c5d51e112d8196a7530
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-test-virtual-machine-in-azure-stack"></a>Criar uma máquina virtual de teste na pilha do Azure

*Aplica-se a: Azure da pilha Kit de desenvolvimento*

Como um operador de pilha do Azure, pode criar uma máquina virtual de teste para validar a sua [Azure pilha](azure-stack-poc.md) implementação Developer Kit.

> [!NOTE]
> Antes de pode aprovisionar máquinas virtuais, deve [adicionar a imagem de avaliação do Windows Server 2016 para o mercado de pilha do Azure](azure-stack-add-default-image.md).
> 
> 

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
1. No anfitrião do Kit de desenvolvimento de pilha do Azure, [sessão](azure-stack-connect-azure-stack.md) para o portal de administrador (`https://adminportal.local.azurestack.external`) e, em seguida, clique em **novo** > **computação**  >  **Datacenter do Windows Server 2016 Eval** > **criar**.  
2. No **Noções básicas** painel, escreva um **nome**, **nome de utilizador**, e **palavra-passe**. Escolha um **subscrição**. Criar um **grupo de recursos**, ou selecione um existente e, em seguida, clique em **OK**.  
3. No **escolher um tamanho** painel, clique em **A1 padrão**e, em seguida, clique em **selecione**.  
4. No **definições** painel, aceite as predefinições e clique em **OK**
5. No painel **Resumo**, clique em **OK** para criar a máquina virtual.  
6. Para ver a nova máquina virtual, clique em **todos os recursos**, em seguida, procure a máquina virtual e clique no respetivo nome.
    ![](media/azure-stack-provision-vm/image06.png)


## <a name="next-steps"></a>Passos seguintes
[Os portais de administrador e utilizador a utilizar na pilha do Azure](azure-stack-manage-portals.md)
