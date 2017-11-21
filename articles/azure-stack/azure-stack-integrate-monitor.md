---
title: "Integrar a solução de monitorização externa com pilha do Azure | Microsoft Docs"
description: "Saiba como integrar a pilha do Azure com uma solução de monitorização externa no seu centro de dados."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: twooley
ms.openlocfilehash: 801e6be91f2b568ce51ec632bccef06a57d809f1
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Integrar a solução de monitorização externa com pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas*

Para externo monitorização da infraestrutura de pilha do Azure, terá de monitorizar o software de pilha do Azure, os computadores físicos e os comutadores de rede física. Cada uma destas áreas oferece um método para obter informações de estado de funcionamento e o alerta.

- Software de pilha do Azure oferece uma API baseada em REST para obter o estado de funcionamento e alertas. (Com a utilização de tecnologias definidas por software como espaços de armazenamento direto, alertas e o estado de funcionamento de armazenamento fazem parte do software de monitorização.)
- Computadores físicos podem disponibilizar estado de funcionamento e alertas informações através de controladores de gestão de placas base (BMCs).
- Dispositivos de rede físicos podem disponibilizar informações de alerta e de estado de funcionamento através do protocolo SNMP.

Cada solução de pilha do Azure é fornecido com um anfitrião do ciclo de vida de hardware. Este anfitrião é executado o software de monitorização do fornecedor de hardware de fabricante de equipamento Original (OEM) para os servidores físicos e os dispositivos de rede. Se assim o desejar, pode ignorar estas soluções de monitorização e diretamente integrar com soluções de monitorização existentes no seu centro de dados.

> [!IMPORTANT]
> A solução de monitorização externa que utilizar tem de ser sem agente. Não é possível instalar agentes de terceiros dentro de componentes de pilha do Azure.

O diagrama seguinte mostra o fluxo de tráfego entre um sistema de pilha do Azure integrado, o anfitrião de ciclo de vida de hardware, uma solução de monitorização externa e um sistema de coleção de emissão de permissões/dados externos.

![Diagrama que mostra o tráfego entre a pilha do Azure, monitorização e relatórios de solução.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

Este artigo explica como integrar a pilha do Azure com soluções de monitorização externas, como o System Center Operations Manager e da Nagios. Também inclui como trabalhar com alertas através de programação utilizando o PowerShell ou através de chamadas da REST API.

## <a name="integrate-with-operations-manager"></a>Integrar com o Operations Manager

Pode utilizar o Gestor de operações para monitorização externo da pilha do Azure. O pacote de gestão do System Center para a pilha do Microsoft Azure permite-lhe monitorizar múltiplas implementações de pilha do Azure com uma única instância do Operations Manager. O pacote de gestão utiliza o fornecedor de recursos de estado de funcionamento e o fornecedor de recursos de atualização REST APIs para comunicar com a pilha do Azure. Se pretender ignorar OEM monitorização software que está a ser executado no anfitrião de ciclo de vida de hardware, pode instalar pacotes de gestão do fornecedor para monitorizar os servidores físicos. Também pode utilizar a deteção de dispositivos de rede do Operations Manager para monitorizar os comutadores de rede.

O pacote de gestão para a pilha do Azure fornece as seguintes capacidades:

- Pode gerir várias implementações de pilha do Azure.
- Não há suporte para o Azure Active Directory (Azure AD) e serviços de Federação do Active Directory (AD FS).
- Pode obter e fechar alertas.
- Não há um Estado de funcionamento e um dashboard de capacidade.
- Inclui a deteção de modo de manutenção automática para quando o patch e atualização (P & U) está em curso.
- Inclui tarefas de atualização de imposição para implementação e a região.
- Pode adicionar informações personalizadas para uma região.
- Suporta notificações e relatórios.

Pode transferir o pacote de gestão do System Center para a pilha do Microsoft Azure e o Guia do utilizador associada [aqui](https://www.microsoft.com/en-us/download/details.aspx?id=55184), ou diretamente a partir do Operations Manager.

Para uma solução de emissão de permissões, pode integrar o Operations Manager com o System Center Service Manager. O conector de produto integrada permite a comunicação bidirecional, que permite-lhe fechar um alerta na pilha do Azure e o Operations Manager depois de resolver um pedido de serviço no Service Manager.

O diagrama seguinte mostra a integração da pilha do Azure com uma implementação existente do System Center. Pode automatizar o Service Manager adicional com o System Center Orchestrator ou o Service Management Automation (SMA) para executar operações na pilha do Azure.

![Diagrama que mostra a integração com OM, o Service Manager e o SMA.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Integrar com da Nagios

Um Nagios Plug-in de monitorização foi desenvolvido em conjunto com soluções de Cloudbase parceiros, que está disponível com a licença de software gratuito permissiva – MIT (Massachusetts Institute of Technology).

O plug-in é escrito no Python e tira partido do fornecedor de recursos de estado de funcionamento REST API. Oferece funcionalidades básicas para obter e fechar alertas na pilha do Azure. Como o pacote de gestão do System Center, permite-lhe para adicionar múltiplas implementações de pilha do Azure e enviar notificações.

O plug-in funciona com Enterprise da Nagios e da Nagios Core. Poderá transferi-lo [aqui](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). O site de transferência inclui também detalhes da instalação e configuração.

### <a name="plugin-parameters"></a>Parâmetros de plug-in

Configure o ficheiro de plug-in "Azurestack_plugin.py" com os seguintes parâmetros:

| Parâmetro | Descrição | Exemplo |
|---------|---------|---------|
| *arm_endpoint* | Ponto final do Gestor de recursos (administrador) do Azure |https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Ponto final do Gestor de recursos (administrador) do Azure  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | ID de subscrição de Admin | Obter através do portal de administrador ou o PowerShell |
| *User_name* | O nome de utilizador do operador subscrição | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | Palavra-passe de subscrição de operador | mypassword |
| *Client_id* | Cliente | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417 * |
| *região* |  Nome de região de pilha do Azure | local |
|  |  |

* O GUID do PowerShell que é fornecida é universal. Pode utilizá-la para cada implementação.

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Utilizar o PowerShell para monitorizar estado de funcionamento e alertas

Se não estiver a utilizar o Operations Manager, Nagios ou uma solução baseada em da Nagios, pode utilizar o PowerShell para ativar uma vasta gama de monitorização de soluções para integrar com a pilha do Azure.
 
1. Para utilizar o PowerShell, certifique-se de que tem [PowerShell instalada e configurada](azure-stack-powershell-configure-quickstart.md) para um ambiente de operador de pilha do Azure. Instale o PowerShell num computador local que pode alcançar o ponto de final do Gestor de recursos (administrador) (https://adminmanagement. [ Região]. [External_FQDN]).

2. Execute os seguintes comandos para estabelecer ligação ao ambiente de pilha do Azure como um operador de pilha do Azure:

   ```PowerShell
   Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN]

   Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```
3. Mude para o diretório onde instalou o [ferramentas Azure pilha](https://github.com/Azure/AzureStack-Tools) como parte da instalação do PowerShell, por exemplo, c:\azurestack-tools-master. Em seguida, mude para o diretório de infraestrutura e execute o seguinte comando para importar o módulo de infraestrutura:

   ```PowerShell
   Import-Module .\AzureStack.Infra.psm1
    ```
4. Utilizar comandos, como os exemplos seguintes para trabalhar com alertas:
   ```PowerShell
   #Retrieve all alerts
   Get-AzsAlert -location [Region]

   #Filter for active alerts
   $Active=Get-AzsAlert -location [Region] | Where {$_.State -eq "active"}
   $Active

   #Close alert
   Close-AzsAlert -location [Region] -AlertID "ID"

   #Retrieve resource provider health
   Get-AzsResourceProviderHealths -location [Region]

   #Retrieve infrastructure role instance health
   Get-AzsInfrastructureRoleHealths -location [Region]
   ```

## <a name="use-the-rest-api-to-monitor-health-and-alerts"></a>Utilizar a API REST para monitorizar estado de funcionamento e alertas

Pode utilizar chamadas da REST API para obter os alertas, fechar alertas e obter o estado de funcionamento de fornecedores de recursos.

### <a name="get-alert"></a>Obter o alerta

**Pedido**

O pedido obtém todos os alertas fechados e Active Directory para a subscrição do fornecedor predefinido. Não há nenhum corpo do pedido.


|Método  |URI do pedido  |
|---------|---------|
|INTRODUÇÃO     |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/System. {RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts?api-version=2016-05-01 "      |
|     |         |

**Argumentos**

|Argumento  |Descrição  |
|---------|---------|
|armendpoint     |  O ponto final do Azure Resource Manager do seu ambiente de pilha do Azure, na https://adminmanagement formato. {RegionName}. {FQDN externo}. Por exemplo, se o FQDN externo é *azurestack.external* e nome de região é *local*, em seguida, o ponto final do Gestor de recursos é https://adminmanagement.local.azurestack.external.       |
|subid     |   ID de subscrição do utilizador que está a efetuar a chamada. Pode utilizar esta API para consulta apenas com um utilizador que tenha permissão para a subscrição do fornecedor predefinido.      |
|RegionName     |    O nome de região da implementação da pilha do Azure.     |
|versão de API     |  Versão do protocolo que é utilizado para efetuar este pedido. Tem de utilizar 2016-05-01.      |
|     |         |

**Resposta**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/Alerts?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Active",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},

…
```

**Detalhes de resposta**


|  Argumento  |Descrição  |
|---------|---------|
|*ID*     |      ID exclusivo do alerta.   |
|*nome*     |     Nome interno do alerta.   |
|*tipo*     |     Definição do recurso.    |
|*localização*     |       Nome de região.     |
|*etiquetas*     |   Sinalizadores de recurso.     |
|*closedtimestamp*    |  Hora UTC quando o alerta foi fechado.    |
|*createdtimestamp*     |     Hora UTC quando o alerta foi criado.   |
|*Descrição*     |    Descrição do alerta.     |
|*faultid*     | Componente afetado.        |
|*alertid*     |  ID exclusivo do alerta.       |
|*faulttypeid*     |  Único tipo de componente defeituoso.       |
|*lastupdatedtimestamp*     |   Hora UTC quando o informações de alerta foi atualizadas pela última vez.    |
|*healthstate*     | Estado de funcionamento geral.        |
|*nome*     |   Nome do alerta específico.      |
|*fabricname*     |    Nome de recursos de infraestrutura registados do componente defeituoso.   |
|*Descrição*     |  Descrição do componente registados infraestrutura.   |
|*ServiceType*     |   Tipo de serviço a recursos de infraestrutura registado.   |
|*remediação*     |   Passos de remediação recomendada.    |
|*tipo*     |   Tipo de alerta.    |
|*resourceRegistrationid*    |     ID do recurso registado afetado.    |
|*resourceProviderRegistrationID*   |    ID do fornecedor de recursos registado do componente afetado.  |
|*serviceregistrationid*     |    ID do serviço registado.   |
|*gravidade*     |     Gravidade do alerta.  |
|*Estado*     |    Estado de alerta.   |
|*título*     |    Título do alerta.   |
|*impactedresourceid*     |     ID de recurso afetado.    |
|*ImpactedresourceDisplayName*     |     Nome do recurso afetado.  |
|*closedByUserAlias*     |   Utilizador fechou o alerta.      |

### <a name="close-alert"></a>Fechar alerta

**Pedido**

O pedido fecha um alerta pelo seu ID exclusivo.

|Método    |URI do pedido  |
|---------|---------|
|COLOCAR     |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/System. {RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts/alertid?api-version=2016-05-01 "    |

**Argumentos**


|Argumento  |Descrição  |
|---------|---------|
|*armendpoint*     |   Ponto final do Gestor de recursos do ambiente de pilha do Azure, na https://adminmanagement formato. {RegionName}. {FQDN externo}. Por exemplo, se o FQDN externo é *azurestack.external* e nome de região é *local*, em seguida, o ponto final do Gestor de recursos é https://adminmanagement.local.azurestack.external.      |
|*subid*     |    ID de subscrição do utilizador que está a efetuar a chamada. Pode utilizar esta API para consulta apenas com um utilizador que tenha permissão para a subscrição do fornecedor predefinido.     |
|*RegionName*     |   O nome de região da implementação da pilha do Azure.      |
|*versão de API*     |    Versão do protocolo que é utilizado para efetuar este pedido. Tem de utilizar 2016-05-01.     |
|*alertid*     |    ID exclusivo do alerta.     |

**Corpo**

```json

{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"2017-08-10T20:18:58.1584868Z",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Closed",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},
```
**Resposta**

```http
PUT https://adminmanagement.local.azurestack.external//subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Closed",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},
```

**Detalhes de resposta**


|  Argumento  |Descrição  |
|---------|---------|
|*ID*     |      ID exclusivo do alerta.   |
|*nome*     |     Nome interno do alerta.   |
|*tipo*     |     Definição do recurso.    |
|*localização*     |       Nome de região.     |
|*etiquetas*     |   Sinalizadores de recurso.     |
|*closedtimestamp*    |  Hora UTC quando o alerta foi fechado.    |
|*createdtimestamp*     |     Hora UTC quando o alerta foi criado.   |
|*Descrição*     |    Descrição do alerta.     |
|*faultid*     | Componente afetado.        |
|*alertid*     |  ID exclusivo do alerta.       |
|*faulttypeid*     |  Único tipo de componente defeituoso.       |
|*lastupdatedtimestamp*     |   Hora UTC quando o informações de alerta foi atualizadas pela última vez.    |
|*healthstate*     | Estado de funcionamento geral.        |
|*nome*     |   Nome do alerta específico.      |
|*fabricname*     |    Nome de recursos de infraestrutura registados do componente defeituoso.   |
|*Descrição*     |  Descrição do componente registados infraestrutura.   |
|*ServiceType*     |   Tipo de serviço a recursos de infraestrutura registado.   |
|*remediação*     |   Passos de remediação recomendada.    |
|*tipo*     |   Tipo de alerta.    |
|*resourceRegistrationid*    |     ID do recurso registado afetado.    |
|*resourceProviderRegistrationID*   |    ID do fornecedor de recursos registado do componente afetado.  |
|*serviceregistrationid*     |    ID do serviço registado.   |
|*gravidade*     |     Gravidade do alerta.  |
|*Estado*     |    Estado de alerta.   |
|*título*     |    Título do alerta.   |
|*impactedresourceid*     |     ID de recurso afetado.    |
|*ImpactedresourceDisplayName*     |     Nome do recurso afetado.  |
|*closedByUserAlias*     |   Utilizador fechou o alerta.      |

### <a name="get-resource-provider-health"></a>Obter o estado de funcionamento de fornecedor de recursos

**Pedido**

O pedido obtém o estado de funcionamento de todos os fornecedores de recursos registado.


|Método  |URI do pedido  |
|---------|---------|
|INTRODUÇÃO    |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/System. {RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths?api-version=2016-05-01 "   |


**Argumentos**


|Argumentos  |Descrição  |
|---------|---------|
|*armendpoint*     |    O ponto final do Gestor de recursos do ambiente de pilha do Azure, na https://adminmanagement formato. {RegionName}. {FQDN externo}. Por exemplo, se o FQDN externo é azurestack.external e nome de região é local, o ponto final do Gestor de recursos é https://adminmanagement.local.azurestack.external.     |
|*subid*     |     ID de subscrição do utilizador que está a efetuar a chamada. Pode utilizar esta API para consulta apenas com um utilizador que tenha permissão para a subscrição do fornecedor predefinido.    |
|*RegionName*     |     O nome de região da implementação da pilha do Azure.    |
|*versão de API*     |   Versão do protocolo que é utilizado para efetuar este pedido. Tem de utilizar 2016-05-01.      |


**Resposta**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths?api-version=2016-05-01
```

```json
{
"value":[
{
"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/03ccf38f-f6b1-4540-9dc8-ec7b6389ecca",
"name":"03ccf38f-f6b1-4540-9dc8ec7b6389ecca",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/serviceHealths",
"location":"local",
"tags":{},
"properties":{
"registrationId":"03ccf38f-f6b1-4540-9dc8-ec7b6389ecca",
"displayName":"Key Vault",
"namespace":"Microsoft.KeyVault.Admin",
"routePrefix":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.KeyVault.Admin/locations/local",
"serviceLocation":"local",
"infraURI":"/subscriptions/4aa97de3-6b83-4582-86e1-65a5e4d1295b/resourceGroups/system.local/providers/Microsoft.KeyVault.Admin/locations/local/infraRoles/Key Vault",
"alertSummary":{"criticalAlertCount":0,"warningAlertCount":0},
"healthState":"Healthy"
}
}

…
```
**Detalhes de resposta**


|Argumento  |Descrição  |
|---------|---------|
|*ID*     |   ID exclusivo do alerta.      |
|*nome*     |  Nome interno do alerta.       |
|*tipo*     |  Definição do recurso.       |
|*localização*     |  Nome de região.       |
|*etiquetas*     |     Sinalizadores de recurso.    |
|*registrationId*     |   Registo exclusivo para o fornecedor de recursos.      |
|*displayName*     |Nome de apresentação do fornecedor de recursos.        |
|*espaço de nomes*     |   Implementa a API espaço de nomes do fornecedor de recursos.       |
|*routePrefix*     |    URI de interagir com o fornecedor de recursos.     |
|*serviceLocation*     |   Região Este fornecedor de recursos está registado.      |
|*infraURI*     |   URI do fornecedor de recursos indicado como uma função de infraestrutura.      |
|*alertSummary*     |   Resumo de alerta de aviso e crítico associado ao fornecedor de recursos.      |
|*healthState*     |    Estado de funcionamento do fornecedor de recursos.     |


### <a name="get-resource-health"></a>Obter o estado de funcionamento de recursos

O pedido obtém o estado de funcionamento para um fornecedor de recursos registados específicos.

**Pedido**

|Método  |URI do pedido  |
|---------|---------|
|INTRODUÇÃO     |     https://{armendpoint}/subscriptions/{subId}/resourceGroups/System. {RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths/{RegistrationID}/resourceHealths?api-version=2016-05-01 "    |

**Argumentos**

|Argumentos  |Descrição  |
|---------|---------|
|*armendpoint*     |    O ponto final do Gestor de recursos do ambiente de pilha do Azure, na https://adminmanagement formato. {RegionName}. {FQDN externo}. Por exemplo, se o FQDN externo é azurestack.external e nome de região é local, o ponto final do Gestor de recursos é https://adminmanagement.local.azurestack.external.     |
|*subid*     |ID de subscrição do utilizador que está a efetuar a chamada. Pode utilizar esta API para consulta apenas com um utilizador que tenha permissão para a subscrição do fornecedor predefinido.         |
|*RegionName*     |  O nome de região da implementação da pilha do Azure.       |
|*versão de API*     |  Versão do protocolo que é utilizado para efetuar este pedido. Tem de utilizar 2016-05-01.       |
|*RegistrationID* |ID de registo para um fornecedor de recursos específico. |

**Resposta**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/03ccf38f-f6b1-4540-9dc8-ec7b6389ecca /resourceHealths?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":
[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/472aaaa6-3f63-43fa-a489-4fd9094e235f/resourceHealths/028c3916-ab86-4e7f-b5c2-0468e607915c",
"name":"028c3916-ab86-4e7f-b5c2-0468e607915c",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/serviceHealths/resourceHealths",
"location":"local",
"tags":{},
"properties":
{"registrationId":"028c3916-ab86-4e7f-b5c2 0468e607915c","namespace":"Microsoft.Fabric.Admin","routePrefix":"/subscriptions/4aa97de3-6b83-4582-86e1 65a5e4d1295b/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local",
"resourceType":"infraRoles",
"resourceName":"Privileged endpoint",
"usageMetrics":[],
"resourceLocation":"local",
"resourceURI":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/Privileged endpoint",
"rpRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"alertSummary":{"criticalAlertCount":0,"warningAlertCount":0},"healthState":"Unknown"
}
}
…
```

**Detalhes de resposta**

|Argumento  |Descrição  |
|---------|---------|
|*ID*     |   ID exclusivo do alerta.      |
|*nome*     |  Nome interno do alerta.       |
|*tipo*     |  Definição do recurso.       |
|*localização*     |  Nome de região.       |
|*etiquetas*     |     Sinalizadores de recurso.    |
|*registrationId*     |   Registo exclusivo para o fornecedor de recursos.      |
|*resourceType*     |Tipo de recurso.        |
|*resourceName*     |   Nome do recurso.   |
|*usageMetrics*     |    Métrica de utilização para o recurso.     |
|*resourceLocation*     |   Nome da região onde implementado.      |
|*resourceURI*     |   URI do recurso.   |
|*alertSummary*     |   Resumo de crítico e alertas de aviso, o estado de funcionamento.     |

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre a monitorização de estado de funcionamento incorporado, consulte [monitorizar o estado de funcionamento e alertas na pilha de Azure](azure-stack-monitor-health.md).


