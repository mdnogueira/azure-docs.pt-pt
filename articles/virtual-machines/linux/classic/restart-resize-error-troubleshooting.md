---
title: VM reiniciar ou redimensionar problemas | Microsoft Docs
description: "Resolver problemas de implementação clássica com reiniciar ou redimensionar uma Máquina Virtual de Linux existente no Azure"
services: virtual-machines-linux
documentationcenter: 
author: Deland-Han
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 73f2672c-602e-4766-8948-2b180115d299
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: required
ms.date: 01/10/2017
ms.devlang: na
ms.author: delhan
ms.openlocfilehash: 391653e8bb85489bb3ec3554e347ace09f92095d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-classic-deployment-issues-with-restarting-or-resizing-an-existing-linux-virtual-machine-in-azure"></a>Resolver problemas de implementação clássica com reiniciar ou redimensionar uma Máquina Virtual de Linux existente no Azure
> [!div class="op_single_selector"]
> * [Clássico](restart-resize-error-troubleshooting.md)
> * [Resource Manager](../restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

Ao tentar iniciar uma Máquina Virtual do Azure (VM) parada ou redimensionar uma VM do Azure existente, o erro comum que ocorrerem é uma falha de alocação. Este erro resulta quando o cluster ou a região não dispõe de recursos disponíveis ou não suporta o tamanho da VM pedido.

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para a versão do Gestor de recursos, consulte [aqui](../restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Os registos de auditoria de recolha
Para iniciar a resolução de problemas, recolha os registos de auditoria para identificar o erro associado o problema.

No portal do Azure, clique em **procurar** > **máquinas virtuais** > *a máquina virtual do Linux* > **definições** > **registos de auditoria**.

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problema: Erro ao iniciar uma VM parada
Tente iniciar uma VM parada mas obtêm uma falha de alocação.

### <a name="cause"></a>Causa
O pedido para iniciar a VM parada tem de ser tentada no cluster original que aloja o serviço em nuvem. No entanto, o cluster não tem espaço livre disponível para satisfazer o pedido.

### <a name="resolution"></a>Resolução
* Criar um novo serviço de nuvem e associá-la a uma região ou uma rede virtual com base na região, mas não é um grupo de afinidade.
* Elimine a VM parada.
* Recrie a VM com o novo serviço em nuvem, utilizando os discos.
* Inicie a VM recriada.

Se obtiver um erro ao tentar criar um novo serviço em nuvem, tente novamente mais tarde ou alterar a região do serviço em nuvem.

> [!IMPORTANT]
> O novo serviço em nuvem terá um novo nome e VIP, por isso terá de alterar essas informações para todas as dependências que utilizem essas informações para o serviço em nuvem existente.
> 
> 

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problema: Ocorreu um erro ao redimensionar uma VM existente
Tente redimensionar uma VM existente, mas obtêm uma falha de alocação.

### <a name="cause"></a>Causa
O pedido para redimensionar a VM tem de ser tentada no cluster original que aloja o serviço em nuvem. No entanto, o cluster não suporta o tamanho da VM pedido.

### <a name="resolution"></a>Resolução
Reduzir o tamanho da VM solicitado e repita o pedido de redimensionamento.

* Clique em **Procurar tudo** > **máquinas virtuais (clássicas)** > *a máquina virtual* > **definições** > **tamanho**. Para obter passos detalhados, consulte [redimensionar a máquina virtual](https://msdn.microsoft.com/library/dn168976.aspx).

Se não for possível reduzir o tamanho da VM, siga estes passos:

* Crie um novo serviço de nuvem, assegurar que não é ligado a um grupo de afinidade e não associado a uma rede virtual que está ligada a um grupo de afinidade.
* Crie uma VM nova, maior dimensão no mesmo.

Pode consolidar todas as suas VMs no mesmo serviço em nuvem. Se o serviço em nuvem existente está associado uma rede virtual com base na região, pode ligar o novo serviço em nuvem para a rede virtual existente.

Se o serviço em nuvem existente não estiver associado uma rede virtual com base na região, em seguida, tem de eliminar as VMs no serviço em nuvem existente e recriá-las no novo serviço de nuvem dos respetivos discos. No entanto, é importante lembrar-se de que o novo serviço em nuvem terá um novo nome e VIP, por isso terá de atualizar estes para todas as dependências que a utilizam atualmente estas informações para o serviço em nuvem existente.

## <a name="next-steps"></a>Passos seguintes
Se ocorrerem problemas ao criar uma nova VM com Linux no Azure, consulte o artigo [resolver problemas de implementação com a criação de uma nova máquina virtual de Linux no Azure](../troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

