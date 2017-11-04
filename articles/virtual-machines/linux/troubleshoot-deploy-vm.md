---
title: "Resolver problemas de máquina virtual Linux implementar no Azure | Microsoft Docs"
description: "Resolva problemas da máquina virtual Linux implementação no modelo de implementação Azurethe Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 65dc2c4ed85253b79aa47428c414d4ec8ce7e515
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-deploying-linux-virtual-machine-issues-in-azure"></a>Resolver problemas de máquina virtual Linux implementar no Azure

Para resolver problemas de implementação da máquina virtual (VM) no Azure, consulte o [principais problemas](#top-issues) para falhas e resoluções comuns.

Se precisar de mais ajuda, a qualquer altura neste artigo, pode contactar as especialistas do Azure no [fóruns do MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode ficheiro um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporta**.

## <a name="top-issues"></a>Problemas principais
[!INCLUDE [virtual-machines-linux-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>O cluster não suporta o tamanho da VM pedido
<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Repita o pedido utilizando um tamanho VM mais pequeno.
- Se não é possível alterar o tamanho da VM pedida:
    - Pare todas as VMs no conjunto de disponibilidade. Clique em **grupos de recursos** > o grupo de recursos > **recursos** > o conjunto de disponibilidade > **máquinas virtuais** > sua máquina virtual >  **Parar**.
    - Depois de parar todas as VMs, crie a VM o tamanho pretendido.
    - Iniciar a nova VM em primeiro lugar e, em seguida, selecione cada uma das VMs paradas e clique em Iniciar.


## <a name="the-cluster-does-not-have-free-resources"></a>O cluster não tem recursos gratuitos
<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Repetir o pedido mais tarde.
- Se a nova VM pode fazer parte de um conjunto de disponibilidade diferente
    - Crie uma VM numa conjunto (na mesma região) de disponibilidade diferente.
    - Adicione a nova VM para a mesma rede virtual.

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Como ativar o meu crédito mensal para Visual studio Enterprise (BizSpark)

Para ativar o crédito mensal, consulte este [artigo](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="why-can-i-not-install-the-gpu-driver-for-an-ubuntu-nv-vm"></a>Por que razão pode não instalar o controlador GPU para uma VM de NV Ubuntu?

Atualmente, o suporte de Linux GPU só está disponível em VMs do Azure NC Ubuntu Server 16.04 LTS a executar. Para obter mais informações, consulte [configurar controladores GPU para VMs de série N executar Linux](n-series-driver-setup.md).

## <a name="my-drivers-are-missing-for-my-linux-n-series-vm"></a>A minha controladores estão em falta para a minha VM do Linux série N

Controladores para as VMs baseadas em Linux estão localizadas [aqui](n-series-driver-setup.md). 

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Não é possível localizar a uma instância GPU na minha VM série N

Para tirar partido das capacidades de GPU de VMs de série N do Azure com o Windows Server 2016 ou o Windows Server 2012 R2, tem de instalar os controladores da placa NVIDIA em cada VM após a implementação. As informações de configuração de controladores estão disponíveis para [VMs do Windows](../windows/n-series-driver-setup.md) e [VMs com Linux](n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>Série N VMs está disponível na minha região?

Pode verificar a disponibilidade do [produtos disponíveis por tabela de região](https://azure.microsoft.com/regions/services)e preços [aqui](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Não consigo ver a família de tamanho da VM que pretendo quando redimensionar a minha VM.

Quando uma VM está em execução, que é implementado um servidor físico. Os servidores físicos em regiões do Azure estão agrupados em clusters de hardware físico comum. Redimensionar uma VM que requer que a VM ser movido para clusters de hardware diferentes é diferente consoante o modelo de implementação foi utilizado para implementar a VM.

- VMs implementadas no modelo de implementação clássica, a implementação do serviço de nuvem devem ser removidas e a implementar para alterar as VMs para um tamanho em outra família de tamanho.

- VMs implementadas no modelo de implementação do Resource Manager, tem de parar todas as VMs no conjunto antes de alterar o tamanho de qualquer VM no conjunto de disponibilidade de disponibilidade.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>O tamanho da VM listado não é suportado durante a implementação num conjunto de disponibilidade.

Escolha um tamanho que é suportado no cluster do conjunto de disponibilidade. Recomenda-se quando criar um disponibilidade definido para escolher o maior tamanho da VM pensa que precisa e tem que ser a primeira implementação para o conjunto de disponibilidade.

## <a name="what-linux-distributionsversions-are-supported-on-azure"></a>Quais as distribuições de Linux/as versões são suportadas no Azure?

Pode encontrar a lista em Linux no [Azure-Endorsed distribuições](endorsed-distros.md).

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Pode adicionar uma VM clássico existente para um conjunto de disponibilidade?

Sim. Pode adicionar uma VM clássica existente para um novo ou existente do conjunto de disponibilidade. Para obter mais informações consulte [adicionar uma máquina virtual existente para um conjunto de disponibilidade](../windows/classic/configure-availability.md#addmachine).


## <a name="next-steps"></a>Passos seguintes
Se precisar de mais ajuda, a qualquer altura neste artigo, pode contactar as especialistas do Azure no [fóruns do MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/).

Em alternativa, pode ficheiro um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporta**.
