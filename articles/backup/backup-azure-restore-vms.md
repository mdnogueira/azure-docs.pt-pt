---
title: "Restaurar uma máquinas virtuais a partir de cópia de segurança | Microsoft Docs"
description: "Saiba como restaurar uma máquina virtual do Azure a partir de um ponto de recuperação"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
keywords: "restaurar a cópia de segurança; como restaurar; ponto de recuperação;"
ms.assetid: fed877b3-b496-49fb-99e4-653be7c23e3a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: trinadhk; jimpark;
ms.openlocfilehash: fc52c909df5e91741ec1fa21fb911487be039fdc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="restore-virtual-machines-in-azure"></a>Restaurar máquinas virtuais no Azure
> [!div class="op_single_selector"]
> * [Restaurar VMs no portal do Azure](backup-azure-arm-restore-vms.md)
> * [Restaurar VMs no portal clássico](backup-azure-restore-vms.md)
>
>

Restaure uma máquina virtual para uma nova VM a partir de cópias de segurança armazenadas num cofre do Backup do Azure com os seguintes passos.

> [!IMPORTANT]
> Agora pode atualizar os cofres de Cópia de Segurança para cofres dos Serviços de Recuperação. Para obter detalhes, veja o artigo [Atualizar um cofre de Cópia de Segurança para um cofre dos Serviços de Recuperação](backup-azure-upgrade-backup-to-recovery-services.md). Recomendamos que atualize os cofres de Cópia de Segurança para cofres dos Serviços de Recuperação.<br/> **15 de outubro de 2017**, não será possível continuar a utilizar o PowerShell para criar cofres de Cópia de segurança. <br/> **A partir de 1 de novembro de 2017**:
>- Quaisquer cofres de Cópia e Segurança restantes serão atualizados automaticamente para cofres dos Serviços de Recuperação.
>- Não vai conseguir aceder aos dados de cópia de segurança no portal clássico. Em vez disso, utilize o portal do Azure para aceder aos dados de cópia de segurança em cofres dos Serviços de Recuperação.
>

## <a name="restore-workflow"></a>Restaurar fluxo de trabalho
### <a name="step-1-choose-an-item-to-restore"></a>Passo 1: Selecione um item a restaurar
1. Navegue para o **itens protegidos** separador e selecione a máquina virtual que pretende restaurar para uma nova VM.

    ![Itens protegidos](./media/backup-azure-restore-vms/protected-items.png)

    O **ponto de recuperação** coluna no **itens protegidos** página irá indicar o número de pontos de recuperação para uma máquina virtual. O **ponto de recuperação mais recente** coluna indica a hora da cópia de segurança mais recente do que uma máquina virtual pode ser restaurada.
2. Clique em **restaurar** para abrir o **restaurar um Item** assistente.

    ![Restaurar um item](./media/backup-azure-restore-vms/restore-item.png)

### <a name="step-2-pick-a-recovery-point"></a>Passo 2: Escolha um ponto de recuperação
1. No **selecionar um ponto de recuperação** ecrã, pode restaurar a partir do ponto de recuperação mais recente, ou de um ponto anterior no tempo. A opção predefinida selecionada quando é aberto o assistente é *ponto de recuperação mais recente*.

    ![Selecione um ponto de recuperação](./media/backup-azure-restore-vms/select-recovery-point.png)
2. Para escolher um ponto anterior no tempo, escolha o **selecione data** opção na lista pendente e selecione uma data no controlo de calendário, clicando no **ícone de calendário**. No controlo da, todas as datas com pontos de recuperação são preenchidas com uma ligeira shade cinzento em são selecionáveis pelo utilizador.

    ![Selecione uma data](./media/backup-azure-restore-vms/select-date.png)

    Assim que clicar numa data no controlo de calendário, os pontos de recuperação disponível em que data será mostrada na tabela de pontos de recuperação abaixo. O **tempo** coluna indica o tempo em que o instantâneo foi tirado. O **tipo** coluna apresenta o [consistência](https://azure.microsoft.com/documentation/articles/backup-azure-vms/#consistency-of-recovery-points) dos pontos de recuperação. O cabeçalho de tabela mostra o número de pontos de recuperação disponíveis nesse dia parênteses.

    ![Pontos de recuperação](./media/backup-azure-restore-vms/recovery-points.png)
3. Selecione o ponto de recuperação do **pontos de recuperação** uma tabela e clique na seta seguinte para aceder ao ecrã seguinte.

### <a name="step-3-specify-a-destination-location"></a>Passo 3: Especificar uma localização de destino
1. No **selecione Restaurar instância** ecrã especificar detalhes de onde pretende restaurar a máquina virtual.

   * Especifique o nome de máquina virtual: no serviço de nuvem especificada, o nome da máquina virtual deve ser exclusivo. Não suportamos escrita excessiva de VM existente.
   * Selecione um serviço em nuvem para a VM: Isto é obrigatório para criar uma VM. Pode optar por utilizar um serviço em nuvem existente ou criar um novo serviço de nuvem.

        Foi selecionado qualquer nome de serviço de nuvem deve ser globalmente exclusivo. Normalmente, o nome do serviço em nuvem obtém associado a um URL de destinado ao público no formato [cloudservice]. cloudapp.net. Azure não irá permitir-lhe criar um novo serviço em nuvem se o nome já foi utilizado. Se optar por criar um novo serviço de nuvem, esta terá o mesmo nome que a máquina virtual – nesse caso o nome da VM selecionado deve ser exclusivo ser aplicado ao serviço de nuvem associado.

        Apenas a apresentar serviços em nuvem e de redes virtuais que não estão associados a quaisquer grupos de afinidades nos detalhes de instância de restauro. [Saiba mais](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
2. Selecione uma conta de armazenamento para a VM: Isto é obrigatório para criar a VM. Pode selecionar a partir de contas do storage existentes na mesma região que o Cofre de cópia de segurança do Azure. As contas de armazenamento que são zona redundante ou do tipo de armazenamento Premium não é suportada.

    Se não houver nenhuma conta de armazenamento com configuração suportada, crie uma conta de armazenamento de uma configuração suportada antes de iniciar a operação de restauro.

    ![Selecione uma rede virtual](./media/backup-azure-restore-vms/restore-sa.png)
3. Selecione uma rede Virtual: A rede virtual (VNET) para a máquina virtual deve ser selecionada no momento da criação da VM. O restauro IU mostra todas as VNETs dentro desta subscrição que podem ser utilizadas. Não é obrigatório para selecionar uma VNET para a VM restaurada – poderá ligar à máquina virtual restaurada através da internet, mesmo que não se aplica a VNET.

    Se o serviço em nuvem selecionado está associado uma rede virtual, não é possível alterar a rede virtual.

    ![Selecione uma rede virtual](./media/backup-azure-restore-vms/restore-cs-vnet.png)
4. Selecione uma sub-rede: caso a VNET tem sub-redes, por predefinição a primeira sub-rede será selecionada. Escolha a sub-rede à sua escolha entre as opções de lista pendente. Para os detalhes da sub-rede, aceda a extensão de redes no [home page do portal](https://manage.windowsazure.com/), aceda a **redes virtuais** e selecione a rede virtual e desagregar para configurar para ver os detalhes da sub-rede.

    ![Selecione uma sub-rede](./media/backup-azure-restore-vms/select-subnet.png)
5. Clique em de **submeter** ícone no Assistente para submeter os detalhes e criar uma tarefa de restauro.

## <a name="track-the-restore-operation"></a>Controlar a operação de restauro
Depois de ter todas as informações para o Assistente de restauro de entrada e submetido-cópia de segurança do Azure irá tentar criar uma tarefa para controlar a operação de restauro.

![Criar uma tarefa de restauro](./media/backup-azure-restore-vms/create-restore-job.png)

Se a criação de tarefas for bem-sucedido, verá uma notificação de alerta a indicar que a tarefa é criada. Pode obter mais detalhes clicando a **ver tarefa** botão que será apresentada a **tarefas** separador.

![Restaurar tarefa criada](./media/backup-azure-restore-vms/restore-job-created.png)

Assim que a operação de restauro estiver concluída, será marcada como concluído no **tarefas** separador.

![Restaurar tarefa concluída](./media/backup-azure-restore-vms/restore-job-complete.png)

Depois de restaurar a máquina virtual poderá ter de voltar a instalar as extensões existente na original VM e [modificar os pontos finais](../virtual-machines/windows/classic/setup-endpoints.md) para a máquina virtual no portal do Azure.

## <a name="post-restore-steps"></a>Passos de pós-restauro
Se estiver a utilizar uma distribuição de Linux baseado em nuvem-init, tais como Ubuntu, por motivos de segurança, palavra-passe será bloqueada após o restauro. Utilize extensão VMAccess na VM restaurada para [repor a palavra-passe](../virtual-machines/linux/classic/reset-access.md). Recomendamos a utilização de chaves SSH nestes distribuições para evitar a repor a palavra-passe post restauro.

## <a name="backup-for-restored-vms"></a>Cópia de segurança para VMs restauradas
Caso tenha restaurado VM ao mesmo serviço em nuvem com o mesmo nome como originalmente um cópia de segurança VM, cópia de segurança irá continuar com o restauro de post VM. Se tiver restaurado VM a um serviço de nuvem diferente ou especificar um nome diferente para a VM restaurada, este será tratado como uma nova VM e terá de cópia de segurança de configuração de VM restaurada.

## <a name="restoring-a-vm-during-azure-datacenter-disaster"></a>Restaurar uma VM durante desastre de centro de dados do Azure
Cópia de segurança do Azure permite restaurar cópias de segurança de VMs para o Centro de dados emparelhado no caso dos dados primários center onde as VMs estão em execução experiências desastre e configurou o Cofre de cópia de segurança para ser georredundante. Durante a tais cenários, tem de selecionar uma conta de armazenamento que está presente no Centro de dados emparelhado e rest do processo de restauro permanece o mesmo. Cópia de segurança do Azure utiliza o serviço de computação de georreplicação emparelhada para criar a máquina virtual restaurada. Saiba mais sobre [resiliência do Centro de dados do Azure](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md)

## <a name="restoring-domain-controller-vms"></a>Restaurar VMs do controlador de domínio
Cópia de segurança de máquinas de virtuais do controlador de domínio (DC) é um cenário suportado com o Backup do Azure. No entanto, deve ter cuidado durante o processo de restauro. O processo de restauro correto depende a estrutura do domínio. Caso mais simples tem um único controlador de domínio num domínio único. Mais frequentemente para cargas de produção, terá um único domínio com vários controladores de domínio, talvez com alguns DCs no local. Por fim, pode ter uma floresta com vários domínios.

De uma perspetiva de Active Directory a VM do Azure é como qualquer outra VM num hipervisor suportado Moderno. A principal diferença com hipervisores no local é o que não há nenhuma consola da VM no Azure. Uma consola é necessária para determinados cenários tais como recuperar utilizando uma cópia de segurança do tipo de recuperação Bare bare Metal (BMR). No entanto, o restauro de VM do cofre da cópia de segurança é uma substituição completa para a BMR. Modo de restauro de diretório Active Directory (DSRM), também está disponível, pelo que todos os cenários de recuperação do Active Directory são viável. Para obter mais informações em segundo plano, verifique [cópia de segurança e restauro considerações para controladores de domínio virtualizado](https://technet.microsoft.com/en-us/library/virtual_active_directory_domain_controller_virtualization_hyperv(v=ws.10).aspx#backup_and_restore_considerations_for_virtualized_domain_controllers) e [planear a recuperação de floresta do Active Directory](https://technet.microsoft.com/en-us/library/planning-active-directory-forest-recovery(v=ws.10).aspx).

### <a name="single-dc-in-a-single-domain"></a>Único DC num domínio único
A VM pode ser restaurada (como outra VM) do Azure portal ou com o PowerShell.

### <a name="multiple-dcs-in-a-single-domain"></a>Vários controladores de domínio num domínio único
Quando os restantes DCs do mesmo domínio podem ser contactados através da rede, o DC pode ser restaurado como qualquer VM. Se o último DC restantes no domínio, ou se é efetuada uma recuperação numa rede isolada, um procedimento de recuperação de floresta tem de ser seguido.

### <a name="multiple-domains-in-one-forest"></a>Vários domínios numa floresta
Quando os restantes DCs do mesmo domínio podem ser contactados através da rede, o DC pode ser restaurado como qualquer VM. No entanto, todos os outros casos é recomendada uma recuperação de floresta.

<!--- WK: this following original supportability statement is incorrect, taking it out.
The challenge arises because DSRM mode is not present in Azure. So to restore such a VM, you cannot use the Azure portal. The only supported restore mechanism is disk-based restore using PowerShell.

> [!WARNING]
> For Domain Controller VMs in a multi-DC environment, do not use the Azure portal for restore! Only PowerShell based restore is supported
>
>

Read more about the [USN rollback problem](https://technet.microsoft.com/library/dd363553) and the strategies suggested to fix it.
--->

## <a name="restoring-vms-with-special-network-configurations"></a>Restaurar VMs com configurações de rede especiais
Cópia de segurança do Azure suporta a cópia de segurança seguintes configurações de rede especiais de máquinas virtuais.

* VMs no balanceador de carga (interno e externo)
* VMs com vários IPs reservados
* VMs com vários NICs

Estas configurações mandatar seguintes considerações ao restaurá-los.

> [!TIP]
> Utilize o fluxo de restauro do PowerShell com base para recriar a configuração de rede especiais de restauro de post de VMs.
>
>

### <a name="restoring-from-the-ui"></a>Restaurar a partir da IU:
Enquanto restaurava a partir da IU, **sempre escolher um novo serviço em nuvem**. Tenha em atenção que uma vez que o portal só leva obrigatório parâmetros durante o fluxo de restauro, VMs restaurados com autoridade utilizando a IU irão perder a configuração de rede especiais que possuem. Por outras palavras, restaurar VMs ficará VMs normais sem configuração de Balanceador de carga ou várias NIC ou vários IP reservado.

### <a name="restoring-from-powershell"></a>Restaurar a partir do PowerShell:
PowerShell tem a capacidade de restaurar apenas os discos VM a partir de cópia de segurança e não criar a máquina virtual. Esta ação é útil quando o restauro de máquinas virtuais que requerem configurações de rede especiais mencionadas acima.

Para poder recriar completamente os discos de restauro de post de máquina virtual, siga estes passos:

1. Restaurar os discos da utilização do Cofre de cópia de segurança [PowerShell de cópia de segurança do Azure](backup-azure-vms-classic-automation.md#restore-an-azure-vm)
2. Criar a configuração VM necessária para o Balanceador de carga / IP reservado vários do NIC/múltiplos, utilizando os cmdlets do PowerShell e utilize-o para criar a VM de configuração pretendido.

   * Criar a VM no serviço em nuvem com [Balanceador de carga interno](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)
   * Criar a VM para ligar ao [Internet com o Balanceador de carga](https://azure.microsoft.com/en-us/documentation/articles/load-balancer-internet-getstarted/)
   * Criar a VM com [vários NICs](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/)
   * Criar a VM com [vários reservado IPs](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/)

## <a name="next-steps"></a>Passos seguintes
* [Resolução de problemas de erros](backup-azure-vms-troubleshoot.md#restore)
* [Gerir máquinas virtuais](backup-azure-manage-vms.md)
