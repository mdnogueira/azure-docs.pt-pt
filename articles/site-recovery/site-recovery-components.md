<properties
    pageTitle="Como funciona a Recuperação de Sites? | Microsoft Azure"
    description="Este artigo fornece uma descrição geral da arquitetura da Recuperação de Sites"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="03/27/2016"
    ms.author="raynew"/>

# Como funciona o Azure Site Recovery?

Leia este artigo para compreender a arquitetura subjacente do Azure Site Recovery e os componentes que fazem que funcione. 

Publique comentários ou perguntas na parte inferior deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Descrição geral

As organizações necessitam de uma estratégia de continuidade empresarial e de recuperação após desastre (BCDR) que determine como as aplicações, cargas de trabalho e dados permanecem disponíveis durante o período de indisponibilidade planeado e imprevisto, e como retomar as condições de trabalho normais com a maior brevidade possível.

A Recuperação de Sites é um serviço Azure que contribui para a sua estratégia BCDR por organizar a replicação tanto dos servidores físicos no local como das máquinas virtuais na nuvem (Azure) ou num site secundário. Quando ocorrem falhas na sua localização principal, faz-se a ativação pós-falha para o site secundário para manter disponíveis as aplicações e cargas de trabalho. A localização principal é reativada quando se retomam as operações normais.

A Recuperação de Sites pode ser implementada para orquestrar a replicação em vários cenários:

- **Replicar máquinas virtuais VMware**: pode replicar máquinas virtuais VMware no local no [Azure](site-recovery-vmware-to-azure-classic.md) ou num [Datacenter secundário](site-recovery-vmware-to-vmware.md).
- **Replicar máquinas físicas**: pode replicar máquinas físicas que executem Windows ou Linux no [Azure](site-recovery-vmware-to-azure-classic.md) ou num [Datacenter secundário](site-recovery-vmware-to-vmware.md).
- **Replicar VMs Hyper-V geridos em nuvens de System Center VMM**: pode replicar máquinas virtuais do Hyper-V no local em nuvens do VMM no [Azure](site-recovery-vmm-to-azure.md) ou num [Datacenter secundário](site-recovery-vmm-to-vmm.md). 
- **Replicar VMs Hyper-V (sem VMM)**: pode replicar VMs Hyper-V que não são geridas pelo VMM no [Azure](site-recovery-hyper-v-site-to-azure.md).
- **Migrar VMs**: pode utilizar a Recuperação de Sites para [migrar VMs IaaS do Azure](site-recovery-migrate-azure-to-azure.md) entre regiões ou para [migrar instâncias de Windows em AWS ](site-recovery-migrate-aws-to-azure.md) para VMs IaaS do Azure. Atualmente, só há suporte para a migração, o que significa essas VMs podem efetuar uma ativação pós-falha mas não podem ser reativadas.

A Recuperação de Sites pode replicar a maioria das aplicações em execução nessas VMs e servidores físicos. Em [Que cargas de trabalho pode o Azure Site Recovery proteger?](site-recovery-workload.md) pode obter um resumo completo das aplicações suportadas.

## Replicar máquinas virtuais de VMware no local ou servidores físicos no Azure

Atualmente, estão disponíveis duas arquiteturas diferentes para replicar VMs VMware ou servidores físicos de Windows/Linux no Azure:

- [Arquitetura herdada](site-recovery-vmware-to-azure-classic-legacy.md): esta arquitetura não deve ser utilizada para novas implementações. 
- [Arquitetura melhorada](site-recovery-vmware-to-azure-classic.md): esta é a arquitetura mais recente e deve ser utilizada para todas as novas implementações. Se já tiver implementado este cenário através de arquitetura herdada, [saiba mais sobre a migração](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment) para a implementação melhorada.

Na implementação avançada terá de configurar um servidor de gestão no local com todos os componentes da Recuperação de Sites. Em cada máquina que pretende proteger insira automaticamente (ou instale manualmente) o Serviço de mobilidade. Depois da replicação inicial, o Serviço de mobilidade na máquina envia dados de replicação delta para o servidor de processos, que os otimiza antes de enviá-los para o armazenamento do Azure.

![Melhorado](./media/site-recovery-components/arch-enhanced.png)
![Melhorado](./media/site-recovery-components/arch-enhanced2.png)

### Local
Eis o que precisa no ambiente no local:

- **Servidor de gestão**: precisará de uma máquina de Windows Server 2012 R2 que atue como Servidor de gestão. Neste servidor instalará todos estes componentes da Recuperação de Sites com um único ficheiro de instalação:

    - **Componente do servidor de configuração**: coordena a comunicação entre o seu ambiente no local e o Azure, e gere a replicação e recuperação de dados.
    - **Componente do servidor de processo**: atua como um gateway de replicação. Recebe dados de replicação de máquinas de origem protegida, otimiza-as com colocação em cache, compressão e encriptação, e envia os dados para o armazenamento do Azure. Também processa a instalação de inserção do serviço de mobilidade para máquinas protegidas e efetua a deteção automática de VMs de VMware. À medida que cresça a implementação é possível adicionar mais servidores de processos dedicados independentes para processar um crescente volume de tráfego de replicação.
    - **Componente de servidor de destino principal**: processa dados de replicação durante a reativação pós-falha a partir do Azure. 
- **Anfitriões VMware ESX/ESXi e servidor vCenter**: precisará de um ou mais servidores anfitriões ESX/ESXi a executar as VMs VMware. Recomendamos que implemente um servidor vCenter para gerir esses anfitriões. **Nota:** **ainda que se repliquem servidores físicos, tem de recuperá-los no VMware**. Quando se replica um servidor físico, este será executado como uma VM do Azure ao efetuar a ativação pós-falha para o Azure. Haverá recuperação no local como uma VM do VMware. 
    
- **VMs/servidores físicos**: cada máquina que quiser replicar no Azure precisará do componente do Serviço de mobilidade instalado. Este serviço obtém dados, escreve na máquina e reencaminha-os para o servidor de processos. Este componente pode ser instalado manualmente ou pode ser inserido e instalado automaticamente pelo servidor de processos ao ativar a replicação para uma máquina.

### Azure

Eis o que necessita na infraestrutura do Azure:     - **Conta do Azure**: precisará de uma conta do Microsoft Azure.
    - **Armazenamento do Azure**: precisará de uma conta de armazenamento do Azure para armazenar dados replicados. Os dados replicados são guardados no armazenamento do Azure e as VMs do Azure são aceleradas quando ocorre a ativação pós-falha. 
    - **Rede Azure**: precisará de uma rede virtual do Azure que acelere o Azure, ao qual se ligarão as VMs quando ocorrer a ativação pós-falha. 
    
    
### Reativação pós-falha

A reativação pós-falha no local é sempre para as VMs VMware, mesmo quando houve ativação pós-falha de um servidor físico. Eis o que necessita:

- **Servidor de processo temporário no Azure**: se pretender uma reativação a partir do Azure após a ativação pós-falha, terá de configurar uma VM do Azure como servidor de processos, para lidar com a replicação a partir do Azure. É possível eliminar esta VM após a conclusão da reativação pós-falha.
- **Ligação VPN**: para a reativação pós-falha, precisará de uma ligação VPN (ou Azure ExpressRoute) configurada a partir da rede Azure para o site no local.
- **Servidor de destino principal no local independente**: o servidor de destino principal no local processa a reativação pós-falha. O servidor de destino principal está instalado por predefinição no servidor de gestão, mas se está a reativar maiores volumes de tráfego deve configurar um servidor de destino principal independente no local para esta finalidade. 

![Reativação pós-falha melhorada](./media/site-recovery-components/enhanced-failback.png)

[Saiba mais](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) sobre os requisitos de implementação melhorada.
[Saiba mais](site-recovery-failback-azure-to-vmware-classic.md) sobre a reativação pós-falha para a implementação melhorada.




## Replicar VMs Hyper-V em nuvens VMM para o Azure

Para implementar este cenário, durante a implementação da Recuperação de Sites, instalará o Fornecedor do Azure Site Recovery no servidor VMM. O Fornecedor coordena e orquestra a replicação com o serviço de Recuperação de Sites através da Internet. O agente do Azure Recovery Services é instalado durante a implementação da Recuperação de Sites no servidor de anfitrião Hyper-V, e os dados são replicados entre ele e o armazenamento do Azure através de HTTPS 443. As comunicações provenientes do Fornecedor e do agente são seguras e encriptadas. Também são encriptados os dados replicados no armazenamento do Azure.

- Local: 
    - **Servidor VMM**: pelo menos um servidor VMM configurado com, pelo menos, uma nuvem privada VMM. O servidor deve estar em execução no System Center 2012 R2 e deve ter conectividade à Internet. Se pretender certificar-se de que as VMs do Azure estão ligadas a uma rede após a ativação pós-falha terá de configurar o mapeamento da rede. Para fazê-lo, as VMs de origem devem estar ligadas a uma rede de VMs VMM. Essa rede VM deve ser ligada a uma rede lógica que está associada à nuvem.
    - **Servidor Hyper-V**: pelo menos um servidor de anfitrião Hyper-V localizado em nuvem VMM. Os anfitriões Hyper-V devem estar a executar o Windows Server 2012 R2.
    - **Computadores protegidos**: o servidor de anfitrião Hyper-V de origem deve ter pelo menos uma VM para proteger.
    
- Azure: 
    - **Conta do Azure**: precisará de uma conta do Microsoft Azure.
    - **Armazenamento do Azure**: precisará de uma conta de armazenamento do Azure para armazenar dados replicados. Os dados replicados são guardados no armazenamento do Azure e as VMs do Azure são aceleradas quando ocorre a ativação pós-falha.
    - **Rede Azure**: se pretender configurar o mapeamento de rede para que as VMs do Azure estejam ligadas a redes depois da ativação pós-falha, terá de configurar uma rede do Azure.

    ![VMM para o Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

Saiba mais sobre [os requisitos de implementação](site-recovery-vmm-to-azure.md#before-you-start) exata.

## Replicar máquinas virtuais de VMware ou servidores físicos num site secundário

Para replicar as VMs VMware ou os servidores físicos Windows/Linux para um site secundário, transferirá o InMage Scout que está incluído na subscrição do Azure Site Recovery. Configure servidores de componentes em cada site (configuração, processo, destino principal) e instale o agente Unified nas máquinas que pretende replicar. Após a replicação inicial, o agente em cada máquina envia alterações de replicação delta para o servidor de processos. O servidor de processos otimiza os dados e transfere-o para o servidor de destino principal no site secundário. O servidor de configuração gere o processo de replicação.

![VMware para VMware](./media/site-recovery-components/vmware-to-vmware.png)

### Site primário no local

- **Servidor de processos**: Configure o componente de servidor de processos no seu site primário para lidar com colocação em cache, compressão e otimização de dados. Ele também faz a instalação por push do Agente Unified nas máquinas que pretende proteger. 
- **VMware ESX/ESXi e servidor vCenter**: Se estiver a proteger as VMs VMware, precisará de um VMware EXS/ESXi hipervisor e, opcionalmente, de um servidor VMware vCenter para gerir os hipervisores.
- **Máquinas virtuais/servidores físicos**: as VMs VMware ou os servidores físicos Windows/Linux que pretende proteger, precisam de ter instalado o Agente Unified. O Agente Unified também é instalado nas máquinas que atuam como servidor de destino principal. O agente atua como um fornecedor de comunicação entre todos os componentes. 
    
### Site secundário no local
 
- **Servidor de configuração**: o servidor de configuração é o primeiro componente que instala e é instalado no site secundário para gerir, configurar e monitorizar a implementação, ou utilizando o Web site de gestão ou a consola de vContinuum. Existe apenas um único servidor de configuração numa implementação e tem de ser instalado num computador que execute o Windows Server 2012 R2.
- **Servidor vContinuum**: está instalado na mesma localização (site secundário) que o servidor de configuração. Fornece uma consola de gestão e monitorização do seu ambiente protegido. Numa instalação predefinida, o servidor vContinuum é o primeiro servidor de destino principal e tem o Agente Unified instalado.
- **Servidor de destino principal**: o servidor de destino principal contém dados replicados. Recebe dados do servidor de processos, cria uma máquina de réplica no site secundário e detém os pontos de retenção de dados. O número de servidores de destino principal de que precisa, depende do número de máquinas que estiver a proteger. Se pretender que haja reativação para o site primário, precisará também que aí exista um servidor de destino principal. 

### Azure

Este cenário é implementado através de InMage Scout. Para obtê-lo,precisará de uma subscrição do Azure. Depois de criar um cofre de Recuperação de Sites, transfere o InMage Scout e instala as atualizações mais recentes para configurar a implementação.


## Replicar VMs Hyper-V para o Azure (sem o VMM)

Para replicar VMs Hyper-V que não são geridas em nuvens VMM para o Azure, instale o Fornecedor do Azure Site Recovery e o agente dos Serviços de Recuperação do Azure no anfitrião Hyper-V durante a implementação da Recuperação de Sites. O Fornecedor coordena e orquestra a replicação com o serviço de Recuperação de Sites através da Internet. O agente processa os dados de replicação de dados através de HTTPS 443. As comunicações provenientes do Fornecedor e do agente são seguras e encriptadas. Também são encriptados os dados replicados no armazenamento do Azure.

![Site Hyper-V para o Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

### Local

- **Servidor Hyper-V**: pelo menos um servidor de anfitrião Hyper-V. Os anfitriões Hyper-V devem estar a executar o Windows Server 2012 R2.
- **Computadores protegidos**: o servidor de anfitrião Hyper-V de origem deve ter pelo menos uma VM para proteger.
    
### Azure

- **Conta do Azure**: precisará de uma conta do Microsoft Azure.
- **Armazenamento do Azure**: precisará de uma conta de armazenamento do Azure para armazenar dados replicados. Os dados replicados são guardados no armazenamento do Azure e as VMs do Azure são aceleradas quando ocorre a ativação pós-falha.

[Saiba mais](site-recovery-hyper-v-site-to-azure.md#before-you-start) sobre os requisitos de implementação.


## Replicar VMs Hyper-V em nuvens VMM para o Azure

Para implementar este cenário, durante a implementação da Recuperação de Sites, instale o Fornecedor do Azure Site Recovery no servidor VMM e o agente dos Serviços de Recuperação do Azure no anfitrião Hyper-V. O Fornecedor coordena e orquestra a replicação com o serviço de Recuperação de Sites através da Internet. O agente processa os dados de replicação de dados através de HTTPS 443. As comunicações provenientes do Fornecedor e do agente são seguras e encriptadas. Também são encriptados os dados replicados no armazenamento do Azure (inativos).

![VMM para o Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

### Local

- **Servidor VMM**: pelo menos um servidor VMM configurado com, pelo menos, uma nuvem privada VMM. O servidor deve estar em execução no System Center 2012 R2 e deve ter conectividade à Internet. Se pretender certificar-se de que as VMs do Azure estão ligadas a uma rede após a ativação pós-falha terá de configurar o mapeamento da rede. Para fazê-lo, precisa de ligar as VMs de origem a uma rede de VMs VMM. Essa rede deve ser ligada a uma rede lógica que está associada à nuvem.
- **Servidor Hyper-V**: pelo menos um servidor de anfitrião Hyper-V localizado em nuvem VMM. Os anfitriões Hyper-V devem estar a executar o Windows Server 2012 R2.
- **Computadores protegidos**: o servidor de anfitrião Hyper-V de origem deve ter pelo menos uma VM para proteger.
    
### Azure

- **Conta do Azure**: precisará de uma conta do Microsoft Azure.
- **Armazenamento do Azure**: precisará de uma conta de armazenamento do Azure para armazenar dados replicados. Os dados replicados são guardados no armazenamento do Azure e as VMs do Azure são aceleradas quando ocorre a ativação pós-falha.
- **Rede Azure**: se pretender certificar-se de que as VMs do Azure estão ligadas a redes após a ativação pós-falha terá de configurar o mapeamento da rede. Para efetuar este procedimento, precisará de uma rede do Azure.

[Saiba mais](site-recovery-vmm-to-azure.md#before-you-start) sobre os requisitos de implementação.

## Replicar VMs Hyper-V para um datacenter secundário

Para implementar este cenário, durante a implementação do Azure Site Recovery, instalará o Fornecedor do Azure Site Recovery no servidor VMM. O Fornecedor coordena e orquestra a replicação com o serviço de Recuperação de Sites através da Internet. Os dados são replicados entre os servidores anfitrião primário e secundário Hyper-V sobre LAN ou VPN utilizando o Kerberos ou autenticação de certificados. As comunicações provenientes do Fornecedor e entre os servidores anfitrião Hyper-V são seguras e encriptadas. 

![Local para local](./media/site-recovery-components/arch-onprem-onprem.png)

### Local

- **Servidor VMM**: recomenda-se um servidor VMM no site primário e um no site secundário, cada um possuindo pelo menos uma nuvem privada VMM. O servidor deve estar a executar, pelo menos, o System Center 2012 SP1 com as atualizações mais recentes e estar ligado à Internet. As nuvens devem ter o conjunto de perfis de capacidade Hyper-V.
- **Servidor Hyper-V**: os servidores anfitriões Hyper-V têm de estar localizados em nuvens VMM primárias e secundárias. Os servidores anfitrião devem estar a executar, pelo menos, o Windows Server 2012 com as atualizações mais recentes instaladas e ligado à Internet.
- **Computadores protegidos**: o servidor de anfitrião Hyper-V de origem deve ter pelo menos uma VM para proteger.
    
### Azure

Precisará de uma subscrição do Azure.

[Saiba mais](site-recovery-vmm-to-vmm.md#before-you-start) sobre os requisitos de implementação.


## Replicar VMs Hyper-V para um datacenter secundário com replicação SAN

Para implementar este cenário, durante a implementação do Azure Site Recovery, instalará o Fornecedor do Azure Site Recovery nos servidores VMM. O Fornecedor coordena e orquestra a replicação com o serviço de Recuperação de Sites através da Internet. Os dados são replicados entre as matrizes de armazenamento primário e secundário utilizando a replicação SAN síncrona.

![Replicação SAN](./media/site-recovery-components/arch-onprem-onprem-san.png)

### Local

- **Matriz SAN**: uma [matriz SAN suportada](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) gerida pelo servidor VMM principal. O SAN partilha uma infraestrutura de rede com outra matriz SAN no site secundário.
- **Servidor VMM**: recomenda-se um servidor VMM no site primário e um no site secundário, cada um possuindo pelo menos uma nuvem privada VMM. O servidor deve estar a executar, pelo menos, o System Center 2012 SP1 com as atualizações mais recentes e estar ligado à Internet. As nuvens devem ter o conjunto de perfis de capacidade Hyper-V.
- **Servidor Hyper-V**: os servidores anfitriões Hyper-V localizados em nuvens VMM primárias e secundárias. Os servidores anfitrião devem estar a executar, pelo menos, o Windows Server 2012 com as atualizações mais recentes instaladas e ligado à Internet.
- **Computadores protegidos**: o servidor de anfitrião Hyper-V de origem deve ter pelo menos uma VM para proteger.
    
### Azure

Precisará de uma subscrição do Azure.  

[Saiba mais](site-recovery-vmm-san.md#before-you-start) sobre os requisitos de implementação.


## Ciclo de vida de proteção do Hyper-V

Este fluxo de trabalho mostra o processo para proteger, replicar e efetuar a ativação pós-falha de máquinas virtuais Hyper-V. 

1. **Ativar proteção**: configure o cofre de Recuperação de Sites, configure as definições de replicação para uma nuvem VMM ou para o site Hyper-V, e ative a proteção para as VMs. É iniciada uma tarefa denominada **Ativar Proteção** que pode ser monitorizada no separador **Tarefas**. A tarefa verifica se a máquina está em conformidade com os pré-requisitos e, em seguida, invoca o método [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) que configura a replicação para o Azure com as definições que configurou. A tarefa **Ativar proteção** também invoca o método [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) para inicializar uma replicação completa de VM.
2. **Replicação inicial**: é obtido um instantâneo da máquina virtual e os discos rígidos virtuais são replicados um de cada vez até estarem todos copiados para o Azure ou para o datacenter secundário. O tempo necessário para concluir isto depende do tamanho da VM, largura de banda da rede e do método de replicação inicial. Se ocorrerem alterações de disco enquanto a replicação inicial está em curso, o Controlador de Replicação de Réplica do Hyper-V controla essas alterações como Registos de Replicação do Hyper-V (.hrl) que se encontram na mesma pasta que os discos. Cada disco tem um ficheiro de .hrl associado que será enviado para o armazenamento secundário. Tenha em atenção que os ficheiros de instantâneo e de registo consomem recursos do disco quando a replicação inicial está em curso. Quando a replicação inicial está concluída, o instantâneo VM é eliminado e as alterações de disco delta no registo são sincronizadas e intercaladas.
3. **Finalizar proteção**: após a conclusão da replicação inicial, a tarefa **Finalizar proteção** configura a rede e outras definições de pós-replicação para que a máquina virtual fique protegida. Se está a replicar para o Azure, poderá ter de otimizar as definições para a máquina virtual para que fique preparada para a ativação pós-falha. Nesta altura, pode executar uma ativação pós-falha de teste para verificar que tudo está a funcionar conforme esperado.
4. **Replicação**: depois da replicação inicial, começa a sincronização delta de acordo com as definições de replicação. 
    - **Falha de replicação**: se falhar a replicação delta, e uma replicação completa seria dispendiosa em termos de largura de banda ou de tempo, ocorre a ressincronização. Por exemplo, se os ficheiros de .hrl atingirem 50% do tamanho do disco, a VM será marcada para ressincronização. A ressincronização minimiza a quantidade de dados enviados por computação de somas de verificação das máquinas virtuais fonte e de destino, enviando-se apenas o delta. Após a conclusão da ressincronização, retoma-se a replicação delta. Por predefinição, a ressincronização está agendada para ser executada automaticamente fora do horário de expediente, mas pode ressincronizar manualmente uma máquina virtual.
    - **Erro de replicação**: se ocorrer um erro de replicação, haverá uma repetição interna. Se for um erro não recuperável, tais como um erro de autenticação ou autorização, ou uma máquina de réplica que está num estado inválido, nenhuma repetição ocorrerá. Se for um erro recuperável, como um erro de rede ou espaço em disco/memória insuficiente, em seguida, uma nova tentativa ocorre em intervalos crescentes (a cada 1, 2, 4, 8, 10 e 30 minutos).
4. **Ativações pós-falha planeadas/imprevistas**: pode executar as ativações pós-falha planeadas ou imprevistas, conforme necessário. Se executar uma ativação pós-falha planeada, as VMs fonte são desligadas para garantir que não há perda de dados. Depois da criação de réplicas de VMs, estas são colocadas num estado de confirmação pendente. Precisa de confirmá-las para concluir a ativação pós-falha, a menos que esteja a replicar com SAN pois, nesse caso, a confirmação é automática. Assim que o site primário estiver em execução, pode ocorrer a reativação pós-falha. Se tiver replicado para o Azure, a replicação inversa será automática. Caso contrário, pode iniciar a replicação inversa manualmente.
 

![fluxo de trabalho](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## Passos seguintes

[Preparar para a implementação](site-recovery-best-practices.md)



<!--HONumber=Jun16_HO2-->


