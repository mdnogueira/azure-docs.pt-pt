---
title: "Operações de fornecedor do Gestor de recursos do Azure | Microsoft Docs"
description: "Fornece detalhes sobre as operações disponíveis nos fornecedores de recursos do Microsoft Azure Resource Manager"
services: active-directory
documentationcenter: 
author: jboeshart
manager: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2017
ms.author: jaboes
ms.openlocfilehash: 9fe7a5f254d8b384cae10ecd34e0bdafa433ff13
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Operações de fornecedor de recursos do Gestor de recursos do Azure

Este documento, lista as operações disponíveis para cada fornecedor de recursos do Microsoft Azure Resource Manager. Estes podem ser utilizados em funções personalizadas para fornecer permissões de controlo de acesso baseado em funções (RBAC) granulares a recursos no Azure. Tenha em atenção de que não se trata de uma lista completa e operações podem ser adicionadas ou removidas como cada fornecedor está atualizado. Cadeias de operação seguem o formato do `Microsoft.<ProviderName>/<ChildResourceType>/<action>`. Para obter uma lista abrangente e atual utilize `Get-AzureRmProviderOperation` (no PowerShell) ou `azure provider operations show` (na CLI do Azure) para operações de lista de fornecedores de recursos do Azure.

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

| Operação | Descrição |
|---|---|
|ação de configuração /|Configuração de inquilinos de atualizações.|
|/ services/ação|As atualizações de uma instância de serviço no inquilino.|
|configuração/escrita|Cria uma configuração de inquilino.|
|/Configuration/Read|Lê a configuração de inquilino.|
|/ services/escrita|Cria uma instância de serviço no inquilino.|
|/Services/Read|Lê as instâncias do serviço no inquilino.|
|/Services/DELETE|Elimina uma instância de serviço no inquilino.|
|/Services/servicemembers/Action|Cria uma instância de membro de serviço no serviço.|
|/Services/servicemembers/Read|Lê a instância de membro de serviço no serviço.|
|/Services/servicemembers/DELETE|Elimina uma instância de membro de serviço no serviço.|
|/Services/servicemembers/Alerts/Read|Lê os alertas para um membro de serviço.|
|/Services/Alerts/Read|Lê os alertas para um serviço.|
|/Services/Alerts/Read|Lê os alertas para um serviço.|

## <a name="microsoftadvisor"></a>Microsoft.Advisor

| Operação | Descrição |
|---|---|
|generateRecommendations/ação|Gera recomendações|
|suppressions/ação|Cria/atualizações suppressions|
|registar/ação|Regista a subscrição para a Microsoft Advisor|
|/generateRecommendations/Read|Obtém gerar Estado recomendações|
|/recommendations/Read|Recomendações de leituras|
|/suppressions/Read|Obtém suppressions|
|/suppressions/DELETE|Elimina a supressão|

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

| Operação | Descrição |
|---|---|
|/Servers/Read|Obtém as informações do servidor de Analysis Services especificada.|
|/ servidores/escrita|Cria ou atualiza o servidor de Analysis Services especificada.|
|/Servers/DELETE|Elimina o servidor de Analysis Services.|
|/Servers/suspend/Action|Suspende o Analysis Server.|
|/Servers/resume/Action|Retoma o Analysis Server.|
|servidores/checkNameAvailability<br>/Action|Verificações de que nome de servidor Analysis Services é válido e não em utilização.|

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

| Operação | Descrição |
|---|---|
|checkNameAvailability/ação|Verifica se o nome de serviço está disponível|
|registar/ação|Registar a subscrição para o fornecedor de recursos de Microsoft.ApiManagement|
|ação de anular o registo /|Subscrição de anular o registo do fornecedor de recursos de Microsoft.ApiManagement|
|/ service/escrita|Criar uma nova instância do serviço de gestão de API|
|/Service/Read|Ler os metadados para uma instância de serviço de gestão de API|
|/Service/DELETE|Eliminar a instância de serviço de gestão de API|
|/Service/updatehostname/Action|O programa de configuração, atualizar ou remover nomes de domínio personalizado para um serviço de gestão de API|
|/Service/uploadcertificate/Action|Carregar o certificado SSL para um serviço de gestão de API|
|/Service/backup/Action|Serviço de gestão de API a cópia de segurança para o contentor especificado um utilizador forneceu a conta de armazenamento|
|/Service/Restore/Action|Restaurar o serviço de gestão de API a partir do contentor especificado um utilizador fornecida a conta de armazenamento|
|/Service/managedeployments/Action|Alterar o SKU/unidades, adicionar/remover implementações regional do serviço de gestão de API|
|/Service/getssotoken/Action|Token SSO obtém que pode ser utilizada para iniciar sessão no portal de legado de serviço de gestão de API como administrador|
|/Service/applynetworkconfigurationupdates/Action|Atualiza os recursos de Microsoft.ApiManagement em execução na rede Virtual para escolher as definições de rede atualizado.|
|/Service/operationresults/Read|Obtém o estado atual da operação de execução longa|
|/Service/networkStatus/Read|Obtém o estado de acesso de rede de recursos.|
|/Service/loggers/Read|Obter a lista de registadores ou obter os detalhes de registo|
|/Service/loggers/Write|Adicionar novo registador ou atualizar detalhes do registo existente|
|/Service/loggers/DELETE|Remover o registo existente|
|/Service/Users/Read|Obter uma lista de utilizadores registados ou obter os detalhes da conta de utilizador|
|/Service/Users/Write|Registar um novo utilizador ou detalhes da conta de atualização de um utilizador existente|
|/Service/Users/DELETE|Remover a conta de utilizador|
|/Service/Users/generateSsoUrl/Action|Gere o URL do SSO. O URL pode ser utilizado para aceder ao portal de administração|
|/Service/Users/subscriptions/Read|Obter a lista de subscrições do utilizador|
|/Service/Users/Keys/Read|Obter a lista de chaves de utilizador|
|/Service/Users/Groups/Read|Obter a lista de grupos de utilizadores|
|/Service/tenant/operationResults/Read|Obter a lista de resultados da operação ou obter Resultado de uma operação específica|
|/Service/tenant/Policy/Read|Obter a política de configuração para o inquilino|
|/Service/tenant/Policy/Write|Definir a política de configuração para o inquilino|
|/Service/tenant/Policy/DELETE|Remover configuração da política para o inquilino|
|/Service/tenant/Configuration/Save/Action|Cria a consolidação com instantâneos de configuração para o ramo especificado no repositório|
|/Service/tenant/Configuration/Deploy/Action|Executa uma tarefa de implementação para aplicar as alterações a partir do ramo de git especificado para a configuração na base de dados.|
|/Service/tenant/Configuration/Validate/Action|Valida as alterações do ramo do git especificado|
|/Service/tenant/Configuration/operationResults/Read|Obter a lista de resultados da operação ou obter Resultado de uma operação específica|
|/Service/tenant/Configuration/syncState/Read|Obter o estado da última sincronização do git|
|/Service/tenant/Access/Read|Obter os detalhes de informações de acesso de inquilino|
|/Service/tenant/Access/Write|Atualizar detalhes de informações de acesso de inquilino|
|/Service/tenant/Access/regeneratePrimaryKey/Action|Regenerar a chave de acesso primária|
|/Service/tenant/Access/regenerateSecondaryKey/Action|Regenerar a chave de acesso secundária|
|/Service/identityProviders/Read|Obter a lista de fornecedores de identidade ou Obtenha detalhes do fornecedor de identidade|
|/Service/identityProviders/Write|Criar um novo fornecedor de identidade ou atualizar os detalhes de um fornecedor de identidade existentes|
|/Service/identityProviders/DELETE|Remover o fornecedor de identidade existentes|
|/Service/subscriptions/Read|Obter uma lista de subscrições do produto ou obter os detalhes da subscrição do produto|
|/Service/subscriptions/Write|Subscrever um utilizador existente para um produto existente ou atualizar os detalhes da subscrição existente. Esta operação pode ser utilizada para renovar a subscrição|
|/Service/subscriptions/DELETE|Elimine a subscrição. Esta operação pode ser utilizada para eliminar a subscrição|
|/Service/subscriptions/regeneratePrimaryKey/Action|Regenerar a chave primária da subscrição|
|/Service/subscriptions/regenerateSecondaryKey/Action|Regenerar a chave secundária da subscrição|
|/Service/backends/Read|Obter a lista de back-ends ou obter os detalhes de back-end|
|/Service/backends/Write|Adicionar um novo back-end ou atualizar detalhes do back-end existente|
|/Service/backends/DELETE|Remova o back-end existente|
|/Service/APIs/Read|Obter a lista de todos os registado APIs ou Get detalhes da API|
|/Service/APIs/Write|Criar nova API ou atualizar os detalhes de API existentes|
|/Service/APIs/DELETE|Remover API existente|
|/Service/APIs/Policy/Read|Obter os detalhes de configuração de políticas de API|
|/Service/APIs/Policy/Write|Definir os detalhes de configuração de política de API|
|/Service/APIs/Policy/DELETE|Remover configuração da política da API|
|/Service/APIs/Operations/Read|Obter a lista de operações de API existentes ou obter os detalhes da operação de API|
|/Service/APIs/Operations/Write|Criar nova operação da API ou atualizar a operação de API existente|
|/Service/APIs/Operations/DELETE|A operação de API existente de remoção|
|/Service/APIs/Operations/Policy/Read|Obter os detalhes de configuração de política para a operação|
|/Service/APIs/Operations/Policy/Write|Definir os detalhes da configuração de política para a operação|
|/Service/APIs/Operations/Policy/DELETE|Remover configuração da política de operação|
|/Service/Products/Read|Obter a lista de produtos ou obter os detalhes do produto|
|/Service/Products/Write|Criar novo produto ou atualizar detalhes do produto existente|
|/Service/Products/DELETE|Remova o produto existente|
|/Service/Products/subscriptions/Read|Obter a lista de subscrições do produto|
|/Service/Products/APIs/Read|Obter a lista de APIs adicionadas ao produto existente|
|/Service/Products/APIs/Write|Adicionar API existente ao produto existente|
|/Service/Products/APIs/DELETE|Remover API existente de produto existente|
|/Service/Products/Policy/Read|Obter a configuração da política de produto existente|
|/Service/Products/Policy/Write|Definir a configuração de política para o produto existente|
|/Service/Products/Policy/DELETE|Remover configuração da política de produto existente|
|/Service/Products/Groups/Read|Obter lista de grupos de programador associados ao produto|
|/Service/Products/Groups/Write|Associar grupo existente do Programador de produto existente|
|/Service/Products/Groups/DELETE|Eliminar a associação de grupo de programador existente com o produto existente|
|/Service/openidConnectProviders/Read|Obter a lista de fornecedores de OpenID Connect ou Obtenha detalhes de OpenID Connect fornecedor|
|/Service/openidConnectProviders/Write|Criar um novo OpenID Connect fornecedor ou atualizar os detalhes de um fornecedor de ligação de OpenID existente|
|/Service/openidConnectProviders/DELETE|Remover existente OpenID Connect fornecedor|
|/Service/Certificates/Read|Obter a lista de certificados ou obter os detalhes do certificado|
|/Service/Certificates/Write|Adicionar novo certificado|
|/Service/Certificates/DELETE|Remover o certificado existente|
|/Service/Properties/Read|Obtém a lista de todas as propriedades ou obtém os detalhes de propriedade especificado|
|/Service/Properties/Write|Cria uma nova propriedade ou atualiza o valor para a propriedade especificada|
|/Service/Properties/DELETE|Remove existente propriedade|
|/Service/Groups/Read|Obter a lista de grupos ou obtém detalhes de um grupo|
|/Service/Groups/Write|Criar novo grupo ou atualizar detalhes do grupo existente|
|/Service/Groups/DELETE|Remover grupo existente|
|/Service/Groups/Users/Read|Obter a lista de utilizadores do grupo|
|/Service/Groups/Users/Write|Adicionar utilizador existente ao grupo existente|
|/Service/Groups/Users/DELETE|Remover utilizador existente do grupo existente|
|/Service/authorizationServers/Read|Obter a lista de servidores de autorização ou obter os detalhes do servidor de autorização|
|/Service/authorizationServers/Write|Criar um novo servidor de autorização ou detalhes da atualização de um servidor de autorização existente|
|/Service/authorizationServers/DELETE|Remover o servidor de autorização existente|
|/Service/Reports/bySubscription/Read|Obter o relatório agregado por subscrição.|
|/Service/Reports/byRequest/Read|Obter pedidos de dados de relatórios|
|/Service/Reports/byOperation/Read|Obter relatório agregado por operações|
|/Service/Reports/byGeo/Read|Obter relatório agregado por região geográfica|
|/Service/Reports/byUser/Read|Obter o relatório agregado pelos programadores.|
|/Service/Reports/byTime/Read|Obter relatório agregado pelo períodos de tempo|
|/Service/Reports/byApi/Read|Obter relatório agregado pelo APIs|
|/Service/Reports/byProduct/Read|Obter o relatório agregado pelo produtos.|

## <a name="microsoftauthorization"></a>Microsoft.Authorization

| Operação | Descrição |
|---|---|
|elevateAccess/ação|Garante ao chamador Administrador de Acesso dos Utilizadores acesso ao âmbito do inquilino|
|/classicAdministrators/Read|Lê os administradores da subscrição.|
|/ classicAdministrators/escrita|Adiciona ou modifica o administrador de uma subscrição.|
|/classicAdministrators/DELETE|Remove o administrador da subscrição.|
|/Locks/Read|Obtém bloqueios no âmbito especificado.|
|/ bloqueios/escrita|Adiciona bloqueios ao âmbito especificado.|
|/Locks/DELETE|Elimina bloqueios no âmbito especificado.|
|/policyAssignments/Read|Obtenha informação sobre uma atribuição de política.|
|/ policyAssignments/escrita|Crie uma atribuição de política no âmbito especificado.|
|/policyAssignments/DELETE|Elimine uma atribuição de política no âmbito especificado.|
|/Permissions/Read|Lista todas as permissões que o chamador tem num determinado âmbito.|
|/roleDefinitions/Read|Obtém informação sobre uma definição de função.|
|/ roleDefinitions/escrita|Criar ou atualizar uma definição de função personalizada com permissões especificadas e âmbitos atribuíveis.|
|/roleDefinitions/DELETE|Eliminar a definição de função personalizada especificada.|
|/providerOperations/Read|Obter operações para todos os fornecedores de recursos que possam ser utilizadas em definições de funções.|
|/policyDefinitions/Read|Obtenha informação sobre uma definição de política.|
|/ policyDefinitions/escrita|Crie uma definição de política personalizada.|
|/policyDefinitions/DELETE|Elimine uma definição de política.|
|/roleAssignments/Read|Obter informações sobre uma atribuição de função.|
|/ roleAssignments/escrita|Cria uma atribuição de função no âmbito especificado.|
|/roleAssignments/DELETE|Elimina uma atribuição de função no âmbito especificado.|

## <a name="microsoftautomation"></a>Microsoft

| Operação | Descrição |
|---|---|
|/automationAccounts/Read|Obtém uma conta de automatização do Azure|
|/ automationAccounts/escrita|Cria ou atualiza uma conta de automatização do Azure|
|/automationAccounts/DELETE|Elimina uma conta de automatização do Azure|
|/automationAccounts/Configurations/readContent/Action|Obtém o conteúdo do DSC de automatização do Azure|
|/automationAccounts/hybridRunbookWorkerGroups/Read|Lê recursos de trabalho de Runbook híbrida|
|/automationAccounts/hybridRunbookWorkerGroups/DELETE|Elimina recursos de trabalho de Runbook híbrida|
|/automationAccounts/jobSchedules/Read|Obtém uma tarefa da automatização do Azure|
|/automationAccounts/jobSchedules/Write|Cria uma tarefa da automatização do Azure|
|/automationAccounts/jobSchedules/DELETE|Elimina uma tarefa da automatização do Azure|
|/automationAccounts/connectionTypes/Read|Obtém um recurso de tipo de ligação de automatização do Azure|
|/automationAccounts/connectionTypes/Write|Cria um recurso de tipo de ligação de automatização do Azure|
|/automationAccounts/connectionTypes/DELETE|Elimina um recurso de tipo de ligação de automatização do Azure|
|/automationAccounts/Modules/Read|Obtém um módulo de automatização do Azure|
|/automationAccounts/Modules/Write|Cria ou atualiza um módulo de automatização do Azure|
|/automationAccounts/Modules/DELETE|Elimina um módulo de automatização do Azure|
|/automationAccounts/credentials/Read|Obtém um recurso de credencial de automatização do Azure|
|/automationAccounts/credentials/Write|Cria ou atualiza um recurso de credencial de automatização do Azure|
|/automationAccounts/credentials/DELETE|Elimina um recurso de credencial de automatização do Azure|
|/automationAccounts/Certificates/Read|Obtém um recurso de certificado da automatização do Azure|
|/automationAccounts/Certificates/Write|Cria ou atualiza um recurso de certificado da automatização do Azure|
|/automationAccounts/Certificates/DELETE|Elimina um recurso de certificado da automatização do Azure|
|/automationAccounts/Schedules/Read|Obtém um recurso de agenda da automatização do Azure|
|/automationAccounts/Schedules/Write|Cria ou atualiza um recurso de agenda da automatização do Azure|
|/automationAccounts/Schedules/DELETE|Elimina um recurso de agenda da automatização do Azure|
|/automationAccounts/Jobs/Read|Obtém uma tarefa de automatização do Azure|
|/automationAccounts/Jobs/Write|Cria uma tarefa de automatização do Azure|
|/automationAccounts/Jobs/Stop/Action|Para uma tarefa de automatização do Azure|
|/automationAccounts/Jobs/suspend/Action|Suspende uma tarefa de automatização do Azure|
|/automationAccounts/Jobs/resume/Action|Retoma uma tarefa de automatização do Azure|
|/automationAccounts/Jobs/runbookContent/Action|Obtém o conteúdo do runbook da automatização do Azure no momento da execução da tarefa|
|/automationAccounts/Jobs/output/Action|Obtém o resultado de uma tarefa|
|/automationAccounts/Jobs/Read|Obtém uma tarefa de automatização do Azure|
|/automationAccounts/Jobs/Write|Cria uma tarefa de automatização do Azure|
|/automationAccounts/Jobs/Stop/Action|Para uma tarefa de automatização do Azure|
|/automationAccounts/Jobs/suspend/Action|Suspende uma tarefa de automatização do Azure|
|/automationAccounts/Jobs/resume/Action|Retoma uma tarefa de automatização do Azure|
|/automationAccounts/Jobs/Streams/Read|Obtém um fluxo de trabalho da automatização do Azure|
|/automationAccounts/Connections/Read|Obtém um recurso de ligação da automatização do Azure|
|/automationAccounts/Connections/Write|Cria ou atualiza um recurso de ligação da automatização do Azure|
|/automationAccounts/Connections/DELETE|Elimina um recurso de ligação da automatização do Azure|
|/automationAccounts/Variables/Read|Lê um recurso de variável da automatização do Azure|
|/automationAccounts/Variables/Write|Cria ou atualiza um recurso de variável da automatização do Azure|
|/automationAccounts/Variables/DELETE|Elimina um recurso de variável da automatização do Azure|
|/automationAccounts/runbooks/readContent/Action|Obtém o conteúdo de um runbook de automatização do Azure|
|/automationAccounts/runbooks/Read|Obtém um runbook de automatização do Azure|
|/automationAccounts/runbooks/Write|Cria ou atualiza um runbook de automatização do Azure|
|/automationAccounts/runbooks/DELETE|Elimina um runbook de automatização do Azure|
|/automationAccounts/runbooks/draft/readContent/Action|Obtém o conteúdo de um rascunho de runbook da automatização do Azure|
|/automationAccounts/runbooks/draft/writeContent/Action|Cria o conteúdo de um rascunho de runbook da automatização do Azure|
|/automationAccounts/runbooks/draft/Read|Obtém um rascunho de runbook da automatização do Azure|
|/automationAccounts/runbooks/draft/publish/Action|Publica um rascunho de runbook da automatização do Azure|
|/automationAccounts/runbooks/draft/undoEdit/Action|Anular edições de um rascunho de runbook da automatização do Azure|
|/automationAccounts/runbooks/draft/testJob/Read|Obtém uma tarefa de teste de rascunho de runbook de automatização do Azure|
|/automationAccounts/runbooks/draft/testJob/Write|Cria uma tarefa de teste de rascunho de runbook de automatização do Azure|
|/automationAccounts/runbooks/draft/testJob/Stop/Action|Parar uma tarefa de teste de rascunho de runbook de automatização do Azure|
|/automationAccounts/runbooks/draft/testJob/suspend/Action|Suspende uma tarefa de teste de rascunho de runbook de automatização do Azure|
|/automationAccounts/runbooks/draft/testJob/resume/Action|Retoma uma tarefa de teste de rascunho de runbook de automatização do Azure|
|/automationAccounts/webhooks/Read|Lê um webhook de automatização do Azure|
|/automationAccounts/webhooks/Write|Cria ou atualiza um webhook de automatização do Azure|
|/automationAccounts/webhooks/DELETE|Elimina um webhook de automatização do Azure |
|/automationAccounts/webhooks/generateUri/Action|Gera um URI para um webhook de automatização do Azure|

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

Este fornecedor não é um fornecedor ARM completo e não fornece quaisquer operações ARM.

## <a name="microsoftbatch"></a>Microsoft.Batch

| Operação | Descrição |
|---|---|
|registar/ação|Regista a subscrição para o fornecedor de recursos do Batch e permite a criação de contas do Batch|
|/ batchAccounts/escrita|Cria uma nova conta do Batch ou atualiza uma conta de Batch existente|
|/batchAccounts/Read|Apresenta uma lista de contas do Batch ou obtém as propriedades de uma conta do Batch|
|/batchAccounts/DELETE|Elimina uma conta do Batch|
|/batchAccounts/listkeys/Action|Apresenta uma lista de aceder às chaves para uma conta do Batch|
|/batchAccounts/regeneratekeys/Action|Gera de novo a aceder às chaves para uma conta do Batch|
|/batchAccounts/syncAutoStorageKeys/Action|Sincroniza as chaves de acesso da conta do storage automaticamente configurado para uma conta do Batch|
|/batchAccounts/Applications/Read|Apresenta uma lista de aplicações ou obtém as propriedades de uma aplicação|
|/batchAccounts/Applications/Write|Cria uma nova aplicação ou atualiza uma aplicação existente|
|/batchAccounts/Applications/DELETE|Elimina uma aplicação|
|/batchAccounts/Applications/versions/Read|Obtém as propriedades de um pacote de aplicação|
|/batchAccounts/Applications/versions/Write|Cria um novo pacote de aplicação ou atualiza um pacote de aplicação existente|
|/batchAccounts/Applications/versions/Activate/Action|Ativa um pacote de aplicação|
|/batchAccounts/Applications/versions/DELETE|Elimina um pacote de aplicação|
|/Locations/quotas/Read|Obtém as quotas de lote da subscrição especificada na região do Azure especificada|

## <a name="microsoftbilling"></a>Microsoft.Billing

| Operação | Descrição |
|---|---|
|/INVOICES/Read|Apresenta uma lista de faturas disponíveis|

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

| Operação | Descrição |
|---|---|
|mapApis/leitura|Operações de leitura|
|mapApis/escrita|Operação de escrita|
|mapApis/eliminar|A operação de eliminação|
|/mapApis/regenerateKey/Action|A chave de gera de novo|
|/mapApis/listSecrets/Action|Lista os segredos|
|/mapApis/listSingleSignOnToken/Action|Ler Token de autorização para o recurso de início de sessão único|
|/ Operações/leitura|Descrição da operação.|

## <a name="microsoftcache"></a>Microsoft.Cache

| Operação | Descrição |
|---|---|
|checknameavailability/ação|Verifica se um nome fica disponível para utilização com uma nova Cache de Redis|
|registar/ação|Regista o fornecedor de recursos 'Microsoft.Cache' com uma subscrição|
|ação de anular o registo /|Anula o registo do fornecedor de recursos 'Microsoft.Cache' com uma subscrição|
|/ redis/escrita|Modificar as definições da Cache de Redis e a configuração no portal de gestão|
|/redis/Read|Ver definições e a configuração da Cache de Redis no portal de gestão|
|/redis/DELETE|Eliminar a Cache de Redis completa|
|/redis/listKeys/Action|Ver o valor das chaves de acesso da Cache de Redis no portal de gestão|
|/redis/regenerateKey/Action|Altere o valor das chaves de acesso da Cache de Redis no portal de gestão|
|/redis/Import/Action|Importar dados de um formato especificado de vários blobs para Redis|
|/redis/Export/Action|Exportar dados de Redis para blobs de armazenamento com prefixo no formato especificado|
|/redis/forceReboot/Action|Force reiniciar uma instância da cache, potencialmente com perda de dados.|
|/redis/Stop/Action|Pare uma instância da cache.|
|/redis/Start/Action|Inicie uma instância da cache.|
|/redis/metricDefinitions/Read|Obtem as métricas disponíveis para uma Cache de Redis|
|/redis/firewallRules/Read|Obter as regras de firewall IP de uma Cache de Redis|
|/redis/firewallRules/Write|Editar as regras de firewall IP de uma Cache de Redis|
|/redis/firewallRules/DELETE|Eliminar as regras de firewall IP de uma Cache de Redis|
|/redis/listUpgradeNotifications/Read|Liste as notificações de atualização mais recente para o inquilino da cache.|
|/redis/linkedservers/Read|Obter os servidores ligados associado uma cache de redis.|
|/redis/linkedservers/Write|Adicionar o servidor ligado a uma Cache de Redis|
|/redis/linkedservers/DELETE|Eliminar o servidor ligado a partir de uma Cache de Redis|
|/redis/patchSchedules/Read|Obtém a agenda de aplicação de patches de uma Cache de Redis|
|/redis/patchSchedules/Write|Modificar a agenda de aplicação de patches de uma Cache de Redis|
|/redis/patchSchedules/DELETE|Eliminar o agendamento de patch de uma Cache de Redis|

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

| Operação | Descrição |
|---|---|
|provisionGlobalAppServicePrincipalInUserTenant/ação|Principal de serviço de aprovisionamento para o principal da aplicação de serviço|
|validateCertificateRegistrationInformation/ação|Validar o objeto de compra do certificado sem submetê-la|
|registar/ação|Registar o fornecedor de recursos Microsoft Certificates para a subscrição|
|certificateOrders/escrita|Adicionar um novo certificateOrder ou atualizar um já existente|
|certificateOrders/eliminar|Eliminar um AppServiceCertificate existente|
|certificateOrders/leitura|Obter a lista de CertificateOrders|
|/certificateOrders/reissue/Action|Volte a emitir um certificateorder existente|
|/certificateOrders/renew/Action|Renovar um certificateorder existente|
|/certificateOrders/retrieveCertificateActions/Action|Obter a lista de ações de certificado|
|/certificateOrders/retrieveEmailHistory/Action|Obter o histórico de correio eletrónico de certificado|
|/certificateOrders/resendEmail/Action|Certificado de reenviar correio eletrónico|
|/certificateOrders/verifyDomainOwnership/Action|Verificar a propriedade de domínio|
|/certificateOrders/resendRequestEmails/Action|Volte a enviar e-mails de pedido para outro endereço de e-mail|
|/certificateOrders/resendRequestEmails/Action|Obter selar de site para um certificado de serviço de aplicações emitido|
|/certificateOrders/Certificates/Write|Adicionar um certificado novo ou atualizar um já existente|
|/certificateOrders/Certificates/DELETE|Eliminar um certificado existente|
|/certificateOrders/Certificates/Read|Obter a lista de certificados|

## <a name="microsoftclassiccompute"></a>Microsoft. classiccompute

| Operação | Descrição |
|---|---|
|registar/ação|Registar para computação clássicos|
|checkDomainNameAvailability/ação|Verifica a disponibilidade de um determinado nome de domínio.|
|moveSubscriptionResources/ação|Mova todos os recursos clássicos para uma subscrição diferente.|
|validateSubscriptionMoveAvailability/ação|Valide a disponibilidade da subscrição para a operação de movimentação clássico.|
|/operatingSystemFamilies/Read|Lista as famílias de sistema operativo convidado disponíveis no Microsoft Azure e também lista as versões de sistema operativo disponíveis para cada f
|/Capabilities/Read|Mostra as funcionalidades|
|/operatingSystems/Read|Lista as versões do sistema operativo convidado que estão atualmente disponíveis no Microsoft Azure.|
|/resourceTypes/SKUs/Read|Obtém a lista de SKUs para tipos de recurso suportados.|
|/domainNames/Read|Devolva os nomes de domínio dos recursos.|
|/ domainNames/escrita|Adiciona ou modifica os nomes de domínio dos recursos.|
|/domainNames/DELETE|Remova os nomes de domínio dos recursos.|
|/domainNames/swap/Action|Troca a ranhura de teste para a ranhura de produção.|
|/domainNames/serviceCertificates/Read|Devolve os certificados de serviço utilizados.|
|/domainNames/serviceCertificates/Write|Adiciona ou modifica os certificados de serviço utilizados.|
|/domainNames/serviceCertificates/DELETE|Elimine os certificados de serviço utilizados.|
|/domainNames/serviceCertificates/operationStatuses/Read|Lê o estado da operação para o domínio de certificados de serviço de nomes.|
|/domainNames/Capabilities/Read|Mostra capacidades de nome de domínio|
|/domainNames/Extensions/Read|Devolve as extensões de nome de domínio.|
|/domainNames/Extensions/Write|Adicione as extensões de nome de domínio.|
|/domainNames/Extensions/DELETE|Remova as extensões de nome de domínio.|
|/domainNames/Extensions/operationStatuses/Read|Lê o estado da operação para o domínio das extensões de nomes.|
|/domainNames/Active/Write|Define o nome de domínio do Active Directory.|
|/domainNames/slots/Read|Mostra os blocos de implementação.|
|/domainNames/slots/Write|Cria ou atualiza a implementação.|
|/domainNames/slots/DELETE|Elimina uma ranhura de implementação especificado.|
|/domainNames/slots/Start/Action|Inicia uma ranhura de implementação.|
|/domainNames/slots/Stop/Action|Suspende a ranhura de implementação.|
|/domainNames/slots/operationStatuses/Read|Lê o estado da operação para o domínio de ranhuras de nomes.|
|/domainNames/slots/roles/Read|Obter a função de bloco de implementação.|
|/domainNames/slots/roles/extensionReferences/Read|Devolve a referência de extensão para a função de bloco de implementação.|
|/domainNames/slots/roles/extensionReferences/Write|Adiciona ou modifica a referência de extensão para a função de bloco de implementação.|
|/domainNames/slots/roles/extensionReferences/DELETE|Remova a referência de extensão para a função de bloco de implementação.|
|/domainNames/slots/roles/extensionReferences/operationStatuses/Read|Lê o estado da operação das referências de extensão de funções de ranhuras de nomes de domínio.|
|/domainNames/slots/roles/roleInstances/Read|Obter a instância de função.|
|/domainNames/slots/roles/roleInstances/Restart/Action|Reinicia as instâncias de função.|
|/domainNames/slots/roles/roleInstances/reimage/Action|Reimages a instância de função.|
|/domainNames/slots/roles/roleInstances/operationStatuses/Read|Lê o estado da operação das instâncias de função de funções de ranhuras de nomes de domínio.|
|/domainNames/slots/State/Start/Write|Altera o estado do bloco de implementação para parado.|
|/domainNames/slots/State/Stop/Write|Altera o estado do bloco de implementação para iniciado.|
|/domainNames/slots/upgradeDomain/Write|Atualização do percurso do domínio.|
|/domainNames/internalLoadBalancers/Read|Obtém os balanceadores de carga interno.|
|/domainNames/internalLoadBalancers/Write|Cria um novo Balanceador de carga interno.|
|/domainNames/internalLoadBalancers/DELETE|Remova um novo Balanceador de carga interno.|
|/domainNames/internalLoadBalancers/operationStatuses/Read|Lê o estado da operação de balanceadores de carga internos de nomes de domínio.|
|/domainNames/loadBalancedEndpointSets/Read|Mostra os conjuntos de ponto final com balanceamento de carga|
|/domainNames/loadBalancedEndpointSets/operationStatuses/Read|Lê o estado da operação de conjuntos de ponto final com balanceamento de carga de nomes de domínio.|
|/domainNames/availabilitySets/Read|Mostra a conjunto de disponibilidade para o recurso.|
|/quotas/Read|Obtém a quota para a subscrição.|
|/virtualMachines/Read|Obtém a lista de máquinas virtuais.|
|/ virtualMachines/escrita|Adiciona ou modifica máquinas virtuais.|
|/virtualMachines/DELETE|Remove as máquinas virtuais.|
|/virtualMachines/Start/Action|Inicie a máquina virtual.|
|/virtualMachines/redeploy/Action|Redeploys a máquina virtual.|
|/virtualMachines/Restart/Action|Reinicia a máquinas virtuais.|
|/virtualMachines/Stop/Action|Interrompe a máquina virtual.|
|/virtualMachines/Shutdown/Action|Encerre a máquina virtual.|
|/virtualMachines/attachDisk/Action|Anexa um disco de dados para uma máquina virtual.|
|/virtualMachines/detachDisk/Action|Desanexa um disco de dados da máquina virtual.|
|/virtualMachines/downloadRemoteDesktopConnectionFile/Action|Transfere o ficheiro RDP da máquina virtual.|
|/virtualMachines/nomes de networkInterfaces /<br>associatedNetworkSecurityGroups/leitura|Obtém o grupo de segurança de rede associado à interface de rede.|
|/virtualMachines/nomes de networkInterfaces /<br>associatedNetworkSecurityGroups/escrita|Adiciona um grupo de segurança de rede associado à interface de rede.|
|/virtualMachines/nomes de networkInterfaces /<br>associatedNetworkSecurityGroups/eliminar|Elimina o grupo de segurança de rede associado à interface de rede.|
|/virtualMachines/nomes de networkInterfaces /<br>operationStatuses/associatedNetworkSecurityGroups/leitura|Lê que o estado da operação para as máquinas virtuais associados a grupos de segurança de rede.|
|/virtualMachines/Providers/Microsoft.Insights/metricDefinitions/Read|Obtém as definições de métricas.|
|/virtualMachines/Providers/Microsoft.Insights/diagnosticSettings/Read|Obter as definições de diagnóstico.|
|/virtualMachines/Providers/Microsoft.Insights/diagnosticSettings/Write|Adiciona ou modifica as definições de diagnóstico.|
|/virtualMachines/Metrics/Read|Obtém as métricas.|
|/virtualMachines/operationStatuses/Read|Lê o estado da operação para as máquinas virtuais.|
|/virtualMachines/Extensions/Read|Obtém a extensão da máquina virtual.|
|/virtualMachines/Extensions/Write|Coloca a extensão da máquina virtual.|
|/virtualMachines/Extensions/operationStatuses/Read|Lê o estado da operação para as extensões de máquinas virtuais.|
|/virtualMachines/asyncOperations/Read|Obtém as operações assíncronas possíveis|
|/virtualMachines/Disks/Read|Obtém a lista de discos de dados|
|/virtualMachines/associatedNetworkSecurityGroups/Read|Obtém o grupo de segurança de rede associado à máquina virtual.|
|/virtualMachines/associatedNetworkSecurityGroups/Write|Adiciona um grupo de segurança de rede associado à máquina virtual.|
|/virtualMachines/associatedNetworkSecurityGroups/DELETE|Elimina o grupo de segurança de rede associado à máquina virtual.|
|/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/Read|Lê que o estado da operação para as máquinas virtuais associados a grupos de segurança de rede.|

## <a name="microsoftclassicnetwork"></a>Classicnetwork

| Operação | Descrição |
|---|---|
|registar/ação|Registar-se à rede clássico|
|/gatewaySupportedDevices/Read|Obtém a lista de dispositivos suportados.|
|/reservedIps/Read|Obtém os Ips reservados|
|/ reservedIps/escrita|Adicionar um novo Ip reservado|
|/reservedIps/DELETE|Elimine um Ip reservado.|
|/reservedIps/Link/Action|Associar um Ip reservado|
|/reservedIps/JOIN/Action|Associar um Ip reservado|
|/reservedIps/operationStatuses/Read|Lê o estado da operação dos ips reservados.|
|/virtualNetworks/Read|Obter a rede virtual.|
|/ virtualNetworks/escrita|Adicione uma nova rede virtual.|
|/virtualNetworks/DELETE|Elimina a rede virtual.|
|/virtualNetworks/peer/Action|Peers uma rede virtual com a outra rede virtual.|
|/virtualNetworks/JOIN/Action|Junta a rede virtual.|
|/virtualNetworks/checkIPAddressAvailability/Action|Verifica a disponibilidade de um determinado endereço IP numa rede virtual.|
|/virtualNetworks/Capabilities/Read|Mostra as funcionalidades|
|/virtualNetworks/sub-redes /<br>associatedNetworkSecurityGroups/leitura|Obtém o grupo de segurança de rede associado à sub-rede.|
|/virtualNetworks/sub-redes /<br>associatedNetworkSecurityGroups/escrita|Adiciona um grupo de segurança de rede associado à sub-rede.|
|/virtualNetworks/sub-redes /<br>associatedNetworkSecurityGroups/eliminar|Elimina o grupo de segurança de rede associado à sub-rede.|
|/virtualNetworks/sub-redes /<br>operationStatuses/associatedNetworkSecurityGroups/leitura|Lê o estado da operação para o grupo de segurança de rede de associado de sub-rede de rede virtual.|
|/virtualNetworks/operationStatuses/Read|Lê o estado da operação das redes virtuais.|
|/virtualNetworks/gateways/Read|Obtém os gateways de rede virtual.|
|/virtualNetworks/gateways/Write|Adiciona um gateway de rede virtual.|
|/virtualNetworks/gateways/DELETE|Elimina o gateway de rede virtual.|
|/virtualNetworks/gateways/startDiagnostics/Action|Inicia o diagnóstico do gateway da rede virtual.|
|/virtualNetworks/gateways/stopDiagnostics/Action|Para o diagnóstico do gateway de rede virtual.|
|/virtualNetworks/gateways/downloadDiagnostics/Action|Transfere o diagnóstico do gateway.|
|/virtualNetworks/gateways/listCircuitServiceKey/Action|Obtém a chave do serviço de circuito.|
|/virtualNetworks/gateways/downloadDeviceConfigurationScript/Action|Transfere o script de configuração do dispositivo.|
|/virtualNetworks/gateways/listPackage/Action|Lista o pacote de gateway de rede virtual.|
|/virtualNetworks/gateways/operationStatuses/Read|Lê o estado da operação para os gateways de redes virtuais.|
|/virtualNetworks/gateways/Packages/Read|Obtém o pacote de gateway de rede virtual.|
|/virtualNetworks/gateways/Connections/Read|Obtém a lista de ligações.|
|/virtualNetworks/gateways/Connections/Connect/Action|Liga-se uma ligação de gateway site para site.|
|/virtualNetworks/gateways/Connections/Disconnect/Action|Desliga uma ligação de gateway site para site.|
|/virtualNetworks/gateways/Connections/Test/Action|Testa uma ligação de gateway site para site.|
|/virtualNetworks/gateways/clientRevokedCertificates/Read|Ler os certificados de cliente revogados.|
|/virtualNetworks/gateways/clientRevokedCertificates/Write|Revoga um certificado de cliente.|
|/virtualNetworks/gateways/clientRevokedCertificates/DELETE|Unrevokes um certificado de cliente.|
|/virtualNetworks/gateways/clientRootCertificates/Read|Localize certificados de raiz de cliente.|
|/virtualNetworks/gateways/clientRootCertificates/Write|Carrega um novo certificado de raiz de cliente.|
|/virtualNetworks/gateways/clientRootCertificates/DELETE|Elimina o certificado de cliente do gateway de rede virtual.|
|/virtualNetworks/gateways/clientRootCertificates/download/Action|Transfere o certificado pelo thumbprint.|
|/virtualNetworks/gateways/clientRootCertificates/listPackage/Action|Lista o pacote de certificado do gateway de rede virtual.|
|/networkSecurityGroups/Read|Obtém o grupo de segurança de rede.|
|/ networkSecurityGroups/escrita|Adiciona um novo grupo de segurança de rede.|
|/networkSecurityGroups/DELETE|Elimina o grupo de segurança de rede.|
|/networkSecurityGroups/operationStatuses/Read|Lê o estado da operação para o grupo de segurança de rede.|
|/networkSecurityGroups/securityRules/Read|Obtém a regra de segurança.|
|/networkSecurityGroups/securityRules/Write|Adiciona ou atualiza uma regra de segurança.|
|/networkSecurityGroups/securityRules/DELETE|Elimina a regra de segurança.|
|/networkSecurityGroups/securityRules/operationStatuses/Read|Lê o estado da operação para as regras de segurança de grupo de segurança de rede.|
|/quotas/Read|Obtém a quota para a subscrição.|

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

| Operação | Descrição |
|---|---|
|registar/ação|Registar para o armazenamento clássico|
|checkStorageAccountAvailability/ação|Verifica a disponibilidade de uma conta de armazenamento.|
|/Capabilities/Read|Mostra as funcionalidades|
|/publicImages/Read|Obtém a imagem de máquina de virtual público.|
|/Images/Read|Devolve a imagem.|
|/storageAccounts/Read|Devolva a conta de armazenamento com a conta fornecida.|
|/ storageAccounts/escrita|Adiciona uma nova conta de armazenamento.|
|/storageAccounts/DELETE|Elimina a conta de armazenamento.|
|/storageAccounts/listKeys/Action|Lista as chaves de acesso para as contas de armazenamento.|
|/storageAccounts/regenerateKey/Action|As chaves de acesso existentes para a conta de armazenamento de gera de novo.|
|/storageAccounts/operationStatuses/Read|Lê o estado da operação para o recurso.|
|/storageAccounts/Images/Read|Devolve a imagem de conta de armazenamento.|
|/storageAccounts/Images/DELETE|Elimina uma imagem de conta de armazenamento.|
|/storageAccounts/Disks/Read|Devolve o disco de conta de armazenamento.|
|/storageAccounts/Disks/Write|Adiciona um disco de conta de armazenamento.|
|/storageAccounts/Disks/DELETE|Elimina um disco de conta de armazenamento.|
|/storageAccounts/Disks/operationStatuses/Read|Lê o estado da operação para o recurso.|
|/storageAccounts/osImages/Read|Devolve a imagem de sistema operativo da conta de armazenamento.|
|/storageAccounts/osImages/DELETE|Elimina uma imagem de sistema operativo da conta de armazenamento.|
|/storageAccounts/Services/Read|Obter os serviços disponíveis.|
|/storageAccounts/Services/metricDefinitions/Read|Obtém as definições de métricas.|
|/storageAccounts/Services/Metrics/Read|Obtém as métricas.|
|/storageAccounts/Services/diagnosticSettings/Read|Obter as definições de diagnóstico.|
|/storageAccounts/Services/diagnosticSettings/Write|Adiciona ou modifica as definições de diagnóstico.|
|/Disks/Read|Devolve o disco de conta de armazenamento.|
|/osImages/Read|Devolve a imagem do sistema operativo.|
|/quotas/Read|Obtém a quota para a subscrição.|

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

| Operação | Descrição |
|---|---|
|/Accounts/Read|Lê as contas de API.|
|contas/escrita|Grava as contas de API.|
|/Accounts/DELETE|Elimina as contas de API|
|/Accounts/listKeys/Action|Lista de chaves|
|/Accounts/regenerateKey/Action|Regeneração da chave|
|/Accounts/SKUs/Read|Lê SKUs disponíveis para um recurso existente.|
|/Accounts/usages/Read|Obter a utilização da quota para um recurso existente.|
|/ Operações/leitura|Descrição da operação.|

## <a name="microsoftcommerce"></a>Microsoft.Commerce

| Operação | Descrição |
|---|---|
|/ RateCard/leitura|Devolve oferece dados, os metadados do recurso/medir e as taxas para a subscrição fornecida.|
|/ UsageAggregates/leitura|Obtém o-consumo Microsoft Azure por uma subscrição. O resultado contém agrega dados de utilização, subscrição e dos recursos relacionados com informações, um intervalo de tempo específico.|

## <a name="microsoftcompute"></a>Microsoft.Compute

| Operação | Descrição |
|---|---|
|registar/ação|Regista a subscrição no fornecedor de recursos Microsoft. Compute|
|/restorePointCollections/Read|Obter as propriedades de uma coleção de ponto de restauro|
|/ restorePointCollections/escrita|Cria uma nova coleção de ponto de restauro ou atualiza um já existente|
|/restorePointCollections/DELETE|Elimina o restauro do ponto de coleção e contido restaurar pontos|
|/restorePointCollections/restorePoints/Read|Obter as propriedades de um ponto de restauro|
|/restorePointCollections/restorePoints/Write|Cria um novo ponto de restauro|
|/restorePointCollections/restorePoints/DELETE|Elimina o ponto de restauro|
|/restorePointCollections/restorePoints/retrieveSasUris/Action|Obter as propriedades de um ponto de restauro, juntamente com o URI de SAS do blob|
|/virtualMachineScaleSets/Read|Obter as propriedades de um conjunto de dimensionamento de máquina virtual|
|/ virtualMachineScaleSets/escrita|Cria um novo conjunto de dimensionamento de máquina virtual ou atualiza um já existente|
|/virtualMachineScaleSets/DELETE|Elimina o conjunto de dimensionamento de máquina virtual|
|/virtualMachineScaleSets/Start/Action|Inicia as instâncias do conjunto de dimensionamento de máquina virtual|
|/virtualMachineScaleSets/powerOff/Action|Desliga as instâncias do conjunto de dimensionamento de máquina virtual|
|/virtualMachineScaleSets/Restart/Action|Reinicia as instâncias do conjunto de dimensionamento de máquina virtual|
|/virtualMachineScaleSets/deallocate/Action|Desliga e liberta os recursos de computação de instâncias do conjunto de dimensionamento de máquina virtual |
|/virtualMachineScaleSets/manualUpgrade/Action|Atualiza manualmente instâncias para o modelo mais recente do conjunto de dimensionamento de máquina virtual|
|/virtualMachineScaleSets/Scale/Action|Dimensionar de / definir aumentar horizontalmente a contagem de instâncias de um dimensionamento da máquina virtual existente|
|/virtualMachineScaleSets/instanceView/Read|Obtém a vista de instância do conjunto de dimensionamento de máquina virtual|
|/virtualMachineScaleSets/SKUs/Read|Lista os SKUs válidos de um conjunto de dimensionamento de máquina virtual existente|
|/virtualMachineScaleSets/virtualMachines/Read|Obtém as propriedades de uma Máquina Virtual num conjunto de dimensionamento VM|
|/virtualMachineScaleSets/virtualMachines/DELETE|Elimine uma Máquina Virtual específica num conjunto de dimensionamento VM.|
|/virtualMachineScaleSets/virtualMachines/Start/Action|Inicia uma instância de Máquina Virtual num conjunto de dimensionamento VM.|
|/virtualMachineScaleSets/virtualMachines/powerOff/Action|Instância de Powers desligar uma máquina no conjunto de dimensionamento da VM.|
|/virtualMachineScaleSets/virtualMachines/Restart/Action|Reinicia uma instância de Máquina Virtual num conjunto de dimensionamento VM.|
|/virtualMachineScaleSets/virtualMachines/deallocate/Action|Desliga e liberta os recursos de computação para uma Máquina Virtual num conjunto de dimensionamento VM.|
|/virtualMachineScaleSets/virtualMachines/instanceView/Read|Obtém a vista de instância de uma Máquina Virtual num conjunto de dimensionamento VM.|
|/Images/Read|Obter as propriedades da imagem|
|imagens/escrita|Cria uma nova imagem ou atualiza um já existente|
|/Images/DELETE|Elimina a imagem|
|/Operations/Read|Lista as operações disponíveis no fornecedor de recursos Microsoft. Compute|
|/Disks/Read|Obter as propriedades de um disco|
|discos/escrita|Cria um novo disco ou atualiza um já existente|
|/Disks/DELETE|Elimina o disco|
|/Disks/beginGetAccess/Action|Obter o URI de SAS do disco para acesso do blob|
|/Disks/endGetAccess/Action|Revogar o URI de SAS do disco|
|/snapshots/Read|Obter as propriedades de um instantâneo|
|instantâneos/escrita|Criar um novo instantâneo ou atualizar um já existente|
|/snapshots/DELETE|Eliminar um instantâneo|
|/availabilitySets/Read|Obter as propriedades de um conjunto de disponibilidade|
|/ availabilitySets/escrita|Cria um novo conjunto de disponibilidade ou atualiza um já existente|
|/availabilitySets/DELETE|Elimina o conjunto de disponibilidade|
|/availabilitySets/vmSizes/Read|Listar tamanhos disponíveis para criar ou atualizar uma máquina virtual no conjunto de disponibilidade|
|/virtualMachines/Read|Obter as propriedades de uma máquina virtual|
|/ virtualMachines/escrita|Cria uma nova máquina virtual ou atualiza uma máquina virtual existente|
|/virtualMachines/DELETE|Elimina a máquina virtual|
|/virtualMachines/Start/Action|Inicia a máquina virtual|
|/virtualMachines/powerOff/Action|Desliga a máquina virtual. Tenha em atenção que a máquina virtual irá continuar a ser faturada.|
|/virtualMachines/redeploy/Action|Redeploys máquina virtual|
|/virtualMachines/Restart/Action|Reinicia a máquina virtual|
|/virtualMachines/deallocate/Action|Desliga a máquina virtual e liberta os recursos de computação|
|/virtualMachines/generalize/Action|Define o estado da máquina virtual como generalizado e prepara a máquina virtual para captura|
|/virtualMachines/Capture/Action|Captura a máquina virtual, copiando os discos rígidos virtuais e gera um modelo que pode ser utilizado para criar máquinas virtuais semelhantes|
|/virtualMachines/convertToManagedDisks/Action|Converte os discos de baseadas em BLOBs da máquina virtual em discos geridos|
|/virtualMachines/vmSizes/Read|Apresenta uma lista de tamanhos disponíveis, que a máquina virtual pode ser atualizada para|
|/virtualMachines/instanceView/Read|Obtém o estado de runtime detalhado da máquina virtual e os respetivos recursos|
|/virtualMachines/Extensions/Read|Obter as propriedades de uma extensão da máquina virtual|
|/virtualMachines/Extensions/Write|Cria uma nova extensão de máquina virtual ou atualiza um já existente|
|/virtualMachines/Extensions/DELETE|Elimina a extensão da máquina virtual|
|/Locations/vmSizes/Read|Apresenta uma lista de tamanhos de máquina virtual disponíveis numa localização|
|/Locations/usages/Read|Obtém os limites de serviço e as quantidades de utilização atual de recursos de computação da subscrição numa localização|
|/Locations/Operations/Read|Obtém o estado de uma operação assíncrona|

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

| Operação | Descrição |
|---|---|
|registar/ação|Regista a subscrição para o fornecedor de recursos de registo do contentor e permite a criação de registos do contentor.|
|/checknameavailability/Read|Verifica que o nome do registo é válido e não está em utilização.|
|/registries/Read|Devolve a lista de registos do contentor ou obtém as propriedades para o registo de contentor especificado.|
|os registos/escrita|Cria um registo de contentor com os parâmetros especificados ou atualiza as propriedades ou etiquetas para o registo de contentor especificado.|
|/registries/DELETE|Elimina um registo de contentor existente.|
|/registries/listCredentials/Action|Lista as credenciais de início de sessão para o registo de contentor especificado.|
|/registries/regenerateCredential/Action|As credenciais de início de sessão para o registo de contentor especificado de gera de novo.|

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

| Operação | Descrição |
|---|---|
|/containerServices/subscriptions/Read|Obter os serviços de contentor especificados com base na subscrição|
|/containerServices/resourceGroups/Read|Obter os serviços de contentor especificados com base no grupo de recursos|
|/containerServices/resourceGroups/ContainerServiceName/Read|Obtém o serviço de contentor especificado|
|/containerServices/resourceGroups/ContainerServiceName/Write|Coloca ou atualiza o serviço de contentor especificado|
|/containerServices/resourceGroups/ContainerServiceName/DELETE|Elimina o serviço de contentor especificado|

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

| Operação | Descrição |
|---|---|
|updateCommunicationPreference/ação|Preferência de comunicação de atualização|
|listCommunicationPreference/ação|Preferência de comunicação de lista|
|/Applications/Read|Operações de leitura|
|aplicações/escrita|Operação de escrita|
|aplicações/escrita|Operação de escrita|
|/Applications/DELETE|A operação de eliminação|
|/Applications/listSecrets/Action|Lista os segredos|
|/Applications/listSingleSignOnToken/Action|Início de sessão único Tokens de leitura|
|/Operations/Read|operações de leitura|

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

| Operação | Descrição |
|---|---|
|/hubs/Read|Ler qualquer Hub de informações de cliente do Azure|
|hubs/escrita|Criar ou atualizar qualquer Hub de informações de cliente do Azure|
|/hubs/DELETE|Eliminar o Hub de Insights qualquer cliente do Azure|
|/hubs/Providers/Microsoft.Insights/metricDefinitions/Read|Obtem as métricas disponíveis para o recurso|
|/hubs/Providers/Microsoft.Insights/diagnosticSettings/Read|Obtém a definição de diagnóstico do recurso|
|/hubs/Providers/Microsoft.Insights/diagnosticSettings/Write|Cria ou atualiza a definição do diagnóstico para o recurso|
|/hubs/Providers/Microsoft.Insights/logDefinitions/Read|Obtém os registos disponíveis para o recurso|
|/hubs/authorizationPolicies/Read|Leia a que política de assinatura de acesso de partilhadas quaisquer informações de cliente do Azure|
|/hubs/authorizationPolicies/Write|Criar ou atualizar qualquer política de assinatura de acesso partilhado de informações de cliente do Azure|
|/hubs/authorizationPolicies/DELETE|Eliminar qualquer política de assinatura de acesso partilhado de informações de cliente do Azure|
|/hubs/authorizationPolicies/regeneratePrimaryKey/Action|Regenerar a chave primária da política de assinaturas de acesso de partilhado das informações do cliente do Azure|
|/hubs/authorizationPolicies/regenerateSecondaryKey/Action|Regenerar a chave secundária da política de assinaturas de acesso de partilhado das informações do cliente do Azure|
|/hubs/Profiles/Read|Ler nenhum perfil de informações de cliente do Azure|
|/hubs/Profiles/Write|Escrever qualquer perfil de informações de cliente do Azure|
|/hubs/KPI/Read|Ler qualquer indicador de chave de desempenho de informações de cliente do Azure|
|/hubs/KPI/Write|Criar ou atualizar qualquer indicador de chave de desempenho de informações de cliente do Azure|
|/hubs/KPI/DELETE|Eliminar qualquer indicador de chave de desempenho de informações de cliente do Azure|
|/hubs/views/Read|Ler a qualquer vista de aplicação de informações de cliente do Azure|
|/hubs/views/Write|Criar ou atualizar qualquer vista de aplicação de informações de cliente do Azure|
|/hubs/views/DELETE|Eliminar qualquer vista de aplicação de informações de cliente do Azure|
|/hubs/interactions/Read|Ler qualquer interação de informações de cliente do Azure|
|/hubs/interactions/Write|Criar ou atualizar qualquer interação de informações de cliente do Azure|
|/hubs/roleAssignments/Read|Ler qualquer atribuição do cliente do Azure Insights Rbac|
|/hubs/roleAssignments/Write|Criar ou atualizar qualquer atribuição do cliente do Azure Insights Rbac|
|/hubs/roleAssignments/DELETE|Eliminar qualquer atribuição do cliente do Azure Insights Rbac|
|/hubs/Connectors/Read|Leia os conectores de informações de cliente do Azure|
|/hubs/Connectors/Write|Criar ou atualizar qualquer conector das informações de cliente do Azure|
|/hubs/Connectors/DELETE|Eliminar qualquer conector das informações de cliente do Azure|
|/hubs/Connectors/mappings/Read|Ler qualquer mapeamento de conector de informações de cliente do Azure|
|/hubs/Connectors/mappings/Write|Criar ou atualizar qualquer mapeamento de conector de informações de cliente do Azure|
|/hubs/Connectors/mappings/DELETE|Eliminar qualquer mapeamento de conector de informações de cliente do Azure|

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

| Operação | Descrição |
|---|---|
|checkNameAvailability/ação|Verifica a disponibilidade do nome de catálogo para o inquilino.|
|/catalogs/Read|Obter propriedades para o catálogo ou catálogos sob a subscrição ou grupo de recursos.|
|/ catálogos/escrita|Cria o catálogo ou as etiquetas e propriedades para o catálogo de atualizações.|
|/catalogs/DELETE|Elimina o catálogo.|

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

| Operação | Descrição |
|---|---|
|/datafactories/Read|Lê a fábrica de dados.|
|/ datafactories/escrita|Criar ou atualizar a fábrica de dados|
|/datafactories/DELETE|Elimina a fábrica de dados.|
|/datafactories/datapipelines/Read|Lê o Pipeline.|
|/datafactories/datapipelines/DELETE|Elimina o Pipeline.|
|/datafactories/datapipelines/pause/Action|Pipeline de pausa.|
|/datafactories/datapipelines/resume/Action|Pipeline de retoma.|
|/datafactories/datapipelines/Update/Action|Pipeline de atualizações.|
|/datafactories/datapipelines/Write|Criar ou atualizar o Pipeline|
|/datafactories/linkedServices/Read|Lê o serviço ligado.|
|/datafactories/linkedServices/DELETE|Elimina o serviço ligado.|
|/datafactories/linkedServices/Write|Criar ou o serviço ligado de atualização|
|/datafactories/Tables/Read|Lê a tabela.|
|/datafactories/Tables/DELETE|Elimina a tabela.|
|/datafactories/Tables/Write|Criar ou atualizar tabela|

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

| Operação | Descrição |
|---|---|
|/Accounts/Read|Obter informações sobre a conta de DataLakeAnalytics.|
|contas/escrita|Criar ou atualizar a conta de DataLakeAnalytics.|
|/Accounts/DELETE|Elimina a conta de DataLakeAnalytics.|
|/Accounts/firewallRules/Read|Obter informações sobre uma regra de firewall.|
|/Accounts/firewallRules/Write|Criar ou atualizar uma regra de firewall.|
|/Accounts/firewallRules/DELETE|Elimine uma regra de firewall.|
|/Accounts/storageAccounts/Read|Obter a conta de armazenamento ligada para a conta de DataLakeAnalytics.|
|/Accounts/storageAccounts/Write|Ligar uma conta de armazenamento para a conta de DataLakeAnalytics.|
|/Accounts/storageAccounts/DELETE|Desassociar uma conta de armazenamento da conta do DataLakeAnalytics.|
|/Accounts/storageAccounts/containers/Read|Obter contentores sob a conta de armazenamento|
|/Accounts/storageAccounts/containers/listSasTokens/Action|Lista os Tokens SAS para o contentor de armazenamento|
|/Accounts/dataLakeStoreAccounts/Read|Obter conta DataLakeStore ligada para a conta de DataLakeAnalytics.|
|/Accounts/dataLakeStoreAccounts/Write|Associe uma conta de DataLakeStore para a conta de DataLakeAnalytics.|
|/Accounts/dataLakeStoreAccounts/DELETE|Desassociar uma conta de DataLakeStore da conta do DataLakeAnalytics.|

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

| Operação | Descrição |
|---|---|
|/Accounts/Read|Obter informações sobre uma conta de DataLakeStore existed.|
|contas/escrita|Criar uma nova conta de DataLakeStore ou atualizar uma conta de DataLakeStore existed.|
|/Accounts/DELETE|Elimine uma conta de DataLakeStore existed.|
|/Accounts/firewallRules/Read|Obter informações sobre uma regra de firewall.|
|/Accounts/firewallRules/Write|Criar ou atualizar uma regra de firewall.|
|/Accounts/firewallRules/DELETE|Elimine uma regra de firewall.|
|/Accounts/trustedIdProviders/Read|Obter informações sobre um fornecedor de identidade fidedigna.|
|/Accounts/trustedIdProviders/Write|Criar ou atualizar um fornecedor de identidade fidedigna.|
|/Accounts/trustedIdProviders/DELETE|Elimine um fornecedor de identidade fidedigna.|

## <a name="microsoftdevices"></a>Microsoft.Devices

| Operação | Descrição |
|---|---|
|registar/ação|Registar a subscrição para o fornecedor de recursos do IotHub e permite a criação dos recursos de IotHub|
|checkNameAvailability/ação|Verifique o nome do IotHub se está disponível|
|utilizações/leitura|Obter detalhes de utilização de subscrição para este fornecedor.|
|operações/leitura|Obter todas as operações de ResourceProvider|
|iotHubs/leitura|Obtém os recursos de IotHub|
|iotHubs/escrita|Criar ou atualizar o recurso de IotHub|
|iotHubs/eliminar|Eliminar recurso IotHub|
|/iotHubs/listkeys/Action|Obter todas as chaves de IotHub|
|/iotHubs/exportDevices/Action|Dispositivos de exportação|
|/iotHubs/importDevices/Action|Importar dispositivos|
|/ IotHubs/metricDefinitions/leitura|Obtem as métricas disponíveis para o serviço de IotHub|
|/iotHubs/iotHubKeys/listkeys/Action|Obter a chave de IotHub para o nome fornecido|
|/iotHubs/iotHubStats/Read|Obter estatísticas IotHub|
|/iotHubs/quotaMetrics/Read|Obter métricas de Quota|
|/iotHubs/eventHubEndpoints/consumerGroups/Write|Criar grupo de consumidores de EventHub|
|/iotHubs/eventHubEndpoints/consumerGroups/Read|Obter os grupos de consumidores de EventHub|
|/iotHubs/eventHubEndpoints/consumerGroups/DELETE|Eliminar grupo de consumidores de EventHub|
|/iotHubs/Routing/routes/$ testall/ação|Testar uma mensagem de todas as rotas existentes|
|/iotHubs/Routing/routes/$ testnew/ação|Uma mensagem de um teste fornecido rota de teste|
|/ IotHubs/diagnosticSettings/leitura|Obtém a definição de diagnóstico do recurso|
|/ IotHubs/diagnosticSettings/escrita|Cria ou atualiza a definição do diagnóstico para o recurso|
|/iotHubs/SKUs/Read|Obter Skus válidos de IotHub|
|/iotHubs/Jobs/Read|Obter tarefas detalhes submetidos numa fornecido IotHub|
|/iotHubs/routingEndpointsHealth/Read|Obtém o estado de funcionamento de todos os pontos finais de encaminhamento para um IotHub|

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

| Operação | Descrição |
|---|---|
|/ Subscrição/register/ação|Regista a subscrição do|
|/Labs/DELETE|Elimine laboratórios.|
|/Labs/Read|Laboratórios de leitura.|
|laboratórios/escrita|Adiciona ou modifica laboratórios.|
|/Labs/ListVhds/Action|Imagens de disco de lista disponível para criação de imagens personalizadas.|
|/Labs/GenerateUploadUri/Action|Gere um URI de carregamento de imagens do disco personalizado para um laboratório.|
|/Labs/CreateEnvironment/Action|Crie máquinas virtuais no laboratório.|
|/Labs/ClaimAnyVm/Action|Uma máquina virtual claimable aleatória no laboratório de afirmações.|
|/Labs/ExportResourceUsage/Action|Exporta a utilização de recursos de laboratório para uma conta de armazenamento|
|/Labs/Users/DELETE|Elimine perfis de utilizador.|
|/Labs/Users/Read|Perfis de utilizador de leitura.|
|/Labs/Users/Write|Adiciona ou modifica os perfis de utilizador.|
|/Labs/Users/Secrets/DELETE|Elimine segredos.|
|/Labs/Users/Secrets/Read|Ler os segredos.|
|/Labs/Users/Secrets/Write|Adiciona ou modifica os segredos.|
|/Labs/Users/Environments/DELETE|Elimine ambientes.|
|/Labs/Users/Environments/Read|Ambientes de leitura.|
|/Labs/Users/Environments/Write|Adiciona ou modifica os ambientes.|
|/Labs/Users/Disks/DELETE|Elimine os discos.|
|/Labs/Users/Disks/Read|Leitura de discos.|
|/Labs/Users/Disks/Write|Adiciona ou modifica os discos.|
|/Labs/Users/Disks/Attach/Action|Ligar e criar a concessão do disco para a máquina virtual.|
|/Labs/Users/Disks/detach/Action|Desligue e quebra a concessão dos discos ligados à máquina virtual.|
|/Labs/customImages/DELETE|Elimine imagens personalizadas.|
|/Labs/customImages/Read|Ler imagens personalizadas.|
|/Labs/customImages/Write|Adiciona ou modifica imagens personalizadas.|
|/Labs/serviceRunners/DELETE|Elimine runners de serviço.|
|/Labs/serviceRunners/Read|Ler runners de serviço.|
|/Labs/serviceRunners/Write|Adiciona ou modifica runners de serviço.|
|/Labs/artifactSources/DELETE|Elimine as origens de artefactos.|
|/Labs/artifactSources/Read|Ler as origens de artefactos.|
|/Labs/artifactSources/Write|Adiciona ou modifica as origens de artefactos.|
|/Labs/artifactSources/artifacts/Read|Ler os artefactos.|
|/Labs/artifactSources/artifacts/GenerateArmTemplate/Action|Gera um modelo ARM para o artefacto indicado, carrega os ficheiros necessários para uma conta de armazenamento e valida o artefacto gerado.|
|/Labs/artifactSources/armTemplates/Read|Ler modelos do Gestor de recursos do azure.|
|/Labs/Costs/Read|Ler os custos.|
|/Labs/Costs/Write|Adiciona ou modifica os custos.|
|/Labs/virtualNetworks/DELETE|Elimine redes virtuais.|
|/Labs/virtualNetworks/Read|Ler as redes virtuais.|
|/Labs/virtualNetworks/Write|Adiciona ou modifica as redes virtuais.|
|/Labs/formulas/DELETE|Elimine as fórmulas.|
|/Labs/formulas/Read|Ler as fórmulas.|
|/Labs/formulas/Write|Adiciona ou modifica as fórmulas.|
|/Labs/Schedules/DELETE|Elimine agendas.|
|/Labs/Schedules/Read|As agendas de leitura.|
|/Labs/Schedules/Write|Adiciona ou modifica as agendas.|
|/Labs/Schedules/execute/Action|Execute uma agenda.|
|/Labs/Schedules/ListApplicable/Action|Apresenta uma lista de todos os agendamentos aplicáveis|
|/Labs/galleryImages/Read|Ler imagens gallery.|
|/Labs/policySets/EvaluatePolicies/Action|Avalia a política de laboratório.|
|/Labs/policySets/Policies/DELETE|Elimine políticas.|
|/Labs/policySets/Policies/Read|Políticas de leitura.|
|/Labs/policySets/Policies/Write|Adicionar ou modificar as políticas.|
|/Labs/virtualMachines/DELETE|Elimine as máquinas virtuais.|
|/Labs/virtualMachines/Read|Máquinas virtuais de leitura.|
|/Labs/virtualMachines/Write|Adiciona ou modifica máquinas virtuais.|
|/Labs/virtualMachines/Start/Action|Inicie uma máquina virtual.|
|/Labs/virtualMachines/Stop/Action|Parar uma máquina virtual|
|/Labs/virtualMachines/ApplyArtifacts/Action|Aplicam-se de artefactos a máquina virtual.|
|/Labs/virtualMachines/AddDataDisk/Action|Anexe um disco de dados nova ou existente à máquina virtual.|
|/Labs/virtualMachines/DetachDataDisk/Action|Desligar o disco da máquina virtual.|
|/Labs/virtualMachines/Claim/Action|Assumir a propriedade de uma máquina virtual existente|
|/Labs/virtualMachines/ListApplicableSchedules/Action|Apresenta uma lista de todos os agendamentos aplicáveis|
|/Labs/virtualMachines/Schedules/DELETE|Elimine agendas.|
|/Labs/virtualMachines/Schedules/Read|As agendas de leitura.|
|/Labs/virtualMachines/Schedules/Write|Adiciona ou modifica as agendas.|
|/Labs/virtualMachines/Schedules/execute/Action|Execute uma agenda.|
|/Labs/notificationChannels/DELETE|Elimine notificationchannels.|
|/Labs/notificationChannels/Read|Ler notificationchannels.|
|/Labs/notificationChannels/Write|Adiciona ou modifica notificationchannels.|
|/Labs/notificationChannels/Notify/Action|Envie notificações para canal fornecido.|
|/Schedules/DELETE|Elimine agendas.|
|/Schedules/Read|As agendas de leitura.|
|as agendas/escrita|Adiciona ou modifica as agendas.|
|/Schedules/execute/Action|Execute uma agenda.|
|/Schedules/Retarget/Action|ID de recurso de destino de uma agenda de atualizações|
|/Locations/Operations/Read|Operações de leitura.|

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

| Operação | Descrição |
|---|---|
|/databaseAccountNames/Read|Verifica a disponibilidade do nome.|
|/databaseAccounts/Read|Lê uma conta de base de dados.|
|/ databaseAccounts/escrita|Atualize contas de base de dados.|
|/databaseAccounts/listKeys/Action|Lista de chaves de uma conta de base de dados|
|/databaseAccounts/regenerateKey/Action|Rodar chaves de uma conta de base de dados|
|/databaseAccounts/listConnectionStrings/Action|Obter as cadeias de ligação para uma conta de base de dados|
|/databaseAccounts/changeResourceGroup/Action|Alterar o grupo de recursos de uma conta de base de dados|
|/databaseAccounts/failoverPriorityChange/Action|Alterar as prioridades de ativação pós-falha de regiões de uma conta de base de dados. Isto é utilizado para efetuar a operação de ativação pós-falha manual|
|/databaseAccounts/DELETE|Elimina as contas de base de dados.|
|/databaseAccounts/metricDefinitions/Read|Lê a base de dados definições de métrica de conta.|
|/databaseAccounts/Metrics/Read|Lê as métricas de conta de base de dados.|
|/databaseAccounts/usages/Read|Lê a base de dados utilizações de conta.|
|/databaseAccounts/Databases/Collections/metricDefinitions/Read|Lê a coleção de definições de métrica.|
|/databaseAccounts/Databases/Collections/Metrics/Read|Lê as métricas de coleção.|
|/databaseAccounts/Databases/Collections/usages/Read|Lê as utilizações de coleção.|
|/databaseAccounts/Databases/metricDefinitions/Read|Lê as definições de métrica de base de dados|
|/databaseAccounts/Databases/Metrics/Read|Lê as métricas de base de dados.|
|/databaseAccounts/Databases/usages/Read|Lê as utilizações de base de dados.|
|/databaseAccounts/readonlykeys/Read|Lê a base de dados de chaves de conta de só de leitura.|

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

| Operação | Descrição |
|---|---|
|generateSsoRequest/ação|Gere um pedido de assinatura no Centro de controlo de domínio.|
|validateDomainRegistrationInformation/ação|Validar o objeto de compra de domínio sem submetê-la|
|checkDomainAvailability/ação|Verifique se um domínio está disponível para a compra|
|listDomainRecommendations/ação|Obter as recomendações de domínio de lista com base em palavras-chave|
|registar/ação|Registar o fornecedor de recursos Microsoft Domains para a subscrição|
|domínios/leitura|Obter a lista de domínios|
|domínios/escrita|Adicionar um novo domínio ou atualizar um já existente|
|domínios/eliminar|Elimine um domínio existente.|
|/Domains/operationresults/Read|Obter uma operação de domínio|

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

| Operação | Descrição |
|---|---|
|/lcsprojects/Read|Apresentar os projetos de serviços de ciclo de vida do Microsoft Dynamics que pertençam a um utilizador|
|/ lcsprojects/escrita|Criar e atualizar os projetos de serviços de ciclo de vida do Microsoft Dynamics que pertencem ao utilizador. Os nome e descrição propriedades só podem ser atualizadas. A subscrição e localização associadas ao projeto não podem ser atualizadas após a criação|
|/lcsprojects/DELETE|Eliminar projetos de serviços de ciclo de vida do Microsoft Dynamics que pertencem ao utilizador|
|/lcsprojects/clouddeployments/Read|Apresentar as implementações de avaliação do Microsoft Dynamics AX 2012 R3 num projeto dos serviços de ciclo de vida do Microsoft Dynamics que pertençam a um utilizador|
|/lcsprojects/clouddeployments/Write|Crie implementação de avaliação do Microsoft Dynamics AX 2012 R3 num projeto dos serviços de ciclo de vida do Microsoft Dynamics que pertençam a um utilizador. Implementações podem ser geridas a partir do portal de gestão do Azure|
|/lcsprojects/Connectors/Read|Conectores de leitura que pertençam a um projeto de serviços de ciclo de vida do Microsoft Dynamics|
|/lcsprojects/Connectors/Write|Criar e atualizar os conectores que pertençam a um projeto de serviços de ciclo de vida do Microsoft Dynamics|

## <a name="microsofteventhub"></a>Microsoft.EventHub

| Operação | Descrição |
|---|---|
|checkNameAvailability/ação|Verificações de disponibilidade de espaço de nomes em dada subscrição.|
|registar/ação|Regista a subscrição para o fornecedor de recursos de EventHub e permite a criação dos recursos de EventHub|
|espaços de nomes/escrita|Crie um recurso de espaço de nomes e Atualize as respetivas propriedades. As etiquetas e estado do espaço de nomes são as propriedades que podem ser atualizadas.|
|/Namespaces/Read|Obter a lista de descrição de recursos do espaço de nomes|
|espaços de nomes/eliminar|Eliminar recurso do espaço de nomes|
|/Namespaces/metricDefinitions/Read|Obter a lista de descrições de recursos de métricas de espaço de nomes|
|/Namespaces/authorizationRules/Read|Obter a lista de descrição de regras de autorização de espaços de nomes.|
|/Namespaces/authorizationRules/Write|Criar regras de autorização ao nível de espaço de nomes e Atualize as respetivas propriedades. Os direitos de acesso de regras de autorização, o site primário e as chaves secundárias podem ser atualizadas.|
|/Namespaces/authorizationRules/DELETE|Elimine regra de autorização de espaço de nomes. Não é possível eliminar a regra de autorização de espaço de nomes predefinido. |
|/Namespaces/authorizationRules/listkeys/Action|Obter a cadeia de ligação ao espaço de nomes|
|/Namespaces/authorizationRules/regenerateKeys/Action|Regenerar a chave primária ou secundária para o recurso|
|/Namespaces/eventhubs/Write|Criar ou propriedades de EventHub de atualização.|
|/Namespaces/eventhubs/Read|Obter a lista de descrições de recursos de EventHub|
|/Namespaces/eventhubs/DELETE|Operação para eliminar o recurso de EventHub|
|/Namespaces/eventHubs/consumergroups/Write|Criar ou atualizar ConsumerGroup propriedades.|
|/Namespaces/eventHubs/consumergroups/Read|Obter a lista de descrições de recurso ConsumerGroup|
|/Namespaces/eventHubs/consumergroups/DELETE|Operação para eliminar recurso ConsumerGroup|
|/Namespaces/eventhubs/authorizationRules/Read| Obter a lista de regras de autorização de EventHub|
|/Namespaces/eventhubs/authorizationRules/Write|Criar regras de autorização de EventHub e Atualize as respetivas propriedades. Os direitos de acesso de regras de autorização, o site primário e as chaves secundárias podem ser atualizadas.|
|/Namespaces/eventhubs/authorizationRules/DELETE|Operação para eliminar as regras de autorização de EventHub|
|/Namespaces/eventhubs/authorizationRules/listkeys/Action|Obter a cadeia de ligação a EventHub|
|/Namespaces/eventhubs/authorizationRules/regenerateKeys/Action|Regenerar a chave primária ou secundária para o recurso|
|/Namespaces/diagnosticSettings/Read|Obter a lista de descrições de recursos de definições de diagnóstico de espaço de nomes|
|/Namespaces/diagnosticSettings/Write|Obter a lista de descrições de recursos de definições de diagnóstico de espaço de nomes|
|/Namespaces/logDefinitions/Read|Obter a lista de registos de espaço de nomes descrições de recursos|

## <a name="microsoftfeatures"></a>Microsoft.Features

| Operação | Descrição |
|---|---|
|/providers/Features/Read|Obtém a funcionalidade de uma subscrição de um fornecedor de recursos específico.|
|/providers/Features/register/Action|Regista a funcionalidade para uma subscrição de um fornecedor de recursos específico.|
|/Features/Read|Obtém as funcionalidades de uma subscrição.|

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

| Operação | Descrição |
|---|---|
|clusters/escrita|Criar ou atualizar o Cluster do HDInsight|
|/clusters/Read|Obter detalhes sobre o Cluster do HDInsight|
|/clusters/DELETE|Eliminar um Cluster do HDInsight|
|/clusters/changerdpsetting/Action|Alteração da definição de RDP de Cluster do HDInsight|
|/clusters/Configurations/Action|Atualizar a configuração de Cluster do HDInsight|
|/clusters/Configurations/Read|Obter configurações de Cluster do HDInsight|
|/clusters/roles/resize/Action|Redimensionar um Cluster do HDInsight|
|/Locations/Capabilities/Read|Obter as funções da subscrição|
|/Locations/checkNameAvailability/Read|Verifique a disponibilidade de nome|

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

| Operação | Descrição |
|---|---|
|registar/ação|Regista a subscrição para o fornecedor de recursos de importação/exportação e permite a criação de tarefas de importação/exportação.|
|/ tarefas/escrita|Cria uma tarefa com os parâmetros especificados ou atualiza as propriedades ou etiquetas para a tarefa especificada.|
|/Jobs/Read|Obtém as propriedades para a tarefa especificada ou devolve a lista de tarefas.|
|/Jobs/listBitLockerKeys/Action|Obtém as chaves do BitLocker para a tarefa especificada.|
|/Jobs/DELETE|Elimina uma tarefa existente.|
|/Locations/Read|Obtém as propriedades para a localização especificada ou devolve a lista de localizações.|

## <a name="microsoftinsights"></a>Insights

| Operação | Descrição |
|---|---|
|/ Register/ação|Registar o fornecedor de informações da microsoft|
|/ AlertRules/escrita|Escrever uma configuração de regra de alerta|
|/ AlertRules/eliminar|Eliminar uma configuração de regra de alerta|
|/ AlertRules/leitura|Ler uma configuração de regra de alerta|
|/ AlertRules/ativado/ação|Regra de alerta ativada|
|/ AlertRules/resolvido/ação|Regra de alerta resolvida|
|/ AlertRules/limitadas/ação|Regra de alerta é limitada|
|/ AlertRules/incidentes/leitura|Ler uma configuração de incidente de regra de alerta|
|/ MetricDefinitions/leitura|Definições de métrica de leitura|
|/eventtypes/Values/Read|Leia o artigo gestão valores do tipo de evento|
|/eventtypes/digestevents/Read|Resumo de tipo de evento de gestão de leitura|
|/ Métricas/leitura|Métricas de leitura|
|/ LogProfiles/escrita|Escrever para uma configuração de perfil de registo|
|/ LogProfiles/eliminar|Eliminar a configuração de perfis de registo|
|/ LogProfiles/leitura|Perfis de registo de leitura|
|/ AutoscaleSettings/escrita|Escrever uma configuração de definição de dimensionamento automático|
|/ AutoscaleSettings/eliminar|Eliminar uma configuração de definição de dimensionamento automático|
|/ AutoscaleSettings/leitura|Ler uma configuração de definição de dimensionamento automático|
|/ AutoscaleSettings/Scaleup/ação|Trabalho operação de dimensionamento de dimensionamento automático|
|/ AutoscaleSettings/Scaledown/ação|Escala de dimensionamento automático para baixo de operação|
|/AutoscaleSettings/Providers/Microsoft.Insights/MetricDefinitions/Read|Definições de métrica de leitura|
|/ ActivityLogAlerts/ativado/ação|Acionou o alerta de registo de atividade|
|/ DiagnosticSettings/escrita|Escrever a configuração de definições de diagnóstico|
|/ DiagnosticSettings/eliminar|Eliminar a configuração de definições de diagnóstico|
|/ DiagnosticSettings/leitura|Ler uma configuração de definições de diagnóstico|
|/ LogDefinitions/leitura|Definições de registo de leitura|
|/ ExtendedDiagnosticSettings/escrita|Escrever a configuração de definições de diagnóstico expandida|
|/ ExtendedDiagnosticSettings/eliminar|Eliminar a configuração de definições de diagnóstico expandida|
|/ ExtendedDiagnosticSettings/leitura|Ler uma configuração de definições de diagnóstico expandida|

## <a name="microsoftkeyvault"></a>Keyvault

| Operação | Descrição |
|---|---|
|registar/ação|Regista uma subscrição|
|/checkNameAvailability/Read|Verifica se um nome de cofre de chaves é válido e se não está em utilização|
|/vaults/Read|Ver as propriedades de um cofre de chaves|
|/ cofres/escrita|Criar um novo cofre de chaves ou atualizar as propriedades de um cofre de chaves existente|
|/vaults/DELETE|Eliminar um cofre de chaves|
|/vaults/Deploy/Action|Permite o acesso ao segredos no Cofre de chaves durante a implementação de recursos do Azure|
|/vaults/Secrets/Read|Ver as propriedades de um segredo, mas não o seu valor|
|/vaults/Secrets/Write|Crie um novo segredo ou atualize o valor de um segredo existente|
|/vaults/accessPolicies/Write|Atualize uma política de acesso existente através da intercalação ou substituição, ou adicione uma nova política de acesso ao cofre.|
|/deletedVaults/Read|Ver as propriedades de forma recuperável cofres de chaves eliminados|
|/Locations/operationResults/Read|Verificar o resultado de uma operação de execução longa|
|/Locations/deletedVaults/Read|Ver as propriedades de um cofre de chaves eliminado recuperável|
|/Locations/deletedVaults/PURGE/Action|Remover um cofre de chaves eliminado recuperável|

## <a name="microsoftlogic"></a>Microsoft.Logic

| Operação | Descrição |
|---|---|
|/workflows/Read|Lê o fluxo de trabalho.|
|fluxos de trabalho/escrita|Cria ou atualiza o fluxo de trabalho.|
|/workflows/DELETE|Elimina o fluxo de trabalho.|
|/workflows/Run/Action|Inicia a execução do fluxo de trabalho.|
|/workflows/disable/Action|Desativa o fluxo de trabalho.|
|/workflows/Enable/Action|Permite que o fluxo de trabalho.|
|/workflows/Validate/Action|Valida o fluxo de trabalho.|
|/workflows/move/Action|Move o fluxo de trabalho do respetivo existente id de subscrição, grupo de recursos, e/ou nome para um id de subscrição diferente, grupo de recursos, e/ou nome.|
|/workflows/listSwagger/Action|Obtém o fluxo de trabalho definições swagger.|
|/workflows/regenerateAccessKey/Action|Os segredos de chave de acesso de gera de novo.|
|/workflows/listCallbackUrl/Action|Obtém o URL de chamada de retorno de fluxo de trabalho.|
|/workflows/versions/Read|Lê a versão de fluxo de trabalho.|
|/workflows/versions/triggers/listCallbackUrl/Action|Obtém o URL de chamada de retorno de Acionador.|
|fluxos de trabalho/executa/leitura|Lê a execução do fluxo de trabalho.|
|/workflows/runs/Cancel/Action|Cancela a execução de um fluxo de trabalho.|
|/workflows/runs/Actions/Read|Lê o fluxo de trabalho a executar a ação.|
|/workflows/runs/Operations/Read|Lê o estado da operação de execução do fluxo de trabalho.|
|/workflows/triggers/Read|Lê o acionador.|
|/workflows/triggers/Run/Action|Executa o acionador.|
|/workflows/triggers/listCallbackUrl/Action|Obtém o URL de chamada de retorno de Acionador.|
|/workflows/triggers/histories/Read|Lê os histories de Acionador.|
|/workflows/triggers/histories/resubmit/Action|Resubmits o acionador do fluxo de trabalho.|
|/workflows/accessKeys/Read|Lê a chave de acesso.|
|/workflows/accessKeys/Write|Cria ou atualiza a chave de acesso.|
|/workflows/accessKeys/DELETE|Elimina a chave de acesso.|
|/workflows/accessKeys/List/Action|Lista os segredos de chave de acesso.|
|/workflows/accessKeys/Regenerate/Action|Os segredos de chave de acesso de gera de novo.|
|/Locations/workflows/Validate/Action|Valida o fluxo de trabalho.|

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

| Operação | Descrição |
|---|---|
|registar/ação|Regista a subscrição para a fornecedor de recursos do serviço web de aprendizagem e permite a criação de serviços web.|
|webServices/ação|Criar as propriedades do serviço Web regional para as regiões suportadas|
|/commitmentPlans/Read|Ler qualquer compromisso plano de aprendizagem|
|/ commitmentPlans/escrita|Criar ou atualizar qualquer plano de compromisso do Machine Learning|
|/commitmentPlans/DELETE|Eliminar qualquer compromisso plano de aprendizagem|
|/commitmentPlans/JOIN/Action|Aderir a qualquer máquina compromisso plano de aprendizagem|
|/commitmentPlans/commitmentAssociations/Read|Qualquer do Machine Learning compromisso plano de associação de leitura|
|/commitmentPlans/commitmentAssociations/move/Action|Mover quaisquer do Machine Learning compromisso plano de associação|
|/ Áreas de trabalho/leitura|Ler qualquer área de trabalho de aprendizagem|
|/ Áreas de trabalho/escrita|Criar ou atualizar qualquer área de trabalho de aprendizagem|
|/ Áreas de trabalho/eliminar|Eliminar qualquer área de trabalho de aprendizagem|
|/ Áreas de trabalho/listworkspacekeys/ação|Lista de chaves para uma área de trabalho do Machine Learning|
|/ Áreas de trabalho/resyncstoragekeys/ação|Ressincronizar chaves de conta de armazenamento configurada para uma área de trabalho do Machine Learning|
|/webServices/Read|Ler a qualquer serviço Web do Machine Learning|
|/ webServices/escrita|Criar ou atualizar qualquer serviço Web do Machine Learning|
|/webServices/DELETE|Eliminar qualquer serviço Web do Machine Learning|

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

| Operação | Descrição |
|---|---|
|/agreements/offers/plans/Read|Devolver um contrato de um item do marketplace indicado|
|/agreements/offers/plans/Sign/Action|Assinar um contrato de um item do marketplace indicado|
|/agreements/offers/plans/Cancel/Action|Cancelar um contrato de um item do marketplace indicado|

## <a name="microsoftmedia"></a>Microsoft.Media

| Operação | Descrição |
|---|---|
|/mediaservices/Read||
|/ mediaservices/escrita||
|/mediaservices/DELETE||
|/mediaservices/regenerateKey/Action||
|/mediaservices/listKeys/Action||
|/mediaservices/syncStorageKeys/Action||

## <a name="microsoftnetwork"></a>Microsoft.Network

| Operação | Descrição |
|---|---|
|registar/ação|Regista a subscrição do|
|ação de anular o registo /|Anula o registo da subscrição|
|checkTrafficManagerNameAvailability/ação|Verifica a disponibilidade de um nome de DNS de caminho relativo do Gestor de tráfego.|
|/dnszones/Read|Obtenha a zona DNS, no formato JSON. As propriedades da zona incluem as etiquetas, etag, numberOfRecordSets e maxNumberOfRecordSets. Tenha em atenção que este comando não obtém os conjuntos de registos contidos na zona.|
|/ dnszones/escrita|Crie ou Atualize uma zona DNS dentro de um grupo de recursos.  Utilizado para atualizar as etiquetas num recurso de zona DNS. Tenha em atenção que este comando não pode ser utilizado para criar ou atualizar conjuntos de registos na zona.|
|/dnszones/DELETE|Elimine a zona DNS, no formato JSON. As propriedades da zona incluem as etiquetas, etag, numberOfRecordSets e maxNumberOfRecordSets.|
|/dnszones/MX/Read|Obtenha o conjunto de registos do tipo "MX", no formato JSON. O conjunto de registos contém uma lista de registos, bem como o TTL, as etiquetas e etag.|
|/dnszones/MX/Write|Crie ou Atualize um conjunto de registos do tipo "MX" dentro de uma zona DNS. Os registos especificados irão substituir os registos atuais no conjunto de registos.|
|/dnszones/MX/DELETE|Remova o conjunto de registos de um determinado nome e tipo "MX" de uma zona DNS.|
|/dnszones/NS/Read|Obtém o conjunto de registos de DNS do tipo NS|
|/dnszones/NS/Write|Cria ou atualiza o conjunto de registos de DNS do tipo NS|
|/dnszones/NS/DELETE|Elimina o conjunto de registos DNS do tipo NS|
|/dnszones/aaaa/Read|Obtenha o conjunto de registos do tipo "AAAA", no formato JSON. O conjunto de registos contém uma lista de registos, bem como o TTL, as etiquetas e etag.|
|/dnszones/aaaa/Write|Crie ou Atualize um conjunto de registos do tipo "AAAA" dentro de uma zona DNS. Os registos especificados irão substituir os registos atuais no conjunto de registos.|
|/dnszones/aaaa/DELETE|Remova o conjunto de registos de um determinado nome e tipo "AAAA" de uma zona DNS.|
|/dnszones/CNAME/Read|Obtenha o conjunto de registos do tipo "CNAME", no formato JSON. O conjunto de registos contém o TTL, as etiquetas e etag.|
|/dnszones/CNAME/Write|Crie ou Atualize um conjunto de registos do tipo "CNAME" dentro de uma zona DNS. Os registos especificados irão substituir os registos atuais no conjunto de registos.|
|/dnszones/CNAME/DELETE|Remova o conjunto de registos de um determinado nome e tipo "CNAME" de uma zona DNS.|
|/dnszones/SOA/Read|Obtém o conjunto de registos DNS do tipo SOA|
|/dnszones/SOA/Write|Cria ou atualiza o conjunto de registos DNS do tipo SOA|
|/dnszones/SRV/Read|Obtenha o conjunto de registos do tipo "SRV", no formato JSON. O conjunto de registos contém uma lista de registos, bem como o TTL, as etiquetas e etag.|
|/dnszones/SRV/Write|Criar ou atualizar conjunto de registos do tipo SRV|
|/dnszones/SRV/DELETE|Remova o conjunto de registos de um determinado nome e tipo "SRV" de uma zona DNS.|
|/dnszones/PTR/Read|Obtenha o conjunto de registos do tipo "PTR", no formato JSON. O conjunto de registos contém uma lista de registos, bem como o TTL, as etiquetas e etag.|
|/dnszones/PTR/Write|Crie ou Atualize um conjunto de registos do tipo "PTR" dentro de uma zona DNS. Os registos especificados irão substituir os registos atuais no conjunto de registos.|
|/dnszones/PTR/DELETE|Remova o conjunto de registos de um determinado nome e tipo "PTR" de uma zona DNS.|
|/dnszones/A/Read|Obtenha o conjunto de registos do tipo "A", no formato JSON. O conjunto de registos contém uma lista de registos, bem como o TTL, as etiquetas e etag.|
|/dnszones/A/Write|Crie ou Atualize um conjunto de registos do tipo 'A' dentro de uma zona DNS. Os registos especificados irão substituir os registos atuais no conjunto de registos.|
|/dnszones/A/DELETE|Remova o conjunto de registos de um determinado nome e tipo "A" de uma zona DNS.|
|/dnszones/txt/Read|Obtenha o conjunto de registos do tipo "TXT", no formato JSON. O conjunto de registos contém uma lista de registos, bem como o TTL, as etiquetas e etag.|
|/dnszones/txt/Write|Crie ou Atualize um conjunto de registos do tipo "TXT" dentro de uma zona DNS. Os registos especificados irão substituir os registos atuais no conjunto de registos.|
|/dnszones/txt/DELETE|Remova o conjunto de registos de um determinado nome e tipo "TXT" de uma zona DNS.|
|/dnszones/recordsets/Read|Obtém os conjuntos de registos de DNS em tipos|
|/networkInterfaces/Read|Obtém uma definição de interface de rede. |
|nomes de networkInterfaces/escrita|Cria uma interface de rede ou atualiza uma interface de rede existente. |
|/networkInterfaces/JOIN/Action|Associa uma Máquina Virtual a uma interface de rede|
|/networkInterfaces/DELETE|Elimina uma interface de rede|
|/networkInterfaces/effectiveRouteTable/Action|Obter tabela de rotas configurado na Interface de rede da Vm|
|/networkInterfaces/effectiveNetworkSecurityGroups/Action|Obter grupos de segurança de rede configurado na Interface da Vm de rede|
|/networkInterfaces/loadBalancers/Read|Obtém todos os balanceadores de carga que a interface de rede faz parte|
|/networkInterfaces/ipconfigurations/Read|Obtém uma definição de configuração de ip de interface de rede. |
|/publicIPAddresses/Read|Obtém uma definição de endereço ip público.|
|/ publicIPAddresses/escrita|Cria um endereço Ip público ou atualiza um endereço Ip público existente. |
|/publicIPAddresses/DELETE|Elimina um endereço Ip público.|
|/publicIPAddresses/JOIN/Action|Associa um endereço ip público|
|/routeFilters/Read|Obtém uma definição de filtro de rota|
|/routeFilters/JOIN/Action|Associa um filtro de rota|
|/routeFilters/DELETE|Elimina uma definição de filtro de rota|
|/ routeFilters/escrita|Cria um filtro de rota ou atualiza um filtro já existente|
|/routeFilters/Rules/Read|Obtém a definição de uma regra de filtro de rota|
|/routeFilters/Rules/Write|Cria uma regra de filtro de rota ou atualiza uma regra de filtro de rota existente|
|/routeFilters/Rules/DELETE|Elimina a definição de uma regra de filtro de rota|
|/networkWatchers/Read|Obter a definição de observador de rede|
|/ networkWatchers/escrita|Cria um observador de rede ou atualiza um observador de rede existente|
|/networkWatchers/DELETE|Elimina um observador de rede|
|/networkWatchers/configureFlowLog/Action|Configura o registo de fluxo para um recurso de destino.|
|/networkWatchers/ipFlowVerify/Action|Devolve se o pacote é permitido ou negado de ou para um destino específico.|
|/networkWatchers/nextHop/Action|Para um destino especificado e o endereço IP de destino, devolver o tipo de salto seguinte e hope seguinte endereço IP.|
|/networkWatchers/queryFlowLogStatus/Action|Obtém o estado do fluxo de registo num recurso.|
|/networkWatchers/queryTroubleshootResult/Action|Obtém o resultado de resolução de problemas de anteriormente executados ou atualmente em execução a operação de resolução de problemas.|
|/networkWatchers/securityGroupView/Action|Ver as regras de grupo de segurança de rede configurados e eficaz aplicadas numa VM.|
|/networkWatchers/Topology/Action|Obtém uma vista de nível de rede de recursos e as respetivas relações num grupo de recursos.|
|/networkWatchers/Troubleshoot/Action|Inicia a resolução de problemas num recurso de rede no Azure.|
|/networkWatchers/packetCaptures/queryStatus/Action|Obtém informações sobre as propriedades e estado de um recurso de captura de pacotes.|
|/networkWatchers/packetCaptures/Stop/Action|Pare a sessão de captura de pacote em execução.|
|/networkWatchers/packetCaptures/Read|Obter a definição de captura de pacotes|
|/networkWatchers/packetCaptures/Write|Cria uma captura de pacotes|
|/networkWatchers/packetCaptures/DELETE|Elimina uma captura de pacotes|
|/loadBalancers/Read|Obtém uma definição de Balanceador de carga|
|/ loadBalancers/escrita|Cria um balanceador de carga ou atualiza um balanceador de carga existente|
|/loadBalancers/DELETE|Elimina um balanceador de carga|
|/loadBalancers/networkInterfaces/Read|Obtém as referências a todas as interfaces de rede num Balanceador de carga|
|/loadBalancers/loadBalancingRules/Read|Obtém uma definição de regra balanceamento de carga Balanceador de carga|
|/loadBalancers/backendAddressPools/Read|Obtém uma definição de conjunto de endereços de back-end de Balanceador de carga|
|/loadBalancers/backendAddressPools/JOIN/Action|Associa um conjunto de endereços de back-end de Balanceador de carga|
|/loadBalancers/inboundNatPools/Read|Obtém um balanceador de carga definição do conjunto nat de entrada|
|/loadBalancers/inboundNatPools/JOIN/Action|Associa um balanceador de carga conjunto nat de entrada|
|/loadBalancers/inboundNatRules/Read|Obtém um balanceador de carga definição da regra nat de entrada|
|/loadBalancers/inboundNatRules/Write|Cria uma regra de nat de entrada do Balanceador de carga ou atualiza uma regra nat de entrada do Balanceador de carga existente|
|/loadBalancers/inboundNatRules/DELETE|Elimina uma regra de nat de entrada do Balanceador de carga|
|/loadBalancers/inboundNatRules/JOIN/Action|Associa uma regra de nat de entrada do Balanceador de carga|
|/loadBalancers/outboundNatRules/Read|Obtém uma definição de regra de nat de saída de Balanceador de carga|
|/loadBalancers/probes/Read|Obtém uma sonda do Balanceador de carga|
|/loadBalancers/virtualMachines/Read|Obtém as referências a todas as máquinas virtuais num Balanceador de carga|
|/loadBalancers/frontendIPConfigurations/Read|Obtém uma definição de configuração de IP de front-end de Balanceador de carga|
|/trafficManagerGeographicHierarchies/Read|Obtém a hierarquia de Geographic do Gestor de tráfego que contém regiões que podem ser utilizadas com o método de encaminhamento de tráfego geográfica|
|/bgpServiceCommunities/Read|Obter Comunidades de Bgp de serviço|
|/applicationGatewayAvailableWafRuleSets/Read|Obtém o Gateway de aplicação define a regra de Waf disponíveis|
|/virtualNetworks/Read|Obter a definição de rede virtual|
|/ virtualNetworks/escrita|Cria uma rede virtual ou atualiza uma rede virtual existente|
|/virtualNetworks/DELETE|Elimina uma rede virtual|
|/virtualNetworks/peer/Action|Peers uma rede virtual com a outra rede virtual|
|/virtualNetworks/virtualNetworkPeerings/Read|Obtém uma definição de peering de rede virtual|
|/virtualNetworks/virtualNetworkPeerings/Write|Cria um peering de rede virtual ou atualiza um peering de rede virtual existente|
|/virtualNetworks/virtualNetworkPeerings/DELETE|Elimina um peering de rede virtual|
|/virtualNetworks/Subnets/Read|Obtém uma definição de sub-rede de rede virtual|
|/virtualNetworks/Subnets/Write|Cria uma sub-rede de rede virtual ou atualiza uma sub-rede de rede virtual existente|
|/virtualNetworks/Subnets/DELETE|Elimina uma sub-rede de rede virtual|
|/virtualNetworks/Subnets/JOIN/Action|Associa uma rede virtual|
|/virtualNetworks/Subnets/joinViaServiceTunnel/Action|Junta recursos, tais como a conta de armazenamento ou base de dados SQL para uma sub-rede serviço túnel ativada.|
|/virtualNetworks/Subnets/virtualMachines/Read|Obtém as referências a todas as máquinas virtuais numa sub-rede de rede virtual|
|/virtualNetworks/checkIpAddressAvailability/Read|Verifique se o endereço Ip está disponível na rede virtual especificada|
|/virtualNetworks/virtualMachines/Read|Obtém as referências a todas as máquinas virtuais numa rede virtual|
|/expressRouteServiceProviders/Read|Obtém os fornecedores de serviços de Expressroute|
|/dnsoperationresults/Read|Obtém os resultados de uma operação de DNS|
|/localnetworkgateways/Read|Obtém LocalNetworkGateway|
|/ localnetworkgateways/escrita|Cria ou atualiza um LocalNetworkGateway existente|
|/localnetworkgateways/DELETE|Elimina LocalNetworkGateway|
|/trafficManagerProfiles/Read|Obter a configuração do perfil do Traffic Manager. Isto inclui as definições DNS, as definições de encaminhamento de tráfego, as definições de monitorização do ponto final e a lista de pontos finais encaminhados por este perfil do Traffic Manager.|
|/ trafficManagerProfiles/escrita|Criar um perfil de Gestor de tráfego ou modificar a configuração de um perfil do Traffic Manager existente. Isto inclui ativar ou desativar um perfil e modificar as definições de DNS, as definições de encaminhamento de tráfego ou as definições de monitorização do ponto final. Os pontos finais encaminhados pelo perfil do Gestor de tráfego podem ser adicionados, removidos, ativados ou desativados.|
|/trafficManagerProfiles/DELETE|Elimine o perfil do Traffic Manager. Todas as definições associadas ao perfil do Gestor de tráfego serão perdidas e o perfil já não pode ser utilizado para encaminhar o tráfego.|
|/dnsoperationstatuses/Read|Obtém o estado de uma operação de DNS |
|/Operations/Read|Obter operações disponíveis|
|/expressRouteCircuits/Read|Obter um ExpressRouteCircuit|
|/ expressRouteCircuits/escrita|Cria ou atualiza um ExpressRouteCircuit existente|
|/expressRouteCircuits/DELETE|Elimina um ExpressRouteCircuit|
|/expressRouteCircuits/Stats/Read|Obtém um ExpressRouteCircuit Stat|
|/expressRouteCircuits/peerings/Read|Obtém um ExpressRouteCircuit Peering|
|/expressRouteCircuits/peerings/Write|Cria ou atualiza um ExpressRouteCircuit Peering existente|
|/expressRouteCircuits/peerings/DELETE|Elimina um ExpressRouteCircuit Peering|
|/expressRouteCircuits/peerings/arpTables/Action|Obtém um ArpTable ExpressRouteCircuit de Peering|
|/expressRouteCircuits/peerings/routeTables/Action|Obtém um RouteTable ExpressRouteCircuit de Peering|
|/expressRouteCircuits/peerings /<br>routeTablesSummary/ação|Obtém um resumo de RouteTable Peering ExpressRouteCircuit|
|/expressRouteCircuits/peerings/Stats/Read|Obtém um ExpressRouteCircuit Peering Stat|
|/expressRouteCircuits/Authorizations/Read|Obtém uma ExpressRouteCircuit autorização|
|/expressRouteCircuits/Authorizations/Write|Cria ou atualiza uma autorização ExpressRouteCircuit existente|
|/expressRouteCircuits/Authorizations/DELETE|Elimina uma ExpressRouteCircuit autorização|
|/Connections/Read|Obtém o VirtualNetworkGatewayConnection|
|ligações/escrita|Cria ou atualiza um VirtualNetworkGatewayConnection existente|
|/Connections/DELETE|Elimina o VirtualNetworkGatewayConnection|
|/Connections/sharedKey/Read|Obtém o VirtualNetworkGatewayConnection SharedKey|
|/Connections/sharedKey/Write|Cria ou atualiza um VirtualNetworkGatewayConnection SharedKey existente|
|/networkSecurityGroups/Read|Obtém uma definição de grupo de segurança de rede|
|/ networkSecurityGroups/escrita|Cria um grupo de segurança de rede ou atualiza um grupo de segurança de rede existente|
|/networkSecurityGroups/DELETE|Elimina um grupo de segurança de rede|
|/networkSecurityGroups/JOIN/Action|Associa um grupo de segurança de rede|
|/networkSecurityGroups/defaultSecurityRules/Read|Obtém uma definição de regra de segurança predefinida|
|/networkSecurityGroups/securityRules/Read|Obtém a definição de uma regra de segurança|
|/networkSecurityGroups/securityRules/Write|Cria uma regra de segurança ou atualiza uma regra de segurança existente|
|/networkSecurityGroups/securityRules/DELETE|Elimina uma regra de segurança|
|/applicationGateways/Read|Obtém um gateway de aplicação|
|/ applicationGateways/escrita|Cria um gateway de aplicação ou atualiza um gateway de aplicação|
|/applicationGateways/DELETE|Elimina um gateway de aplicação|
|/applicationGateways/backendhealth/Action|Obtém um funcionamento de back-end do gateway de aplicação|
|/applicationGateways/Start/Action|Inicia um gateway de aplicação|
|/applicationGateways/Stop/Action|Parar um gateway de aplicação|
|/applicationGateways/backendAddressPools/JOIN/Action|Associa um conjunto de endereços de back-end de gateway de aplicação|
|/routeTables/Read|Obtém uma definição de tabela de rota|
|/ routeTables/escrita|Cria uma tabela de rotas ou atualiza uma tabela já existente|
|/routeTables/DELETE|Elimina uma definição de tabela de rota|
|/routeTables/JOIN/Action|Junta a tabela de rotas|
|/routeTables/routes/Read|Obtém uma definição de rota|
|/routeTables/routes/Write|Cria uma rota ou atualiza uma já existente|
|/routeTables/routes/DELETE|Elimina uma definição de rota|
|/Locations/operationResults/Read|Obtém o resultado de um async POST ou operação de eliminação|
|/Locations/checkDnsNameAvailability/Read|Verifica se a etiqueta de dns está disponível na localização especificada|
|/Locations/usages/Read|Obtém os recursos a métrica de utilização|
|/Locations/Operations/Read|Obtém o recurso da operação que representa o estado de uma operação assíncrona|

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

| Operação | Descrição |
|---|---|
|registar/ação|Regista a subscrição para o fornecedor de recursos NotifciationHubs e permite a criação de espaços de nomes e Notification hubs|
|/ CheckNamespaceAvailability/ação|Verifica se um determinado nome de recurso de espaço de nomes está disponível no âmbito do serviço de NotificationHub.|
|/ Espaços de nomes/escrita|Crie um recurso de espaço de nomes e Atualize as respetivas propriedades. As etiquetas e estado do espaço de nomes são as propriedades que podem ser atualizadas.|
|/ Espaços de nomes/leitura|Obter a lista de descrição de recursos do espaço de nomes|
|/ Espaços de nomes/eliminar|Eliminar recurso do espaço de nomes|
|/ Espaços de nomes/authorizationRules/ação|Obter a lista de descrição de regras de autorização de espaços de nomes.|
|/ Espaços de nomes/CheckNotificationHubAvailability/ação|Verifica se é ou não um determinado nome de NotificationHub disponível dentro de um espaço de nomes.|
|/ Espaços de nomes/authorizationRules/escrita|Criar regras de autorização ao nível de espaço de nomes e Atualize as respetivas propriedades. Os direitos de acesso de regras de autorização, o site primário e as chaves secundárias podem ser atualizadas.|
|/ Espaços de nomes/authorizationRules/leitura|Obter a lista de descrição de regras de autorização de espaços de nomes.|
|/ Espaços de nomes/authorizationRules/eliminar|Elimine regra de autorização de espaço de nomes. Não é possível eliminar a regra de autorização de espaço de nomes predefinido. |
|/ Espaços de nomes/authorizationRules/listkeys/ação|Obter a cadeia de ligação ao espaço de nomes|
|/ Espaços de nomes/authorizationRules/regenerateKeys/ação|Espaço de nomes autorização regra regenerar principal/SecondaryKey, especifique a chave tem de ser novamente geradas|
|/ Espaços de nomes/NotificationHubs/escrita|Criar um Hub de notificação e atualizar as respetivas propriedades. As respetivas propriedades incluem principalmente PNS credenciais. Regras de autorização e TTL|
|/ Espaços de nomes/NotificationHubs/leitura|Obter a lista de descrições de recursos de Hub de notificação|
|/ Espaços de nomes/NotificationHubs/eliminar|Eliminar recurso do Hub de notificação|
|/ Espaços de nomes/NotificationHubs/authorizationRules/ação|Obter a lista de regras de autorização de Hub de notificação|
|/ Espaços de nomes/NotificationHubs/pnsCredentials/ação|Obter todas as credenciais PNS do Hub de notificação. Isto inclui as credenciais do WNS, o MPNS, o APNS, o GCM e o Baidu|
|/ Espaços de nomes/NotificationHubs/debugSend/ação|Envie uma notificação push de teste.|
|/ Espaços de nomes/NotificationHubs/metricDefinitions/leitura|Obter a lista de descrições de recursos de métricas de espaço de nomes|
|/Namespaces/NotificationHubs /<br>authorizationRules/escrita|Criar regras de autorização de Hub de notificação e atualizar as respetivas propriedades. Os direitos de acesso de regras de autorização, o site primário e as chaves secundárias podem ser atualizadas.|
|/Namespaces/NotificationHubs /<br>authorizationRules/leitura|Obter a lista de regras de autorização de Hub de notificação|
|/Namespaces/NotificationHubs /<br>authorizationRules/eliminar|Eliminar regras de autorização de Hub de notificação|
|/Namespaces/NotificationHubs /<br>listkeys/authorizationRules/ação|Obter a cadeia de ligação para o Hub de notificação|
|/Namespaces/NotificationHubs /<br>regenerateKeys/authorizationRules/ação|SecondaryKey/Hub autorização regra regenerar primário, notificação especifique a chave tem de ser novamente geradas|

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

| Operação | Descrição |
|---|---|
|registar/ação|Registe uma subscrição para um fornecedor de recursos.|
|/linkTargets/Read|Apresenta uma lista de contas existentes que não estão associadas uma subscrição do Azure. Para ligar esta subscrição do Azure a uma área de trabalho, utilize um id de cliente devolvido por esta operação na propriedade de id de cliente da operação de área de trabalho de criar.|
|/ áreas de trabalho/escrita|Cria uma nova área de trabalho ou ligações para uma área de trabalho existente ao fornecer o id de cliente da área de trabalho existente.|
|/Workspaces/Read|Obtém uma área de trabalho existente|
|/Workspaces/DELETE|Elimina uma área de trabalho. Se a área de trabalho foi associada a uma área de trabalho existente no momento da criação a área de trabalho que foi ligada não é eliminada.|
|/Workspaces/generateregistrationcertificate/Action|Gera o certificado de registo para a área de trabalho. Este certificado é utilizado para ligar o Microsoft System Center Operation Manager à área de trabalho.|
|/Workspaces/sharedKeys/Action|Obtém as chaves partilhadas para a área de trabalho. Estas chaves são utilizadas para ligar agentes de informações operacionais do Microsoft à área de trabalho.|
|/Workspaces/Search/Action|Executa uma consulta de pesquisa|
|/Workspaces/DataSources/Read|Obter as origens de dados na área de trabalho.|
|/Workspaces/DataSources/Write|Criar/atualizar as origens de dados na área de trabalho.|
|/Workspaces/DataSources/DELETE|Elimine as origens de dados na área de trabalho.|
|/Workspaces/managementGroups/Read|Obtém os nomes e os metadados para grupos de gestão do System Center Operations Manager ligados a esta área de trabalho.|
|/Workspaces/Schema/Read|Obtém o esquema de pesquisa para a área de trabalho.  Esquema de pesquisa inclui os campos expostos e os respetivos tipos.|
|/Workspaces/usages/Read|Obtém dados de utilização de uma área de trabalho, incluindo a quantidade de dados lidos pela área de trabalho.|
|/Workspaces/intelligencepacks/Read|Apresenta uma lista de todos os pacotes de intelligence que estão visíveis para um determinado worksapce e também indica se o pacote está ativado ou desativado para essa área de trabalho.|
|/Workspaces/intelligencepacks/Enable/Action|Permite que um pacote de análise para uma área de trabalho especificado.|
|/Workspaces/intelligencepacks/disable/Action|Desativa um pacote de análise para uma área de trabalho especificado.|
|/Workspaces/sharedKeys/Read|Obtém as chaves partilhadas para a área de trabalho. Estas chaves são utilizadas para ligar agentes de informações operacionais do Microsoft à área de trabalho.|
|/Workspaces/savedSearches/Read|Obtém uma consulta de pesquisa guardada|
|/Workspaces/savedSearches/Write|Cria uma consulta de pesquisa guardada|
|/Workspaces/savedSearches/DELETE|Elimina uma consulta de pesquisa guardada|
|/Workspaces/storageinsightconfigs/Write|Cria uma nova configuração de armazenamento. Estas configurações são utilizadas para solicitar dados a partir de uma localização de uma conta de armazenamento existente.|
|/Workspaces/storageinsightconfigs/Read|Obtém uma configuração de armazenamento.|
|/Workspaces/storageinsightconfigs/DELETE|Elimina uma configuração de armazenamento. Isto irá parar informações operacionais do Microsoft ao ler os dados da conta do storage.|
|/Workspaces/configurationScopes/Read|Obter o âmbito de configuração|
|/Workspaces/configurationScopes/Write|Definir âmbito de configuração|
|/Workspaces/configurationScopes/DELETE|Eliminar âmbito de configuração|

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

| Operação | Descrição |
|---|---|
|registar/ação|Registe uma subscrição para um fornecedor de recursos.|
|soluções/escrita|Criar nova solução do OMS|
|/Solutions/Read|Obter a sair com a solução do OMS|
|/Solutions/DELETE|Eliminar a solução existente do OMS|

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

| Operação | Descrição |
|---|---|
|/ Cofres/backupJobsExport/ação|Tarefas de exportação|
|/ Cofres/escrita|A operação Criar Cofre cria um recurso do tipo "cofre" do Azure|
|/ Cofres/leitura|A operação de introdução cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre'|
|/ Cofres/eliminar|A operação eliminar cofre elimina os recursos do Azure especificado do tipo 'cofre'|
|/ Cofres/refreshContainers/leitura|Atualiza a lista de contentor|
|/ Cofres/backupJobsExport/operationResults/leitura|Devolve o resultado da operação de tarefa de exportação.|
|/ Cofres/backupOperationResults/leitura|Devolve o Resultado da Operação da Cópia de Segurança do Cofre de Serviços de Recuperação.|
|/ Cofres/monitoringAlerts/leitura|Obtém os alertas para o Cofre de serviços de recuperação.|
|/Vaults monitoringAlerts / {uniqueAlertId} / leitura|Obtém os detalhes do alerta.|
|/ Cofres/backupSecurityPIN/leitura|Devolve a segurança PIN Cofre de serviços de informação para recuperação.|
|/vaults/replicationEvents/Read|Ler quaisquer eventos|
|/ Cofres/backupProtectableItems/leitura|Devolve a lista de todos os Itens Susceptíveis de Proteção.|
|/vaults/replicationFabrics/Read|Ler todos os recursos de infraestrutura|
|/vaults/replicationFabrics/Write|Criar ou atualizar quaisquer recursos de infraestrutura|
|/vaults/replicationFabrics/remove/Action|Remover recursos de infraestrutura|
|/vaults/replicationFabrics/checkConsistency/Action|Verifica a Consistência dos Recursos de infraestrutura|
|/vaults/replicationFabrics/DELETE|Elimine quaisquer recursos de infraestrutura|
|/vaults/replicationFabrics/renewcertificate/Action||
|/vaults/replicationFabrics/deployProcessServerImage/Action|Implementar a imagem de servidor de processo|
|/vaults/replicationFabrics/reassociateGateway/Action|Reassociar Gateway|
|/ cofres replicationFabrics/replicationRecoveryServicesProviders /<br>Leitura|Ler quaisquer fornecedores de serviços de recuperação|
|/ cofres replicationFabrics/replicationRecoveryServicesProviders /<br>remover/ação|Remover o fornecedor de serviços de recuperação|
|/ cofres replicationFabrics/replicationRecoveryServicesProviders /<br>eliminar|Eliminar quaisquer fornecedores de serviços de recuperação|
|/ cofres replicationFabrics/replicationRecoveryServicesProviders /<br>refreshProvider/ação|Atualize o fornecedor|
|/vaults/replicationFabrics/replicationStorageClassifications/Read|Ler as classificações de armazenamento|
|/ cofres replicationFabrics/replicationStorageClassifications /<br>replicationStorageClassificationMappings/leitura|Ler quaisquer mapeamentos de classificação de armazenamento|
|/ cofres replicationFabrics/replicationStorageClassifications /<br>replicationStorageClassificationMappings/escrita|Criar ou atualizar quaisquer mapeamentos de classificação de armazenamento|
|/ cofres replicationFabrics/replicationStorageClassifications /<br>replicationStorageClassificationMappings/eliminar|Eliminar quaisquer mapeamentos de classificação de armazenamento|
|/vaults/replicationFabrics/replicationvCenters/Read|Ler todas as tarefas|
|/vaults/replicationFabrics/replicationvCenters/Write|Criar ou atualizar todas as tarefas|
|/vaults/replicationFabrics/replicationvCenters/DELETE|Eliminar todas as tarefas|
|/vaults/replicationFabrics/replicationNetworks/Read|Ler a quaisquer redes|
|/ cofres replicationFabrics/replicationNetworks /<br>replicationNetworkMappings/leitura|Leia os mapeamentos de rede|
|/ cofres replicationFabrics/replicationNetworks /<br>replicationNetworkMappings/escrita|Criar ou atualizar quaisquer mapeamentos de rede|
|/ cofres replicationFabrics/replicationNetworks /<br>replicationNetworkMappings/eliminar|Eliminar quaisquer mapeamentos de rede|
|/ cofres replicationFabrics/replicationProtectionContainers /<br>Leitura|Leu os contentores de proteção|
|/ cofres replicationFabrics/replicationProtectionContainers /<br>discoverProtectableItem/ação|Detetar o Item Protegível|
|/ cofres replicationFabrics/replicationProtectionContainers /<br>escrita|Criar ou atualizar quaisquer contentores de proteção|
|/ cofres replicationFabrics/replicationProtectionContainers /<br>remover/ação|Remover o contentor de proteção|
|/ cofres replicationFabrics/replicationProtectionContainers /<br>switchprotection/ação|Contentor de proteção do comutador|
|/ cofres replicationFabrics/replicationProtectionContainers /<br>replicationProtectableItems/leitura|Ler quaisquer itens passíveis de proteção|
|/ cofres replicationFabrics/replicationProtectionContainers /<br>replicationProtectionContainerMappings/leitura|Ler quaisquer mapeamentos de contentor de proteção|
|/ cofres replicationFabrics/replicationProtectionContainers /<br>replicationProtectionContainerMappings/escrita|Criar ou atualizar quaisquer mapeamentos de contentor de proteção|
|/ cofres replicationFabrics/replicationProtectionContainers /<br>remover/replicationProtectionContainerMappings/ação|Remova o mapeamento de contentor de proteção|
|/ cofres replicationFabrics/replicationProtectionContainers /<br>replicationProtectionContainerMappings/eliminar|Eliminar quaisquer mapeamentos de contentor de proteção|
|/ cofres replicationFabrics/replicationProtectionContainers /<br>replicationProtectedItems/leitura|Ler os itens protegidos|
|/ cofres replicationFabrics/replicationProtectionContainers /<br>replicationProtectedItems/escrita|Criar ou atualizar quaisquer itens protegidos|
|/ cofres replicationFabrics/replicationProtectionContainers /<br>replicationProtectedItems/eliminar|Eliminar os itens protegidos|
|/ cofres replicationFabrics/replicationProtectionContainers /<br>remover/replicationProtectedItems/ação|Remover o Item protegido|
|/ cofres replicationFabrics/replicationProtectionContainers /<br>plannedFailover/replicationProtectedItems/ação|Ativação pós-falha planeada|
|/ cofres replicationFabrics/replicationProtectionContainers /<br>unplannedFailover/replicationProtectedItems/ação|Ativação pós-falha|
|/ cofres replicationFabrics/replicationProtectionContainers /<br>testFailover/replicationProtectedItems/ação|Ativação pós-falha de teste|
|/ cofres replicationFabrics/replicationProtectionContainers /<br>testFailoverCleanup/replicationProtectedItems/ação|Limpeza da ativação pós-falha de teste|
|/ cofres replicationFabrics/replicationProtectionContainers /<br>failoverCommit/replicationProtectedItems/ação|Consolidação de ativação pós-falha|
|/ cofres replicationFabrics/replicationProtectionContainers /<br>Reproteção/replicationProtectedItems/ação|Proteja o Item protegido|
|/ cofres replicationFabrics/replicationProtectionContainers /<br>updateMobilityService/replicationProtectedItems/ação|Serviço de mobilidade de atualização|
|/ cofres replicationFabrics/replicationProtectionContainers /<br>repairReplication/replicationProtectedItems/ação|Reparar a replicação|
|/ cofres replicationFabrics/replicationProtectionContainers /<br>applyRecoveryPoint/replicationProtectedItems/ação|Aplicar o ponto de recuperação|
|/ cofres replicationFabrics/replicationProtectionContainers /<br>recoveryPoints/replicationProtectedItems/leitura|Pontos de recuperação de replicação de leitura|
|/vaults/replicationPolicies/Read|Ler todas as políticas|
|/vaults/replicationPolicies/Write|Criar ou atualizar as políticas|
|/vaults/replicationPolicies/DELETE|Eliminar todas as políticas|
|/vaults/replicationRecoveryPlans/Read|Leia os planos de recuperação|
|/vaults/replicationRecoveryPlans/Write|Criar ou atualizar quaisquer planos de recuperação|
|/vaults/replicationRecoveryPlans/DELETE|Eliminar quaisquer planos de recuperação|
|/vaults/replicationRecoveryPlans/plannedFailover/Action|Plano de recuperação de ativação pós-falha planeada|
|/vaults/replicationRecoveryPlans/unplannedFailover/Action|Plano de recuperação de ativação pós-falha|
|/vaults/replicationRecoveryPlans/testFailover/Action|Plano de recuperação de ativação pós-falha de teste|
|/vaults/replicationRecoveryPlans/testFailoverCleanup/Action|Plano de recuperação de limpeza da ativação pós-falha de teste|
|/vaults/replicationRecoveryPlans/failoverCommit/Action|Plano de recuperação de consolidação de ativação pós-falha|
|/vaults/replicationRecoveryPlans/reProtect/Action|Proteja o plano de recuperação|
|/ Cofres/extendedInformation/leitura|A operação Obter Informações Detalhadas obtém as Informações Detalhadas de um objeto que representa o tipo de recurso ?vault? do Azure|
|/ Cofres/extendedInformation/escrita|A operação Obter Informações Detalhadas obtém as Informações Detalhadas de um objeto que representa o tipo de recurso ?vault? do Azure|
|/ Cofres/extendedInformation/eliminar|A operação Obter Informações Detalhadas obtém as Informações Detalhadas de um objeto que representa o tipo de recurso ?vault? do Azure|
|/ Cofres/backupManagementMetaData/leitura|Devolve os Metadados da Gestão da Cópia de Segurança do Cofre de Serviços de Recuperação.|
|/ Cofres/backupProtectionContainers/leitura|Devolve todos os contentores que pertence à subscrição|
|/ Cofres/backupFabrics/operationResults/leitura|Devolve o estado da operação|
|/ Cofres/backupFabrics/protectionContainers/leitura|Devolve todas as registado contentores|
|/ Cofres/backupFabrics/protectionContainers /<br>operationResults/leitura|Obtém o resultado da Operação efetuada no Contentor de Proteção.|
|/ Cofres/backupFabrics/protectionContainers /<br>protectedItems/leitura|Devolve detalhes do objecto do Item protegido|
|/ Cofres/backupFabrics/protectionContainers /<br>protectedItems/escrita|Criar um Item protegido cópia de segurança|
|/ Cofres/backupFabrics/protectionContainers /<br>protectedItems/eliminar|Elimina protegidos Item|
|/ Cofres/backupFabrics/protectionContainers /<br>cópia de segurança/protectedItems/ação|Executa a Cópia de Segurança do Item Protegido.|
|/ Cofres/backupFabrics/protectionContainers /<br>operationResults/protectedItems/leitura|Obtém o Resultado da Operação Efetuada nos Itens Protegidos.|
|/ Cofres/backupFabrics/protectionContainers /<br>operationStatus/protectedItems/leitura|Devolve o estado da Operação efetuada nos Itens Protegidos.|
|/ Cofres/backupFabrics/protectionContainers /<br>recoveryPoints/protectedItems/leitura|Obter Pontos de Recuperação de Itens Protegidos.|
|/ Cofres/backupFabrics/protectionContainers /<br>protectedItems/recoveryPoints /<br>acção de restauro /|Restaurar Pontos de Recuperação de Itens Protegidos.|
|/ Cofres/backupFabrics/protectionContainers /<br>protectedItems/recoveryPoints /<br>provisionInstantItemRecovery/ação|Aprovisionar Item instantâneas recuperação para o Item protegido|
|/ Cofres/backupFabrics/protectionContainers /<br>protectedItems/recoveryPoints /<br>revokeInstantItemRecovery/ação|Revogar a recuperação do Item instantâneas para Item protegido|
|/ Cofres/utilizações/leitura|Devolve detalhes de utilização de um Cofre de Serviços de Recuperação.|
|/vaults/usages/Read|Ler as utilizações do Cofre|
|/ Cofres/certificados/escrita|A operação de certificado de recursos de atualização atualiza o certificado da credencial de recursos/cofre.|
|/ Cofres/tokenInfo/leitura|Devolve informações sobre o token para o Cofre de serviços de recuperação.|
|/vaults/replicationAlertSettings/Read|Ler as definições de alertas|
|/vaults/replicationAlertSettings/Write|Criar ou atualizar as definições de alertas|
|/ Cofres/backupOperations/leitura|Devolve a operação de cópia de segurança cofre dos serviços de estado de recuperação.|
|/ Cofres/storageConfig/leitura|Devolve a configuração de armazenamento para a recuperação cofre dos serviços.|
|/ Cofres/storageConfig/escrita|Cofre dos serviços de configuração de armazenamento de atualizações para a recuperação.|
|/ Cofres/backupUsageSummaries/leitura|Devolve resumos de itens protegidos e os servidores protegidos dos serviços de recuperação.|
|/ Cofres/backupProtectedItems/leitura|Devolve a lista de todos os Itens Protegidos.|
|/ Cofres/backupconfig/vaultconfig/leitura|Cofre dos serviços de configuração de devolve para recuperação.|
|/ Cofres/backupconfig/vaultconfig/escrita|Cofre dos serviços de configuração de atualizações para a recuperação.|
|/ Cofres/registeredIdentities/escrita|A operação de registar o contentor de serviço pode ser utilizada para registar um contentor com o serviço de recuperação.|
|/ Cofres/registeredIdentities/leitura|Os contentores obter operação pode ser utilizada obter contentores registados para um recurso.|
|/ Cofres/registeredIdentities/eliminar|A operação de anular o registo do contentor pode ser utilizada para anular o registo de um contentor.|
|/ Cofres/registeredIdentities/operationResults/leitura|A obter os resultados da operação operação pode ser utilizada obter o estado da operação e o resultado para a operação assíncrona foi submetido|
|/vaults/replicationJobs/Read|Ler todas as tarefas|
|/vaults/replicationJobs/Cancel/Action|Cancelar a tarefa|
|/vaults/replicationJobs/Restart/Action|Reinicie a tarefa|
|/vaults/replicationJobs/resume/Action|Retomar a tarefa|
|/ Cofres/backupPolicies/leitura|Devolve todas as políticas de proteção|
|/ Cofres/backupPolicies/escrita|Cria a política de proteção|
|/ Cofres/backupPolicies/eliminar|Eliminar uma política de proteção|
|/ Cofres/backupPolicies/operationResults/leitura|Obter Resultados da Operação de Política.|
|/ Cofres/backupPolicies/operationStatus/leitura|Obter o estado da operação de política.|
|/ Cofres/vaultTokens/leitura|A operação de Token de cofre pode ser utilizada para obter Token do cofre para operações de back-end ao nível do cofre.|
|/ Cofres/monitoringConfigurations/notificationConfiguration/leitura|Obtém a configuração de notificação de Cofre de serviços de recuperação.|
|/ Cofres/backupJobs/leitura|Devolve todos os objetos da tarefa|
|/ Cofres/backupJobs / / ação de cancelamento|Cancelar a tarefa|
|/ Cofres/backupJobs/operationResults/leitura|Devolve o Resultado da Operação de Tarefa.|
|/Locations/allocateStamp/Action|AllocateStamp é uma operação interna utilizada pelo serviço|
|/Locations/allocatedStamp/Read|GetAllocatedStamp é uma operação interna utilizada pelo serviço|

## <a name="microsoftrelay"></a>Microsoft.Relay

| Operação | Descrição |
|---|---|
|checkNamespaceAvailability/ação|Verificações de disponibilidade de espaço de nomes em dada subscrição.|
|registar/ação|Regista a subscrição para o fornecedor de recursos de reencaminhamento e permite a criação dos recursos de reencaminhamento|
|espaços de nomes/escrita|Crie um recurso de espaço de nomes e Atualize as respetivas propriedades. As etiquetas e estado do espaço de nomes são as propriedades que podem ser atualizadas.|
|/Namespaces/Read|Obter a lista de descrição de recursos do espaço de nomes|
|espaços de nomes/eliminar|Eliminar recurso do espaço de nomes|
|/Namespaces/authorizationRules/Write|Criar regras de autorização ao nível de espaço de nomes e Atualize as respetivas propriedades. Os direitos de acesso de regras de autorização, o site primário e as chaves secundárias podem ser atualizadas.|
|/Namespaces/authorizationRules/DELETE|Elimine regra de autorização de espaço de nomes. Não é possível eliminar a regra de autorização de espaço de nomes predefinido. |
|/Namespaces/authorizationRules/listkeys/Action|Obter a cadeia de ligação ao espaço de nomes|
|/Namespaces/HybridConnections/Write|Criar ou atualizar HybridConnection propriedades.|
|/Namespaces/HybridConnections/Read|Obter a lista de descrições de recurso HybridConnection|
|/Namespaces/HybridConnections/DELETE|Operação para eliminar recurso HybridConnection|
|/Namespaces/HybridConnections/authorizationRules/Write|Criar regras de autorização de HybridConnection e Atualize as respetivas propriedades. Os direitos de acesso de regras de autorização, o site primário e as chaves secundárias podem ser atualizadas.|
|/Namespaces/HybridConnections/authorizationRules/DELETE|Operação para eliminar as regras de autorização de HybridConnection|
|/Namespaces/HybridConnections/authorizationRules/listkeys/Action|Obter a cadeia de ligação para HybridConnection|
|/Namespaces/WcfRelays/Write|Criar ou atualizar WcfRelay propriedades.|
|/Namespaces/WcfRelays/Read|Obter a lista de descrições de recurso WcfRelay|
|/Namespaces/WcfRelays/DELETE|Operação para eliminar recurso WcfRelay|
|/Namespaces/WcfRelays/authorizationRules/Write|Criar regras de autorização de WcfRelay e Atualize as respetivas propriedades. Os direitos de acesso de regras de autorização, o site primário e as chaves secundárias podem ser atualizadas.|
|/Namespaces/WcfRelays/authorizationRules/DELETE|Operação para eliminar as regras de autorização de WcfRelay|
|/Namespaces/WcfRelays/authorizationRules/listkeys/Action|Obter a cadeia de ligação para WcfRelay|

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

| Operação | Descrição |
|---|---|
|/ AvailabilityStatuses/leitura|Obtém os Estados de disponibilidade de todos os recursos no âmbito especificado|
|/ AvailabilityStatuses/atual/leitura|Obtém o estado de disponibilidade para o recurso especificado|

## <a name="microsoftresources"></a>Microsoft.Resources

| Operação | Descrição |
|---|---|
|checkResourceName/ação|Verifique o nome do recurso de validade.|
|/providers/Read|Obter a lista de fornecedores.|
|/subscriptions/Read|Obtém a lista de subscrições.|
|/subscriptions/operationresults/Read|Obter a subscrição os resultados da operação.|
|/subscriptions/Providers/Read|Obtém ou lista fornecedores de recursos.|
|/subscriptions/tagNames/Read|Obtém ou lista etiquetas de subscrição.|
|/subscriptions/tagNames/Write|Adiciona uma etiqueta de subscrição.|
|/subscriptions/tagNames/DELETE|Elimina uma etiqueta de subscrição.|
|/subscriptions/tagNames/tagValues/Read|Obtém ou lista valores de etiqueta de subscrição.|
|/subscriptions/tagNames/tagValues/Write|Adiciona um valor de etiqueta de subscrição.|
|/subscriptions/tagNames/tagValues/DELETE|Elimina um valor de etiqueta de subscrição.|
|/subscriptions/resources/Read|Obtém os recursos de uma subscrição.|
|/subscriptions/resourceGroups/Read|Obtém ou lista de grupos de recursos.|
|/subscriptions/resourceGroups/Write|Cria ou atualiza um grupo de recursos.|
|/subscriptions/resourceGroups/DELETE|Elimina um grupo de recursos e todos os respetivos recursos.|
|/subscriptions/resourceGroups/moveResources/Action|Move os recursos de um grupo de recursos para outro.|
|/subscriptions/resourceGroups/validateMoveResources/Action|Valide a movimentação de recursos de um grupo de recursos para outro.|
|/subscriptions/resourcegroups/Resources/Read|Obtém os recursos para o grupo de recursos.|
|/subscriptions/resourcegroups/Deployments/Read|Obtém ou lista de implementações.|
|/subscriptions/resourcegroups/Deployments/Write|Cria ou atualiza uma implementação.|
|/subscriptions/resourcegroups/Deployments/operationstatuses/Read|Obtém ou lista de Estados de operação de implementação.|
|/subscriptions/resourcegroups/Deployments/Operations/Read|Obtém ou lista as operações de implementação.|
|/subscriptions/Locations/Read|Obtém a lista de localizações suportadas.|
|/Links/Read|Obtém ou lista ligações de recursos.|
|ligações/escrita|Cria ou atualiza uma ligação de recursos.|
|/Links/DELETE|Elimina uma ligação de recursos.|
|/Tenants/Read|Obtém a lista de inquilinos.|
|/Resources/Read|Obter a lista de recursos com base em filtros.|
|/Deployments/Read|Obtém ou lista de implementações.|
|implementações/escrita|Cria ou atualiza uma implementação.|
|/Deployments/DELETE|Elimina uma implementação.|
|/Deployments/Cancel/Action|Cancela uma implementação.|
|/Deployments/Validate/Action|Valida uma implementação.|
|/Deployments/Operations/Read|Obtém ou lista as operações de implementação.|

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

| Operação | Descrição |
|---|---|
|/jobcollections/Read|Obter a coleção de tarefas|
|as jobcollections/escrita|Cria ou atualiza a coleção de tarefas.|
|/jobcollections/DELETE|Elimina a coleção de tarefas.|
|/jobcollections/Enable/Action|Permite que a coleção de tarefas.|
|/jobcollections/disable/Action|Desativa a coleção de tarefas.|
|/jobcollections/Jobs/Read|Obtém a tarefa.|
|/jobcollections/Jobs/Write|Cria ou atualiza a tarefa.|
|/jobcollections/Jobs/DELETE|Elimina a tarefa.|
|/jobcollections/Jobs/Run/Action|Tarefa é executada.|
|/jobcollections/Jobs/generateLogicAppDefinition/Action|Gera a definição da aplicação lógica com base numa tarefa do agendador.|
|/jobcollections/Jobs/jobhistories/Read|Obtém o histórico de tarefa.|

## <a name="microsoftsearch"></a>Microsoft.Search

| Operação | Descrição |
|---|---|
|registar/ação|Regista a subscrição para o fornecedor de recursos de pesquisa e permite a criação de serviços de pesquisa.|
|checkNameAvailability/ação|Verifica a disponibilidade do nome do serviço.|
|/ searchServices/escrita|Cria ou atualiza o serviço de pesquisa.|
|/searchServices/Read|Lê o serviço de pesquisa.|
|/searchServices/DELETE|Elimina o serviço de pesquisa.|
|/searchServices/Start/Action|Inicia o serviço de pesquisa.|
|/searchServices/Stop/Action|Para o serviço de pesquisa.|
|/searchServices/listAdminKeys/Action|Lê as chaves de administração.|
|/searchServices/regenerateAdminKey/Action|A chave de administrador de gera de novo.|
|/searchServices/createQueryKey/Action|Cria a chave de consulta.|
|/searchServices/queryKey/Read|Lê as chaves de consulta.|
|/searchServices/queryKey/DELETE|Elimina a chave de consulta.|

## <a name="microsoftsecurity"></a>Microsoft.Security

| Operação | Descrição |
|---|---|
|/jitNetworkAccessPolicies/Read|Obtém as políticas de acesso de rede de just-in-time|
|/ jitNetworkAccessPolicies/escrita|Cria uma nova política de acesso de rede de just-in-time ou atualiza um já existente|
|/jitNetworkAccessPolicies/initiate/Action|Inicia uma política de acesso de rede de just-in-time|
|/securitySolutionsReferenceData/Read|Obtém as soluções de segurança de dados de referência|
|/securityStatuses/Read|Obtém a segurança Estados de funcionamento de recursos do Azure|
|/webApplicationFirewalls/Read|Obtém a web firewalls de aplicação|
|/ webApplicationFirewalls/escrita|Cria uma nova firewall de aplicação web ou atualiza um já existente|
|/webApplicationFirewalls/DELETE|Elimina uma firewall de aplicação web|
|/securitySolutions/Read|Obtém as soluções de segurança|
|/ securitySolutions/escrita|Cria uma nova solução de segurança ou atualiza um já existente|
|/securitySolutions/DELETE|Elimina uma solução de segurança|
|/Tasks/Read|Obtém todas as recomendações de segurança disponíveis|
|/Tasks/dismiss/Action|Ignorar uma recomendação de segurança|
|/Tasks/Activate/Action|Ativar uma recomendação de segurança|
|/Policies/Read|Obtém a política de segurança|
|/ políticas/escrita|Atualiza a política de segurança|
|/applicationWhitelistings/Read|Obtém o whitelistings de aplicação|
|/ applicationWhitelistings/escrita|Cria uma nova aplicação a listas brancas ou atualiza um já existente|

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

| Operação | Descrição |
|---|---|
|subscrições/escrita|Cria ou atualiza uma subscrição|
|/ gateways/escrita|Cria ou atualiza um gateway|
|/gateways/DELETE|Elimina um gateway|
|/gateways/Read|Obtém um gateway|
|/gateways/regenerateprofile/Action|Gera de novo o perfil de gateway|
|/gateways/upgradetolatest/Action|Atualiza o gateway para a versão mais recente|
|nós/escrita|cria ou atualiza um nó|
|/nodes/DELETE|Elimina um nó|
|/nodes/Read|Obtém um nó|
|sessões/escrita|Cria ou atualiza uma sessão|
|/Sessions/Read|Obtém uma sessão|
|/Sessions/DELETE|Elimina um sesssion|

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

| Operação | Descrição |
|---|---|
|checkNameAvailability/ação|Verificações de disponibilidade de espaço de nomes em dada subscrição.|
|registar/ação|Regista a subscrição para o fornecedor de recursos de barramento de serviço e permite a criação dos recursos de barramento de serviço|
|espaços de nomes/escrita|Crie um recurso de espaço de nomes e Atualize as respetivas propriedades. As etiquetas e estado do espaço de nomes são as propriedades que podem ser atualizadas.|
|/Namespaces/Read|Obter a lista de descrição de recursos do espaço de nomes|
|espaços de nomes/eliminar|Eliminar recurso do espaço de nomes|
|/Namespaces/metricDefinitions/Read|Obter a lista de descrições de recursos de métricas de espaço de nomes|
|/Namespaces/authorizationRules/Write|Criar regras de autorização ao nível de espaço de nomes e Atualize as respetivas propriedades. Os direitos de acesso de regras de autorização, o site primário e as chaves secundárias podem ser atualizadas.|
|/Namespaces/authorizationRules/Read|Obter a lista de descrição de regras de autorização de espaços de nomes.|
|/Namespaces/authorizationRules/DELETE|Elimine regra de autorização de espaço de nomes. Não é possível eliminar a regra de autorização de espaço de nomes predefinido. |
|/Namespaces/authorizationRules/listkeys/Action|Obter a cadeia de ligação ao espaço de nomes|
|/Namespaces/authorizationRules/regenerateKeys/Action|Regenerar a chave primária ou secundária para o recurso|
|/Namespaces/diagnosticSettings/Read|Obter a lista de descrições de recursos de definições de diagnóstico de espaço de nomes|
|/Namespaces/diagnosticSettings/Write|Obter a lista de descrições de recursos de definições de diagnóstico de espaço de nomes|
|/Namespaces/Queues/Write|Criar ou as propriedades da fila de atualização.|
|/Namespaces/Queues/Read|Obter a lista de descrições de recursos da fila|
|/Namespaces/Queues/DELETE|Operação para eliminar os recursos da fila|
|/Namespaces/Queues/authorizationRules/Write|Criar regras de autorização de fila e Atualize as respetivas propriedades. Os direitos de acesso de regras de autorização, o site primário e as chaves secundárias podem ser atualizadas.|
|/Namespaces/Queues/authorizationRules/Read| Obter a lista de regras de autorização de fila|
|/Namespaces/Queues/authorizationRules/DELETE|Operação para eliminar as regras de autorização de fila|
|/Namespaces/Queues/authorizationRules/listkeys/Action|Obter a cadeia de ligação para a fila|
|/Namespaces/Queues/authorizationRules/regenerateKeys/Action|Regenerar a chave primária ou secundária para o recurso|
|/Namespaces/logDefinitions/Read|Obter a lista de registos de espaço de nomes descrições de recursos|
|/Namespaces/topics/Write|Criar ou propriedades de tópico de atualização.|
|/Namespaces/topics/Read|Obter a lista de descrições de recursos de tópico|
|/Namespaces/topics/DELETE|Operação para eliminar o recurso de tópico|
|/Namespaces/topics/authorizationRules/Write|Criar regras de autorização de tópico e Atualize as respetivas propriedades. Os direitos de acesso de regras de autorização, o site primário e as chaves secundárias podem ser atualizadas.|
|/Namespaces/topics/authorizationRules/Read| Obter a lista de regras de autorização de tópico|
|/Namespaces/topics/authorizationRules/DELETE|Operação para eliminar as regras de autorização de tópico|
|/Namespaces/topics/authorizationRules/listkeys/Action|Obter a cadeia de ligação de tópico|
|/Namespaces/topics/authorizationRules/regenerateKeys/Action|Regenerar a chave primária ou secundária para o recurso|
|/Namespaces/topics/subscriptions/Write|Criar ou atualizar TopicSubscription propriedades.|
|/Namespaces/topics/subscriptions/Read|Obter a lista de descrições de recurso TopicSubscription|
|/Namespaces/topics/subscriptions/DELETE|Operação para eliminar recurso TopicSubscription|
|/Namespaces/topics/subscriptions/Rules/Write|Criar ou propriedades da regra de atualização.|
|/Namespaces/topics/subscriptions/Rules/Read|Obter a lista de descrições de recurso de regra|
|/Namespaces/topics/subscriptions/Rules/DELETE|Operação para eliminar o recurso de regra|

## <a name="microsoftsql"></a>Microsoft.Sql

| Operação | Descrição |
|---|---|
|/Servers/Read|Devolver uma lista de servidores num grupo de recursos de uma subscrição|
|/ servidores/escrita|Criar um novo servidor ou modificar as propriedades do servidor existente num grupo de recursos de uma subscrição|
|/Servers/DELETE|Eliminar um servidor e incluídas todas as bases de dados e conjuntos elásticos|
|/Servers/Import/Action|Criar uma nova base de dados no servidor e implementar o esquema e dados a partir de um pacote de DacPac|
|/Servers/Upgrade/Action|Ativar novas funcionalidades disponíveis na versão mais recente do servidor e especificar o mapa de conversão de edição de bases de dados|
|/Servers/VulnerabilityAssessmentScans/Action|Executar a análise de servidor avaliação da vulnerabilidade|
|/Servers/operationResults/Read|Operação é utilizada para controlar o progresso da atualização do servidor de versão inferior à superior|
|/Servers/operationResults/DELETE|Abortar a atualização de versão do servidor em curso|
|/Servers/securityAlertPolicies/Read|Obter os detalhes da política de deteção de ameaças de servidor configurada num determinado servidor|
|/Servers/securityAlertPolicies/Write|Alterar a deteção de ameaças de servidor para um determinado servidor|
|/Servers/securityAlertPolicies/operationResults/Read|Obter resultados do servidor de operação de definição de política de deteção de ameaças|
|/Servers/Administrators/Read|Obter os detalhes de administrador do servidor|
|/Servers/Administrators/Write|Criar ou atualizar o administrador do servidor|
|/Servers/Administrators/DELETE|Elimine o administrador do servidor do servidor|
|/Servers/recoverableDatabases/Read|Esta operação é utilizada para recuperação após desastre de base de dados dinâmicos para restaurar a base de dados ao conhecido do último ponto de cópia de segurança boa. Devolve informações sobre a última cópia de segurança boa mas, na verdade, o sistema não restaurar a base de dados.|
|/Servers/serviceObjectives/Read|Obter a lista de serviço objetivos do nível (também conhecido como escalões de desempenho) disponíveis num determinado servidor|
|/Servers/firewallRules/Read|Obter os detalhes de regras de firewall do servidor|
|/Servers/firewallRules/Write|Criar ou atualizar regra de firewall do servidor que controla o intervalo de endereços IP permitido para ligar ao servidor|
|/Servers/firewallRules/DELETE|Eliminar regra de firewall do servidor|
|/Servers/administratorOperationResults/Read|Obter resultados de operação de administrador do servidor|
|/Servers/recommendedElasticPools/Read|Obter a recomendação para conjuntos de bases de dados elásticas reduzir o custo ou melhorar o desempenho com base na utilização de recursos de historica|
|/Servers/recommendedElasticPools/Metrics/Read|Obter métricas para os conjuntos de bases de dados elásticas recomendado para um determinado servidor|
|/Servers/recommendedElasticPools/Databases/Read|Obter bases de dados que devem ser adicionados para conjuntos de bases de dados elásticas para um determinado servidor recomendados.|
|/Servers/elasticPools/Read|Obter os detalhes do agrupamento de base de dados elástica num determinado servidor|
|/Servers/elasticPools/Write|Crie um novo ou alterar as propriedades do conjunto de bases de dados elásticas existente|
|/Servers/elasticPools/DELETE|Eliminar conjunto de bases de dados elásticas existente|
|/Servers/elasticPools/operationResults/Read|Obter detalhes sobre uma operação de agrupamento de base de dados elásticas indicado|
|/Servers/elasticPools/Providers/Microsoft.Insights/<br>metricDefinitions/leitura|Tipos de retorno de métricas que estão disponíveis para conjuntos de bases de dados elásticas|
|/Servers/elasticPools/Providers/Microsoft.Insights/<br>diagnosticSettings/leitura|Obtém a definição de diagnóstico do recurso|
|/Servers/elasticPools/Providers/Microsoft.Insights/<br>diagnosticSettings/escrita|Cria ou atualiza a definição do diagnóstico para o recurso|
|/Servers/elasticPools/Metrics/Read|Devolver métricas de utilização de recursos de agrupamento de base de dados elástica|
|/Servers/elasticPools/elasticPoolDatabaseActivity/Read|Obter as atividades e os detalhes numa determinada base de dados que faz parte do agrupamento de base de dados elástica|
|/Servers/elasticPools/advisors/Read|Devolve a lista de consultores disponíveis para o conjunto elástico|
|/Servers/elasticPools/advisors/Write|Atualização automática-executar o estado de um advisor no nível do conjunto elástico.|
|/Servers/elasticPools/advisors/recommendedActions/Read|Devolve a lista de ações recomendadas do advisor especificado para o conjunto elástico|
|/Servers/elasticPools/advisors/recommendedActions/Write|Aplicar a ação recomendada do conjunto elástico|
|/Servers/elasticPools/elasticPoolActivity/Read|Obter as atividades e detalhes sobre um conjunto de bases de dados elásticas indicado|
|/Servers/elasticPools/Databases/Read|Obter a lista e os detalhes das bases de dados que fazem parte do conjunto de bases de dados elásticas num determinado servidor|
|/Servers/auditingPolicies/Read|Obter os detalhes da tabela de servidor predefinida configurada num determinado servidor de política de auditoria|
|/Servers/auditingPolicies/Write|Alterar a predefinição servidor tabela auditoria para um determinado servidor|
|/Servers/disasterRecoveryConfiguration/operationResults/Read|Obter resultados de operação de configuração de recuperação de desastre|
|/Servers/advisors/Read|Devolve a lista de consultores disponíveis para o servidor|
|/Servers/advisors/Write|As atualizações de autom-executar o estado de um advisor no nível do servidor.|
|/Servers/advisors/recommendedActions/Read|Devolve a lista de ações recomendadas do advisor especificado para o servidor|
|/Servers/advisors/recommendedActions/Write|Aplicar a ação recomendada no servidor|
|/Servers/usages/Read|Devolver a quota DTU do servidor e consuption DTU atual por todas as bases de dados no servidor|
|/Servers/elasticPoolEstimates/Read|Devolve a lista de estimativas de conjunto elástico já criada para este servidor|
|/Servers/elasticPoolEstimates/Write|Cria nova estimativa de conjunto elástico para obter a lista de bases de dados fornecido|
|/Servers/auditingSettings/Read|Obter detalhes sobre a servidor blob política de auditoria configurado num determinado servidor|
|/Servers/auditingSettings/Write|Alterar a auditoria de blob de servidor para um determinado servidor|
|/Servers/auditingSettings/operationResults/Read|Obter o resultado do blob servidor operação de definição de política de auditoria|
|/Servers/backupLongTermRetentionVaults/Read|Esta operação é utilizada para obter um cofre de retenção de cópias de segurança de longo prazo. Devolve informações sobre o Cofre registada para este servidor.|
|/Servers/backupLongTermRetentionVaults/Write|Registe um cofre de retenção de cópias de segurança de longa duração|
|/Servers/restorableDroppedDatabases/Read|Obter uma lista de bases de dados que foram ignorados num determinado servidor que estão ainda na política de retenção. Esta operação devolve uma lista de bases de dados e metadados associados, como data de eliminação.|
|/Servers/Databases/Read|Devolver uma lista de servidores num grupo de recursos de uma subscrição|
|/Servers/Databases/Write|Criar um novo servidor ou modificar as propriedades do servidor existente num grupo de recursos de uma subscrição|
|/Servers/Databases/DELETE|Eliminar um servidor e incluídas todas as bases de dados e conjuntos elásticos|
|/Servers/Databases/Export/Action|Criar uma nova base de dados no servidor e implementar o esquema e dados a partir de um pacote de DacPac|
|/Servers/Databases/VulnerabilityAssessmentScans/Action|Execute uma análise de avaliação da vulnerabilidade da base de dados.|
|/Servers/Databases/pause/Action|Colocar em pausa de uma base de dados de edição de DataWarehouse|
|/Servers/Databases/resume/Action|Retomar uma base de dados de edição de DataWarehouse|
|/Servers/Databases/operationResults/Read|Operação é utilizada para controlar o progresso da operação de base de dados longa, por exemplo, a escala.|
|/Servers/Databases/replicationLinks/Read|Retorno detalhes sobre ligações de replicação estabelecidas para uma determinada base de dados|
|/Servers/Databases/replicationLinks/DELETE|Terminar a relação de replicação forçadamente e com uma potencial perda de dados|
|/Servers/Databases/replicationLinks/unlink/Action|Terminar a relação de replicação forçadamente ou após a sincronização com o parceiro|
|/Servers/Databases/replicationLinks/failover/Action|Ativação pós-falha após a sincronização de todos os é alterado de principal, tornar esta base de dados no site primário a relação de replicação e efetuar remoto primário para uma secundária|
|/Servers/Databases/replicationLinks/forceFailoverAllowDataLoss/Action|Ativação pós-falha imediatamente com potencial perda de dados, tornar esta base de dados no site primário a relação de replicação e efetuar remoto primário para uma secundária|
|/Servers/Databases/replicationLinks/updateReplicationMode/Action|Atualizar o modo de replicação para a ligação para o modo síncrono ou assíncrono|
|/Servers/Databases/replicationLinks/operationResults/Read|Obter o estado das operações de execução longa em ligações de replicação de base de dados|
|/Servers/Databases/dataMaskingPolicies/Read|Obter detalhes sobre os política configurada numa determinada base de dados de máscara de dados|
|/Servers/Databases/dataMaskingPolicies/Write|Alterar a política para uma determinada base de dados de máscara de dados|
|/Servers/Databases/dataMaskingPolicies/Rules/Read|Obter detalhes sobre os regra de política configurada numa base de dados indicado de máscara de dados|
|/Servers/Databases/dataMaskingPolicies/Rules/Write|Alterar a regra de política para uma determinada base de dados de máscara de dados|
|/Servers/Databases/securityAlertPolicies/Read|Obter os detalhes da política de deteção de ameaças configurado numa base de dados indicado|
|/Servers/Databases/securityAlertPolicies/Write|Alterar a política de deteção de ameaças para uma determinada base de dados|
|/Servers/Databases/Providers/Microsoft.Insights/<br>metricDefinitions/leitura|Tipos de retorno de métricas que estão disponíveis para bases de dados|
|/Servers/Databases/Providers/Microsoft.Insights/<br>diagnosticSettings/leitura|Obtém a definição de diagnóstico do recurso|
|/Servers/Databases/Providers/Microsoft.Insights/<br>diagnosticSettings/escrita|Cria ou atualiza a definição do diagnóstico para o recurso|
|/Servers/Databases/Providers/Microsoft.Insights/<br>logDefinitions/leitura|Obtém os registos disponíveis para bases de dados|
|/Servers/Databases/topQueries/Read|Devolve agregar estatísticas de tempo de execução para a consulta selecionada no período de tempo selecionado|
|/Servers/Databases/topQueries/queryText/Read|Devolve o texto de Transact-SQL para o ID de consulta selecionada|
|/Servers/Databases/topQueries/statistics/Read|Devolve agregar estatísticas de tempo de execução para a consulta selecionada no período de tempo selecionado|
|/Servers/Databases/connectionPolicies/Read|Obter os detalhes da política de ligação configurado numa base de dados indicado|
|/Servers/Databases/connectionPolicies/Write|Alterar a política de ligação para uma determinada base de dados|
|/Servers/Databases/Metrics/Read|Devolver métricas de utilização de recursos de base de dados|
|/Servers/Databases/auditRecords/Read|Obter os registos de auditoria de blob de base de dados|
|/Servers/Databases/transparentDataEncryption/Read|Obter o estado e os detalhes da funcionalidade de segurança de encriptação transparente de dados para uma determinada base de dados|
|/Servers/Databases/transparentDataEncryption/Write|Ativar ou desativar a encriptação transparente de dados para uma determinada base de dados|
|/Servers/Databases/transparentDataEncryption/operationResults/Read|Obter o estado e os detalhes da funcionalidade de segurança de encriptação transparente de dados para uma determinada base de dados|
|/Servers/Databases/auditingPolicies/Read|Obter os detalhes da política de auditoria de tabela configurado numa base de dados indicado|
|/Servers/Databases/auditingPolicies/Write|Alterar a política de auditoria de tabela para uma determinada base de dados|
|/Servers/Databases/dataWarehouseQueries/Read|Devolve as informações da consulta de distribuição de armazém de dados para o ID de consulta selecionada|
|/ servidores bases de dados/dataWarehouseQueries /<br>dataWarehouseQuerySteps/leitura|Devolve as informações de passo de consulta distribuída da consulta de armazém de dados para o ID do passo selecionado|
|/Servers/Databases/serviceTierAdvisors/Read|Devolver a sugestão sobre dimensionar a base de dados ou reduzir verticalmente baseadas em estatísticas de execução de consulta para melhorar o desempenho ou reduzir o custo|
|/Servers/Databases/advisors/Read|Devolve a lista de consultores disponíveis para a base de dados|
|/Servers/Databases/advisors/Write|Atualização automática-executar o estado de um advisor no nível de base de dados.|
|/Servers/Databases/advisors/recommendedActions/Read|Devolve a lista de ações recomendadas do advisor especificado para a base de dados|
|/Servers/Databases/advisors/recommendedActions/Write|Aplicar a ação recomendada na base de dados|
|/Servers/Databases/usages/Read|Devolver o tamanho máximo da base de dados que pode ser contactado e o tamanho atual ocupado por dados|
|/Servers/Databases/queryStore/Read|Devolve os valores atuais das definições de arquivo de consultas da base de dados|
|/Servers/Databases/queryStore/Write|Atualizações de definição de arquivo de consultas da base de dados|
|/Servers/Databases/auditingSettings/Read|Obter os detalhes da política de auditoria de blob configurado numa base de dados indicado|
|/Servers/Databases/auditingSettings/Write|Alterar a política de auditoria de blob para uma determinada base de dados|
|/Servers/Databases/schemas/Tables/recommendedIndexes/Read|Obter a lista de recomendações do índice numa base de dados|
|/Servers/Databases/schemas/Tables/recommendedIndexes/Write|Aplicar a recomendação do índice|
|/Servers/Databases/schemas/Tables/columns/Read|Obter a lista de colunas de uma tabela|
|/Servers/Databases/missingindexes/Read|Devolver sugestões sobre índices da base de dados para criar, modificar ou eliminar para melhorar o desempenho das consultas|
|/Servers/Databases/missingindexes/Write|Utilize a recomendação do índice de base de dados numa base de dados específico|
|/Servers/Databases/importExportOperationResults/Read|Devolve detalhes sobre a importação da base de dados ou a operação de exportação de DacPac localizado na conta de armazenamento|
|/Servers/importExportOperationResults/Read|Devolver a lista com detalhes para operações de importação de base de dados da conta de armazenamento num determinado servidor|

## <a name="microsoftstorage"></a>Microsoft.Storage

| Operação | Descrição |
|---|---|
|registar/ação|Regista a subscrição para o fornecedor de recursos de armazenamento e permite a criação de contas do storage.|
|/checknameavailability/Read|Verifica esse nome de conta é válido e não está em utilização.|
|/ storageAccounts/escrita|Cria uma conta de armazenamento com os parâmetros especificados ou atualiza as propriedades ou etiquetas ou adiciona um domínio personalizado para a conta de armazenamento especificada.|
|/storageAccounts/DELETE|Elimina uma conta de armazenamento existente.|
|/storageAccounts/listkeys/Action|Devolve as chaves de acesso para a conta de armazenamento especificada.|
|/storageAccounts/regeneratekey/Action|As chaves de acesso para a conta de armazenamento especificado gera de novo.|
|/storageAccounts/Read|Devolve a lista de armazenamento de contas ou obtém as propriedades para a conta de armazenamento especificada.|
|/storageAccounts/listAccountSas/Action|Devolve o token SAS de conta para a conta de armazenamento especificada.|
|/storageAccounts/listServiceSas/Action|Token SAS do serviço de armazenamento|
|/storageAccounts/Services/diagnosticSettings/Write|Criar/atualizar definições de diagnóstico de conta de armazenamento.|
|/SKUs/Read|Apresenta uma lista de Skus suportados pela Microsoft.|
|/usages/Read|Devolve o limite e a contagem de utilização atual dos recursos na subscrição especificada|
|/Operations/Read|Consulta o estado de uma operação assíncrona.|
|/Locations/deleteVirtualNetworkOrSubnets/Action|Notifica Microsoft que está a ser eliminada uma rede virtual ou de sub-rede|

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

| Operação | Descrição |
|---|---|
|/managers/clearAlerts/Action|Limpe todos os alertas associados com o Gestor de dispositivos.|
|/managers/getActivationKey/Action|Obter a chave de ativação para o Gestor de dispositivos.|
|/managers/regenerateActivationKey/Action|Regenere a chave de ativação para o Gestor de dispositivos.|
|/managers/regenarateRegistationCertificate/Action|Regenere o certificado de registo para os gestores de dispositivos.|
|/managers/getEncryptionKey/Action|Obter a chave de encriptação para o Gestor de dispositivos.|
|/managers/Read|Apresenta uma lista ou obtém os gestores de dispositivos|
|/managers/DELETE|Elimina os gestores de dispositivos|
|gestores/escrita|Criar ou atualizar os gestores de dispositivos|
|/managers/configureDevice/Action|Configura um dispositivo|
|/managers/listActivationKey/Action|Obtém a chave de ativação do Gestor de dispositivos StorSimple.|
|/managers/listPublicEncryptionKey/Action|Listar chaves de encriptação pública de um Gestor de dispositivos do StorSimple.|
|/managers/listPrivateEncryptionKey/Action|Obtém a chave privada de encriptação para um Gestor de dispositivos do StorSimple.|
|/managers/provisionCloudAppliance/Action|Crie uma nova aplicação de nuvem.|
|/ Gestores/escrita|A operação Criar Cofre cria um recurso do tipo "cofre" do Azure|
|/ Gestores/leitura|A operação de introdução cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre'|
|/ Gestores/eliminar|A operação eliminar cofre elimina os recursos do Azure especificado do tipo 'cofre'|
|/managers/storageAccountCredentials/Write|Criar ou atualizar as credenciais da conta de armazenamento|
|/managers/storageAccountCredentials/Read|Apresenta uma lista ou obtém as credenciais da conta de armazenamento|
|/managers/storageAccountCredentials/DELETE|Elimina as credenciais da conta de armazenamento|
|/managers/storageAccountCredentials/listAccessKey/Action|Lista de chaves de acesso das credenciais de conta de armazenamento|
|/managers/accessControlRecords/Read|Apresenta uma lista ou obtém os registos de controlo de acesso|
|/managers/accessControlRecords/Write|Criar ou atualizar os registos de controlo de acesso|
|/managers/accessControlRecords/DELETE|Elimina os registos de controlo de acesso|
|/managers/Metrics/Read|Apresenta uma lista ou obtém as métricas|
|/managers/bandwidthSettings/Read|Lista as definições de largura de banda (apenas 8000 série)|
|/managers/bandwidthSettings/Write|Cria um novo ou atualiza as definições de largura de banda (8000 série apenas)|
|/managers/bandwidthSettings/DELETE|Elimina as definições de largura de banda de um existente (8000 série apenas)|
|/ Gestores/extendedInformation/leitura|A operação Obter Informações Detalhadas obtém as Informações Detalhadas de um objeto que representa o tipo de recurso ?vault? do Azure|
|/ Gestores/extendedInformation/escrita|A operação Obter Informações Detalhadas obtém as Informações Detalhadas de um objeto que representa o tipo de recurso ?vault? do Azure|
|/ Gestores/extendedInformation/eliminar|A operação Obter Informações Detalhadas obtém as Informações Detalhadas de um objeto que representa o tipo de recurso ?vault? do Azure|
|/managers/Alerts/Read|Apresenta uma lista ou obtém os alertas|
|/managers/storageDomains/Read|Apresenta uma lista ou obtém os domínios de armazenamento|
|/managers/storageDomains/Write|Criar ou atualizar os domínios de armazenamento|
|/managers/storageDomains/DELETE|Elimina os domínios de armazenamento|
|/managers/Devices/scanForUpdates/Action|Análise de atualizações de um dispositivo.|
|/managers/Devices/download/Action|Atualizações de transferências para um dispositivo.|
|/managers/Devices/Install/Action|Instale as atualizações num dispositivo.|
|/managers/Devices/Read|Apresenta uma lista ou obtém os dispositivos|
|/managers/Devices/Write|Criar ou atualizar os dispositivos|
|/managers/Devices/DELETE|Elimina os dispositivos|
|/managers/Devices/Deactivate/Action|Desativa um dispositivo.|
|/managers/Devices/publishSupportPackage/Action|Publica o pacote de suporte de um dispositivo para a resolução de problemas de Support da Microsoft.|
|/managers/Devices/failover/Action|Ativação pós-falha do dispositivo.|
|/managers/Devices/sendTestAlertEmail/Action|Envie e-mail de teste de alerta para destinatários de correio eletrónico configurado.|
|/managers/Devices/installUpdates/Action|Instala as atualizações nos dispositivos|
|/managers/Devices/listFailoverSets/Action|Liste os conjuntos de ativação pós-falha de um dispositivo existente.|
|/managers/Devices/listFailoverTargets/Action|Destinos de ativação pós-falha de lista dos dispositivos|
|/managers/Devices/publicEncryptionKey/Action|Chave de encriptação pública da lista do Gestor de dispositivos|
|/ gestores dispositivos/hardwareComponentGroups /<br>Leitura|Lista os grupos de componentes de Hardware|
|/ gestores dispositivos/hardwareComponentGroups /<br>changeControllerPowerState/ação|Alterar o estado de energia do controlador de grupos de componentes de hardware|
|/managers/Devices/Metrics/Read|Apresenta uma lista ou obtém as métricas|
|/managers/Devices/chapSettings/Write|Criar ou atualizar as definições Chap|
|/managers/Devices/chapSettings/Read|Apresenta uma lista ou obtém as definições Chap|
|/managers/Devices/chapSettings/DELETE|Elimina as definições do Chap|
|/managers/Devices/backupScheduleGroups/Read|Apresenta uma lista ou obtém os grupos de agenda de cópia de segurança|
|/managers/Devices/backupScheduleGroups/Write|Criar ou atualizar os grupos de agenda de cópia de segurança|
|/managers/Devices/backupScheduleGroups/DELETE|Elimina os grupos de agenda de cópia de segurança|
|/managers/Devices/updateSummary/Read|Apresenta uma lista ou obtém o resumo de atualização|
|/ gestores dispositivos/migrationSourceConfigurations /<br>Importar/ação|Importar configurações de origem para migração|
|/ gestores dispositivos/migrationSourceConfigurations /<br>startMigrationEstimate/ação|Inicie uma tarefa para estimar a duração do processo de migração.|
|/ gestores dispositivos/migrationSourceConfigurations /<br>startMigration/ação|Começar a utilizar configurações de origem de migração|
|/ gestores dispositivos/migrationSourceConfigurations /<br>confirmMigration/ação|Confirma que uma migração com êxito e a consolidação da mesma.|
|/ gestores dispositivos/migrationSourceConfigurations /<br>fetchMigrationEstimate/ação|Obter o estado da tarefa de migração estimativa.|
|/ gestores dispositivos/migrationSourceConfigurations /<br>fetchMigrationStatus/ação|Obter o estado para a migração.|
|/ gestores dispositivos/migrationSourceConfigurations /<br>fetchConfirmMigrationStatus/ação|Obter o estado de confirmar de migração.|
|/managers/Devices/alertSettings/Read|Apresenta uma lista ou obtém as definições de alerta|
|/managers/Devices/alertSettings/Write|Criar ou atualizar as definições de alerta|
|/managers/Devices/networkSettings/Read|Apresenta uma lista ou obtém as definições de rede|
|/managers/Devices/networkSettings/Write|Cria um novo ou atualiza as definições de rede|
|/managers/Devices/Jobs/Read|Apresenta uma lista ou obtém as tarefas|
|/managers/Devices/Jobs/Cancel/Action|Cancelar uma tarefa em execução|
|/managers/Devices/metricsDefinitions/Read|Apresenta uma lista ou obtém as definições de métricas|
|/managers/Devices/volumeContainers/Write|Cria um novo ou atualiza os contentores de Volume (8000 série apenas)|
|/managers/Devices/volumeContainers/Read|Lista os contentores de Volume (8000 série apenas)|
|/managers/Devices/volumeContainers/DELETE|Elimina um contentores de Volume existente (8000 série apenas)|
|/managers/Devices/volumeContainers/listEncryptionKeys/Action|Lista de chaves de encriptação de contentores de Volume|
|/managers/Devices/volumeContainers/rolloverEncryptionKey/Action|Chaves de encriptação de rollover de contentores de Volume|
|/managers/Devices/volumeContainers/Metrics/Read|Lista as métricas|
|/managers/Devices/volumeContainers/volumes/Read|Lista de Volumes|
|/managers/Devices/volumeContainers/volumes/Write|Cria um novo ou Volumes de atualizações|
|/managers/Devices/volumeContainers/volumes/DELETE|Elimina um Volumes existentes|
|/managers/Devices/volumeContainers/volumes/Metrics/Read|Lista as métricas|
|/managers/Devices/volumeContainers/volumes/metricsDefinitions/Read|Lista as definições de métricas|
|/managers/Devices/volumeContainers/metricsDefinitions/Read|Lista as definições de métricas|
|/managers/Devices/iscsiservers/Read|Apresenta uma lista ou obtém o iSCSI de servidores|
|/managers/Devices/iscsiservers/Write|Criar ou atualizar o iSCSI de servidores|
|/managers/Devices/iscsiservers/DELETE|Elimina o iSCSI de servidores|
|/managers/Devices/iscsiservers/backup/Action|Efetuar cópia de segurança de um servidor de iSCSI.|
|/managers/Devices/iscsiservers/Metrics/Read|Apresenta uma lista ou obtém as métricas|
|/managers/Devices/iscsiservers/Disks/Read|Apresenta uma lista ou obtém os discos|
|/managers/Devices/iscsiservers/Disks/Write|Criar ou atualizar os discos|
|/managers/Devices/iscsiservers/Disks/DELETE|Elimina os discos|
|/managers/Devices/iscsiservers/Disks/Metrics/Read|Apresenta uma lista ou obtém as métricas|
|/managers/Devices/iscsiservers/Disks/metricsDefinitions/Read|Apresenta uma lista ou obtém as definições de métricas|
|/managers/Devices/iscsiservers/metricsDefinitions/Read|Apresenta uma lista ou obtém as definições de métricas|
|/managers/Devices/backups/Read|Apresenta uma lista ou obtém o conjunto de cópia de segurança|
|/managers/Devices/backups/DELETE|Elimina o conjunto de cópia de segurança|
|/managers/Devices/backups/Restore/Action|Restaure todos os volumes a partir de um conjunto de cópia de segurança.|
|/managers/Devices/backups/elements/clone/Action|Clone de uma partilha ou volume com um elemento de cópia de segurança.|
|/managers/Devices/backupPolicies/Write|Cria um novo ou atualizações de políticas de cópia de segurança (8000 série apenas)|
|/managers/Devices/backupPolicies/Read|Políticas de lista a cópia de segurança (8000 série apenas)|
|/managers/Devices/backupPolicies/DELETE|Elimina um políticas de cópia de segurança existente (8000 série apenas)|
|/managers/Devices/backupPolicies/backup/Action|Efetuar uma cópia de segurança manual para criar um pedido cópia de segurança de todos os volumes protegidos pela política.|
|/managers/Devices/backupPolicies/Schedules/Write|Cria um novo ou agendas de atualizações|
|/managers/Devices/backupPolicies/Schedules/Read|Lista as agendas|
|/managers/Devices/backupPolicies/Schedules/DELETE|Elimina um agendas existentes|
|/managers/Devices/securitySettings/Update/Action|Atualize as definições de segurança.|
|/managers/Devices/securitySettings/Read|Lista as definições de segurança|
|/ gestores dispositivos/securitySettings /<br>syncRemoteManagementCertificate/ação|Sincronize o certificado de gestão remota para um dispositivo.|
|/managers/Devices/securitySettings/Write|Cria um novo ou atualiza as definições de segurança|
|/managers/Devices/fileservers/Read|Apresenta uma lista ou obtém os servidores de ficheiros|
|/managers/Devices/fileservers/Write|Criar ou atualizar os servidores de ficheiros|
|/managers/Devices/fileservers/DELETE|Elimina os servidores de ficheiros|
|/managers/Devices/fileservers/backup/Action|Efetuar cópia de segurança de um servidor de ficheiros.|
|/managers/Devices/fileservers/Metrics/Read|Apresenta uma lista ou obtém as métricas|
|/managers/Devices/fileservers/shares/Write|Criar ou atualizar as partilhas|
|/managers/Devices/fileservers/shares/Read|Apresenta uma lista ou obtém as partilhas|
|/managers/Devices/fileservers/shares/DELETE|Elimina as partilhas|
|/managers/Devices/fileservers/shares/Metrics/Read|Apresenta uma lista ou obtém as métricas|
|/managers/Devices/fileservers/shares/metricsDefinitions/Read|Apresenta uma lista ou obtém as definições de métricas|
|/managers/Devices/fileservers/metricsDefinitions/Read|Apresenta uma lista ou obtém as definições de métricas|
|/managers/Devices/timeSettings/Read|Apresenta uma lista ou obtém as definições de tempo|
|/managers/Devices/timeSettings/Write|Cria um novo ou atualiza as definições de hora|
|/ Gestores/certificados/escrita|A operação de certificado de recursos de atualização atualiza o certificado da credencial de recursos/cofre.|
|/managers/cloudApplianceConfigurations/Read|A aplicação de nuvem de lista as configurações suportadas|
|/managers/metricsDefinitions/Read|Apresenta uma lista ou obtém as definições de métricas|
|/managers/encryptionSettings/Read|Apresenta uma lista ou obtém as definições de encriptação|

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

| Operação | Descrição |
|---|---|
|/streamingjobs/Start/Action|Iniciar a tarefa do Stream Analytics|
|/streamingjobs/Stop/Action|Parar a tarefa do Stream Analytics|
|streamingjobs/leitura|Leitura tarefa do Stream Analytics|
|streamingjobs/escrita|Escrever a tarefa do Stream Analytics|
|streamingjobs/eliminar|Eliminar a tarefa do Stream Analytics|
|/streamingjobs/Providers/Microsoft.Insights/metricDefinitions/Read|Obtem as métricas disponíveis para streamingjobs|
|/streamingjobs/Providers/Microsoft.Insights/diagnosticSettings/Read|Definição de diagnóstico de leitura.|
|/streamingjobs/Providers/Microsoft.Insights/diagnosticSettings/Write|Escreva a definição de diagnóstico.|
|/streamingjobs/Providers/Microsoft.Insights/logDefinitions/Read|Obtém os registos disponíveis para streamingjobs|
|/streamingjobs/Transformations/Read|Transformação de tarefa de análise de fluxo de leitura|
|/streamingjobs/Transformations/Write|Escrever transformação de tarefa do Stream Analytics|
|/streamingjobs/Transformations/DELETE|Eliminar a transformação de tarefa do Stream Analytics|
|/streamingjobs/inputs/Read|Entrada de tarefa de análise de fluxo de leitura|
|/streamingjobs/inputs/Write|Escrever a entrada de fluxo de trabalho de análise|
|/streamingjobs/inputs/DELETE|Eliminar a entrada de fluxo de trabalho de análise|
|/streamingjobs/outputs/Read|Resultado da tarefa leitura Stream Analytics|
|/streamingjobs/outputs/Write|Escrever a saída da tarefa do Stream Analytics|
|/streamingjobs/outputs/DELETE|Eliminar o resultado da tarefa do Stream Analytics|

## <a name="microsoftsupport"></a>Microsoft. support

| Operação | Descrição |
|---|---|
|registar/ação|Regista para suportar o fornecedor de recursos|
|/supportTickets/Read|Obtém os detalhes do pedido de suporte (incluindo o estado, gravidade, detalhes de contacto e comunicações) ou obtém a lista de pedidos de suporte nas subscrições.|
|/ supportTickets/escrita|Cria ou atualiza um pedido de suporte. Pode criar um pedido de suporte para técnica, Quotas ou gestão de subscrição de faturação problemas relacionados com. Pode atualizar a gravidade, detalhes de contacto e comunicações para pedidos de suporte existentes.|

## <a name="microsoftweb"></a>Microsoft. Web

| Operação | Descrição |
|---|---|
|ação de anular o registo /|Anular o registo do fornecedor de recursos Microsoft. Web para a subscrição.|
|Validar/ação|Valide.|
|registar/ação|Registe o fornecedor de recursos Microsoft. Web para a subscrição.|
|hostingEnvironments/leitura|Obter as propriedades de um ambiente de serviço de aplicações|
|hostingEnvironments/escrita|Criar um novo ambiente de serviço de aplicações ou Atualize uma existente|
|hostingEnvironments/eliminar|Eliminar um ambiente de serviço de aplicações|
|/hostingEnvironments/reboot/Action|Reinicie todas as máquinas no ambiente de serviço de aplicações|
|/hostingenvironments/resume/Action|Retomar a ambientes de alojamento.|
|/hostingenvironments/suspend/Action|Suspenda a ambientes de alojamento.|
|/hostingenvironments/metricdefinitions/Read|Obter as definições de métrica de ambientes de alojamento.|
|/hostingEnvironments/workerPools/Read|Obter as propriedades de um conjunto de trabalho num ambiente de serviço de aplicações|
|/hostingEnvironments/workerPools/Write|Criar um novo conjunto de trabalho num ambiente de serviço de aplicações ou atualizar um já existente|
|/hostingenvironments/workerpools/metricdefinitions/Read|Obter definições de métrica de Workerpools de ambientes de alojamento.|
|/hostingenvironments/workerpools/Metrics/Read|Obter métricas de Workerpools de ambientes de alojamento.|
|/hostingenvironments/workerpools/SKUs/Read|Obter o alojamento de ambientes Workerpools SKUs.|
|/hostingenvironments/workerpools/usages/Read|Obter o alojamento de ambientes Workerpools utilizações.|
|/hostingenvironments/sites/Read|Obter aplicações Web de ambientes de alojamento.|
|/hostingenvironments/serverfarms/Read|Obter planos de serviço de aplicações de ambientes de alojamento.|
|/hostingenvironments/usages/Read|Obter utilizações de ambientes de alojamento.|
|/hostingenvironments/capacities/Read|Obter as capacidades de ambientes de alojamento.|
|/hostingenvironments/Operations/Read|Obter operações de ambientes de alojamento.|
|/hostingEnvironments/multiRolePools/Read|Obter as propriedades de um conjunto de front-end num ambiente de serviço de aplicações|
|/hostingEnvironments/multiRolePools/Write|Criar um novo conjunto de front-end num ambiente de serviço de aplicações ou atualizar um já existente|
|/hostingenvironments/multirolepools/metricdefinitions/Read|Obter as definições de métrica MultiRole conjuntos de ambientes de alojamento.|
|/hostingenvironments/multirolepools/Metrics/Read|Obter métricas de MultiRole conjuntos de ambientes de alojamento.|
|/hostingenvironments/multirolepools/SKUs/Read|Obter SKUs de MultiRole conjuntos de ambientes de alojamento.|
|/hostingenvironments/multirolepools/usages/Read|Obter utilizações MultiRole conjuntos de ambientes de alojamento.|
|/hostingenvironments/Diagnostics/Read|Obter o diagnóstico de ambientes de alojamento.|
|/publishingusers/Read|Obter a publicação os utilizadores.|
|/ publishingusers/escrita|Atualize a publicação de utilizadores.|
|/checknameavailability/Read|Verifique se o nome do recurso está disponível.|
|geoRegions/leitura|Obter a lista de regiões de Georreplicação.|
|sites/leitura|Obter as propriedades de uma aplicação Web|
|sites/escrita|Criar uma nova aplicação Web ou atualizar um já existente|
|sites/eliminar|Eliminar uma aplicação Web existente|
|/sites/backup/Action|Criar uma nova cópia de segurança de aplicação de web|
|/sites/publishxml/Action|Obtenha o xml do perfil de publicação para uma aplicação Web|
|/sites/publish/Action|Publicar uma aplicação Web|
|/sites/Restart/Action|Reiniciar uma aplicação Web|
|/sites/Start/Action|Iniciar uma aplicação Web|
|/sites/Stop/Action|Parar uma aplicação Web|
|/sites/slotsswap/Action|Trocar ranhuras de implementação de aplicação Web|
|/sites/slotsdiffs/Action|Obter diferenças na configuração de aplicação web e de ranhuras|
|/sites/applySlotConfig/Action|Aplicar a configuração de ranhura de aplicação do web a partir da ranhura de destino para a aplicação web atual|
|/sites/resetSlotConfig/Action|Repor a configuração de aplicação web|
|/sites/Functions/Action|Aplicações Web de funções.|
|/sites/listsyncfunctiontriggerstatus/Action|Lista sincronização função acionar Estado as Web Apps.|
|/sites/networktrace/Action|Aplicações de Web de rastreio de rede.|
|/sites/newpassword/Action|Aplicações newpassword Web.|
|/sites/Sync/Action|Aplicações Web de sincronização.|
|/sites/operationresults/Read|Obter os resultados da operação aplicações Web.|
|/sites/webjobs/Read|Obter WebJobs de aplicações Web.|
|sites/cópia de segurança/leitura|Obter a cópia de segurança do Web Apps.|
|/sites/backup/Write|Atualize a cópia de segurança do Web Apps.|
|/sites/metricdefinitions/Read|Obter definições de métrica de aplicações Web.|
|/sites/Metrics/Read|Obter métricas de aplicações Web.|
|/sites/continuouswebjobs/DELETE|Elimine tarefas de Web contínua de aplicações Web.|
|/sites/continuouswebjobs/Read|Obter as tarefas de Web contínua de aplicações Web.|
|/sites/continuouswebjobs/Start/Action|Inicie tarefas de Web contínua de aplicações Web.|
|/sites/continuouswebjobs/Stop/Action|Pare tarefas de Web contínua de aplicações Web.|
|/sites/domainownershipidentifiers/Read|Obter os identificadores de propriedade de domínio de aplicações Web.|
|/sites/domainownershipidentifiers/Write|Atualize identificadores de propriedade de domínio de aplicações Web.|
|/sites/premieraddons/DELETE|Elimine Addons de Premier de aplicações Web.|
|/sites/premieraddons/Read|Obter Addons de Premier de aplicações Web.|
|/sites/premieraddons/Write|Atualize Addons de Premier de aplicações Web.|
|/sites/triggeredwebjobs/DELETE|Elimine WebJobs accionadas de aplicações Web.|
|/sites/triggeredwebjobs/Read|Obter WebJobs accionadas de aplicações Web.|
|/sites/triggeredwebjobs/Run/Action|Execute Web Apps accionadas WebJobs.|
|/sites/hostnamebindings/DELETE|Elimine enlaces de nome de anfitrião de aplicações Web.|
|/sites/hostnamebindings/Read|Obter os enlaces de nome de anfitrião de aplicações Web.|
|/sites/hostnamebindings/Write|Atualize os enlaces de nome de anfitrião de aplicações Web.|
|/sites/virtualnetworkconnections/DELETE|Elimine ligações de rede Virtual de aplicações Web.|
|/sites/virtualnetworkconnections/Read|Obter ligações de rede Virtual de aplicações Web.|
|/sites/virtualnetworkconnections/Write|Atualize ligações de rede Virtual de aplicações Web.|
|/sites/virtualnetworkconnections/gateways/Read|Obter os Gateways de ligações de rede Virtual de aplicações Web.|
|/sites/virtualnetworkconnections/gateways/Write|Atualize os Gateways de ligações de rede Virtual de aplicações Web.|
|/sites/publishxml/Read|Obterem aplicações Web publicar XML.|
|/sites/hybridconnectionrelays/Read|Obter reencaminhamentos de ligação de híbrida de aplicações Web.|
|/sites/perfcounters/Read|Obter os contadores de desempenho de aplicações Web.|
|/sites/usages/Read|Obter as utilizações de aplicações Web.|
|/sites/slots/Write|Criar uma nova ranhura de aplicação Web ou atualizar um já existente|
|/sites/slots/DELETE|Eliminar uma ranhura de aplicação Web existente|
|/sites/slots/backup/Action|Crie nova cópia de segurança da ranhura de aplicação Web.|
|/sites/slots/publishxml/Action|Obtenha o xml do perfil de publicação para a ranhura de aplicação Web|
|/sites/slots/publish/Action|Publicar uma ranhura de aplicação Web|
|/sites/slots/Restart/Action|Reiniciar uma ranhura de aplicação Web|
|/sites/slots/Start/Action|Iniciar uma ranhura de aplicação Web|
|/sites/slots/Stop/Action|Parar uma ranhura de aplicação Web|
|/sites/slots/slotsswap/Action|Trocar ranhuras de implementação de aplicação Web|
|/sites/slots/slotsdiffs/Action|Obter diferenças na configuração de aplicação web e de ranhuras|
|/sites/slots/applySlotConfig/Action|Aplica a configuração de ranhura de aplicação do web a partir da ranhura de destino para a ranhura atual.|
|/sites/slots/resetSlotConfig/Action|Repor a configuração de ranhura de aplicação web|
|/sites/slots/Read|Obter as propriedades de uma ranhura de implementação de aplicação Web|
|/sites/slots/newpassword/Action|Ranhuras de aplicações newpassword Web.|
|/sites/slots/Sync/Action|Ranhuras de aplicações Web de sincronização.|
|/sites/slots/operationresults/Read|Obter resultados de operação de ranhuras de aplicações Web.|
|/sites/slots/webjobs/Read|Obter WebJobs de ranhuras de aplicações Web.|
|/sites/slots/backup/Write|Atualize a cópia de segurança de ranhuras de aplicações de Web.|
|/sites/slots/metricdefinitions/Read|Obter definições de métrica de ranhuras de aplicações de Web.|
|/sites/slots/Metrics/Read|Obter métricas de ranhuras de aplicações Web.|
|/sites/slots/continuouswebjobs/DELETE|Elimine tarefas de Web contínua de ranhuras de aplicações Web.|
|/sites/slots/continuouswebjobs/Read|Obter tarefas de Web contínua de ranhuras de aplicações Web.|
|/sites/slots/continuouswebjobs/Start/Action|Inicie tarefas de Web contínua de ranhuras de aplicações Web.|
|/sites/slots/continuouswebjobs/Stop/Action|Pare tarefas de Web contínua de ranhuras de aplicações Web.|
|/sites/slots/premieraddons/DELETE|Elimine Addons de Premier de ranhuras de aplicações Web.|
|/sites/slots/premieraddons/Read|Obter Addons de Premier de ranhuras de aplicações Web.|
|/sites/slots/premieraddons/Write|Atualize Addons de Premier de ranhuras de aplicações Web.|
|/sites/slots/triggeredwebjobs/DELETE|Elimine as Web Apps ranhuras accionadas WebJobs.|
|/sites/slots/triggeredwebjobs/Read|Obter Web Apps ranhuras accionadas WebJobs.|
|/sites/slots/triggeredwebjobs/Run/Action|Execute Web Apps ranhuras accionadas WebJobs.|
|/sites/slots/hostnamebindings/DELETE|Elimine enlaces de nome de anfitrião de ranhuras de aplicações Web.|
|/sites/slots/hostnamebindings/Read|Obter os enlaces de nome de anfitrião de ranhuras de aplicações Web.|
|/sites/slots/hostnamebindings/Write|Atualize os enlaces de nome de anfitrião de ranhuras de aplicações Web.|
|/sites/slots/phplogging/Read|Obter Phplogging de ranhuras de aplicações Web.|
|/sites/slots/virtualnetworkconnections/DELETE|Elimine ligações de rede Virtual de ranhuras de aplicações Web.|
|/sites/slots/virtualnetworkconnections/Read|Obter ligações de rede Virtual de ranhuras de aplicações Web.|
|/sites/slots/virtualnetworkconnections/Write|Atualize ligações de rede Virtual de ranhuras de aplicações Web.|
|/sites/slots/virtualnetworkconnections/gateways/Write|Atualize os Gateways de ligações de rede Virtual de ranhuras aplicações Web.|
|/sites/slots/usages/Read|Obter as utilizações de ranhuras de aplicações Web.|
|/sites/slots/hybridconnection/DELETE|Elimine a ligação de híbrida de ranhuras de aplicações Web.|
|/sites/slots/hybridconnection/Read|Obter aplicações da Web ranhuras ligação híbrida.|
|/sites/slots/hybridconnection/Write|Atualize ligação de híbrida de ranhuras de aplicações Web.|
|/sites/slots/config/Read|Obter as definições de configuração da ranhura de aplicação Web|
|/sites/slots/config/List/Action|Lista confidenciais definições de segurança da ranhura de aplicação Web, tais como publicar as credenciais, as definições de aplicação e as cadeias de ligação|
|/sites/slots/config/Write|Atualizar as definições de configuração da ranhura de aplicação Web|
|/sites/slots/config/DELETE|Elimine a configuração de ranhuras de aplicações Web.|
|/sites/slots/instances/Read|Obter instâncias de ranhuras de aplicações Web.|
|/sites/slots/instances/processes/Read|Obter os processos de instâncias de ranhuras de aplicações Web.|
|/sites/slots/instances/Deployments/Read|Obter implementações de instâncias de ranhuras de aplicações Web.|
|/sites/slots/sourcecontrols/Read|Obter as definições de configuração de controlo de origem da ranhura de aplicação Web|
|/sites/slots/sourcecontrols/Write|Atualizar definições de configuração de controlo de origem da ranhura de aplicação Web|
|/sites/slots/sourcecontrols/DELETE|Eliminar definições de configuração de controlo de origem da ranhura de aplicação Web|
|/sites/slots/Restore/Read|Obter o restauro de ranhuras de aplicações Web.|
|/sites/slots/analyzecustomhostname/Read|Introdução a Web Apps ranhuras analisam o nome do anfitrião personalizado.|
|/sites/slots/backups/Read|Obter as propriedades de cópia de segurança dos ranhuras de aplicação um web|
|/sites/slots/backups/List/Action|Cópias de segurança da ranhuras de aplicações do Web de lista.|
|/sites/slots/backups/Restore/Action|Restaure cópias de segurança de ranhuras de aplicações de Web.|
|/sites/slots/Deployments/DELETE|Elimine as implementações de ranhuras de aplicações Web.|
|/sites/slots/Deployments/Read|Obter implementações de ranhuras de aplicações Web.|
|/sites/slots/Deployments/Write|Atualize as implementações de ranhuras de aplicações Web.|
|/sites/slots/Deployments/log/Read|Obter o registo de implementações de ranhuras de aplicações Web.|
|/sites/hybridconnection/DELETE|Elimine a ligação de híbrida de aplicações Web.|
|/sites/hybridconnection/Read|Obter da ligação híbrida de aplicações Web.|
|/sites/hybridconnection/Write|Atualize da ligação híbrida de aplicações Web.|
|/sites/recommendationhistory/Read|Obter o histórico de recomendação de aplicações Web.|
|/sites/recommendations/Read|Obter a lista de recomendações para a aplicação web.|
|/sites/recommendations/disable/Action|Desative as recomendações de aplicações Web.|
|/sites/config/Read|Obter definições de configuração de aplicação Web|
|/sites/config/List/Action|Lista confidenciais definições de segurança da aplicação Web, tais como publicar as credenciais, as definições de aplicação e as cadeias de ligação|
|/sites/config/Write|Atualizar as definições de configuração da aplicação Web|
|/sites/config/DELETE|Elimine a configuração de aplicações Web.|
|/sites/instances/Read|Obter instâncias de aplicações Web.|
|/sites/instances/processes/DELETE|Elimine processos de instâncias de aplicações Web.|
|/sites/instances/processes/Read|Obter os processos de instâncias de aplicações Web.|
|/sites/instances/Deployments/Read|Obter implementações de instâncias de aplicações Web.|
|/sites/sourcecontrols/Read|Obter as definições de configuração de controlo de origem da aplicação Web|
|/sites/sourcecontrols/Write|Atualizar definições de configuração de controlo de origem da aplicação Web|
|/sites/sourcecontrols/DELETE|Eliminar definições de configuração de controlo de origem da aplicação Web|
|/sites/Restore/Read|Obter o restauro de aplicações Web.|
|/sites/analyzecustomhostname/Read|Analise o nome do anfitrião personalizado.|
|/sites/backups/Read|Obter as propriedades de cópia de segurança de uma aplicação web|
|/sites/backups/List/Action|Lista Web Apps as cópias de segurança.|
|/sites/backups/Restore/Action|Restaure cópias de segurança do Web Apps.|
|/sites/snapshots/Read|Obter instantâneos de aplicações Web.|
|/sites/Functions/DELETE|Elimine funções de aplicações Web.|
|/sites/Functions/listsecrets/Action|Lista os segredos Web Apps funções.|
|/sites/Functions/Read|Obter as funções de aplicações Web.|
|/sites/Functions/Write|Atualize funções de aplicações Web.|
|/sites/Deployments/DELETE|Elimine as implementações de aplicações Web.|
|/sites/Deployments/Read|Obter implementações de aplicações Web.|
|/sites/Deployments/Write|Atualize as implementações de aplicações Web.|
|/sites/Deployments/log/Read|Obter o registo de implementações de aplicações Web.|
|/sites/Diagnostics/Read|Obter o diagnóstico de aplicações Web.|
|/sites/Diagnostics/workerprocessrecycle/Read|Obter a Reciclagem de processo de trabalho de diagnóstico de aplicações Web.|
|/sites/Diagnostics/workeravailability/Read|Obter Workeravailability de diagnóstico de aplicações Web.|
|/sites/Diagnostics/runtimeavailability/Read|Obter a disponibilidade de tempo de execução de diagnóstico de aplicações Web.|
|/sites/Diagnostics/cpuanalysis/Read|Obter Cpuanalysis de diagnóstico de aplicações Web.|
|/sites/Diagnostics/servicehealth/Read|Obter o estado de funcionamento de serviço Web diagnóstico de aplicações.|
|/sites/Diagnostics/frebanalysis/Read|Obter a análise FREB de diagnóstico de aplicações Web.|
|/availablestacks/Read|Obter pilhas disponíveis.|
|/isusernameavailable/Read|Verifique se o nome de utilizador está disponível.|
|/Microsoft.Web/apiManagementAccounts/<br>APIs/leitura|Obter a lista de Apis.|
|/Microsoft.Web/apiManagementAccounts/<br>APIs/escrita|Adicionar uma nova Api ou Atualize uma existente.|
|/Microsoft.Web/apiManagementAccounts/<br>APIs/eliminar|Elimine uma Api existente.|
|/Microsoft.Web/apiManagementAccounts/<br>ligações/APIs/leitura|Obter a lista de ligações.|
|/Microsoft.Web/apiManagementAccounts/<br>APIs/ligações/escrita|Guardar uma nova ligação ou atualizar um já existente.|
|/Microsoft.Web/apiManagementAccounts/<br>APIs/ligações/eliminar|Elimine uma ligação existente.|
|/Microsoft.Web/apiManagementAccounts/<br>APIs/ligações/connectionAcls/leitura|Ler ConnectionAcls|
|/Microsoft.Web/apiManagementAccounts/<br>APIs/ligações/connectionAcls/escrita|Adicionar ou atualizar ConnectionAcl|
|/Microsoft.Web/apiManagementAccounts/<br>APIs/ligações/connectionAcls/eliminar|Eliminar ConnectionAcl|
|/Microsoft.Web/apiManagementAccounts/<br>APIs/connectionAcls/leitura|Ler ConnectionAcls para Api|
|/Microsoft.Web/apiManagementAccounts/<br>APIs/apiAcls/leitura|Ler ConnectionAcls|
|/Microsoft.Web/apiManagementAccounts/<br>APIs/apiAcls/escrita|Criar ou atualizar as Acls de Api|
|/Microsoft.Web/apiManagementAccounts/<br>APIs/apiAcls/eliminar|Eliminar a ACL de Api|
|serverfarms/leitura|Obter as propriedades num plano do App Service|
|serverfarms/escrita|Criar um novo plano do App Service ou atualizar um já existente|
|serverfarms/eliminar|Eliminar uma existente plano do App Service|
|/serverfarms/restartSites/Action|Reiniciar todas as aplicações Web dentro de um plano do App Service|
|/serverfarms/operationresults/Read|Obter os resultados da operação planos do serviço de aplicações.|
|/serverfarms/Capabilities/Read|Obter capacidades de planos do App Service.|
|/serverfarms/metricdefinitions/Read|Obter as definições de métrica de planos do serviço de aplicações.|
|/serverfarms/Metrics/Read|Obter métricas de planos de serviço de aplicações.|
|/serverfarms/hybridconnectionplanlimits/Read|Obter os limites de plano de ligação do serviço de aplicações planos híbrida.|
|/serverfarms/virtualnetworkconnections/Read|Obter as ligações de rede Virtual de planos de serviço de aplicações.|
|/serverfarms/virtualnetworkconnections/routes/DELETE|Elimine rotas de ligações de rede Virtual de planos de serviço de aplicações.|
|/serverfarms/virtualnetworkconnections/routes/Read|Obter rotas de ligações de rede Virtual de planos de serviço de aplicações.|
|/serverfarms/virtualnetworkconnections/routes/Write|Atualize rotas de ligações de rede Virtual de planos de serviço de aplicações.|
|/serverfarms/virtualnetworkconnections/gateways/Write|Atualize os Gateways de ligações de rede Virtual de planos de serviço de aplicações.|
|/serverfarms/firstpartyapps/Settings/DELETE|Elimine o serviço de aplicações planos primeiro intervenientes as definições de aplicações.|
|/serverfarms/firstpartyapps/Settings/Read|Obter definições de aplicações intervenientes primeiro planos do serviço de aplicações.|
|/serverfarms/firstpartyapps/Settings/Write|Atualize definições de aplicações da primeira planos do serviço de aplicações.|
|/serverfarms/sites/Read|Obter aplicações de Web de planos do App Service.|
|/serverfarms/Workers/reboot/Action|Reinicie trabalhadores de planos de serviço de aplicações.|
|/serverfarms/hybridconnectionrelays/Read|Obter planos de serviço de aplicações híbridas reencaminhamentos de ligação.|
|/serverfarms/SKUs/Read|Obter SKUs de planos de serviço de aplicações.|
|/serverfarms/usages/Read|Obter utilizações de planos de serviço de aplicações.|
|/serverfarms/hybridconnectionnamespaces/relays/sites/Read|Obter planos do serviço de aplicações híbridas ligação espaços de nomes reencaminhamentos da aplicações Web.|
|/ishostnameavailable/Read|Verifique se o nome do anfitrião está disponível.|
|connectionGateways/leitura|Obter a lista de Gateways de ligação.|
|connectionGateways/escrita|Cria ou atualiza um Gateway de ligação.|
|connectionGateways/eliminar|Elimina um Gateway de ligação.|
|/connectionGateways/JOIN/Action|Associa um Gateway de ligação.|
|/classicmobileservices/Read|Obter clássicos Mobile Services.|
|/SKUs/Read|Obter SKUs.|
|certificados/leitura|Obter a lista de certificados.|
|certificados/escrita|Adicionar um certificado novo ou atualizar um já existente.|
|certificados/eliminar|Elimine um certificado existente.|
|/Operations/Read|Obter operações.|
|recomendações/leitura|Obter a lista de recomendações para subscrições.|
|/ishostingenvironmentnameavailable/Read|Get se o nome do ambiente de alojamento está disponível.|
|apiManagementAccounts/leitura|Obter a lista de ApiManagementAccounts.|
|apiManagementAccounts/escrita|Adicionar um novo ApiManagementAccount ou atualizar um já existente|
|apiManagementAccounts/eliminar|Eliminar um ApiManagementAccount existente|
|/apiManagementAccounts/connectionAcls/Read|Obter a lista de ACL de ligação.|
|/apiManagementAccounts/apiAcls/Read|Ler ConnectionAcls|
|ligações/leitura|Obter a lista de ligações.|
|ligações/escrita|Cria ou atualiza uma ligação.|
|ligações/eliminar|Elimina uma ligação.|
|/Connections/JOIN/Action|Associa uma ligação.|
|/Connections/confirmconsentcode/Action|Confirme o código de autorização de ligações.|
|/Connections/listconsentlinks/Action|Ligações de consentimento de lista de ligações.|
|/deploymentlocations/Read|Obter as localizações de implementação.|
|/sourcecontrols/Read|Obter controlos de origem.|
|/ sourcecontrols/escrita|Controlos de origem de atualização.|
|/managedhostingenvironments/Read|Obter geridos ambientes de alojamento.|
|/managedhostingenvironments/sites/Read|Obter geridos aplicações Web de ambientes de alojamento.|
|/managedhostingenvironments/serverfarms/Read|Obter geridos planos de serviço de aplicações de ambientes de alojamento.|
|/Locations/managedapis/Read|Obter as localizações de APIs geridas.|
|/Locations/apioperations/Read|Obter operações de API de localizações.|
|/Locations/connectiongatewayinstallations/Read|Obter instalações de Gateway de ligação de localizações.|
|listSitesAssignedToHostName/leitura|Obter os nomes dos sites atribuídos ao nome do anfitrião.|

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar uma função personalizada](role-based-access-control-custom-roles.md).

- Reveja o [RBAC funções incorporadas](role-based-access-built-in-roles.md).

- Saiba como gerir atribuições de acesso [por utilizador](role-based-access-control-manage-assignments.md) ou [por recurso](role-based-access-control-configure.md) 
