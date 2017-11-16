---
title: "Configurar o endereçamento IP para ligar após a ativação pós-falha para o Azure com o Azure Site Recovery | Microsoft Docs"
description: "Descreve como configurar a ligação para VMs do Azure após a ativação pós-falha no local com o Azure Site Recovery de endereçamento IP"
services: site-recovery
documentationcenter: 
author: prateek9us
manager: carmonm
editor: 
ms.assetid: f02cdbea-0940-48bf-9fa5-f38d9e584fae
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/14/2017
ms.author: pratshar
ms.openlocfilehash: 5519a965d9828cfa1e73ba12f8acd1d509a36a66
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="set-up-ip-addressing-to-connect-after-failover-to-azure"></a>Configurar a ligação após a ativação pós-falha para o Azure de endereçamento IP

Este artigo explica os requisitos de rede para estabelecer a ligação para VMs do Azure, depois de utilizar o [do Azure Site Recovery](site-recovery-overview.md) serviço de replicação e ativação pós-falha para o Azure.

Neste artigo, irá aprender:

> [!div class="checklist"]
> * Pode utilizar os métodos de ligação
> * Como utilizar um endereço IP diferentes para replicar as VMs do Azure
> * Como manter os endereços IP para VMs do Azure após a ativação pós-falha

## <a name="connecting-to-replica-vms"></a>Ligar para a réplica de VMs

Ao planear a replicação e a estratégia de ativação pós-falha, uma das perguntas chaves é como ligar à VM do Azure após a ativação pós-falha. Existem duas opções ao conceber a estratégia de rede para a réplica de VMs do Azure:

- **Utilize o endereço IP diferente**:, pode optar por utilizar um intervalo de endereços IP diferentes para a rede VM do Azure replicada. Neste cenário a VM obtém um novo endereço IP após a ativação pós-falha e é necessária uma atualização DNS.
- **Manter o mesmo endereço IP**: pode querer utilizar o mesmo intervalo de endereços IP que no site primário no local, para a rede Azure após a ativação pós-falha. Manter o mesmo IP endereços simplifica a recuperação ao reduzir relacionadas com a rede problemas após a ativação pós-falha. No entanto, quando está a replicar para o Azure, terá de atualizar as rotas com a nova localização dos endereços IP após a ativação pós-falha. 

## <a name="retaining-ip-addresses"></a>Endereços IP de retenção

Recuperação de sites fornece a capacidade de manter IP fixo endereços quando efetuar a ativação pós-falha para o Azure, com uma sub-rede de ativação pós-falha.

- Com a ativação pós-falha de sub-rede, uma sub-rede específica está presente no Site 1 ou 2 do Site, mas nunca em ambos os sites em simultâneo.
- Para manter o espaço de endereços IP na eventualidade de ocorrer uma ativação pós-falha, é através de programação dispor para a infraestrutura de router mover as sub-redes de um site para outro.
- Durante a ativação pós-falha, as sub-redes que se deslocam-se com as VMs protegidas associadas. A principal desvantagem é que o se ocorrer uma falha, terá de mover a sub-rede de toda.


### <a name="failover-example"></a>Exemplo de ativação pós-falha

Vamos ver um exemplo de ativação pós-falha para o Azure usng uma empresa de ficticious Banco Woodgrove.

- Banco Woodgrove aloja que aplicações empresariais de um site no local. Se alojam as aplicações móveis no Azure.
- Não há conectividade a VPN site a site entre a rede de limite no local e a rede virtual do Azure. Devido a ligação VPN, a rede virtual no Azure aparece como uma extensão da rede no local.
- Woodgrove pretende replicar as cargas de trabalho no local para o Azure com a recuperação de Site.
 - Woodgrove tem as aplicações que dependem hard-coded endereços IP, pelo que precisam para manter os endereços IP para as aplicações, após a ativação pós-falha para o Azure.
 - Recursos em execução no Azure, utilize o IP endereço intervalo 172.16.1.0/24, 172.16.2.0/24.

![Antes da ativação pós-falha de sub-rede](./media/site-recovery-network-design/network-design7.png)

**Infraestrutura antes da ativação pós-falha**


Para Woodgrove ser capaz de replicar as respetivas VMs no Azure, mantendo os endereços IP, aqui que a empresa precisa de fazer:


1. Crie rede virtual do Azure na qual serão criadas as VMs do Azure após a ativação pós-falha do local máquinas. Deve ser uma extensão de rede no local, para que as aplicações podem efetuar a ativação pós-falha totalmente integrada.
2. Antes da ativação pós-falha, no Site Recovery, se atribuir o mesmo endereço IP nas propriedades da máquina. Após a ativação pós-falha, a recuperação de Site atribui este endereço à VM do Azure.
3. Após a ativação pós-falha é executado e as VMs do Azure são criadas com o mesmo endereço IP, que se ligarem à rede através um [ligação Vnet a Vnet](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Esta ação pode ser colocado em script.
4. Se necessitam de modificar rotas para refletir que 192.168.1.0/24 foi agora movido para o Azure.


**Infraestrutura após a ativação pós-falha**

![Após a ativação pós-falha de sub-rede](./media/site-recovery-network-design/network-design9.png)

#### <a name="site-to-site-connection"></a>Ligação site a site

Para além da ligação vnet a vnet, após a ativação pós-falha, pode configurar Woodgrove conectividade VPN de site para site:
- Quando configurar uma ligação site a site, na rede do Azure que só pode encaminhar o tráfego para a localização no local (local ntwork) se o intervalo de endereços IP é diferente do intervalo de endereços IP no local. Isto acontece porque o Azure não suporta sub-redes Stretch. Por isso, se tiver de sub-rede 192.168.1.0/24 no local, não é possível adicionar um 192.168.1.0/24 local da rede na rede do Azure. Isto é esperado porque o Azure não sabe que não existem sem VMs do Active Directory na sub-rede e que a sub-rede está a ser criada para a recuperação de desastre apenas.
- Para conseguir corretamente encaminhar o tráfego de rede fora de uma rede do Azure, as sub-redes na rede e da rede local não pode entrar em conflito.




## <a name="assigning-new-ip-addresses"></a>Atribuir novos endereços IP

Isto [blogue](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) explica como configurar a infraestrutura de rede do Azure quando não precisa de manter os endereços IP após a ativação pós-falha. Começa com uma descrição da aplicação, analisa como definir a segurança de rede no local e no Azure e conclui com informações sobre como executar as ativações pós-falha. 

## <a name="next-steps"></a>Passos seguintes
[Executar uma ativação pós-falha](site-recovery-failover.md)




