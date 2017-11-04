---
title: "Reveja a arquitetura da replicação do servidor físico para o Azure | Microsoft Docs"
description: "Este artigo fornece uma descrição geral da arquitetura de utilizada quando replicar servidores físicos no local para o Azure com o serviço do Azure Site Recovery e componentes"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: aac3450e-dfac-4e20-b377-1a6cd39d04ca
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: raynew
ms.openlocfilehash: 02dafa60f19df88123358446ac72d9be85577554
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="physical-server-to-azure-replication-architecture"></a>Servidor físico à arquitetura de replicação do Azure

Este artigo descreve a arquitetura e processos utilizados quando replicar, efetuar a ativação pós-falha e recuperar servidores físicos do Windows e Linux entre um site no local e o Azure, utilizando o [do Azure Site Recovery](site-recovery-overview.md) serviço.


## <a name="architectural-components"></a>Componentes da arquitetura

A seguinte tabela e o gráfico fornecem uma vista detalhada dos componentes utilizados para replicação do servidor físico para o Azure.  

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**Azure** | Uma subscrição do Azure, uma conta de armazenamento do Azure e uma rede do Azure. | Os dados replicados de VMs no local são armazenados na conta de armazenamento. As VMs do Azure são criadas com os dados replicados quando executar a uma falha ao longo no local para o Azure. As VMs do Azure ligam-se à rede virtual do Azure quando são criadas.
**Servidor de configuração** | Um único local máquina física ou VM do VMware for implementada para executar todas as instalações de componentes da recuperação de Site. A VM é executado o servidor de configuração, o servidor de processos e o servidor de destino principal. | O servidor de configuração coordena as comunicações entre o local e o Azure, e gere a replicação de dados.
 **Servidor de processos**:  | Instalado por predefinição, juntamente com o servidor de configuração. | Atua como um gateway de replicação. Recebe dados de replicação, otimiza-os com a colocação em cache, compressão e encriptação, e envia-os para o armazenamento do Azure.<br/><br/> O servidor de processos também instala o serviço de mobilidade em servidores que pretende replicar.<br/><br/> À medida que cresça a implementação, pode adicionar servidores de processos adicionais, em separado para processar maiores volumes de tráfego de replicação.
 **Servidor de destino mestre** | Instalado por predefinição, juntamente com o servidor de configuração. | Processa dados de replicação durante a reativação pós-falha a partir do Azure.<br/><br/> Para grandes implementações, pode adicionar um servidor de destino mestre adicionais, em separado para reativação pós-falha.
**Replicadas de servidores** | O serviço de mobilidade está instalado em cada servidor que replica. | Recomendamos a que permitir a instalação automática do servidor de processos. Em alternativa pode instalar manualmente o serviço ou utilize um método de implementação automática, tais como o System Center Configuration Manager. 

**Físico à arquitetura do Azure**

![Componentes](./media/concepts-physical-to-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Processo de replicação

1. Configurar a implementação, incluindo no local e os componentes do Azure. No cofre dos serviços de recuperação, especifique a origem de replicação e de destino, configurar o servidor de configuração, crie uma política de replicação e ativar a replicação.
2. Replicar máquinas em conformidade com a política de replicação e uma cópia inicial dos dados de servidor é replicado para o armazenamento do Azure.
3. Após a conclusão da replicação inicial, começa a replicação das alterações de delta para o Azure. As alterações registadas relativas a uma máquina são guardadas num ficheiro .hrl.
    - Máquinas comunicam com o servidor de configuração na porta HTTPS 443 entrada para a gestão de replicação.
    - As máquinas enviam dados de replicação para o servidor de processos na porta HTTPS 9443 entrada (podem ser modificadas).
    - O servidor de configuração orquestra a gestão da replicação com o Azure através da porta HTTPS 443 de saída.
    - O servidor de processos recebe os dados das máquinas de origem, otimiza-os, encripta-os e envia-os para o armazenamento do Azure através da porta 443 de saída.
    - Se ativar a consistência multi VM, as máquinas do grupo de replicação comunicam entre si através da porta 20004. A consistência multi-VM é utilizada se agrupar várias máquinas em grupos de replicação que partilhem pontos de consistência de falhas e pontos de consistência de aplicação quando é efetuada a ativação pós-falha dos mesmos. Isto é útil se as máquinas estiverem a executar a mesma carga de trabalho e tiverem de ser consistentes.
4. O tráfego é replicado para pontos finais públicos do armazenamento do Azure, através da Internet. Em alternativa, pode utilizar o [peering público](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) do Azure ExpressRoute. A replicação de tráfego através de uma rede de VPNs a partir de um site no local para o Azure não é suportada.


**Físico para o processo de replicação do Azure**

![Processo de replicação](./media/concepts-physical-to-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processo de ativação pós-falha e de reativação pós-falha

Depois de configurar a replicação e tiver de executar um exercício de recuperação após desastre (ativação pós-falha de teste) para verificar que tudo está a funcionar conforme esperado, pode executar a ativação pós-falha e reativação pós-falha conforme necessário para. Tenha em atenção que:

- A ativação pós-falha planeada não é suportada.
- Tem de falhar novamente para uma VM de VMware no local. O que significa que precisa de uma infraestrutura de VMware no local, mesmo se estiver a replicar servidores físicos no local para o Azure.


1. Efetuar a ativação pós-falha de um único computador ou, criar planos de recuperação, para efetuar a ativação pós-falha de várias máquinas em conjunto.
2. Quando executar uma ativação pós-falha, as VMs do Azure são criadas a partir dos dados replicados no armazenamento do Azure.
3. Após a acionar a ativação pós-falha inicial, a consolidação-o para iniciar a aceder a carga de trabalho a partir da VM do Azure.

Quando o site no local primário estiver novamente disponível, pode fazer a reativação pós-falha.

1. Terá de configurar uma infraestrutura de reativação pós-falha, incluindo:
    - **Servidor de processo temporário no Azure**: falhar novamente a partir do Azure, configurar uma VM do Azure para atuar como um servidor de processos, para lidar com a replicação a partir do Azure. É possível eliminar esta VM após a conclusão da reativação pós-falha.
    - **Ligação VPN**: para falhar novamente, precisa de uma ligação VPN (ou Azure ExpressRoute) da rede do Azure para o site no local.
    - **Servidor de destino principal separado**: por predefinição, o servidor de destino principal foi instalado com o servidor de configuração, em que a VM, de VMware no local processa a reativação pós-falha. No entanto, se precisar de falhar novamente grandes volumes de tráfego, deve configurar um servidor de destino principal independente no local para esta finalidade.
    - **Política de reativação pós-falha**: para replicar de novo para o site no local, precisa de uma política de reativação pós-falha. Isto foi criado automaticamente quando criou a política de replicação no local para o Azure.
    - **Infraestrutura do VMware**: precisa de uma infraestrutura do VMware para reativação pós-falha. Não pode efetuar a reativação pós-falha num servidor físico.
2. Depois dos componentes estão em vigor, a reativação pós-falha ocorre em três fases:
    - Fase 1: Voltar a proteger as VMs do Azure para que o se replicar a partir do Azure para as VMs de VMware no local.
    - Fase 2: Executar uma ativação pós-falha para o site no local.
    - Fase 3: Depois de cargas de trabalho falharam novamente, reativar a replicação.

**Reativação pós-falha da VMware a partir do Azure**

![Reativação pós-falha](./media/concepts-physical-to-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Passos seguintes

Rever a matriz de suporte siga o tutorial para ativar o VMware para a replicação do Azure.
Execute uma ativação pós-falha e a reativação pós-falha.