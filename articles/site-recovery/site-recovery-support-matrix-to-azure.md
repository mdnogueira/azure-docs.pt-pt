---
title: "Matriz de suporte de recuperação de sites do Azure para replicar para o Azure | Microsoft Docs"
description: Resume os componentes e sistemas operativos suportados para o Azure Site Recovery
services: site-recovery
documentationcenter: 
author: Rajani-Janaki-Ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/30/2017
ms.author: rajanaki
ms.openlocfilehash: 0302b4f8f4171d288a7e7c62de036c6f1cec8212
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="azure-site-recovery-support-matrix-for-replicating-from-on-premises-to-azure"></a>Matriz de suporte de recuperação de sites do Azure para replicar no local para Azure


Este artigo resume os componentes e configurações suportadas para o Azure Site Recovery quando replicar e a recuperar para o Azure. Para obter mais informações sobre os requisitos do Azure Site Recovery, consulte o [pré-requisitos](site-recovery-prereq.md).

> [!NOTE]
> Certifique-se de que Atualize para a versão mais recente do fornecedor da recuperação de Site e o agente para alcançar a compatibilidade com as atualizações a matriz de suporte.


## <a name="support-for-deployment-options"></a>Suporte para as opções de implementação

**Implementação** | **Servidor VMware/físico** | **Hyper-V (com/sem o Virtual Machine Manager)** |
--- | --- | ---
**Portal do Azure** | No local as VMs VMware para o armazenamento do Azure, com o Azure Resource Manager ou o armazenamento clássico e redes.<br/><br/> Ativação pós-falha para as VMs baseadas no Resource Manager ou clássicas. | No local VMs de Hyper-V para o armazenamento do Azure, com o Resource Manager ou o armazenamento clássico e redes.<br/><br/> Ativação pós-falha para as VMs baseadas no Resource Manager ou clássicas.
**Portal clássico** | Modo de manutenção apenas. Não não possível criar novos cofres. | Modo de manutenção apenas.
**PowerShell** | Não é atualmente suportado. | Suportado


## <a name="support-for-datacenter-management-servers"></a>Suporte para servidores de gestão de centros de dados

### <a name="virtualization-management-entities"></a>Entidades de gestão de Virtualização

**Implementação** | **Suporte**
--- | ---
**Servidor de VM de VMware/físico** | vCenter 6.5, 6.0 ou 5.5
**Hyper-V (com o Virtual Machine Manager)** | O System Center Virtual Machine Manager 2016 e System Center Virtual Machine Manager 2012 R2

  >[!Note]
  > Atualmente não é suportada uma nuvem do System Center Virtual Machine Manager 2016 com uma mistura de anfitriões de R2 de 2012 e Windows Server 2016.
  > Configurações que incluam a atualização de um existente SCVMM 2012 R2 para 2016 não é atualmente suportada.
### <a name="host-servers"></a>Servidores de anfitrião

**Implementação** | **Suporte**
--- | ---
**Servidor de VM de VMware/físico** | vSphere 6.5, 6.0, 5.5
**Hyper-V (com/sem o Virtual Machine Manager)** | Windows Server 2016, Windows Server 2012 R2 com as atualizações mais recentes.<br></br>Se for utilizado SCVMM, os anfitriões do Windows Server 2016 devem ser geridos pelo SCVMM de 2016.


  >[!Note]
  >Um site de Hyper-V que mistura de anfitriões que executam o Windows Server 2016 e 2012 R2 não é atualmente suportado. Atualmente não é suportada a recuperação para uma localização alternativa para as VMs num anfitrião Windows Server 2016.

## <a name="support-for-replicated-machine-os-versions"></a>Suporte para versões de SO da máquina replicada

Máquinas virtuais que estão protegidas têm de cumprir [requisitos do Azure](#failed-over-azure-vm-requirements) quando replicar para o Azure.
A tabela seguinte resume o suporte do sistema de operativo replicadas em vários cenários de implementação ao utilizar o Azure Site Recovery. Este suporte é aplicável a qualquer carga de trabalho em execução no sistema operativo mencionados.

 **Servidor VMware/físico** | **Hyper-V (com/sem VMM)** |
--- | --- |
64 bits do Windows Server 2016 (Server Core, o servidor com experiência de ambiente de trabalho)\*, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 com, pelo menos, SP1<br/><br/> Red Hat Enterprise Linux: 5.2 para 5.11, 6.1 para 6.9, 7.0 para 7.3 <br/><br/>CentOS: 5.2 para 5.11, 6.1 para 6.9, 7.0 para 7.3 <br/><br/>Servidor Ubuntu 14.04 LTS[ (versões de kernel suportado)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Servidor Ubuntu 16.04 LTS[ (versões de kernel suportado)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Debian 7 <br/><br/>Debian 8<br/><br/>Oracle Enterprise Linux 6.4, 6.5 com o kernel compatível do Red Hat ou Unbreakable Enterprise Kernel versão 3 (UEK3) <br/><br/>SP3 do SUSE Linux Enterprise Server 11 <br/><br/>SP4 do SUSE Linux Enterprise Server 11 <br/>(Não é suportada a atualização de replicar máquinas do SLES 11 SP3 para SLES 11 SP4. Se uma máquina replicada tiver sido atualizada do SLES 11SP3 para SLES 11 SP4, terá de desativar a replicação e proteger a máquina post novamente a atualização.) | Qualquer SO convidado [suportado pelo Azure](https://technet.microsoft.com/library/cc794868.aspx)

>[!NOTE]
>
> \*Windows Server 2016 Nano Server não é suportado.

>[!IMPORTANT]
>(Aplica-se a VMware/servidores físicos a replicar para o Azure)
>
> Red Hat Enterprise Linux Server 7 + e servidores do CentOS 7 +, kernel 3.10.0-514 de versão é suportada a partir da versão 9.8 o Azure Site Recovery do serviço de mobilidade.<br/><br/>
> Os clientes em kernel 3.10.0-514 com uma versão do serviço de mobilidade inferior à versão 9.8 são necessários para desativar a replicação, atualize a versão do serviço de mobilidade para versão 9.8 e, em seguida, ativar a replicação novamente.


### <a name="supported-ubuntu-kernel-versions-for-vmwarephysical-servers"></a>Versões suportadas do kernel Ubuntu para VMware/servidores físicos

**Versão** | **Versão do serviço de mobilidade** | **Versão de kernel** |
--- | --- | --- |
14.04 LTS | 9.9 | 3.13.0-24-Generic para 3.13.0-117-generic,<br/>3.16.0-25-Generic para 3.16.0-77-generic,<br/>3.19.0-18-Generic para 3.19.0-80-generic,<br/>4.2.0-18-Generic para 4.2.0-42-generic,<br/>4.4.0-21-Generic para 4.4.0-75-generic |
14.04 LTS | 9.10 | 3.13.0-24-Generic para 3.13.0-121-generic,<br/>3.16.0-25-Generic para 3.16.0-77-generic,<br/>3.19.0-18-Generic para 3.19.0-80-generic,<br/>4.2.0-18-Generic para 4.2.0-42-generic,<br/>4.4.0-21-Generic para 4.4.0-81-generic |
14.04 LTS | 9.11 | 3.13.0-24-Generic para 3.13.0-128-generic,<br/>3.16.0-25-Generic para 3.16.0-77-generic,<br/>3.19.0-18-Generic para 3.19.0-80-generic,<br/>4.2.0-18-Generic para 4.2.0-42-generic,<br/>4.4.0-21-Generic para 4.4.0-91-generic |
14.04 LTS | 9.12 | 3.13.0-24-Generic para 3.13.0-132-generic,<br/>3.16.0-25-Generic para 3.16.0-77-generic,<br/>3.19.0-18-Generic para 3.19.0-80-generic,<br/>4.2.0-18-Generic para 4.2.0-42-generic,<br/>4.4.0-21-Generic para 4.4.0-96-generic |
16.04 LTS | 9.10 | 4.4.0-21-Generic para 4.4.0-81-generic,<br/>4.8.0-34-Generic para 4.8.0-56-generic,<br/>4.10.0-14-Generic para 4.10.0-24-generic |
16.04 LTS | 9.11 | 4.4.0-21-Generic para 4.4.0-91-generic,<br/>4.8.0-34-Generic para 4.8.0-58-generic,<br/>4.10.0-14-Generic para 4.10.0-32-generic |
16.04 LTS | 9.12 | 4.4.0-21-Generic para 4.4.0-96-generic,<br/>4.8.0-34-Generic para 4.8.0-58-generic,<br/>4.10.0-14-Generic para 4.10.0-35-generic |

## <a name="supported-file-systems-and-guest-storage-configurations-on-linux-vmwarephysical-servers"></a>Sistemas de ficheiro suportados e configurações de armazenamento de convidado no Linux (VMware/servidores físicos)

Armazenamento e sistemas de ficheiros o seguinte software de configuração é suportada em servidores Linux em execução nos servidores de VMware ou física:
* Sistemas de ficheiros: ext3 ext4, ReiserFS (Suse Linux Enterprise Server apenas), XFS
* Gestor de volumes: LVM2
* O software MultiPath i: mapeador de dispositivo

Dispositivos de armazenamento Paravirtualized (exportados pelo paravirtualized controladores de dispositivos) não são suportados.<br/>
Dispositivos de e/s de fila vários blocos não são suportados.<br/>
Servidores físicos com o controlador de armazenamento HP CCISS não são suportados.<br/>

>[!Note]
> Nos servidores Linux os seguintes diretórios (se configurar como separados partições /-sistemas de ficheiros) têm de estar todos no mesmo disco (o disco do SO) no servidor de origem: / (raiz), /boot /usr, /usr/local, /var, /etc/hosts<br/><br/>
> Funcionalidades de XFSv5 nos sistemas de ficheiros, tais como a soma de verificação de metadados XFS instalados são suportadas a partir da versão 9.10 do serviço de mobilidade. Se estiver a utilizar funcionalidades de XFSv5, certifique-se de que está a executar a versão de serviço de mobilidade 9.10 ou posterior. Pode utilizar o utilitário de xfs_info para verificar o superblock XFS para a partição. Se ftype estiver definido como 1, em seguida, XFSv5 funcionalidades estão a ser utilizadas.
>


## <a name="support-for-network-configuration"></a>Suporte para a configuração de rede
As tabelas seguintes resumem o suporte da configuração de rede em vários cenários de implementação que utilizar o Azure Site Recovery para replicar para o Azure.

### <a name="host-network-configuration"></a>Configuração de rede do anfitrião

**Configuração** | **Servidor VMware/físico** | **Hyper-V (com/sem o Virtual Machine Manager)**
--- | --- | ---
Agrupamento NIC | Sim<br/><br/>Não suportados quando replicar máquinas físicas| Sim
VLAN | Sim | Sim
IPv4 | Sim | Sim
IPv6 | Não | Não

### <a name="guest-vm-network-configuration"></a>Configuração de rede VM do convidado

**Configuração** | **Servidor VMware/físico** | **Hyper-V (com/sem o Virtual Machine Manager)**
--- | --- | ---
Agrupamento NIC | Não | Não
IPv4 | Sim | Sim
IPv6 | Não | Não
IP estático (Windows) | Sim | Sim
IP estático (Linux) | Sim <br/><br/>Máquinas virtuais está configurado para utilizar DHCP na reativação pós-falha  | Não
Vários NICs | Sim | Sim

### <a name="failed-over-azure-vm-network-configuration"></a>Configuração de rede de VM do Azure de efetuada a ativação pós-falha

**Redes do Azure** | **Servidor VMware/físico** | **Hyper-V (com/sem o Virtual Machine Manager)**
--- | --- | ---
ExpressRoute | Sim | Sim
ILB | Sim | Sim
ELB | Sim | Sim
Gestor de Tráfego | Sim | Sim
Vários NICs | Sim | Sim
IP Reservado | Sim | Sim
IPv4 | Sim | Sim
Manter o IP de origem | Sim | Sim
Virtual rede pontos finais de serviço (firewalls de armazenamento do Azure e redes virtuais) | Não | Não


## <a name="support-for-storage"></a>Suporte para armazenamento
As tabelas seguintes resumem o suporte da configuração de armazenamento em vários cenários de implementação que utilizar o Azure Site Recovery para replicar para o Azure.

### <a name="host-storage-configuration"></a>Configuração de armazenamento do anfitrião

**Configuração** | **Servidor VMware/físico** | **Hyper-V (com/sem o Virtual Machine Manager)**
--- | --- | --- | ---
NFS | Sim para VMware<br/><br/> Não para servidores físicos | N/D
SMB 3.0 | N/D | Sim
SAN (ISCSI) | Sim | Sim
Caminho multi (o MPIO)<br></br>Testar com: Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM para CLARiiON | Sim | Sim

### <a name="guest-or-physical-server-storage-configuration"></a>Configuração de armazenamento do servidor físico ou de convidado

**Configuração** | **Servidor VMware/físico** | **Hyper-V (com/sem o Virtual Machine Manager)**
--- | --- | ---
VMDK | Sim | N/D
VHD/VHDX | N/D | Sim
Ger 2 VM | N/D | Sim
EFI/UEFI| Não | Sim
Disco de cluster partilhado | Não | Não
Disco encriptado | Não | Não
NFS | Não | N/D
SMB 3.0 | Não | Não
RDM | Sim<br/><br/> N/d para servidores físicos | N/D
Disco > 1 TB | Sim<br/><br/>Até 4095 GB | Sim<br/><br/>Até 4095 GB
Disco com o tamanho de sector físico lógicos e 4 k de 4K | Sim | Não é suportada para VMs de 1 de geração<br/><br/>Não é suportada para a geração 2 VMs.
Disco com lógico de 4K e tamanho de setor físico de 512 bytes | Sim |  Sim
Volume com o disco repartidos > 1 TB<br/><br/> Gestão de volumes LVM lógica | Sim | Sim
Espaços de armazenamento | Não | Sim
Disco frequente Adicionar/remover | Não | Não
Excluir o disco | Sim | Sim
Caminho multi (o MPIO) | N/D | Sim

**Armazenamento do Azure** | **Servidor VMware/físico** | **Hyper-V (com/sem o Virtual Machine Manager)**
--- | --- | ---
LRS | Sim | Sim
GRS | Sim | Sim
RA-GRS | Sim | Sim
Armazenamento de acesso esporádico | Não | Não
Armazenamento frequente| Não | Não
Blobs de bloco | Não | Não
Encriptação rest(SSE)| Sim | Sim
Armazenamento Premium | Sim | Sim
Serviço de importação/exportação | Não | Não
Virtual rede pontos finais de serviço (firewalls de armazenamento do Azure e redes virtuais) configurada no armazenamento de destino da conta ou conta de armazenamento utilizada para armazenar dados de replicação de cache | Não | Não


## <a name="support-for-azure-compute-configuration"></a>Suporte para a configuração de computação do Azure

**Funcionalidade de computação** | **Servidor VMware/físico** | **Hyper-V (com/sem o Virtual Machine Manager)**
--- | --- | ---
Conjuntos de disponibilidade | Sim | Sim
HUB | Sim | Sim  
Managed disks | Sim | Sim<br/><br/>A reativação pós-falha no local na VM do Azure com discos geridos não é atualmente suportada.

## <a name="failed-over-azure-vm-requirements"></a>Requisitos de VM do Azure de efetuada a ativação pós-falha

Pode implementar o Site Recovery para replicar máquinas virtuais e servidores físicos que executem qualquer sistema operativo que o Azure suporte. Estes incluem a maioria das versões do Windows e do Linux. No local as VMs que pretende replicar devem estar em conformidade com os seguintes requisitos do Azure ao replicar para o Azure.

**Entidade** | **Requisitos** | **Detalhes**
--- | --- | ---
**Sistema operativo convidado** | Hyper-V para replicação do Azure: recuperação de sites suporta todos os sistemas operativos que são [suportado pelo Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Para VMware e replicação do servidor físico: verificar o Windows e Linux [pré-requisitos](site-recovery-vmware-to-azure-classic.md) | Verificação de pré-requisitos irá falhar se não suportado.
**Arquitetura do sistema operativo convidado** | 64 bits | Verificação de pré-requisitos irá falhar se não suportado
**Tamanho do disco do sistema operativo** | Até 2048 GB se estiver a replicar **VMs de VMware ou servidores físicos para o Azure**.<br/><br/>Até 2048 GB para **geração do Hyper-V 1** VMs.<br/><br/>Até 300 GB para **VMs de Hyper-V de geração 2**.  | Verificação de pré-requisitos irá falhar se não suportado
**Contagem de discos do sistema operativo** | 1 | Verificação de pré-requisitos irá falhar se não suportado.
**Contagem de discos de dados** | 64 ou menos se estiver a replicar **VMs de VMware para Azure**; 16 ou menos se estiver a replicar **VMs de Hyper-V para o Azure** | Verificação de pré-requisitos irá falhar se não suportado
**Tamanho VHD do disco de dados** | 4095 GB | Verificação de pré-requisitos irá falhar se não suportado
**Adaptadores de rede** | São suportados vários adaptadores |
**VHD partilhado** | Não suportado | Verificação de pré-requisitos irá falhar se não suportado
**Disco FC** | Não suportado | Verificação de pré-requisitos irá falhar se não suportado
**Formato de disco rígido** | VHD <br/><br/> VHDX | Embora VHDX não é atualmente suportado no Azure, a recuperação de sites converte automaticamente VHDX VHD, quando efetuar a ativação pós-falha para o Azure. Quando falhar no local as máquinas virtuais continuam a utilizar o formato VHDX.
**BitLocker** | Não suportado | O BitLocker tem de ser desativado antes de proteger uma máquina virtual.
**Nome da VM** | Entre 1 e 63 carateres. Restritas a letras, números e hífenes. O nome da VM tem de começar e terminar com uma letra ou número. | Atualize o valor nas propriedades de máquina virtual na recuperação de sites.
**Tipo VM** | Geração 1<br/><br/> Geração 2 – Windows | Geração 2 VMs com um tipo de disco de SO de basic (que inclui um ou dois volumes de dados formatados como VHDX) e inferior a 300 GB de espaço em disco são suportadas.<br></br>As VMs do Linux geração 2 não são suportadas. [Saiba mais](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="support-for-recovery-services-vault-actions"></a>Suporte para ações de cofre dos serviços de recuperação

**Ação** | **Servidor VMware/físico** | **Hyper-V (sem o Virtual Machine Manager)** | **Hyper-V (com o Virtual Machine Manager)**
--- | --- | --- | ---
Mover o Cofre entre grupos de recursos<br/><br/> Dentro e entre subscrições | Não | Não | Não
Mover o armazenamento, rede, as VMs do Azure através de grupos de recursos<br/><br/> Dentro e entre subscrições | Não | Não | Não


## <a name="support-for-provider-and-agent"></a>Suporte para o fornecedor e agente

**Nome** | **Descrição** | **Versão mais recente** | **Detalhes**
--- | --- | --- | --- | ---
**Fornecedor do Azure Site Recovery** | Coordena a comunicação entre servidores no local e o Azure <br/><br/> Instalado em servidores do Virtual Machine Manager no local ou em servidores de Hyper-V, se não houver nenhum servidor do Virtual Machine Manager | 5.1.2700.1 (disponível a partir do portal) | [Funcionalidades e correções mais recentes](https://aka.ms/latest_asr_updates)
**Recuperação de sites do Azure Unified configuração (VMware para o Azure)** | Coordena a comunicação entre servidores do VMware no local e o Azure <br/><br/> Instalado nos servidores do VMware no local | 9.12.4653.1 (disponível a partir do portal) | [Funcionalidades e correções mais recentes](https://aka.ms/latest_asr_updates)
**Serviço de mobilidade** | Coordena a replicação entre servidores de servidores/físico de VMware no local e o site secundário/Azure<br/><br/> Instalado na VM de VMware ou servidores físicos que pretende replicar  | 9.12.4653.1 (disponível a partir do portal) | [Funcionalidades e correções mais recentes](https://aka.ms/latest_asr_updates)
**Agente de serviços de recuperação do Azure (MARS) da Microsoft** | Coordena a replicação entre VMs de Hyper-V e o Azure<br/><br/> Instalado nos servidores de Hyper-V no local (com ou sem um servidor do Virtual Machine Manager) | Agente mais recente (disponível a partir do portal) |






## <a name="next-steps"></a>Passos seguintes
[Verificar pré-requisitos](site-recovery-prereq.md)
