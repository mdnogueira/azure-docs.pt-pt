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
ms.date: 02/21/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 080dce21c2c803fc05c945cdadb1edd55bd7fe1c
ms.openlocfilehash: 4993a873742db5ca2bd8c31eaab098beb0a0a030


---
# <a name="how-does-azure-site-recovery-work"></a>Como funciona o Azure Site Recovery?

Leia este artigo para compreender a arquitetura subjacente do Azure Site Recovery e os componentes que fazem com que funcione.

A Recuperação de Sites é um serviço do Azure que contribui para a sua estratégia de BCDR através da orquestração da replicação dos servidores físicos no local e das máquinas virtuais para a nuvem (Azure) ou para um datacenter secundário. Quando ocorrem falhas na sua localização principal, faz-se a ativação pós-falha para a localização secundária para manter disponíveis as aplicações e cargas de trabalho. A localização principal é reativada quando se retomam as operações normais. Saiba mais em [O que é a Recuperação de Sites?](site-recovery-overview.md)

Este artigo descreve a implementação no [portal do Azure](https://portal.azure.com). O [portal clássico do Azure](https://manage.windowsazure.com/) pode ser utilizado para manter cofres existentes do Site Recovery, mas não pode criar cofres novos.

Publique comentários na parte inferior deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="deployment-scenarios"></a>Cenários de implementação

A Recuperação de Sites pode ser implementada para orquestrar a replicação em vários cenários:

- **Replicar máquinas virtuais VMware**: pode replicar máquinas virtuais VMware no local no Azure ou num datacenter secundário.
- **Replicar computadores**: pode replicar computadores (Windows ou Linux) para o Azure ou para um datacenter secundário. O processo para replicar computadores é quase o mesmo que o processo para replicar VMs de VMware.
- **Replicar VMs de Hyper-V**: pode replicar VMs de Hyper-V para o Azure ou para um site do VMM secundário. Se pretender replicá-las para um site secundário, estas têm de ser geridas em clouds do System Center Virtual Machine Manager (VMM).
- **Migrar VMs**: para além de poder replicar (replicar, fazer a ativação pós-falha e a reativação pós-falha) VMs no local e servidores físicos para o Azure, também pode migrá-los para VMs do Azure (replicar e fazer a ativação pós-falha, mas não a reativação pós-falha). Eis o que pode migrar:
    - Migrar cargas de trabalho em execução em VMs de Hyper-V no local, VMs de VMware e servidores físicos para serem executadas em VMs do Azure.
    - Migrar [VMs de IaaS do Azure](site-recovery-migrate-azure-to-azure.md) entre regiões do Azure. Atualmente, só a migração é suportada neste cenário, o que significa que a reativação pós-falha não o é.
    - Migrar [instâncias do Windows AWS](site-recovery-migrate-aws-to-azure.md) para VMs de IaaS do Azure. Atualmente, só a migração é suportada neste cenário, o que significa que a reativação pós-falha não o é.

O Site Recovery replica aplicações em execução em VMs e servidores físicos suportados. Em [Que cargas de trabalho pode o Azure Site Recovery proteger?](site-recovery-workload.md) pode obter um resumo completo das aplicações suportadas.

## <a name="replicate-vmware-vmsphysical-servers-to-azure"></a>Replicar VMs de VMware/servidores físicos para o Azure

### <a name="components"></a>Componentes

**Componente** | **Detalhes**
--- | ---
**Azure** | No Azure, precisa de uma conta do Microsoft Azure, de uma conta de armazenamento do Azure e de uma rede do Azure.<br/><br/> O armazenamento e a rede podem ser contas do Resource Manager ou contas clássicas.<br/><br/>  Os dados replicados são armazenados na conta de armazenamento e as VMs do Azure são criadas com os dados replicados quando ocorre a ativação pós-falha a partir do site no local. As VMs do Azure ligam-se à rede virtual do Azure quando são criadas.
**Servidor de configuração** | O servidor de configuração é configurado no local para coordenar as comunicações entre o site no local e o Azure e para gerir a replicação de dados.
**Servidor de processos** | Instalado por predefinição no servidor de configuração no local.<br/><br/> Atua como um gateway de replicação. Recebe dados de replicação de máquinas de origem protegida, otimiza-as com colocação em cache, compressão e encriptação, e envia os dados para o armazenamento do Azure.<br/><br/> Processa a instalação push do Serviço de mobilidade para máquinas protegidas e efetua a deteção automática de VMs de VMware.<br/><br/> À medida que cresça a implementação é possível adicionar mais servidores de processos dedicados independentes para processar um crescente volume de tráfego de replicação.
**Servidor de destino mestre** | Instalado por predefinição no servidor de configuração no local.<br/><br/> Processa dados de replicação durante a reativação pós-falha a partir do Azure. Se os volumes de tráfego da reativação pós-falha forem elevados, pode implementar um servidor de destino mestre separado para a reativação pós-falha.
**Servidores de VMware** | Adiciona servidores vCenter e vSphere ao cofre dos Serviços de Recuperação para replicar VMs de VMware.<br/><br/> Se replicar servidores físicos, vai precisar de uma infraestrutura de VMware no local para a reativação pós-falha. Não pode fazer a reativação pós-falha para um computador.
**Máquinas replicadas** | O Serviço de mobilidade tem de ser instalado em cada máquina que quer replicar. Pode ser instalado manualmente em cada máquina ou através de uma instalação push a partir do servidor de processos.

**Figura 1: Componentes de VMware para o Azure**

![Componentes](./media/site-recovery-components/arch-enhanced.png)

### <a name="replication-process"></a>Processo de replicação

1. É configurada a implementação, incluindo os componentes do Azure, e um cofre dos Serviços de Recuperação. No cofre, especifica a origem e o destino da replicação, configura o servidor de configuração, adiciona servidores de VMware, cria uma política de replicação, implementa o Serviço de mobilidade, ativa a replicação e executa uma ativação pós-falha de teste.
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

### <a name="failover-and-failback-process"></a>Processo de ativação pós-falha e de reativação pós-falha

1. São executadas ativações pós-falha não planeadas de VMs de VMWare no local e de servidores físicos para o Azure. A ativação pós-falha planeada não é suportada.
2. Pode fazer a ativação pós-falha de uma máquina individual ou criar [planos de recuperação](site-recovery-create-recovery-plans.md) para orquestrar a ativação pós-falha de várias máquinas.
3. Quando executa uma ativação pós-falha, são criadas VMs de réplica no Azure. Consolida uma ativação pós-falha para começar a aceder à carga de trabalho da VM do Azure de réplica.
4. Quando o site no local primário estiver novamente disponível, pode fazer a reativação pós-falha. Configura uma infraestrutura de reativação pós-falha, inicia a replicação da máquina do site secundário para o primário e executa uma ativação pós-falha não planeada a partir do site secundário. Depois de consolidar esta ativação pós-falha, os dados voltam ao site no local e terá de ativar novamente a replicação para o Azure. [Saiba mais](site-recovery-failback-azure-to-vmware.md)

Existem alguns requisitos para a reativação pós-falha:

- **Reativação pós-falha físico para físico não suportada**: tal significa que, se efetuar uma ativação pós-falha dos servidores físico para o Azure e pretender de seguida proceder a uma reativação pós-falha, a reativação pós-falha terá de ser numa VM de VMware. Não pode efetuar a reativação pós-falha num servidor físico. Para a reativação pós-falha, irá precisar de uma VM do Azure e, caso não tenha implementado o servidor de configuração como uma VM de VMware, terá de configurar um servidor de destino principal separado como uma VM de VMware. Esta ação é necessária uma vez que o servidor de destino principal interage e estabelece a ligação com o armazenamento do VMware para restaurar os discos para uma VM de VMware.
- **Servidor de processo temporário no Azure**: se pretender uma reativação a partir do Azure após a ativação pós-falha, terá de configurar uma VM do Azure como servidor de processos, para lidar com a replicação a partir do Azure. É possível eliminar esta VM após a conclusão da reativação pós-falha.
- **Ligação VPN**: para a reativação pós-falha, precisará de uma ligação VPN (ou Azure ExpressRoute) configurada a partir da rede Azure para o site no local.
- **Servidor de destino principal no local independente**: o servidor de destino principal no local processa a reativação pós-falha. O servidor de destino principal está instalado por predefinição no servidor de gestão, mas se está a reativar maiores volumes de tráfego deve configurar um servidor de destino principal independente no local para esta finalidade.
- **Política de reativação pós-falha**: para replicar de novo para o site no local, precisa de uma política de reativação pós-falha. Esta foi criada automaticamente quando criou a política de replicação.

**Figura 3: Reativação pós-falha de VMware/servidor físico**

![Reativação pós-falha](./media/site-recovery-components/enhanced-failback.png)


## <a name="replicate-vmware-vmsphysical-servers-to-a-secondary-site"></a>Replicar VMs de VMware/servidores físicos para um site secundário

### <a name="components"></a>Componentes

**Componente** | **Detalhes**
--- | ---
**Azure** | Este cenário é implementado através de InMage Scout. Para obtê-lo,precisará de uma subscrição do Azure.<br/><br/> Depois de criar um cofre dos Serviços de Recuperação, transfere o InMage Scout e instala as atualizações mais recentes para configurar a implementação.
**Servidor de processos** | Implementa o componente de servidor de processos no seu site primário para lidar com a colocação em cache, a compressão e a otimização de dados.<br/><br/> Ele também faz a instalação por push do Agente Unified nas máquinas que pretende proteger.
**VMware ESX/ESXi e servidor vCenter** |  Precisa de uma infraestrutura do VMware para replicar VMs de VMware.
**VMs/servidores físicos** |  Instala o Agente Unified nas VMs de VMware ou nos servidores físicos Windows/Linux que pretende replicar.<br/><br/> O agente atua como um fornecedor de comunicação entre todos os componentes.
**Servidor de configuração** | O servidor de configuração é instalado no site secundário para gerir, configurar e monitorizar a implementação, utilizando o Web site de gestão ou a consola do vContinuum.
**Servidor de vContinuum** | Instalado na mesma localização que o servidor de configuração.<br/><br/> Fornece uma consola de gestão e monitorização do seu ambiente protegido.
**Servidor de destino mestre (site secundário)** | O servidor de destino mestre contém dados replicados. Recebe dados do servidor de processos, cria uma máquina de réplica no site secundário e detém os pontos de retenção de dados.<br/><br/> O número de servidores de destino principal de que precisa, depende do número de máquinas que estiver a proteger.<br/><br/> Se pretender que haja reativação para o site primário, precisa também que aí exista um servidor de destino principal. O Agente Unified é instalado neste servidor.

### <a name="replication-process"></a>Processo de replicação

1. Configure servidores de componentes em cada site (configuração, processo, destino principal) e instale o agente Unified nas máquinas que pretende replicar.
2. Após a replicação inicial, o agente em cada máquina envia alterações de replicação delta para o servidor de processos.
3. O servidor de processos otimiza os dados e transfere-o para o servidor de destino principal no site secundário. O servidor de configuração gere o processo de replicação.

**Figura 4: Replicação de VMware para VMware**

![VMware para VMware](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="replicate-hyper-v-vms-to-azure"></a>Replicar VMs Hyper-V para o Azure


### <a name="components"></a>Componentes

**Componente** | **Detalhes**
--- | ---

**Azure** | No Azure, precisa de uma conta do Microsoft Azure, de uma conta de armazenamento do Azure e de uma rede do Azure.<br/><br/> O armazenamento e a rede podem ser contas baseadas no Resource Manager ou contas clássicas.<br/><br/> Os dados replicados são armazenados na conta de armazenamento e as VMs do Azure são criadas com os dados replicados quando ocorre a ativação pós-falha a partir do site no local.<br/><br/> As VMs do Azure ligam-se à rede virtual do Azure quando são criadas.
**Servidor VMM** |Se os anfitriões de Hyper-V estiverem localizados em clouds do VMM, precisa de ter redes lógicas e de VMs configuradas para definir o mapeamento de rede. Uma rede VM deve ser ligada a uma rede lógica que está associada à nuvem.
**Anfitrião de Hyper-V** | Precisa de um ou mais servidores de anfitrião Hyper-V.
**VMs de Hyper-V** | precisa de uma ou mais VMs no servidor anfitrião Hyper-V. O Fornecedor em execução no anfitrião Hyper-V coordena e orquestra a replicação com o serviço Site Recovery através da Internet. O agente processa os dados de replicação de dados através de HTTPS 443. As comunicações provenientes do Fornecedor e do agente são seguras e encriptadas. Também são encriptados os dados replicados no armazenamento do Azure.


## <a name="replication-process"></a>Processo de replicação

1. São configurados os componentes do Azure. Recomendamos que configure as contas de armazenamento e de rede antes de iniciar a implementação do Site Recovery.
2. Cria um cofre dos Serviços de Replicação para o Site Recovery e configura as definições do cofre, incluindo:
    - Se não estiver a gerir anfitriões Hyper-V numa cloud do VMM, crie um contentor de sites de Hyper-V e adicione anfitriões Hyper-V ao mesmo.
    - A origem e o destino da replicação. Se os anfitriões Hyper-V forem geridos no VMM, a origem é a cloud do VMM. Se não, é o site Hyper-V.
    - Instalação do Fornecedor do Azure Site Recovery e do agente dos Serviços de Recuperação do Microsoft Azure. Se tiver o VMM, o Fornecedor será instalado no mesmo e o agente em cada anfitrião Hyper-V. Se não tiver o VMM, o Fornecedor e o agente são instalados em cada anfitrião.
    - Cria uma política de replicação para o site Hyper-V ou para a cloud do VMM. A política é aplicada a todas as VMs localizadas nos anfitriões no site ou na cloud.
    - Ativa a replicação nas VMs de Hyper-V. A replicação inicial ocorre de acordo com as definições da política de replicação.
4. As alterações aos dados são registadas e a replicação das alterações delta para o Azure começa depois de concluída a replicação inicial. As alterações registadas relativas a um item são guardadas num ficheiro .hrl.
5. Executa uma ativação pós-falha de teste, para confirmar que está tudo a funcionar.

### <a name="failover-and-failback-process"></a>Processo de ativação pós-falha e de reativação pós-falha

1. Executa uma [ativação pós-falha](site-recovery-failover.md) planeada ou não planeada a partir das VMs de Hyper-V no local para o Azure. Se executar uma ativação pós-falha planeada, as VMs de origem são desligadas para garantir que não há perda de dados.
2. Pode fazer a ativação pós-falha de uma máquina individual ou criar [planos de recuperação](site-recovery-create-recovery-plans.md) para orquestrar a ativação pós-falha de várias máquinas.
4. Depois de executar a ativação pós-falha, deverá conseguir ver as VMs de réplica criadas no Azure. Pode atribuir um endereço IP público à VM, se necessário.
5. Em seguida, consolida a ativação pós-falha para começar a aceder à carga de trabalho da VM do Azure de réplica.
6. Quando o site no local primário estiver novamente disponível, pode fazer a reativação pós-falha. Arranca uma ativação pós-falha planeada do Azure para o site primário. Nas ativações pós-falha planeadas, pode optar por ativar para a mesma VM ou para uma localização alternativa, bem como sincronizar as alterações entre o Azure e o site no local, para garantir que não se perdem dados. Quando as VMs são criadas no local, é consolidada a ativação pós-falha.

**Figura 5: Replicação de site Hyper-V para o Azure**

![Componentes](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

**Figura 6: Replicação de Hyper-V em clouds do VMM para o Azure**

![Componentes](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)



## <a name="replicate-hyper-v-vms-to-a-secondary-site"></a>Replicar VMs Hyper-V para um site secundário

### <a name="components"></a>Componentes

**Componente** | **Detalhes**
--- | ---
**Conta do Azure** | Precisa de uma conta do Microsoft Azure.
**Servidor VMM** | Recomendamos um servidor VMM no site primário e outro no secundário, ligados à Internet.<br/><br/> Cada servidor deve ter, pelo menos, uma cloud privada do VMM, com o perfil de capacidade de Hyper-V definido.<br/><br/> O Fornecedor do Azure Site Recovery é instalado no servidor VMM. O Fornecedor coordena e orquestra a replicação com o serviço de Recuperação de Sites através da Internet. As comunicações entre o Fornecedor e o Azure são seguras e encriptadas.
**Servidor Hyper-V** |  Precisa de um ou mais servidores de anfitriões Hyper-V nas clouds do VMM primárias e secundárias. Os servidores devem estar ligados à Internet.<br/><br/> Os dados são replicados entre os servidores anfitrião primário e secundário Hyper-V através de LAN ou VPN mediante a utilização de Kerberos ou da autenticação de certificados.  
**Máquinas de origem** | O servidor de anfitrião Hyper-V de origem deve ter, pelo menos, uma VM que queira replicar.

## <a name="replication-process"></a>Processo de replicação

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

### <a name="failover-and-failback-process"></a>Processo de ativação pós-falha e de reativação pós-falha

1. Executa uma [ativação pós-falha](site-recovery-failover.md) planeada ou não planeada entre os sites no local. Se executar uma ativação pós-falha planeada, as VMs de origem são desligadas para garantir que não há perda de dados.
2. Pode fazer a ativação pós-falha de uma máquina individual ou criar [planos de recuperação](site-recovery-create-recovery-plans.md) para orquestrar a ativação pós-falha de várias máquinas.
4. Se fizer uma ativação pós-falha não planeada para um site secundário, as máquinas na localização secundária não estão ativadas para proteção ou replicação após a ativação pós-falha. Se tiver executado uma ativação pós-falha planeada, as máquinas na localização secundária estão protegidas após a ativação pós-falha.
5. Em seguida, consolida a ativação pós-falha para começar a aceder à carga de trabalho da VM de réplica.
6. Quando o site primário estiver novamente disponível, inicia a replicação inversa, para replicar do site secundário para o primário. A replicação inversa coloca as máquinas virtuais num estado protegido, mas a localização ativa continua a ser o datacenter secundário.
7. Para que o site primário volte a ser a localização ativa, inicie uma ativação pós-falha planeada do site secundário para o primário, seguida de outra replicação inversa.


### <a name="hyper-v-replication-workflow"></a>Fluxo de trabalho da replicação de Hyper-V

**Fase do fluxo de trabalho** | **Ação**
--- | ---
1. **Ativar a proteção** | Depois de ativar a proteção numa VM de Hyper-V, é iniciada a tarefa **Ativar Proteção**, para verificar se a máquina está em conformidade com os pré-requisitos. A tarefa invoca dois métodos:<br/><br/> [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx), para configurar a replicação com as definições que configurou.<br/><br/> [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx), para inicializar uma replicação completa da VM.
2. **Replicação inicial** |  É obtido um instantâneo da máquina virtual e os discos rígidos virtuais são replicados um de cada vez até estarem todos copiados para a localização secundária.<br/><br/> O tempo necessário para concluir isto depende do tamanho da VM, largura de banda da rede e do método de replicação inicial.<br/><br/> Se ocorrerem alterações de disco enquanto a replicação inicial está em curso, o Controlador de Replicação de Réplica do Hyper-V regista essas alterações como Registos de Replicação do Hyper-V (.hrl) que se encontram na mesma pasta que os discos.<br/><br/> Cada disco tem um ficheiro de .hrl associado que será enviado para o armazenamento secundário.<br/><br/> Os ficheiros de instantâneo e de registo consomem recursos do disco quando a replicação inicial está em curso. Quando a replicação inicial for concluída, o instantâneo de VM é eliminado e as alterações de disco delta no registo são sincronizadas e intercaladas.
3. **Finalizar a proteção** | Após a conclusão da replicação inicial, a tarefa **Finalizar proteção** configura a rede e outras definições de pós-replicação, para que a máquina virtual fique protegida.<br/><br/> Se está a replicar para o Azure, poderá ter de otimizar as definições para a máquina virtual para que fique preparada para a ativação pós-falha.<br/><br/> Nesta altura, pode executar uma ativação pós-falha de teste para verificar que tudo está a funcionar conforme esperado.
4. **Replicação** | Depois da replicação inicial, começa a sincronização delta, de acordo com as definições de replicação.<br/><br/> **Falha de replicação**: se falhar a replicação delta, e uma replicação completa seria dispendiosa em termos de largura de banda ou de tempo, ocorre a ressincronização. Por exemplo, se os ficheiros de .hrl atingirem 50% do tamanho do disco, a VM será marcada para ressincronização. A ressincronização minimiza a quantidade de dados enviados por computação de somas de verificação das máquinas virtuais de origem e de destino, enviando apenas o delta. Após a conclusão da ressincronização, retoma-se a replicação delta. Por predefinição, a ressincronização está agendada para ser executada automaticamente fora do horário de expediente, mas pode ressincronizar manualmente uma máquina virtual.<br/><br/> **Erro de replicação**: se ocorrer um erro de replicação, haverá uma repetição interna. Se for um erro não recuperável, tais como um erro de autenticação ou autorização, ou uma máquina de réplica que está num estado inválido, nenhuma repetição ocorrerá. Se for um erro recuperável, como um erro de rede ou espaço em disco/memória insuficiente, em seguida, uma nova tentativa ocorre em intervalos crescentes (a cada 1, 2, 4, 8, 10 e 30 minutos).
5. **Ativação pós-falha planeada/não planeada** | Pode executar ativações pós-falha planeadas ou não planeadas conforme necessário.<br/><br/> Se executar uma ativação pós-falha planeada, as VMs de origem são desligadas para garantir que não há perda de dados.<br/><br/> Depois de as réplicas de VMs serem criadas, são colocadas no estado de confirmação pendente. Para concluir a ativação pós-falha, tem de consolidá-las.<br/><br/> Quando o site primário estiver a funcionar, pode fazer a reativação pós-falha para o site primário, quando este estiver disponível.


**Figura 8: Fluxo de trabalho do Hyper-V**

![fluxo de trabalho](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>Passos seguintes

[Verificar pré-requisitos](site-recovery-prereq.md)



<!--HONumber=Feb17_HO4-->


