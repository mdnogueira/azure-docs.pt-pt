---
title: "Resolver problemas de implementação de VM com Linux-clássico | Microsoft Docs"
description: "Resolver problemas de implementação clássica, quando criar uma nova máquina virtual de Linux no Azure"
services: virtual-machines-linux
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: c8a963fa-6b2a-4c7a-a1f4-7793adb02b19
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/06/2016
ms.author: cjiang
ms.openlocfilehash: 4f97f28118e0fc6e92373f04c45450537723fce5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-classic-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Resolver problemas de implementação clássica com a criação de uma nova máquina virtual de Linux no Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-selectors](../../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-selectors-include.md)]

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para a versão do Gestor de recursos deste artigo, consulte [aqui](../troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Os registos de auditoria de recolha
Para iniciar a resolução de problemas, recolha os registos de auditoria para identificar o erro associado o problema.

No portal do Azure, clique em **procurar** > **máquinas virtuais** > *a máquina virtual do Windows* > **definições** > **registos de auditoria**.

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** se o SO é o Linux generalizado e é carregado e/ou capturado com a definição generalizada, em seguida, haverá quaisquer erros. Da mesma forma, se for o SO Linux especializada e é carregado e/ou capturado com a definição especializada, em seguida, haverá quaisquer erros.

**Carregar erros:**

**N<sup>1</sup>:** se o SO Linux generalizado, e ser carregada como especializada, obterá um erro de tempo limite de aprovisionamento porque a VM está bloqueada a fase de aprovisionamento.

**N<sup>2</sup>:** se o SO é especializada de Linux e é carregada como generalizado, obterá um erro de falha de aprovisionamento porque está a executar a nova VM com o nome do computador, nome de utilizador e palavra-passe original.

**Resolução:**

Para resolver ambos estes erros, carregue o VHD original, disponível no local, com a mesma definição que para o SO (generalizado/especializado). Para carregar como generalizado, lembre-se executar - desaprovisionar primeiro. Consulte [criar e carregar um disco rígido Virtual que contém o sistema operativo Linux](create-upload-vhd.md) para obter mais informações.

**Captura de erros:**

**N<sup>3</sup>:** se o SO Linux generalizado, e é capturado como especializada, obterá um erro de tempo limite de aprovisionamento porque a VM original não é utilizável porque está marcada como generalizado.

**N<sup>4</sup>:** se o SO é especializada de Linux e é capturado como generalizado, obterá um erro de falha de aprovisionamento porque está a executar a nova VM com o nome do computador, nome de utilizador e palavra-passe original. Além disso, original VM não é utilizável porque está marcado como especializado.

**Resolução:**

Para resolver ambos estes erros, elimine a imagem atual no portal, e [recapturá-lo a partir dos VHD atual](capture-image.md) com a mesma definição que para o SO (generalizado/especializado).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problema: Personalizada / Galeria / imagem do marketplace; Falha de alocação
Este erro for em situações quando o novo pedido VM é enviado para um cluster que não tem espaço livre disponível para acomodar o pedido, ou não suporta o tamanho da VM que está a ser solicitado. Não é possível combinar uma série diferente de VMs no mesmo serviço em nuvem. Por isso, se pretender criar uma nova VM de um tamanho diferente que o seu serviço em nuvem pode suportar, irá falhar o pedido de computação.

Consoante as restrições do serviço de nuvem que é utilizado para criar a nova VM, poderá encontrar um erro foi causado por uma das duas situações.

**Causa 1:** o serviço em nuvem está afixado para um cluster específicos, ou está ligado a um grupo de afinidade e afixado, por conseguinte, para um cluster específicos, por predefinição. Por isso, novo recurso de computação pedidos em que o grupo de afinidade são experimentadas no mesmo cluster onde estão alojados os recursos existentes. No entanto, o mesmo cluster pode não suporta o tamanho da VM pedido ou ter espaço disponível suficiente, resultando no erro de alocação. Isto acontece se os novos recursos são criados através de um novo serviço em nuvem ou através de um serviço em nuvem existente.

**Resolução 1:**

* Criar um novo serviço de nuvem e associá-la a uma região ou uma rede virtual com base na região.
* Crie uma nova VM no novo serviço em nuvem.
  Se obtiver um erro ao tentar criar um novo serviço em nuvem, tente novamente mais tarde ou alterar a região do serviço em nuvem.

> [!IMPORTANT]
> Se estava a tentar criar uma nova VM num serviço em nuvem existente, mas não foi possível e que tiveram de criar um novo serviço de nuvem para a nova VM, pode escolher a consolidação de todas as suas VMs no mesmo serviço em nuvem. Para tal, eliminar as VMs no serviço em nuvem existente e recapturá-los a partir os respetivos discos no novo serviço em nuvem. No entanto, é importante lembrar-se de que o novo serviço em nuvem terá um novo nome e VIP, por isso terá de atualizar estes para todas as dependências que a utilizam atualmente estas informações para o serviço em nuvem existente.
> 
> 

**Causa 2:** o serviço em nuvem está associado uma rede virtual que está ligada a um grupo de afinidade, pelo que está afixada para um cluster específico por predefinição. Todos os recursos de computação novos pedidos em que o grupo de afinidade, por isso, são experimentadas no mesmo cluster onde estão alojados os recursos existentes. No entanto, o mesmo cluster pode não suporta o tamanho da VM pedido ou ter espaço disponível suficiente, resultando no erro de alocação. Isto acontece se os novos recursos são criados através de um novo serviço em nuvem ou através de um serviço em nuvem existente.

**Resolução de 2:**

* Crie uma nova rede virtual regional.
* Crie nova VM na rede virtual de novo.
* [Ligar à rede virtual existente](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) para a nova rede virtual. Ver mais informações sobre [redes virtuais regionais](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/). Em alternativa, pode [migrar a rede virtual baseado em grupo de afinidade para uma rede virtual regional](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)e, em seguida, crie a nova VM.

## <a name="next-steps"></a>Passos seguintes
Se ocorrerem problemas ao iniciar uma VM com Linux parada ou redimensionar uma VM com Linux existente no Azure, consulte o artigo [resolver problemas de implementação clássica com reiniciar ou redimensionar uma Máquina Virtual de Linux existente no Azure](restart-resize-error-troubleshooting.md).

