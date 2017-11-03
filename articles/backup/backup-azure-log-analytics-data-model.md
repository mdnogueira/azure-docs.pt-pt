---
title: Modelo de dados do Log Analytics para o Azure Backup
description: "Este artigo aborda detalhes de modelo de dados de análise de registos de dados de cópia de segurança do Azure."
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: dfd5c73d-0d34-4d48-959e-1936986f9fc0
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/24/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 041a8835a1dd185739b23d4073fd5811bb4490b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Modelo de dados de análise de registos de dados de cópia de segurança do Azure
Este artigo descreve o modelo de dados utilizado para enviar dados de relatórios para análise de registos. Utilizar este modelo de dados, pode criar consultas personalizadas, dashboards e utilizá-lo no OMS. 

## <a name="using-azure-backup-data-model"></a>Utilizar o modelo de dados de cópia de segurança do Azure
Pode utilizar os seguintes campos fornecidos como parte do modelo de dados para criar os visuais, consultas personalizadas e um dashboard de acordo com os seus requisitos.

### <a name="alert"></a>Alerta
Esta tabela fornece detalhes sobre os campos de alerta relacionados.

| Campo | Tipo de dados | Descrição |
| --- | --- | --- |
| AlertUniqueId_s |Texto |Id exclusivo do alerta gerado |
| AlertType_s |Texto |Tipo de alerta gerado, por exemplo, cópia de segurança |
| AlertStatus_s |Texto |Estado do alerta, por exemplo, Active Directory |
| AlertOccurenceDateTime_s |Data/hora |Data e hora quando o alerta foi criado |
| AlertSeverity_s |Texto |Gravidade do alerta, por exemplo, crítico |
| EventName_s |Texto |Este campo representa o nome deste evento, é sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Texto |Id exclusivo do item de cópia de segurança a que este alerta pertence a |
| SchemaVersion_s |Texto |Este campo indica que a versão atual do esquema, é **V1** |
| State_s |Texto |Estado atual do objeto do alerta, por exemplo, o Active Directory, o eliminado |
| BackupManagementType_s |Texto |Tipo de fornecedor para efetuar FileFolder de cópia de segurança, por exemplo, IaaSVM, ao qual este alerta pertence |
| OperationName |Texto |Este campo representa o nome da operação atual - alerta |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico enviados para análise de registos, é AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual estão a ser recolhidos dados, mostra o nome do cofre dos serviços de recuperação |
| ProtectedServerUniqueId_s |Texto |Id exclusivo de protegida ao qual este alerta pertence |
| VaultUniqueId_s |Texto |Id exclusivo de protegida ao qual este alerta pertence |
| SourceSystem |Texto |Sistema de origem de dados atuais - Azure |
| ResourceId |Texto |Este campo representa id do recurso para o qual estão a ser recolhidos dados, mostra o id de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Texto |Este campo representa o id do recurso (RS cofre) para o qual estão a ser recolhidos dados de subscrição |
| ResourceGroup |Texto |Este campo representa o grupo de recursos do recurso (RS cofre) para o qual estão a ser recolhidos dados |
| ResourceProvider |Texto |Este campo representa o fornecedor de recursos para o qual estão a ser recolhidos dados - Microsoft.RecoveryServices |
| ResourceType |Texto |Este campo representa o tipo de recurso para o qual estão a ser recolhidos dados - cofres |

### <a name="backupitem"></a>BackupItem
Esta tabela fornece detalhes sobre campos relacionados com o item de cópia de segurança.

| Campo | Tipo de dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa o nome deste evento, é sempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Texto |Id exclusivo do item de cópia de segurança |
| BackupItemId_s |Texto |ID de item de cópia de segurança |
| BackupItemName_s |Texto |Nome do item de cópia de segurança |
| BackupItemFriendlyName_s |Texto |Nome amigável do item de cópia de segurança |
| BackupItemType_s |Texto |Tipo de item de cópia de segurança, por exemplo, VM, FileFolder |
| ProtectedServerName_s |Texto |Nome do servidor protegido, a qual o item de cópia de segurança pertence |
| ProtectionState_s |Texto |Estado de proteção atual do item de cópia de segurança, por exemplo, protegidos, ProtectionStopped |
| SchemaVersion_s |Texto |Este campo indica que a versão atual do esquema, é **V1** |
| State_s |Texto |Estado atual do objeto do item de cópia de segurança, por exemplo, o Active Directory, o eliminado |
| BackupManagementType_s |Texto |Tipo de fornecedor para efetuar FileFolder de cópia de segurança, por exemplo, IaaSVM, para que este item de cópia de segurança pertence |
| OperationName |Texto |Este campo representa o nome da operação atual - BackupItem |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico enviados para análise de registos, é AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual estão a ser recolhidos dados, mostra o nome do cofre dos serviços de recuperação |
| SourceSystem |Texto |Sistema de origem de dados atuais - Azure |
| ResourceId |Texto |Este campo representa id do recurso para o qual estão a ser recolhidos dados, mostra o id de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Texto |Este campo representa o id do recurso (RS cofre) para o qual estão a ser recolhidos dados de subscrição |
| ResourceGroup |Texto |Este campo representa o grupo de recursos do recurso (RS cofre) para o qual estão a ser recolhidos dados |
| ResourceProvider |Texto |Este campo representa o fornecedor de recursos para o qual estão a ser recolhidos dados - Microsoft.RecoveryServices |
| ResourceType |Texto |Este campo representa o tipo de recurso para o qual estão a ser recolhidos dados - cofres |

### <a name="backupitemassociation"></a>BackupItemAssociation
Esta tabela fornece detalhes sobre as associações de item de cópia de segurança com várias entidades.

| Campo | Tipo de dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa o nome deste evento, é sempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Texto |Id exclusivo do item de cópia de segurança |
| SchemaVersion_s |Texto |Este campo indica que a versão atual do esquema, é **V1** |
| State_s |Texto |Estado atual do objeto de associação do item de cópia de segurança, por exemplo, o Active Directory, o eliminado |
| BackupManagementType_s |Texto |Tipo de fornecedor para efetuar FileFolder de cópia de segurança, por exemplo, IaaSVM, para que este item de cópia de segurança pertence |
| OperationName |Texto |Este campo representa o nome da operação atual - BackupItemAssociation |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico enviados para análise de registos, é AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual estão a ser recolhidos dados, mostra o nome do cofre dos serviços de recuperação |
| PolicyUniqueId_g |Texto |Id exclusivo para identificar a política, o qual o item de cópia de segurança está associado a |
| ProtectedServerUniqueId_s |Texto |Id exclusivo do servidor protegido ao qual este item de cópia de segurança pertence |
| VaultUniqueId_s |Texto |Id exclusivo do cofre a que este item de cópia de segurança pertence a |
| SourceSystem |Texto |Sistema de origem de dados atuais - Azure |
| ResourceId |Texto |Este campo representa id do recurso para o qual estão a ser recolhidos dados, mostra o id de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Texto |Este campo representa o id do recurso (RS cofre) para o qual estão a ser recolhidos dados de subscrição |
| ResourceGroup |Texto |Este campo representa o grupo de recursos do recurso (RS cofre) para o qual estão a ser recolhidos dados |
| ResourceProvider |Texto |Este campo representa o fornecedor de recursos para o qual estão a ser recolhidos dados - Microsoft.RecoveryServices |
| ResourceType |Texto |Este campo representa o tipo de recurso para o qual estão a ser recolhidos dados - cofres |

### <a name="job"></a>Tarefa
Esta tabela fornece detalhes sobre campos relacionados com a tarefa.

| Campo | Tipo de dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa o nome deste evento, é sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Texto |Id exclusivo do item de cópia de segurança a que esta tarefa pertence a |
| SchemaVersion_s |Texto |Este campo indica que a versão atual do esquema, é **V1** |
| State_s |Texto |Estado atual do objeto tarefa, por exemplo, o Active Directory, o eliminado |
| BackupManagementType_s |Texto |Tipo de fornecedor para efetuar FileFolder de cópia de segurança, por exemplo, IaaSVM, a que esta tarefa pertence a |
| OperationName |Texto |Este campo representa o nome da operação atual - tarefa |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico enviados para análise de registos, é AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual estão a ser recolhidos dados, mostra o nome do cofre dos serviços de recuperação |
| ProtectedServerUniqueId_s |Texto |Id exclusivo de protegida a que esta tarefa pertence a |
| VaultUniqueId_s |Texto |Id exclusivo de protegida a que esta tarefa pertence a |
| JobOperation_s |Texto |Operação para o qual tarefa é executada por exemplo, cópia de segurança, restauro, configurar a cópia de segurança |
| JobStatus_s |Texto |Estado da tarefa foi terminada, por exemplo, concluído, com falhas |
| JobFailureCode_s |Texto |Cadeia de código de falha devido ao qual foi efetuada a falha da tarefa |
| JobStartDateTime_s |Data/hora |Data e hora quando a tarefa foi iniciada em execução |
| BackupStorageDestination_s |Texto |Destino de cópia de segurança armazenamento, por exemplo, nuvem, o disco  |
| JobDurationInSecs_s | Número |Duração total da tarefa em segundos |
| DataTransferredInMB_s | Número |Dados transferidos em MB para esta tarefa|
| JobUniqueId_g |Texto |Id exclusivo para identificar a tarefa |
| SourceSystem |Texto |Sistema de origem de dados atuais - Azure |
| ResourceId |Texto |Este campo representa id do recurso para o qual estão a ser recolhidos dados, mostra o id de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Texto |Este campo representa o id do recurso (RS cofre) para o qual estão a ser recolhidos dados de subscrição |
| ResourceGroup |Texto |Este campo representa o grupo de recursos do recurso (RS cofre) para o qual estão a ser recolhidos dados |
| ResourceProvider |Texto |Este campo representa o fornecedor de recursos para o qual estão a ser recolhidos dados - Microsoft.RecoveryServices |
| ResourceType |Texto |Este campo representa o tipo de recurso para o qual estão a ser recolhidos dados - cofres |

### <a name="policy"></a>Política
Esta tabela fornece detalhes sobre campos relacionados com a política.

| Campo | Tipo de dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa o nome deste evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto |Este campo indica que a versão atual do esquema, é **V1** |
| State_s |Texto |Estado atual do objeto de política, por exemplo, o Active Directory, o eliminado |
| BackupManagementType_s |Texto |Tipo de fornecedor para efetuar FileFolder de cópia de segurança, por exemplo, IaaSVM, a qual esta política pertence a |
| OperationName |Texto |Este campo representa o nome da operação atual - política |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico enviados para análise de registos, é AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual estão a ser recolhidos dados, mostra o nome do cofre dos serviços de recuperação |
| PolicyUniqueId_g |Texto |Id exclusivo para identificar a política |
| PolicyName_s |Texto |Nome da política definida |
| BackupFrequency_s |Texto |Frequência com que sejam executadas cópias de segurança, por exemplo, diária, semanal |
| BackupTimes_s |Texto |Data e hora quando estão agendadas cópias de segurança |
| BackupDaysOfTheWeek_s |Texto |Dias da semana quando tem sido agendadas cópias de segurança |
| RetentionDuration_s |Número inteiro |Duração de retenção para cópias de segurança configuradas |
| DailyRetentionDuration_s |Número inteiro |Duração total de retenção em dias para cópias de segurança configurados |
| DailyRetentionTimes_s |Texto |Data e hora quando a retenção diária foi configurada |
| WeeklyRetentionDuration_s |Número decimal |Duração de retenção semanais total no semanas para as cópias de segurança configuradas |
| WeeklyRetentionTimes_s |Texto |Data e hora quando a retenção semana está configurada |
| WeeklyRetentionDaysOfTheWeek_s |Texto |Dias da semana selecionados para retenção semana |
| MonthlyRetentionDuration_s |Número decimal |Duração de retenção total em meses para cópias de segurança configuradas |
| MonthlyRetentionTimes_s |Texto |Data e hora quando a retenção mensal está configurada |
| MonthlyRetentionFormat_s |Texto |Tipo de configuração de retenção mensal, por exemplo, diária, dia, com base, semanalmente semana com base |
| MonthlyRetentionDaysOfTheWeek_s |Texto |Dias da semana selecionados para retenção mensal |
| MonthlyRetentionWeeksOfTheMonth_s |Texto |Semanas do mês quando a retenção mensal estiver configurada, por exemplo, First, Last etc. |
| YearlyRetentionDuration_s |Número decimal |Duração de retenção total em anos para cópias de segurança configurados |
| YearlyRetentionTimes_s |Texto |Data e hora quando a retenção anual está configurada |
| YearlyRetentionMonthsOfTheYear_s |Texto |Meses do ano selecionada para a retenção anual |
| YearlyRetentionFormat_s |Texto |Tipo de configuração para a retenção anual, por exemplo, diária, dia, com base, semanalmente semana com base |
| YearlyRetentionDaysOfTheMonth_s |Texto |Datas do mês selecionado para a retenção anual |
| SourceSystem |Texto |Sistema de origem de dados atuais - Azure |
| ResourceId |Texto |Este campo representa id do recurso para o qual estão a ser recolhidos dados, mostra o id de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Texto |Este campo representa o id do recurso (RS cofre) para o qual estão a ser recolhidos dados de subscrição |
| ResourceGroup |Texto |Este campo representa o grupo de recursos do recurso (RS cofre) para o qual estão a ser recolhidos dados |
| ResourceProvider |Texto |Este campo representa o fornecedor de recursos para o qual estão a ser recolhidos dados - Microsoft.RecoveryServices |
| ResourceType |Texto |Este campo representa o tipo de recurso para o qual estão a ser recolhidos dados - cofres |

### <a name="policyassociation"></a>PolicyAssociation
Esta tabela fornece detalhes sobre as associações de política com várias entidades.

| Campo | Tipo de dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa o nome deste evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto |Este campo indica que a versão atual do esquema, é **V1** |
| State_s |Texto |Estado atual do objeto de política, por exemplo, o Active Directory, o eliminado |
| BackupManagementType_s |Texto |Tipo de fornecedor para efetuar a cópia de segurança por exemplo, IaaSVM FileFolder a que esta política pertence a |
| OperationName |Texto |Este campo representa o nome da operação atual - PolicyAssociation |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico enviados para análise de registos, é AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual estão a ser recolhidos dados, mostra o nome do cofre dos serviços de recuperação |
| PolicyUniqueId_g |Texto |Id exclusivo para identificar a política |
| VaultUniqueId_s |Texto |Id exclusivo do cofre para os quais esta política pertence |
| SourceSystem |Texto |Sistema de origem de dados atuais - Azure |
| ResourceId |Texto |Este campo representa id do recurso para o qual estão a ser recolhidos dados, mostra o id de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Texto |Este campo representa o id do recurso (RS cofre) para o qual estão a ser recolhidos dados de subscrição |
| ResourceGroup |Texto |Este campo representa o grupo de recursos do recurso (RS cofre) para o qual estão a ser recolhidos dados |
| ResourceProvider |Texto |Este campo representa o fornecedor de recursos para o qual estão a ser recolhidos dados - Microsoft.RecoveryServices |
| ResourceType |Texto |Este campo representa o tipo de recurso para o qual estão a ser recolhidos dados - cofres |

### <a name="protectedserver"></a>ProtectedServer
Esta tabela fornece detalhes sobre campos relacionados com o servidor protegidos.

| Campo | Tipo de dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa o nome deste evento, é sempre AzureBackupCentralReport |
| ProtectedServerName_s |Texto |Nome do servidor protegido |
| SchemaVersion_s |Texto |Este campo indica que a versão atual do esquema, é **V1** |
| State_s |Texto |Estado atual do objeto do servidor protegido, por exemplo, o Active Directory, o eliminado |
| BackupManagementType_s |Texto |Tipo de fornecedor para efetuar a cópia de segurança por exemplo, IaaSVM FileFolder a que este servidor protegido pertence a |
| OperationName |Texto |Este campo representa o nome da operação atual - ProtectedServer |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico enviados para análise de registos, é AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual estão a ser recolhidos dados, mostra o nome do cofre dos serviços de recuperação |
| ProtectedServerUniqueId_s |Texto |Id exclusivo do servidor protegido |
| RegisteredContainerId_s |Texto |O ID do contentor registado para cópia de segurança |
| ProtectedServerType_s |Texto |Tipo de servidor protegido de uma cópia de segurança por exemplo, o Windows |
| ProtectedServerFriendlyName_s |Texto |Nome amigável do servidor protegido |
| AzureBackupAgentVersion_s |Texto |Número de versão de versão do agente de cópia de segurança |
| SourceSystem |Texto |Sistema de origem de dados atuais - Azure |
| ResourceId |Texto |Este campo representa id do recurso para o qual estão a ser recolhidos dados, mostra o id de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Texto |Este campo representa o id do recurso (RS cofre) para o qual estão a ser recolhidos dados de subscrição |
| ResourceGroup |Texto |Este campo representa o grupo de recursos do recurso (RS cofre) para o qual estão a ser recolhidos dados |
| ResourceProvider |Texto |Este campo representa o fornecedor de recursos para o qual estão a ser recolhidos dados - Microsoft.RecoveryServices |
| ResourceType |Texto |Este campo representa o tipo de recurso para o qual estão a ser recolhidos dados - cofres |

### <a name="protectedserverassociation"></a>ProtectedServerAssociation
Esta tabela fornece detalhes sobre as associações de servidor protegido com outras entidades.

| Campo | Tipo de dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa o nome deste evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto |Este campo indica que a versão atual do esquema, é **V1** |
| State_s |Texto |Estado atual do objeto de associação do servidor protegido, por exemplo, o Active Directory, o eliminado |
| BackupManagementType_s |Texto |Tipo de fornecedor para efetuar FileFolder de cópia de segurança, por exemplo, IaaSVM, para que este servidor protegido pertence |
| OperationName |Texto |Este campo representa o nome da operação atual - ProtectedServerAssociation |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico enviados para análise de registos, é AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual estão a ser recolhidos dados, mostra o nome do cofre dos serviços de recuperação |
| ProtectedServerUniqueId_s |Texto |Id exclusivo do servidor protegido |
| VaultUniqueId_s |Texto |Id exclusivo do cofre a que este servidor protegido pertence a |
| SourceSystem |Texto |Sistema de origem de dados atuais - Azure |
| ResourceId |Texto |Este campo representa id do recurso para o qual estão a ser recolhidos dados, mostra o id de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Texto |Este campo representa o id do recurso (RS cofre) para o qual estão a ser recolhidos dados de subscrição |
| ResourceGroup |Texto |Este campo representa o grupo de recursos do recurso (RS cofre) para o qual estão a ser recolhidos dados |
| ResourceProvider |Texto |Este campo representa o fornecedor de recursos para o qual estão a ser recolhidos dados - Microsoft.RecoveryServices |
| ResourceType |Texto |Este campo representa o tipo de recurso para o qual estão a ser recolhidos dados - cofres |

### <a name="storage"></a>Armazenamento
Esta tabela fornece detalhes sobre campos relacionados com o armazenamento.

| Campo | Tipo de dados | Descrição |
| --- | --- | --- |
| CloudStorageInBytes_s |Número decimal |Armazenamento de cópia de segurança na nuvem utilizado pelo cópias de segurança, calculadas com base no valor mais recente |
| ProtectedInstances_s |Número decimal |Número de instâncias protegidas utilizada para calcular o armazenamento de front-end no faturação, calculada com base no valor mais recente |
| EventName_s |Texto |Este campo representa o nome deste evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto |Este campo indica que a versão atual do esquema, é **V1** |
| State_s |Texto |Estado atual do objeto de armazenamento, por exemplo, o Active Directory, o eliminado |
| BackupManagementType_s |Texto |Tipo de fornecedor para efetuar FileFolder de cópia de segurança, por exemplo, IaaSVM, para que este tipo de armazenamento pertence |
| OperationName |Texto |Este campo representa o nome da operação atual - armazenamento |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico enviados para análise de registos, é AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual estão a ser recolhidos dados, mostra o nome do cofre dos serviços de recuperação |
| ProtectedServerUniqueId_s |Texto |Id exclusivo do servidor protegido para o qual é calculado de armazenamento |
| VaultUniqueId_s |Texto |Id exclusivo do Cofre de armazenamento é calculado |
| SourceSystem |Texto |Sistema de origem de dados atuais - Azure |
| ResourceId |Texto |Este campo representa id do recurso para o qual estão a ser recolhidos dados, mostra o id de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Texto |Este campo representa o id do recurso (RS cofre) para o qual estão a ser recolhidos dados de subscrição |
| ResourceGroup |Texto |Este campo representa o grupo de recursos do recurso (RS cofre) para o qual estão a ser recolhidos dados |
| ResourceProvider |Texto |Este campo representa o fornecedor de recursos para o qual estão a ser recolhidos dados - Microsoft.RecoveryServices |
| ResourceType |Texto |Este campo representse tipo do recurso para o qual estão a ser recolhidos dados - cofres |

### <a name="vault"></a>Cofre
Esta tabela fornece detalhes sobre campos relacionados com o cofre.

| Campo | Tipo de dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa o nome deste evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto |Este campo indica que a versão atual do esquema, é **V1** |
| State_s |Texto |Estado atual do objeto do cofre, por exemplo, o Active Directory, o eliminado |
| OperationName |Texto |Este campo representa o nome da operação atual - Cofre |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico enviados para análise de registos, é AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual estão a ser recolhidos dados, mostra o nome do cofre dos serviços de recuperação |
| VaultUniqueId_s |Texto |Id exclusivo do Cofre de |
| VaultName_s |Texto |Nome do Cofre |
| AzureDataCenter_s |Texto |Onde está localizado o Cofre de centro de dados |
| StorageReplicationType_s |Texto |Tipo de replicação de armazenamento para o cofre, por exemplo, GeoRedundant |
| SourceSystem |Texto |Sistema de origem de dados atuais - Azure |
| ResourceId |Texto |Este campo representa id do recurso para o qual estão a ser recolhidos dados, mostra o id de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Texto |Este campo representa o id do recurso (RS cofre) para o qual estão a ser recolhidos dados de subscrição |
| ResourceGroup |Texto |Este campo representa o grupo de recursos do recurso (RS cofre) para o qual estão a ser recolhidos dados |
| ResourceProvider |Texto |Este campo representa o fornecedor de recursos para o qual estão a ser recolhidos dados - Microsoft.RecoveryServices |
| ResourceType |Texto |Este campo representa o tipo de recurso para o qual estão a ser recolhidos dados - cofres |

## <a name="next-steps"></a>Passos seguintes
Depois de rever o modelo de dados para a criação de relatórios de cópia de segurança do Azure, pode começar a [criar dashboard](../log-analytics/log-analytics-dashboards.md) na análise de registos e OMS.
