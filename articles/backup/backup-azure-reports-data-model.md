---
title: "Modelo de dados para cópia de segurança do Azure"
description: "Este artigo aborda os detalhes do modelo de dados Power BI para relatórios de cópia de segurança do Azure."
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 0767c330-690d-474d-85a6-aa8ddc410bb2
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/26/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: efecbc9f1c410744f49795889c4ec3cc618f07e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="data-model-for-azure-backup-reports"></a>Modelo de dados para relatórios do Azure Backup
Este artigo descreve o modelo de dados do Power BI utilizado para criar relatórios de cópia de segurança do Azure. Utilizar este modelo de dados, pode filtrar relatórios existentes com base nos campos relevantes e mais importante ainda, criar os seus próprios relatórios através de tabelas e campos no modelo. 

## <a name="creating-new-reports-in-power-bi"></a>Criar novos relatórios no Power BI
Power BI fornece funcionalidades de personalização que pode utilizar [criar relatórios utilizando o modelo de dados](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/).

## <a name="using-azure-backup-data-model"></a>Utilizar o modelo de dados de cópia de segurança do Azure
Pode utilizar os seguintes campos fornecidos como parte do modelo de dados para criar relatórios e personalizar relatórios existentes.

### <a name="alert"></a>Alerta
Esta tabela fornece campos básicos e agregações ao longo de vários campos relacionados alerta.

| Campo | Tipo de dados | Descrição |
| --- | --- | --- |
| #AlertsCreatedInPeriod |Número inteiro |Número de alertas criados num período de tempo selecionado |
| % ActiveAlertsCreatedInPeriod |Percentagem |Percentagem de alertas ativos no período de tempo selecionado |
| % CriticalAlertsCreatedInPeriod |Percentagem |Percentagem de alertas críticos no período de tempo selecionado |
| AlertOccurenceDate |Data |Data quando o alerta foi criado |
| AlertSeverity |Texto |Gravidade do alerta, por exemplo, crítico |
| AlertStatus |Texto |Estado do alerta, por exemplo, o Active Directory |
| AlertType |Texto |Tipo de alerta gerado por exemplo, a cópia de segurança |
| AlertUniqueId |Texto |Id exclusivo do alerta gerado |
| AsOnDateTime |Data/hora |Última hora de atualização para a linha selecionada |
| AvgResolutionTimeInMinsForAlertsCreatedInPeriod |Número decimal |Tempo médio (em minutos) para resolver o alerta para o período de tempo selecionado |
| EntityState |Texto |Estado atual do objeto alerta, por exemplo, Active Directory, eliminado |

### <a name="backup-item"></a>Item de cópia de segurança
Esta tabela fornece campos básicos e agregações ao longo de vários campos de cópia de segurança relacionadas com o item.

| Campo | Tipo de dados | Descrição |
| --- | --- | --- |
| #BackupItems |Número inteiro |Número de itens de cópia de segurança |
| #UnprotectedBackupItems |Número inteiro |Número de itens de cópia de segurança de interrupção de proteção ou configurado para cópias de segurança, mas as cópias de segurança não foi iniciadas|
| AsOnDateTime |Data/hora |Última hora de atualização para a linha selecionada |
| BackupItemFriendlyName |Texto |Nome amigável do item de cópia de segurança |
| BackupItemId |Texto |ID de item de cópia de segurança |
| BackupItemName |Texto |Nome do item de cópia de segurança |
| BackupItemType |Texto |Tipo de item de cópia de segurança por exemplo, VM, FileFolder |
| EntityState |Texto |Estado atual do objeto de item de cópia de segurança, por exemplo, Active Directory, eliminado |
| LastBackupDateTime |Data/hora |Hora da última cópia de segurança para o item de cópia de segurança selecionado |
| LastBackupState |Texto |Estado da última cópia de segurança para o item de cópia de segurança selecionado, por exemplo, Successful, falha |
| LastSuccessfulBackupDateTime |Data/hora |Hora da última cópia de segurança com êxito para o item de cópia de segurança selecionado |
| ProtectionState |Texto |Estado de proteção atual do item de cópia de segurança, por exemplo, protegidos, ProtectionStopped |

### <a name="calendar"></a>Calendário
Esta tabela fornece detalhes sobre campos relacionados com o calendário.

| Campo | Tipo de dados | Descrição |
| --- | --- | --- |
| Data |Data |Data seleccionada para filtrar dados |
| DateKey |Texto |Chave exclusiva para cada item de data |
| DayDiff |Número decimal |Diferença no dia para dados de filtragem, por exemplo, 0 indica a data do dia atual, -1 indica os dados de um dia anterior, 0 e -1 indicam dados de dia atual e anterior  |
| Mês |Texto |Mês do ano selecionado para filtrar dados, o mês começa no primeiro dia e termina no dia 31 |
| MonthDate | Data |Datas no mês quando termina o mês, selecionado para filtrar dados |
| MonthDiff |Número decimal |Diferença no mês de dados de filtragem, por exemplo, 0 indica a data do mês atual, -1 indica os dados de mês anterior, 0 e -1 indicam dados de mês atuais e anteriores |
| Semana |Texto |Semana selecionada para filtrar dados, semana começa no Domingo e termina no Sábado |
| WeekDate |Data |Data da semana quando termina semana, selecionado para filtrar dados |
| WeekDiff |Número decimal |Diferença na semana para os dados de filtragem, por exemplo, 0 indica dados semana atual, -1 indica dados semana anterior, 0 e -1 indicam dados de semana atual e anterior |
| ano |Texto |Ano de calendário selecionado para filtrar dados |
| YearDate |Data |Datas no ano quando termina o ano, selecionado para filtrar dados |

### <a name="job"></a>Tarefa
Esta tabela fornece campos básicos e agregações ao longo de vários campos relacionados com a tarefa.

| Campo | Tipo de dados | Descrição |
| --- | --- | --- |
| #JobsCreatedInPeriod |Número inteiro |Número de tarefas criada no período de tempo selecionado |
| % FailuresForJobsCreatedInPeriod |Percentagem |Percentagem global da tarefa falhas no período de tempo selecionado |
| 80thPercentileDataTransferredInMBForBackupJobsCreatedInPeriod |Número decimal |valor de percentil 80th dos dados transferidos em MB para **cópia de segurança** tarefas criadas no período de tempo selecionado |
| AsOnDateTime |Data/hora |Última hora de atualização para a linha selecionada |
| AvgBackupDurationInMinsForJobsCreatedInPeriod |Número decimal |Tempo médio em minutos para **cópia de segurança concluída** tarefas criadas no período de tempo selecionado |
| AvgRestoreDurationInMinsForJobsCreatedInPeriod |Número decimal |Tempo médio em minutos para **concluir o restauro** tarefas criadas no período de tempo selecionado |
| BackupStorageDestination |Texto |Destino de armazenamento de cópia de segurança por exemplo, nuvem, o disco  |
| EntityState |Texto |Estado atual do objeto de tarefa, por exemplo, Active Directory, eliminado |
| JobFailureCode |Texto |Cadeia de código de falha devido ao qual foi efetuada a falha da tarefa |
| JobOperation |Texto |Operação para o qual tarefa é executada por exemplo, cópia de segurança, restauro, configurar a cópia de segurança |
| JobStartDate |Data |Data quando iniciar a tarefa em execução |
| JobStartTime |Hora |Quando iniciar a tarefa em execução |
| Estado da Tarefa |Texto |Estado da tarefa foi terminada por exemplo, concluído, com falhas |
| JobUniqueId |Texto |Id exclusivo para identificar a tarefa |

### <a name="policy"></a>Política
Esta tabela fornece campos básicos e agregações ao longo de vários campos relacionados com a política.

| Campo | Tipo de dados | Descrição |
| --- | --- | --- |
| #Policies |Número inteiro |Número de políticas de cópia de segurança que existe no sistema |
| #PoliciesInUse |Número inteiro |Número de políticas atualmente a ser utilizado para configurar cópias de segurança |
| AsOnDateTime |Data/hora |Última hora de atualização para a linha selecionada |
| BackupDaysOfTheWeek |Texto |Dias da semana quando tem sido agendadas cópias de segurança |
| BackupFrequency |Texto |Frequência com que as cópias de segurança são executadas por exemplo, diária, semanal |
| BackupTimes |Texto |Data e hora quando estão agendadas cópias de segurança |
| DailyRetentionDuration |Número inteiro |Duração total de retenção em dias para cópias de segurança configurados |
| DailyRetentionTimes |Texto |Data e hora quando a retenção diária foi configurada |
| EntityState |Texto |Estado atual do objeto de política, por exemplo, Active Directory, eliminado |
| MonthlyRetentionDaysOfTheMonth |Texto |Datas do mês selecionado para a retenção mensal |
| MonthlyRetentionDaysOfTheWeek |Texto |Dias da semana selecionados para retenção mensal |
| MonthlyRetentionDuration |Número decimal |Duração de retenção total em meses para cópias de segurança configuradas |
| MonthlyRetentionFormat |Texto |Tipo de configuração de retenção mensal por exemplo, diariamente para dia com base, semanalmente semana com base |
| MonthlyRetentionTimes |Texto |Data e hora quando a retenção mensal está configurada |
| MonthlyRetentionWeeksOfTheMonth |Texto |Por exemplo, o semanas do mês quando o período de retenção mensal é configurado First, Last etc. |
| PolicyName |Texto |Nome da política definida |
| PolicyUniqueId |Texto |Id exclusivo para identificar a política |
| RetentionType |Texto |Tipo de política de retenção por exemplo, diariamente, semanalmente, mensalmente, anual |
| WeeklyRetentionDaysOfTheWeek |Texto |Dias da semana selecionados para retenção semana |
| WeeklyRetentionDuration |Número decimal |Duração de retenção semanais total no semanas para as cópias de segurança configuradas |
| WeeklyRetentionTimes |Texto |Data e hora quando a retenção semana está configurada |
| YearlyRetentionDaysOfTheMonth |Texto |Datas do mês selecionado para a retenção anual |
| YearlyRetentionDaysOfTheWeek |Texto |Dias da semana selecionados para retenção anual |
| YearlyRetentionDuration |Número decimal |Duração de retenção total em anos para cópias de segurança configurados |
| YearlyRetentionFormat |Texto |Tipo de configuração de retenção anual por exemplo, diariamente para dia com base, semanalmente semana com base |
| YearlyRetentionMonthsOfTheYear |Texto |Meses do ano selecionada para a retenção anual |
| YearlyRetentionTimes |Texto |Data e hora quando a retenção anual está configurada |
| YearlyRetentionWeeksOfTheMonth |Texto |Por exemplo, o semanas do mês quando o período de retenção anual é configurado First, Last etc. |

### <a name="protected-server"></a>Servidor protegido
Esta tabela fornece campos básicos e agregações ao longo de vários campos protegidos relacionados com o servidor.

| Campo | Tipo de dados | Descrição |
| --- | --- | --- |
| #ProtectedServers |Número inteiro |Número de servidores protegidos |
| AsOnDateTime |Data/hora |Última hora de atualização para a linha selecionada |
| AzureBackupAgentOSType |Texto |Tipo de SO do agente de cópia de segurança do Azure |
| AzureBackupAgentOSVersion |Texto |Versão de SO do agente de cópia de segurança do Azure |
| AzureBackupAgentUpdateDate |Texto |Data quando o agente de cópia de segurança do agente foi atualizado |
| AzureBackupAgentVersion |Texto |Número de versão de versão do agente de cópia de segurança |
| BackupManagementType |Texto |Tipo de fornecedor para efetuar a cópia de segurança por exemplo, IaaSVM FileFolder |
| EntityState |Texto |Estado atual do objeto de servidor protegido, por exemplo, Active Directory, eliminado |
| ProtectedServerFriendlyName |Texto |Nome amigável do servidor protegido |
| ProtectedServerName |Texto |Nome do servidor protegido |
| ProtectedServerType |Texto |Tipo de servidor protegido de uma cópia de segurança por exemplo, IaaSVMContainer |
| ProtectedServerName |Texto |Nome do servidor protegido, a qual o item de cópia de segurança pertence |
| RegisteredContainerId |Texto |O ID do contentor registado para cópia de segurança |

### <a name="storage"></a>Armazenamento
Esta tabela fornece campos básicos e agregações ao longo de vários campos relacionados com o armazenamento.

| Campo | Tipo de dados | Descrição |
| --- | --- | --- |
| #ProtectedInstances |Número decimal |Número de instâncias protegidas utilizada para calcular o armazenamento de front-end no faturação, calculada com base no valor mais recente no tempo selecionado |
| AsOnDateTime |Data/hora |Última hora de atualização para a linha selecionada |
| CloudStorageInMB |Número decimal |Armazenamento de cópia de segurança na nuvem utilizado pelo cópias de segurança, calculadas com base no valor mais recente no tempo selecionado |
| EntityState |Texto |Estado atual do objeto, por exemplo, Active Directory, eliminado |
| LastUpdatedDate |Data |Data quando linha selecionada foi atualizada pela última vez |

### <a name="time"></a>Hora
Esta tabela fornece detalhes sobre campos relacionados com o tempo.

| Campo | Tipo de dados | Descrição |
| --- | --- | --- |
| Hora |Hora |Hora do dia, por exemplo, 1:00:00 PM |
| HourNumber |Número decimal |Número de hora do dia, por exemplo, 13.00 |
| Minuto |Número decimal |Minuto da hora |
| PeriodOfTheDay |Texto |Ranhura de período de hora do dia, por exemplo, 12 3 AM |
| Hora |Hora |Hora do dia, por exemplo, 12:00:01: 00 |
| TimeKey |Texto |Valor de chave para representar tempo |

### <a name="vault"></a>Cofre
Esta tabela fornece campos básicos e agregações ao longo de vários campos relacionados com o cofre.

| Campo | Tipo de dados | Descrição |
| --- | --- | --- |
| #Vaults |Número inteiro |Número de cofres |
| AsOnDateTime |Data/hora |Última hora de atualização para a linha selecionada |
| AzureDataCenter |Texto |Onde está localizado o Cofre de centro de dados |
| EntityState |Texto |Estado atual do objeto cofre, por exemplo, Active Directory, eliminado |
| StorageReplicationType |Texto |Tipo de replicação de armazenamento para o cofre, por exemplo, GeoRedundant |
| SubscriptionId |Texto |Id de subscrição do cliente selecionado para gerar relatórios |
| VaultName |Texto |Nome do Cofre |
| VaultTags |Texto |Etiquetas associadas para o Cofre de |

## <a name="next-steps"></a>Passos seguintes
Depois de rever o modelo de dados para a criação de relatórios de cópia de segurança do Azure, consulte os artigos seguintes para obter mais detalhes sobre como criar e ver relatórios no Power BI.

* [A criação de relatórios no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
* [Filtragem de relatórios no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
