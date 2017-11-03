---
title: "Cópias de segurança de máquinas de virtuais implementadas no monitor Resource Manager | Microsoft Docs"
description: "Monitorizar eventos e alertas de cópias de segurança de máquinas de virtuais implementadas no Resource Manager. Envie correio eletrónico com base em alertas."
services: backup
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
editor: 
ms.assetid: fed32015-2db2-44f8-b204-d89f6fd1bea2
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2016
ms.author: markgal;trinadhk;giridham;
ms.openlocfilehash: b9dc3f52e5fc275bc56b9964f2115833f2dde42e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-alerts-for-azure-virtual-machine-backups"></a>Monitorizar alertas de cópias de segurança de máquinas virtuais do Azure
Os alertas são as respostas do serviço do que um limiar de evento foi alcançado ou ultrapassado. Saber quando o início de problemas pode ser fundamental para manter os custos de negócio para baixo. Normalmente, não ocorrerem os alertas com base numa agenda e por isso, é útil logo que possível saber depois de ocorrerem os alertas. Por exemplo, quando uma tarefa de cópia de segurança ou restauro falhar, um alerta ocorre dentro de cinco minutos da falha. No dashboard do cofre, o mosaico alertas de cópia de segurança apresenta os eventos críticos e de nível de aviso. Nas definições de alertas de cópia de segurança, pode ver todos os eventos. Mas, o que pode fazer se um alerta ocorre quando estiver a trabalhar num problema separado? Se não souber quando o alerta acontece, pode ser uma inconveniência secundária ou comprometer dados. Para garantir que as pessoas corretas sabem de um alerta - quando ocorrer, configure o serviço para enviar notificações de alerta por e-mail. Para obter mais informações sobre como configurar notificações por e-mail, consulte [configurar notificações](backup-azure-monitor-vms.md#configure-notifications).

## <a name="how-do-i-find-information-about-the-alerts"></a>Como posso encontrar informações sobre os alertas?
Para ver informações sobre o evento que acionou um alerta, tem de abrir o painel Alertas de cópia de segurança. Existem duas formas para abrir o painel Alertas de cópia de segurança: os alertas de cópia de segurança a partir de mosaico no dashboard do cofre, ou a partir do painel de alertas e os eventos.

Para abrir o painel Alertas de cópia de segurança a partir do mosaico alertas de cópia de segurança:

* No **alertas de cópia de segurança** mosaico no dashboard do cofre, clique em **crítico** ou **aviso** para ver os eventos operacionais para esse nível de gravidade.

    ![Mosaico alertas de cópias de segurança](./media/backup-azure-monitor-vms/backup-alerts-tile.png)

Para abrir o painel de alertas de cópia de segurança a partir do painel de alertas e eventos:

1. No dashboard do cofre, clique em **todas as definições**. ![Botão de todas as definições](./media/backup-azure-monitor-vms/all-settings-button.png)
2. No **definições** painel, clique em **eventos e alertas**. ![Botão de eventos e alertas](./media/backup-azure-monitor-vms/alerts-and-events-button.png)
3. No **eventos e alertas** painel, clique em **alertas de cópia de segurança**. ![Botão de alertas de cópia de segurança](./media/backup-azure-monitor-vms/backup-alerts.png)

    O **alertas de cópia de segurança** painel abre e apresenta os alertas filtrados.

    ![Mosaico alertas de cópias de segurança](./media/backup-azure-monitor-vms/backup-alerts-critical.png)
4. Para ver informações detalhadas sobre um alerta específico, da lista de eventos, clique no alerta para abrir o respetivo **detalhes** painel.

    ![Detalhe do evento](./media/backup-azure-monitor-vms/audit-logs-event-detail.png)

    Para personalizar os atributos apresentados na lista, consulte [ver atributos eventos adicionais](backup-azure-monitor-vms.md#view-additional-event-attributes)

## <a name="configure-notifications"></a>Configurar notificações
 Pode configurar o serviço para enviar notificações por e-mail para os alertas que ocorreram durante a hora anterior, ou quando ocorrem tipos específicos de eventos.

Para configurar notificações por e-mail para alertas

1. No menu de alertas de cópia de segurança, clique em **configurar notificações**

    ![Menu de alertas de cópia de segurança](./media/backup-azure-monitor-vms/backup-alerts-menu.png)

    É aberto o painel Configurar notificações.

    ![Configurar o painel de notificações](./media/backup-azure-monitor-vms/configure-notifications.png)
2. No painel de notificações de configurar, para notificações por E-Mail, clique em **no**.

    Os destinatários e a gravidade caixas de diálogo tem uma estrela junto-lhes porque essas informações são necessárias. Fornecer, pelo menos, um endereço de e-mail e selecione, pelo menos, uma gravidade.
3. No **destinatários (E-Mail)** caixa de diálogo, escreva os endereços de e-mail para que receber as notificações. Utilize o formato: username@domainname.com. Separe vários endereços de e-mail com um ponto e vírgula (;).
4. No **notificar** área, escolha **por alerta** enviar a notificação quando ocorre o alerta especificado, ou **resumida horária** para enviar um resumo para a hora anterior.
5. No **gravidade** caixa de diálogo, escolha um ou mais níveis que quiser acionar a notificação por correio eletrónico.
6. Clique em **Guardar**.

   ### <a name="what-alert-types-are-available-for-azure-iaas-vm-backup"></a>Que tipos de alertas estão disponíveis para cópia de segurança de VM do IaaS do Azure?
   | Nível de alerta | Alertas enviados |
   | --- | --- |
   | Crítico |Falha de cópia de segurança, falha de recuperação |
   | Aviso |Nenhuma |
   | Informativo |Nenhuma |

### <a name="are-there-situations-where-email-isnt-sent-even-if-notifications-are-configured"></a>Existem situações em que um e-mail não é enviado, mesmo que as notificações estejam configuradas?
Existem situações em que não é enviado um alerta, apesar das notificações foi configuradas corretamente. No e-mail de situações seguintes não são enviadas notificações para evitar o ruído de alertas:

* Se as notificações estão configuradas para texto implícita por hora e um alerta é gerado e resolvido dentro da hora.
* A tarefa foi cancelada.
* Uma tarefa de cópia de segurança é acionada e, em seguida, falha e está em curso outra tarefa de cópia de segurança.
* Inicia uma tarefa de cópia de segurança agendada para uma VM preparados no Gestor de recursos, mas já não existe a VM.

## <a name="customize-your-view-of-events"></a>Personalizar a sua vista de eventos
O **registos de auditoria** definição inclui um conjunto predefinido de filtros e as colunas que mostra informações de eventos operacionais. Pode personalizar a vista, por isso, que, à **eventos** abre painel, mostra as informações que pretende.

1. No [dashboard do cofre](backup-azure-manage-vms.md#open-a-recovery-services-vault-in-the-dashboard), procure e clique em **registos de auditoria** para abrir o **eventos** painel.

    ![Registos de Auditoria](./media/backup-azure-monitor-vms/audit-logs-1606-1.png)

    O **eventos** é aberto um painel para os eventos operacionais, filtrados apenas para o Cofre atual.

    ![Filtro de auditoria de registos](./media/backup-azure-monitor-vms/audit-logs-filter.png)

    O painel mostra a lista de crítico, erro, aviso e eventos informativos que ocorreram da semana passada. O intervalo de tempo é um valor predefinido definido **filtro**. O **eventos** painel também mostra um gráfico de barras quando ocorrerem os eventos de controlo. Se não pretender ver o gráfico de barras, no **eventos** menu, clique em **ocultar gráfico** para alternar fora do gráfico. A vista predefinida de eventos mostra informações de operação, o nível, o estado, o recurso e o tempo. Para obter informações sobre a exposição de atributos de eventos adicionais, consulte a secção [expandir as informações de evento](backup-azure-monitor-vms.md#view-additional-event-attributes).
2. Para obter informações adicionais sobre um evento operacional, no **operação** coluna, clique num evento operacional para abrir o painel. O painel contém informações detalhadas sobre os eventos. Eventos são agrupados pelo respetivo ID de correlação e uma lista dos eventos que ocorreram no intervalo de tempo.

    ![Detalhes de operação](./media/backup-azure-monitor-vms/audit-logs-details-window.png)
3. Para ver informações detalhadas sobre um determinado evento, na lista de eventos, clique no evento para abrir o respetivo **detalhes** painel.

    ![Detalhe do evento](./media/backup-azure-monitor-vms/audit-logs-details-window-deep.png)

    As informações de nível do evento são como detalhadas como obtém as informações. Se preferir ver neste muito informações sobre cada evento e pretende adicionar este muito detalhes para a **eventos** painel, consulte a secção [expandir as informações de evento](backup-azure-monitor-vms.md#view-additional-event-attributes).

## <a name="customize-the-event-filter"></a>Personalizar o filtro de eventos
Utilize o **filtro** para ajustar ou escolher as informações que é apresentado um painel específico. Para filtrar as informações de evento:

1. No [dashboard do cofre](backup-azure-manage-vms.md#open-a-recovery-services-vault-in-the-dashboard), procure e clique em **registos de auditoria** para abrir o **eventos** painel.

    ![Registos de Auditoria](./media/backup-azure-monitor-vms/audit-logs-1606-1.png)

    O **eventos** é aberto um painel para os eventos operacionais, filtrados apenas para o Cofre atual.

    ![Filtro de auditoria de registos](./media/backup-azure-monitor-vms/audit-logs-filter.png)
2. No **eventos** menu, clique em **filtro** para abrir o mesmo.

    ![Abra o painel de filtro](./media/backup-azure-monitor-vms/audit-logs-filter-button.png)
3. No **filtro** painel, ajuste o **nível**, **intervalo de tempo**, e **chamador** filtros. Os outros filtros não estão disponíveis, uma vez que tenham sido definidos para fornecer as informações atuais para o Cofre dos serviços de recuperação.

    ![Detalhes de consulta de registos de auditoria](./media/backup-azure-monitor-vms/filter-blade.png)

    Pode especificar o **nível** do evento: crítico, erro, aviso ou informativos. Pode escolher qualquer combinação dos níveis de eventos, mas tem de ter, pelo menos, um nível selecionado. Alternar o nível de ligado ou desligado. O **intervalo de tempo** filtro permite-lhe especificar o período de tempo para capturar eventos. Se utilizar um intervalo de tempo personalizado, pode definir as horas de início e de fim.
4. Assim que estiver pronto para os registos de operações, utilizando o filtro de consulta, clique em **atualização**. Apresentam os resultados de **eventos** painel.

    ![Detalhes de operação](./media/backup-azure-monitor-vms/edited-list-of-events.png)

### <a name="view-additional-event-attributes"></a>Ver eventos adicionais atributos
Utilizar o **colunas** botão, pode ativar atributos de evento adicional a aparecer na lista no **eventos** painel. A lista predefinida de eventos apresenta informações para a operação, o nível, o estado, o recurso e o tempo. Para ativar atributos adicionais:

1. No **eventos** painel, clique em **colunas**.

    ![Abra colunas](./media/backup-azure-monitor-vms/audi-logs-column-button.png)

    O **escolher colunas** abre o painel.

    ![Painel de colunas](./media/backup-azure-monitor-vms/columns-blade.png)
2. Para selecionar o atributo, clique na caixa de verificação. Activa/desactiva a caixa de verificação do atributo e desativar.
3. Clique em **repor** para repor a lista de atributos no **eventos** painel. Após a adição ou remoção de atributos na lista, utilize **repor** para ver a nova lista de atributos de eventos.
4. Clique em **atualizar** atualizar os dados de eventos de atributos. A tabela seguinte fornece informações sobre cada atributo.

| Nome da coluna | Descrição |
| --- | --- |
| Operação |O nome da operação |
| Nível |O nível da operação, os valores podem ser: informativo, erro, aviso ou crítico |
| Estado |Estado descritivo da operação |
| Recurso |URL que identifica o recurso; Também conhecido como o ID de recurso |
| Hora |Tempo, medido desde a hora atual, quando o evento ocorreu |
| Autor da chamada |Quem ou o que chamado ou acionado o evento; pode ser o sistema ou um utilizador |
| Timestamp |A hora quando o evento foi activado |
| Grupo de Recursos |O grupo de recursos associados |
| Tipo de Recurso |O tipo de recurso interno utilizado pelo Gestor de recursos |
| ID da subscrição |O ID de subscrição associado. |
| Categoria |Categoria do evento |
| ID de correlação |ID de comuns de eventos relacionados |

## <a name="use-powershell-to-customize-alerts"></a>Utilizar o PowerShell para personalizar alertas
Pode obter notificações de alerta personalizadas para as tarefas no portal. Para obter estas tarefas, defina regras de alerta baseada no PowerShell nos eventos de registos operacionais. Utilize *PowerShell versão 1.3.0 ou posterior*.

Para definir uma notificação para o alertar para falhas de cópia de segurança, utilize um comando como o script seguinte:

```
PS C:\> $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail contoso@microsoft.com
PS C:\> Add-AzureRmLogAlertRule -Name backupFailedAlert -Location "East US" -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -OperationName Microsoft.RecoveryServices/recoveryServicesVault/Backup -Status Failed -TargetResourceId /subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/Microsoft.RecoveryServices/vaults/trinadhVault -Actions $actionEmail
```

**ResourceId** : pode obter ResourceId a partir de registos de auditoria. O ResourceId é um URL fornecido na coluna recurso dos registos de operações.

**OperationName** : OperationName está no formato "Microsoft.RecoveryServices/recoveryServicesVault/*EventName*" onde *EventName* pode ser:<br/>

* Registar <br/>
* Anular o Registo <br/>
* ConfigureProtection <br/>
* Cópia de segurança <br/>
* Restauro <br/>
* StopProtection <br/>
* DeleteBackupData <br/>
* CreateProtectionPolicy <br/>
* DeleteProtectionPolicy <br/>
* UpdateProtectionPolicy <br/>

**Estado** : os valores suportados são iniciado, com êxito ou falha.

**ResourceGroup** : Este é o grupo de recursos a que pertence o recurso. Pode adicionar a coluna de grupo de recursos para os registos gerados. Grupo de recursos é um dos tipos de informações de evento disponíveis.

**Nome** : nome da regra de alerta.

**CustomEmail** : Especifique o endereço de e-mail personalizadas para o qual pretende enviar uma notificação de alerta

**SendToServiceOwners** : esta opção envia notificações de alerta para todos os administradores e coadministradores da subscrição. Podem ser utilizado em **New-AzureRmAlertRuleEmail** cmdlet

### <a name="limitations-on-alerts"></a>Limitações de alertas
Alertas com base em eventos estão sujeitos às seguintes limitações:

1. Alertas são acionados em todas as máquinas virtuais no cofre dos serviços de recuperação. Não é possível personalizar o alerta para um subconjunto de máquinas virtuais num cofre dos serviços de recuperação.
2. Esta funcionalidade está em pré-visualização. [Saiba mais](../monitoring-and-diagnostics/insights-powershell-samples.md#create-metric-alerts)
3. Os alertas são enviados a partir de "alerts-noreply@mail.windowsazure.com". Atualmente não é possível modificar o remetente do e-mail.

## <a name="next-steps"></a>Passos seguintes
Registos de eventos ativar excelente post-mortem e suporte para as operações de cópia de segurança de auditoria. Com sessão iniciadas as seguintes operações:

* Registar
* Anular o Registo
* Configurar a proteção
* Cópia de segurança (ambos agendadas, bem como a cópia de segurança a pedido)
* Restauro
* Interrompa a proteção
* Eliminar dados de cópia de segurança
* Adicionar política
* Eliminar política
* Política de atualização
* Cancelar a tarefa

Para obter uma explicação abrangente de eventos, operações e os registos de auditoria em todos os serviços do Azure, consulte o artigo [ver eventos e registos de auditoria](../monitoring-and-diagnostics/insights-debugging-with-events.md).

Para obter informações sobre como voltar a criar uma máquina virtual a partir de um ponto de recuperação, consulte [restaurar VMs do Azure](backup-azure-restore-vms.md). Se precisar de informações sobre a proteção de máquinas virtuais, consulte o artigo [primeiras impressões: cópia de segurança VMs para um cofre dos serviços de recuperação](backup-azure-vms-first-look-arm.md). Saiba mais sobre as tarefas de gestão para cópias de segurança VM no artigo, [cópias de segurança do Azure de gerir máquinas virtuais](backup-azure-manage-vms.md).
