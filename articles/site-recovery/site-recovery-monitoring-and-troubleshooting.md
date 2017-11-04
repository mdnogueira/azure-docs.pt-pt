---
title: "Monitorizar e resolver problemas de proteção para máquinas virtuais e servidores físicos | Microsoft Docs"
description: "O Azure Site Recovery coordena a replicação, ativação pós-falha e recuperação de máquinas virtuais localizadas em servidores no local para Azure ou num datacenter secundário. Utilize este artigo para monitorizar e resolver problemas de proteção de site do Virtual Machine Manager ou o Hyper-V."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: mkjain
editor: 
ms.assetid: 0fc8e368-0c0e-4bb1-9d50-cffd5ad0853f
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: rajanaki
ms.openlocfilehash: 2d033e5af13660c99aba813c58b743bf94a6b95a
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="monitor-and-troubleshoot-protection-for-virtual-machines-and-physical-servers"></a>Monitorizar e resolver problemas de proteção para máquinas virtuais e servidores físicos
Este guia de resolução de problemas e de monitorização ajuda-o a saber como controlar o estado de funcionamento de replicação e técnicas de resolução de problemas para o Azure Site Recovery.

## <a name="understand-the-components"></a>Compreender os componentes
### <a name="vmware-virtual-machine-or-physical-server-site-deployment-for-replication-between-on-premises-and-azure"></a>Máquina virtual VMware ou implementação de site do servidor físico para a replicação entre no local e o Azure
Para configurar a recuperação de base de dados entre uma máquina de virtual do VMware no local ou o servidor físico e o Azure, terá de configurar o servidor de configuração, o servidor de destino principal e os componentes de servidor de processo da máquina virtual ou o servidor. Quando ativar a proteção para o servidor de origem, o Azure Site Recovery instala a funcionalidade de Mobile Apps do App Service do Microsoft Azure. Após uma falha no local e depois da origem de servidor de ativação pós-falha para o Azure, os clientes precisam para configurar um servidor de processos no Azure e um servidor de destino principal no local para reconstruir o servidor de origem no local.

![Implementação de site VMware/físico para a replicação entre no local e o Azure](media/site-recovery-monitoring-and-troubleshooting/image18.png)

### <a name="virtual-machine-manager-site-deployment-for-replication-between-on-premises-sites"></a>Implementação de sites do Virtual Machine Manager para a replicação entre sites no local
Para configurar a recuperação de base de dados entre duas localizações no local, terá de transferir o fornecedor do Azure Site Recovery e instalá-la no servidor do Virtual Machine Manager. O fornecedor necessita de conectividade de Internet para se certificar de que todas as operações que são acionadas do portal do Azure obterem convertidas em operações no local.

![Implementação de sites do Virtual Machine Manager para a replicação entre sites no local](media/site-recovery-monitoring-and-troubleshooting/image1.png)

### <a name="virtual-machine-manager-site-deployment-for-replication-between-on-premises-locations-and-azure"></a>Implementação de sites do Virtual Machine Manager para a replicação entre localizações no local e o Azure
Quando configurar a recuperação de base de dados entre localizações no local e o Azure, terá de transferir o fornecedor do Azure Site Recovery e instalá-la no servidor do Virtual Machine Manager. Também terá de instalar o agente de serviços de recuperação do Azure, que tem de ser instalado em cada anfitrião de Hyper-V. [Saiba mais](site-recovery-hyper-v-azure-architecture.md) para obter mais informações.

![Implementação de sites do Virtual Machine Manager para a replicação entre localizações no local e o Azure](media/site-recovery-monitoring-and-troubleshooting/image2.png)

### <a name="hyper-v-site-deployment-for-replication-between-on-premises-locations-and-azure"></a>Implementação de site Hyper-V para a replicação entre localizações no local e o Azure
Este processo é semelhante à implementação do Virtual Machine Manager. A única diferença é que o fornecedor do Azure Site Recovery e o Azure Recovery Services Agent obterem instalados no próprio anfitrião Hyper-V. [Saiba mais](site-recovery-hyper-v-azure-architecture.md). .

## <a name="monitor-configuration-protection-and-recovery-operations"></a>Monitorizar as operações de configuração, a proteção e recuperação
Cada operação no Azure Site Recovery está auditada e registada no **tarefas** separador. Para qualquer configuração, proteção ou erro de recuperação, vá para o **tarefas** separador e procure falhas.

![O filtro de falha no separador tarefas da](media/site-recovery-monitoring-and-troubleshooting/image3.png)

Se encontrar falhas sob o **tarefas** separador, clique na tarefa e, em **detalhes do erro** para essa tarefa.

![O botão de detalhes do erro](media/site-recovery-monitoring-and-troubleshooting/image4.png)

Os detalhes do erro irão ajudá-lo a identificar uma causa possível e recomendações para o problema.

![Caixa de diálogo que mostra os detalhes do erro para uma tarefa específica](media/site-recovery-monitoring-and-troubleshooting/image5.png)

No exemplo anterior, outra operação que está em curso parece estar a causar a configuração de proteção falha. Resolva o problema, com base na recomendação e, em seguida, clique em **REINICIAR** para iniciar a operação de novo.

![O botão de REINÍCIO no separador tarefas da](media/site-recovery-monitoring-and-troubleshooting/image6.png)

O **REINICIAR** opção não está disponível para todas as operações. Se não tiver uma operação de **REINICIAR** opção, volte atrás para o objeto e repita a operação de novo. Pode cancelar qualquer tarefa que está em curso, utilizando o **Cancelar** botão.

![O botão para cancelar](media/site-recovery-monitoring-and-troubleshooting/image7.png)

## <a name="monitor-replication-health-for-virtual-machines"></a>Monitorizar estado de funcionamento de replicação para máquinas virtuais
Pode utilizar o portal do Azure para monitorizar remotamente fornecedores do Azure Site Recovery para cada uma das entidades protegidas. Clique em **itens protegidos**e, em seguida, clique em **NUVENS VMM** ou **grupos de PROTEÇÃO**. O **NUVENS VMM** separador apenas está disponível para implementações que se baseiam num Virtual Machine Manager. Para obter outros cenários, as entidades protegidas estão sob o **grupos de PROTEÇÃO** separador.

![As opções de nuvens do VMM e de grupos de PROTEÇÃO](media/site-recovery-monitoring-and-troubleshooting/image8.png)

Clique numa entidade protegida com a respetiva nuvem ou grupo de proteção para ver todas as operações disponíveis são apresentados no painel inferior.

![Opções disponíveis para uma entidade protegida selecionada](media/site-recovery-monitoring-and-troubleshooting/image9.png)

Como é mostrado na captura de ecrã anterior, o estado de funcionamento da máquina virtual é **crítico**. Pode clicar no **detalhes do erro** botão na parte inferior para ver o erro. Com base no **as causas possíveis** e **recomendação**, resolva o problema.

![O botão de detalhes do erro](media/site-recovery-monitoring-and-troubleshooting/image10.png)

![Erros e as recomendações na caixa de diálogo de detalhes do erro](media/site-recovery-monitoring-and-troubleshooting/image11.png)

> [!NOTE]
> Se quaisquer operações ativas em curso ou falha, avance para o **tarefas** ver como mencionado anteriormente para ver os erros para uma tarefa específica.
>
>

## <a name="troubleshoot-on-premises-hyper-v-issues"></a>Resolver problemas de Hyper-V no local
Ligar a consola do Gestor de Hyper-V no local, selecione a máquina virtual e consulte o estado de funcionamento de replicação.

![Opção para ver o estado de funcionamento de replicação na consola do Gestor de Hyper-V](media/site-recovery-monitoring-and-troubleshooting/image12.png)

Neste caso, **estado de funcionamento de replicação** é **crítico**. A máquina virtual com o botão direito e, em seguida, clique em **replicação** > **Ver estado de funcionamento de replicação** para ver os detalhes.

![Estado de funcionamento de replicação para uma máquina virtual específica](media/site-recovery-monitoring-and-troubleshooting/image13.png)

Se a replicação está em pausa para a máquina virtual, a máquina virtual com o botão direito e, em seguida, clique em **replicação** > **retomar replicação**.

![Opção para retomar a replicação na consola do Gestor de Hyper-V](media/site-recovery-monitoring-and-troubleshooting/image19.png)

Se uma máquina virtual migrada de um novo anfitrião do Hyper-V que esteja dentro do cluster ou um computador autónomo e o anfitrião de Hyper-V tiver sido configurado através do Azure Site Recovery, a replicação da máquina virtual não ser afetada. Certifique-se de que o novo anfitrião do Hyper-V cumpre todos os pré-requisitos e é configurado através do Azure Site Recovery.

### <a name="event-log"></a>Registo de eventos
| Origens de eventos | Detalhes |
| --- |:--- |
| **Aplicações e serviço de registos/Microsoft/VirtualMachineManager/servidor/Admin** (servidor do Virtual Machine Manager) |Fornece registos útil para resolver problemas do Virtual Machine Manager diferentes muitos. |
| **Aplicações e serviço de registos/MicrosoftAzureRecoveryServices/replicação** (anfitrião de Hyper-V) |Fornece registos útil para resolver problemas do Microsoft Azure Recovery Services Agent muitos. <br/> ![Localização da origem de evento de replicação para o anfitrião Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer03.png) |
| **Aplicações e serviço de registos/Microsoft/Azure Site Recovery/fornecedor/Operational** (anfitrião de Hyper-V) |Fornece registos útil para resolver problemas de serviço do Microsoft Azure Site Recovery muitos. <br/> ![Localização da origem de eventos operacionais para o anfitrião Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer02.png) |
| **Aplicações e serviço de registos/Microsoft/Windows/Hyper-V-VMMS/Admin** (anfitrião de Hyper-V) |Fornece registos útil para resolver problemas de gestão de máquina virtual de Hyper-V muitas. <br/> ![Localização da origem de evento do Virtual Machine Manager para o anfitrião de Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer01.png) |

### <a name="hyper-v-replication-logging-options"></a>Opções de registo de replicação de Hyper-V
Todos os eventos que dizem respeito a replicação de Hyper-V são registados no Hyper-V-VMMS\\Admin registo localizada em registos de serviços e aplicações\\Microsoft\\Windows. Além disso, pode ativar um registo de análise para o serviço de gestão de Máquina Virtual de Hyper-V. Para ativar este registo, primeiro Certifique-os registos analíticos e de depuração podem ser visualizados no Visualizador de eventos. Abra o Visualizador de eventos e, em seguida, clique em **vista** > **Mostrar depurar registos analíticos e**.

![A opção de mostrar depurar registos analíticos e](media/site-recovery-monitoring-and-troubleshooting/image14.png)

Um registo de análise é visível em **Hyper-V-VMMS**.

![Os registos analíticos de registo de eventos árvore Visualizador](media/site-recovery-monitoring-and-troubleshooting/image15.png)

No **ações** painel, clique em **ativar registo**. Depois de estar ativado, é apresentado no **Monitor de desempenho** como um **sessão de rastreio de eventos** localizada sob **conjuntos de Recoletores de dados.**

![Sessões de rastreio de eventos na árvore de Monitor de desempenho](media/site-recovery-monitoring-and-troubleshooting/image16.png)

Para ver as informações recolhidas, pare a sessão de rastreio ao desativar o registo. Guarde o registo e abri-lo novamente no Visualizador de eventos ou utilizar outras ferramentas convertê-lo conforme pretendido.

## <a name="reach-out-for-microsoft-support"></a>Entrar Support da Microsoft
### <a name="log-collection"></a>Recolha de registos
Para proteção do site do Virtual Machine Manager, consulte [recolha de registos do Azure Site Recovery utilizando a ferramenta de diagnóstico de suporte plataforma (SDP)](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx) para recolher os registos necessários.

Para proteção do site de Hyper-V, transfira o [ferramenta](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab) e executá-lo no anfitrião Hyper-V para recolher os registos.

Para cenários de servidor do VMware/físico, consulte [recolha de registos do Azure Site Recovery para VMware e proteção de site físico](http://social.technet.microsoft.com/wiki/contents/articles/30677.azure-site-recovery-log-collection-for-vmware-and-physical-site-protection.aspx) para recolher os registos necessários.

A ferramenta recolhe registos localmente numa subpasta nomeada aleatoriamente % LocalAppData%\ElevatedDiagnostics.

![Passos de exemplo mostrados da proteção do site de Hyper-V.](media/site-recovery-monitoring-and-troubleshooting/animate01.gif)

### <a name="open-a-support-ticket"></a>Abra um pedido de suporte
Emitir um pedido de suporte para o Azure Site Recovery, entrar para suportar o Azure utilizando o URL em <http://aka.ms/getazuresupport>.

## <a name="kb-articles"></a>Artigos da KB
* [Como preservar a letra de unidade para máquinas virtuais protegidas que estão a ativação pós-falha ou migrado para o Azure](http://support.microsoft.com/kb/3031135)
* [Como gerir no local para a utilização de largura de banda de rede de proteção do Azure](https://support.microsoft.com/kb/3056159)
* [O Azure Site Recovery: "não foi possível encontrar o recurso de cluster" Ocorreu um erro ao tentar ativar a proteção para uma máquina virtual](http://support.microsoft.com/kb/3010979)
* [Guia de replicação de Hyper-V, resolver problemas e compreender](http://social.technet.microsoft.com/wiki/contents/articles/21948.hyper-v-replica-troubleshooting-guide.aspx)

## <a name="common-azure-site-recovery-errors-and-their-resolutions"></a>Erros comuns do Azure Site Recovery e as resoluções
Seguem-se erros comuns e as resoluções. Cada erro está documentado numa página separada wiki.

### <a name="general"></a>Geral
* <span style="color:green;">NOVO</span> [tarefas falhar com o erro "é uma operação em curso." Erro 505, 514, 532.](http://social.technet.microsoft.com/wiki/contents/articles/32190.azure-site-recovery-jobs-failing-with-error-an-operation-is-in-progress-error-505-514-532.aspx)
* <span style="color:green;">NOVO</span> [tarefas falhar com o erro "O servidor não está ligado à Internet". Erro 25018.](http://social.technet.microsoft.com/wiki/contents/articles/32192.azure-site-recovery-jobs-failing-with-error-server-isn-t-connected-to-the-internet-error-25018.aspx)

### <a name="setup"></a>Configurar
* [Não é possível registar o servidor do Virtual Machine Manager devido a um erro interno. Consulte a vista tarefas no portal de recuperação de Site para obter mais detalhes sobre o erro. Execute novamente a configuração para registar o servidor.](http://social.technet.microsoft.com/wiki/contents/articles/25570.the-vmm-server-cannot-be-registered-due-to-an-internal-error-please-refer-to-the-jobs-view-in-the-site-recovery-portal-for-more-details-on-the-error-run-setup-again-to-register-the-server.aspx)
* [Não é possível estabelecer uma ligação para o Cofre de Gestor de recuperação do Hyper-V. Verifique as definições de proxy ou tente novamente mais tarde.](http://social.technet.microsoft.com/wiki/contents/articles/25571.a-connection-cant-be-established-to-the-hyper-v-recovery-manager-vault-verify-the-proxy-settings-or-try-again-later.aspx)

### <a name="configuration"></a>Configuração
* [Não é possível criar o grupo de proteção: Ocorreu um erro ao obter a lista de servidores.](http://blogs.technet.com/b/somaning/archive/2015/08/12/unable-to-create-the-protection-group-in-azure-site-recovery-portal.aspx)
* [Cluster de anfitriões Hyper-V contém, pelo menos, um adaptador de rede estática ou não existem adaptadores ligados estão configurados para utilizar DHCP.](http://social.technet.microsoft.com/wiki/contents/articles/25498.hyper-v-host-cluster-contains-at-least-one-static-network-adapter-or-no-connected-adapters-are-configured-to-use-dhcp.aspx)
* [O Virtual Machine Manager não tem permissões para concluir uma ação.](http://social.technet.microsoft.com/wiki/contents/articles/31110.vmm-does-not-have-permissions-to-complete-an-action.aspx)
* [Não é possível selecionar a conta de armazenamento na subscrição ao configurar a proteção.](http://social.technet.microsoft.com/wiki/contents/articles/32027.can-t-select-the-storage-account-within-the-subscription-while-configuring-protection.aspx)

### <a name="protection"></a>Proteção
* <span style="color:green;">NOVO</span> [ativar proteção falha com o erro "Não foi possível configurar a proteção da máquina virtual". Erro 60007, 40003.](http://social.technet.microsoft.com/wiki/contents/articles/32194.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-configured-for-the-virtual-machine-error-60007-40003.aspx)
* <span style="color:green;">NOVO</span> [ativar proteção falha com o erro "Não foi possível ativar a proteção da máquina virtual." Erro 70094.](http://social.technet.microsoft.com/wiki/contents/articles/32195.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-enabled-for-the-virtual-machine-error-70094.aspx)
* <span style="color:green;">NOVO</span> [migração em direto erro 23848 - a máquina virtual vai ser movida utilizando o tipo em direto. Isto poderá interromper o estado de proteção de recuperação da máquina virtual.](http://social.technet.microsoft.com/wiki/contents/articles/32021.live-migration-error-23848-the-virtual-machine-is-going-to-be-moved-using-type-live-this-could-break-the-recovery-protection-status-of-the-virtual-machine.aspx)
* [Ative a proteção falhou porque o agente não instalado no computador anfitrião.](http://social.technet.microsoft.com/wiki/contents/articles/31105.enable-protection-failed-since-agent-not-installed-on-host-machine.aspx)
* [Não é possível localizar um anfitrião adequado para a máquina virtual de réplica - devido a recursos de computação baixa.](http://social.technet.microsoft.com/wiki/contents/articles/25501.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-low-compute-resources.aspx)
* [Não é possível localizar um anfitrião adequado para a máquina virtual de réplica - devido a nenhuma rede lógica ligada.](http://social.technet.microsoft.com/wiki/contents/articles/25502.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-no-logical-network-attached.aspx)
* [Não é possível ligar à máquina de anfitrião de réplica - não foi possível estabelecer ligação.](http://social.technet.microsoft.com/wiki/contents/articles/31106.cannot-connect-to-the-replica-host-machine-connection-could-not-be-established.aspx)

### <a name="recovery"></a>Recuperação
* O Virtual Machine Manager não consegue concluir a operação de anfitrião:
  * [Ativação pós-falha para o ponto de recuperação selecionado para a máquina virtual: geral acesso negado erro.](http://social.technet.microsoft.com/wiki/contents/articles/25504.fail-over-to-the-selected-recovery-point-for-virtual-machine-general-access-denied-error.aspx)
  * [Não foi possível efetuar a ativação pós-falha para o ponto de recuperação selecionado para a máquina virtual Hyper-V: operação abortada.  Tente um ponto de recuperação mais recente. (0x80004004).](http://social.technet.microsoft.com/wiki/contents/articles/25503.hyper-v-failed-to-fail-over-to-the-selected-recovery-point-for-virtual-machine-operation-aborted-try-a-more-recent-recovery-point-0x80004004.aspx)
  * Uma ligação com o servidor não pode ser estabelecida (0x00002EFD).
    * [Não foi possível ativar a replicação inversa da máquina virtual Hyper-V.](http://social.technet.microsoft.com/wiki/contents/articles/25505.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-reverse-replication-for-virtual-machine.aspx)
    * [Não foi possível ativar a replicação da máquina virtual de máquina virtual Hyper-V.](http://social.technet.microsoft.com/wiki/contents/articles/25506.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-replication-for-virtual-machine-virtual-machine.aspx)
  * [Não foi possível consolidar ativação pós-falha para a máquina virtual.](http://social.technet.microsoft.com/wiki/contents/articles/25508.could-not-commit-failover-for-virtual-machine.aspx)
* [O plano de recuperação contém máquinas virtuais que não estão prontas para a ativação pós-falha planeada.](http://social.technet.microsoft.com/wiki/contents/articles/25509.the-recovery-plan-contains-virtual-machines-which-are-not-ready-for-planned-failover.aspx)
* [A máquina virtual não está preparada para ativação pós-falha planeada.](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
* [Máquina virtual não está em execução e não está desligada.](http://social.technet.microsoft.com/wiki/contents/articles/25510.virtual-machine-is-not-running-and-is-not-powered-off.aspx)
* [Operação fora de banda foi efetuada uma máquina virtual e a consolidação da ativação pós-falha falhou.](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
* Ativação pós-falha de teste
  * [Não foi possível iniciar a ativação pós-falha, uma vez que a ativação pós-falha de teste está em curso.](http://social.technet.microsoft.com/wiki/contents/articles/31111.failover-could-not-be-initiated-since-test-failover-is-in-progress.aspx)
* <span style="color:green;">NOVO</span> ativação pós-falha exceder o tempo limite com 'PreFailoverWorkflow task WaitForScriptExecutionTaskTimeout' devido a definições de configuração no grupo de segurança de rede associado à máquina Virtual ou a sub-rede à qual a máquina pertence a. Consulte ['PreFailoverWorkflow task WaitForScriptExecutionTaskTimeout'](https://aka.ms/troubleshoot-nsg-issue-azure-site-recovery) para obter mais detalhes.

### <a name="configuration-server-process-server-master-target"></a>Servidor de configuração, o servidor de processos, o destino principal
* [O anfitrião ESXi em que está alojada a PS/CS como uma VM falha com um ecrã roxa de death.](http://social.technet.microsoft.com/wiki/contents/articles/31107.vmware-esxi-host-experiences-a-purple-screen-of-death.aspx)

### <a name="remote-desktop-troubleshooting-after-failover"></a>Ambiente de trabalho remoto após a ativação pós-falha de resolução de problemas
* Muitos clientes tem deparam problemas para estabelecer ligação com a ativação pós-falha da máquina virtual no Azure. [Utilizar o documento de resolução de problemas para o RDP para a máquina virtual](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

#### <a name="adding-a-public-ip-on-a-resource-manager-virtual-machine"></a>A adição de um IP público numa máquina virtual de Gestor de recursos
Se o **Connect** botão no portal estiver desativado e não estiver ligado ao Azure através de uma ligação de VPN Express Route ou Site a Site, terá de criar e atribuir um endereço IP público a sua máquina virtual antes de poder utilizar o ambiente de trabalho remoto / Shell partilhado. Em seguida, pode adicionar um IP público na interface de rede da máquina virtual.  

![Falha ao adicionar um IP público na interface de rede de ativação pós-falha da máquina virtual](media/site-recovery-monitoring-and-troubleshooting/createpublicip.gif)
