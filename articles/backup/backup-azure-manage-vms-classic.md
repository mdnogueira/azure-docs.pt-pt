---
title: "Gerir e monitorizar as cópias de segurança da máquina virtual do Azure | Microsoft Docs"
description: "Saiba como gerir e monitorizar as cópias de segurança de uma máquina virtual do Azure"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: 4372944e-d33a-4f6a-bd5f-d04af2842713
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: trinadhk;markgal;
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e3d3de79c7f2465791ec68f850df2fc6317880f9
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="manage-common-azure-backup-jobs-and-trigger-alerts-in-the-classic-portal"></a>Gerir tarefas de cópia de segurança do Azure comuns e alertas de Acionador no portal clássico
> [!div class="op_single_selector"]
> * [Gerir cópias de segurança de VM do Azure](backup-azure-manage-vms.md)
> * [Gerir cópias de segurança de VMS clássicas](backup-azure-manage-vms-classic.md)
>
>

Este artigo fornece informações sobre a gestão comuns e tarefas de monitorização para máquinas de virtuais do modelo de clássico protegidas no Azure.  

> [!NOTE]
> O Azure tem dois modelos de implementação para criar e trabalhar com recursos: [Resource Manager e Clássico](../azure-resource-manager/resource-manager-deployment-model.md). Consulte [preparar o ambiente para fazer cópias de segurança de máquinas virtuais do Azure](backup-azure-vms-prepare.md) para obter detalhes sobre como trabalhar com a implementação clássica modelo VMs.
>
> [!IMPORTANT]
>A partir de março de 2017, já não pode utilizar o portal clássico para criar cofres de Cópia de Segurança.
>
> Agora pode atualizar os cofres de Cópia de Segurança para cofres dos Serviços de Recuperação. Para obter detalhes, veja o artigo [Atualizar um cofre de Cópia de Segurança para um cofre dos Serviços de Recuperação](backup-azure-upgrade-backup-to-recovery-services.md). Recomendamos que atualize os cofres de Cópia de Segurança para cofres dos Serviços de Recuperação.<br/> Após 30 de Novembro de 2017, não poderá utilizar o PowerShell para criar cofres de cópia de segurança. **Até 30 de Novembro de 2017**:
>- Todos os cofres de Cópia e Segurança restantes serão atualizados automaticamente para cofres dos Serviços de Recuperação.
>- Não vai conseguir aceder aos dados de cópia de segurança no portal clássico. Em vez disso, utilize o portal do Azure para aceder aos dados de cópia de segurança em cofres dos Serviços de Recuperação.

## <a name="manage-protected-virtual-machines"></a>Gerir máquinas virtuais protegidas
Para gerir máquinas virtuais protegidas:

1. Para ver e gerir as definições de cópia de segurança para uma máquina virtual clique o **itens protegidos** separador.
2. Clique no nome de um item protegido para ver o **detalhes de cópia de segurança** separador, que mostra-lhe informações sobre a última cópia de segurança.

    ![Cópia de segurança da máquina virtual](./media/backup-azure-manage-vms/backup-vmdetails.png)
3. Para ver e gerir definições de política de cópia de segurança para uma máquina virtual clique o **políticas** separador.

    ![Política da máquina virtual](./media/backup-azure-manage-vms/manage-policy-settings.png)

    O **políticas de cópia de segurança** separador mostra a política existente. Pode modificar conforme necessário. Se precisar de criar uma nova política, clique em **criar** no **políticas** página. Tenha em atenção que se pretender remover uma política não deve ter quaisquer máquinas de virtuais associadas à mesma.

    ![Política da máquina virtual](./media/backup-azure-manage-vms/backup-vmpolicy.png)
4. Pode obter mais informações sobre as ações ou estado para uma máquina virtual no **tarefas** página. Clique numa tarefa na lista para obter mais detalhes ou, filtre as tarefas para uma máquina virtual específica.

    ![Tarefas](./media/backup-azure-manage-vms/backup-job.png)

## <a name="on-demand-backup-of-a-virtual-machine"></a>Cópia de segurança a pedido de uma máquina virtual
Pode demorar um pedido cópia de segurança de uma máquina virtual, assim que estiver configurado para proteção. Se a cópia de segurança inicial está pendente para a máquina virtual, cópia de segurança a pedido irá criar uma cópia completa da máquina virtual no Cofre de cópia de segurança do Azure. Se a primeira cópia de segurança estiver concluída, será de cópia de segurança a pedido apenas alterações de envio de cópia de segurança anterior a cópia de segurança do Azure, ou seja, Cofre é sempre incremental.

> [!NOTE]
> Período de retenção de uma cópia de segurança a pedido está definido para o valor de retenção especificado para a retenção diária na política de cópia de segurança correspondente para a VM.  
>
>

Para efetuar um pedido a cópia de segurança de uma máquina virtual:

1. Navegue para o **itens protegidos** página e selecione **Máquina Virtual do Azure** como **tipo** (se ainda não estiver selecionado) e clique em **selecione** botão.

    ![Tipo VM](./media/backup-azure-manage-vms/vm-type.png)
2. Selecione a máquina virtual no qual pretende efetuar cópia de segurança um pedido e clique em **cópia de segurança agora** na parte inferior da página.

    ![Criar cópias de segurança agora](./media/backup-azure-manage-vms/backup-now.png)

    Isto irá criar uma tarefa de cópia de segurança na máquina virtual selecionada. Período de retenção do ponto de recuperação criado através da tarefa irá ser idênticos aos especificada na política de associado à máquina virtual.

    ![Criar tarefa de cópia de segurança](./media/backup-azure-manage-vms/creating-job.png)

   > [!NOTE]
   > Para ver a política associada uma máquina virtual, desagregar máquina virtual no **itens protegidos** e aceda ao separador de política de cópia da página.
   >
   >
3. Depois da tarefa é criada, pode clicar em **ver tarefa** botão na barra de alerta para ver a tarefa correspondente na página de tarefas.

    ![tarefa de cópia de segurança criada](./media/backup-azure-manage-vms/created-job.png)
4. Após a conclusão bem-sucedida da tarefa, será possível criar um ponto de recuperação que pode utilizar para restaurar a máquina virtual. Isto também irá incrementar o valor de coluna de ponto de recuperação por 1 na **itens protegidos** página.

## <a name="stop-protecting-virtual-machines"></a>Parar a proteção de máquinas virtuais
Pode optar por parar as cópias de segurança futuras de uma máquina virtual com as seguintes opções:

* Manter os dados de cópia de segurança associados à máquina virtual no Cofre de cópia de segurança do Azure
* Eliminar dados de cópia de segurança associados à máquina virtual

Se tiver optado por manter os dados de cópia de segurança associados à máquina virtual, pode utilizar os dados de cópia de segurança para restaurar a máquina virtual. Para detalhes de tais máquinas virtuais de preços, clique em [aqui](https://azure.microsoft.com/pricing/details/backup/).

Para parar a proteção para uma máquina virtual:

1. Navegue para **itens protegidos** página e selecione **máquina virtual do Azure** como o tipo de filtro (se ainda não estiver selecionado) e clique em **selecione** botão.

    ![Tipo VM](./media/backup-azure-manage-vms/vm-type.png)
2. Selecione a máquina virtual e clique em **parar proteção** na parte inferior da página.

    ![Interrompa a proteção](./media/backup-azure-manage-vms/stop-protection.png)
3. Por predefinição, cópia de segurança do Azure não elimina os dados de cópia de segurança associados à máquina virtual.

    ![Confirme a interrupção de proteção](./media/backup-azure-manage-vms/confirm-stop-protection.png)

    Se pretender eliminar dados de cópia de segurança, selecione a caixa de verificação.

    ![Caixa de verificação](./media/backup-azure-manage-vms/checkbox.png)

    Selecione um motivo para parar a cópia de segurança. Embora seja opcional, fornecer um motivo irá ajudar a cópia de segurança do Azure para trabalhar em comentários e atribuir prioridade os cenários de cliente.
4. Clique em **submeter** botão para submeter o **interromper a proteção** tarefa. Clique em **ver tarefa** para ver a tarefa no correspondente **tarefas** página.

    ![Interrompa a proteção](./media/backup-azure-manage-vms/stop-protect-success.png)

    Se não tiver selecionado **associados a eliminar dados de cópia de segurança** opção durante **parar proteção** assistente, em seguida, quando a tarefa post, alterações de estado de proteção para **proteção parada**. Os dados permanecem com cópia de segurança do Azure até serem eliminada explicitamente. Sempre pode eliminar os dados ao selecionar a máquina virtual no **itens protegidos** página e clicando em **eliminar**.

    ![Paragem da proteção](./media/backup-azure-manage-vms/protection-stopped-status.png)

    Se tiver selecionado o **associados a eliminar dados de cópia de segurança** opção, a máquina virtual será parte a **itens protegidos** página.

## <a name="re-protect-virtual-machine"></a>Voltar a proteger máquinas virtuais
Se não tiver selecionado o **eliminar dados de cópia de segurança associar** opção **parar proteção**, pode voltar a proteger a máquina virtual, seguindo os passos semelhantes para cópia de segurança de máquinas virtuais registadas. Depois de protegidos, esta máquina virtual serão mantidos antes de parar a proteção de dados de cópia de segurança e pontos de recuperação criado após voltar a proteger.

Depois de voltar a proteger, estado de proteção da máquina virtual será alterado para **protegidos** se existirem pontos de recuperação anteriores ao **parar proteção**.

  ![VM protegida](./media/backup-azure-manage-vms/reprotected-status.png)

> [!NOTE]
> Quando voltar a proteger a máquina virtual, pode escolher uma política diferente do que a política com o qual proteger máquina virtual foi inicialmente.
>
>

## <a name="unregister-virtual-machines"></a>Anular o registo de máquinas virtuais
Se pretender remover a máquina virtual a partir do Cofre de cópia de segurança:

1. Clique em de **UNREGISTER** na parte inferior da página.

    ![Desative a proteção](./media/backup-azure-manage-vms/unregister-button.png)

    Será apresentada uma notificação de alerta na parte inferior do ecrã pedir confirmação. Clique em **Sim** para continuar.

    ![Desative a proteção](./media/backup-azure-manage-vms/confirm-unregister.png)

## <a name="delete-backup-data"></a>Eliminar dados de cópia de segurança
Pode eliminar os dados de cópia de segurança associados a uma máquina virtual, está:

* Durante a tarefa de paragem da proteção
* Após uma interrupção de proteção a tarefa é concluída numa máquina virtual

Para eliminar dados de cópia de segurança numa máquina virtual, que está a ser o *proteção parada* Estado após a conclusão com êxito de um **parar a cópia de segurança** tarefa:

1. Navegue para o **itens protegidos** página e selecione **Máquina Virtual do Azure** como *tipo* e clique em de **selecione** botão.

    ![Tipo VM](./media/backup-azure-manage-vms/vm-type.png)
2. Selecione a máquina virtual. A máquina virtual estará no **proteção parada** estado.

    ![Proteção parada](./media/backup-azure-manage-vms/protection-stopped-b.png)
3. Clique em de **eliminar** na parte inferior da página.

    ![Eliminar cópia de segurança](./media/backup-azure-manage-vms/delete-backup.png)
4. No **eliminar dados de cópia de segurança** assistente, selecione um motivo para eliminar dados de cópia de segurança (altamente recomendados) e clique em **submeter**.

    ![Eliminar dados de cópia de segurança](./media/backup-azure-manage-vms/delete-backup-data.png)
5. Isto irá criar uma tarefa para eliminar dados de cópia de segurança da máquina virtual selecionada. Clique em **ver tarefa** para ver tarefa correspondente na página das tarefas.

    ![Eliminação de dados com êxito](./media/backup-azure-manage-vms/delete-data-success.png)

    Assim que a tarefa é concluída, a entrada correspondente à máquina virtual será removida do **itens protegidos** página.

## <a name="dashboard"></a>Dashboard
No **Dashboard** página pode rever informações sobre máquinas virtuais do Azure, o respetivo armazenamento e tarefas associadas nas últimas 24 horas. Pode ver o estado da cópia de segurança e quaisquer erros de cópia de segurança associados.

![Dashboard](./media/backup-azure-manage-vms/dashboard-protectedvms.png)

> [!NOTE]
> Os valores no dashboard são atualizados a cada 24 horas.
>
>

## <a name="auditing-operations"></a>Operações de auditorias
Cópia de segurança do Azure fornece revisão "operação registos" das operações de cópia de segurança acionada pelo cliente facilitando ver exatamente as operações de gestão foram executadas no Cofre de cópia de segurança. Registos de operações ativar excelente post-mortem e suporte para as operações de cópia de segurança de auditoria.

As seguintes operações são registadas nos registos de operações:

* Registar
* Anular o Registo
* Configurar a proteção
* Cópia de segurança (ambos agendadas, bem como a cópia de segurança a pedido através de BackupNow)
* Restauro
* Interrompa a proteção
* Eliminar dados de cópia de segurança
* Adicionar política
* Eliminar política
* Política de atualização
* Cancelar a tarefa

Para ver os registos de operações correspondente para um cofre de cópia de segurança:

1. Navegue para **dos serviços de gestão** no portal do Azure e, em seguida, clique o **os registos de operações** separador.

    ![Registos de operações](./media/backup-azure-manage-vms/ops-logs.png)
2. Os filtros, selecionar **cópia de segurança** como *tipo* e especifique o nome do Cofre de cópia de segurança no *nome do serviço* e clique em **submeter**.

    ![Filtro de registos de operação](./media/backup-azure-manage-vms/ops-logs-filter.png)
3. Nos registos de operações, selecione todas as operações e clique em **detalhes** para ver detalhes correspondente a uma operação.

    ![Detalhes de obtenção de registos de operação](./media/backup-azure-manage-vms/ops-logs-details.png)

    O **assistente detalhes** contém informações sobre a operação acionada, Id, recurso em que esta operação é acionada e a hora da operação de início da tarefa.

    ![Detalhes de operação](./media/backup-azure-manage-vms/ops-logs-details-window.png)

## <a name="alert-notifications"></a>Notificações de alerta
Pode obter notificações de alerta personalizadas para as tarefas no portal. Isto é conseguido através da definição de regras de alerta baseada no PowerShell nos eventos de registos operacionais. Recomendamos que utilize *PowerShell versão 1.3.0 ou acima*.

Para definir uma notificação para o alertar para falhas de cópia de segurança, um comando de exemplo irá ter o seguinte aspeto:

```
PS C:\> $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail contoso@microsoft.com
PS C:\> Add-AzureRmLogAlertRule -Name backupFailedAlert -Location "East US" -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -OperationName Microsoft.Backup/backupVault/Backup -Status Failed -TargetResourceId /subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/microsoft.backupbvtd2/BackupVault/trinadhVault -Actions $actionEmail
```

**ResourceId**: pode obter esta de pop-up de registos de operações, tal como descrito no acima secção. ResourceUri na janela de pop-up de detalhes de uma operação é ResourceId ser fornecidos para este cmdlet.

**OperationName**: Isto vai estar no formato "Microsoft.Backup/backupvault/<EventName>" onde EventName é uma das Register, Unregister, ConfigureProtection, cópia de segurança, restaurar, StopProtection DeleteBackupData, CreateProtectionPolicy, DeleteProtectionPolicy, UpdateProtectionPolicy

**Estado**: suportados valores estão-iniciado com êxito e falha.

**ResourceGroup**: ResourceGroup do recurso no qual a operação é acionada. Pode obter este valor ResourceId. Valor entre os campos */resourceGroups/* e */providers/* ResourceId valor é o valor para o ResourceGroup.

**Nome**: nome da regra de alerta.

**CustomEmail**: Especifique o endereço de e-mail personalizadas para o qual pretende enviar notificações de alerta

**SendToServiceOwners**: esta opção envia a notificação de alertas para todos os administradores e coadministradores da subscrição. Podem ser utilizado em **New-AzureRmAlertRuleEmail** cmdlet

### <a name="limitations-on-alerts"></a>Limitações de alertas
Alertas com base em eventos estão sujeitos às seguintes limitações:

1. Alertas são acionados em todas as máquinas virtuais no Cofre de cópia de segurança. Não é possível personalizar a obter alertas para o conjunto específico de máquinas virtuais no Cofre de cópia de segurança.
2. Esta funcionalidade está em pré-visualização. [Saiba mais](../monitoring-and-diagnostics/insights-powershell-samples.md#create-metric-alerts)
3. Irá receber alertas a partir de "alerts-noreply@mail.windowsazure.com". Atualmente não é possível modificar o remetente do e-mail.

## <a name="next-steps"></a>Passos seguintes
* [Restaurar VMs do Azure](backup-azure-restore-vms.md)
