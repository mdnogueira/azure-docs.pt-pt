---
title: "Escalável do Azure Analysis Services | Microsoft Docs"
description: Replicar servidores do Azure Analysis Services com Escalamento horizontal
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: owend
ms.openlocfilehash: a97f9648efef7f07659110d720c200dcd0a241a9
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="azure-analysis-services-scale-out"></a>Escalável do Azure Analysis Services

Com Escalamento horizontal, consultas de cliente podem ser distribuídas entre vários *consultar réplicas* no agrupamento de consulta, reduzindo os tempos de resposta durante a cargas de trabalho de consulta elevado. Também pode separar o processamento do conjunto de consulta, garantindo a consultas de cliente não são afetadas negativamente por operações de processamento. Escalamento horizontal pode ser configurado no portal do Azure ou através da API de REST do Analysis Services.

## <a name="how-it-works"></a>Como funciona

Numa implementação típica do servidor, um servidor funciona como o servidor de processamento e o servidor de consulta. Se o número de consultas de cliente em relação a modelos no seu servidor excede a consulta de processamento unidades (QPU) para o plano do seu servidor, ou se ocorrer o processamento do modelo ao mesmo tempo que cargas de trabalho de consulta elevado, pode diminuir o desempenho. 

Com o Escalamento horizontal, pode criar um conjunto de consulta com réplicas de consulta adicionais até sete (total de oito, incluindo o seu servidor). Pode aumentar o número de réplicas de consulta para satisfazer os pedidos QPU em alturas críticos e pode separar um servidor de processamento do conjunto de consulta em qualquer altura. 

Independentemente do número de réplicas de consulta que tiver de um conjunto de consulta, processamento de cargas de trabalho não são distribuídas em réplicas de consulta. Um único servidor funciona como o servidor de processamento. Réplicas de consulta servem apenas consultas nos modelos sincronizados entre cada réplica no agrupamento de consulta. 

Quando as operações de processamento estiverem concluídas, tem de ser efetuada uma sincronização entre o servidor de processamento e servidores de réplica de consulta. Ao automatizar as operações de processamento, é importante configurar uma operação de sincronização após a conclusão bem-sucedida da operações de processamento. Pode ser efetuada manualmente a sincronização no portal, ou utilizando o PowerShell ou a REST API.

> [!NOTE]
> Escalamento horizontal está disponível para os servidores no escalão de preço padrão. Cada réplica de consulta é faturada a taxa mesmo que o servidor.

> [!NOTE]
> Escalamento horizontal não aumente a quantidade de memória disponível para o servidor. Para aumentar a memória, terá de atualizar o seu plano.

## <a name="monitor-qpu-usage"></a>Monitorizar a utilização QPU

 Para determinar se escalável para o servidor é necessário, monitorize o servidor no portal do Azure utilizando as métricas. Se a sua QPU regularmente maxes terminar, significa que o número de consultas dos seus modelos está a exceder o limite QPU para o seu plano. Métrica de comprimento de fila de tarefa de conjunto de consulta também aumenta quando o número de consultas na fila do conjunto de thread de consulta excede QPU disponível. Para obter mais informações, consulte [monitorizar métricas do servidor](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Configurar Escalamento horizontal

### <a name="in-azure-portal"></a>No portal do Azure

1. No portal, clique em **escalável**. Utilize o controlo de deslize para selecionar o número de servidores de réplica de consulta. É o número de réplicas que escolher, para além do servidor existente.

2. No **separar o servidor de processamento de agrupamento de consulta**, selecione Sim para excluir o seu servidor de processamento de servidores de consulta.

   ![Controlo de deslize de escalamento horizontal](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Clique em **guardar** para aprovisionar os novos servidores de réplica de consulta. 

Modelos em tabela no seu servidor primário são sincronizados com os servidores de réplica. Quando a sincronização estiver concluída, o conjunto de consulta começa a distribuição de consultas recebidas entre os servidores de réplica. 


## <a name="synchronization"></a>Sincronização 

Quando aprovisionar novos réplicas de consulta, o Azure Analysis Services replica automaticamente seus modelos em todas as réplicas. Também pode efetuar uma sincronização manual utilizando o portal ou a REST API. Ao processar o seus modelos, deve efetuar uma sincronização para que as atualizações são sincronizadas entre as réplicas de consulta.

### <a name="in-azure-portal"></a>No portal do Azure

No **descrição geral** > modelo > **sincronizar modelo**.

![Controlo de deslize de escalamento horizontal](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>API REST
Utilize o **sincronização** operação.

#### <a name="synchronize-a-model"></a>Sincronizar um modelo   
`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Obter o estado de sincronização  
`GET https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

### <a name="powershell"></a>PowerShell
Para executar a sincronização a partir do PowerShell, [atualizar para a versão mais recente](https://github.com/Azure/azure-powershell/releases) módulo de AzureRM 5.01 ou superior. Utilize [sincronização AzureAnalysisServicesInstance](https://docs.microsoft.com/en-us/powershell/module/azurerm.analysisservices/sync-azureanalysisservicesinstance).

## <a name="connections"></a>Ligações

Na página de descrição geral do servidor, existem dois nomes de servidor. Se ainda não configurou ainda escalável para um servidor, ambos os nomes de servidor funcionam da mesma. Assim que configurar Escalamento horizontal para um servidor, terá de especificar o nome de servidor adequado dependendo do tipo de ligação. 

Utilizador final para ligações de cliente, como o Power BI Desktop, Excel e aplicações personalizadas, utilização **nome do servidor**. 

Para SSMS, SSDT e cadeias de ligação no PowerShell, utilizam aplicações de função do Azure e no AMO, **nome do servidor de gestão**. O nome do servidor de gestão inclui uma oferta especial de `:rw` qualificador (leitura / escrita). Todas as operações de processamento ocorrerem no servidor de gestão.

![Nomes de servidor](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="related-information"></a>Informações relacionadas

[Métricas do servidor de monitor](analysis-services-monitor.md)   
[Gerir serviços de análise do Azure](analysis-services-manage.md) 

