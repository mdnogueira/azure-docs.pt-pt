---
title: "Reativação pós-falha no Azure Site Recovery para máquinas virtuais Hyper-v | Microsoft Docs"
description: "O Azure Site Recovery coordena a replicação, ativação pós-falha e recuperação de máquinas virtuais e servidores físicos. Saiba mais sobre a reativação pós-falha a partir do Azure datacenter no local."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: rajanaki
ms.openlocfilehash: fafaf3f55f07741d438a06e58713d57d465b1137
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="failback-in-site-recovery-for-hyper-v-virtual-machines"></a>Reativação pós-falha no Site Recovery para máquinas virtuais de Hyper-V

Este artigo descreve como máquinas virtuais de reativação pós-falha protegidos pela recuperação de sites.

## <a name="prerequisites"></a>Pré-requisitos
1. Certifique-se de que o servidor do site primário VMM server/Hyper-V está ligado.
2. Deve efetuou **consolidar** na máquina virtual.

## <a name="why-is-there-no-button-called-failback"></a>Por que motivo não é não existe nenhum botão chamado reativação pós-falha?
No portal, não há nenhum gesto explícito chamado reativação pós-falha. Reativação pós-falha é um passo onde pode voltar para o site primário. Por definição, a ativação pós-falha é quando a ativação pós-falha as máquinas virtuais do primary(on-premises) site recovery (Azure) e a reativação pós-falha é quando a ativação pós-falha as máquinas virtuais de recuperação de volta para principal.

Quando seleciona uma ativação pós-falha, o painel informa sobre a direção da tarefa. Se a direção do Azure no local, é uma reativação pós-falha.

## <a name="why-is-there-only-a-planned-failover-gesture-to-failback"></a>Por que motivo existe apenas um gesto de ativação pós-falha planeada para reativação pós-falha?
Azure é um ambiente altamente disponível e as máquinas virtuais estão sempre disponíveis. Reativação pós-falha é uma atividade planeada onde optar por adotar um pequeno período de inatividade para que as cargas de trabalho podem começar a executar novamente no local. Isto espera sem perda de dados. Por conseguinte, está disponível apenas um gesto de ativação pós-falha planeada, o que irá desligar as VMs no Azure, transferir as alterações mais recentes e certifique-se de que não há nenhuma perda de dados.

## <a name="do-i-need-a-process-server-in-azure-to-failback-to-hyper-v"></a>É necessário um servidor de processos no Azure para reativação pós-falha para o Hyper-v?
Não, um servidor de processos é necessário apenas quando estiver a proteger máquinas virtuais VMware. Não existem componentes adicionais são necessários para ser implementada quando proteger/reativação pós-falha de máquinas virtuais de Hyper-v.

## <a name="initiate-failback"></a>Iniciar a reativação pós-falha
Após a ativação pós-falha do primário para a localização secundária, máquinas virtuais replicadas não estão protegidas pela recuperação de sites e a localização secundária está agora a funcionar como a localização do Active Directory. Siga estes procedimentos para falhar novamente para o site primário original. Este procedimento descreve como executar uma ativação pós-falha planeada para um plano de recuperação. Em alternativa, pode executar a ativação pós-falha para uma única máquina virtual **máquinas virtuais** separador.

1. Selecione **planos de recuperação** > *recoveryplan_name*. Clique em **ativação pós-falha** > **a ativação pós-falha planeada**.
2. No * * confirmar a ativação de pós-falha planeadas * * página, escolha as localizações de origem e de destino. Tenha em atenção a direção de ativação pós-falha. Se a ativação pós-falha do primário funcionado como esperado e todas as máquinas virtuais estão na localização secundária que esta é apenas para informação.
3. Se estiver a efetuar a reativação a partir do Azure, selecione as definições no **sincronização de dados**:

   * **Sincronizar os dados antes da ativação pós-falha (sincronizar as alterações de delta apenas)**— esta opção minimiza o tempo de inatividade para as máquinas virtuais como sincroniza-se sem encerrá-los. Faz os seguintes passos:
     * Fase 1: Guarda o instantâneo da máquina virtual no Azure e copia-o para o anfitrião de Hyper-V no local. A máquina continua em execução no Azure.
     * Fase 2: Será encerrado a máquina virtual no Azure, para que não existem novas alterações ocorrem não existe. O conjunto final de diferenças de alterações são transferidas para o servidor no local e uma máquina virtual no local é iniciado.

    - **Sincronizar os dados durante a ativação pós-falha apenas (transferência completa)**— Utilize esta opção se tiver sido executar no Azure durante muito tempo. Esta opção é mais rápida porque esperamos que a maioria do disco foi alterado e não queremos gaste tempo no cálculo de soma de verificação. Executa uma transferência do disco. Também é útil quando a máquina virtual no local foi eliminada.

    >[!NOTE]
    >Recomendamos que utilize esta opção se já foi executar do Azure para o tempo (um mês ou mais) ou de máquina virtual no local foi eliminada. Esta opção não efetuar qualquer cálculos de soma de verificação.


4. Se a encriptação de dados é ativada para a nuvem, no **chave de encriptação** selecionar o certificado que foi emitido quando ativar a encriptação de dados durante a instalação do fornecedor no servidor do VMM.
5. Inicie a ativação pós-falha. Pode seguir o progresso de ativação pós-falha no **tarefas** separador.
6. Se tiver selecionado a opção para sincronizar os dados antes da ativação pós-falha, assim que a sincronização inicial de dados estiver concluída e estará pronto encerrar as máquinas virtuais no Azure, clique em **tarefas** o nome da tarefa de ativação pós-falha planeada **Concluir a ativação pós-falha**. Isto será encerrado a máquina do Azure, transfere as alterações mais recentes para as máquinas no local e começa a VM no local.
7. Pode agora o registo para a máquina virtual para validar está disponível como esperado.
8. A máquina virtual está num Estado de confirmação pendente. Clique em **consolidação** para consolidar a ativação pós-falha.
9. Agora para concluir a reativação pós-falha, clique em **inverso replicar** para começar a proteger a máquina virtual no site primário.

## <a name="failback-to-an-alternate-location"></a>Reativação pós-falha para uma localização alternativa
Se tiver implementado proteção entre um [site Hyper-V e o Azure](site-recovery-hyper-v-site-to-azure.md) tem capacidade para reativação pós-falha a partir do Azure para uma localização alternativa no local. Isto é útil se precisar de definir o novo hardware no local. Eis como fazê-lo.

1. Se estiver a configurar o novo hardware de instalar o Windows Server 2012 R2 e a função Hyper-V no servidor.
2. Crie um comutador de rede virtual com o mesmo nome que tinha no servidor original.
3. Selecione **itens protegidos** -> **grupo de proteção**  ->  <ProtectionGroupName>  ->  <VirtualMachineName> pretende voltar a falhar e selecione **planeada Ativação pós-falha**.
4. No **confirmar a ativação de pós-falha planeadas** selecione **criar no local máquina virtual se não existir**.
5. Nome de anfitrião e * * selecione o novo servidor de anfitrião de Hyper-V no qual pretende colocar a máquina virtual.
6. Sincronização de dados, recomendamos que selecione a opção **sincronizar os dados antes da ativação pós-falha**. Isto minimiza o tempo de inatividade para as máquinas virtuais como sincroniza-se sem encerrá-los. Faz o seguinte:

   * Fase 1: Guarda o instantâneo da máquina virtual no Azure e copia-o para o anfitrião de Hyper-V no local. A máquina continua em execução no Azure.
   * Fase 2: Será encerrado a máquina virtual no Azure, para que não existem novas alterações ocorrem não existe. O conjunto final de alterações são transferidas para o servidor no local e as máquinas no local é iniciada.
7. Clique na marca de verificação para iniciar a ativação pós-falha (reativação pós-falha).
8. Depois de concluída a sincronização inicial e estará pronto encerrar a máquina virtual no Azure, clique em **tarefas** > <planned failover job> > **concluir a ativação pós-falha**. Isto será encerrado a máquina do Azure, transfere as alterações mais recentes para as máquinas no local e é iniciada.
9. Pode iniciar sessão para a máquina virtual no local Certifique-se de que tudo está a funcionar conforme esperado. Em seguida, clique em **consolidar** para concluir a ativação pós-falha.
10. Clique em **inverso replicar** para começar a proteger a máquina virtual no local.

    > [!NOTE]
    > Se cancelar a tarefa de reativação pós-falha enquanto este estiver no passo de sincronização de dados, a VM no local será-se num estado danificado. Isto acontece porque a sincronização de dados copia os dados mais recentes dos discos de VM do Azure para os discos de dados no local e até a sincronização seja concluída, os dados do disco não podem estar num estado consistente. Se a VM no local é iniciada após a sincronização de dados foi cancelada, não podem arrancar. Reacionar a ativação pós-falha para concluir a sincronização de dados.

## <a name="time-taken-to-failback"></a>Tempo decorrido para reativação pós-falha
O tempo necessário para concluir a sincronização de dados e arrancar a máquina virtual depende de vários fatores. Para dar uma aprofundadas sobre o tempo decorrido, vamos explicar o que acontece durante a sincronização de dados.

Sincronização de dados tira um instantâneo dos discos da máquina virtual e inicia a verificação bloco a bloco e calcula a soma de verificação. Isto calculado soma de verificação é enviada para o local para a comparação com a soma de verificação no local do bloco do mesmo. No caso de somas de verificação corresponderem, o bloco de dados não é transferido. Se não corresponderem, o bloco de dados é transferido para o local. Este tempo de transferência depende a largura de banda disponível. A velocidade da soma de verificação é GBs alguns por min. 

Para acelerar a transferência de dados, pode configurar o agente MARS utilizar mais threads para parallelize a transferência. Consulte o [documentar aqui](https://support.microsoft.com/en-us/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) sobre como alterar os threads de transferência no agente.


## <a name="next-steps"></a>Passos Seguintes

Depois de concluir a tarefa de reativação pós-falha, **consolidar** a máquina virtual. Consolidação elimina a máquina virtual do Azure e os respetivos discos e prepara a VM para ser protegida novamente.

Depois de **consolidar**, pode iniciar o *inverso replicar*. Esta ação inicia a proteger a máquina virtual no local para o Azure. Apenas esta ação irá replicar as alterações, uma vez que a VM foi desativada no Azure e, por conseguinte, envia apenas alterações diferenciais.
