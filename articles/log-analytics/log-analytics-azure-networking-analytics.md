---
title: "Solução de análise de redes do Azure no Log Analytics | Microsoft Docs"
description: "Pode utilizar a solução de análise de redes do Azure na análise de registos para rever os registos de grupo de segurança de rede do Azure e os registos do Gateway de aplicação do Azure."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: ewinner
editor: 
ms.assetid: 66a3b8a1-6c55-4533-9538-cad60c18f28b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: richrund
ms.openlocfilehash: 06b67322b3812a668a515ecc357171ede1d85441
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-networking-monitoring-solutions-in-log-analytics"></a>Soluções de análise de registos de monitorização da rede do Azure

Análise de registos oferece as seguintes soluções para monitorização as redes:
* Monitor de desempenho de rede (NPM) para
 * Monitorizar o estado de funcionamento da rede
* Análise de Gateway de aplicação do Azure para rever
 * Registos de Gateway de aplicação do Azure
 * Métricas de Gateway de aplicação do Azure
* Análise de grupo de segurança de rede do Azure para rever
 * Registos do grupo de segurança de rede do Azure

## <a name="network-performance-monitor-npm"></a>Monitor de desempenho de rede (NPM)

O [Monitor de desempenho de rede](log-analytics-network-performance-monitor.md) solução de gestão é uma solução, que monitoriza o estado de funcionamento, a disponibilidade e acessibilidade de redes de monitorização de rede.  É utilizado para monitorizar a conectividade entre:

* Nuvem pública e no local
* Os centros de dados e localizações de utilizador (sucursais)
* Sub-redes alojar várias camadas de uma aplicação de várias camadas.

Para obter mais informações, consulte [Monitor de desempenho de rede](log-analytics-network-performance-monitor.md).

## <a name="azure-application-gateway-and-network-security-group-analytics"></a>Análise de Gateway de aplicação e o grupo de segurança de rede do Azure
Para utilizar as soluções:
1. Adicionar a solução de gestão para análise de registos, e
2. Ative os diagnósticos direcionar o diagnóstico para uma área de trabalho de análise de registos. Não é necessário escrever os registos no Blob storage do Azure.

Pode ativar os diagnósticos e a solução correspondente para uma ou ambas Gateway de aplicação e grupos de segurança de rede.

Se não ativar o registo de diagnóstico para um tipo de recurso específico, mas instalar a solução, os painéis de dashboard para esse recurso em branco e apresentam uma mensagem de erro.

> [!NOTE]
> Em Janeiro de 2017, alterar a forma suportada de envio de registos de Gateways de aplicação e os grupos de segurança de rede para análise de registos. Se vir o **análise de rede do Azure (preterido)** solução, consulte [migrar da solução de análise de redes antiga](#migrating-from-the-old-networking-analytics-solution) para obter os passos, tem de seguir.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Reveja os detalhes de recolha de dados de rede do Azure
A análise de Gateway de aplicação do Azure e as soluções de gestão do grupo de segurança de rede análise recolher registos de diagnóstico diretamente a partir de grupos de segurança de rede e Gateways de aplicação do Azure. Não é necessário escrever os registos no Blob storage do Azure e não existe nenhum agente é necessário para a recolha de dados.

A tabela seguinte mostra os métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para análise de Gateway de aplicação do Azure e a análise do grupo de segurança de rede.

| Plataforma | Direcionar o agente | Agente do System Center Operations Manager de sistemas | Azure | O Operations Manager necessárias? | Dados de agente do Operations Manager enviados através do grupo de gestão | Frequência da recolha |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  |Quando tem sessão iniciada |


## <a name="azure-application-gateway-analytics-solution-in-log-analytics"></a>Solução de análise do Gateway de aplicação do Azure na análise de registos

![Símbolo de análise de Gateway de aplicação do Azure](./media/log-analytics-azure-networking/azure-analytics-symbol.png)

Os seguintes registos são suportados para Gateways de aplicação:

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

As métricas seguintes são suportadas para Gateways de aplicação:

* débito de 5 minutos

### <a name="install-and-configure-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes instruções para instalar e configurar a solução de análise do Gateway de aplicação do Azure:

1. Ativar a solução de análise do Gateway de aplicação do Azure de [do Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureAppGatewayAnalyticsOMS?tab=Overview) ou utilizando o processo descrito no [soluções de análise de registos adicionar da galeria do soluções](log-analytics-add-solutions.md).
2. Ativar o diagnóstico de registo para o [Gateways de aplicação](../application-gateway/application-gateway-diagnostics.md) que pretende monitorizar.

#### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>Ativar o diagnóstico do Gateway de aplicação do Azure no portal

1. No portal do Azure, navegue para o recurso de Gateway de aplicação a monitorizar
2. Selecione *registos de diagnóstico* para abrir a página seguinte

   ![imagem de recurso de Gateway de aplicação do Azure](./media/log-analytics-azure-networking/log-analytics-appgateway-enable-diagnostics01.png)
3. Clique em *ative os diagnósticos* para abrir a página seguinte

   ![imagem de recurso de Gateway de aplicação do Azure](./media/log-analytics-azure-networking/log-analytics-appgateway-enable-diagnostics02.png)
4. Para ativar diagnósticos, clique em *no* em *Estado*
5. Clique na caixa de verificação para *enviar ao Log Analytics*
6. Selecione uma área de trabalho de análise de registos existente ou crie uma área de trabalho
7. Clique na caixa de verificação em **registo** para cada um dos tipos de registo a recolher
8. Clique em *guardar* para ativar o registo de diagnóstico ao Log Analytics

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>Ativar o diagnóstico de rede do Azure com o PowerShell

O script do PowerShell seguinte fornece um exemplo de como ativar o diagnóstico de registo para gateways de aplicação.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzureRmApplicationGateway -Name 'ContosoGateway'

Set-AzureRmDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-application-gateway-analytics"></a>Utilize a análise de Gateway de aplicação do Azure
![imagem do Gateway de aplicação do Azure mosaico de análise](./media/log-analytics-azure-networking/log-analytics-appgateway-tile.png)

Depois de clicar no **análise de Gateway de aplicação do Azure** mosaico de descrição geral, pode ver resumos dos seus registos e, em seguida, para obter os detalhes para as seguintes categorias:

* Os registos de acesso de Gateway de aplicação
  * Erros de cliente e servidor para os registos de acesso do Gateway de aplicação
  * Pedidos por hora para cada Gateway de aplicação
  * Falha de pedidos por hora para cada Gateway de aplicação
  * Erros pelo agente de utilizador para Gateways de aplicação
* Desempenho do Gateway de aplicação
  * Estado de funcionamento do anfitrião para o Gateway de aplicação
  * Pedidos falhados de percentil máximo e 95th para Gateway de aplicação

![imagem do dashboard de análise do Gateway de aplicação do Azure](./media/log-analytics-azure-networking/log-analytics-appgateway01.png)

![imagem do dashboard de análise do Gateway de aplicação do Azure](./media/log-analytics-azure-networking/log-analytics-appgateway02.png)

No **análise de Gateway de aplicação do Azure** dashboard, reveja as informações de resumo de uma das seguintes painéis de e, em seguida, clique num para ver informações detalhadas na página de pesquisa de registo.

Em qualquer uma das páginas de pesquisa de registo, pode ver os resultados por tempo, os resultados detalhados e o histórico de pesquisa de registo. Também pode filtrar por facetas para restringir os resultados.


## <a name="azure-network-security-group-analytics-solution-in-log-analytics"></a>Solução de análise do grupo de segurança de rede do Azure na análise de registos

![Símbolo de análise do grupo de segurança de rede do Azure](./media/log-analytics-azure-networking/azure-analytics-symbol.png)

Os seguintes registos são suportados para grupos de segurança de rede:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes instruções para instalar e configurar a solução de análise de redes do Azure:

1. Ativar a solução de análise do grupo de segurança de rede do Azure de [do Azure marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureNSGAnalyticsOMS?tab=Overview) ou utilizando o processo descrito no [soluções de análise de registos adicionar da galeria do soluções](log-analytics-add-solutions.md).
2. Ativar o diagnóstico de registo para o [grupo de segurança de rede](../virtual-network/virtual-network-nsg-manage-log.md) recursos que pretende monitorizar.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Ativar o diagnóstico de grupo de segurança de rede do Azure no portal

1. No portal do Azure, navegue para o recurso do grupo de segurança de rede para monitorizar
2. Selecione *registos de diagnóstico* para abrir a página seguinte

   ![imagem de recurso do grupo de segurança de rede do Azure](./media/log-analytics-azure-networking/log-analytics-nsg-enable-diagnostics01.png)
3. Clique em *ative os diagnósticos* para abrir a página seguinte

   ![imagem de recurso do grupo de segurança de rede do Azure](./media/log-analytics-azure-networking/log-analytics-nsg-enable-diagnostics02.png)
4. Para ativar diagnósticos, clique em *no* em *Estado*
5. Clique na caixa de verificação para *enviar ao Log Analytics*
6. Selecione uma área de trabalho de análise de registos existente ou crie uma área de trabalho
7. Clique na caixa de verificação em **registo** para cada um dos tipos de registo a recolher
8. Clique em *guardar* para ativar o registo de diagnóstico ao Log Analytics

### <a name="enable-azure-network-diagnostics-using-powershell"></a>Ativar o diagnóstico de rede do Azure com o PowerShell

O script do PowerShell seguinte fornece um exemplo de como ativar o diagnóstico de registo para grupos de segurança de rede
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzureRmNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzureRmDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Análise de grupo de segurança de rede de Azure de utilização
Depois de clicar no **análise do grupo de segurança de rede de Azure** mosaico de descrição geral, pode ver resumos dos seus registos e, em seguida, para obter os detalhes para as seguintes categorias:

* Grupo de segurança de rede bloqueado fluxos
  * Regras de grupo de segurança de rede com fluxos de bloqueados
  * Endereços MAC com fluxos bloqueados
* Grupo de segurança de rede permitido fluxos
  * Regras de grupo de segurança de rede com fluxos permitidos
  * Endereços MAC com fluxos permitidos

![imagem do dashboard de análise do grupo de segurança de rede do Azure](./media/log-analytics-azure-networking/log-analytics-nsg01.png)

![imagem do dashboard de análise do grupo de segurança de rede do Azure](./media/log-analytics-azure-networking/log-analytics-nsg02.png)

No **análise do grupo de segurança de rede de Azure** dashboard, reveja as informações de resumo de uma das seguintes painéis de e, em seguida, clique num para ver informações detalhadas na página de pesquisa de registo.

Em qualquer uma das páginas de pesquisa de registo, pode ver os resultados por tempo, os resultados detalhados e o histórico de pesquisa de registo. Também pode filtrar por facetas para restringir os resultados.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>Migrar a partir da solução de análise de redes antiga
Em Janeiro de 2017, alterar a forma de envio de registos de Gateways de aplicação do Azure e os grupos de segurança de rede do Azure ao Log Analytics suportada. Estas alterações fornecem as seguintes vantagens:
+ Os registos são escritos diretamente ao Log Analytics sem a necessidade de utilizar uma conta de armazenamento
+ Menor latência de tempo quando são gerados registos aos mesmos estejam disponíveis no Log Analytics
+ Menos passos de configuração
+ Um formato comum para todos os tipos de diagnóstico do Azure

Para utilizar as soluções atualizadas:

1. [Configurar diagnósticos para serem enviados diretamente ao Log Analytics dos Gateways de aplicação do Azure](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [Configurar diagnósticos para serem enviados diretamente ao Log Analytics grupos de segurança de rede do Azure](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. Ativar o *análise de Gateway de aplicação do Azure* e *análises de grupo de segurança de rede de Azure* solução utilizando o processo descrito no [soluções de análise de registos adicionar da galeria do soluções](log-analytics-add-solutions.md)
3. Atualizar qualquer consultas guardadas, dashboards ou alertas para utilizar o novo tipo de dados
  + Tipo é AzureDiagnostics. Pode utilizar o ResourceType para filtrar nos registos de rede do Azure.

    | Em vez de: | Utilização: |
    | --- | --- |
    |`Type=NetworkApplicationgateways OperationName=ApplicationGatewayAccess`| `Type=AzureDiagnostics ResourceType=APPLICATIONGATEWAYS OperationName=ApplicationGatewayAccess` |
    |`Type=NetworkApplicationgateways OperationName=ApplicationGatewayPerformance` | `Type=AzureDiagnostics ResourceType=APPLICATIONGATEWAYS OperationName=ApplicationGatewayPerformance` |
    | `Type=NetworkSecuritygroups` | `Type=AzureDiagnostics ResourceType=NETWORKSECURITYGROUPS` |

   + Para qualquer campo que têm um sufixo de \_s, \_d, ou \_g no nome, altere o primeiro caráter caso inferior
   + Para qualquer campo que têm um sufixo de \_o nome e os dados está dividido em campos individuais com base nos nomes de campo aninhada.
4. Remova o *redes a análise do Azure (preterido)* solução.
  + Se estiver a utilizar o PowerShell, utilize`Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`

Recolher os dados antes da alteração não é visível na solução de novo. Pode continuar a consultar estes dados com o tipo de antigo e nomes de campo.

## <a name="troubleshooting"></a>Resolução de problemas
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Passos seguintes
* Utilize [pesquisas de registo na análise de registos](log-analytics-log-searches.md) para ver detalhadas dados de diagnóstico do Azure.
