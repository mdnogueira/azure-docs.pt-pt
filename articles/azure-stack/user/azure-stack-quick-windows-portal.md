---
title: "Rápido do Azure de pilha iniciar - criar uma máquina virtual do Windows"
description: "Rápido do Azure de pilha iniciar - criar uma VM do Windows através do portal"
services: azure-stack
author: ErikjeMS
manager: byronr
ms.service: azure-stack
ms.topic: azure-stack
ms.date: 09/15/2017
ms.author: erikje
ms.custom: mvc
ms.openlocfilehash: abca538f28bbc0a8f3f00311ca1a69d196f10272
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-windows-virtual-machine-with-the-azure-stack-portal"></a>Criar uma máquina virtual do Windows com o portal de pilha do Azure

Pode criar uma máquina virtual do Windows utilizando o portal de pilha do Azure. O portal é uma interface de utilizador baseada no browser, onde pode criar, configurar e gerir os recursos.

## <a name="sign-in-to-the-azure-stack-portal"></a>Inicie sessão no portal do Azure pilha

Inicie sessão no portal do Azure pilha. O endereço do portal do Azure pilha depende o produto de pilha do Azure ao qual está a ligar:

* Para o Azure pilha Development Kit (ASDK), aceda a: https://portal.local.azurestack.external.
* Para um sistema de pilha do Azure integrado, avance para o URL que o operador de pilha do Azure fornecido.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Clique em **novo** > **computação** > **Datacenter do Windows Server 2016 Eval** > **criar**. Se não vir **Windows Server 2016 Datacenter Eval** entrada, contacte o operador de pilha do Azure. Peça que eles adicioná-lo para o mercado conforme explicado no [adicionar a imagem de VM do Windows Server 2016 para o mercado de pilha do Azure](../azure-stack-add-default-image.md) artigo. 
    ![](media/azure-stack-quick-windows-portal/image01.png)
2. Em **Noções básicas**, escreva um **nome**, **nome de utilizador**, e **palavra-passe**. Escolha um **subscrição**. Criar um **grupo de recursos**, ou selecione um existente um, selecione um **localização**e, em seguida, clique em **OK**.

    ![](media/azure-stack-quick-windows-portal/image02.png)
3. Em **escolher um tamanho**, clique em **D1 padrão** > **selecione**.
    ![](media/azure-stack-quick-windows-portal/image03.png)
4. Em **definições**, aceite as predefinições e clique em **OK**.
    ![](media/azure-stack-quick-windows-portal/image04.png)
5. Em **resumo**, clique em **OK** para criar a máquina virtual. 
    ![](media/azure-stack-quick-windows-portal/image05.png)
6. Para ver a nova máquina virtual, clique em **todos os recursos**, em seguida, procure a máquina virtual e clique no respetivo nome.
    ![](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não necessita da máquina virtual, elimine o grupo de recursos, a máquina virtual e todos os recursos relacionados. Para tal, selecione o grupo de recursos a partir da página da máquina virtual e clique em **eliminar**.

## <a name="next-steps"></a>Passos seguintes
Este guia de introdução, implementou uma máquina virtual simple do Windows. Para saber mais sobre as máquinas virtuais de pilha do Azure, avance para [considerações para máquinas virtuais no Azure pilha](azure-stack-vm-considerations.md).
