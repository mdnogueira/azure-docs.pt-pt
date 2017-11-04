---
title: Problemas de VM, reiniciar ou redimensionar no Azure | Microsoft Docs
description: "Resolver problemas de implementação do Resource Manager com reiniciar ou redimensionar uma Máquina Virtual existente do Windows no Azure"
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: Deland-Han
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 0756b52d-4f5a-4503-ae45-c00a6a2edcdf
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.workload: required
ms.date: 11/03/2017
ms.author: delhan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2fecb9ebf359e3d3d23d5af0f60882cdfac5ff45
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>Resolver problemas de implementação com reiniciar ou redimensionar uma VM existente do Windows no Azure
Ao tentar iniciar uma Máquina Virtual do Azure (VM) parada ou redimensionar uma VM do Azure existente, o erro comum que ocorrerem é uma falha de alocação. Este erro resulta quando o cluster ou a região não dispõe de recursos disponíveis ou não suporta o tamanho da VM pedido.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>Regista a atividade de recolha
Para iniciar a resolução de problemas, recolha os registos de atividade para identificar o erro associado o problema. As hiperligações seguintes contêm informações detalhadas sobre o processo:

[Ver as operações de implementação](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Ver registos de atividade para gerir recursos do Azure](../../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problema: Erro ao iniciar uma VM parada
Tente iniciar uma VM parada mas obtêm uma falha de alocação.

### <a name="cause"></a>Causa
O pedido para iniciar a VM parada tem de ser tentada no cluster original que aloja o serviço em nuvem. No entanto, o cluster não tem espaço livre disponível para satisfazer o pedido.

### <a name="resolution"></a>Resolução
* Parar todas as VMs no conjunto de disponibilidade e, em seguida, reiniciar cada VM.
  
  1. Clique em **grupos de recursos** > *o grupo de recursos* > **recursos** > *o conjunto de disponibilidade* > **máquinas virtuais** > *a máquina virtual* > **parar**.
  2. Depois de parar todas as VMs, selecione cada uma das VMs paradas e clique em Iniciar.
* Repita o pedido de reinício numa altura posterior.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problema: Ocorreu um erro ao redimensionar uma VM existente
Tente redimensionar uma VM existente, mas obtêm uma falha de alocação.

### <a name="cause"></a>Causa
O pedido para redimensionar a VM tem de ser tentada no cluster original que aloja o serviço em nuvem. No entanto, o cluster não suporta o tamanho da VM pedido.

### <a name="resolution"></a>Resolução
* Repita o pedido utilizando um tamanho VM mais pequeno.
* Se não é possível alterar o tamanho da VM pedida:
  
  1. Pare todas as VMs no conjunto de disponibilidade.
     
     * Clique em **grupos de recursos** > *o grupo de recursos* > **recursos** > *o conjunto de disponibilidade* > **máquinas virtuais** > *a máquina virtual* > **parar**.
  2. Depois de parar todas as VMs, redimensione a VM pretendida para um tamanho maior.
  3. Selecione a VM redimensionada e clique em **iniciar**, e, em seguida, inicie cada uma das VMs paradas.

## <a name="next-steps"></a>Passos seguintes
Se ocorrerem problemas ao criar uma nova VM do Windows no Azure, consulte o artigo [resolver problemas de implementação com a criação de nova máquina virtual do Windows Azure](troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

