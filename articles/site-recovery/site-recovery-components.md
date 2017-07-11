---
title: "Como funciona a Recuperação de Sites? | Microsoft Docs"
description: "Este artigo fornece uma descrição geral da arquitetura da Recuperação de Sites"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/14/2017
ms.author: raynew
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: site-recovery-azure-to-azure-architecture
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: e8faa2c7cde18fc08f0255e80f0acdb961082fcb
ms.contentlocale: pt-pt
ms.lasthandoff: 06/16/2017

---


<a id="how-does-azure-site-recovery-work-for-on-premises-infrastructure" class="xliff"></a>

# Como funciona o Azure Site Recovery para infraestruturas no local?

> [!div class="op_single_selector"]
> * [Replicar máquinas virtuais do Azure](site-recovery-azure-to-azure-architecture.md)
> * [Replicar máquinas no local](site-recovery-components.md)

Este artigo descreve a arquitetura subjacente do serviço [Azure Site Recovery](site-recovery-overview.md) e os componentes que fazem com que funcione para replicar cargas de trabalho do local para o Azure.

Publique comentários na parte inferior deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


<a id="replicate-to-azure" class="xliff"></a>

## Replicar para o Azure

Pode replicar e proteger a seguinte infraestrutura no local para o Azure:

- **VMware**: VMs VMware no local em execução num [anfitrião suportado](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers). É possível replicar VMs VMware que executam [sistemas operativos suportados](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)
- **Hyper-V**: VMs Hyper-V no local em execução em [anfitriões suportados](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
- **Máquinas físicas**: servidores físicos no local que executem o Windows ou Linux em [sistemas operativos suportados](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions). Pode replicar VMs Hyper-V que executem qualquer sistema operativo convidado [suportado pelo Hyper-V e o Azure](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).

<a id="vmware-to-azure" class="xliff"></a>

## VMware para o Azure

Segue-se aquilo de que precisa para replicar VMs de VMware para o Azure.

Área | Componente | Detalhes
--- | --- | ---
**Servidor de configuração** | Um servidor de gestão único (VM VMware) executa todos os componentes no local - servidor de configuração, servidor de processos, servidor de destino mestre | O servidor de configuração coordena as comunicações entre o local e o Azure, e gere a replicação de dados.
 **Servidor de processos**:  | Instalado por predefinição no servidor de configuração. | Atua como um gateway de replicação. Recebe dados de replicação, otimiza-os com a colocação em cache, compressão e encriptação, e envia-os para o armazenamento do Azure.<br/><br/> O servidor de processos processa a instalação por push do serviço de Mobilidade para máquinas protegidas e efetua a deteção automática de VMs VMware.<br/><br/> À medida que cresça a implementação é possível adicionar mais servidores de processos dedicados independentes para processar um crescente volume de tráfego de replicação.
 **Servidor de destino mestre** | Instalado por predefinição no servidor de configuração no local. | Processa dados de replicação durante a reativação pós-falha a partir do Azure.<br/><br/> Se os volumes de tráfego da reativação pós-falha forem elevados, pode implementar um servidor de destino mestre separado para a reativação pós-falha.
**Servidores de VMware** | As VMs VMware são alojadas em servidores do ESXi do vSphere e recomenda-se um servidor vCenter para gerir os anfitriões. | Pode adicionar servidores VMware ao cofre dos Serviços de Recuperação.<br/><br/>
**Máquinas replicadas** | O serviço de Mobilidade será instalado em cada VM VMware que pretende replicar. Pode ser instalado manualmente em cada máquina ou através de uma instalação push a partir do servidor de processos.| -

**Figura 1: Componentes de VMware para o Azure**

![Componentes](./media/site-recovery-components/arch-enhanced.png)

<a id="replication-process" class="xliff"></a>

### Processo de replicação

1. É configurada a implementação, incluindo componentes do Azure, e um cofre dos Serviços de Recuperação. No cofre, especifica a origem e o destino da replicação, configura o servidor de configuração, adiciona servidores de VMware, cria uma política de replicação, implementa o Serviço de mobilidade, ativa a replicação e executa uma ativação pós-falha de teste.
2.  A replicação das máquinas é iniciada de acordo com a política de replicação e é replicada para o armazenamento do Azure uma cópia inicial dos dados.
4. A replicação das alterações delta para o Azure começa depois de concluída a replicação inicial. As alterações registadas relativas a uma máquina são guardadas num ficheiro .hrl.
    - As máquinas a ser replicadas comunicam com o servidor de configuração na porta HTTPS 443 de entrada, para gestão da replicação.
    - As máquinas a ser replicadas enviam os dados da replicação para o servidor de processos na porta HTTPS 9443 de entrada (pode ser configurada).
    - O servidor de configuração orquestra a gestão da replicação com o Azure através da porta HTTPS 443 de saída.
    - O servidor de processos recebe os dados das máquinas de origem, otimiza-os, encripta-os e envia-os para o armazenamento do Azure através da porta 443 de saída.
    - Se ativar a consistência multi-VM, as máquinas no grupo de replicação comunicam entre si pela porta 20004. A consistência multi-VM é utilizada se agrupar várias máquinas em grupos de replicação que partilhem pontos de consistência de falhas e pontos de consistência de aplicação quando é efetuada a ativação pós-falha dos mesmos. Isto é útil se as máquinas estiverem a executar a mesma carga de trabalho e tiverem de ser consistentes.
5. O tráfego é replicado para pontos finais públicos do armazenamento do Azure, através da Internet. Em alternativa, pode utilizar o [peering público](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-circuit-peerings#public-peering) do Azure ExpressRoute. A replicação de tráfego através de uma rede de VPNs a partir de um site no local para o Azure não é suportada.

**Figura 2: Replicação de VMware para o Azure**

![Melhorada](./media/site-recovery-components/v2a-architecture-henry.png)

<a id="failover-and-failback" class="xliff"></a>

### Ativação pós-falha e reativação pós-falha

1. Depois de verificar que a ativação pós-falha de teste está a funcionar conforme esperado, pode executar as ativações pós-falha não planeadas do Azure conforme necessário. A ativação pós-falha planeada não é suportada.
2. Pode fazer a ativação pós-falha de uma máquina individual ou criar [planos de recuperação](site-recovery-create-recovery-plans.md) para a ativação pós-falha de várias VMs.
3. Quando executa uma ativação pós-falha, são criadas VMs de réplica no Azure. Consolida uma ativação pós-falha para começar a aceder à carga de trabalho da VM do Azure de réplica.
4. Quando o site no local primário estiver novamente disponível, pode fazer a reativação pós-falha. Configura uma infraestrutura de reativação pós-falha, inicia a replicação da máquina do site secundário para o primário e executa uma ativação pós-falha não planeada a partir do site secundário. Depois de consolidar esta ativação pós-falha, os dados voltam ao site no local e terá de ativar novamente a replicação para o Azure. [Saiba mais](site-recovery-failback-azure-to-vmware.md)

**Figura 3: Reativação pós-falha de VMware/servidor físico**

![Reativação pós-falha](./media/site-recovery-components/enhanced-failback.png)

<a id="physical-to-azure" class="xliff"></a>

## Físico para o Azure

Quando replicar servidores físicos no local para o Azure, a replicação utiliza também os mesmos componentes e processos como [VMware para o Azure](#vmware-replication-to-azure), mas tenha em atenção estas diferenças:

- Pode utilizar um servidor físico para o servidor de configuração, em vez de uma VM VMware
- Vai precisar de uma infraestrutura de VMware no local para a reativação pós-falha. Não pode fazer a reativação pós-falha para um computador.

<a id="hyper-v-to-azure" class="xliff"></a>

## Hyper-V para o Azure

<a id="replication-process" class="xliff"></a>

### Processo de replicação

1. São configurados os componentes do Azure. Recomendamos que configure as contas de armazenamento e de rede antes de iniciar a implementação do Site Recovery.
2. Cria um cofre dos Serviços de Replicação para o Site Recovery e configura as definições do cofre, incluindo:
    - Definições de origem e de destino. Se não estiver a gerir anfitriões Hyper-V numa cloud do VMM, para o destino, crie um contentor de sites Hyper-V e adicione anfitriões Hyper-V ao mesmo. Se os anfitriões Hyper-V forem geridos no VMM, a origem é a cloud do VMM. O destino é o Azure.
    - Instalação do Fornecedor do Azure Site Recovery e do agente dos Serviços de Recuperação do Microsoft Azure. Se tiver o VMM, o Fornecedor será instalado no mesmo e o agente em cada anfitrião Hyper-V. Se não tiver o VMM, o Fornecedor e o agente são instalados em cada anfitrião.
    - Cria uma política de replicação para o site Hyper-V ou para a cloud do VMM. A política é aplicada a todas as VMs localizadas nos anfitriões no site ou na cloud.
    - Ativa a replicação nas VMs de Hyper-V. A replicação inicial ocorre de acordo com as definições da política de replicação.
4. As alterações aos dados são registadas e a replicação das alterações delta para o Azure começa depois de concluída a replicação inicial. As alterações registadas relativas a um item são guardadas num ficheiro .hrl.
5. Executa uma ativação pós-falha de teste, para confirmar que está tudo a funcionar.

<a id="failover-and-failback-process" class="xliff"></a>

### Processo de ativação pós-falha e de reativação pós-falha

1. Executa uma [ativação pós-falha](site-recovery-failover.md) planeada ou não planeada a partir das VMs de Hyper-V no local para o Azure. Se executar uma ativação pós-falha planeada, as VMs de origem são desligadas para garantir que não há perda de dados.
2. Pode fazer a ativação pós-falha de uma máquina individual ou criar [planos de recuperação](site-recovery-create-recovery-plans.md) para orquestrar a ativação pós-falha de várias máquinas.
4. Depois de executar a ativação pós-falha, deverá conseguir ver as VMs de réplica criadas no Azure. Pode atribuir um endereço IP público à VM, se necessário.
5. Em seguida, consolida a ativação pós-falha para começar a aceder à carga de trabalho da VM do Azure de réplica.
6. Quando o site no local primário estiver novamente disponível, pode fazer a [reativação pós-falha](site-recovery-failback-from-azure-to-hyper-v.md). Arranca uma ativação pós-falha planeada do Azure para o site primário. Nas ativações pós-falha planeadas, pode optar por ativar para a mesma VM ou para uma localização alternativa, bem como sincronizar as alterações entre o Azure e o site no local, para garantir que não se perdem dados. Quando as VMs são criadas no local, é consolidada a ativação pós-falha.

**Figura 4: Replicação de site Hyper-V para o Azure**

![Componentes](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

**Figura 5: replicação de Hyper-V em clouds do VMM para o Azure**

![Componentes](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)


<a id="replicate-to-a-secondary-site" class="xliff"></a>

## Replicar para um site secundário

Pode replicar o que se segue para o seu site secundário:

- **VMware**: VMs VMware no local em execução num [anfitrião suportado](site-recovery-support-matrix-to-sec-site.md#on-premises-servers). É possível replicar VMs VMware que executam [sistemas operativos suportados](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions)
- **Máquinas físicas**: servidores físicos no local que executem o Windows ou Linux em [sistemas operativos suportados](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions).
- **Hyper-V**: VMs Hyper-V no local em execução em [anfitriões Hyper-V suportados](site-recovery-support-matrix-to-sec-site.md#on-premises-servers), geridos em clouds do VMM. [anfitriões suportados](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers). Pode replicar VMs Hyper-V que executem qualquer sistema operativo convidado [suportado pelo Hyper-V e o Azure](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).


<a id="vmwarephysical-to-a-secondary-site" class="xliff"></a>

## VMware/físico para um site secundário

Pode replicar VMs de VMware ou servidores físicos para um site secundário com o InMage Scout.

<a id="components" class="xliff"></a>

### Componentes

**Área** | **Componente** | **Detalhes**
--- | --- | ---
**Servidor de processos** | Localizado no site primário | O servidor de processos é implementado para lidar com a colocação em cache, a compressão e a otimização de dados.<br/><br/> Ele também faz a instalação por push do Agente Unified nas máquinas que pretende proteger.
**Servidor de configuração** | Localizado no site secundário | O servidor de configuração gere, configura e monitoriza a implementação, utilizando o site de gestão ou a consola do vContinuum.
**Servidor de vContinuum** | Opcional. Instalado na mesma localização que o servidor de configuração. | Fornece uma consola de gestão e monitorização do seu ambiente protegido.
**Servidor de destino mestre** | Localizado no site secundário | O servidor de destino mestre contém dados replicados. Recebe dados do servidor de processos, cria uma máquina de réplica no site secundário e detém os pontos de retenção de dados.<br/><br/> O número de servidores de destino principal de que precisa, depende do número de máquinas que estiver a proteger.<br/><br/> Se pretender que haja reativação para o site primário, precisa também que aí exista um servidor de destino principal. O Agente Unified é instalado neste servidor.
**VMware ESX/ESXi e servidor vCenter** |  As VMs são alojadas em anfitriões ESX/ESXi. Os anfitriões são geridos com um servidor vCenter | Precisa de uma infraestrutura do VMware para replicar VMs de VMware.
**VMs/servidores físicos** |  Agente Unificado instalado nas VMs VMware e servidores físicos que pretende replicar. | O agente atua como um fornecedor de comunicação entre todos os componentes.


<a id="replication-process" class="xliff"></a>

### Processo de replicação

1. Configure servidores de componentes em cada site (configuração, processo, destino principal) e instale o agente Unified nas máquinas que pretende replicar.
2. Após a replicação inicial, o agente em cada máquina envia alterações de replicação delta para o servidor de processos.
3. O servidor de processos otimiza os dados e transfere-o para o servidor de destino principal no site secundário. O servidor de configuração gere o processo de replicação.

**Figura 6: Replicação de VMware para VMware**

![VMware para VMware](./media/site-recovery-components/vmware-to-vmware.png)



<a id="hyper-v-to-a-secondary-site" class="xliff"></a>

## Hyper-V para um site secundário

Segue-se aquilo de que precisa para replicar VMs de Hyper-V para um site secundário.


**Área** | **Componente** | **Detalhes**
--- | --- | ---
**Servidor VMM** | Recomenda-se um servidor VMM no site primário e um no site secundário | Cada servidor VMM deve estar ligado à Internet.<br/><br/> Cada servidor deve ter, pelo menos, uma cloud privada do VMM, com o perfil de capacidade de Hyper-V definido.<br/><br/> O Fornecedor do Azure Site Recovery é instalado no servidor VMM. O Fornecedor coordena e orquestra a replicação com o serviço de Recuperação de Sites através da Internet. As comunicações entre o Fornecedor e o Azure são seguras e encriptadas.
**Servidor Hyper-V** |  Um ou mais servidores de anfitriões Hyper-V nas clouds do VMM primárias e secundárias.<br/><br/> Os servidores devem estar ligados à Internet.<br/><br/> Os dados são replicados entre os servidores anfitrião primário e secundário Hyper-V através de LAN ou VPN mediante a utilização de Kerberos ou da autenticação de certificados.  
**VMs de Hyper-V** | Localizadas no servidor de anfitrião Hyper-V de origem. | O servidor de anfitrião de origem deve ter, pelo menos, uma VM que queira replicar.

<a id="replication-process" class="xliff"></a>

### Processo de replicação

1. É configurada a conta do Azure.
2. Cria um cofre dos Serviços de Replicação para o Site Recovery e configura as definições do cofre, incluindo:

    - A origem e o destino da replicação (site primário e secundário).
    - Instalação do Fornecedor do Azure Site Recovery e do agente dos Serviços de Recuperação do Microsoft Azure. O Fornecedor é instalado nos servidores VMM e o agente em cada anfitrião Hyper-V.
    - Cria uma política de replicação para a cloud do VMM de origem. A política é aplicada a todas as VMs localizadas nos anfitriões na cloud.
    - Ativa a replicação nas VMs de Hyper-V. A replicação inicial ocorre de acordo com as definições da política de replicação.
4. As alterações aos dados são registadas e a replicação das alterações delta para começar depois de concluída a replicação inicial. As alterações registadas relativas a um item são guardadas num ficheiro .hrl.
5. Executa uma ativação pós-falha de teste, para confirmar que está tudo a funcionar.

**Figura 7: Replicação do VMM para o VMM**

![Local para local](./media/site-recovery-components/arch-onprem-onprem.png)

<a id="failover-and-failback" class="xliff"></a>

### Ativação pós-falha e reativação pós-falha

1. Executa uma [ativação pós-falha](site-recovery-failover.md) planeada ou não planeada entre os sites no local. Se executar uma ativação pós-falha planeada, as VMs de origem são desligadas para garantir que não há perda de dados.
2. Pode fazer a ativação pós-falha de uma máquina individual ou criar [planos de recuperação](site-recovery-create-recovery-plans.md) para orquestrar a ativação pós-falha de várias máquinas.
4. Se fizer uma ativação pós-falha não planeada para um site secundário, as máquinas na localização secundária não estão ativadas para proteção ou replicação após a ativação pós-falha. Se tiver executado uma ativação pós-falha planeada, as máquinas na localização secundária estão protegidas após a ativação pós-falha.
5. Em seguida, consolida a ativação pós-falha para começar a aceder à carga de trabalho da VM de réplica.
6. Quando o site primário estiver novamente disponível, inicia a replicação inversa, para replicar do site secundário para o primário. A replicação inversa coloca as máquinas virtuais num estado protegido, mas a localização ativa continua a ser o datacenter secundário.
7. Para que o site primário volte a ser a localização ativa, inicie uma ativação pós-falha planeada do site secundário para o primário, seguida de outra replicação inversa.


<a id="next-steps" class="xliff"></a>

## Passos seguintes

- [Saiba mais](site-recovery-hyper-v-azure-architecture.md) sobre o fluxo de trabalho da replicação de Hyper-V.
- [Verificar pré-requisitos](site-recovery-prereq.md)

