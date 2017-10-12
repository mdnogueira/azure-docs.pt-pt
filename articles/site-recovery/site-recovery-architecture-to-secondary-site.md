---
title: "De que forma funciona a replicação do computador no local para um site no local secundário no Azure Site Recovery? | Microsoft Docs"
description: "Este artigo apresenta uma descrição geral dos componentes e da arquitetura utilizada ao replicar VMs e servidores físicos no local para um site secundário com o serviço Azure Site Recovery."
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
ms.openlocfilehash: fca95c63964b955db7ddfbe53250702cc8af122e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-does-on-premises-machine-replication-to-a-secondary-site-work-in-site-recovery"></a>De que forma funciona a replicação do computador no local para um site secundário no Site Recovery?

Este artigo descreve os componentes e os processos envolvidos ao replicar máquinas virtuais e servidores físicos no Azure com o serviço [Azure Site Recovery](site-recovery-overview.md).

Pode replicar o que se segue para um site no local secundário:
- VMs Hyper-V no local em clusters Hyper-V e anfitriões autónomos que são geridos nas clouds do System Center Virtual Machine Manager (VMM).
- VMs de VMware no local e servidores físicos do Windows/Linux. Neste cenário, a replicação é gerida pelo Scout.

Publique comentários na parte inferior deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="replicate-hyper-v-vms-to-a-secondary-on-premises-site"></a>Replicar VMs Hyper-V para um site no local secundário


### <a name="architectural-components"></a>Componentes da arquitetura

Segue-se aquilo de que precisa para replicar VMs de Hyper-V para um site secundário.

**Componente** | **Localização** | **Detalhes**
--- | --- | ---
**Azure** | Precisa de uma conta da Microsoft. |
**Servidor VMM** | Recomenda-se um servidor VMM no site primário e um no site secundário | Cada servidor VMM deve estar ligado à Internet.<br/><br/> Cada servidor deve ter, pelo menos, uma cloud privada do VMM, com o perfil de capacidade de Hyper-V definido.<br/><br/> O Fornecedor do Azure Site Recovery é instalado no servidor VMM. O Fornecedor coordena e orquestra a replicação com o serviço de Recuperação de Sites através da Internet. As comunicações entre o Fornecedor e o Azure são seguras e encriptadas.
**Servidor Hyper-V** |  Um ou mais servidores de anfitriões Hyper-V nas clouds do VMM primárias e secundárias.<br/><br/> Os servidores devem estar ligados à Internet.<br/><br/> Os dados são replicados entre os servidores anfitrião primário e secundário Hyper-V através de LAN ou VPN mediante a utilização de Kerberos ou da autenticação de certificados.  
**VMs de Hyper-V** | Localizadas no servidor de anfitrião Hyper-V de origem. | O servidor de anfitrião de origem deve ter, pelo menos, uma VM que queira replicar.

### <a name="replication-process"></a>Processo de replicação

1. É configurada a conta do Azure.
2. Cria um cofre dos Serviços de Replicação para o Site Recovery e configura as definições do cofre, incluindo:

    - A origem e o destino da replicação (site primário e secundário).
    - Instalação do Fornecedor do Azure Site Recovery e do agente dos Serviços de Recuperação do Microsoft Azure. O Fornecedor é instalado nos servidores VMM e o agente em cada anfitrião Hyper-V.
    - Cria uma política de replicação para a cloud do VMM de origem. A política é aplicada a todas as VMs localizadas nos anfitriões na cloud.
    - Ativa a replicação nas VMs de Hyper-V. A replicação inicial ocorre de acordo com as definições da política de replicação.
4. As alterações aos dados são registadas e a replicação das alterações delta para começar depois de concluída a replicação inicial. As alterações registadas relativas a um item são guardadas num ficheiro .hrl.
5. Executa uma ativação pós-falha de teste, para confirmar que está tudo a funcionar.

**Figura 1: Replicação do VMM para o VMM**

![Local para local](./media/site-recovery-components/arch-onprem-onprem.png)

### <a name="failover-and-failback-process"></a>Processo de ativação pós-falha e de reativação pós-falha

1. Executa uma [ativação pós-falha](site-recovery-failover.md) planeada ou não planeada entre os sites no local. Se executar uma ativação pós-falha planeada, as VMs de origem são desligadas para garantir que não há perda de dados.
2. Pode fazer a ativação pós-falha de uma máquina individual ou criar [planos de recuperação](site-recovery-create-recovery-plans.md) para orquestrar a ativação pós-falha de várias máquinas.
4. Se fizer uma ativação pós-falha não planeada para um site secundário, as máquinas na localização secundária não estão ativadas para proteção ou replicação após a ativação pós-falha. Se tiver executado uma ativação pós-falha planeada, as máquinas na localização secundária estão protegidas após a ativação pós-falha.
5. Em seguida, consolida a ativação pós-falha para começar a aceder à carga de trabalho da VM de réplica.
6. Quando o site primário estiver novamente disponível, inicia a replicação inversa, para replicar do site secundário para o primário. A replicação inversa coloca as máquinas virtuais num estado protegido, mas a localização ativa continua a ser o datacenter secundário.
7. Para que o site primário volte a ser a localização ativa, inicie uma ativação pós-falha planeada do site secundário para o primário, seguida de outra replicação inversa.




## <a name="replicate-vmware-vmsphysical-servers-to-a-secondary-site"></a>Replicar VMs de VMware/servidores físicos para um site secundário

Pode replicar VMs de VMware ou servidores físicos para um site secundário com o InMage Scout, com estes componentes da arquitetura:


### <a name="architectural-components"></a>Componentes da arquitetura

**Componente** | **Localização** | **Detalhes**
--- | --- | ---
**Azure** | InMage Scout. | Para obter o InMage Scout, precisa de uma subscrição do Azure.<br/><br/> Depois de criar um cofre dos Serviços de Recuperação, transfere o InMage Scout e instala as atualizações mais recentes para configurar a implementação.
**Servidor de processos** | Localizado no site primário | O servidor de processos é implementado para lidar com a colocação em cache, a compressão e a otimização de dados.<br/><br/> Ele também faz a instalação por push do Agente Unified nas máquinas que pretende proteger.
**Servidor de configuração** | Localizado no site secundário | O servidor de configuração gere, configura e monitoriza a implementação, utilizando o site de gestão ou a consola do vContinuum.
**Servidor de vContinuum** | Opcional. Instalado na mesma localização que o servidor de configuração. | Fornece uma consola de gestão e monitorização do seu ambiente protegido.
**Servidor de destino mestre** | Localizado no site secundário | O servidor de destino mestre contém dados replicados. Recebe dados do servidor de processos, cria uma máquina de réplica no site secundário e detém os pontos de retenção de dados.<br/><br/> O número de servidores de destino principal de que precisa, depende do número de máquinas que estiver a proteger.<br/><br/> Se pretender que haja reativação para o site primário, precisa também que aí exista um servidor de destino principal. O Agente Unified é instalado neste servidor.
**VMware ESX/ESXi e servidor vCenter** |  As VMs são alojadas em anfitriões ESX/ESXi. Os anfitriões são geridos com um servidor vCenter | Precisa de uma infraestrutura do VMware para replicar VMs de VMware.
**VMs/servidores físicos** |  Agente Unificado instalado nas VMs VMware e servidores físicos que pretende replicar. | O agente atua como um fornecedor de comunicação entre todos os componentes.


### <a name="replication-process"></a>Processo de replicação

1. Configure servidores de componentes em cada site (configuração, processo, destino principal) e instale o agente Unified nas máquinas que pretende replicar.
2. Após a replicação inicial, o agente em cada máquina envia alterações de replicação delta para o servidor de processos.
3. O servidor de processos otimiza os dados e transfere-o para o servidor de destino principal no site secundário. O servidor de configuração gere o processo de replicação.

**Figura 2: Replicação de VMware para VMware**

![VMware para VMware](./media/site-recovery-components/vmware-to-vmware.png)


## <a name="next-steps"></a>Passos seguintes

Analise a [matriz de suporte](site-recovery-support-matrix-to-sec-site.md)
