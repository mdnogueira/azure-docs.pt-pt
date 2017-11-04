---
title: "Resolver problemas de implementação de VM com Linux-RM | Microsoft Docs"
description: "Resolver problemas de implementação do Resource Manager, quando criar uma nova máquina virtual de Linux no Azure"
services: virtual-machines-linux, azure-resource-manager
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue, azure-resource-manager
ms.assetid: 906a9c89-6866-496b-b4a4-f07fb39f990c
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/09/2016
ms.author: cjiang
ms.openlocfilehash: 64db243400fed063d1336c448ed6991877cca72d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Resolver problemas de implementação do Resource Manager com a criação de uma nova máquina virtual de Linux no Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Problemas principais
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

Para outros problemas de implementação da VM e perguntas, consulte [resolver problemas de máquina virtual Linux implementar no Azure](troubleshoot-deploy-vm.md).
## <a name="collect-activity-logs"></a>Regista a atividade de recolha
Para iniciar a resolução de problemas, recolha os registos de atividade para identificar o erro associado o problema. As hiperligações seguintes contêm informações detalhadas sobre o processo a seguir.

[Ver as operações de implementação](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Ver registos de atividade para gerir recursos do Azure](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** se o SO é o Linux generalizado e é carregado e/ou capturado com a definição generalizada, em seguida, haverá quaisquer erros. Da mesma forma, se for o SO Linux especializada e é carregado e/ou capturado com a definição especializada, em seguida, haverá quaisquer erros.

**Carregar erros:**

**N<sup>1</sup>:** se o SO Linux generalizado, e ser carregada como especializada, obterá um erro de tempo limite de aprovisionamento porque a VM está bloqueada a fase de aprovisionamento.

**N<sup>2</sup>:** se o SO é especializada de Linux e é carregada como generalizado, obterá um erro de falha de aprovisionamento porque está a executar a nova VM com o nome do computador, nome de utilizador e palavra-passe original.

**Resolução:**

Para resolver ambos estes erros, carregue o VHD original, disponível no local, com a mesma definição que para o SO (generalizado/especializado). Para carregar como generalizado, lembre-se executar - desaprovisionar primeiro.

**Captura de erros:**

**N<sup>3</sup>:** se o SO Linux generalizado, e é capturado como especializada, obterá um erro de tempo limite de aprovisionamento porque a VM original não é utilizável porque está marcada como generalizado.

**N<sup>4</sup>:** se o SO é especializada de Linux e é capturado como generalizado, obterá um erro de falha de aprovisionamento porque está a executar a nova VM com o nome do computador, nome de utilizador e palavra-passe original. Além disso, original VM não é utilizável porque está marcado como especializado.

**Resolução:**

Para resolver ambos estes erros, elimine a imagem atual no portal, e [recapturá-lo a partir dos VHD atual](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) com a mesma definição que para o SO (generalizado/especializado).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problema: Personalizada / Galeria / imagem do marketplace; Falha de alocação
Este erro for em situações quando o novo pedido VM está afixado para um cluster que não suporta o tamanho da VM que está a ser solicitado, ou não tem espaço livre disponível para acomodar o pedido.

**Causa 1:** o cluster não suporta o tamanho da VM pedido.

**Resolução 1:**

* Repita o pedido utilizando um tamanho VM mais pequeno.
* Se não é possível alterar o tamanho da VM pedida:
  * Pare todas as VMs no conjunto de disponibilidade.
    Clique em **grupos de recursos** > *o grupo de recursos* > **recursos** > *o conjunto de disponibilidade* > **máquinas virtuais** > *a máquina virtual* > **parar**.
  * Depois de parar todas as VMs, crie a nova VM o tamanho pretendido.
  * Em primeiro lugar, a iniciar a nova VM e, em seguida, selecione cada uma das VMs paradas e clique em **iniciar**.

**Causa 2:** o cluster não tem recursos gratuitos.

**Resolução de 2:**

* Repita o pedido mais tarde.
* Se a nova VM pode fazer parte de um conjunto de disponibilidade diferente
  * Crie uma nova VM numa conjunto (na mesma região) de disponibilidade diferente.
  * Adicione a nova VM para a mesma rede virtual.

## <a name="next-steps"></a>Passos seguintes
Se ocorrerem problemas ao iniciar uma VM com Linux parada ou redimensionar uma VM com Linux existente no Azure, consulte o artigo [problemas de implementação do Gestor de recursos de resolução de problemas com reiniciar ou redimensionar uma Máquina Virtual de Linux existente no Azure](restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

