---
title: "Ativação pós-falha e falhar fazer uma cópia de VMs de Hyper-V replicado para o Azure com a recuperação de Site | Microsoft Docs"
description: "Saiba como efetuar a ativação pós-falha de VMs de Hyper-V para o Azure e haja reativação para o site no local, com o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 72065d01-ed0f-430e-b117-a5885cecd1db
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/15/2017
ms.author: raynew
ms.openlocfilehash: e1cc21661450a983c25b24fe2a6228e26ceecec6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>Ativação pós-falha e falhar fazer uma cópia de VMs de Hyper-V replicado para o Azure

O [do Azure Site Recovery](site-recovery-overview.md) serviço gere e orquestra a replicação, ativação pós-falha e a reativação pós-falha de máquinas no local e máquinas de virtuais (VMs) do Azure.

Este tutorial descreve como efetuar a ativação pós-falha de uma VM de Hyper-V para o Azure. Depois de ter a efetuar a ativação pós-falha, falhar ao seu site no local quando está disponível. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Efetuar a ativação pós-falha de VMs de Hyper-V no local para o Azure
> * Falhar novamente a partir do Azure no local e inicie novamente a replicar para o Azure

## <a name="overview"></a>Descrição geral
Ativação pós-falha e a reativação pós-falha tem duas fases:

1. **Efetuar a ativação pós-falha para o Azure**: falhar máquinas do site no local para o Azure.
2. **Falhar novamente a partir do Azure para o local**: executar uma ativação pós-falha planeada do Azure no local. Após a ativação pós-falha planeada, ativar a replicação inversa, iniciar a replicação no local para o Azure novamente. 


## <a name="fail-over-to-azure"></a>Efetuar a ativação pós-falha para o Azure

### <a name="failover-prerequisites"></a>Pré-requisitos de ativação pós-falha

Para concluir a ativação pós-falha:

- Certifique-se de que concluiu uma [exercício de recuperação após desastre](tutorial-dr-drill-azure.md) para verificar que tudo está a funcionar conforme esperado.
- Opcionalmente, prepare a ligação para VMs do Azure antes da ativação pós-falha.
- Verifique as propriedades da VM antes de executar a ativação pós-falha.

#### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar a ligação para VMs do Azure após a ativação pós-falha

Se pretende ligar as VMs do Azure com RDP após a ativação pós-falha, efetue o seguinte:

##### <a name="azure-vms-running-windows"></a>VMs do Azure com o Windows

1. Para aceder a VMs do Azure através da internet, ative o RDP na VM no local antes da ativação pós-falha. Certifique-se de que TCP e regras UDP estão adicionadas para o **pública** perfil e que o RDP é permitido na **Firewall do Windows** > **aplicações permitidas** para todos os perfis.
2. Para aceder através de VPN de site a site, ative o RDP na máquina no local. RDP deve ser permitido no **Firewall do Windows** -> **aplicações e funcionalidades permitidas** para **domínio e privada** redes. Verifique se a política de SAN do sistema operativo estiver definida como **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135). Não deverá haver nenhum atualizações pendentes do Windows na VM quando acione uma ativação pós-falha. Se existirem, não poderá iniciar sessão para a máquina virtual enquanto a atualização for concluída. 
3. No Windows VM do Azure após a ativação pós-falha, verifique **diagnóstico de arranque** para ver uma captura de ecrã da VM. Se não conseguir ligar, verifique se a VM está em execução e reveja estes [sugestões de resolução de problemas](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


##### <a name="azure-vms-running-linux"></a>VMs do Azure com o Linux

1. Na máquina no local antes da ativação pós-falha, verifique se o serviço de Secure Shell está definido para iniciar automaticamente no arranque do sistema. Certifique-se de que as regras de firewall permitem uma ligação SSH.
2. Na VM do Azure após a ativação pós-falha, permita ligações de entrada na porta SSH para as regras de grupo de segurança de rede na ativação pós-falha VM e para a sub-rede do Azure à qual está ligado.  [Adicionar um endereço IP público](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine) para a VM. Pode verificar **diagnóstico de arranque** para ver uma captura de ecrã da VM.


#### <a name="verify-vm-properties"></a>Verifique as propriedades VM

Verifique as propriedades da VM e certifique-se de que a VM está em conformidade com [requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. No **itens protegidos**, clique em **itens replicados** > VM.
2. No **replicados item** painel, existe um resumo das informações de VM, o estado de funcionamento, e pontos de recuperação mais recente disponível. Clique em **propriedades** para ver mais detalhes.
3. No **computação e rede**, pode modificar o nome do Azure, o grupo de recursos, o tamanho de destino, [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md), e [geridas as definições do disco](#managed-disk-considerations)
4. Pode ver e modificar as definições de rede, incluindo a rede/sub-rede na qual a VM do Azure estarão localizada após a ativação pós-falha e o endereço IP que será atribuído ao mesmo.
5. No **discos**, pode ver informações sobre o sistema operativo e os discos de dados na VM.


### <a name="run-a-failover-from-on-premises-to-azure"></a>Executar uma ativação pós-falha no local para o Azure

Pode executar uma ativação pós-falha planeada ou regular para VMs de Hyper-V

- Utilize uma ativação pós-falha regular para inesperadas. Quando executa esta ativação pós-falha, a recuperação de sites cria uma VM do Azure e for ligado, cópia de segurança. Executar a ativação pós-falha contra um ponto de recuperação específico. Pode ocorrer a perda de dados, consoante o ponto de recuperação a que utilizar.
- Uma ativação pós-falha planeada pode ser utilizada para manutenção ou durante a indisponibilidade esperada. Esta opção fornece a perda de dados. Quando é acionada uma ativação pós-falha planeada, as VMs de origem são encerradas. Os dados não sincronizados estão sincronizados e a ativação pós-falha é acionada.

Este procedimento descreve como executar uma ativação pós-falha regular.


1. No **definições** > **replicado itens** clique na VM > **ativação pós-falha**.
2. No **ativação pós-falha** selecionar um **ponto de recuperação** para efetuar a ativação pós-falha. Pode utilizar uma das seguintes opções:
    - **Mais recente** (predefinição): esta opção primeiro processa todos os dados enviados para a recuperação de sites. Fornece o RPO mais baixo (objetivo de ponto de recuperação) porque a VM do Azure criadas após ativação pós-falha tem todos os dados que foi replicados para o Site de recuperação quando a ativação pós-falha foi acionada.
    - **Mais recentes processados**: esta opção a ativação pós-falha da VM para o ponto de recuperação mais recente, processado pelo Site Recovery. Esta opção fornece um RTO baixa (objetivo de tempo de recuperação), porque nenhum tempo é gasto a processar dados não processados.
    - **Mais recente consistentes da aplicação**: esta opção a ativação pós-falha da VM para o ponto mais recente recuperação consistentes da aplicação processada pela recuperação de sites. 
    - **Personalizada**: especificar um ponto de recuperação.
3. Se estiver a efetuar a ativação pós-falha de VMs de Hyper-V em nuvens VMM do System Center, para o Azure, e para a nuvem, a encriptação de dados é ativada em **chave de encriptação**, selecione o certificado que foi emitido quando ativada a encriptação de dados durante a configuração do fornecedor no o servidor do VMM...
4. Selecione **encerrar a máquina antes de iniciar a ativação pós-falha** se pretender que a recuperação de sites para tentar efetuar um encerramento de máquinas virtuais de origem antes de acionar a ativação pós-falha. Recuperação de site também irá tentar sincronizar os dados no local que ainda não foi enviados para o Azure, antes de acionar a ativação pós-falha. Tenha em atenção que a ativação pós-falha continua, mesmo se falha de encerramento. Pode seguir o progresso de ativação pós-falha no **tarefas** página.
5. Se preparou ligar à VM do Azure, ligar a validá-lo após a ativação pós-falha.
6. Depois de verificar, **consolidar** a ativação pós-falha. Isto elimina todos os pontos de recuperação disponíveis.

> [!WARNING]
> **Não cancele uma ativação pós-falha em curso**: antes de iniciar a ativação pós-falha, a replicação de VM está parada. Se cancelar uma ativação pós-falha em curso, deixa de ativação pós-falha, mas a VM não irá replicar novamente.  


## <a name="fail-back-from-azure-to-on-premises"></a>Falhar novamente a partir do Azure no local

### <a name="prerequisites-for-failback"></a>Pré-requisitos para reativação pós-falha

Para concluir a reativação pós-falha, certifique-se de que o servidor do site primário VMM server/Hyper-V está ligado ao Site Recovery.


### <a name="run-a-failback-and-reprotect-on-premises-vms"></a>Execute uma reativação pós-falha e proteja VMs no local

Ativação pós-falha do Azure para o site no local e inicie a replicar VMs a partir do site no local para o Azure.

1. No **definições** > **replicado itens** clique na VM > **ativação pós-falha planeada**.
2. No **confirmar a ativação de pós-falha planeadas**, certifique-se a direção de ativação pós-falha (a partir do Azure) e selecione as localizações de origem e de destino. 
3. No **sincronização de dados**, especifique como deseja sincronizar:
    - **Sincronizar os dados antes da ativação pós-falha (sincronizar apenas alterações de delta)**— esta opção minimiza o período de indisponibilidade VM, porque sincroniza-se sem encerrar a VM. Eis o que faz:
        1. Guarda um instantâneo da VM do Azure e copia-o para o anfitrião de Hyper-V no local. A VM mantém em execução no Azure.
        2. Será encerrado a VM do Azure, para que não existem novas alterações ocorrem não existe. O conjunto final de alterações são transferidas para o servidor no local e a VM no local é iniciada.
    - **Sincronizar os dados durante a ativação pós-falha apenas (transferência completa)**— Utilize esta opção se tiver sido executar no Azure durante muito tempo. Esta opção é mais rápida, uma vez que vamos esperar várias alterações de disco e não demora tempo nos cálculos de soma de verificação. Esta opção efetua uma transferência de disco. Também é útil quando a VM no local foi eliminada.
4. Se estiver a efetuar a ativação pós-falha de VMs de Hyper-V em nuvens VMM do System Center, para o Azure, e para a nuvem, a encriptação de dados é ativada em **chave de encriptação**, selecione o certificado que foi emitido quando ativada a encriptação de dados durante a configuração do fornecedor no o servidor VMM.
5. Inicie a ativação pós-falha. Pode seguir o progresso de ativação pós-falha no **tarefas** separador.
6. Se tiver selecionado para sincronizar os dados antes da ativação pós-falha, após a sincronização inicial de dados é efetuada e estará pronto encerrar o VMs do Azure, clique em **tarefas** > nome da tarefa de ativação pós-falha planeada > **concluir a ativação pós-falha**. Isto será encerrado a VM do Azure, transfere a mais recente alterações no local e começa a VM no local.
7. Inicie sessão para a VM no local para verificar que se está disponível como esperado.
8. A VM no local agora está num Estado de consolidar pendente. Clique em **consolidação**, para confirmar a ativação pós-falha. Esta ação elimina as VMs do Azure e os respetivos discos e prepara a VM no local para a replicação inversa.
9. Iniciar a replicação da VM no local para o Azure, ativar **inverso replicar**.


> [!NOTE]
> A replicação inversa replica apenas as alterações que ocorreram desde que foi desativada a VM do Azure e são enviadas apenas alterações de delta.

