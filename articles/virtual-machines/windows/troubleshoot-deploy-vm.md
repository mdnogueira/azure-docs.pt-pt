---
title: "Resolver problemas de máquina virtual do Windows implementar no Azure | Microsoft Docs"
description: "Resolver problemas de implementação problemas de máquina virtual do Windows no modelo de implementação Azurethe Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/22/2017
ms.author: genli
ms.openlocfilehash: 019ccbcec9dc344cb5ea57afdff1f81ab094dc64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Resolver problemas de máquina virtual do Windows implementar no Azure

Para resolver problemas de implementação da máquina virtual (VM) no Azure, consulte o [principais problemas](#top-issues) para falhas e resoluções comuns.

Se precisar de mais ajuda, a qualquer altura neste artigo, pode contactar as especialistas do Azure no [fóruns do MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode ficheiro um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporta**.

## <a name="top-issues"></a>Problemas principais
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

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

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>Como utilizar e implementar uma imagem de cliente do windows no Azure?

Pode utilizar Windows 7, Windows 8 ou Windows 10 no Azure para cenários de desenvolvimento/teste se tiver uma subscrição do Visual Studio (anteriormente MSDN) adequada. Isto [artigo](client-images.md) descreve os requisitos de elegibilidade para o cliente Windows em execução no Azure e utiliza das imagens da galeria do Azure.

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>Como posso implementar uma máquina virtual utilizando o benefício de utilização de híbrida (HUB)?

Existem duas formas diferentes de implementar máquinas virtuais do Windows com a vantagem de utilizar híbrida do Azure.

Para uma subscrição do Enterprise Agreement:

• Implementar VMs a partir de imagens do Marketplace específicas que são previamente configuradas com a vantagem de utilizar de híbrida do Azure.

Contrato Enterprise:

• Carregar uma VM personalizada e implementar a utilizar um modelo do Resource Manager ou o Azure PowerShell.

Para obter mais informações, consulte os seguintes recursos:

 - [Descrição geral do benefício de utilização de híbrida do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [FAQ transferível](http://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [Benefício de utilização híbridos do Azure para o Windows Server e cliente Windows](hybrid-use-benefit-licensing.md).

 - [Como utilizar a vantagem de utilizar híbridos no Azure](https://blogs.msdn.microsoft.com/azureedu/2016/04/13/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Como ativar o meu crédito mensal para Visual studio Enterprise (BizSpark)

Para ativar o crédito mensal, consulte este [artigo](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>Como adicionar Enterprise programador/teste para a minha Enterprise Agreement (EA) para obter acesso a imagens de cliente de janela?

A capacidade de criar subscrições com base na oferta de Enterprise programador/teste é restringida aos proprietários de conta que tiver recebido permissão para fazê-lo por um administrador de empresa. O proprietário da conta cria subscrições através do Portal de conta do Azure e, em seguida, deve adicionar subscritores ativos do Visual Studio como coadministradores. Para que podem gerir e utilizar os recursos necessários para desenvolvimento e testes. Para obter mais informações, consulte [Enterprise programador/teste](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>A minha controladores estão em falta para a minha VM do Windows série N

Controladores para as VMs baseadas no Windows estão localizados [aqui](n-series-driver-setup.md).

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Não é possível localizar a uma instância GPU na minha VM série N

Para tirar partido das capacidades de GPU de VMs de série N do Azure com o Windows Server 2016 ou o Windows Server 2012 R2, tem de instalar os controladores da placa NVIDIA em cada VM após a implementação. As informações de configuração de controladores estão disponíveis para [VMs do Windows](n-series-driver-setup.md) e [VMs com Linux](../linux/n-series-driver-setup.md).

## <a name="are-client-images-supported-for-n-series"></a>Imagens de cliente são suportadas para série N?

Atualmente, o Azure suporta apenas a série N em VMs com sistemas operativos Windows Server e do Linux.

## <a name="is-n-series-vms-available-in-my-region"></a>Série N VMs está disponível na minha região?

Pode verificar a disponibilidade do [produtos disponíveis por tabela de região](https://azure.microsoft.com/regions/services)e preços [aqui](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>As imagens de cliente posso utilizar e implementar no Azure e como posso obtê-las?

Pode utilizar o Windows 7, Windows 8 ou Windows 10 no Azure para cenários de desenvolvimento/teste fornecida que tem uma subscrição do Visual Studio (anteriormente MSDN) adequada. 

- Imagens do Windows 10 estão disponíveis na galeria do Azure dentro [elegível dev/teste oferece](client-images.md#eligible-offers). 
- Visual Studio os subscritores dentro de qualquer tipo de oferta também podem [adequadamente preparar e criar](prepare-for-upload-vhd-image.md) uma imagem do Windows 7, Windows 8 ou Windows 10 de 64 bits e, em seguida, [carregar para o Azure](upload-generalized-managed.md). A utilização continua a ser limitada para dev/teste por subscritores ativos do Visual Studio.

Isto [artigo](client-images.md) descreve os requisitos de elegibilidade para o cliente Windows em execução no Azure e a utilização das imagens da galeria do Azure.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Não consigo ver a família de tamanho da VM que pretendo quando redimensionar a minha VM.

Quando uma VM está em execução, que é implementado um servidor físico. Os servidores físicos em regiões do Azure estão agrupados em clusters de hardware físico comum. Redimensionar uma VM que requer que a VM ser movido para clusters de hardware diferentes é diferente consoante o modelo de implementação foi utilizado para implementar a VM.

- VMs implementadas no modelo de implementação clássica, a implementação do serviço de nuvem devem ser removidas e a implementar para alterar as VMs para um tamanho em outra família de tamanho.

- VMs implementadas no modelo de implementação do Resource Manager, tem de parar todas as VMs no conjunto antes de alterar o tamanho de qualquer VM no conjunto de disponibilidade de disponibilidade.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>O tamanho da VM listado não é suportado durante a implementação num conjunto de disponibilidade.

Escolha um tamanho que é suportado no cluster do conjunto de disponibilidade. Recomenda-se quando criar um disponibilidade definido para escolher o maior tamanho da VM pensa que precisa e tem que ser a primeira implementação para o conjunto de disponibilidade.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Pode adicionar uma VM clássico existente para um conjunto de disponibilidade?

Sim. Pode adicionar uma VM clássica existente para um novo ou existente do conjunto de disponibilidade. Para obter mais informações consulte [adicionar uma máquina virtual existente para um conjunto de disponibilidade](classic/configure-availability.md#addmachine).


## <a name="next-steps"></a>Passos seguintes
Se precisar de mais ajuda, a qualquer altura neste artigo, pode contactar as especialistas do Azure no [fóruns do MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/).

Em alternativa, pode ficheiro um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporta**.
