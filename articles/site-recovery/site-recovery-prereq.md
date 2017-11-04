---
title: "Pré-requisitos para replicação no Azure utilizando o Azure Site Recovery | Microsoft Docs"
description: "Saiba mais sobre os pré-requisitos para replicar as VMs e máquinas físicas no Azure utilizando o serviço do Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/19/2017
ms.author: raynew
ms.openlocfilehash: cb7c3892dc0acbffdec636e5e7b3ce063660153c
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2017
---
#  <a name="prerequisites-for-replication-from-on-premises-to-azure-by-using-site-recovery"></a>Pré-requisitos para replicação no local no Azure utilizando a recuperação de Site

> [!div class="op_single_selector"]
> * [Replicar a partir do Azure para o Azure](site-recovery-azure-to-azure-prereq.md)
> * [Replicar no local para o Azure](site-recovery-prereq.md)

O Azure Site Recovery pode ajudá-lo suportam a continuidade do negócio e a estratégia de recuperação (BCDR) desastres através da orquestração da replicação de uma máquina virtual do Azure (VM) a outra região do Azure. Recuperação de site replica também VMs e servidores físicos no local para a nuvem (Azure) ou para um datacenter secundário. Se ocorrer uma falha na sua localização principal, pode efetuar a ativação pós-falha para uma localização secundária para manter as aplicações e cargas de trabalho disponíveis. Pode efetuar a sua localização principal quando a localização primária retomam as operações normais. Para mais informações sobre a recuperação de sites, consulte [que é o Site Recovery?](site-recovery-overview.md).

Neste artigo, vamos resumem os pré-requisitos para iniciar a replicação de recuperação de Site a partir de uma máquina no local para o Azure.

Pode publicar quaisquer comentários na parte inferior do artigo. Também pode colocar questões técnicas no [fórum do Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="azure-requirements"></a>Requisitos do Azure

**Requisito** | **Detalhes**
--- | ---
**Conta do Azure** | A [conta do Microsoft Azure](http://azure.microsoft.com/). Pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
**O serviço de recuperação do site** | Para obter mais informações sobre preços para o serviço do Azure Site Recovery, consulte [preços da recuperação de Site](https://azure.microsoft.com/pricing/details/site-recovery/). |
**Armazenamento do Azure** | Necessita de uma conta de armazenamento do Azure para armazenar dados replicados. A conta de armazenamento tem de estar na mesma região que o cofre dos Serviços de Recuperação do Azure. As VMs do Azure são criadas quando ocorre a ativação pós-falha.<br/><br/> Consoante o modelo de recursos que pretende utilizar para ativação pós-falha da VM do Azure, pode configurar uma conta ao utilizar o [modelo de implementação Azure Resource Manager](../storage/common/storage-create-storage-account.md) ou [modelo de implementação clássica](../storage/common/storage-create-storage-account.md).<br/><br/>Pode utilizar o [armazenamento georredundante](../storage/common/storage-redundancy.md#geo-redundant-storage) ou o armazenamento localmente redundante. Recomendamos o armazenamento georredundante. Com o armazenamento georredundante, os dados são resilientes se ocorrer uma falha regional ou se a região primária não puder ser recuperada.<br/><br/> Pode utilizar uma conta de armazenamento do Azure standard ou pode utilizar o Premium Storage do Azure. [Armazenamento Premium](https://docs.microsoft.com/azure/storage/storage-premium-storage) é normalmente utilizado para as VMs que necessitam de uma forma consistente de elevado desempenho de e/s e a latência baixa. Armazenamento premium, uma VM pode alojar cargas de trabalho de I/O intensivo. Se utilizar o armazenamento premium para os dados replicados, também vai precisar de uma conta de armazenamento standard. Esta conta armazena os registos de replicação que capturam alterações contínuas aos dados no local.<br/><br/>
**Limitações de armazenamento** | Não é possível mover a contas do storage utiliza na recuperação de Site para um grupo de recursos diferente, ou move para ou com outra subscrição.<br/><br/> Atualmente, a replicar para contas de armazenamento premium no Sul da Índia e Índia Central no não está disponível.
**Rede do Azure** | Precisa de uma rede do Azure, para que as VMs do Azure ligar após a ativação pós-falha. A rede do Azure tem de estar na mesma região que o cofre dos Serviços de Recuperação.<br/><br/> No portal do Azure, pode criar uma rede do Azure utilizando o [modelo de implementação do Resource Manager](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou [modelo de implementação clássica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).<br/><br/> Se replicar do System Center Virtual Machine Manager (VMM) para o Azure, deve configurar o mapeamento de rede entre redes de VM do VMM e redes do Azure. Isto garante que as VMs do Azure ligar às redes adequadas depois da ativação pós-falha.
**Limitações de rede** | Não é possível mover as contas de rede que pode utiliza na recuperação de Site para um grupo de recursos diferente, ou move para ou com outra subscrição.
**Mapeamento da rede** | Se replicar VMs de Hyper-V do Microsoft em nuvens VMM, tem de configurar o mapeamento de rede. Isto garante que as VMs do Azure se ligar às redes adequadas quando são criados após a ativação pós-falha.

> [!NOTE]
> As secções seguintes descrevem os pré-requisitos para vários componentes do ambiente de cliente. Para obter mais informações sobre o suporte para configurações específicas, consulte o [matriz de suporte](site-recovery-support-matrix.md).
>

## <a name="disaster-recovery-of-vmware-vms-or-physical-windows-or-linux-servers-to-azure"></a>Recuperação após desastre de VMs de VMware ou servidores físicos do Windows ou Linux para o Azure
Os seguintes componentes são necessários para recuperação após desastre de VMs de VMware ou servidores físicos do Windows ou Linux. Estes são para além dos descritos na [requisitos do Azure](#azure-requirements).


### <a name="configuration-server-or-additional-process-server"></a>Servidor de configuração ou o servidor de processos adicionais

Configure uma máquina no local como o servidor de configuração para orquestrar a comunicação entre o site no local e o Azure. O abaixo tabela talks sobre os requisitos de sistema e do software de uma máquina virtual que podem ser configurados como um servidor de configuração.

> [!IMPORTANT]
> Quando implementar um servidor de configuração de proteção de máquinas virtuais VMware, recomendamos que implemente-o como um **altamente disponível (HA)** máquina virtual.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

### <a name="vmware-vcenter-or-vsphere-host-prerequisites"></a>VMware vCenter ou vSphere anfitrião pré-requisitos

| **Componente** | **Requisitos** |
| --- | --- |
| **vSphere** | Tem de ter um ou mais hipervisores vSphere de VMware.<br/><br/>Hipervisores tem de executar vSphere versão 6.0, 5.5 ou 5.1, com as atualizações mais recentes.<br/><br/>Recomendamos que vSphere anfitriões e servidores do vCenter ambos ser na mesma rede que o servidor de processos. A menos que tiver configurado a um servidor de processos dedicados, esta é a rede onde está localizado o servidor de configuração. |
| **vCenter** | Recomendamos que implemente um servidor VMware vCenter para gerir os seus anfitriões vSphere. Tem de executar vCenter versão 6.0 ou 5.5, com as atualizações mais recentes.<br/><br/>**Limitação**: recuperação de sites não suporta a replicação entre instâncias do vCenter vMotion. Armazenamento DRS e armazenamento vMotion também não são suportadas uma VM de destino principal após uma operação de reproteção.|

### <a name="replicated-machine-prerequisites"></a>Pré-requisitos de máquina replicada

| **Componente** | **Requisitos** |
| --- | --- |
| **No local máquinas** (VMs de VMware) | VMs replicadas tem de ter as ferramentas do VMware instalado e em execução.<br/><br/> As VMs têm de cumprir [pré-requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) para criar uma VM do Azure.<br/><br/>Capacidade do disco em cada máquina protegida não pode ser superior a 1,023 GB. <br/><br/>Um mínimo, 2 GB de espaço disponível na unidade de instalação é necessária para instalação de componentes.<br/><br/>Se pretender ativar a consistência multi VM, porta 20004 terá de ser aberta na firewall local de VM.<br/><br/>Os nomes de computador tem de ser entre 1 e 63 carateres (pode utilizar letras, números e hífenes). O nome tem de começar com uma letra ou número e terminar com uma letra ou número. <br/><br/>Depois de ativar a replicação para uma máquina, pode modificar o nome do Azure.<br/><br/> |
| **Máquinas Windows** (física ou VMware) | A máquina tem de executar um dos seguintes sistemas de operativos de 64 bits suportados: <br/>-Windows Server 2016 (servidor principal, servidor com experiência de ambiente de trabalho)<br/>-Windows Server 2012 R2<br/>-Windows Server 2012<br/>-Windows Server 2008 R2 com SP1 ou uma versão posterior<br/><br/> O sistema operativo tem de estar instalado na unidade C. O disco do SO tem de ser um disco básico do Windows e não dinâmica. O disco de dados pode ser dinâmico.<br/><br/>|
| **Computadores Linux** (física ou VMware) | A máquina tem de executar um dos seguintes sistemas de operativos de 64 bits suportados: <br/>-Red Hat Enterprise Linux 5.2 para 5.11, 6.1 para 6.9, 7.0 para 7.3<br/>-CentOS 5.2 para 5.11, 6.1 para 6.9, 7.0 para 7.3<br/>-Servidor Ubuntu 14.04 LTS (para obter uma lista das versões suportadas do kernel para Ubuntu, consulte [sistemas operativos suportados](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions))<br/>-Servidor Ubuntu 16.04 LTS (para obter uma lista das versões suportadas do kernel para Ubuntu, consulte [sistemas operativos suportados](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions))<br/>-Debian 7 ou Debian 8<br/>-Oracle Enterprise Linux 6.5 ou 6.4, com o kernel do Red Hat compatível com a ou Unbreakable Enterprise Kernel versão 3 (UEK3)<br/>-SUSE Linux Enterprise Server 11 SP4 ou SP3 do SUSE Linux Enterprise Server 11<br/><br/>Os ficheiros de /etc/hosts nas máquinas protegidas tem de ter entradas que mapeiam o nome do anfitrião local para endereços IP associados a todos os adaptadores de rede.<br/><br/>Após a ativação pós-falha, se pretender ligar à VM do Azure que está a executar Linux utilizando um cliente Secure Shell (SSH), certifique-se de que o serviço SSH na máquina protegida está definido para iniciar automaticamente no início do sistema. Certifique-se também que as regras de firewall permitem uma ligação SSH para a máquina protegida.<br/><br/>O nome de anfitrião, pontos de montagem, nomes de dispositivos e caminhos de sistema de Linux e os nomes de ficheiros (por exemplo, /etc/ e /usr) devem ser apenas em inglês.<br/><br/>Os seguintes diretórios (se configurar como partições separadas ou sistemas de ficheiros) têm de estar todos no mesmo disco (o disco do SO) no servidor de origem:<br/>- / (raiz)<br/>- / arranque<br/>-/usr<br/>-/ usr/local<br/>-/var<br/>- / etc<br/><br/>Atualmente, funcionalidades de v5 XFS, como a soma de verificação de metadados, não são suportadas pela recuperação de sites em sistemas de ficheiros XFS. Certifique-se de que os sistemas de ficheiros XFS não estiverem a utilizar quaisquer funcionalidades v5. Pode utilizar o utilitário de xfs_info para verificar o superblock XFS para a partição. Se **ftype** está definido como **1**, as funcionalidades de v5 XFS estão a ser utilizadas.<br/><br/>Nos servidores Red Hat Enterprise Linux 7 e CentOS 7, o utilitário de lsof tem de ser instalado e disponível.<br/><br/>


## <a name="disaster-recovery-of-hyper-v-vms-to-azure-no-vmm"></a>Recuperação após desastre de VMs de Hyper-V para o Azure (sem VMM)

Os seguintes componentes são necessários para recuperação após desastre de VMs de Hyper-V em nuvens VMM. Estes são para além dos descritos na [requisitos do Azure](#azure-requirements).

| **Pré-requisito** | **Detalhes** |
| --- | --- |
| **Anfitrião Hyper-V** |Um ou mais servidores no local tem de estar em execução no Windows Server 2012 R2 com as atualizações mais recentes e a função Hyper-V ativada ou Microsoft Hyper-V Server 2012 R2.<br/><br/>Servidores Hyper-V tem de ter uma ou mais VMs.<br/><br/>Servidores Hyper-V tem de estar ligados à Internet, diretamente ou através de um proxy.<br/><br/>Servidores Hyper-V tem de ter as correções descritas no artigo da Base de dados de conhecimento [2961977](https://support.microsoft.com/kb/2961977) instalado.
|**Fornecedor e agente**| Durante a implementação da recuperação de Site, instale o fornecedor do Azure Site Recovery. A instalação do fornecedor também instala o agente dos serviços de recuperação do Azure em cada servidor de Hyper-V que está a executar as VMs que pretende proteger. <br/><br/>Todos os servidores de Hyper-V no Cofre de recuperação de sites tem de ter as mesmas versões do fornecedor e o agente.<br/><br/>O fornecedor tem de ligar a recuperação de sites através da Internet. Tráfego pode ser enviado diretamente ou através de um proxy. Proxy baseado em HTTPS não é suportada. O servidor proxy tem de permitir acesso aos seguintes URLs:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/>Se tiver regras de firewall baseadas no endereço IP no servidor, certifique-se de que as regras permitem a comunicação com o Azure.<br/><br/> Permitir o [intervalos IP do datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)e HTTPS (porta 443).<br/><br/> Permita intervalos de endereços IP para a região do Azure da sua subscrição e para EUA Tsunami (utilizado para gestão de identidade e controlo de acesso).


## <a name="disaster-recovery-of-hyper-v-vms-in-vmm-clouds-to-azure"></a>Recuperação após desastre de VMs de Hyper-V em nuvens VMM para o Azure

Os seguintes componentes são necessários para recuperação após desastre de VMs de Hyper-V em nuvens VMM. Estes são para além dos descritos na [requisitos do Azure](#azure-requirements).

| **Pré-requisito** | **Detalhes** |
| --- | --- |
| **O Virtual Machine Manager** |Tem de ter um ou mais servidores VMM em execução no System Center 2012 R2 ou uma versão posterior. Cada servidor VMM tem de ter um ou mais nuvens configuradas. <br/><br/>Uma nuvem tem de ter:<br/>-Um ou mais grupos de anfitriões VMM.<br/>-Um ou mais servidores de anfitrião Hyper-V ou clusters em cada grupo anfitrião.<br/><br/>Para obter mais informações sobre como configurar as nuvens do VMM, consulte [como criar uma nuvem no Virtual Machine Manager 2012](http://social.technet.microsoft.com/wiki/contents/articles/2729.how-to-create-a-cloud-in-vmm-2012.aspx). |
| **Hyper-V** |Os servidores anfitriões de Hyper-V têm de executar, pelo menos, o Windows Server 2012 R2 com a função Hyper-V ativada ou o Microsoft Hyper-V Server 2012 R2. Têm de estar instaladas as atualizações mais recentes.<br/><br/> Um servidor de Hyper-V tem de ter uma ou mais VMs.<br/><br/> Um servidor anfitrião Hyper-V ou cluster que inclui VMs que pretende replicar tem de ser gerido numa nuvem VMM.<br/><br/>Servidores Hyper-V tem de estar ligados à Internet, diretamente ou através de um proxy.<br/><br/>Servidores Hyper-V tem de ter as correções descritas no artigo da Base de dados de conhecimento [2961977](https://support.microsoft.com/kb/2961977) instalado.<br/><br/>Servidores de anfitrião Hyper-V tem acesso à Internet para replicação de dados no Azure. |
| **Fornecedor e agente** |Durante a implementação do Azure Site Recovery, instale o fornecedor do Azure Site Recovery no servidor do VMM. Instale o agente de serviços de recuperação nos anfitriões Hyper-V. O fornecedor e o agente tem de ligar ao Azure diretamente através da Internet ou através de um proxy. Os proxies baseados em HTTPS não são suportados. O servidor de proxy no servidor VMM e anfitriões Hyper-V deve permitir acesso a: <br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>Se tiver regras de firewall baseadas no endereço IP no servidor do VMM, certifique-se de que as regras permitem a comunicação com o Azure.<br/><br/> Permitir o [intervalos IP do datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e HTTPS (porta 443).<br/><br/>Permita intervalos de endereços IP para a região do Azure para a sua subscrição e para EUA Tsunami (utilizado para gestão de identidade e controlo de acesso).<br/><br/> |

### <a name="replicated-machine-prerequisites"></a>Pré-requisitos de máquina replicada

| **Componente** | **Detalhes** |
| --- | --- |
| **VMs protegidas** | Recuperação de sites suporta todos os sistemas operativos que são suportados pelo [Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).<br/><br/>As VMs têm de cumprir os [pré-requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) para criar uma VM do Azure. Os nomes de computador tem de ser entre 1 e 63 carateres (pode utilizar letras, números e hífenes). O nome tem de começar com uma letra ou número e terminar com uma letra ou número. <br/><br/>Pode modificar o nome da VM, depois de ativar a replicação para a VM. <br/><br/> Capacidade do disco para cada máquina protegida não pode ser superior a 1,023 GB. Uma VM pode ter até 16 discos (até 16 TB).<br/><br/>


## <a name="disaster-recovery-of-hyper-v-vms-in-vmm-clouds-to-a-customer-owned-site"></a>Recuperação após desastre de VMs de Hyper-V em nuvens VMM para um site de propriedade de cliente

Os seguintes componentes são necessários para recuperação após desastre de VMs de Hyper-V em nuvens VMM para um site de propriedade de cliente. Estes são para além dos descritos na [requisitos do Azure](#azure-requirements).

| **Componente** | **Detalhes** |
| --- | --- |
| **O Virtual Machine Manager** |  Recomendamos que implemente um servidor do VMM no site primário e o site secundário.<br/><br/> Para replicar entre nuvens num único servidor VMM, terá, pelo menos, dois nuvens configuradas no servidor do VMM.<br/><br/> Servidores do VMM tem de estar em execução, pelo menos, System Center 2012 SP1, com as atualizações mais recentes.<br/><br/> Cada servidor VMM tem de ter uma ou mais nuvens. Todas as nuvens têm de ter o conjunto de perfis de capacidade do Hyper-V. <br/><br/>As nuvens têm de ter um ou mais grupos de anfitriões do VMM. Para obter mais informações sobre como configurar as nuvens do VMM, consulte [preparar para a implementação do Azure Site Recovery](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric). |
| **Hyper-V** | Servidores Hyper-V tem de estar em execução, pelo menos, Windows Server 2012 com a função Hyper-V ativada e instalou as atualizações mais recentes.<br/><br/> Um servidor de Hyper-V tem de ter uma ou mais VMs.<br/><br/>  Servidores de anfitrião Hyper-V tem de estar localizados em grupos de anfitriões em nuvens VMM primárias e secundárias.<br/><br/> Se executar o Hyper-V num cluster no Windows Server 2012 R2, recomendamos que instale a atualização descrita no artigo da Base de dados de conhecimento [2961977](https://support.microsoft.com/kb/2961977).<br/><br/> Se executar o Hyper-V num cluster no Windows Server 2012 e tiver um cluster de baseadas no endereço IP estático, não é criado automaticamente um mediador de clusters. Tem de configurar manualmente o Mediador de clusters. Para obter mais informações sobre o Mediador de clusters, consulte [configurar a função de Mediador de réplicas para replicação cluster a cluster](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx). |
| **Fornecedor** | Durante a implementação da recuperação de Site, instale o fornecedor do Azure Site Recovery nos servidores VMM. O fornecedor comunica com a recuperação de Site através de HTTPS (porta 443) para orquestrar a replicação. Ocorre a replicação de dados entre os servidores de Hyper-V principais e secundários através da rede local ou através de uma ligação VPN.<br/><br/> O fornecedor que está em execução no servidor do VMM necessita de acesso para os seguintes URLs:<br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>O fornecedor da recuperação de sites têm de permitir a comunicação de firewall entre os servidores do VMM para o [intervalos IP do datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)e permitir que o protocolo HTTPS (porta 443). |


## <a name="url-access"></a>Acesso de URL
Estes URLs têm de estar disponíveis de servidores de anfitrião do VMware, o VMM e o Hyper-V:

|**URL** | **VMM para o VMM** | **VMM para o Azure (VMM to Azure)** | **Hyper-V para o Azure** | **VMware para o Azure** |
|--- | --- | --- | --- | --- |
|``*.accesscontrol.windows.net`` | Permitir | Permitir | Permitir | Permitir |
|``*.backup.windowsazure.com`` | Não necessário | Permitir | Permitir | Permitir |
|``*.hypervrecoverymanager.windowsazure.com`` | Permitir | Permitir | Permitir | Permitir |
|``*.store.core.windows.net`` | Permitir | Permitir | Permitir | Permitir |
|``*.blob.core.windows.net`` | Não necessário | Permitir | Permitir | Permitir |
|``https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi`` | Não necessário | Não necessário | Não necessário | Permitir a transferência do SQL Server |
|``time.windows.com`` | Permitir | Permitir | Permitir | Permitir|
|``time.nist.gov`` | Permitir | Permitir | Permitir | Permitir |
