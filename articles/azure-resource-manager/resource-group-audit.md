---
title: Ver registos de atividade do Azure para monitorizar recursos | Microsoft Docs
description: "Utilize os registos de atividade para ações de utilizador de revisão e erros. Mostra o PowerShell Portal do Azure, CLI do Azure e REST."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: fcdb3125-13ce-4c3b-9087-f514c5e41e73
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: tomfitz
ms.openlocfilehash: 9f90bc80c146c6c2da04aacbc110f7d389c0baa2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="view-activity-logs-to-audit-actions-on-resources"></a>Ver registos de atividade para auditar as ações em recursos
Através de registos de atividade, poderá determinar:

* as operações que foram executadas nos recursos na sua subscrição
* quem iniciou a operação (embora operações iniciadas por um serviço de back-end não devolver um utilizador, como o autor da chamada)
* quando ocorreu a operação
* o estado da operação
* os valores de outras propriedades que possam ajudá-lo pesquisar a operação

[!INCLUDE [resource-manager-audit-limitations](../../includes/resource-manager-audit-limitations.md)]

Pode obter informações a partir de registos de atividade através do portal, do PowerShell, a CLI do Azure, a API de REST de Insights, ou [Insights .NET biblioteca](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="portal"></a>Portal
1. Para ver os registos de atividade através do portal, selecione **Monitor**.
   
    ![Selecione os registos de atividade](./media/resource-group-audit/select-monitor.png)

   Ou, para filtrar automaticamente o registo de atividade para um determinado recurso ou grupo de recursos, selecione **registo de atividade** a partir do painel recurso. Tenha em atenção que o registo de atividade é filtrado automaticamente pelo recurso selecionado.
   
    ![Filtrar por recurso](./media/resource-group-audit/filtered-by-resource.png)
2. No **registo de atividade** painel, pode ver um resumo das operações recentes.
   
    ![Mostrar ações](./media/resource-group-audit/audit-summary.png)
3. Para restringir o número de operações, apresentada, selecione condições diferentes. Por exemplo, a imagem seguinte mostra o **Timespan** e **eventos iniciadas pelo** campos alterado para ver as ações executadas por um utilizador específico ou aplicação para o último mês. Selecione **aplicar** para ver os resultados da sua consulta.
   
    ![Opções de filtro de conjunto](./media/resource-group-audit/set-filter.png)

4. Se precisar de executar a consulta novamente mais tarde, selecione **guardar** e atribua um nome de consulta.
   
    ![guardar consultas](./media/resource-group-audit/save-query.png)
5. Para executar rapidamente uma consulta, pode selecionar uma das consultas incorporadas, tais como implementação falhou.

    ![Selecione a consulta](./media/resource-group-audit/select-quick-query.png)

   A consulta selecionada configura automaticamente os valores de filtro necessária.

    ![erros de implementação de vista](./media/resource-group-audit/view-failed-deployment.png)   

6. Selecione uma das operações para ver um resumo do evento.

    ![operação de vista](./media/resource-group-audit/view-operation.png)  

## <a name="powershell"></a>PowerShell
1. Para obter as entradas de registo, execute o **Get-AzureRmLog** comando. Fornecer parâmetros adicionais para filtrar a lista de entradas. Se não especificar uma hora de início e de fim, são devolvidas as entradas de última hora. Por exemplo, para obter as operações de um grupo de recursos durante a hora anterior a executar:

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup
  ```
   
    O exemplo seguinte mostra como utilizar o registo de atividade para operações de investigação executadas durante um período de tempo especificado. Estão especificadas as datas de início e de fim no formato de data.

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00
  ```

    Em alternativa, pode utilizar funções de data para especificar o intervalo de datas, tais como os últimos 14 dias.
   
  ```powershell 
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
  ```

2. Consoante a hora de início que especificar, os comandos anteriores podem devolver uma longa lista de operações para o grupo de recursos. Pode filtrar os resultados para aquilo procura, fornecendo os critérios de pesquisa. Por exemplo, se estiver a tentar pesquisar como uma aplicação web foi parada, é possível executar o seguinte comando:

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action
  ```

    Que para este exemplo mostra que uma ação de paragem foi realizada someone@contoso.com. 

  ```powershell 
  Authorization     :
  Scope     : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
  Action    : Microsoft.Web/sites/stop/action
  Role      : Subscription Admin
  Condition :
  Caller            : someone@contoso.com
  CorrelationId     : 84beae59-92aa-4662-a6fc-b6fecc0ff8da
  EventSource       : Administrative
  EventTimestamp    : 8/28/2015 4:08:18 PM
  OperationName     : Microsoft.Web/sites/stop/action
  ResourceGroupName : ExampleGroup
  ResourceId        : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
  Status            : Succeeded
  SubscriptionId    : xxxxx
  SubStatus         : OK
  ```

3. Pode procurar as ações executadas por um utilizador em particular, mesmo para um grupo de recursos que já não existe.

  ```powershell 
  Get-AzureRmLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
  ```

4. Pode filtrar operações falhadas.

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed
  ```

5. Pode concentrar-se com um erro ao observar a mensagem de estado para essa entrada.
   
        ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
   
    Que devolve:
   
        code           message                                                                        
        ----           -------                                                                        
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. 


## <a name="azure-cli"></a>CLI do Azure
* Para obter as entradas de registo, execute o **mostrar de registo de grupo do azure** comando.

  ```azurecli
  azure group log show ExampleGroup --json
  ```


## <a name="rest-api"></a>API REST
As operações REST para trabalhar com o registo de atividade são parte a [Insights API de REST](https://msdn.microsoft.com/library/azure/dn931943.aspx). Para obter eventos de registo de atividade, consulte [lista os eventos de gestão numa subscrição](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## <a name="next-steps"></a>Passos seguintes
* Os registos de atividade do Azure podem ser utilizados com o Power BI para obter mais informações sobre as ações na sua subscrição. Consulte [ver e analisar registos de atividade do Azure no Power BI e muito mais](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
* Para saber mais sobre a definição de políticas de segurança, consulte [controlo de acesso baseado em funções do Azure](../active-directory/role-based-access-control-configure.md).
* Para saber mais sobre os comandos para ver as operações de implementação, consulte [ver as operações de implementação](resource-manager-deployment-operations.md).
* Para saber como impedir eliminações num recurso para todos os utilizadores, consulte [bloquear recursos com o Azure Resource Manager](resource-group-lock-resources.md).

