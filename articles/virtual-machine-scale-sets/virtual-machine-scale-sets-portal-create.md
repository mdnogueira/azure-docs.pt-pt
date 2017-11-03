---
title: "Criar um conjunto de dimensionamento de Máquina Virtual no portal do Azure | Microsoft Docs"
description: Implemente os conjuntos de dimensionamento com o portal do Azure.
keywords: "Conjuntos de dimensionamento de máquina virtual"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: 9c1583f0-bcc7-4b51-9d64-84da76de1fda
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: negat
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fc52368a1a14ad094e7ab9180b90a9aa4ccdb6d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-the-azure-portal"></a>Como criar um conjunto de dimensionamento de Máquina Virtual com o portal do Azure
Este tutorial mostra como é fácil criar um conjunto de dimensionamento de Máquina Virtual em apenas alguns minutos, utilizando o portal do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="choose-the-vm-image-from-the-marketplace"></a>Selecione a imagem VM do marketplace
No portal, pode facilmente implementar uma escala com CentOS, CoreOS, Debian, Ubuntu Server, outras imagens de Linux e imagens do Windows Server.

Em primeiro lugar, navegue para o [portal do Azure](https://portal.azure.com) num web browser. Clique em **novo**, procure **conjunto de dimensionamento**e, em seguida, selecione o **conjunto de dimensionamento da Máquina Virtual** entrada:

![pesquisa de portal de conjunto de dimensionamento de máquina virtual do Azure](./media/virtual-machine-scale-sets-portal-create/portal-search.png)

## <a name="create-the-scale-set"></a>Crie o conjunto de dimensionamento
Agora pode utilizar as predefinições e criar rapidamente o conjunto de dimensionamento.

* Introduza um nome para o conjunto de dimensionamento.  
Este nome torna-se a base do FQDN do Balanceador de carga à frente do conjunto de dimensionamento, por isso certifique-se de que o nome é exclusivo em todas as do Azure.

* Selecione o tipo de SO pretendido.

* Introduza o seu nome de utilizador pretendido e selecione o tipo de autenticação que preferir.  
Se escolher uma palavra-passe, tem de ser, pelo menos, 12 carateres longos e cumprir os três fora os seguintes requisitos de complexidade quatro: uma minúscula, uma maiúscula, um número e um caráter especial. Saiba mais sobre os [requisitos de nomes de utilizador e palavras-passe](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm). Se optar por **chave pública SSH**ser colar se apenas a chave pública, *não* a chave privada:

* Selecione **Sim** ou **não** para **ativar o dimensionamento para além de 100 instâncias**.  
Se Sim, o conjunto de dimensionamento de pode abranger em vários grupos de colocação. Para obter mais informações, consulte [esta documentação](./virtual-machine-scale-sets-placement-groups.md).

* Certifique-se de que seleciona um adequado **instância tamanho**.  
Para obter mais informações acerca dos tamanhos da máquina virtual, consulte [tamanhos de Windows VM](..\virtual-machines\windows\sizes.md) ou [tamanhos de VM com Linux](..\virtual-machines\linux\sizes.md).

* Introduza o nome do grupo de recursos desejados e localização.  
Se a região e **instância tamanho** suporta zonas de disponibilidade, o **zonas de disponibilidade** campo está ativado. Para obter mais informações sobre as zonas de disponibilidade, consulte este [descrição geral](../availability-zones/az-overview.md) artigo.

* Introduza a etiqueta do nome de domínio pretendida (a base do FQDN para o Balanceador de carga à frente do conjunto de dimensionamento).  
Esta etiqueta deve ser exclusiva em todas as do Azure.

* Escolha a sua imagem de disco do sistema operativo pretendido, a contagem de instâncias e o tamanho da máquina.

* Escolher o tipo de disco desejado: geridos ou não geridos.  
Para obter mais informações, consulte [esta documentação](./virtual-machine-scale-sets-managed-disks.md). Se optar por ter o conjunto de dimensionamento span vários grupos de posicionamento, esta opção não estará disponível porque o disco gerido é necessário para conjuntos de dimensionamento abranger os grupos de colocação.

* Ativar ou desativar o dimensionamento automático e configurar se estiver ativada.

![portal de conjunto de dimensionamento de máquina virtual do Azure criar linha de comandos](./media/virtual-machine-scale-sets-portal-create/portal-create.png)

## <a name="connect-to-a-vm-in-the-scale-set"></a>Ligar a uma VM no conjunto de dimensionamento
Se tiver escolhido limitar o conjunto a um grupo de posicionamento única de dimensionamento, o conjunto de dimensionamento com a em seguida, é implementado com regras NAT configuradas para permitir estabelecer ligação com o facilmente conjunto de dimensionamento (caso contrário, para ligar às máquinas virtuais no conjunto de dimensionamento, provavelmente, necessita de criar um jumpbox no mesmo  rede virtual que o conjunto de dimensionamento). Para vê-los, navegue para o `Inbound NAT Rules` separador do Balanceador de carga para o conjunto de dimensionamento:

![regras de portal nat de conjunto de dimensionamento de máquina virtual do Azure](./media/virtual-machine-scale-sets-portal-create/portal-nat-rules.png)

Pode ligar para cada VM em escala definida utilizando estas regras NAT. Por exemplo, para um conjunto de dimensionamento do Windows, se existir uma regra NAT de entrada porta 50000, foi ligar a esse computador através de RDP no `<load-balancer-ip-address>:50000`. Para um conjunto de dimensionamento do Linux, ligaria utilizando o comando `ssh -p 50000 <username>@<load-balancer-ip-address>`.

## <a name="next-steps"></a>Passos seguintes
Para obter documentação sobre como implementar o dimensionamento define a partir da CLI do, consulte [esta documentação](virtual-machine-scale-sets-cli-quick-create.md).

Para obter documentação sobre como implementar o dimensionamento define a partir do PowerShell, consulte [esta documentação](virtual-machine-scale-sets-windows-create.md).

Para obter documentação sobre como implementar o dimensionamento define a partir do Visual Studio, consulte [esta documentação](virtual-machine-scale-sets-vs-create.md).

Para obter documentação geral, veja o [página de descrição geral de documentação para conjuntos de dimensionamento](virtual-machine-scale-sets-overview.md).

Para obter informações gerais, veja o [página de destino principal para conjuntos de dimensionamento](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

