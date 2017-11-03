---
title: "Solução de Cofre de chaves do Azure no Log Analytics | Microsoft Docs"
description: "Pode utilizar a solução Cofre de chaves do Azure na análise de registos para rever os registos do Cofre de chaves do Azure."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: 5e25e6d6-dd20-4528-9820-6e2958a40dae
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: richrund
ms.openlocfilehash: 651586e0846ffb22a23e64b73c2cc614980d9b92
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-key-vault-analytics-solution-in-log-analytics"></a>Soluções de análise do Cofre de chaves do Azure na análise de registos

![Símbolo do Cofre de chaves](./media/log-analytics-azure-keyvault/key-vault-analytics-symbol.png)

Pode utilizar a solução do Azure Key Vault no Log Analytics para rever os registos AuditEvent do Azure Key Vault.

Para utilizar a solução, terá de ativar o registo de diagnóstico do Cofre de chaves do Azure e direcionar o diagnóstico para uma área de trabalho de análise de registos. Não é necessário escrever os registos no Blob storage do Azure.

> [!NOTE]
> Em Janeiro de 2017, alterar a forma de envio de registos do Cofre de chaves ao Log Analytics suportada. Mostra se a solução Cofre de chaves estiver a utilizar *(preterido)* o título, referem-se a [migrar da solução Cofre de chaves antiga](#migrating-from-the-old-key-vault-solution) para obter os passos, tem de seguir.
>
>

## <a name="install-and-configure-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes instruções para instalar e configurar a solução Cofre de chaves do Azure:

1. Ativar a solução Cofre de chaves do Azure de [do Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview) ou utilizando o processo descrito no [soluções de análise de registos adicionar da galeria do soluções](log-analytics-add-solutions.md).
2. Ativar o registo de diagnóstico para os recursos do Cofre de chaves monitorizar, utilizando o [portal](#enable-key-vault-diagnostics-in-the-portal) ou [PowerShell](#enable-key-vault-diagnostics-using-powershell)

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Ativar o diagnóstico do Cofre de chaves no portal

1. No portal do Azure, navegue para o recurso do Cofre de chaves para monitorizar
2. Selecione *registos de diagnóstico* para abrir a página seguinte

   ![imagem de mosaico do Cofre de chaves do Azure](./media/log-analytics-azure-keyvault/log-analytics-keyvault-enable-diagnostics01.png)
3. Clique em *ative os diagnósticos* para abrir a página seguinte

   ![imagem de mosaico do Cofre de chaves do Azure](./media/log-analytics-azure-keyvault/log-analytics-keyvault-enable-diagnostics02.png)
4. Para ativar diagnósticos, clique em *no* em *Estado*
5. Clique na caixa de verificação para *enviar ao Log Analytics*
6. Selecione uma área de trabalho de análise de registos existente ou crie uma área de trabalho
7. Para ativar *AuditEvent* registos, clique na caixa de verificação no registo
8. Clique em *guardar* para ativar o registo de diagnóstico ao Log Analytics

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Ativar o diagnóstico do Cofre de chaves utilizando o PowerShell
O script do PowerShell seguinte fornece um exemplo de como utilizar `Set-AzureRmDiagnosticSetting` para ativar o registo de diagnóstico para o Cofre de chaves:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'

Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Reveja os detalhes de recolha de dados do Cofre de chaves do Azure
Solução do Cofre de chaves do Azure recolhe registos de diagnóstico diretamente a partir do Cofre de chaves.
Não é necessário escrever os registos no Blob storage do Azure e não existe nenhum agente é necessário para a recolha de dados.

A tabela seguinte mostra os métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para o Cofre de chaves do Azure.

| Plataforma | Direcionar o agente | Agente do System Center Operations Manager de sistemas | Azure | O Operations Manager necessárias? | Dados de agente do Operations Manager enviados através do grupo de gestão | Frequência da recolha |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | sobre chegada |

## <a name="use-azure-key-vault"></a>Utilizar o Azure Key Vault
Depois de [instalar a solução](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview), ver os dados do Cofre de chaves clicando a **Cofre de chaves do Azure** mosaico do **descrição geral** página de análise de registos.

![imagem de mosaico do Cofre de chaves do Azure](./media/log-analytics-azure-keyvault/log-analytics-keyvault-tile.png)

Depois de clicar no **descrição geral** mosaico, pode ver resumos dos seus registos e, em seguida, para obter os detalhes para as seguintes categorias:

* Volume de todas as operações do Cofre de chaves ao longo do tempo
* Falha de volumes de operação ao longo do tempo
* Latência média de operacional operação
* Qualidade de serviço para as operações com o número de operações que demorar mais de 1000 ms e uma lista de operações que demorar mais de 1000 ms

![imagem do dashboard do Cofre de chaves do Azure](./media/log-analytics-azure-keyvault/log-analytics-keyvault01.png)

![imagem do dashboard do Cofre de chaves do Azure](./media/log-analytics-azure-keyvault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Para ver os detalhes em nenhuma operação
1. No **descrição geral** página, clique em de **Cofre de chaves do Azure** mosaico.
2. No **Cofre de chaves do Azure** dashboard, reveja as informações de resumo de uma das seguintes painéis de e, em seguida, clique num para ver informações detalhadas acerca do mesmo na página de pesquisa de registo.

    Em qualquer uma das páginas de pesquisa de registo, pode ver os resultados por tempo, os resultados detalhados e o histórico de pesquisa de registo. Também pode filtrar por facetas para restringir os resultados.

## <a name="log-analytics-records"></a>Registos do Log Analytics
A solução Cofre de chaves do Azure analisa os registos que tenham um tipo de **KeyVaults** que são recolhidos de [AuditEvent registos](../key-vault/key-vault-logging.md) no diagnóstico do Azure.  As propriedades para estes registos estão na tabela seguinte:  

| Propriedade | Descrição |
|:--- |:--- |
| Tipo |*AzureDiagnostics* |
| SourceSystem |*Azure* |
| CallerIpAddress |Endereço IP do cliente que efetuou o pedido |
| Categoria | *AuditEvent* |
| CorrelationId |Um GUID opcional que o cliente pode passar para correlacionar os registos do lado do cliente com os registos do lado do serviço (Cofre de Chaves). |
| DurationMs |Tempo necessário para o processamento do pedido de API REST, em milissegundos. Neste momento não inclui a latência de rede, para que o tempo que medir do lado do cliente poderá não corresponder a este período de tempo. |
| httpStatusCode_d |Código de estado HTTP devolvido por pedido (por exemplo, *200*) |
| id_s |ID exclusivo do pedido |
| identity_claim_appid_g | GUID para o id da aplicação |
| OperationName |Nome da operação, conforme documentado no [registo do Cofre de chaves do Azure](../key-vault/key-vault-logging.md) |
| OperationVersion |Versão de REST API solicitada pelo cliente (por exemplo *2015-06-01*) |
| requestUri_s |URI do pedido |
| Recurso |Nome do Cofre de chaves |
| ResourceGroup |Grupo de recursos do Cofre de chaves |
| ResourceId |ID do Recurso do Azure Resource Manager Para os registos do Cofre de chaves, este é o ID de recurso do Cofre de chaves. |
| ResourceProvider |*MICROSOFT. KEYVAULT* |
| ResourceType | *COFRES* |
| ResultSignature |Estado de HTTP (por exemplo, *OK*) |
| ResultType |Resultado do pedido de REST API (por exemplo, *êxito*) |
| SubscriptionId |ID de subscrição do Azure da subscrição que contém o Cofre de chaves |

## <a name="migrating-from-the-old-key-vault-solution"></a>Migrar a partir da solução antiga do Cofre de chaves
Em Janeiro de 2017, alterar a forma de envio de registos do Cofre de chaves ao Log Analytics suportada. Estas alterações fornecem as seguintes vantagens:
+ Os registos são escritos diretamente ao Log Analytics sem a necessidade de utilizar uma conta de armazenamento
+ Menor latência de tempo quando são gerados registos aos mesmos estejam disponíveis no Log Analytics
+ Menos passos de configuração
+ Um formato comum para todos os tipos de diagnóstico do Azure

Para utilizar a solução atualizada:

1. [Configurar diagnósticos para ser enviado diretamente para análise de registos do Cofre de chaves](#enable-key-vault-diagnostics-in-the-portal)  
2. Ativar a solução Cofre de chaves do Azure utilizando o processo descrito no [soluções de análise de registos adicionar da galeria do soluções](log-analytics-add-solutions.md)
3. Atualizar qualquer consultas guardadas, dashboards ou alertas para utilizar o novo tipo de dados
  + Tipo é alterar de: KeyVaults para AzureDiagnostics. Pode utilizar o ResourceType para filtrar nos registos do Cofre de chave.
  - Em vez de: `Type=KeyVaults`, utilize`Type=AzureDiagnostics ResourceType=VAULTS`
  + Campos: (nomes de campo são sensível a maiúsculas e minúsculas)
  - Para qualquer campo que têm um sufixo de \_s, \_d, ou \_g no nome, altere o primeiro caráter caso inferior
  - Para qualquer campo que têm um sufixo de \_o nome e os dados está dividido em campos individuais com base nos nomes de campo aninhada. Por exemplo, o UPN do chamador é armazenado num campo`identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   - Campo CallerIpAddress alterado para CallerIPAddress
   - Campo RemoteIPCountry já não está presente
4. Remova o *análise do Cofre de chave (preterido)* solução. Se estiver a utilizar o PowerShell, utilize`Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

Recolher os dados antes da alteração não é visível na solução de novo. Pode continuar a consultar estes dados com o tipo de antigo e nomes de campo.

## <a name="troubleshooting"></a>Resolução de problemas
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Passos seguintes
* Utilize [pesquisas de registo na análise de registos](log-analytics-log-searches.md) para ver os dados detalhados do Cofre de chaves do Azure.
