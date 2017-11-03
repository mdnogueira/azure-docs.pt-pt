---
title: "Avaliar as aplicações de Service Fabric com a análise de registos com o portal do Azure | Microsoft Docs"
description: "Pode utilizar a solução de Service Fabric no Log Analytics no portal do Azure para avaliar o risco e o funcionamento das suas aplicações Service Fabric, microserviços, nós e clusters."
services: log-analytics
documentationcenter: 
author: niniikhena
manager: jochan
editor: 
ms.assetid: 9c91aacb-c48e-466c-b792-261f25940c0c
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: nini
ms.openlocfilehash: 8c564c0dcbb2f9be286917b2f4d8a40da5406fae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="assess-service-fabric-applications-and-micro-services-with-the-azure-portal"></a>Avalie as aplicações de Service Fabric e microserviços com o portal do Azure

> [!div class="op_single_selector"]
> * [Resource Manager](log-analytics-service-fabric-azure-resource-manager.md)
> * [PowerShell](log-analytics-service-fabric.md)
>
>

![Símbolo de Service Fabric](./media/log-analytics-service-fabric/service-fabric-assessment-symbol.png)

Este artigo descreve como utilizar a solução de Service Fabric na análise de registos para ajudar a identificar e resolver problemas no seu cluster de Service Fabric.

A solução de Service Fabric utiliza dados de diagnóstico do Azure da sua VMs de recursos de infraestrutura de serviço, através da recolha de dados de tabelas do Azure WAD. Análise de registos, em seguida, lê os eventos de estrutura de Service Fabric, incluindo **fiável de serviço de eventos**, **eventos de Ator**, **eventos operacionais**, e **Eventos ETW personalizada**. Com o dashboard de solução, que é possível ver problemas relevantes e eventos relevantes no seu ambiente do Service Fabric.

Para começar a utilizar com a solução, terá de ligar o seu cluster do Service Fabric para uma área de trabalho de análise de registos. Seguem-se três cenários a ter em consideração:

1. Se não tiver implementado o cluster do Service Fabric, utilize os passos em ***implementar um Cluster do Service Fabric ligado a uma área de trabalho de análise de registos*** para implementar um novo cluster e tiver configurado para relatar para análise de registos.
2. Se pretender recolher contadores de desempenho a partir dos anfitriões para utilizar outras soluções OMS, tais como a segurança no seu Cluster do Service Fabric, siga os passos no ***implementar um Cluster do Service Fabric ligado a uma área de trabalho de análise de registos com a extensão da VM instalado.***
3. Se já tiver implementado o cluster do Service Fabric e pretende estabelecer a ligação à análise de registos, siga os passos no ***adicionar uma conta de armazenamento existente para análise de registos.***

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace"></a>Implemente um Cluster do Service Fabric ligado a uma área de trabalho de análise de registos.
Este modelo faz o seguinte:

1. Implementa um cluster do Service Fabric do Azure já tiver ligado a uma área de trabalho de análise de registos. Tem a opção para criar uma nova área de trabalho ao implementar o modelo ou introduza o nome de uma área de trabalho de análise de registos já existente.
2. Adiciona a conta de armazenamento de diagnóstico para a área de trabalho de análise de registos.
3. Permite que a solução de Service Fabric na sua área de trabalho de análise de registos.

[![Implementar no Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-oms%2F%2Fazuredeploy.json)

Depois de selecionar o botão implementar acima, abre o portal do Azure com os parâmetros para editar. É necessário se introduzir um novo nome de área de trabalho de análise de registos, crie um novo grupo de recursos:

![Service Fabric](./media/log-analytics-service-fabric/2.png)

![Service Fabric](./media/log-analytics-service-fabric/3.png)

Aceite os termos legais e clique em **criar** para iniciar a implementação. Depois de concluída a implementação, deverá ver a nova área de trabalho e cluster criada e a WADServiceFabric * tabelas de eventos, WADWindowsEventLogs e WADETWEvent adicionadas:

![Service Fabric](./media/log-analytics-service-fabric/4.png)

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace-with-vm-extension-installed"></a>Implemente um Cluster do Service Fabric ligado a uma área de trabalho de análise de registos com a extensão da VM instalado.

Este modelo faz o seguinte:

1. Implementa um cluster do Service Fabric do Azure já tiver ligado a uma área de trabalho de análise de registos. Pode criar uma área de trabalho nova ou utilize uma já existente.
2. Adiciona as contas de armazenamento de diagnóstico para a área de trabalho de análise de registos.
3. Permite que a solução de Service Fabric na área de trabalho de análise de registos.
4. Instala a extensão de agente MMA em cada conjunto no seu cluster do Service Fabric de dimensionamento de máquina virtual. Com o agente MMA instalado, conseguir ver as métricas de desempenho sobre os nós.

[![Implementar no Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-vmss-oms%2F%2Fazuredeploy.json)

Seguir os mesmos passos acima, introduza os parâmetros necessários e iniciar uma implementação. Novamente, deve ver a nova área de trabalho do cluster e tabelas WAD criadas todas as:

![Service Fabric](./media/log-analytics-service-fabric/5.png)

### <a name="viewing-performance-data"></a>Visualizar dados de desempenho

Para ver dados de desempenho a partir dos seus nós:


[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

- Inicie a área de trabalho de análise de registos do portal do Azure.
  ![Service Fabric](./media/log-analytics-service-fabric/6.png)
- Aceda a definições no painel da esquerda e selecione >> contadores de desempenho do Windows >> "Adicionar os contadores de desempenho selecionado": ![Service Fabric](./media/log-analytics-service-fabric/7.png)
- No registo de pesquisa, utilize as seguintes consultas para delve para as métricas-chave sobre os nós:

    a. Comparar a utilização da CPU média em todos os seus nós na última hora de um para ver quais os nós estão a ter problemas e num intervalo de tempo que um nó tinha um pico de pedidos:

    ```
    Type=Perf ObjectName=Processor CounterName="% Processor Time"|measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/10.png)

    b. Ver gráficos semelhante para a memória disponível em cada nó com esta consulta:

    ```
    Type=Perf ObjectName=Memory CounterName="Available MBytes Memory" | measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    Para ver uma lista de todos os seus nós, que mostra o valor médio exato de MBytes disponíveis para cada nó, utilize esta consulta:

    ```
    Type=Perf (ObjectName=Memory) (CounterName="Available MBytes") | measure avg(CounterValue) by Computer
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/11.png)

    c. No caso de que pretende abrir subdiretórios de um nó específico, examinando o média por hora, a utilização de CPU mínima, máxima e percentil de 75, está conseguir fazê-lo utilizando esta consulta (substituir o campo de computador):

    ```
    Type=Perf CounterName="% Processor Time" InstanceName=_Total Computer="BaconDC01.BaconLand.com"| measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/12.png)

Ler mais informações sobre as métricas de desempenho na análise de registos no [blogue do Operations Management Suite](https://blogs.technet.microsoft.com/msoms/tag/metrics/).


## <a name="adding-an-existing-storage-account-to-log-analytics"></a>Adicionar uma conta de armazenamento existente para análise de registos

Este modelo simplesmente adiciona as contas de armazenamento existente para uma área de trabalho de análise de registos de nova ou existente.

[![Implementar no Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Foms-existing-storage-account%2Fazuredeploy.json)

> [!NOTE]
> Na seleção de um grupo de recursos, se estiver a trabalhar com uma área de trabalho de análise de registos já existente, selecione "Utilizar existentes" e procure o grupo de recursos que contém a área de trabalho de análise de registos. Criar um novo um se; caso contrário.
> ![Service Fabric](./media/log-analytics-service-fabric/8.png)
>
>

Depois de implementar este modelo, será capaz de ver a conta de armazenamento ligada à sua área de trabalho de análise de registos. Neste caso, posso adicionar uma conta de armazenamento mais para a área de trabalho do Exchange que criei acima.
![Service Fabric](./media/log-analytics-service-fabric/9.png)

## <a name="view-service-fabric-events"></a>Ver eventos de Service Fabric

Depois de concluídas as implementações e a solução de Service Fabric foi ativada na sua área de trabalho, selecione o **Service Fabric** mosaico no portal do Log Analytics para iniciar o dashboard de Service Fabric. O dashboard inclui as colunas da tabela seguinte. Cada coluna apresenta os eventos principais de 10 por contagem correspondentes aos critérios dessa coluna para o intervalo de tempo especificado. Pode executar uma pesquisa de registo que fornece a lista completa clicando **ver todos os** na parte inferior direita de cada coluna ou ao clicar no cabeçalho da coluna.

| **Evento de Service Fabric** | **Descrição** |
| --- | --- |
| Problemas Relevantes |Uma apresentação de problemas, tais como RunAsyncFailures RunAsynCancellations e nó pendentes. |
| Eventos operacionais |Eventos operacionais relevantes, tais como a atualização da aplicação e implementações. |
| Eventos do serviço fiável |Eventos relevantes serviço fiável essas um Runasyncinvocations. |
| Eventos de ator |Eventos de ator acentuadas gerados pelo seu microserviços, tais como exceções acionadas por um método de ator, ativações de ator e deactivations e assim sucessivamente. |
| Eventos de aplicações |Todos os ETW eventos personalizados gerados pelas suas aplicações. |

![Dashboard de Service Fabric](./media/log-analytics-service-fabric/sf3.png)

![Dashboard de Service Fabric](./media/log-analytics-service-fabric/sf4.png)

A tabela seguinte mostra os métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para o Service Fabric.

| Plataforma | Direcionar o agente | Agente do Operations Manager | Storage do Azure | O Operations Manager necessárias? | Dados de agente do Operations Manager enviados através do grupo de gestão | Frequência de recolha |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |  |  | &#8226; |  |  |10 minutos |

> [!NOTE]
> Pode alterar o âmbito destes eventos na solução de Service Fabric clicando **dados com base nos últimos 7 dias** na parte superior do dashboard. Também pode mostrar eventos gerados dentro os últimos sete dias, um dia ou seis horas. Em alternativa, pode selecionar **personalizado** para especificar um intervalo de datas personalizado.
>
>

## <a name="next-steps"></a>Passos seguintes

* Utilize [pesquisas de registo na análise de registos](log-analytics-log-searches.md) para ver os dados de evento detalhados do Service Fabric.
