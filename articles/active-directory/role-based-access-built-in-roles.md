---
title: "Ações e NotActions - controlo de acesso do Azure baseada em funções (RBAC) | Microsoft Docs"
description: "Este tópico descreve a incorporado em funções para o controlo de acesso baseado em funções (RBAC). As funções continuamente são adicionadas, por isso a atualização de documentação."
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
editor: 
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/28/2017
ms.author: andredm
ms.reviewer: 
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9a5de00793621cfdecea887c53a22d482a25d1b8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="built-in-roles-for-azure-role-based-access-control"></a>Funções incorporadas para controlo de acesso baseado em funções do Azure
Inclui as seguintes funções incorporadas que podem ser atribuídas a utilizadores, grupos e serviços do Azure baseada em funções controlo de acesso (RBAC). Não é possível modificar as definições de funções incorporadas. No entanto, pode criar [funções personalizadas no Azure RBAC](role-based-access-control-custom-roles.md) para satisfazer as necessidades específicas da sua organização.

## <a name="roles-in-azure"></a>Funções no Azure
A tabela seguinte fornece breves descrições das funções incorporadas. Clique no nome de função para ver a lista de detalhado **ações** e **notactions** para a função. O **ações** propriedade especifica as ações permitidas nos recursos do Azure. Cadeias de ação podem utilizar carateres universais. O **notactions** propriedade especifica as ações que foram excluídas as ações permitidas.

A ação define o tipo de operações que pode efetuar num tipo de recurso especificado. Por exemplo:
- **Escrever** permite-lhe efetuar operações PUT, POST, PATCH e DELETE.
- **Leitura** permite-lhe efetuar operações GET.

Este artigo aborda apenas as diferentes funções que existe atualmente. Quando atribui uma função a um utilizador, pode limitar ainda mais as ações permitidas por definir um âmbito. Isto é útil se pretender que alguém um contribuinte de Web site, mas apenas para um grupo de recursos.

> [!NOTE]
> As definições de função do Azure são constantemente evolução. Este artigo é mantido como atualizado quanto possível, mas pode localizar sempre as definições mais recentes de funções no Azure PowerShell. Utilize o [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) cmdlet para listar todas as funções atuais. Pode começar para uma função específica a utilizar `(get-azurermroledefinition "<role name>").actions` ou `(get-azurermroledefinition "<role name>").notactions` como aplicável. Utilize [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) para operações de lista de fornecedores de recursos do Azure específicos.


| Nome da função | Descrição |
| --- | --- |
| [Contribuinte de serviço de gestão de API](#api-management-service-contributor) |Pode gerir o serviço de gestão de API e as APIs |
| [Função de operador de serviço de gestão de API](#api-management-service-operator-role) | Pode gerir o serviço de API Management, mas não as APIs próprios |
| [Função de leitor de serviço de gestão de API](#api-management-service-reader-role) | Acesso só de leitura para o serviço de API Management e das APIs |
| [Contribuinte de componentes do Application Insights](#application-insights-component-contributor) |Pode gerir componentes do Application Insights |
| [Operador de automatização](#automation-operator) |Conseguir iniciar, parar, suspender e retomar trabalhos |
| [Cópia de segurança contribuinte](#backup-contributor) | Pode gerir a cópia de segurança no Cofre de serviços de recuperação |
| [Operador de cópia de segurança](#backup-operator) | Pode gerir a cópia de segurança, exceto a remover a cópia de segurança, no Cofre de serviços de recuperação |
| [Leitor de cópia de segurança](#backup-reader) | Pode ver todos os serviços de gestão de cópia de segurança  |
| [Leitor de faturação](#billing-reader) | Pode ver todas as informações de faturação  |
| [Contribuinte de BizTalk](#biztalk-contributor) |Pode gerir os BizTalk services |
| [Contribuidor ClearDB MySQL DB](#cleardb-mysql-db-contributor) |Pode gerir bases de dados ClearDB MySQL |
| [Contribuinte](#contributor) |Podem gerir tudo, exceto o acesso. |
| [Contribuinte da fábrica de dados](#data-factory-contributor) |Pode criar e gerir as fábricas de dados e recursos subordinados dentro delas. |
| [DevTest Labs utilizador](#devtest-labs-user) |Pode ver tudo e estabelecer ligação, início, reiniciar e encerrar as máquinas virtuais |
| [Contribuinte de zona DNS](#dns-zone-contributor) |Pode gerir registos e zonas DNS |
| [Contribuinte de conta do Azure Cosmos DB](#documentdb-account-contributor) |Pode gerir contas de base de dados do Azure Cosmos |
| [Contribuinte de conta de sistemas inteligentes](#intelligent-systems-account-contributor) |Pode gerir contas de sistemas inteligentes |
| Contribuinte de aplicação lógica | Pode gerir todos os aspetos de uma aplicação lógica, mas não criar um novo. |
| Operador de aplicação lógica |Pode iniciar e parar os fluxos de trabalho definidos no âmbito de uma aplicação lógica. |
| [Leitor de monitorização](#monitoring-reader) |Pode ler todos os dados de monitorização |
| [Monitorização contribuinte](#monitoring-contributor) |Pode ler os dados de monitorização e editar as definições de monitorização |
| [Contribuidor de Rede](#network-contributor) |Pode gerir todos os recursos de rede |
| [Novo contribuinte de conta APM Relic](#new-relic-apm-account-contributor) |Pode gerir contas de gestão de desempenho de aplicações do novo Relic e as aplicações |
| [Proprietário](#owner) |Podem gerir tudo, incluindo o acesso |
| [Leitor](#reader) |Pode ver tudo, mas não é possível efetuar alterações |
| [Contribuinte de Cache de redis](#redis-cache-contributor) |Pode gerir caches de Redis |
| [Contribuinte de coleções de tarefa do agendador](#scheduler-job-collections-contributor) |Pode gerir coleções de tarefas do Programador |
| [Contribuinte de serviço de pesquisa](#search-service-contributor) |Pode gerir os serviços de pesquisa |
| [Gestor de segurança](#security-manager) |Pode gerir os componentes de segurança, as políticas de segurança e as máquinas virtuais |
| [Contribuinte de recuperação de site](#site-recovery-contributor) | Pode gerir a recuperação de sites no Cofre de serviços de recuperação |
| [Operador de recuperação de site](#site-recovery-operator) | Pode gerir a ativação pós-falha e a reativação pós-falha as operações do Site Recovery no Cofre de serviços de recuperação |
| [Leitor de recuperação de site](#site-recovery-reader) | Pode ver todas as operações de gestão de recuperação de sites  |
| [Contribuinte da BD SQL](#sql-db-contributor) |Pode gerir bases de dados SQL, mas não as respetivas políticas relacionadas com segurança |
| [Gestor de segurança do SQL Server](#sql-security-manager) |Pode gerir as políticas relacionadas com a segurança de bases de dados e servidores SQL |
| [SQL Server contribuinte](#sql-server-contributor) |Pode gerir servidores SQL e bases de dados, mas não as respetivas políticas relacionadas com segurança |
| [Contribuinte de conta de armazenamento clássico](#classic-storage-account-contributor) |Pode gerir contas de armazenamento clássicas |
| [Contribuinte de conta de armazenamento](#storage-account-contributor) |Pode gerir contas de armazenamento |
| [Contribuinte de pedido de suporte](#support-request-contributor) | Pode criar e gerir pedidos de suporte |
| [Administrador de acesso de utilizador](#user-access-administrator) |Pode gerir o acesso de utilizador nos recursos do Azure |
| [Contribuidor clássico Máquina Virtual](#classic-virtual-machine-contributor) |Pode gerir as máquinas virtuais clássicas, mas não o armazenamento de rede ou conta virtual ao qual estão ligados |
| [Contribuinte de máquina virtual](#virtual-machine-contributor) |Pode gerir máquinas virtuais, mas não o armazenamento de rede ou conta virtual ao qual estão ligados |
| [Contribuidor de Rede Clássica](#classic-network-contributor) |Pode gerir redes virtuais clássicas e IPs reservados |
| [Plano de contribuinte da Web](#web-plan-contributor) |Pode gerir planos de web |
| [Contribuinte de Web site](#website-contributor) |Pode gerir sites, mas não os planos de web ao qual estão ligados |

## <a name="role-permissions"></a>Permissões de função
As tabelas seguintes descrevem as permissões específicas para cada função. Isto pode incluir **ações**, que atribua permissões, e **NotActions**, que restringi-los.

### <a name="api-management-service-contributor"></a>Contribuinte de serviço de gestão de API
Pode gerir os serviços de gestão de API

| **Ações** |  |
| --- | --- |
| Microsoft.ApiManagement/Service/* |Criar e gerir o serviço de API Management |
| Microsoft.Authorization/*/read |Autorização de leitura |
| Microsoft.Insights/alertRules/* |Criar e gerir regras de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read |Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* |Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Funções de leitura e atribuições de função |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |

### <a name="api-management-service-operator-role"></a>Função de operador de serviço de gestão de API
Pode gerir os serviços de gestão de API

| **Ações** |  |
| --- | --- |
| Microsoft.ApiManagement/Service/*/read | Instâncias de serviço de gestão de API de leitura |
| Microsoft.ApiManagement/Service/backup/action | Fazer cópias de segurança do serviço de gestão de API para o contentor especificado um utilizador fornecida a conta de armazenamento |
| Microsoft.ApiManagement/Service/delete | Eliminar uma instância de serviço de gestão de API |
| Microsoft.ApiManagement/Service/managedeployments/action | SKU/unidades de alteração Adicionar ou remover regionais implementações do serviço de gestão de API |
| Microsoft.ApiManagement/Service/read | Ler os metadados para uma instância de serviço de gestão de API |
| Microsoft.ApiManagement/Service/restore/action | Restaurar o serviço de gestão de API a partir do contentor especificado um utilizador fornecida a conta de armazenamento |
| Microsoft.ApiManagement/Service/updatehostname/action | Configurar, atualizar ou remover nomes de domínio personalizado para um serviço de gestão de API |
| Microsoft.ApiManagement/Service/write | Criar uma nova instância do serviço de gestão de API |
| Microsoft.Authorization/*/read |Autorização de leitura |
| Microsoft.Insights/alertRules/* |Criar e gerir regras de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read |Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* |Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Funções de leitura e atribuições de função |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |

### <a name="api-management-service-reader-role"></a>Função de leitor de serviço de gestão de API
Pode gerir os serviços de gestão de API

| **Ações** |  |
| --- | --- |
| Microsoft.ApiManagement/Service/*/read | Instâncias de serviço de gestão de API de leitura |
| Microsoft.ApiManagement/Service/read | Ler os metadados para uma instância de serviço de gestão de API |
| Microsoft.Authorization/*/read |Autorização de leitura |
| Microsoft.Insights/alertRules/* |Criar e gerir regras de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read |Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* |Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Funções de leitura e atribuições de função |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |

### <a name="application-insights-component-contributor"></a>Contribuinte de componentes do Application Insights
Pode gerir componentes do Application Insights

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Funções de leitura e atribuições de função |
| Microsoft.Insights/alertRules/* |Criar e gerir regras de alertas |
| Microsoft.Insights/components/* |Criar e gerir Insights componentes |
| Microsoft.Insights/webtests/* |Criar e gerir os testes web |
| Microsoft.ResourceHealth/availabilityStatuses/read |Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* |Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Grupos de recursos de leitura |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |

### <a name="automation-operator"></a>Operador de Automatização
Conseguir iniciar, parar, suspender e retomar trabalhos

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Funções de leitura e atribuições de função |
| Microsoft.Automation/automationAccounts/jobs/read |Ler tarefas da conta de automatização |
| Microsoft.Automation/automationAccounts/jobs/resume/action |Retomar uma tarefa de conta de automatização |
| Microsoft.Automation/automationAccounts/jobs/stop/action |Parar uma tarefa de conta de automatização |
| Microsoft.Automation/automationAccounts/jobs/streams/read |Ler os fluxos de trabalho de conta de automatização |
| Microsoft.Automation/automationAccounts/jobs/suspend/action |Suspender uma tarefa de conta de automatização |
| Microsoft.Automation/automationAccounts/jobs/write |Escrever as tarefas de conta de automatização |
| Microsoft.Automation/automationAccounts/jobSchedules/read |Ler um agendamento de trabalhos de conta de automatização |
| Microsoft.Automation/automationAccounts/jobSchedules/write |Ler um agendamento de trabalhos de conta de automatização |
| Microsoft.Automation/automationAccounts/read |Contas de automatização de leitura |
| Microsoft.Automation/automationAccounts/runbooks/read |Runbooks de automatização de leitura |
| Microsoft.Automation/automationAccounts/schedules/read |Ler as agendas de conta de automatização |
| Microsoft.Automation/automationAccounts/schedules/write |Escrever as agendas de conta de automatização |
| Microsoft.Insights/components/* |Criar e gerir Insights componentes |
| Microsoft.ResourceHealth/availabilityStatuses/read |Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* |Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Grupos de recursos de leitura |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |

### <a name="backup-contributor"></a>Cópia de segurança contribuinte
Pode gerir todas as ações de gestão de cópia de segurança, exceto criar Cofre de serviços de recuperação e conceder acesso a outras pessoas

| **Ações** | |
| --- | --- |
| Microsoft.Network/virtualNetworks/read | Ler as redes virtuais |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Gerir os resultados da operação na gestão de cópia de segurança |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Criar e gerir contentores de cópia de segurança no interior de recursos de infraestrutura de cópia de segurança do Cofre de serviços de recuperação |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | Criar e gerir tarefas de cópia de segurança |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportar as tarefas de cópia de segurança para um excel |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Criar e gerir os dados de metadados relacionados com a gestão de cópia de segurança |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Criar e gerir resultados de operações de gestão de cópia de segurança |
| Microsoft.RecoveryServices/Vaults/backupPolicies/* | Criar e gerir políticas de cópia de segurança |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Criar e gerir itens que podem ser uma cópia de segurança |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Criar e gerir itens de cópias de segurança |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Criar e gerir contentores que contém os itens de cópia de segurança |
| Microsoft.RecoveryServices/Vaults/certificates/* | Criar e gerir certificados relacionados com a cópia de segurança no Cofre de serviços de recuperação |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | Criar e gerir informações expandidas relacionados com o Cofre |
| Microsoft.RecoveryServices/Vaults/read | Leia os cofres dos serviços de recuperação |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | Gerir a operação de deteção para obtenção recém-criado contentores |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Criar e gerir identidades registadas |
| Microsoft.RecoveryServices/Vaults/usages/* | Criar e gerir a utilização do Cofre de serviços de recuperação |
| Microsoft.Resources/deployments/* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Storage/storageAccounts/read | Contas de armazenamento de leitura |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |

### <a name="backup-operator"></a>Operador de cópia de segurança
Pode gerir todas as ações de gestão de cópia de segurança, exceto a criação de cofres de cópia de segurança e fornecer acesso a outras pessoas a remover

| **Ações** | |
| --- | --- |
| Microsoft.Network/virtualNetworks/read | Ler as redes virtuais |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Ler os resultados da operação na gestão de cópia de segurança |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Resultados da operação de leitura em contentores de proteção |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Efetuar a operação de cópia de segurança a pedido num item de cópia de segurança |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Resultado de leitura da operação efetuada numa cópia de segurança item |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationStatus/read | Estado de leitura da operação efetuada numa cópia de segurança item |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Ler uma cópia de segurança itens |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Ponto de recuperação de leitura de um item de cópia de segurança |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Efetuar uma operação de restauro utilizando um ponto de recuperação de um item de cópia de segurança |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Criar um item de cópia de segurança |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Leu os contentores que contém o item de cópia de segurança |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | Criar e gerir tarefas de cópia de segurança |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportar as tarefas de cópia de segurança para um excel |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Ler dados de metadados relacionados com a gestão de cópia de segurança |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Criar e gerir resultados de operações de gestão de cópia de segurança |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Resultados de leitura das operações executadas em políticas de cópia de segurança |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Políticas de cópia de leitura |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Criar e gerir itens que podem ser uma cópia de segurança |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Ler uma cópia de segurança itens |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Ler uma cópia de segurança contentores que contém os itens de cópia de segurança |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Ler informações expandidas relacionados com o Cofre |
| Microsoft.RecoveryServices/Vaults/extendedInformation/write | Escrever informações expandidas relacionados com o Cofre |
| Microsoft.RecoveryServices/Vaults/read | Leia os cofres dos serviços de recuperação |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | Gerir a operação de deteção para obtenção recém-criado contentores |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Resultados de leitura da operação efetuadas em itens de registada do Cofre de |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Ler itens registados do Cofre de |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Escrever itens registados para o Cofre |
| Microsoft.RecoveryServices/Vaults/usages/read | Utilização de leitura do cofre dos serviços de recuperação |
| Microsoft.Resources/deployments/* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Storage/storageAccounts/read | Contas de armazenamento de leitura |
| Microsoft.Support/* | Criar e gerir pedidos de suporte |

### <a name="backup-reader"></a>Leitor de cópia de segurança
Pode monitorizar a gestão de cópia de segurança no Cofre de serviços de recuperação

| **Ações** | |
| --- | --- |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read  | Ler os resultados da operação na gestão de cópia de segurança |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read  | Resultados da operação de leitura em contentores de proteção |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read  | Resultado de leitura da operação efetuada numa cópia de segurança item |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationStatus/read  | Estado de leitura da operação efetuada numa cópia de segurança item |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read  | Ler uma cópia de segurança itens |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read  | Leu os contentores que contém o item de cópia de segurança |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read  | Ler os resultados das tarefas de cópia de segurança |
| Microsoft.RecoveryServices/Vaults/backupJobs/read  | Tarefas de cópia de segurança de leitura |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportar as tarefas de cópia de segurança para um excel |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read  | Ler dados de metadados relacionados com a gestão de cópia de segurança |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/read  | Ler os resultados da operação de gestão de cópia de segurança |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read  | Resultados de leitura das operações executadas em políticas de cópia de segurança |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read  | Políticas de cópia de leitura |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read  |  Ler uma cópia de segurança itens |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read  | Ler uma cópia de segurança contentores que contém os itens de cópia de segurança |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read  | Ler informações expandidas relacionados com o Cofre |
| Microsoft.RecoveryServices/Vaults/read  | Leia os cofres dos serviços de recuperação |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read  | Resultado da operação de deteção para obtenção de recém-criado contentores de leitura |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read  | Resultados de leitura da operação efetuadas em itens de registada do Cofre de |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read  | Ler itens registados do Cofre de |
| Microsoft.RecoveryServices/Vaults/usages/read  |  Utilização de leitura do cofre dos serviços de recuperação |

### <a name="billing-reader"></a>Leitor de faturação
Pode ver todas as informações de faturação

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Funções de leitura e atribuições de função |
| Microsoft.Billing/*/read |Ler as informações de faturação |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |

### <a name="biztalk-contributor"></a>Contribuinte de BizTalk
Pode gerir os BizTalk services

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Funções de leitura e atribuições de função |
| Microsoft.BizTalkServices/BizTalk/* |Criar e gerir serviços BizTalk |
| Microsoft.Insights/alertRules/* |Criar e gerir regras de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read |Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* |Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Grupos de recursos de leitura |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |

### <a name="cleardb-mysql-db-contributor"></a>Contribuidor ClearDB MySQL DB
Pode gerir bases de dados ClearDB MySQL

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Funções de leitura e atribuições de função |
| Microsoft.Insights/alertRules/* |Criar e gerir regras de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read |Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* |Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Grupos de recursos de leitura |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |
| successbricks.cleardb/Databases/* |Criar e gerir bases de dados ClearDB MySQL |

### <a name="contributor"></a>Contribuinte
Pode gerir tudo, exceto acesso

| **Ações** |  |
| --- | --- |
| * |Criar e gerir recursos de todos os tipos |

| **NotActions** |  |
| --- | --- |
| Microsoft.Authorization/*/Delete |Não é possível eliminar funções e atribuições de função |
| Microsoft.Authorization/*/Write |Não é possível criar funções e atribuições de função |

### <a name="data-factory-contributor"></a>Contribuinte da fábrica de dados
Criar e gerir as fábricas de dados e recursos subordinados dentro delas.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Funções de leitura e atribuições de função |
| Microsoft.DataFactory/dataFactories/* |Criar e gerir as fábricas de dados e recursos subordinados dentro delas. |
| Microsoft.Insights/alertRules/* |Criar e gerir regras de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read |Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* |Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Grupos de recursos de leitura |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |

### <a name="devtest-labs-user"></a>DevTest Labs utilizador
Pode ver tudo e estabelecer ligação, início, reiniciar e encerrar as máquinas virtuais

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Funções de leitura e atribuições de função |
| Microsoft.Compute/availabilitySets/read |Ler as propriedades de conjuntos de disponibilidade |
| Microsoft.Compute/virtualMachines/*/read |Ler as propriedades de uma máquina virtual (tamanhos de VM, estado de runtime, extensões VM, etc.) |
| Microsoft.Compute/virtualMachines/deallocate/action |Anular a atribuição de máquinas virtuais |
| Microsoft.Compute/virtualMachines/read |Ler as propriedades de uma máquina virtual |
| Microsoft.Compute/virtualMachines/restart/action |Reiniciar máquinas virtuais |
| Microsoft.Compute/virtualMachines/start/action |Iniciar máquinas virtuais |
| Microsoft.DevTestLab/*/read |Ler as propriedades de um laboratório |
| Microsoft.DevTestLab/labs/createEnvironment/action |Criar um ambiente de laboratório |
| Microsoft.DevTestLab/labs/formulas/delete |Eliminar fórmulas |
| Microsoft.DevTestLab/labs/formulas/read |Fórmulas de leitura |
| Microsoft.DevTestLab/labs/formulas/write |Adiciona ou modifica as fórmulas |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action |Avaliar as políticas de laboratório |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action |Associar um conjunto de endereços de back-end de Balanceador de carga |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action |Aderir a um balanceador de carga regra NAT de entrada |
| Microsoft.Network/networkInterfaces/*/read |Ler as propriedades de uma interface de rede (por exemplo, todos os balanceadores de carga que a interface de rede faz parte de) |
| Microsoft.Network/networkInterfaces/join/action |Associar uma Máquina Virtual a uma interface de rede |
| Microsoft.Network/networkInterfaces/read |Interfaces de rede de leitura |
| Microsoft.Network/networkInterfaces/write |Escrever interfaces de rede |
| Microsoft.Network/publicIPAddresses/*/read |Ler as propriedades de um endereço IP público |
| Microsoft.Network/publicIPAddresses/join/action |Aderir a um endereço IP público |
| Microsoft.Network/publicIPAddresses/read |Leitura rede os endereços IP públicos |
| Microsoft.Network/virtualNetworks/subnets/join/action |Associar uma rede virtual |
| Microsoft.Resources/deployments/operations/read |Operações de implementação de leitura |
| Microsoft.Resources/deployments/read |Implementações de leitura |
| Microsoft.Resources/subscriptions/resourceGroups/read |Grupos de recursos de leitura |
| Microsoft.Storage/storageAccounts/listKeys/action |Lista de chaves de conta de armazenamento |

### <a name="dns-zone-contributor"></a>Contribuinte de zona DNS
Pode gerir zonas DNS e registos.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/ \* /leitura |Funções de leitura e atribuições de função |
| Microsoft.Insights/alertRules/\* |Criar e gerir regras de alertas |
| Microsoft.Network/dnsZones/\* |Criar e gerir registos e zonas DNS |
| Microsoft.ResourceHealth/availabilityStatuses/read |Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/\* |Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Grupos de recursos de leitura |
| Microsoft.Support/\* |Criar e gerir pedidos de suporte |

### <a name="azure-cosmos-db-account-contributor"></a>Contribuinte de conta do Azure Cosmos DB
Pode gerir contas de base de dados do Azure Cosmos

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Funções de leitura e atribuições de função |
| Microsoft.DocumentDb/databaseAccounts/* |Criar e gerir contas do DocumentDB |
| Microsoft.Insights/alertRules/* |Criar e gerir regras de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read |Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* |Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Grupos de recursos de leitura |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |

### <a name="intelligent-systems-account-contributor"></a>Contribuinte de conta de sistemas inteligentes
Pode gerir contas de sistemas inteligentes

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Funções de leitura e atribuições de função |
| Microsoft.Insights/alertRules/* |Criar e gerir regras de alertas |
| Microsoft.IntelligentSystems/accounts/* |Criar e gerir contas de sistemas inteligentes |
| Microsoft.ResourceHealth/availabilityStatuses/read |Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* |Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Grupos de recursos de leitura |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |

### <a name="monitoring-reader"></a>Leitor de monitorização
Pode ler todos os dados de monitorização (métricas, os registos, etc.). Consulte também [introdução à segurança com a monitorização do Azure, funções e permissões](/monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles).

| **Ações** |  |
| --- | --- |
| * / leitura |Ler os recursos de todos os tipos, exceto os segredos. |
| Microsoft.OperationalInsights/workspaces/search/action |Dados de análise de registos de pesquisa |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |

### <a name="monitoring-contributor"></a>Monitorização contribuinte
Pode ler todos os dados de monitorização e editar as definições de monitorização. Consulte também [introdução à segurança com a monitorização do Azure, funções e permissões](/monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles).

| **Ações** |  |
| --- | --- |
| * / leitura |Ler os recursos de todos os tipos, exceto os segredos. |
| Microsoft.Insights/AlertRules/* |Regras de alertas de leitura/escrita/eliminar. |
| Microsoft.Insights/components/* |Componentes do Application Insights de leitura/escrita/eliminar. |
| Microsoft.Insights/DiagnosticSettings/* |Definições de diagnóstico de leitura/escrita/eliminar. |
| Microsoft.Insights/eventtypes/* |Lista de eventos de registo de atividade (eventos de gestão) numa subscrição. Esta permissão é aplicável ao acesso programático e portal de registo de atividade. |
| Microsoft.Insights/LogDefinitions/* |Esta permissão é necessária para os utilizadores que necessitam de aceder a registos de atividade através do portal. Lista categorias de registo no registo de atividade. |
| Microsoft.Insights/MetricDefinitions/* |Ler as definições de métrica (lista de tipos de métricas disponíveis para um recurso). |
| Microsoft.Insights/Metrics/* |Ler métricas para um recurso. |
| Microsoft.Insights/Register/Action |Registe o fornecedor de insights. |
| Microsoft.Insights/webtests/* |Testes web do Application Insights de leitura/escrita/eliminar. |
| Microsoft.OperationalInsights/workspaces/intelligencepacks/* |Pacotes de solução de análise de registos de leitura/escrita/eliminar. |
| Microsoft.OperationalInsights/workspaces/savedSearches/* |Análise de registos de leitura/escrita/eliminar pesquisas guardadas. |
| Microsoft.OperationalInsights/workspaces/search/action |Pesquise áreas de trabalho de análise de registos. |
| Microsoft.OperationalInsights/workspaces/sharedKeys/action |Listar chaves para uma área de trabalho de análise de registos. |
| Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* |Configurações de conhecimentos aprofundados de armazenamento de análise de registos de leitura/escrita/eliminar. |

### <a name="network-contributor"></a>Contribuinte de rede
Pode gerir todos os recursos de rede

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Funções de leitura e atribuições de função |
| Microsoft.Insights/alertRules/* |Criar e gerir regras de alertas |
| Microsoft.Network/* |Criar e gerir redes |
| Microsoft.ResourceHealth/availabilityStatuses/read |Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* |Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Grupos de recursos de leitura |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |

### <a name="new-relic-apm-account-contributor"></a>Contribuidor de Conta APM do New Relic
Pode gerir contas de gestão de desempenho de aplicações do novo Relic e as aplicações

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Funções de leitura e atribuições de função |
| Microsoft.Insights/alertRules/* |Criar e gerir regras de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read |Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* |Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Grupos de recursos de leitura |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |
| NewRelic.APM/accounts/* |Criar e gerir contas de gestão de desempenho do New Relic aplicação |

### <a name="owner"></a>Proprietário
Podem gerir tudo, incluindo o acesso

| **Ações** |  |
| --- | --- |
| * |Criar e gerir recursos de todos os tipos |

### <a name="reader"></a>Leitor
Pode ver tudo, mas não é possível efetuar alterações

| **Ações** |  |
| --- | --- |
| * / leitura |Ler os recursos de todos os tipos, exceto os segredos. |

### <a name="redis-cache-contributor"></a>Contribuinte de Cache de redis
Pode gerir caches de Redis

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Funções de leitura e atribuições de função |
| Microsoft.Cache/redis/* |Criar e gerir caches de Redis |
| Microsoft.Insights/alertRules/* |Criar e gerir regras de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read |Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* |Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Grupos de recursos de leitura |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |

### <a name="scheduler-job-collections-contributor"></a>Contribuinte de coleções de tarefa do agendador
Pode gerir coleções de tarefas do Programador

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Funções de leitura e atribuições de função |
| Microsoft.Insights/alertRules/* |Criar e gerir regras de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read |Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* |Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Grupos de recursos de leitura |
| Microsoft.Scheduler/jobcollections/* |Criar e gerir coleções de tarefas |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |

### <a name="search-service-contributor"></a>Contribuinte de serviço de pesquisa
Pode gerir os serviços de pesquisa

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Funções de leitura e atribuições de função |
| Microsoft.Insights/alertRules/* |Criar e gerir regras de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read |Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* |Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Grupos de recursos de leitura |
| Microsoft.Search/searchServices/* |Criar e gerir serviços de pesquisa |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |

### <a name="security-manager"></a>Gestor de segurança
Pode gerir os componentes de segurança, as políticas de segurança e as máquinas virtuais

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Funções de leitura e atribuições de função |
| Microsoft.ClassicCompute/*/read |Máquinas virtuais de computação de informações de configuração de leitura clássico |
| Microsoft.ClassicCompute/virtualMachines/*/write |Escrita de configuração para máquinas virtuais |
| Microsoft.ClassicNetwork/*/read |Ler as informações de configuração sobre a rede clássico |
| Microsoft.Insights/alertRules/* |Criar e gerir regras de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read |Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* |Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Grupos de recursos de leitura |
| Microsoft.Security/* |Criar e gerir os componentes de segurança e políticas |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |

### <a name="site-recovery-contributor"></a>Contribuinte de recuperação de site
Pode gerir todas as ações de gestão de recuperação de sites, exceto criar Cofre de serviços de recuperação e atribuição de direitos de acesso a outros utilizadores

| **Ações** | |
| --- | --- |
| Microsoft.Authorization/*/read | Funções de leitura e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerir regras de alertas |
| Microsoft.Network/virtualNetworks/read | Ler as redes virtuais |
| Microsoft.RecoveryServices/Vaults/certificates/write | O certificado de credenciais do Cofre de atualizações |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | Criar e gerir informações expandidas relacionados com o Cofre |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/*  | Alertas de leitura para o Cofre de serviços de recuperação |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/leitura  | Configuração de notificação do Cofre de serviços de recuperação de leitura |
| Microsoft.RecoveryServices/Vaults/read | Os cofres dos serviços de recuperação de leitura |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | Gerir a operação de deteção para obtenção recém-criado contentores |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Criar e gerir identidades registadas |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Criar ou atualizar definições de alerta de replicação |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Ler eventos de replicação |
| Microsoft.RecoveryServices/vaults/replicationFabrics/* | Criar e gerir recursos de infraestrutura de replicação |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | Criar e gerir tarefas de replicação |
| Microsoft.RecoveryServices/vaults/replicationPolicies/* | Criar e gerir políticas de replicação |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Criar e gerir planos de recuperação |
| Microsoft.RecoveryServices/Vaults/storageConfig/* | Criar e gerir a configuração de armazenamento do Cofre de serviços de recuperação |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Informações sobre o token de cofre dos serviços de recuperação de leitura |
| Microsoft.RecoveryServices/Vaults/usages/read | Lê os detalhes de utilização de um cofre dos serviços de recuperação |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Storage/storageAccounts/read | Contas de armazenamento de leitura |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |

### <a name="site-recovery-operator"></a>Operador de recuperação de site
Pode ativação pós-falha e a reativação pós-falha, mas pode não efetuar outras ações de gestão de recuperação de sites ou atribuir acesso a outros utilizadores

| **Ações** | |
| --- | --- |
| Microsoft.Authorization/*/read | Funções de leitura e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerir regras de alertas |
| Microsoft.Network/virtualNetworks/read | Ler as redes virtuais |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Ler informações expandidas relacionados com o Cofre |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/*  | Alertas de leitura para o Cofre de serviços de recuperação |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/leitura  | Configuração de notificação do Cofre de serviços de recuperação de leitura |
| Microsoft.RecoveryServices/Vaults/read | Os cofres dos serviços de recuperação de leitura |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | Gerir a operação de deteção para obtenção recém-criado contentores |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Ler o estado da operação e o resultado de uma operação submetido |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Contentores de leitura registados para um recurso |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Ler definições de alerta de replicação |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Ler eventos de replicação |
| Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Verificação de consistência de recursos de infraestrutura |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | Recursos de infraestrutura de replicação de leitura |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ reassociateGateway/ação | Voltar a associar o gateway de replicação |
| Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Renovar certificado de recursos de infraestrutura de replicação |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Ler replicação redes de infraestrutura |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/leitura | Mapeamento de rede de recursos de infraestrutura de replicação de leitura |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/leitura | Leu os contentores de proteção |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/leitura | Obter a lista de todos os itens passíveis de proteção |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/ação | Aplicar um ponto de recuperação específico |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/failoverCommit/ação | Consolidar ativação pós-falha de uma falha ao longo do item |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/plannedFailover/ação | Iniciar ativação pós-falha planeada para um item protegido |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/leitura | Obter a lista de todos os itens protegidos |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/leitura | Obter a lista de pontos de recuperação disponíveis |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/repairReplication/ação | Reparar a replicação para um item protegido |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/Reproteção/ação | Começar a proteger novamente para um item protegido|
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailover/ação | Iniciar ativação pós-falha de teste de um item protegido |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/ação | Limpeza de início de uma ativação pós-falha de teste |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/unplannedFailover/ação | Iniciar ativação pós-falha não planeada de um item protegido |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/updateMobilityService/ação | Atualizar o serviço de mobilidade |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/leitura | Leia os mapeamentos de contentor de proteção |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/leitura | Fornecedores de serviços de recuperação de leitura |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/refreshProvider/ação | Atualize o fornecedor de serviços de recuperação |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/leitura | Classificações de armazenamento para recursos de infraestrutura de replicação de leitura |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/leitura | Mapeamentos de classificação de armazenamento de leitura |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Leitura registado informações de vCenter |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | Criar e gerir tarefas de replicação |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | Políticas de replicação de leitura |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/ failoverCommit/ação | Consolidar ativação pós-falha para ativação pós-falha do plano de recuperação |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/ plannedFailover/ação | Iniciar ativação pós-falha de um plano de recuperação |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Planos de recuperação de leitura |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Iniciar a voltar a proteger de um plano de recuperação |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Iniciar ativação pós-falha de teste de um plano de recuperação |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/ testFailoverCleanup/ação | Inicie a limpeza da ativação pós-falha de teste do plano recuperação |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/ unplannedFailover/ação | Iniciar ativação pós-falha não planeada de um plano de recuperação |
| Microsoft.RecoveryServices/Vaults/storageConfig/read | Ler a configuração de armazenamento de um cofre dos serviços de recuperação |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Informações sobre o token de cofre dos serviços de recuperação de leitura |
| Microsoft.RecoveryServices/Vaults/usages/read | Lê os detalhes de utilização de um cofre dos serviços de recuperação |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Storage/storageAccounts/read | Contas de armazenamento de leitura |
| Microsoft.Support/* | Criar e gerir pedidos de suporte |

### <a name="site-recovery-reader"></a>Leitor de recuperação de site
Pode monitorizar o estado de recuperação de sites no Cofre de serviços de recuperação e emitir pedidos de suporte

| **Ações** | |
| --- | --- |
| Microsoft.Authorization/*/read | Funções de leitura e atribuições de função |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read  | Ler informações expandidas relacionados com o Cofre |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read  | Alertas de leitura para o Cofre de serviços de recuperação |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/leitura  | Configuração de notificação do Cofre de serviços de recuperação de leitura |
| Microsoft.RecoveryServices/Vaults/read  | Os cofres dos serviços de recuperação de leitura |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read  | Gerir a operação de deteção para obtenção recém-criado contentores |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read  | Ler o estado da operação e o resultado de uma operação submetido |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read  | Contentores de leitura registados para um recurso |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Ler definições de alerta de replicação |
| Microsoft.RecoveryServices/vaults/replicationEvents/read  | Ler eventos de replicação |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read  | Recursos de infraestrutura de replicação de leitura |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read  | Ler replicação redes de infraestrutura |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/leitura  | Mapeamento de rede de recursos de infraestrutura de replicação de leitura |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/leitura  |  Leu os contentores de proteção |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/leitura  | Obter a lista de todos os itens passíveis de proteção |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/leitura  | Obter a lista de todos os itens protegidos |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/leitura  | Obter a lista de pontos de recuperação disponíveis |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/leitura  | Leia os mapeamentos de contentor de proteção |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/leitura  | Fornecedores de serviços de recuperação de leitura |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/leitura  | Classificações de armazenamento para recursos de infraestrutura de replicação de leitura |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/leitura  |  Mapeamentos de classificação de armazenamento de leitura |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read  |  Leitura registado informações de vCenter |
| Microsoft.RecoveryServices/vaults/replicationJobs/read  |  Ler o estado das tarefas de replicação |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read  |  Políticas de replicação de leitura |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read  |  Planos de recuperação de leitura |
| Microsoft.RecoveryServices/Vaults/storageConfig/read  |  Ler a configuração de armazenamento de um cofre dos serviços de recuperação |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read  |  Informações sobre o token de cofre dos serviços de recuperação de leitura |
| Microsoft.RecoveryServices/Vaults/usages/read  |  Lê os detalhes de utilização de um cofre dos serviços de recuperação |
| Microsoft.Support/*  |  Criar e gerir pedidos de suporte |

### <a name="sql-db-contributor"></a>Contribuinte da BD SQL
Pode gerir bases de dados SQL, mas não as respetivas políticas relacionadas com segurança

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Funções de leitura e atribuições de função |
| Microsoft.Insights/alertRules/* |Criar e gerir regras de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read |Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* |Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Grupos de recursos de leitura |
| Microsoft.Sql/servers/databases/* |Criar e gerir bases de dados SQL |
| Microsoft.Sql/servers/read |Servidores SQL de leitura |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/databases/auditingPolicies/* |Não é possível editar as políticas de auditoria |
| Microsoft.Sql/servers/databases/auditingSettings/* |Não é possível editar as definições de auditoria |
| Microsoft.Sql/servers/databases/auditRecords/read |Não é possível ler os registos de auditoria |
| Microsoft.Sql/servers/databases/connectionPolicies/* |Não é possível editar as políticas de ligação |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* |Não é possível editar as políticas de máscara de dados |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* |Não é possível editar as políticas de alerta de segurança |
| Microsoft.Sql/servers/databases/securityMetrics/* |Não é possível editar as métricas de segurança |

### <a name="sql-security-manager"></a>Gestor de segurança do SQL Server
Pode gerir as políticas relacionadas com a segurança de bases de dados e servidores SQL

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Autorização de leitura Microsoft |
| Microsoft.Insights/alertRules/* |Criar e gerir regras de alerta Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read |Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* |Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Grupos de recursos de leitura |
| Microsoft.Sql/servers/auditingPolicies/* |Criar e gerir políticas de auditoria do SQL server |
| Microsoft.Sql/servers/auditingSettings/* |Criar e gerir a definição de auditoria do servidor SQL |
| Microsoft.Sql/servers/databases/auditingPolicies/* |Criar e gerir políticas de auditoria de base de dados do SQL server |
| Microsoft.Sql/servers/databases/auditingSettings/* |Criar e gerir definições de auditoria de base de dados do SQL server |
| Microsoft.Sql/servers/databases/auditRecords/read |Registos de auditoria de leitura |
| Microsoft.Sql/servers/databases/connectionPolicies/* |Criar e gerir políticas de ligação de base de dados do SQL server |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* |Criar e gerir políticas de máscara de dados do SQL server da base de dados |
| Microsoft.Sql/servers/databases/read |Bases de dados SQL de leitura |
| Microsoft.Sql/servers/databases/schemas/read |Esquemas de base de dados do servidor de SQL de leitura |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read |Colunas de tabela de base de dados de servidor de SQL de leitura |
| Microsoft.Sql/servers/databases/schemas/tables/read |Tabelas de base de dados do servidor de SQL de leitura |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* |Criar e gerir políticas alertas de segurança da base de dados de servidor SQL |
| Microsoft.Sql/servers/databases/securityMetrics/* |Criar e gerir as métricas de segurança de base de dados do SQL server |
| Microsoft.Sql/servers/read |Servidores SQL de leitura |
| Microsoft.Sql/servers/securityAlertPolicies/* |Criar e gerir políticas de alerta de segurança do SQL server |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |

### <a name="sql-server-contributor"></a>SQL Server contribuinte
Pode gerir servidores SQL e de bases de dados, mas não as respetivas políticas relacionadas com segurança

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Autorização de leitura |
| Microsoft.Insights/alertRules/* |Criar e gerir regras de alerta Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read |Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* |Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Grupos de recursos de leitura |
| Microsoft.Sql/servers/* |Criar e gerir servidores SQL |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/auditingPolicies/* |Não é possível editar as políticas de auditoria do SQL server |
| Microsoft.Sql/servers/auditingSettings/* |Não é possível editar definições de auditoria do SQL server |
| Microsoft.Sql/servers/databases/auditingPolicies/* |Não é possível editar as políticas de auditoria de base de dados do SQL server |
| Microsoft.Sql/servers/databases/auditingSettings/* |Não é possível editar definições de auditoria de base de dados do SQL server |
| Microsoft.Sql/servers/databases/auditRecords/read |Não é possível ler os registos de auditoria |
| Microsoft.Sql/servers/databases/connectionPolicies/* |Não é possível editar as políticas de ligação de base de dados do SQL server |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* |Não é possível editar as políticas de máscara de dados do SQL server da base de dados |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* |Não é possível editar o SQL server da base de dados alerta as políticas de segurança |
| Microsoft.Sql/servers/databases/securityMetrics/* |Não é possível editar as métricas de segurança de base de dados do SQL server |
| Microsoft.Sql/servers/securityAlertPolicies/* |Não é possível editar as políticas de alerta de segurança do SQL server |

### <a name="classic-storage-account-contributor"></a>Contribuinte de conta de armazenamento clássico
Pode gerir contas de armazenamento clássicas

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Autorização de leitura |
| Microsoft.ClassicStorage/storageAccounts/* |Criar e gerir contas de armazenamento |
| Microsoft.Insights/alertRules/* |Criar e gerir regras de alerta Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read |Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* |Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Grupos de recursos de leitura |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |

### <a name="storage-account-contributor"></a>Contribuinte de conta de armazenamento
Pode gerir as contas de armazenamento, mas não aceder aos mesmos.

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Ler todas as autorização |
| Microsoft.Insights/alertRules/* |Criar e gerir regras de alerta Insights |
| Microsoft.Insights/diagnosticSettings/* |Gerir definições de diagnóstico |
| Microsoft.ResourceHealth/availabilityStatuses/read |Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* |Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Grupos de recursos de leitura |
| Microsoft.Storage/storageAccounts/* |Criar e gerir contas de armazenamento |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |

### <a name="support-request-contributor"></a>Contribuinte de pedido de suporte
Pode criar e gerir pedidos de suporte no âmbito de subscrição

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Autorização de leitura |
| Microsoft.Support/* | Criar e gerir pedidos de suporte |
| Microsoft.Resources/subscriptions/resourceGroups/read | Funções de leitura e atribuições de função |

### <a name="user-access-administrator"></a>Administrador de Acesso de Utilizador
Pode gerir o acesso de utilizador nos recursos do Azure

| **Ações** |  |
| --- | --- |
| * / leitura |Ler os recursos de todos os tipos, exceto os segredos. |
| Microsoft.Authorization/* |Gerir autorização |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |

### <a name="classic-virtual-machine-contributor"></a>Contribuidor clássico Máquina Virtual
Pode gerir as máquinas virtuais clássicas, mas não o armazenamento de rede ou conta virtual ao qual estão ligados

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Autorização de leitura |
| Microsoft.ClassicCompute/domainNames/* |Criar e gerir nomes de domínio de computação clássicos |
| Microsoft.ClassicCompute/virtualMachines/* |Criar e gerir máquinas virtuais |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action |Associar grupos de segurança de rede |
| Microsoft.ClassicNetwork/reservedIps/link/action |Ligação IPs reservados |
| Microsoft.ClassicNetwork/reservedIps/read |Leitura endereços IP reservados |
| Microsoft.ClassicNetwork/virtualNetworks/join/action |Associar redes virtuais |
| Microsoft.ClassicNetwork/virtualNetworks/read |Ler as redes virtuais |
| Microsoft.ClassicStorage/storageAccounts/disks/read |Leitura de discos de conta de armazenamento |
| Microsoft.ClassicStorage/storageAccounts/images/read |Ler as imagens de conta de armazenamento |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action |Lista de chaves de conta de armazenamento |
| Microsoft.ClassicStorage/storageAccounts/read |Contas de armazenamento clássicas de leitura |
| Microsoft.Insights/alertRules/* |Criar e gerir regras de alerta Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read |Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* |Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Grupos de recursos de leitura |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |

### <a name="virtual-machine-contributor"></a>Contribuinte de máquina virtual
Pode gerir máquinas virtuais, mas não o armazenamento de rede ou conta virtual ao qual estão ligados

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Autorização de leitura |
| Microsoft.Compute/availabilitySets/* |Criar e gerir conjuntos de disponibilidade de computação |
| Microsoft.Compute/locations/* |Criar e gerir as localizações de computação |
| Microsoft.Compute/virtualMachines/* |Criar e gerir máquinas virtuais |
| Microsoft.Compute/virtualMachineScaleSets/* |Criar e gerir conjuntos de dimensionamento de máquina virtual |
| Microsoft.Insights/alertRules/* |Criar e gerir regras de alerta Insights |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action |Associar conjuntos de endereços de back-end de gateway de aplicação de rede |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action |Associar conjuntos de endereços de back-end de Balanceador de carga |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action |Associar o Balanceador de carga conjuntos NAT de entrada |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action |Associar o Balanceador de carga regras NAT de entrada |
| Microsoft.Network/loadBalancers/read |Balanceadores de carga de leitura |
| Microsoft.Network/locations/* |Criar e gerir as localizações de rede |
| Microsoft.Network/networkInterfaces/* |Criar e gerir as interfaces de rede |
| Microsoft.Network/networkSecurityGroups/join/action |Associar grupos de segurança de rede |
| Microsoft.Network/networkSecurityGroups/read |Leia os grupos de segurança de rede |
| Microsoft.Network/publicIPAddresses/join/action |Associar endereços IP públicos de rede |
| Microsoft.Network/publicIPAddresses/read |Leitura rede os endereços IP públicos |
| Microsoft.Network/virtualNetworks/read |Ler as redes virtuais |
| Microsoft.Network/virtualNetworks/subnets/join/action |Associe sub-redes da rede virtual |
| Microsoft.ResourceHealth/availabilityStatuses/read |Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* |Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Grupos de recursos de leitura |
| Microsoft.Storage/storageAccounts/listKeys/action |Lista de chaves de conta de armazenamento |
| Microsoft.Storage/storageAccounts/read |Contas de armazenamento de leitura |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |

### <a name="classic-network-contributor"></a>Contribuinte de rede clássico
Pode gerir redes virtuais clássicas e IPs reservados

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Autorização de leitura |
| Microsoft.ClassicNetwork/* |Criar e gerir redes clássicos |
| Microsoft.Insights/alertRules/* |Criar e gerir regras de alerta Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read |Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* |Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Grupos de recursos de leitura |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |

### <a name="web-plan-contributor"></a>Plano de contribuinte da Web
Pode gerir planos de web

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Autorização de leitura |
| Microsoft.Insights/alertRules/* |Criar e gerir regras de alerta Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read |Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* |Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Grupos de recursos de leitura |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |
| Microsoft.Web/serverFarms/* |Criar e gerir farms de servidores |

### <a name="website-contributor"></a>Contribuinte de Web site
Pode gerir sites, mas não os planos de web ao qual estão ligados

| **Ações** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Autorização de leitura |
| Microsoft.Insights/alertRules/* |Criar e gerir regras de alerta Insights |
| Microsoft.Insights/components/* |Criar e gerir Insights componentes |
| Microsoft.ResourceHealth/availabilityStatuses/read |Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* |Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Grupos de recursos de leitura |
| Microsoft.Support/* |Criar e gerir pedidos de suporte |
| Microsoft.Web/certificates/* |Criar e gerir certificados de Web site |
| Microsoft.Web/listSitesAssignedToHostName/read |Sites de leitura atribuídos a um nome de anfitrião |
| Microsoft.Web/serverFarms/join/action |Associar farms de servidores |
| Microsoft.Web/serverFarms/read |Ler farms de servidores |
| Microsoft.Web/sites/* |Criar e gerir sites (criação de site também necessita de permissões de escrita para o plano de serviço de aplicação associado) |

## <a name="see-also"></a>Consultar também
* [Controlo de acesso baseado em funções](role-based-access-control-configure.md): começar a utilizar o RBAC no portal do Azure.
* [Funções personalizadas no Azure RBAC](role-based-access-control-custom-roles.md): Saiba como criar funções personalizadas para se ajustarem às suas necessidades de acesso.
* [Criar um relatório de histórico de alterações de acesso](role-based-access-control-access-change-history-report.md): manter um registo dos alteração atribuições de funções no RBAC.
* [Resolução de problemas de controlo de acesso baseado em funções](role-based-access-control-troubleshooting.md): Obtenha sugestões sobre como corrigir problemas comuns.
