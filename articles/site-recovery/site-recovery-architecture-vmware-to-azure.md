---
title: "Como funciona a replicação de VMware para o Azure no Azure Site Recovery? | Microsoft Docs"
description: "Este artigo apresenta uma descrição geral dos componentes e da arquitetura utilizada ao replicar VMs VMware e servidores físicos no local para o Azure com o serviço Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/29/2017
ms.author: raynew
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: vmware-walkthrough-architecture
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 81f02c1277ae8a2c377ca0d6db67ec4211e9aa5e
ms.contentlocale: pt-pt
ms.lasthandoff: 06/28/2017

---



<a id="how-does-vmware-replication-to-azure-work-in-site-recovery" class="xliff"></a>

# Como funciona a replicação de VMware para o Azure no Site Recovery?

Este artigo descreve os componentes e os processos envolvidos ao replicar máquinas virtuais VMware e servidores físicos Windows/Linux no Azure com o serviço [Azure Site Recovery](site-recovery-overview.md).

Quando replicar servidores físicos no local para o Azure, a replicação utiliza também os mesmos componentes e processos que a replicação de VM VMware, com estas diferenças:

- Pode utilizar um servidor físico para o servidor de configuração, em vez de uma VM VMware.
- Vai precisar de uma infraestrutura de VMware no local para a reativação pós-falha. Não pode fazer a reativação pós-falha para um computador.

Publique comentários na parte inferior deste artigo ou coloque questões no [Azure Recovery Services Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) (Fórum dos Serviços de Recuperação do Azure).


<a id="architectural-components" class="xliff"></a>

## Componentes da arquitetura

Existem vários componentes envolvidos ao replicar VMs VMware e servidores físicos para o Azure.

**Componente** | **Localização** | **Detalhes**
--- | --- | ---
**Azure** | No Azure, precisa de uma conta do Azure, de uma conta de armazenamento do Azure e de uma rede do Azure. | Os dados replicados são armazenados na conta de armazenamento e as VMs do Azure são criadas com os dados replicados quando ocorre a ativação pós-falha a partir do site no local. As VMs do Azure ligam-se à rede virtual do Azure quando são criadas.
**Servidor de configuração** | Um único servidor de gestão no local (VM VMWare) que executa todos os componentes no local que são precisos para a implementação, incluindo o servidor de configuração, o servidor de processo, o servidor de destino principal | O componente do servidor de configuração coordena as comunicações entre o local e o Azure, e gere a replicação de dados.
 **Servidor de processos**:  | Instalado por predefinição no servidor de configuração. | Atua como um gateway de replicação. Recebe dados de replicação, otimiza-os com a colocação em cache, compressão e encriptação, e envia-os para o armazenamento do Azure.<br/><br/> O servidor de processos processa a instalação por push do serviço de Mobilidade para máquinas protegidas e efetua a deteção automática de VMs VMware.<br/><br/> À medida que cresça a implementação é possível adicionar mais servidores de processos dedicados independentes para processar um crescente volume de tráfego de replicação.
 **Servidor de destino mestre** | Instalado por predefinição no servidor de configuração no local. | Processa dados de replicação durante a reativação pós-falha a partir do Azure.<br/><br/> Se os volumes de tráfego da reativação pós-falha forem elevados, pode implementar um servidor de destino mestre separado para a reativação pós-falha.
**Servidores de VMware** | As VMs VMware são alojadas em servidores do ESXi do vSphere e recomenda-se um servidor vCenter para gerir os anfitriões. | Pode adicionar servidores VMware ao cofre dos Serviços de Recuperação.
**Máquinas replicadas** | O serviço de Mobilidade será instalado em cada VM VMware que pretende replicar. Pode ser instalado manualmente em cada máquina ou através de uma instalação push a partir do servidor de processos.

Saiba mais sobre os pré-requisitos de implementação e os requisitos para cada um destes componentes na [matriz de suporte](site-recovery-support-matrix-to-azure.md).

**Figura 1: Componentes de VMware para o Azure**

![Componentes](./media/site-recovery-components/arch-enhanced.png)

<a id="replication-process" class="xliff"></a>

## Processo de replicação

1. É configurada a implementação, incluindo componentes do Azure, e um cofre dos Serviços de Recuperação. No cofre, especifica a origem e o destino da replicação, configura o servidor de configuração, adiciona servidores de VMware, cria uma política de replicação, implementa o Serviço de mobilidade, ativa a replicação e executa uma ativação pós-falha de teste.
2.  A replicação das máquinas é iniciada de acordo com a política de replicação e é replicada para o armazenamento do Azure uma cópia inicial dos dados.
4. A replicação das alterações delta para o Azure começa depois de concluída a replicação inicial. As alterações registadas relativas a uma máquina são guardadas num ficheiro .hrl.
    - As máquinas a ser replicadas comunicam com o servidor de configuração na porta HTTPS 443 de entrada, para gestão da replicação.
    - As máquinas a ser replicadas enviam os dados da replicação para o servidor de processos na porta HTTPS 9443 de entrada (pode ser configurada).
    - O servidor de configuração orquestra a gestão da replicação com o Azure através da porta HTTPS 443 de saída.
    - O servidor de processos recebe os dados das máquinas de origem, otimiza-os, encripta-os e envia-os para o armazenamento do Azure através da porta 443 de saída.
    - Se ativar a consistência multi-VM, as máquinas no grupo de replicação comunicam entre si pela porta 20004. A consistência multi-VM é utilizada se agrupar várias máquinas em grupos de replicação que partilhem pontos de consistência de falhas e pontos de consistência de aplicação quando é efetuada a ativação pós-falha dos mesmos. Isto é útil se as máquinas estiverem a executar a mesma carga de trabalho e tiverem de ser consistentes.
5. O tráfego é replicado para pontos finais públicos do armazenamento do Azure, através da Internet. Em alternativa, pode utilizar o [peering público](../expressroute/expressroute-circuit-peerings.md#public-peering) do Azure ExpressRoute. A replicação de tráfego através de uma rede de VPNs a partir de um site no local para o Azure não é suportada.

**Figura 2: Replicação de VMware para o Azure**

![Melhorada](./media/site-recovery-components/v2a-architecture-henry.png)

<a id="failover-and-failback-process" class="xliff"></a>

## Processo de ativação pós-falha e de reativação pós-falha

1. Depois de verificar que a ativação pós-falha de teste está a funcionar conforme esperado, pode executar as ativações pós-falha não planeadas do Azure conforme necessário. A ativação pós-falha planeada não é suportada.
2. Pode fazer a ativação pós-falha de uma máquina individual ou criar [planos de recuperação](site-recovery-create-recovery-plans.md) para a ativação pós-falha de várias VMs.
3. Quando executa uma ativação pós-falha, são criadas VMs de réplica no Azure. Consolida uma ativação pós-falha para começar a aceder à carga de trabalho da VM do Azure de réplica.
4. Quando o site no local primário estiver novamente disponível, pode fazer a reativação pós-falha. Configura uma infraestrutura de reativação pós-falha, inicia a replicação da máquina do site secundário para o primário e executa uma ativação pós-falha não planeada a partir do site secundário. Depois de consolidar esta ativação pós-falha, os dados voltam ao site no local e terá de ativar novamente a replicação para o Azure. [Saiba mais](site-recovery-failback-azure-to-vmware.md)

Existem alguns requisitos para a reativação pós-falha:


- **Servidor de processo temporário no Azure**: se pretender uma reativação a partir do Azure após a ativação pós-falha, terá de configurar uma VM do Azure como servidor de processos, para lidar com a replicação a partir do Azure. É possível eliminar esta VM após a conclusão da reativação pós-falha.
- **Ligação VPN**: para a reativação pós-falha, precisará de uma ligação VPN (ou Azure ExpressRoute) configurada a partir da rede Azure para o site no local.
- **Servidor de destino principal no local independente**: o servidor de destino principal no local processa a reativação pós-falha. O servidor de destino principal está instalado por predefinição no servidor de gestão, mas se está a reativar maiores volumes de tráfego deve configurar um servidor de destino principal independente no local para esta finalidade.
- **Política de reativação pós-falha**: para replicar de novo para o site no local, precisa de uma política de reativação pós-falha. Esta foi criada automaticamente quando criou a política de replicação.
- **Infraestrutura do VMware**: tem de fazer uma reativação pós-falha numa VM VMware no local. O que significa que precisa de uma infraestrutura de VMware no local, mesmo se estiver a replicar servidores físicos no local para o Azure.

**Figura 3: Reativação pós-falha de VMware/servidor físico**

![Reativação pós-falha](./media/site-recovery-components/enhanced-failback.png)


<a id="next-steps" class="xliff"></a>

## Passos seguintes

Analise a [matriz de suporte](site-recovery-support-matrix-to-azure.md)

