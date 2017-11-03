---
title: "Introdução à segurança com a monitorização do Azure, funções e permissões | Microsoft Docs"
description: "Saiba como utilizar funções incorporadas e permissões do Monitor do Azure para restringir o acesso a recursos de monitorização."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 2686e53b-72f0-4312-bcd3-3dc1b4a9b912
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: johnkem
ms.openlocfilehash: f8767073bb7a6723088bb2727346d23ec8872cd1
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="get-started-with-roles-permissions-and-security-with-azure-monitor"></a>Introdução à segurança com a monitorização do Azure, funções e permissões
Várias equipas necessário estritamente regular o acesso aos dados e definições de monitorização. Por exemplo, se tiver os membros da equipa que trabalham exclusivamente no monitorização (engenheiros de suporte, os engenheiros de devops) ou se utilizar um fornecedor de serviço geridas, poderá querer conceder-lhes acesso a dados de monitorização apenas ao restringir a capacidade de criar, modificar ou eliminar recursos. Este artigo mostra como rapidamente aplicam-se uma função RBAC monitorização incorporada a um utilizador no Azure ou criar a sua própria função personalizada para um utilizador que tem permissões de monitorização limitadas. Em seguida, explica como considerações de segurança para os recursos relacionados com o Monitor do Azure e como pode limitar o acesso a dados que contêm.

## <a name="built-in-monitoring-roles"></a>Monitorização de funções incorporadas
Funções incorporadas do Monitor do Azure foram concebidas para ajudar a limitar o acesso a recursos numa subscrição permitindo os responsáveis pela monitorização de infraestrutura para obter e configurar os dados que precisam. Monitor do Azure fornece duas funções out of box: A monitorização leitor e contribuinte de monitorização.

### <a name="monitoring-reader"></a>Leitor de monitorização
Pessoas atribuídas a função de leitor de monitorização podem ver todos os dados de monitorização de uma subscrição, mas não é possível modificar qualquer recurso ou editar as definições relacionadas com a monitorização de recursos. Esta função é adequada para os utilizadores numa organização, tais como engenheiros de suporte ou operações que necessitam de ser capaz de:

* Ver os dashboards de monitorização no portal e criar os seus próprios dashboards de monitorização privadas.
* Consulta para a utilização de métricas de [API REST da Azure Monitor](https://msdn.microsoft.com/library/azure/dn931930.aspx), [cmdlets do PowerShell](insights-powershell-samples.md), ou [CLI de várias plataformas](insights-cli-samples.md).
* Consulta o registo de atividade com o portal, API de REST de Monitor do Azure, os cmdlets do PowerShell ou CLI de várias plataformas.
* Ver o [definições de diagnóstico](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) para um recurso.
* Ver o [registo perfil](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile) para uma subscrição.
* Ver definições de dimensionamento automático.
* Atividade de alerta de vista e as definições.
* Aceder a dados do Application Insights e ver dados AI análise.
* Procure dados de área de trabalho de análise de registos, incluindo dados de utilização para a área de trabalho.
* Ver grupos de gestão de análise de registos.
* Obter o esquema de pesquisa de análise de registos.
* Lista de pacotes de intelligence de análise de registos.
* Obter e executar a análise de registos pesquisas guardadas.
* Obter a configuração de armazenamento da análise de registos.

> [!NOTE]
> Esta função não conceda acesso de leitura aos dados de registo que foi transmitidos para um hub de eventos ou armazenados numa conta do storage. [Consulte abaixo](#security-considerations-for-monitoring-data) para obter informações sobre como configurar o acesso a estes recursos.
> 
> 

### <a name="monitoring-contributor"></a>Monitorização contribuinte
As pessoas atribuídas a função de contribuinte de monitorização podem ver todos os dados de monitorização de uma subscrição e criar ou modificar as definições de monitorização, mas não é possível modificar quaisquer outros recursos. Esta função é um superconjunto da função de leitor de monitorização e é adequada para os membros da equipa de monitorização ou fornecedores de serviço gerida que, além das permissões acima, também tem de conseguir da organização:

* Publica os dashboards de monitorização como um dashboard partilhado.
* Definir [definições de diagnóstico](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) para um resource.*
* Definir o [registo perfil](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile) para um subscription.*
* Defina a atividade de alerta e as definições.
* Crie testes web do Application Insights e componentes.
* Área de trabalho de análise de registos de lista de chaves partilhadas.
* Ativar ou desativar a análise de registos intelligence pacotes.
* Criar e eliminar e executar a análise de registos pesquisas guardadas.
* Criar e eliminar a configuração de armazenamento da análise de registos.

* tem também separadamente ser concedido ao utilizador permissão ListKeys no recurso de destino (armazenamento conta ou event hub espaço de nomes) para definir um perfil de registo ou a definição de diagnóstico.

> [!NOTE]
> Esta função não conceda acesso de leitura aos dados de registo que foi transmitidos para um hub de eventos ou armazenados numa conta do storage. [Consulte abaixo](#security-considerations-for-monitoring-data) para obter informações sobre como configurar o acesso a estes recursos.
> 
> 

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>As permissões e funções do RBAC personalizadas de monitorização
Se as funções incorporadas acima não satisfazem as necessidades exatas da sua equipa, pode [criar uma função personalizada do RBAC](../active-directory/role-based-access-control-custom-roles.md) com permissões mais granulares. Seguem-se as operações comuns do RBAC de Monitor do Azure com as suas descrições.

| Operação | Descrição |
| --- | --- |
| Microsoft.Insights/ActionGroups/[Read, escrita, eliminar] |Grupos de ação de escrita/leitura/eliminar. |
| Microsoft.Insights/ActivityLogAlerts/[Read, escrita, eliminar] |Alerta de registo de atividade de leitura/escrita/eliminar. |
| Microsoft.Insights/AlertRules/[Read, escrita, eliminar] |Regras de alertas de leitura/escrita/eliminar (alertas métricas). |
| Microsoft.Insights/AlertRules/Incidents/Read |Lista de incidentes (histórico de regra de alerta que está a ser acionado) para regras de alertas. Isto aplica-se apenas para o portal. |
| Microsoft.Insights/AutoscaleSettings/[Read, escrita, eliminar] |Definições de dimensionamento automático de leitura/escrita/eliminar. |
| Microsoft.Insights/DiagnosticSettings/[Read, escrita, eliminar] |Definições de diagnóstico de leitura/escrita/eliminar. |
| Microsoft.Insights/EventCategories/Read |Enumerar todas as categorias de possíveis no registo de atividade. Utilizada pelo Portal do Azure. |
| Microsoft.Insights/eventtypes/digestevents/Read |Esta permissão é necessária para os utilizadores que necessitam de aceder a registos de atividade através do portal. |
| Microsoft.Insights/eventtypes/values/Read |Lista de eventos de registo de atividade (eventos de gestão) numa subscrição. Esta permissão é aplicável ao acesso programático e portal de registo de atividade. |
| Microsoft.Insights/ExtendedDiagnosticSettings/[Read, escrita, eliminar] | Definições de diagnóstico de leitura/escrita/eliminar registos de fluxo de rede. |
| Microsoft.Insights/LogDefinitions/Read |Esta permissão é necessária para os utilizadores que necessitam de aceder a registos de atividade através do portal. |
| Microsoft.Insights/LogProfiles/[Read, escrita, eliminar] |Perfis de registo de leitura/escrita/eliminar (transmissão em fluxo de registo de atividade para a conta de armazenamento ou hub de eventos). |
| Microsoft.Insights/MetricAlerts/[Read, escrita, eliminar] |Leitura/escrita/eliminar quase em tempo real alertas métricas (pré-visualização pública). |
| Microsoft.Insights/MetricDefinitions/Read |Ler as definições de métrica (lista de tipos de métricas disponíveis para um recurso). |
| Microsoft.Insights/Metrics/Read |Ler métricas para um recurso. |
| Microsoft.Insights/Register/Action |Registe o fornecedor de recursos de Monitor do Azure. |


> [!NOTE]
> Aceder a alertas, definições de diagnóstico e métricas para um recurso requer que o utilizador tem acesso de leitura para o tipo de recurso e o âmbito desse recurso. Criar ("escrita") um perfil de definição ou o registo de diagnóstico que arquiva para uma conta de armazenamento ou fluxos para os event hubs exige que o utilizador também ter permissão de ListKeys no recurso de destino.
> 
> 

Por exemplo, utilizando a tabela acima, pode criar uma função RBAC personalizada para um "leitor do registo de atividade" como esta:

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzureRmRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Considerações de segurança para dados de monitorização
Dados de monitorização — particularmente ficheiros de registo — pode conter informações confidenciais, tais como endereços IP ou nomes de utilizador. Dados de monitorização do Azure é fornecido em três formulários básicos:

1. O registo de atividade, que descreve a todas as ações de controlo plane na sua subscrição do Azure.
2. Registos de diagnóstico, que são emitidos por um recurso de registos.
3. Métricas que são emitidas pelo recursos.

Todas as três dos seguintes tipos de dados podem ser armazenadas numa conta de armazenamento ou transmissão em fluxo para o Hub de eventos, que são recursos do Azure para fins gerais. Uma vez que estes são os recursos para fins gerais, criar, eliminar e aceder às mesmas são uma operação com privilégios normalmente reservada para um administrador. Sugerimos que utilize as seguintes práticas para recursos relacionados com a monitorização para impedir a utilização indevida:

* Utilize uma conta de armazenamento dedicada único para os dados de monitorização. Se precisar de separar os dados de monitorização em várias contas do storage, nunca partilhar a utilização de uma conta de armazenamento entre monitorização e não-monitorização de dados, como esta inadvertidamente podem dar os utilizadores que apenas necessitam de aceder a dados (ex de monitorização. um SIEM de terceiros) acesso a monitorização não dados.
* Utilize um único, dedicado barramento de serviço ou o Hub de eventos espaço de nomes em todas as definições de diagnóstico para a mesma razão, conforme apresentado acima.
* Limitar o acesso a contas de armazenamento relacionadas com a monitorização ou event hubs ao mantê-los num grupo de recursos separado, e [utilizar âmbito](../active-directory/role-based-access-control-what-is.md#basics-of-access-management-in-azure) nas funções de monitorização para limitar o acesso a esse grupo de recursos.
* Nunca conceda a permissão de ListKeys para contas de armazenamento ou os hubs de eventos no âmbito da subscrição quando um utilizador necessita apenas de acesso a dados de monitorização. Em vez disso, atribuir estas permissões ao utilizador a um recurso ou grupo de recursos (se tiver um grupo de recursos dedicado monitorização) âmbito.

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Limitar o acesso a contas de armazenamento relacionadas com a monitorização
Quando um utilizador ou aplicação precisa de aceder a dados numa conta de armazenamento de monitorização, deve [gerar um SAS de conta](https://msdn.microsoft.com/library/azure/mt584140.aspx) na conta de armazenamento que contém dados de monitorização com acesso só de leitura do nível de serviço para o blob storage. No PowerShell, este poderá ter o seguinte aspeto:

```powershell
$context = New-AzureStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzureStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Em seguida, pode dar o token para a entidade que tem de ler a partir desse armazenamento conta e pode listar e ler a partir de todos os blobs nessa conta de armazenamento.

Em alternativa, se precisar de controlar esta permissão com RBAC, pode conceder essa entidade as permissões Microsoft.Storage/storageAccounts/listkeys/action essa conta de armazenamento específico. Isto é necessário para os utilizadores que têm de ser capaz de configurar uma definição de diagnóstico ou iniciar sessão perfil para arquivar uma conta de armazenamento. Por exemplo, pode criar a seguinte função do RBAC personalizada para um utilizador ou aplicação que necessita apenas de leitura a partir de uma conta de armazenamento:

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzureRmRoleDefinition -Role $role 
```

> [!WARNING]
> A permissão de ListKeys permite ao utilizador listar as chaves de conta de armazenamento primário e secundário. Estas chaves conceder ao utilizador todas assinadas permissões (ler, escrever, blobs de criar, eliminar blobs, etc.) em todas assinadas serviços (blob, fila, tabela, o ficheiro) nessa conta de armazenamento. Recomendamos que utilize um SAS de conta descrito acima, sempre que possível.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Limitar o acesso aos hubs de eventos relacionados com a monitorização
Um padrão semelhante pode ser seguido com os event hubs, mas tem primeiro de criar uma regra de autorização de escutar dedicada. Se pretende conceder acesso a uma aplicação que necessita apenas para escutar os hubs de eventos relacionados com a monitorização, efetue o seguinte:

1. Crie uma política de acesso partilhado no hub(s) eventos que foram criadas para dados de monitorização com apenas afirmações de escuta de transmissão em fluxo. Pode fazê-lo no portal. Por exemplo, pode chamar-"monitoringReadOnly." Se for possível, irá querer dê essa chave diretamente para o consumidor e ignore o passo seguinte.
2. Se o consumidor tem de ser capaz de obter a chave ad-hoc, conceda ao utilizador a ação de ListKeys para esse hub de eventos. Isto também é necessário para os utilizadores que têm de ser capaz de configurar uma definição de diagnóstico ou iniciar sessão perfil fluxo para os event hubs. Por exemplo, poderá criar uma regra RBAC:
   
   ```powershell
   $role = Get-AzureRmRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzureRmRoleDefinition -Role $role 
   ```

## <a name="next-steps"></a>Passos seguintes
* [Leia sobre RBAC e permissões no Gestor de recursos](../active-directory/role-based-access-control-what-is.md)
* [Leia a descrição geral da monitorização no Azure](monitoring-overview.md)

