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
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/05/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 94250becb238adad7983d4b45939eb988411fca3


---
# <a name="how-does-azure-site-recovery-work"></a>Como funciona o Azure Site Recovery?
Leia este artigo para compreender a arquitetura subjacente do Azure Site Recovery e os componentes que fazem que funcione.

Publique comentários ou perguntas na parte inferior deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Descrição geral
As organizações necessitam de uma estratégia BCDR que determine como as aplicações, cargas de trabalho e dados continuam em execução e disponíveis durante o período de indisponibilidade planeado e imprevisto, e como retomar as condições de trabalho normais com a maior brevidade possível. A estratégia de BCDR deve manter os dados de negócio seguros e recuperáveis, e garantir que as cargas de trabalho continuam a estar continuamente disponíveis quando ocorrer um desastre.

A Recuperação de Sites é um serviço Azure que contribui para a sua estratégia BCDR através da orquestração da replicação dos servidores físicos no local e das máquinas virtuais para a nuvem (Azure) ou para um datacenter secundário. Quando ocorrem falhas na sua localização principal, faz-se a ativação pós-falha para a localização secundária para manter disponíveis as aplicações e cargas de trabalho. A localização principal é reativada quando se retomam as operações normais. Saiba mais em [O que é a Recuperação de Sites?](site-recovery-overview.md)

## <a name="site-recovery-in-the-azure-portal"></a>Recuperação de Sites no Portal do Azure
O Azure tem dois [modelos de implementação](../resource-manager-deployment-model.md) diferentes para criar e trabalhar com recursos: o modelo Azure Resource Manager e o modelo clássico de gestão de serviços. O Azure também tem dois portais: o [Portal Clássico do Azure](https://manage.windowsazure.com/), que suporta o modelo de implementação clássica, e o [Portal do Azure](https://portal.azure.com), com suporte para ambos os modelos de implementação.

A Recuperação de Sites está disponível no Portal Clássico e no Portal do Azure. No Portal Clássico do Azure, pode suportar a Recuperação de Sites com o modelo clássico de gestão de serviços. No Portal do Azure, pode suportar as implementações do modelo clássico ou do Modelo de Recursos. [Saiba mais](site-recovery-overview.md#site-recovery-in-the-azure-portal) sobre a implementação com o Portal do Azure.

As informações contidas neste artigo aplicam-se às implementações do Portal Clássico do Azul e do Portal do Azure. As diferenças são assinaladas quando aplicável.

## <a name="deployment-scenarios"></a>Cenários de implementação
A Recuperação de Sites pode ser implementada para orquestrar a replicação em vários cenários:

* **Replicar máquinas virtuais VMware**: pode replicar máquinas virtuais VMware no local no Azure ou num datacenter secundário.
* * **Replicar máquinas físicas**: pode replicar máquinas físicas que executem o Windows ou Linux no Azure ou num datacenter secundário. O processo para replicar máquinas físicas é quase o mesmo que o processo para replicar VMs de VMware
* **Replicar VMs Hyper-V (sem VMM)**: pode replicar VMs Hyper-V que não são geridas pelo VMM no Azure.
* **Replicar VMs de Hyper-V geridas em nuvens do System Center VMM**: pode replicar máquinas virtuais do Hyper-V no local executadas em servidores de anfitrião Hyper-V em nuvens do VMM no Azure ou num datacenter secundário. Pode utilizar a Réplica do Hyper-V padrão ou a replicação SAN para replicar.
* **Migrar VMs**: pode utilizar a Recuperação de Sites para [migrar VMs IaaS do Azure](site-recovery-migrate-azure-to-azure.md) entre regiões ou para [migrar instâncias de Windows em AWS ](site-recovery-migrate-aws-to-azure.md) para VMs IaaS do Azure. Atualmente, só há suporte para a migração, o que significa essas VMs podem efetuar uma ativação pós-falha mas não podem ser reativadas.

A Recuperação de Sites pode replicar a maioria das aplicações em execução nessas VMs e servidores físicos. Em [Que cargas de trabalho pode o Azure Site Recovery proteger?](site-recovery-workload.md) pode obter um resumo completo das aplicações suportadas.

## <a name="replicate-to-azure-vmware-virtual-machines-or-physical-windowslinux-servers"></a>Replicar para o Azure: máquinas virtuais VMware ou servidores físicos do Windows/Linux
Existem duas formas de replicar VMs de VMware com a Recuperação de Sites.

* **Com o portal do Azure** – Quando implementa a Recuperação de Sites no Portal do Azure, pode efetuar uma ativação pós-falha das VMs para o armazenamento de gestor do serviço clássico ou para o Resource Manager. Replicar VMs de VMware no Portal do Azure proporciona várias vantagens, incluindo a capacidade de replicar para o armazenamento clássico ou do Resource Manager no Azure. [Saiba mais](site-recovery-vmware-to-azure.md).
* **Com o portal clássico** – Pode implementar a Recuperação de Sites no portal clássico através de uma experiência melhorada. Esta ação deverá ser a opção utilizada para todas as novas implementações no portal clássico. Nesta implementação, apenas pode proceder à ativação pós-falha de VMs para o armazenamento clássico no Azure e não para o armazenamento do Resource Manager. [Saiba mais](site-recovery-vmware-to-azure-classic.md). Também existe uma [experimentação legada](site-recovery-vmware-to-azure-classic-legacy.md) para configurar a replicação de VMware no portal clássico. Esta opção não deverá ser utilizada para novas implementações.  Se já tiver implementado através da experimentação legada, [saiba mais sobre a migração](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment) para a implementação melhorada.

Os requisitos de arquitetura para implementar a Recuperação de Sites para a replicação de VMs de VMware/servidores físicos no Portal do Azure ou no Portal Clássico do Azure (melhorado) são semelhantes, embora com algumas diferenças:

* Se implementar no Portal do Azure, pode replicar para o armazenamento baseado no Resource Manager e utilizar as redes do Resource Manager para ligar as VMs do Azure após a ativação pós-falha.
* Quando implementar no Portal do Azure, são suportados ambos os armazenamentos LRS e GRS. O portal clássico requer o GRS.
* O processo de implementação é simplificado e mais intuitivo no Portal do Azure.

Eis o que necessita:

* **Conta do Azure**: precisará de uma conta do Microsoft Azure.
* **Armazenamento do Azure**: precisará de uma conta de armazenamento do Azure para armazenar dados replicados. Pode utilizar uma conta clássica ou uma conta de armazenamento do Resource Manager. A conta pode ser LRS ou GRS quando implementar no Portal do Azure. Os dados replicados são guardados no armazenamento do Azure e as VMs do Azure são aceleradas quando ocorre a ativação pós-falha.
* **Rede do Azure**: precisará de uma rede virtual do Azure à qual se ligarão as VMs quando ocorrer a ativação pós-falha. No Portal do Azure, podem ser redes criadas com base no modelo de gestor de serviço clássico ou no modelo do Resource Manager.
* **Servidor de configuração no local**: irá precisar de uma máquina com o Windows Server 2012 R2 no local, que executa o servidor de configuração e outros componentes da Recuperação de Sites. Se estiver a replicar VMs de VMware, deverá tratar-se de uma VM de VMware altamente disponível. Se pretender replicar servidores físicos, a máquina pode ser física. Os seguintes componentes da Recuperação de Sites serão instalados na máquina:
  * **Servidor de configuração**: coordena a comunicação entre o seu ambiente no local e o Azure e gere a replicação e recuperação de dados.
  * **Servidor de processo**: atua como um gateway de replicação. Recebe dados de replicação de máquinas de origem protegida, otimiza-as com colocação em cache, compressão e encriptação, e envia os dados para o armazenamento do Azure. Também processa a instalação de inserção do serviço de mobilidade para máquinas protegidas e efetua a deteção automática de VMs de VMware. À medida que cresça a implementação é possível adicionar mais servidores de processos dedicados independentes para processar um crescente volume de tráfego de replicação.
  * **Servidor de destino principal**: processa dados de replicação durante a reativação pós-falha a partir do Azure.
* **VMs de VMware ou servidores físicos a replicar**: cada máquina que quiser replicar no Azure precisará de ter o componente Serviço de mobilidade instalado. Este serviço obtém dados, escreve na máquina e reencaminha-os para o servidor de processos. Este componente pode ser instalado manualmente ou pode ser inserido e instalado automaticamente pelo servidor de processos ao ativar a replicação para uma máquina.
* **Anfitriões vSPhere/servidor vCenter**: precisará de um ou mais servidores anfitriões vSPhere a executar as VMs de VMware. Recomendamos que implemente um servidor vCenter para gerir esses anfitriões.
* **Reativação pós-falha**: eis o que necessita:
  * **Reativação pós-falha físico para físico não suportada**: tal significa que, se efetuar uma ativação pós-falha dos servidores físico para o Azure e pretender de seguida proceder a uma reativação pós-falha, a reativação pós-falha terá de ser numa VM de VMware. Não pode efetuar a reativação pós-falha num servidor físico. Para a reativação pós-falha, irá precisar de uma VM do Azure e, caso não tenha implementado o servidor de configuração como uma VM de VMware, terá de configurar um servidor de destino principal separado como uma VM de VMware. Esta ação é necessária uma vez que o servidor de destino principal interage e estabelece a ligação com o armazenamento do VMware para restaurar os discos para uma VM de VMware.
  * * **Servidor de processo temporário no Azure**: se pretender uma reativação a partir do Azure após a ativação pós-falha, terá de configurar uma VM do Azure como servidor de processos, para lidar com a replicação a partir do Azure. É possível eliminar esta VM após a conclusão da reativação pós-falha.
  * **Ligação VPN**: para a reativação pós-falha, precisará de uma ligação VPN (ou Azure ExpressRoute) configurada a partir da rede Azure para o site no local.
  * **Servidor de destino principal no local independente**: o servidor de destino principal no local processa a reativação pós-falha. O servidor de destino principal está instalado por predefinição no servidor de gestão, mas se está a reativar maiores volumes de tráfego deve configurar um servidor de destino principal independente no local para esta finalidade.

**Arquitetura geral**

![Melhorada](./media/site-recovery-components/arch-enhanced.png)

**Componentes de implementação**

![Melhorada](./media/site-recovery-components/arch-enhanced2.png)

**Reativação pós-falha**

![Reativação pós-falha melhorada](./media/site-recovery-components/enhanced-failback.png)

* [Saiba mais](site-recovery-vmware-to-azure.md#azure-prerequisites) sobre os requisitos de implementação no Portal do Azure.
* [Saiba mais](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) sobre os requisitos de implementação melhorada no portal clássico.
* [Saiba mais](site-recovery-failback-azure-to-vmware.md) sobre a reativação pós-falha no Portal do Azure.
* [Saiba mais](site-recovery-failback-azure-to-vmware-classic.md) sobre a reativação pós-falha no portal clássico.

## <a name="replicate-to-azure-hyperv-vms-not-managed-by-vmm"></a>Replicar para o Azure: VMs de Hyper-V não geridas pelo VMM
Pode replicar VMs de Hyper-V que não são geridas pelo System Center VMM para o Azure com a Recuperação de Sites ao proceder da seguinte forma:

* **Utilizar o Portal do Azure** – Quando implementar a Recuperação de Sites no Portal do Azure, pode efetuar uma ativação pós-falha das VMs para o armazenamento clássico ou o Resource Manager. [Saiba mais](site-recovery-hyper-v-site-to-azure.md).
* **Utilizar o portal clássico** – Pode implementar a Recuperação de Sites no portal clássico. Nesta implementação, apenas pode proceder à ativação pós-falha de VMs para o armazenamento clássico no Azure e não para o armazenamento do Resource Manager. [Saiba mais](site-recovery-hyper-v-site-to-azure-classic.md).

A arquitetura para ambas as implementações é semelhante, à exceção de:

* Se implementar no Portal do Azure, pode replicar para o armazenamento do Resource Manager e utilizar as redes do Resource Manager para ligar as VMs do Azure após a ativação pós-falha.
* O processo de implementação é simplificado e mais intuitivo no Portal do Azure.

Eis o que necessita:

* **Conta do Azure**: precisará de uma conta do Microsoft Azure.
* **Armazenamento do Azure**: precisará de uma conta de armazenamento do Azure para armazenar dados replicados. No Portal do Azure, pode utilizar uma conta clássica ou uma conta de armazenamento do Resource Manager. No portal clássico, apenas pode utilizar uma conta clássica. Os dados replicados são guardados no armazenamento do Azure e as VMs do Azure são criadas quando ocorre a ativação pós-falha.
* **Rede do Azure**: precisará de uma rede do Azure à qual se ligarão as VMs do Azure quando ocorrer a ativação pós-falha.
* **Anfitrião Hyper-V**: irá precisar de um ou mais servidores anfitriões Hyper-V do Windows Server 2012 R2. Durante a implementação do Site Recovery, irá instalar o Azure Site Recovery Provider e o agente do Serviços de Recuperação do Microsoft Azure no anfitrião.
* **VMs de Hyper-V**: irá precisar de uma ou mais VMs no servidor anfitrião Hyper-V. Fornecedor do Azure Site Recovery e agente dos Serviços de Recuperação do Azure nos anfitriões Hyper-V durante a implementação da Recuperação de Sites. O Fornecedor coordena e orquestra a replicação com o serviço de Recuperação de Sites através da Internet. O agente processa os dados de replicação de dados através de HTTPS 443. As comunicações provenientes do Fornecedor e do agente são seguras e encriptadas. Também são encriptados os dados replicados no armazenamento do Azure.

**Arquitetura geral**

![Site Hyper-V para o Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

* [Saiba mais](site-recovery-hyper-v-site-to-azure.md#azure-prerequisites) sobre os requisitos de implementação no Portal do Azure.
* [Saiba mais](site-recovery-hyper-v-site-to-azure-classic.md#azure-prerequisites) sobre os requisitos para a implementação no portal clássico.

## <a name="replicate-to-azure-hyperv-vms-managed-by-vmm"></a>Replicar para o Azure: VMs de Hyper-V geridas pelo VMM
Pode replicar VMs de Hyper-V nas nuvens do VMM para o Azure com a Recuperação de Sites ao proceder da seguinte forma:

* **Utilizar o Portal do Azure** – Quando implementar a Recuperação de Sites no Portal do Azure, pode efetuar uma ativação pós-falha das VMs para o armazenamento clássico ou o Resource Manager. [Saiba mais](site-recovery-vmm-to-azure.md).
* **Utilizar o portal clássico** – Pode implementar a Recuperação de Sites no portal clássico. Nesta implementação, apenas pode proceder à ativação pós-falha de VMs para o armazenamento clássico no Azure e não para o armazenamento do Resource Manager. [Saiba mais](site-recovery-vmm-to-azure-classic.md).

A arquitetura para ambas as implementações é semelhante, à exceção de:

* Se implementar no Portal do Azure, pode replicar para o armazenamento baseado no Resource Manager e utilizar as redes do Resource Manager para ligar as VMs do Azure após a ativação pós-falha.
* O processo de implementação é simplificado e mais intuitivo no Portal do Azure.

Eis o que necessita:

* **Conta do Azure**: precisará de uma conta do Microsoft Azure.
* **Armazenamento do Azure**: precisará de uma conta de armazenamento do Azure para armazenar dados replicados. No Portal do Azure, pode utilizar uma conta clássica ou uma conta de armazenamento do Resource Manager. No portal clássico, apenas pode utilizar uma conta clássica. Os dados replicados são guardados no armazenamento do Azure e as VMs do Azure são criadas quando ocorre a ativação pós-falha.
* **Rede do Azure**: terá de configurar o mapeamento de rede para ligar as VMs do Azure às redes adequadas depois da ativação pós-falha.
* **Servidor VMM**: irá precisar de um ou mais servidores VMM no local em execução no System Center 2012 R2 e configurados com uma ou várias nuvens privadas. Se estiver a implementar no Portal do Azure, irá precisar de redes lógicas e VM configuradas de modo a poder configurar o mapeamento da rede. No portal clássico, esta ação é opcional.  Uma rede VM deve ser ligada a uma rede lógica que está associada à nuvem.
* **Anfitrião Hyper-V**: irá precisar de um ou mais servidores anfitriões Hyper-V do Windows Server 2012 R2 na nuvem VMM.
* **VMs de Hyper-V**: irá precisar de uma ou mais VMs no servidor anfitrião Hyper-V.

**Arquitetura geral**

![VMM para o Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

* [Saiba mais](site-recovery-vmm-to-azure.md#azure-requirements) sobre os requisitos de implementação no Portal do Azure.
* [Saiba mais](site-recovery-vmm-to-azure-classic.md#before-you-start) sobre os requisitos para a implementação no portal clássico.

## <a name="replicate-to-a-secondary-site-vmware-virtual-machines-or-physical-servers"></a>Replicar num site secundário: máquinas virtuais VMware ou servidores físicos
Para replicar as VMs de VMware ou os servidores físicos para um site secundário, transfira o InMage Scout que está incluído na subscrição do Azure Site Recovery. Pode transferi-lo a partir do Portal do Azure ou do Portal Clássico do Azure.

Configure servidores de componentes em cada site (configuração, processo, destino principal) e instale o agente Unified nas máquinas que pretende replicar. Após a replicação inicial, o agente em cada máquina envia alterações de replicação delta para o servidor de processos. O servidor de processos otimiza os dados e transfere-o para o servidor de destino principal no site secundário. O servidor de configuração gere o processo de replicação.

Eis o que necessita:

**Conta do Azure**: implementa este cenário através do InMage Scout. Para obtê-lo,precisará de uma subscrição do Azure. Depois de criar um cofre de Recuperação de Sites, transfere o InMage Scout e instala as atualizações mais recentes para configurar a implementação.
**Servidor de processos (site primário)**: configure o componente de servidor de processos no seu site primário para lidar com a colocação em cache, a compressão e a otimização de dados. Ele também faz a instalação por push do Agente Unified nas máquinas que pretende proteger.
**VMware ESX/ESXi e servidor vCenter (site primário)**: se estiver a proteger as VMs de VMware, precisará de um hipervisor VMware EXS/ESXi e, opcionalmente, de um servidor VMware vCenter para gerir os hipervisores.

* **VMs/servidores físicos (site primário)**: as VMs de VMware ou os servidores físicos Windows/Linux que pretende proteger precisam de ter instalado o Agente Unified. O Agente Unified também é instalado nas máquinas que atuam como servidor de destino principal. O agente atua como um fornecedor de comunicação entre todos os componentes.
* * **Servidor de configuração (site secundário)**: o servidor de configuração é o primeiro componente que instala e é instalado no site secundário para gerir, configurar e monitorizar a implementação com o Web site de gestão ou a consola de vContinuum. Existe apenas um único servidor de configuração numa implementação e tem de ser instalado num computador que execute o Windows Server 2012 R2.
* **Servidor vContinuum (site secundário)**: está instalado na mesma localização (site secundário) que o servidor de configuração. Fornece uma consola de gestão e monitorização do seu ambiente protegido. Numa instalação predefinida, o servidor vContinuum é o primeiro servidor de destino principal e tem o Agente Unified instalado.
* **Servidor de destino principal (site secundário)**: o servidor de destino principal contém dados replicados. Recebe dados do servidor de processos, cria uma máquina de réplica no site secundário e detém os pontos de retenção de dados. O número de servidores de destino principal de que precisa, depende do número de máquinas que estiver a proteger. Se pretender que haja reativação para o site primário, precisará também que aí exista um servidor de destino principal.

**Arquitetura geral**

![VMware para VMware](./media/site-recovery-components/vmware-to-vmware.png)

## <a name="replicate-to-a-secondary-site-hyperv-vms-managed-by-vmm"></a>Replicar para um site secundário: VMs Hyper-V geridas pelo VMM
Pode replicar VMs de Hyper-V geridas pelo System Center VMM para um datacenter secundário com a Recuperação de Sites da seguinte forma:

* **Utilizar o Portal do Azure** – Quando implementar a Recuperação de Sites no portal do Azure. [Saiba mais](site-recovery-hyper-v-site-to-azure.md).
* **Utilizar o portal clássico** – Pode implementar a Recuperação de Sites no portal clássico. [Saiba mais](site-recovery-hyper-v-site-to-azure-classic.md).

A arquitetura para ambas as implementações é semelhante, à exceção de:

* Se implementar no Portal do Azure, terá de configurar o mapeamento da rede. No portal clássico, esta ação é opcional.
* O processo de implementação é simplificado e mais intuitivo no Portal do Azure.
* * Se implementar no Portal Clássico do Azure, o [mapeamento de armazenamento](site-recovery-storage-mapping.md) está disponível.

Eis o que necessita:

* **Conta do Azure**: precisará de uma conta do Microsoft Azure.
* **Servidor VMM**: recomenda-se um servidor VMM no site primário e um no site secundário, cada um possuindo pelo menos uma nuvem privada VMM. O servidor deve estar a executar, pelo menos, o System Center 2012 SP1 com as atualizações mais recentes e estar ligado à Internet. As nuvens devem ter o conjunto de perfis de capacidade Hyper-V. Instale o Fornecedor do Azure Site Recovery no servidor VMM. O Fornecedor coordena e orquestra a replicação com o serviço de Recuperação de Sites através da Internet. As comunicações entre o Fornecedor e o Azure são seguras e encriptadas.
* **Servidor Hyper-V**: os servidores anfitriões Hyper-V têm de estar localizados em nuvens VMM primárias e secundárias. Os servidores anfitrião devem estar a executar, pelo menos, o Windows Server 2012 com as atualizações mais recentes instaladas e ligado à Internet. Os dados são replicados entre os servidores anfitrião primário e secundário Hyper-V sobre LAN ou VPN utilizando o Kerberos ou autenticação de certificados.  
* **Computadores protegidos**: o servidor de anfitrião Hyper-V de origem deve ter pelo menos uma VM para proteger.

**Arquitetura geral**

![Local para local](./media/site-recovery-components/arch-onprem-onprem.png)

* [Saiba mais](site-recovery-vmm-to-vmm.md#azure-prerequisites) sobre os requisitos de implementação no Portal do Azure.
* * [Saiba mais](site-recovery-vmm-to-vmm-classic.md#before-you-start) sobre os requisitos de implementação no Portal Clássico do Azure.

## <a name="replicate-to-a-secondary-site-with-san-replication-hyperv-vms-managed-by-vmm"></a>Replicar para um site secundário com uma replicação SAN: VMs de Hyper-V geridas pelo VMM
Pode replicar VMs de Hyper-V geridas nas nuvens VMM para um site secundário através da replicação SAN com o Portal Clássico do Azure. Este cenário não é atualmente suportado no novo Portal do Azure.

Para implementar este cenário, durante a implementação do Azure Site Recovery, instalará o Fornecedor do Azure Site Recovery nos servidores VMM. O Fornecedor coordena e orquestra a replicação com o serviço de Recuperação de Sites através da Internet. Os dados são replicados entre as matrizes de armazenamento primário e secundário utilizando a replicação SAN síncrona.

Eis o que necessita:

**Conta do Azure**: irá precisar de uma subscrição do Azure

* **Matriz SAN**: uma [matriz SAN suportada](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) gerida pelo servidor VMM principal. O SAN partilha uma infraestrutura de rede com outra matriz SAN no site secundário.
* **Servidor VMM**: recomenda-se um servidor VMM no site primário e um no site secundário, cada um possuindo pelo menos uma nuvem privada VMM. O servidor deve estar a executar, pelo menos, o System Center 2012 SP1 com as atualizações mais recentes e estar ligado à Internet. As nuvens devem ter o conjunto de perfis de capacidade Hyper-V.
* **Servidor Hyper-V**: os servidores anfitriões Hyper-V localizados em nuvens VMM primárias e secundárias. Os servidores anfitrião devem estar a executar, pelo menos, o Windows Server 2012 com as atualizações mais recentes instaladas e ligado à Internet.
* **Computadores protegidos**: o servidor de anfitrião Hyper-V de origem deve ter pelo menos uma VM para proteger.

**Arquitetura da replicação SAN**

![Replicação SAN](./media/site-recovery-components/arch-onprem-onprem-san.png)

[Saiba mais](site-recovery-vmm-san.md#before-you-start) sobre os requisitos de implementação.

### <a name="onpremises"></a>Local
## <a name="hyperv-protection-lifecycle"></a>Ciclo de vida de proteção do Hyper-V
Este fluxo de trabalho mostra o processo para proteger, replicar e efetuar a ativação pós-falha de máquinas virtuais Hyper-V.

1. **Ativar proteção**: configure o cofre de Recuperação de Sites, configure as definições de replicação para uma nuvem VMM ou para o site Hyper-V, e ative a proteção para as VMs. É iniciada uma tarefa denominada **Ativar Proteção** que pode ser monitorizada no separador **Tarefas**. A tarefa verifica se a máquina está em conformidade com os pré-requisitos e, em seguida, invoca o método [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) que configura a replicação para o Azure com as definições que configurou. A tarefa **Ativar proteção** também invoca o método [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) para inicializar uma replicação completa de VM.
2. **Replicação inicial**: é obtido um instantâneo da máquina virtual e os discos rígidos virtuais são replicados um de cada vez até estarem todos copiados para o Azure ou para o datacenter secundário. O tempo necessário para concluir isto depende do tamanho da VM, largura de banda da rede e do método de replicação inicial. Se ocorrerem alterações de disco enquanto a replicação inicial está em curso, o Controlador de Replicação de Réplica do Hyper-V controla essas alterações como Registos de Replicação do Hyper-V (.hrl) que se encontram na mesma pasta que os discos. Cada disco tem um ficheiro de .hrl associado que será enviado para o armazenamento secundário. Tenha em atenção que os ficheiros de instantâneo e de registo consomem recursos do disco quando a replicação inicial está em curso. Quando a replicação inicial está concluída, o instantâneo VM é eliminado e as alterações de disco delta no registo são sincronizadas e intercaladas.
3. **Finalizar proteção**: após a conclusão da replicação inicial, a tarefa **Finalizar proteção** configura a rede e outras definições de pós-replicação para que a máquina virtual fique protegida. Se está a replicar para o Azure, poderá ter de otimizar as definições para a máquina virtual para que fique preparada para a ativação pós-falha. Nesta altura, pode executar uma ativação pós-falha de teste para verificar que tudo está a funcionar conforme esperado.
4. **Replicação**: depois da replicação inicial, começa a sincronização delta de acordo com as definições de replicação.
   * **Falha de replicação**: se falhar a replicação delta, e uma replicação completa seria dispendiosa em termos de largura de banda ou de tempo, ocorre a ressincronização. Por exemplo, se os ficheiros de .hrl atingirem 50% do tamanho do disco, a VM será marcada para ressincronização. A ressincronização minimiza a quantidade de dados enviados por computação de somas de verificação das máquinas virtuais fonte e de destino, enviando-se apenas o delta. Após a conclusão da ressincronização, retoma-se a replicação delta. Por predefinição, a ressincronização está agendada para ser executada automaticamente fora do horário de expediente, mas pode ressincronizar manualmente uma máquina virtual.
   * **Erro de replicação**: se ocorrer um erro de replicação, haverá uma repetição interna. Se for um erro não recuperável, tais como um erro de autenticação ou autorização, ou uma máquina de réplica que está num estado inválido, nenhuma repetição ocorrerá. Se for um erro recuperável, como um erro de rede ou espaço em disco/memória insuficiente, em seguida, uma nova tentativa ocorre em intervalos crescentes (a cada 1, 2, 4, 8, 10 e 30 minutos).
5. **Ativações pós-falha planeadas/imprevistas**: pode executar as ativações pós-falha planeadas ou imprevistas, conforme necessário. Se executar uma ativação pós-falha planeada, as VMs fonte são desligadas para garantir que não há perda de dados. Depois da criação de réplicas de VMs, estas são colocadas num estado de confirmação pendente. Precisa de confirmá-las para concluir a ativação pós-falha, a menos que esteja a replicar com SAN pois, nesse caso, a confirmação é automática. Assim que o site primário estiver em execução, pode ocorrer a reativação pós-falha. Se tiver replicado para o Azure, a replicação inversa será automática. Caso contrário, pode iniciar a replicação inversa manualmente.

![fluxo de trabalho](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>Passos seguintes
[Preparar para a implementação](site-recovery-best-practices.md)




<!--HONumber=Nov16_HO2-->


