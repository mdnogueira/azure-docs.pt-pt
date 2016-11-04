---
title: 'Recuperação de Sites: Perguntas mais frequentes | Microsoft Docs'
description: Este artigo responde a questões recorrentes sobre o Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: jwhit
editor: ''

ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/27/2016
ms.author: raynew

---
# O Azure Site Recovery: Perguntas mais frequentes (FAQ)
Leia este artigo para rever as perguntas mais frequentes sobre o serviço do Azure Site Recovery.

Se ainda tiver questões depois de ler o artigo pode publicá-las na parte inferior deste artigo ou no [Fórum de Azure Recovery Services](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).

## Geral
### O que faz a Recuperação de Sites?
A Recuperação de Sites contribui para a estratégia da continuidade do seu negócio e recuperação após desastre (BCDR) através da orquestração e automatização de replicação de máquinas virtuais no local e servidores físicos para o Azure ou para um datacenter secundário. [Saiba mais](site-recovery-overview.md).

### O que pode ser replicado pela Recuperação de Sites?
* **Máquinas virtuais Hyper-V**: a Recuperação de Sites pode replicar qualquer carga de trabalho em execução numa VM Hyper-V suportada. 
* **Servidores físicos**: a Recuperação de Sites pode replicar qualquer carga de trabalho em execução num servidor físico Windows/Linux suportado.
* **Máquinas virtuais VMware**: a Recuperação de Sites pode replicar qualquer carga de trabalho em execução numa VM VMware suportada.

### O que preciso no Hyper-V?
Para o servidor de anfitrião Hyper-V, o que precisa depende do cenário de implementação. Confira os pré-requisitos do Hyper-V para:

* [Replicar VMs Hyper-V (sem o VMM) para o Azure](site-recovery-hyper-v-site-to-azure.md#before-you-start)
* [Replicar VMs Hyper-V (com o VMM) para o Azure](site-recovery-vmm-to-azure.md#before-you-start)
* [Replicar VMs Hyper-V para um datacenter secundário](site-recovery-vmm-to-vmm.md#before-you-start)

Para os convidados em execução no servidor de anfitrião Hyper-V:

* Se está a replicar para um site secundário, leia sobre [os sistemas operativos convidados suportados pelas VMs Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
* Se está a replicar para o Azure, a Recuperação de Sites suporta os mesmos sistemas operativos convidados que são [suportados pelo Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### Posso proteger as VMs se o Hyper-V está a executar num sistema operativo cliente?
Não, as VMs têm de estar localizadas num servidor de anfitrião Hyper-V que está a ser executado na máquina do servidor Windows suportada. Se precisar de proteger um computador cliente, pode replicá-la como máquina física [para o Azure](site-recovery-vmware-to-azure-classic.md) ou para um [atacenter secundário](site-recovery-vmware-to-vmware.md).

### Quais as cargas de trabalho que podem ser protegidas com a Recuperação de Sites ?
A Recuperação de Sites pode replicar qualquer carga de trabalho em execução numa VM ou num servidor físico suportado. A Recuperação de Sites fornece suporte para replicação com reconhecimento de aplicações para que estas possam ser recuperadas para um estado inteligente. Integra-se com aplicações da Microsoft, tais como o SharePoint, Exchange, Dynamics, SQL Server e Active Directory, e funciona diretamente com os principais fornecedores, incluindo Oracle, SAP, IBM e Red Hat. [Saiba mais](site-recovery-workload.md) sobre proteção de carga de trabalho.

### Os anfitriões Hyper-V têm de estar em nuvens VMM do System Center para que possa ser replicados com a Recuperação de Sites ?
Se pretender replicar para um datacenter secundário, as VMs Hyper-V têm de estar localizadas em servidores anfitriões Hyper-V numa nuvem VMM. Se pretender replicar para o Azure, pode replicar as VMs nos servidores anfitrião Hyper-V com ou sem nuvens VMM. [Saiba mais](site-recovery-hyper-v-site-to-azure.md). 

### Posso implementar a Recuperação de Sites com o VMM se tiver apenas um servidor VMM?
Sim. Pode optar por replicar as VMs nos servidores Hyper-V na nuvem VMM para o Azure ou pode replicar entre nuvens VMM no mesmo servidor. Para replicações local para local, recomendamos que tenha um servidor VMM tanto no site primário como no secundário. [Saiba mais](site-recovery-single-vmm.md)

### Que servidores físicos posso proteger?
Pode proteger servidores físicos a executar os sistemas operativos Windows e Linux suportados para o Azure ou para um site secundário. [Saiba](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) sobre os requisitos do sistema operativo para a replicação para o Azure ou para o site secundário. Tenha em atenção que os servidores físicos que são replicados para o armazenamento do Azure executarão como VMs no Azure quando ocorrer a ativação pós-falha. Atualmente, quando fizer a reativação a partir do Azure para o site no local, não é suportado quando se reativa para um servidor físico. Apenas poderá reativar para uma máquina virtual em execução no VMware.

### Quais as VMs VMware que posso proteger?
Para proteger as VMs VMware precisará de um hipervisor vSphere e de máquinas virtuais que executem ferramentas do VMware. Recomendamos também que tenha um servidor VMware vCenter para gerir os hipervisores. [Saiba mais](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) sobre os requisitos exatos para replicar servidores VMware e VMs para o Azure ou para um site secundário.

### Posso gerir a recuperação após desastre para as minhas sucursais com a Recuperação de Sites?
Sim. Quando utilizar a Recuperação de Sites para orquestrar replicação e ativação pós-falha das sucursais, pode orquestrar e ver todos as cargas de trabalho das suas sucursais numa localização central. Pode facilmente executar as ativações pós-falha e administrar a recuperação após desastre de todas as sucursais a partir da sede, sem visitar as sucursais. 

## Segurança
### Os dados de replicação são enviados para o serviço de Recuperação de Sites?
Não, a Recuperação de Sites não interceta os dados replicados ou tem quaisquer informação sobre o que está em execução nas suas máquinas virtuais ou servidores físicos.

Os dados de replicação são trocados entre os anfitriões Hyper-V no local, os hipervisores VMware ou os servidores físicos, e o armazenamento do Azure ou o seu site secundário.  A Recuperação de Sites não tem capacidade para intercetar esses dados. Apenas os metadados necessários para orquestrar a replicação e a ativação pós-falha são enviados para o serviço de Recuperação de Sites. 

A Recuperação de Sites está certificada segundo a norma ISO 27001:2005 e está em processo de conclusão de avaliação das normas HIPAA, DPA e FedRAMP JAB.

### Para fins de conformidade, até mesmo os metadados dos nossos ambientes no local têm de permanecer na mesma região geográfica. A Recuperação de Sites consegue fazê-lo?
Sim. Quando criar um cofre de Recuperação de Sites numa região, podemos assegurar que todos os metadados que é necessário ativar e orquestrar com replicação e ativação pós-falha permanecem dentro do limite geográfico dessa região.

### A Recuperação de Sites faz encriptação de replicação?
Há suporte para encriptação em trânsito para as máquinas virtuais e servidores físicos que replicam entre sites no local. Há suporte tanto para a encriptação em trânsito, como para encriptação dos inativos, quando as máquinas virtuais e servidores físicos replicam para o Azure. 

## Replicação
### Existem pré-requisitos para replicar máquinas virtuais para o Azure?
As máquinas virtuais que pretende replicar para o Azure devem estar em conformidade com [os requisitos Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

### Pode replicar máquinas virtuais de 2.ª geração do Hyper-V para o Azure?
Sim. A Recuperação de Sites converte VMs de 2.ª geração para a 1.ª geração durante a ativação pós-falha. Na reativação pós-falha, a VM é convertida novamente para 2.ª geração. [Ler mais](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### Se replicar para o Azure como posso pagar as VMs do Azure?
Durante a replicação normal, os dados são replicados para o armazenamento do Azure com redundância geográfica e não é necessário pagar quaisquer custos de máquina virtual IaaS do Azure.  Quando executar uma ativação pós-falha para o Azure, a Recuperação de Sites cria automaticamente máquinas virtuais de IaaS do Azure e, depois disso, serão cobrados os recursos de computação que consumir no Azure.

### Existe um SDK que posso utilizar para automatizar fluxos de trabalho da Recuperação de Sites?
Sim. Pode automatizar fluxos de trabalho da Recuperação de Sites utilizando a API REST, PowerShell ou o SDK do Azure. Saiba mais sobre [Implementação da Recuperação de Sites utilizando o PowerShell e o Azure Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md).

### Se eu replicar para o Azure qual o tipo de conta de armazenamento de que preciso?
Precisará de uma conta de armazenamento com [ Armazenamento georredundante padrão](../storage/storage-introduction.md#replication-for-durability-and-high-availability). Atualmente, não há suporte para o armazenamento Premium.

### Com que frequência posso replicar os dados?
* **Hyper-V:** As VMs Hyper-V que se executam no Windows Server 2012 R2 podem ser replicadas a cada 30 segundos, 5 minutos ou 15 minutos. Se configurou a replicação SAN, a replicação será síncrona.
* **VMware e servidores físicos:** A frequência de replicação não é relevante. A replicação será contínua. 

### Posso expandir a replicação de um site de recuperação existente para outro site de recuperação?
Não é suportada a replicação expandida ou em cadeia. [Envie-nos comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication/) sobre esta funcionalidade.

### Posso fazer uma replicação offline pela primeira vez que faço a replicação para o Azure?
Tal não é suportado. [Envie-nos comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from/) sobre esta funcionalidade.

### Posso excluir discos específicos na replicação?
Tal não é suportado. [Envie-nos comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6418801-exclude-disks-from-replication/) sobre esta funcionalidade.

### Posso replicar máquinas virtuais com discos dinâmicos?
Os discos dinâmicos são suportados quando replicar máquinas virtuais Hyper-V. Também são suportados quando replicar VMs VMware e máquinas físicas se estiver a utilizar a [implementação melhorada](site-recovery-vmware-to-azure-classic.md). Tenha em atenção que o disco do sistema operativo tem de ser um disco básico.

### Posso limitar a largura de banda atribuída para o tráfego de replicação do Hyper-V?
* Se está a replicar entre dois sites no local com VMs Hyper-V, pode utilizar o Windows QoS. Eis um script de exemplo: 
  
        New-NetQosPolicy -Name ASRReplication -IPDstPortMatchCondition 8084 -ThrottleRate (2048*1024)
        gpupdate.exe /force
* Se está a replicar VMs Hyper-V para o Azure, pode configurar a limitação utilizando o seguinte cmdlet do PowerShell de exemplo:
  
        Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)
* [Saiba mais](https://support.microsoft.com/en-us/kb/3056159) sobre limitação de tráfego do Hyper-V.
* [Saiba mais](site-recovery-vmware-to-azure-classic.md#capacity-planning) sobre limitação de replicação do VMware para o Azure.

## Ativação pós-falha
### Como acedo a máquinas virtuais do Azure após a ativação pós-falha para o Azure?
Pode aceder a VMs do Azure através de uma ligação de Internet segura, através de uma VPN de site para site ou através do ExpressRoute do Azure. As comunicações através de uma ligação de VPN fazem-se nas portas internas na rede Azure onde se encontra a VM. As comunicações através da Internet são mapeadas para os pontos finais públicos no serviço em nuvem do Azure para as VMs.

### Para ativação pós-falha para o Azure, como pode o Azure garantir que os meus dados são resilientes?
O Azure foi concebido para ser resiliente. A Recuperação de Sites já foi desenvolvido para ativação pós-falha para um datacenter secundário do Azure, em conformidade com o SLA do Azure, caso seja necessário. Se isto acontecer, certifique-se de que os metadados e os cofres permanecem na mesma região geográfica escolhida para o cofre. 

### Se eu estiver a replicar entre dois sites, o que acontece se o meu site primário sofre uma falha inesperada?
Pode acionar uma ativação pós-falha não planeada do site secundário. A Recuperação de Sites não necessita de conectividade do site primário para efetuar a ativação pós-falha.

### A ativação pós-falha é automática?
A ativação pós-falha não é automática. Você inicia as ativações pós-falha com um único clique no portal ou pode utilizar [Cmdlets do PowerShell da Recuperação de Sites](https://msdn.microsoft.com/library/dn850420.aspx) para acionar uma ativação pós-falha. A reativação pós-falha é uma série de ações no portal da Recuperação de Sites.

Para automatizar a ativação pós-falha, pode utilizar o Orchestrator ou o Operations Manager no local a fim de detetar uma falha de máquina virtual e, em seguida, acionar a ativação pós-falha utilizando o SDK.

## 
### Sou um fornecedor de serviços. A Recuperação de Sites funciona para modelos de infraestrutura partilhados ou dedicados?
Sim, a Recuperação de Sites suporta os modelos de infraestrutura partilhados e dedicados.

### Para os fornecedores de serviços, é a identidade do meu inquilino partilhado com o serviço de Recuperação de Sites?
Não. Os seus inquilinos não precisam de acesso ao portal de recuperação de Recuperação de Sites. Apenas o administrador do fornecedor de serviços interage com o portal.

### Os dados das aplicações dos meus inquilinos nunca irão para o Azure?
Quando há replicação entre sites pertencentes ao fornecedor de serviços, os dados das aplicações nunca chegam ao Azure. Os dados são encriptados em trânsito e replicados diretamente entre os sites do fornecedor de serviço.

Se está a replicar para o Azure, os dados da aplicação são enviados para armazenamento do Azure, mas não para o serviço de Recuperação de Sites. Os dados são encriptados em trânsito e mantém-se encriptados no Azure. 

### Os meus inquilinos receberão uma fatura de quaisquer serviços do Azure?
Não. A relação de faturação do Azure é diretamente com o fornecedor de serviços. Os fornecedores de serviços são responsáveis por gerar faturas específicas para os seus inquilinos.

### Se estiver a replicar para o Azure, necessitamos executar as máquinas virtuais no Azure todo o tempo?
Não. Os dados são replicados para uma conta de armazenamento com redundância geográfica do Azure na sua subscrição. Quando efetua uma ativação pós-falha de teste (simulação de recuperação após desastre) ou uma ativação pós-falha real, a Recuperação de Sites cria automaticamente máquinas virtuais na sua subscrição.

### Garantem o isolamento de inquilinos quando faço replicação para o Azure?
Sim.

### Quais as plataformas a que atualmente dão suporte?
Fornecemos suporte para implementações do Azure Pack, Cloud Platform System e System Center (2012 e posterior). Saiba mais sobre a integração do Azure Pack em [Configurar a proteção para máquinas virtuais](https://technet.microsoft.com/library/dn850370.aspx).

### Suportam implementações únicas de Azure Pack e de servidor VMM?
Sim, pode replicar máquinas virtuais Hyper-V e o Azure, ou entre sites do fornecedor de serviço.  Tenha em atenção que se replicar entre sites do fornecedor de serviço, a integração runbook do Azure não está disponível.

## Passos seguintes
* Leia a [Descrição geral da Recuperação de Sites](site-recovery-overview.md)
* Saiba mais sobre a [Arquitetura da Recuperação de Sites](site-recovery-components.md)  

<!--HONumber=Jun16_HO2-->


