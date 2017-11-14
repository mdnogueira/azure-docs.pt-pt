---
title: "Matriz de suporte para a replicação para um site secundário com o Azure Site Recovery | Microsoft Docs"
description: Resume os componentes e sistemas operativos suportados para o Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/30/2017
ms.author: raynew
ms.openlocfilehash: da120d8e325867eaf9eb8b9be1ae8d9152db54c4
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="support-matrix-for-replication-to-a-secondary-site-with-azure-site-recovery"></a>Matriz de suporte para a replicação para um site secundário com o Azure Site Recovery

Este artigo resume o que é suportado quando utiliza o [do Azure Site Recovery](site-recovery-overview.md) serviço para replicar para um site secundário no local.

## <a name="supported-scenarios"></a>Cenários suportados

**Implementação** | **Detalhes** 
--- | ---
**VMware para VMware** | Recuperação de desastre de VMs de VMware no local para o site VMware secundário.<br/><br/> Transferir o [Guia do utilizador InMage Scout](https://aka.ms/asr-scout-user-guide)
**Hyper-V para Hyper-V** | Recuperação de desastre de VMs de Hyper-V no local em nuvens VMM para uma nuvem VMM secundária.<br></br> Não é suportado sem o VMM.



  

## <a name="host-servers"></a>Servidores de anfitrião

**Implementação** | **Suporte**
--- | ---
**Servidor de VM de VMware/físico** | vCenter 5.5, 6.0 e 6.5 (suporte para 5.5 funcionalidades apenas)
**Hyper-V com o VMM** | Windows Server 2016 e o Windows Server 2012 R2 com as atualizações mais recentes.<br/><br/> Anfitriões do Windows Server 2016 devem ser geridos pelo VMM 2016.<br/><br/> Atualmente não são suportadas nuvens do VMM 2016 com uma mistura de anfitriões de R2 de 2012 e Windows Server 2016.<br/><br/> Implementação que incluem uma atualização de um existente VMM 2012 R2 para o System Center 2016 não são atualmente suportadas.


## <a name="support-for-replicated-machine-os-versions"></a>Suporte para versões de SO da máquina replicada

A tabela seguinte resume suporte do sistema operativo para máquinas replicados com a recuperação de Site. Qualquer carga de trabalho pode estar em execução no sistema operativo suportado.

**Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | ---
64 bits do Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 com, pelo menos, SP1<br/><br/> Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6 6.7, 6.8, 6.9, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5, 6.8 com o kernel compatível do Red Hat ou Unbreakable Enterprise Kernel versão 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 11 SP4  | Qualquer sistema operativo de convidado [suportadas pelo Hyper-V](https://technet.microsoft.com/library/mt126277.aspx)

## <a name="linux-machine-storage"></a>Armazenamento de máquina do Linux

Apenas máquinas Linux com o armazenamento seguinte podem ser replicadas:

- Ficheiro de sistema (EXT3 ETX4, ReiserFS, XFS).
- Mapeador de software MultiPath-dispositivo.
- Gestor de volume (LVM2).
- Servidores físicos com o armazenamento de controlador do HP CCISS não são suportados.
- O sistema de ficheiros ReiserFS só é suportado em SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration"></a>Configuração da rede

### <a name="hosts"></a>Anfitriões

**Configuração** | **Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | --- | ---
Agrupamento NIC | Sim | Sim
VLAN | Sim | Sim
IPv4 | Sim | Sim
IPv6 | Não | Não

### <a name="guest-vms"></a>VMs de convidado

**Configuração** | **Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | --- | ---
Agrupamento NIC | Não | Não
IPv4 | Sim | Sim
IPv6 | Não | Não
IP estático (Windows) | Sim | Sim
IP estático (Linux) | Sim | Sim
Vários NICs | Sim | Sim


## <a name="storage"></a>Armazenamento

### <a name="host-storage"></a>Armazenamento de anfitrião

**Armazenamento (anfitrião)** | **Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | --- | ---
NFS | Sim | N/D
SMB 3.0 | N/D | Sim
SAN (ISCSI) | Sim | Sim
Caminho multi (o MPIO) | Sim | Sim

### <a name="guest-or-physical-server-storage"></a>Armazenamento de servidor físico ou de convidado

**Configuração** | **Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | --- | ---
VMDK | Sim | N/D
VHD/VHDX | N/D | Sim (até 16 discos)
Ger 2 VM | N/D | Sim
Disco de cluster partilhado | Sim  | Não
Disco encriptado | Não | Não
UEFI| Sim | N/D
NFS | Não | Não
SMB 3.0 | Não | Não
RDM | Sim | N/D
Disco > 1 TB | Sim | Sim
Volume com o disco repartidos > 1 TB<br/><br/> LVM | Sim | Sim
Espaços de armazenamento | Não | Sim
Disco frequente Adicionar/remover | Sim | Não
Excluir o disco | Sim | Sim
Caminho multi (o MPIO) | N/D | Sim

## <a name="vaults"></a>cofres

**Ação** | **Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | --- | ---
Mover cofres entre grupos de recursos (dentro ou entre subscrições) | Não | Não
Mover o armazenamento, rede, as VMs do Azure através de grupos de recursos (dentro ou entre subscrições) | Não | Não

## <a name="provider-and-agent"></a>Fornecedor e agente

**Nome** | **Descrição** | **Versão mais recente** | **Detalhes**
--- | --- | --- | --- | ---
**Fornecedor do Azure Site Recovery** | Coordena a comunicação entre servidores no local e o Azure <br/><br/> Instalado nos servidores do VMM no local, ou em servidores de Hyper-V, se não houver nenhum servidor VMM | 5.1.19 ([disponível a partir do portal](http://aka.ms/downloaddra)) | [Funcionalidades e correções mais recentes](https://support.microsoft.com/kb/3155002)
**Serviço de mobilidade** | Coordena a replicação entre servidores do VMware no local ou servidores físicos e o site secundário<br/><br/> Instalado na VM de VMware ou servidores físicos que pretende replicar  | N/d (disponível a partir do portal) | N/D


## <a name="next-steps"></a>Passos seguintes

- [Replicar VMs de Hyper-V em nuvens VMM para um site secundário](tutorial-vmm-to-vmm.md)
- [Replicar VMs VMware e servidores físicos para um site secundário](tutorial-vmware-to-vmware.md)
