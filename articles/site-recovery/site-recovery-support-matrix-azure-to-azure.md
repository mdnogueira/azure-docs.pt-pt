---
title: Matriz de suporte do Azure Site Recovery para replicar a partir do Azure para o Azure | Microsoft Docs
description: "Resume os sistemas operativos suportados e configurações para a replicação do Azure Site Recovery máquinas virtuais do Azure (VMs) de uma região para outro para as necessidades de recuperação (DR) após desastre."
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/31/2017
ms.author: sujayt
ms.openlocfilehash: b157e2f90fa2daf00cf71472eb799ee98797b4dc
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2017
---
# <a name="azure-site-recovery-support-matrix-for-replicating-from-azure-to-azure"></a>Matriz de suporte do Azure Site Recovery para replicar a partir do Azure para o Azure


>[!NOTE]
>
> Replicação de recuperação de site para máquinas virtuais do Azure está atualmente em pré-visualização.

Este artigo resume os componentes e configurações suportadas para o Azure Site Recovery quando replicar e recuperar máquinas virtuais do Azure a partir de uma região noutra região.

## <a name="user-interface-options"></a>Opções de interface de utilizador

**Interface de utilizador** |  **Suportado / não suportado**
--- | ---
**Portal do Azure** | Suportado
**Portal clássico** | Não suportado
**PowerShell** | Não é atualmente suportado
**API REST** | Não é atualmente suportado
**CLI** | Não é atualmente suportado


## <a name="resource-move-support"></a>Suporte de movimentação de recursos

**Tipo de movimentação de recurso** | **Suportado / não suportado** | **Observações**  
--- | --- | ---
**Mover o Cofre entre grupos de recursos** | Não suportado |Não é possível mover o Cofre de serviços de recuperação em grupos de recursos.
**Mover computação, armazenamento e de rede entre grupos de recursos** | Não suportado |Se mover uma máquina virtual (ou os respetivos componentes, tais como o armazenamento e de rede associados) depois de ativar a replicação, terá de desativar a replicação e ativar a replicação da máquina virtual novamente.



## <a name="support-for-deployment-models"></a>Suporte para modelos de implementação

**Modelo de implementação** | **Suportado / não suportado** | **Observações**  
--- | --- | ---
**Clássico** | Suportado | Apenas pode replicar uma máquina virtual clássica e recuperá-la como uma máquina virtual clássica. Não é possível recuperá-la como uma máquina virtual do Gestor de recursos. Se implementar uma VM clássica sem uma rede virtual e diretamente a uma região do Azure, não é suportada.
**Resource Manager** | Suportado |

>[!NOTE]
>
> 1. Replicar máquinas virtuais do Azure a partir de uma subscrição para outro para cenários de recuperação após desastre não é suportada.
> 2. Azure migrar máquinas virtuais entre subscrições não é suportada.
> 3. Azure migrar máquinas virtuais na mesma região não é suportada.
> 4. Migrar máquinas virtuais do Azure do modelo de implementação clássica para o recurso de modelo de implementação manager não é suportado.

## <a name="support-for-replicated-machine-os-versions"></a>Suporte para versões de SO da máquina replicada

O abaixo suporte é aplicável a qualquer carga de trabalho em execução no sistema operativo mencionados.

#### <a name="windows"></a>Windows

- Windows Server 2016 (servidor principal, servidor com experiência de utilização) *
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 com, pelo menos, SP1

>[!NOTE]
>
> \*Windows Server 2016 Nano Server não é suportado.

#### <a name="linux"></a>Linux

- Red Hat Enterprise Linux 6.7, 6.8 6.9, 7.0, 7.1, 7.2, 7.3
- CentOS 6.5, 6.6 6.7, 6.8, 6.9, 7.0, 7.1, 7.2, 7.3
- Ubuntu 14.04 LTS Server [ (versões de kernel suportado)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Ubuntu 16.04 LTS Server [ (versões de kernel suportado)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Debian 7
- Debian 8
- Oracle Enterprise Linux 6.4, 6.5 com o kernel compatível do Red Hat ou Unbreakable Enterprise Kernel versão 3 (UEK3)
- SP3 do SUSE Linux Enterprise Server 11
- SP4 do SUSE Linux Enterprise Server 11

(Não é suportada a atualização de replicar máquinas do SLES 11 SP3 para SLES 11 SP4. Se uma máquina replicada tiver sido atualizada do SLES 11SP3 para SLES 11 SP4, terá de desativar a replicação e proteger a máquina post novamente a atualização.)

>[!NOTE]
>
> Servidores de Ubuntu utilizando a palavra-passe com autenticação e início de sessão, utilizando o pacote de nuvem init para configurar máquinas virtuais na nuvem, poderá ter palavra-passe base e início de sessão desativado após a ativação pós-falha (dependendo da configuração de cloudinit.) Início de sessão de palavra-passe com base pode ser novamente ativado na máquina virtual ao repor a palavra-passe no menu de definições (sob a suporte + a resolução de problemas de secção) da máquina virtual no portal do Azure de ativação pós-falha.

### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Versões suportadas do kernel Ubuntu máquinas virtuais do Azure

**Versão** | **Versão do serviço de mobilidade** | **Versão de kernel** |
--- | --- | --- |
14.04 LTS | 9.9 | 3.13.0-24-Generic para 3.13.0-117-generic,<br/>3.16.0-25-Generic para 3.16.0-77-generic,<br/>3.19.0-18-Generic para 3.19.0-80-generic,<br/>4.2.0-18-Generic para 4.2.0-42-generic,<br/>4.4.0-21-Generic para 4.4.0-75-generic |
14.04 LTS | 9.10 | 3.13.0-24-Generic para 3.13.0-121-generic,<br/>3.16.0-25-Generic para 3.16.0-77-generic,<br/>3.19.0-18-Generic para 3.19.0-80-generic,<br/>4.2.0-18-Generic para 4.2.0-42-generic,<br/>4.4.0-21-Generic para 4.4.0-81-generic |
14.04 LTS | 9.11 | 3.13.0-24-Generic para 3.13.0-125-generic,<br/>3.16.0-25-Generic para 3.16.0-77-generic,<br/>3.19.0-18-Generic para 3.19.0-80-generic,<br/>4.2.0-18-Generic para 4.2.0-42-generic,<br/>4.4.0-21-Generic para 4.4.0-83-generic |
16.04 LTS | 9.10 | 4.4.0-21-Generic para 4.4.0-81-generic,<br/>4.8.0-34-Generic para 4.8.0-56-generic,<br/>4.10.0-14-Generic para 4.10.0-24-generic |
16.04 LTS | 9.11 | 4.4.0-21-Generic para 4.4.0-83-generic,<br/>4.8.0-34-Generic para 4.8.0-58-generic,<br/>4.10.0-14-Generic para 4.10.0-27-generic |

## <a name="supported-file-systems-and-guest-storage-configurations-on-azure-virtual-machines-running-linux-os"></a>Sistemas de ficheiro suportados e configurações de armazenamento de convidado em máquinas virtuais do Azure com o SO Linux

* Sistemas de ficheiros: ext3 ext4, ReiserFS (Suse Linux Enterprise Server apenas), XFS
* Gestor de volumes: LVM2
* O software MultiPath i: mapeador de dispositivo

## <a name="region-support"></a>Suporte de região

Pode replicar e recuperar VMs entre as duas regiões do mesmo cluster geográfica.

**Cluster geográfica** | **Regiões do Azure**
-- | --
América do | Canadá leste, EUA Central Canadá Central, -Sul, EUA Centro Oeste, EUA leste, EUA Leste 2, EUA oeste, EUA oeste, 2, EUA Central, EUA Centro-Norte
Europa | RU oeste, RU Sul, Europa Norte, Europa Ocidental
Ásia | Sul da Índia, Índia Central, Sudeste asiático, leste do Japão, Ásia Oriental, oeste do Japão, Coreia Central, Coreia South
Austrália   | Leste da Austrália, Sudeste da Austrália

>[!NOTE]
>
> Região do Sul do Brasil, pode apenas a replicação e ativação pós-falha para um dos Sul Central dos EUA Central EUA oeste, EUA leste, EUA Leste 2, EUA oeste, EUA oeste 2 e regiões Norte Central-nos e voltar a falhar.


## <a name="support-for-compute-configuration"></a>Suporte para a configuração de computação

**Configuração** | **Suportado/não suportado** | **Observações**
--- | --- | ---
Tamanho | Nenhum tamanho de VM do Azure com, pelo menos, 2 núcleos de CPU e de 1 GB de RAM | Consulte [tamanhos de máquina virtual do Azure](../virtual-machines/windows/sizes.md)
Conjuntos de disponibilidade | Suportado | Se utilizar a opção predefinida durante 'Ativar a replicação' passo no portal, o conjunto de disponibilidade é automaticamente criada com base na configuração da região de origem. Pode alterar a disponibilidade de destino definida em ' replicados item > Definições > computação e rede > do conjunto de disponibilidade ' qualquer altura.
VMs do híbrida utilize benefício (CONCENTRADOR) | Suportado | Se a VM de origem tiver licenças HUB ativada, a ativação pós-falha de teste ou de uma VM de ativação pós-falha também utiliza a licença do HUB.
Conjuntos de dimensionamento de máquinas virtuais | Não suportado |
Imagens de galeria do Azure - Microsoft publicado | Suportado | Suportado, desde que a VM é executado num sistema operativo suportado pela recuperação de sites
Imagens de galeria do Azure - terceiros publicadas | Suportado | Suportado, desde que a VM é executado num sistema operativo suportado pela recuperação de sites.
Personalizar imagens - terceiros publicadas | Suportado | Suportado, desde que a VM é executado num sistema operativo suportado pela recuperação de sites.
VMs migrada utilizando a recuperação de sites | Suportado | Se estiver a que migrar uma máquina de VMware/físico para o Azure utilizando a recuperação de sites, terá de desinstalar a versão mais antiga do serviço de mobilidade e reinicie a máquina antes de replicar-o para outra região do Azure.

## <a name="support-for-storage-configuration"></a>Suporte para a configuração de armazenamento

**Configuração** | **Suportado/não suportado** | **Observações**
--- | --- | ---
Tamanho máximo de disco do SO | 2048 GB | Consulte [discos utilizados por VMs.](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)
Tamanho do disco de dados | 4095 GB | Consulte [discos utilizados por VMs.](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)
Número de discos de dados | Até 64 como suportados por um tamanho de VM do Azure específico | Consulte [tamanhos de máquina virtual do Azure](../virtual-machines/windows/sizes.md)
Disco temporário | Sempre excluídos da replicação | Disco temporário foi excluído da replicação sempre. Não deve colocar todos os dados persistentes em disco temporário de acordo com a documentação de orientação do Azure. Consulte [disco temporário em VMs do Azure](../virtual-machines/windows/about-disks-and-vhds.md#temporary-disk) para obter mais detalhes.
Taxa de alteração de dados no disco | Máximo de 6 MBps por disco | Se os dados de média, taxa alterar no disco para além dos 6 MBps continuamente, a replicação não irá detetar cópias de segurança. No entanto, se for uma rajada ocasionais dados e a taxa de alteração de dados for superior a 6 MBps durante algum tempo e ficar, a replicação será acompanhar. Neste caso, poderá ver pontos de recuperação ligeiramente atrasado.
Discos em contas de armazenamento standard | Suportado |
Discos em contas de armazenamento premium | Suportado | Se uma VM tem discos distribuídos por contas do standard storage e premium, pode selecionar uma conta de armazenamento de destino diferente para cada disco garantir que têm a mesma configuração de armazenamento na região de destino
Discos gerida padrão | Não suportado |  
Discos Premium gerido | Não suportado |
Espaços de armazenamento | Suportado |         
Encriptação de Inativos (SSE) | Suportado | Para contas de armazenamento de cache e de destino, pode selecionar uma conta de armazenamento de SSE ativada.     
Encriptação de disco do Azure (ADE) | Não suportado |
Disco frequente Adicionar/remover | Não suportado | Se adicionar ou remover o disco de dados na VM, terá de desativar a replicação e ativar a replicação novamente para a VM.
Excluir o disco | Não suportado|   Disco temporário é excluído por predefinição.
LRS | Suportado |
GRS | Suportado |
RA-GRS | Suportado |
ZRS | Não suportado |  
Armazenamento de acesso frequente e esporádico | Não suportado | Discos da máquina virtual não são suportados no armazenamento de acesso frequente e esporádico

>[!IMPORTANT]
> Certifique-se de que segue o [orientações de armazenamento](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) para a origem de Azure máquinas virtuais para evitar quaisquer problemas de desempenho. Se seguir as predefinições, a recuperação de sites irá criar as contas de armazenamento necessários com base na configuração da origem. Se personalizar e selecione as suas próprias definições, certifique-se de que segue a (../ storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) como as VMs de origem.

## <a name="support-for-network-configuration"></a>Suporte para a configuração de rede
**Configuração** | **Suportado/não suportado** | **Observações**
--- | --- | ---
Interface de rede (NIC) | Número máximo de um máximo de NICs suportado por um tamanho de VM do Azure específico | NICs são criados quando é criada a VM como parte da ativação pós-falha de teste ou de operação de ativação pós-falha. O número de NICs a ativação pós-falha da VM depende do número de NICs a VM tem no momento da ativação da replicação de origem. Se lhe adicionar/remover NIC depois de ativar a replicação, não afeta a contagem NIC na ativação pós-falha da VM.
Balanceador de Carga de Externo | Suportado | Precisa de associar o Balanceador de carga previamente configurada utilizando um script de automatização do azure num plano de recuperação.
Balanceador de carga interno | Suportado | Precisa de associar o Balanceador de carga previamente configurada utilizando um script de automatização do azure num plano de recuperação.
IP público| Suportado | Tem de associar um IP público já existente para o NIC ou crie um e associe a NIC utilizando um script de automatização do azure num plano de recuperação.
NSG no NIC (Resource Manager)| Suportado | Precisa de associar o NSG para o NIC utilizando um script de automatização do azure num plano de recuperação.  
NSG na sub-rede (Resource Manager e clássico)| Suportado | Precisa de associar o NSG para o NIC utilizando um script de automatização do azure num plano de recuperação.
NSG na VM (clássica)| Suportado | Precisa de associar o NSG para o NIC utilizando um script de automatização do azure num plano de recuperação.
(IP estático) do IP reservado / manter o IP de origem | Suportado | Se a NIC na VM de origem tiver uma configuração de IP estático e a sub-rede de destino tiver o mesmo IP disponível, é atribuída para a ativação pós-falha da VM. Se a sub-rede de destino não tem o mesmo IP disponível, uma dos IPs disponíveis na sub-rede é reservada para esta VM. Pode especificar um IP fixo da sua preferência em ' item replicados > Definições > computação e rede > interfaces de rede. Pode selecionar a NIC e especifique a sub-rede e o IP da sua preferência.
IP dinâmico| Suportado | Se a NIC na VM de origem tiver uma configuração de IP dinâmica, o NIC a ativação pós-falha de VM também é dinâmica por predefinição. Pode especificar um IP fixo da sua preferência em ' item replicados > Definições > computação e rede > interfaces de rede. Pode selecionar a NIC e especifique a sub-rede e o IP da sua preferência.
Integração do Gestor de Tráfego | Suportado | Pode pré-configurar o Gestor de tráfego de forma a que o tráfego é encaminhado para o ponto final na região de origem regulares e para o ponto final na região de destino em caso de ativação pós-falha.
Azure geridos DNS | Suportado |
DNS Personalizado  | Suportado |    
Proxy não autenticados | Suportado | Consulte [documento de orientação na rede.](site-recovery-azure-to-azure-networking-guidance.md)    
Proxy autenticado | Não suportado | Se a VM estiver a utilizar um proxy autenticado para conectividade de saída, não é possível replicar utilizando o Azure Site Recovery.    
VPN de site a Site no local (com ou sem ExpressRoute)| Suportado | Certifique-se de que o UDRs e NSGs estão configurados de forma a que o tráfego de recuperação do Site não for encaminhado para o local. Consulte [documento de orientação na rede.](site-recovery-azure-to-azure-networking-guidance.md)  
Ligação VNET a VNET | Suportado | Consulte [documento de orientação na rede.](site-recovery-azure-to-azure-networking-guidance.md)  


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [redes orientações para replicar as VMs do Azure](site-recovery-azure-to-azure-networking-guidance.md)
- Começar a proteger as cargas de trabalho por [replicar as VMs do Azure](site-recovery-azure-to-azure.md)
