---
title: "Cópia de segurança do Azure: Restaurar máquinas virtuais utilizando o portal do Azure | Microsoft Docs"
description: "Restaurar uma máquina virtual do Azure a partir de um ponto de recuperação utilizando o portal do Azure"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "restaurar a cópia de segurança; como restaurar; ponto de recuperação;"
ms.assetid: 372b87c6-3544-4dc5-bbc9-c742ca502159
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: markgal;trinadhk;
ms.openlocfilehash: b659d5dc894afd2beef529c6b4f736e888b4540e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-azure-portal-to-restore-virtual-machines"></a>Utilizar o portal do Azure para monitorizar máquinas virtuais
> [!div class="op_single_selector"]
> * [Restaurar VMs no portal clássico](backup-azure-restore-vms.md)
> * [Restaurar VMs no portal do Azure](backup-azure-arm-restore-vms.md)
>
>

Proteger os seus dados, efetuando os instantâneos dos seus dados em intervalos definidos. Estes instantâneos são conhecidos como pontos de recuperação e que está a ser armazenadas numa cofres dos serviços de recuperação. Se for necessário reparar ou reconstrua uma máquina virtual (VM), pode restaurar a VM a partir de qualquer um dos pontos de recuperação guardado. Quando restaurar um ponto de recuperação, pode:

* Crie uma nova VM, que é uma representação de ponto no tempo da sua VM de cópia de segurança.
* Restaurar discos e utilizar o modelo que vem com o processo para personalizar a VM restaurada ou Efetue uma recuperação de ficheiros individuais. 

Este artigo explica como restaurar uma VM para uma nova VM ou restaurar todos os discos de cópia de segurança. Para a recuperação de ficheiros individuais, consulte [recuperar ficheiros a partir de uma cópia de segurança de VM do Azure](backup-azure-restore-files-from-vm.md).

![Três formas para restaurar a partir de cópia de segurança VM](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)

> [!NOTE]
> O Azure tem dois modelos de implementação para criar e trabalhar com recursos: [do Azure Resource Manager e clássico](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo fornece as informações e procedimentos utilizados para restaurar implementado VMs utilizando o modelo do Resource Manager.
>
>

Restaurar uma VM ou todos os discos de VM a cópia de segurança envolve dois passos:

* Selecione um ponto de restauro do restauro.
* Selecione o tipo de restauro, criar uma nova VM ou restaurar os discos e especifique os parâmetros necessários. 

## <a name="select-a-restore-point-for-restore"></a>Selecione um ponto de restauro do restauro
1. Inicie sessão no [Portal do Azure](http://portal.azure.com/).

2. No menu do Azure, selecione **procurar**. Na lista de serviços, escreva **dos serviços de recuperação**. A lista de serviços ajustável à qual é o tipo. Quando vir **cofres dos serviços de recuperação**, selecione-o.

    ![Cofre dos Serviços de Recuperação](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    É apresentada a lista de cofres na subscrição.

    ![Lista de serviços de recuperação cofres dos](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
3. Na lista, selecione o Cofre associado a VM que pretende restaurar. Quando seleciona o cofre, abre o dashboard.

    ![Selecionar o Cofre dos serviços de recuperação](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade.png)
4. No dashboard do cofre, no **itens de cópia de segurança** mosaico, selecione **Virtual Machines do Azure**.

    ![dashboard do Cofre](./media/backup-azure-arm-restore-vms/vault-dashboard.png)

    O **itens de cópia de segurança** painel abre-se e apresenta a lista de VMs do Azure.

    ![lista de VMs no Cofre](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault.png)
5. Na lista, selecione uma VM para abrir o dashboard. Abre o dashboard VM para a área de monitorização, que contém o **restaurar pontos** mosaico.

    ![Restaurar pontos](./media/backup-azure-arm-restore-vms/vm-blade.png)
6. No menu do dashboard de VM, selecione **restaurar**.

    ![Botão Restaurar](./media/backup-azure-arm-restore-vms/vm-blade-menu-restore.png)

    O **restaurar** abre o painel.

    ![restaurar painel](./media/backup-azure-arm-restore-vms/restore-blade.png)
7. No **restaurar** painel, selecione **ponto de restauro**. O **selecionar ponto de restauro** abre o painel.

    ![Selecione o ponto de restauro](./media/backup-azure-arm-restore-vms/recovery-point-selector.png)

    Por predefinição, a caixa de diálogo apresenta todos os pontos de restauro dos últimos 30 dias. Utilize o **filtro** alterar o intervalo de tempo dos pontos de restauro apresentado. Por predefinição, os pontos de restauro de todos os consistencies são apresentados. Modificar o **restaurar todos os pontos de** filtro para selecionar uma consistência de ponto de restauro específico. Para mais informações sobre cada tipo de ponto de restauro, consulte [consistência dos dados](backup-azure-vms-introduction.md#data-consistency).

    Opções de consistência de ponto de restauro:

     * Pontos de restauro consistentes de falhas
     * Pontos de restauro consistentes da aplicação
     * Pontos de restauro consistentes do sistema de ficheiros
     * Todos os pontos de restauro

8. Escolha um ponto de restauro e selecione **OK**.

    ![Opções de ponto de restauro](./media/backup-azure-arm-restore-vms/select-recovery-point.png)

    O **restaurar** painel mostra que o ponto de restauro está definido.

9. Se ainda não existe, está a passar para o **restaurar** painel. Certifique-se de que um [é selecionado o ponto de restauro](#select-restore-point-for-restore)e selecione **restauro configuração**. O **restauro configuração** abre o painel.

## <a name="choose-a-vm-restore-configuration"></a>Escolha uma configuração de restauro de VM
Depois de selecionar o ponto de restauro, escolha uma configuração de restauro de VM. Para configurar a VM restaurada, pode utilizar o portal do Azure ou o PowerShell.

1. Se ainda não existe, está a passar para o **restaurar** painel. Certifique-se de que um [é selecionado o ponto de restauro](#select-restore-point-for-restore)e selecione **restauro configuração**. O **restauro configuração** abre o painel.

    ![Restaurar o Assistente de configuração](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard-recovery-type.png)
2. No **restauro configuração** painel, tem duas opções:

   * **Criar máquina virtual**

   * **Restaurar discos**

O portal fornece um **criação rápida** opção para uma VM restaurada. Para personalizar a configuração de VM ou os nomes de recursos criados como parte da criação de uma nova opção VM, utilize o PowerShell ou o portal para restaurar os discos de cópia de segurança. Utilize comandos do PowerShell para anexá-los à sua escolha de configuração de VM. Ou pode utilizar o modelo que vem com discos restaurados para personalizar a VM restaurada. Para obter informações sobre como restaurar uma VM que tem vários NICs ou é um balanceador de carga, consulte [restaurar uma VM com configurações de rede especiais](#restore-a vm-with-special-network-configurations). Se a VM do Windows utiliza [HUB licenciamento](../virtual-machines/windows/hybrid-use-benefit-licensing.md), restaurar os discos e utilizar o PowerShell/modelo conforme especificado neste artigo para criar a VM. Certifique-se de que especifica o **tipo de licença** como "Windows_Server" ao criar a VM para beneficiar vantagens HUB na VM restaurada. 
 
## <a name="create-a-new-vm-from-a-restore-point"></a>Criar uma nova VM a partir de um ponto de restauro
1. Se não estiver já existe, [selecionar um ponto de restauro](#restore-a vm-with-special-network-configurations) antes de começar a criar uma nova VM a partir de um ponto de restauro. Depois de selecionar um ponto de restauro no **restauro configuração** painel, introduza ou selecione os valores para cada um dos seguintes campos:

    a. **Restaurar tipo**. Crie uma máquina virtual.

    b. **Nome da máquina virtual**. Forneça um nome para a VM. O nome tem de ser exclusivo para o grupo de recursos (para uma VM implementadas no Azure Resource Manager) ou o serviço em nuvem (para uma VM clássico). Não é possível substituir a VM se já existe na subscrição.

    c. **Grupo de recursos**. Utilize um grupo de recursos existente ou crie um novo. Se estiver a restaurar uma VMS clássicas, utilize este campo para especificar o nome de um novo serviço em nuvem. Se estiver a criar um novo serviço de nuvem/grupo de recursos, o nome deve ser globalmente exclusivo. Normalmente, o nome do serviço em nuvem está associado um URL de destinado ao público: por exemplo, [cloudservice]. cloudapp.net. Se tentar utilizar um nome para o serviço de nuvem/grupo de recursos de nuvem já em utilização, o Azure atribui o serviço de nuvem/grupo de recursos o mesmo nome que a VM. Azure apresenta recursos grupos/cloud services e as VMs que não associadas a quaisquer grupos de afinidade. Para obter mais informações, consulte [como migrar a partir de grupos de afinidade para uma rede virtual regional](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

    d. **Rede virtual**. Selecione a rede virtual ao criar a VM. O campo fornece todas as redes virtuais associadas à subscrição. O grupo de recursos da VM é apresentado no parênteses.

    e. **Sub-rede**. Se a rede virtual tem sub-redes, a primeira sub-rede está selecionada por predefinição. Se existirem sub-redes adicionais, selecione a sub-rede que pretende.

    f. **Conta de armazenamento**. Este menu lista as contas do storage na mesma localização que o Cofre dos serviços de recuperação. As contas de armazenamento que são zona redundante não são suportadas. Se não houver nenhuma conta de armazenamento com a mesma localização que o Cofre dos serviços de recuperação, tem de criar um antes de iniciar a operação de restauro. Tipo de replicação de uma conta de armazenamento é apresentado no parênteses.

    ![Restaurar o Assistente de configuração](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

    > [!NOTE]
    > * Se restaurar uma VM implementadas no Resource Manager, tem de identificar uma rede virtual. Uma rede virtual é opcional para uma VM clássica.

    > * Se restaurar VMs com discos geridos, certifique-se de que a conta de armazenamento selecionada não está ativada para a encriptação de serviço de armazenamento do Azure no seu período de duração.

    > * Consoante o tipo de armazenamento da conta de armazenamento selecionada (premium ou standard), todos os discos restaurados será premium ou discos padrão. Não é atualmente suportado um modo misto de discos ao restaurar.
    >
    >

2. No **restauro configuração** painel, selecione **OK** para finalizar a configuração de restauro. No **restaurar** painel, selecione **restaurar** para acionar a operação de restauro.

## <a name="restore-backed-up-disks"></a>Restaurar os discos de cópia de segurança
Para personalizar a VM que pretende criar a partir de discos de cópia de segurança diferente do que se encontra presente no **restauro configuração** painel, selecione **restaurar discos** como o valor de **restaurar tipo**. Esta opção pede-lhe uma conta do storage onde os discos de cópias de segurança estão a ser copiado. Ao escolher uma conta de armazenamento, selecione uma conta que partilha a mesma localização que o Cofre dos serviços de recuperação. As contas de armazenamento que são zona redundante não são suportadas. Se não houver nenhuma conta de armazenamento com a mesma localização que o Cofre dos serviços de recuperação, tem de criar um antes de iniciar a operação de restauro. Tipo de replicação de uma conta de armazenamento é apresentado no parênteses.

Uma vez concluída a operação de restauro, pode:

* [Utilize o modelo para personalizar a VM restaurada](#use-templates-to-customize-restore-vm)
* [Utilize os discos restaurados para anexar a uma VM existente](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Criar uma nova VM através do PowerShell de discos restaurados](./backup-azure-vms-automation.md#restore-an-azure-vm)

No **restauro configuração** painel, selecione **OK** para finalizar a configuração de restauro. No **restaurar** painel, selecione **restaurar** para acionar a operação de restauro.

![Configuração de recuperação concluída](./media/backup-azure-arm-restore-vms/trigger-restore-operation.png)

## <a name="track-the-restore-operation"></a>Controlar a operação de restauro
Após o acionar a operação de restauro, o serviço de cópia de segurança cria uma tarefa para a operação de restauro de controlo. O serviço de cópia de segurança também cria e apresenta temporariamente a notificação no **notificações** área do portal. Se não vir a notificação, selecione o **notificações** símbolo para ver as notificações.

![Restauro acionado](./media/backup-azure-arm-restore-vms/restore-notification.png)

Para ver a operação enquanto estiver a processar, ou para vê-lo quando terminar, abra o **tarefas de cópia de segurança** lista.

1. No menu do Azure, selecione **procurar**e, na lista de serviços, escreva **dos serviços de recuperação**. A lista de serviços ajustável à qual é o tipo. Quando vir **cofres dos serviços de recuperação**, selecione-o.

    ![Abra o Cofre dos serviços de recuperação](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    É apresentada a lista de cofres na subscrição.

    ![Lista de serviços de recuperação cofres dos](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
2. Na lista, selecione o Cofre associado a VM é restaurada. Quando seleciona o cofre, abre o dashboard.

3. No dashboard do cofre no **tarefas de cópia de segurança** mosaico, selecione **máquinas virtuais do Azure** para apresentar as tarefas associadas ao cofre.

    ![dashboard do Cofre](./media/backup-azure-arm-restore-vms/vault-dashboard-jobs.png)

    O **tarefas de cópia de segurança** painel abre e apresenta a lista de tarefas.

    ![Lista de VMs num cofre](./media/backup-azure-arm-restore-vms/restore-job-in-progress.png)
    
## <a name="use-templates-to-customize-a-restored-vm"></a>Utilizar modelos para personalizar uma VM restaurada
Depois do [concluída a operação de discos de restauro](#Track-the-restore-operation), utilize o modelo que foi gerado como parte da operação de restauro para criar uma nova VM com uma configuração diferente da configuração de cópia de segurança. Também pode utilizá-lo para personalizar os nomes de recursos que foram criados durante o processo de criar uma nova VM a partir de um ponto de restauro. 

> [!NOTE]
> Modelos são adicionados como parte do restauro discos para pontos de recuperação efetuados após 1 de Março de 2017. Se estiver a aplicável para o disco em VMs. Suporte para as VMs de disco gerido estará disponível em versões futuras. 
>
>

Para obter o modelo que foi gerado como parte da opção de discos de restauro:

1. Vá para os detalhes da tarefa de restauro correspondente para a tarefa.

2. No **restaurar os detalhes da tarefa** ecrã, selecione **implementar a modelo** para iniciar a implementação do modelo. 

     ![restaurar tarefa desagregar](./media/backup-azure-arm-restore-vms/restore-job-drill-down.png)
   
3. No **implementar modelo** painel implementação personalizada, para a implementação do modelo de utilização [editar e implementar o modelo](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) ou acrescentar mais personalizações pela [criar um modelo de](../azure-resource-manager/resource-group-authoring-templates.md) antes de implementar. 

   ![Implementação do modelo de carga](./media/backup-azure-arm-restore-vms/loading-template.png)
   
4. Depois de introduzir os valores necessários, aceite o **termos e condições** e selecione **Compra**.

   ![Submeter a implementação de modelo](./media/backup-azure-arm-restore-vms/submitting-template.png)

## <a name="post-restore-steps"></a>Passos de pós-restauro
* Se utilizar uma distribuição de Linux init-baseado na nuvem, tais como Ubuntu, por motivos de segurança, a palavra-passe é bloqueada após o restauro. Utilizar a extensão VMAccess à VM restaurada [repor a palavra-passe](../virtual-machines/linux/classic/reset-access.md). Recomendamos a utilização de chaves SSH nestes distribuições para evitar a repor o restauro de post de palavra-passe.
* Extensões presentes durante a configuração de cópia de segurança estão instaladas, mas não serão ativadas. Se vir um problema, reinstale as extensões. 
* Se a VM de cópia de segurança tem o restauro de post IP estático, a VM restaurada tem um IP dinâmico para evitar conflitos quando criar uma VM restaurada. Saiba mais sobre como pode [adicionar um IP estático para uma VM restaurada](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm).
* Uma VM restaurada não tem um valor de disponibilidade definido. Recomendamos que utilize a opção de discos de restauro de [adicionar um conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md) quando criar uma VM do PowerShell ou modelos utilizando o restauro de discos. 


## <a name="backup-for-restored-vms"></a>Cópia de segurança para VMs restauradas
Se restaurar uma VM para o mesmo grupo de recursos com o mesmo nome que a VM originalmente com cópia de segurança, continua a cópia de segurança no restauro de post VM. Se restaurar a VM a um grupo de recursos diferente ou especificar um nome diferente para a VM restaurada, a VM é tratada como se fosse uma nova VM. Terá de configurar a cópia de segurança para a VM restaurada.

## <a name="restore-a-vm-during-an-azure-datacenter-disaster"></a>Restaurar uma VM durante um desastre datacenter do Azure
Cópia de segurança do Azure permite restaurar VMs de cópia de segurança para o Centro de dados emparelhado no caso do datacenter primário onde as VMs estão em execução ocorrer um desastre no e configurou o Cofre de cópia de segurança para ser georredundante. Durante a tais cenários, selecione uma conta de armazenamento, que está presente no Centro de dados emparelhado. O resto do processo de restauro permanece igual. Cópia de segurança utiliza o serviço de computação da georreplicação emparelhada para criar a VM restaurada. Para obter mais informações, consulte [resiliência do datacenter do Azure](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md).

## <a name="restore-domain-controller-vms"></a>Restaurar VMs do controlador de domínio
Cópia de segurança do controlador de domínio (DC) de VMs é um cenário suportado com cópia de segurança. No entanto, tem de ser cuidado durante o processo de restauro. O processo de restauro correto depende a estrutura do domínio. O cenário mais simples, é necessário um único controlador de domínio num único domínio. Mais frequentemente para cargas de produção, tem um único domínio com vários controladores de domínio, talvez com alguns DCs no local. Por fim, pode ter uma floresta com vários domínios. 

Uma perspetiva de Active Directory, a VM do Azure é como qualquer outra VM num hipervisor suportado Moderno. A principal diferença com hipervisores no local é o que não há nenhuma consola da VM no Azure. Uma consola é necessária para certos cenários, como recuperar utilizando uma recuperação bare-metal (BMR)-cópia de segurança de tipo. No entanto, o restauro de VM do cofre da cópia de segurança é uma substituição completa para a BMR. Modo de restauro dos serviços de diretório (DSRM), também está disponível, pelo que todos os cenários de recuperação do Active Directory são viável. Para obter mais informações, consulte [considerações sobre a cópia de segurança e restauro de controladores de domínio virtualizados](https://technet.microsoft.com/en-us/library/virtual_active_directory_domain_controller_virtualization_hyperv(v=ws.10).aspx#backup_and_restore_considerations_for_virtualized_domain_controllers) e [planear a recuperação de floresta do Active Directory](https://technet.microsoft.com/en-us/library/planning-active-directory-forest-recovery(v=ws.10).aspx).

### <a name="single-dc-in-a-single-domain"></a>Único DC num domínio único
A VM pode ser restaurada (como outra VM) do portal do Azure ou através do PowerShell.

### <a name="multiple-dcs-in-a-single-domain"></a>Vários controladores de domínio num domínio único
Quando os restantes DCs do mesmo domínio podem ser contactados através da rede, o DC pode ser restaurado como qualquer VM. Se o último DC restantes no domínio, ou se é efetuada uma recuperação numa rede isolada, um procedimento de recuperação de floresta tem de ser seguido.

### <a name="multiple-domains-in-one-forest"></a>Vários domínios numa floresta
Quando os restantes DCs do mesmo domínio podem ser contactados através da rede, o DC pode ser restaurado como qualquer VM. Noutros casos, recomenda-se uma recuperação de floresta.

## <a name="restore-vms-with-special-network-configurations"></a>Restaurar VMs com configurações de rede especiais
É possível fazer cópias de segurança e restaurar VMs com as seguintes configurações de rede especiais. No entanto, estas configurações requerem algum especial atenção ao percorrer o processo de restauro:

* VMs em balanceadores de carga (internos e externos)
* VMs com vários IPs reservados
* VMs com vários NICs

> [!IMPORTANT]
> Quando cria a configuração de rede especiais para as VMs, tem de utilizar o PowerShell para criar VMs dos discos restaurados.
>
>

Totalmente voltar a criar as VMs após o restauro para o disco, siga estes passos:

1. Restaurar os discos dos serviços de recuperação cofre utilizando [PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

2. Criar a configuração de VM necessária para o Balanceador de carga / vários NIC/vários IP reservado, utilizando os cmdlets do PowerShell. Utilize-o para criar a VM com a configuração que pretende:

   a. Criar uma VM no serviço de nuvem com um [Balanceador de carga interno](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/).

   b. Criar uma VM para ligar a um [Balanceador de carga para a internet](https://azure.microsoft.com/en-us/documentation/articles/load-balancer-internet-getstarted/).

   c. Criar uma VM com [vários NICs](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/).

   d. Criar uma VM com [vários reservado IPs](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/).

## <a name="next-steps"></a>Passos seguintes
Agora que pode restaurar as suas VMs, consulte o artigo de resolução de problemas para obter informações sobre erros comuns com VMs. Além disso, consulte o artigo na gestão de tarefas com as suas VMs.

* [Resolução de problemas de erros](backup-azure-vms-troubleshoot.md#restore)
* [Gerir máquinas virtuais](backup-azure-manage-vms.md)
