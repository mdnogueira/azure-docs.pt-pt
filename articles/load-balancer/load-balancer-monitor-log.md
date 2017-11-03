---
title: "Monitorizar operações, eventos e contadores de Balanceador de carga | Microsoft Docs"
description: Saiba como ativar a eventos de alerta e a sonda de registo de estado de funcionamento para o Balanceador de carga do Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
ms.assetid: 56656d74-0241-4096-88c8-aa88515d676d
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 36b9379f88ce024c1dcbf9977a873d95076d10df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-for-azure-load-balancer"></a>Log Analytics para o Balanceador de Carga do Azure

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Pode utilizar diferentes tipos de registos no Azure para gerir e resolver problemas de balanceadores de carga. Alguns destes registos podem ser acedidos através do portal. Todos os registos podem ser extraídos de armazenamento de Blobs do Azure e visualizados em diferentes ferramentas, como o Excel e o Power BI. Pode saber mais sobre os diferentes tipos de registos da lista abaixo.

* **Registos de auditoria:** pode utilizar [registos de auditoria do Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (anteriormente conhecida como registos operacionais) para ver todas as operações que está a ser submetidas para as subscrições do Azure e o respetivo estado. Os registos de auditoria são ativados por predefinição e podem ser visualizados no portal do Azure.
* **Registos de eventos de alertas:** pode utilizar este registo para ver alertas rasied pelo balanceador de carga. O estado para o Balanceador de carga é recolhido a cada cinco minutos. Este registo só é escrito se é gerado um evento de alerta de Balanceador de carga.
* **Os registos de pesquisa de estado de funcionamento:** pode utilizar este registo para ver os problemas detetados pela sua pesquisa de estado de funcionamento, como o número de instâncias do conjunto de back-end que não são receção de pedidos do Balanceador de carga devido a falhas de sonda de estado de funcionamento. Este registo é escrito quando existe uma alteração do Estado de sonda de estado de funcionamento.

> [!IMPORTANT]
> Análise de registo atualmente funciona apenas para a Internet com balanceadores de carga. Os registos apenas estão disponíveis para os recursos implementados no modelo de implementação Resource Manager. Não é possível utilizar os registos de recursos de no modelo de implementação clássica. Para obter mais informações sobre os modelos de implementação, consulte [Gestor de recursos de compreender implementação clássica e](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="enable-logging"></a>Ativar registo

Um registo de auditoria é ativado automaticamente para todos os recursos do Resource Manager. Tem de ativar o evento e o registo de pesquisa de estado de funcionamento para iniciar a recolha de dados disponíveis através desses registos. Utilize os seguintes passos para ativar o registo.

Início de sessão para o [portal do Azure](http://portal.azure.com). Se ainda não tiver um balanceador de carga, [criar um balanceador de carga](load-balancer-get-started-internet-arm-ps.md) antes de continuar.

1. No portal, clique em **procurar**.
2. Selecione **balanceadores de carga**.

    ![Portal – Balanceador de carga](./media/load-balancer-monitor-log/load-balancer-browse.png)

3. Selecione um balanceador de carga existente >> **todas as definições**.
4. No lado direito da caixa de diálogo sob o nome do Balanceador de carga, desloque-se para **monitorização**, clique em **diagnóstico**.

    ![Portal – definições de Balanceador de carga](./media/load-balancer-monitor-log/load-balancer-settings.png)

5. No **diagnóstico** painel, em **estado**, selecione **no**.
6. Clique em **conta de armazenamento**.
7. Em **registos**, selecione uma conta de armazenamento existente ou crie um novo. Utilize o controlo de deslize para determinar quantos dias, mas de dados de eventos serão armazenados nos registos de evento. 
8. Clique em **Guardar**.

    ![Portal – registos de diagnóstico](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

> [!NOTE]
> Os registos de auditoria não necessitam de uma conta de armazenamento separada. A utilização do armazenamento de estado de funcionamento e eventos de registo de pesquisa resultará em encargos serviço.

## <a name="audit-log"></a>Registo de auditoria

O registo de auditoria é gerado por predefinição. Os registos são preservados durante 90 dias no arquivo de registos de eventos do Azure. Saiba mais sobre estes registos o lendo o [ver eventos e registos de auditoria](../monitoring-and-diagnostics/insights-debugging-with-events.md) artigo.

## <a name="alert-event-log"></a>Alerta de registo de eventos

Este registo só é gerado se ativou-num por base de Balanceador de carga. Os eventos são registados no formato JSON e armazenados na conta de armazenamento que especificou quando ativou o registo. Segue-se um exemplo de um evento.

```json
{
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
}
```

O JSON de saída apresenta o *eventname* propriedade que descreve o motivo para o Balanceador de carga criado um alerta. Neste caso, o alerta gerado se deveu a esgotamento de porta TCP causado pela origem IP NAT limites (realizar o SNAT).

## <a name="health-probe-log"></a>Registo de pesquisa de estado de funcionamento

Este registo só é gerado se ativou-num por carga balanceador base conforme detalhado acima. Os dados são armazenados na conta de armazenamento que especificou quando ativou o registo. É criado um contentor com o nome 'insights-registos-loadbalancerprobehealthstatus' e os dados seguintes foram registados:

```json
{
    "records":[
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 1,
            "healthPercentage": 50.000000
        }
    },
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 0,
            "healthPercentage": 100.000000
        }
    }]
}
```

A saída JSON no campo propriedades mostra as informações básicas para o estado de funcionamento da pesquisa. O *dipDownCount* propriedade mostra o número total de instâncias no back-end que não está a receber o tráfego de rede devido ao respostas de falhas de sonda.

## <a name="view-and-analyze-the-audit-log"></a>Ver e analisar o registo de auditoria

Pode ver e analisar dados de registo de auditoria utilizando qualquer um dos seguintes métodos:

* **Ferramentas do Azure:** obter informações a partir de registos de auditoria através do Azure PowerShell, a Interface de linha de comandos (CLI do Azure), a API de REST do Azure ou o portal de pré-visualização do Azure. Instruções passo a passo para cada método passo são detalhadas no [auditar operações com o Resource Manager](../azure-resource-manager/resource-group-audit.md) artigo.
* **Power BI:** se já tiver um [Power BI](https://powerbi.microsoft.com/pricing) conta, pode experimentar, gratuitamente. Utilizar o [conteúdo de registos de auditoria do Azure pack para o Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs), pode analisar os dados com dashboards previamente configuradas ou pode personalizar vistas de acordo com as suas necessidades.

## <a name="view-and-analyze-the-health-probe-and-event-log"></a>Ver e analisar a sonda de estado de funcionamento e o registo de eventos

Terá de ligar à sua conta de armazenamento e obter as entradas de registo JSON para os registos de sonda de estado de funcionamento e de eventos. Depois de transferir os ficheiros JSON, pode convertê-las em CSV e vista no Excel, PowerBI ou qualquer outra ferramenta de visualização de dados.

> [!TIP]
> Se estiver familiarizado com conceitos básicos do alterar os valores de constantes e variáveis em c# e o Visual Studio, pode utilizar o [iniciar ferramentas de conversor](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis a partir do GitHub.

## <a name="additional-resources"></a>Recursos adicionais

* [Visualizar os registos de auditoria do Azure com Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) blogue.
* [Ver e analisar os registos de auditoria do Azure no Power BI e muito mais](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogue.

## <a name="next-steps"></a>Passos seguintes

[Compreender as sondas do balanceador de carga](load-balancer-custom-probe-overview.md)
