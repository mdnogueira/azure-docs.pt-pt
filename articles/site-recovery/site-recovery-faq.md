---
title: 'O Azure Site Recovery: Perguntas mais frequentes | Microsoft Docs'
description: Este artigo aborda populares perguntas sobre o Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5cdc4bcd-b4fe-48c7-8be1-1db39bd9c078
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/19/2017
ms.author: raynew
ms.openlocfilehash: ad6f70cf9c2f420e887031c8b240d2f831e6c359
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>O Azure Site Recovery: Perguntas mais frequentes (FAQ)
Este artigo inclui perguntas mais frequentes sobre o Azure Site Recovery. Se tiver questões depois de ler este artigo, publique-los no [fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).

## <a name="general"></a>Geral
### <a name="what-does-site-recovery-do"></a>O que faz a Recuperação de Sites?
Recuperação de sites contribui para a continuidade do negócio e a estratégia de recuperação (BCDR) após desastre, através da orquestração e automatização de replicação de VMs do Azure entre regiões, máquinas virtuais no local e servidores físicos para o Azure e no local máquinas para um datacenter secundário. [Saiba mais](site-recovery-overview.md).

### <a name="what-can-site-recovery-protect"></a>O que pode proteger a recuperação de sites?
* **As VMs do Azure**: recuperação de sites pode replicar qualquer carga de trabalho em execução numa VM do Azure suportada
* **Máquinas virtuais Hyper-V**: recuperação de sites podem proteger qualquer carga de trabalho em execução numa VM de Hyper-V.
* **Servidores físicos**: recuperação de sites pode proteger servidores físicos que executem Windows ou Linux.
* **Máquinas virtuais VMware**: recuperação de sites podem proteger qualquer carga de trabalho em execução numa VMware VM.



### <a name="can-i-replicate-azure-vms"></a>Pode replicar as VMs do Azure?
Sim, pode replicar VMs do Azure suportadas entre regiões do Azure. [Saiba mais](site-recovery-azure-to-azure.md).

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>O que preciso no Hyper-V para orquestrar a replicação com a recuperação de sites?
Para o servidor de anfitrião Hyper-V, o que precisa depende do cenário de implementação. Confira os pré-requisitos do Hyper-V para:

* [Replicar VMs de Hyper-V (sem VMM) para o Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replicar VMs de Hyper-V (com o VMM) para o Azure](site-recovery-vmm-to-azure.md)
* [Replicar VMs Hyper-V para um datacenter secundário](site-recovery-vmm-to-vmm.md)
* Se está a replicar para um datacenter secundário, leia sobre [sistemas operativos convidados suportados para as VMs de Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
* Se estiver a replicar para o Azure, a recuperação de sites suporta todos os os sistemas operativos convidados que são [suportado pelo Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Posso proteger as VMs quando o Hyper-V está em execução num sistema operativo cliente?
Não, as VMs têm de estar localizadas num servidor de anfitrião Hyper-V que está a ser executado na máquina do servidor Windows suportada. Se precisar de proteger um computador cliente pode replicá-la como máquina física para [Azure](site-recovery-vmware-to-azure.md) ou um [datacenter secundário](site-recovery-vmware-to-vmware.md).

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Quais as cargas de trabalho que podem ser protegidas com a Recuperação de Sites ?
Pode utilizar a recuperação de sites para proteger a maioria das cargas de trabalho em execução num servidor físico ou VM suportado. Recuperação de sites fornece suporte para aplicações com suporte para replicação, para que as aplicações podem ser recuperadas para um Estado inteligente. Integra-se com aplicações da Microsoft, tais como o SharePoint, Exchange, Dynamics, SQL Server e Active Directory e funciona diretamente com os principais fornecedores, incluindo Oracle, SAP, IBM e Red Hat. [Saiba mais](site-recovery-workload.md) sobre proteção de carga de trabalho.

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Os anfitriões de Hyper-V têm de ser em nuvens VMM?
Se pretender replicar para um datacenter secundário, em seguida, VMs de Hyper-V tem de estar num Hyper-V aloja servidores localizados na nuvem VMM. Se pretender replicar para o Azure, pode replicar as VMs com ou sem nuvens VMM. [Leia mais](tutorial-hyper-v-to-azure.md) sobre a replicação de Hyper-V para o Azure.

### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Posso implementar a Recuperação de Sites com o VMM se tiver apenas um servidor VMM?

Sim. Pode optar por replicar as VMs nos servidores de Hyper-V na nuvem VMM para o Azure ou pode replicar entre nuvens do VMM no mesmo servidor. Para no local para replicação no local, recomendamos que tem um servidor do VMM em sites primários e secundários.  

### <a name="what-physical-servers-can-i-protect"></a>Que servidores físicos posso proteger?
Pode replicar servidores físicos a executar o Windows e Linux para o Azure ou para um site secundário. [Saiba mais sobre](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) requisitos do sistema operativo.  Os mesmos requisitos se aplica se está a replicar servidores físicos para o Azure ou para um site secundário.


Tenha em atenção que servidores físicos executarão como VMs no Azure se o servidor no local ficar inativo. Reativação pós-falha para um servidor físico no local não é atualmente suportada. Para uma máquina protegida como físico, pode apenas a reativação pós-falha para uma máquina virtual VMware.

### <a name="what-vmware-vms-can-i-protect"></a>Quais as VMs VMware que posso proteger?

Para proteger as VMs VMware precisará de um hipervisor vSphere e de máquinas virtuais que executem ferramentas do VMware. Recomendamos também que tenha um servidor VMware vCenter para gerir os hipervisores. [Saiba mais](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) sobre os requisitos exatos para replicar servidores VMware e VMs para o Azure ou para um site secundário.


### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Posso gerir a recuperação após desastre para as minhas sucursais com a Recuperação de Sites?
Sim. Quando utilizar a recuperação de sites para orquestrar a replicação e ativação pós-falha das sucursais, obterá uma orquestração unificada e a vista de todas as suas sucursais office cargas de trabalho numa localização central. Pode facilmente executar as ativações pós-falha e administrar a recuperação após desastre de todas as sucursais a partir da sede, sem visitar as sucursais.

## <a name="pricing"></a>Preços
Para perguntas relacionadas com os preços, consulte as FAQ em [preços do Azure Site Recovery](https://azure.microsoft.com/en-in/pricing/details/site-recovery/).

## <a name="security"></a>Segurança
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Os dados de replicação são enviados para o serviço de Recuperação de Sites?
Não, a recuperação de sites não interceta os dados replicados e não tem quaisquer informações sobre o que está a ser executado no seu máquinas virtuais ou servidores físicos.
Os dados de replicação são trocados entre os anfitriões Hyper-V no local, os hipervisores VMware ou os servidores físicos, e o armazenamento do Azure ou o seu site secundário. A Recuperação de Sites não tem capacidade para intercetar esses dados. Apenas os metadados necessários para orquestrar a replicação e a ativação pós-falha são enviados para o serviço de Recuperação de Sites.  

Recuperação de sites é ISO 27001: 2013, 27018, HIPAA, DPA e está no processo SOC2 e FedRAMP JAB.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Por motivos de conformidade, mesmo nosso metadados no local têm de permanecer na mesma região geográfica. Permite a recuperação de sites ajudar-nos?
Sim. Quando criar um cofre de recuperação de sites numa região, podemos assegurar que todos os metadados que é necessário ativar e orquestrar a replicação e ativação pós-falha permanece dentro dessa região 's geográfica limites.

### <a name="does-site-recovery-encrypt-replication"></a>A Recuperação de Sites faz encriptação de replicação?
Para máquinas virtuais e servidores físicos, replicar no local sites encriptação em trânsito é suportada. Para máquinas virtuais e servidores físicos a replicar para o Azure, ambos os encriptação em trânsito e [encriptação em rest (no Azure)](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption) são suportados.

## <a name="replication"></a>Replicação

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Pode replicar através de uma VPN de site a site para o Azure?
O Azure Site Recovery replica os dados para uma conta de armazenamento do Azure, ao longo de um ponto final público. Não é a replicação através de uma VPN de site para site. Pode criar uma VPN de site a site, com uma rede virtual do Azure. Isto não interfere com a replicação do Site Recovery.

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Pode utilizar ExpressRoute para replicar máquinas virtuais para o Azure?
Sim, ExpressRoute pode ser utilizado para replicar máquinas virtuais no Azure. O Azure Site Recovery replica os dados para uma conta de armazenamento do Azure através de um ponto final público. Terá de configurar [peering público](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) para utilizar o ExpressRoute para replicação de recuperação de sites. Depois das máquinas virtuais ter sido a ativação pós-falha para uma rede virtual do Azure possa aceder aos mesmos utilizando o [peering privado](../expressroute/expressroute-circuit-peerings.md#azure-private-peering) configuração com a rede virtual do Azure.

### <a name="are-there-any-prerequisites-for-replicating-virtual-machines-to-azure"></a>Existem pré-requisitos para replicar máquinas virtuais para o Azure?
Máquinas virtuais que pretende replicar para o Azure devem estar em conformidade com [requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

A conta de utilizador do Azure tem de ter determinados [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para ativar a replicação de uma nova máquina virtual para o Azure.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Pode replicar máquinas virtuais de 2.ª geração do Hyper-V para o Azure?
Sim. Recuperação de sites converte de geração 2 para a geração 1 durante a ativação pós-falha. Na reativação pós-falha, a máquina é convertida novamente para a geração 2. [Leia mais](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### <a name="if-i-replicate-to-azure-how-do-i-pay-for-azure-vms"></a>Se replicar para o Azure como posso pagar as VMs do Azure?
Durante a replicação normal, os dados são replicados para o armazenamento do Azure com redundância geográfica e não é necessário pagar quaisquer custos de máquina virtual IaaS do Azure, fornecendo das vantagens significativa. Quando executar uma ativação pós-falha para o Azure, a Recuperação de Sites cria automaticamente máquinas virtuais de IaaS do Azure e, depois disso, serão cobrados os recursos de computação que consumir no Azure.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Pode automatizar os cenários de recuperação de sites com um SDK?
Sim. Pode automatizar fluxos de trabalho da Recuperação de Sites utilizando a API REST, PowerShell ou o SDK do Azure. Atualmente, suportado cenários para implementar a recuperação de sites com o PowerShell:

* [Replicar VMs de Hyper-V em nuvens VMMs para o Gestor de recursos do Azure PowerShell](site-recovery-vmm-to-azure-powershell-resource-manager.md)
* [Replicar VMs de Hyper-V sem o VMM para o Gestor de recursos do Azure PowerShell](site-recovery-deploy-with-powershell-resource-manager.md)

### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-do-i-need"></a>Se eu replicar para o Azure qual o tipo de conta de armazenamento de que preciso?
Necessita de uma conta de armazenamento LRS ou GRS. Recomendamos GRS para que os dados sejam resilientes se ocorrer uma falha regional ou se a região primária não pode ser recuperada. A conta tem de ser na mesma região que o cofre de Serviços de Recuperação. Armazenamento Premium é suportado para a VM de VMware, VM do Hyper-V e replicação do servidor físico, quando implementar a recuperação de sites no portal do Azure.

### <a name="how-often-can-i-replicate-data"></a>Com que frequência posso replicar os dados?
* **Hyper-v:** VMs de Hyper-V podem ser replicadas cada 30 segundos (exceto para o armazenamento premium), 5 minutos ou 15 minutos. Se configurou a replicação SAN replicação será síncrona.
* **VMware e servidores físicos:** A frequência de replicação não é relevante. A replicação está contínua.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Pode expandir a replicação do site de recuperação existente para outro site terciária?
Não é suportada a replicação expandida ou em cadeia. Pedir esta funcionalidade no [fórum de comentários](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Posso fazer uma replicação offline pela primeira vez que faço a replicação para o Azure?
Tal não é suportado. Esta funcionalidade do pedido de [fórum de comentários](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Posso excluir discos específicos na replicação?
Isto é suportado quando estiver [replicar VMware VMs e VMs de Hyper-V](site-recovery-exclude-disk.md) para o Azure, utilizando o portal do Azure.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Pode replicar máquinas virtuais com discos dinâmicos?
Os discos dinâmicos são suportados quando replicar máquinas virtuais Hyper-V. Também são suportados quando replicar VMs de VMware e máquinas físicas para o Azure. O disco do sistema operativo tem de ser um disco básico.

### <a name="can-i-add-a-new-machine-to-an-existing-replication-group"></a>Pode adicionar uma nova máquina a um grupo de replicação existente?
A adição de novas máquinas para grupos de replicação existente é suportada. Para tal, selecione o grupo de replicação (a partir do painel de 'Itens replicados') e o botão direito do rato clique/selecione o menu de contexto no grupo de replicação e selecione a opção adequada.

![Adicionar ao grupo de replicação](./media/site-recovery-faq/add-server-replication-group.png)

### <a name="can-i-throttle-bandwidth-allotted-for-hyper-v-replication-traffic"></a>Posso limitar a largura de banda atribuída para o tráfego de replicação do Hyper-V?
Sim. Pode ler mais sobre limitação de largura de banda em artigos de implementação:

* [Planeamento da capacidade para replicar VMware VMs e servidores físicos](site-recovery-plan-capacity-vmware.md)
* [Planeamento da capacidade para replicar VMs Hyper-V para o Azure](site-recovery-capacity-planning-for-hyper-v-replication.md)

## <a name="failover"></a>Ativação pós-falha
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-virtual-machines-after-failover"></a>Se estiver a efetuar ativação pós-falha do Azure, como acedo as máquinas virtuais do Azure após a ativação pós-falha?
Pode aceder a VMs do Azure através de uma ligação de Internet segura, através de uma VPN de site para site ou através do ExpressRoute do Azure. Terá de preparar um número de elementos para se ligar. [Saiba mais](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Se posso efetuar uma ativação pós-falha para o Azure como o Azure garantir que os meus dados são resilientes?
O Azure foi concebido para ser resiliente. Recuperação de sites já foi desenvolvida para ativação pós-falha para um datacenter secundário do Azure, de acordo com o SLA do Azure caso seja necessário. Se isto acontecer, iremos certificar os seus metadados e cofres permanecem na mesma região geográfica que escolheu para o cofre.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Se estiver a replicar entre dois centros de dados, o que acontece se o meu datacenter primário sofre uma falha inesperada?
Pode acionar uma ativação pós-falha não planeada do site secundário. A Recuperação de Sites não necessita de conectividade do site primário para efetuar a ativação pós-falha.

### <a name="is-failover-automatic"></a>A ativação pós-falha é automática?
A ativação pós-falha não é automática. Iniciar as ativações pós-falha com único clique no portal ou pode utilizar [PowerShell da recuperação de Site](/powershell/module/azurerm.siterecovery) para acionar uma ativação pós-falha. Voltar a falhar é uma ação simple no portal de recuperação de sites.

Para automatizar a foi utilizar o Orchestrator no local ou o Operations Manager para detetar uma falha de máquina virtual e, em seguida, acionar a ativação pós-falha utilizando o SDK.

* [Leia mais](site-recovery-create-recovery-plans.md) sobre os planos de recuperação.
* [Leia mais](site-recovery-failover.md) sobre a ativação pós-falha.
* [Leia mais](site-recovery-failback-azure-to-vmware.md) sobre falha fazer uma cópia de VMs de VMware e servidores físicos

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-failover-back-to-a-different-host"></a>Se a minha anfitrião no local não está a responder ou falhados, posso ativação pós-falha para outro anfitrião?
Sim, pode utilizar a recuperação para localização alternativa para reativação pós-falha para um anfitrião diferente a partir do Azure. Saiba mais sobre as opções de abaixo as ligações para máquinas virtuais VMware e Hyper-v.

* [Máquinas virtuais VMware](site-recovery-how-to-failback-azure-to-vmware.md#fail-back-to-the-original-or-alternate-location)
* [Máquinas virtuais do Hyper-v](site-recovery-failback-from-azure-to-hyper-v.md#failback-to-an-alternate-location)

## <a name="service-providers"></a>Fornecedores de serviços
### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Sou um fornecedor de serviços. Recuperação de sites funciona para modelos de infraestrutura partilhados e dedicados?
Sim, a Recuperação de Sites suporta os modelos de infraestrutura partilhados e dedicados.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Para um fornecedor de serviços, é a identidade do meu inquilino partilhado com o serviço de recuperação de sites?
Não. Identidade do inquilino permanece anónima. Os seus inquilinos não precisam de acesso ao portal de recuperação de Recuperação de Sites. Apenas o administrador do fornecedor de serviços interage com o portal.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Dados de aplicação do inquilino nunca passará para o Azure?
Quando há replicação entre sites pertencentes ao fornecedor de serviços, os dados das aplicações nunca chegam ao Azure. Os dados são encriptados em trânsito e replicados diretamente entre os sites do fornecedor de serviço.

Se está a replicar para o Azure, os dados da aplicação são enviados para armazenamento do Azure, mas não para o serviço de Recuperação de Sites. Dados são encriptado em trânsito e permanecem encriptados no Azure.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Os meus inquilinos receberão uma fatura de quaisquer serviços do Azure?
Não. A relação de faturação do Azure é diretamente com o fornecedor de serviços. Os fornecedores de serviços são responsáveis por gerar faturas específicas para os seus inquilinos.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Se estiver a replicar para o Azure, necessitamos executar as máquinas virtuais no Azure todo o tempo?
Não, os dados são replicados para uma conta de armazenamento do Azure na sua subscrição. Quando efetua uma ativação pós-falha de teste (simulação de recuperação após desastre) ou uma ativação pós-falha real, a Recuperação de Sites cria automaticamente máquinas virtuais na sua subscrição.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Garantem o isolamento de inquilinos quando faço replicação para o Azure?
Sim.

### <a name="what-platforms-do-you-currently-support"></a>Quais as plataformas a que atualmente dão suporte?
Suportamos Azure Pack, Cloud Platform System, e o System Center em implementações (2012 e posterior). [Saiba mais](https://technet.microsoft.com/library/dn850370.aspx) sobre a integração do Azure Pack e a recuperação de sites.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Suportam implementações únicas de Azure Pack e de servidor VMM?
Sim, pode replicar máquinas virtuais Hyper-V para o Azure, ou entre sites do fornecedor de serviço.  Tenha em atenção que se replicar entre sites do fornecedor de serviço, a integração runbook do Azure não está disponível.

## <a name="next-steps"></a>Passos seguintes
* Leia a [Descrição geral da Recuperação de Sites](site-recovery-overview.md)
* Saiba mais sobre a [Arquitetura da Recuperação de Sites](site-recovery-components.md)  
