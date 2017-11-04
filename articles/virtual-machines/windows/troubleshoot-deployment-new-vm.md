---
title: "Resolver problemas de implementação de VM do Windows no Azure | Microsoft Docs"
description: "Resolver problemas de implementação do Resource Manager, quando criar uma nova máquina virtual do Windows no Azure"
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: JiangChen79
manager: willchen
editor: 
tags: top-support-issue, azure-resource-manager
ms.assetid: afc6c1a4-2769-41f6-bbf9-76f9f23bcdf4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: cjiang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a3a2f53c7abc1f2dfba90a30f4830df1abe63f5d
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>Resolver problemas de implementação quando criar uma nova VM do Windows no Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Problemas principais
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

Para outros problemas de implementação da VM e perguntas, consulte [resolver problemas de máquina virtual do Windows implementar no Azure](troubleshoot-deploy-vm.md).

## <a name="collect-activity-logs"></a>Regista a atividade de recolha
Para iniciar a resolução de problemas, recolha os registos de atividade para identificar o erro associado o problema. As hiperligações seguintes contêm informações detalhadas sobre o processo a seguir.

[Ver as operações de implementação](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Ver registos de atividade para gerir recursos do Azure](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**Y:** se o SO é o Windows generalizado e é carregado e/ou capturado com a definição generalizada, em seguida, haverá quaisquer erros. Da mesma forma, se o SO é o Windows especializada e é carregado e/ou capturado com a definição especializada, em seguida, haverá quaisquer erros.

**Carregar erros:**

**N<sup>1</sup>:** se o SO é o Windows generalizado e ser carregada como especializada, obterá um erro de tempo limite de aprovisionamento com a VM bloqueada no ecrã de OOBE.

**N<sup>2</sup>:** se o SO é especializada do Windows e de ser carregada como generalizado, obterá um erro de falha de aprovisionamento com a VM bloqueada no ecrã de OOBE porque está a executar a nova VM com o nome do computador, nome de utilizador e palavra-passe original.

**Resolução**

Para resolver ambos estes erros, utilize [Add-AzureRmVhd para carregar o VHD original](https://msdn.microsoft.com/library/mt603554.aspx), disponível no local, com a mesma definição que para o SO (generalizado/especializado). Para carregar como generalizado, lembre-se primeiro a executar o sysprep.

**Captura de erros:**

**N<sup>3</sup>:** se o SO é o Windows generalizado e é capturado como especializada, obterá um erro de tempo limite de aprovisionamento porque a VM original não é utilizável porque está marcada como generalizado.

**N<sup>4</sup>:** se o SO é especializada do Windows e é capturado como generalizado, obterá um erro de falha de aprovisionamento porque está a executar a nova VM com o nome do computador original, nome de utilizador e palavra-passe. Além disso, original VM não é utilizável porque está marcado como especializado.

**Resolução**

Para resolver ambos estes erros, elimine a imagem atual no portal, e [recapturá-lo a partir dos VHD atual](create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) com a mesma definição que para o SO (generalizado/especializado).

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Problema: Imagem personalizada/Galeria/marketplace; Falha de alocação
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
Se ocorrerem problemas ao iniciar uma VM do Windows parada ou redimensionar uma VM existente do Windows no Azure, consulte o artigo [problemas de implementação do Gestor de recursos de resolução de problemas com reiniciar ou redimensionar uma Máquina Virtual existente do Windows no Azure](restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

