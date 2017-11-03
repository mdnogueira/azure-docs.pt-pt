---
title: "Dedicado a capacidade para tarefas de serviço de execução do Machine Learning Batch | Microsoft Docs"
description: "Descrição geral dos serviços do Azure Batch para tarefas de Machine Learning."
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: 
ms.service: machine-learning
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: v-donglo
ms.openlocfilehash: 40641910f904aa0c0516f96d84d0ebbbbe8e5501
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-batch-service-for-machine-learning-jobs"></a>Serviço de lote do Azure para as tarefas de Machine Learning

Processamento do Machine Learning conjunto do Batch fornece escala gerida pelo cliente para o serviço de execução de lote do Azure Machine Learning. O processamento de lote clássico para é de aprendizagem efetuada num ambiente multi-inquilino, que limita o número de tarefas simultâneas pode submeter e as tarefas são colocados em fila de forma primeiro na primeira fora. Este uncertainty significa que não é possível prever com exatidão quando a tarefa será executada.

Processamento de conjunto do batch permite-lhe criar conjuntos de onde pode submeter tarefas de lote. Controlar o tamanho do conjunto e, para que conjunto de trabalho for submetido. A tarefa de BES é executado no seu próprio espaço de processamento de fornecer o desempenho de processamento previsível e a capacidade de criar agrupamentos de recursos que correspondam à carga de processamento submeter.

## <a name="how-to-use-batch-pool-processing"></a>Como utilizar o processamento de conjunto do Batch

Configuração do processamento de conjunto do Batch não está atualmente disponível através do portal do Azure. Para utilizar o processamento de conjunto do Batch, tem de:

-   Chamar CSS para criar uma conta de conjunto do Batch e obter um URL de serviço do conjunto e uma chave de autorização
-   Criar um serviço web do novo Gestor de recursos com base e o plano de faturação

Para criar a sua conta, chamar o serviço de cliente da Microsoft e de suporte (CSS) e forneça o ID de subscrição. CSS irá trabalhar para determinar a capacidade adequada para o seu cenário. CSS, em seguida, configura a conta com o número máximo de conjuntos, que pode criar e o número máximo de máquinas virtuais (VMs) que pode colocar em cada agrupamento. Assim que a conta está configurada, são fornecidas o URL de serviço do conjunto e uma chave de autorização.

Após a sua conta está criada, utilize a chave de autorização e URL do serviço de agrupamento para executar operações de gestão de agrupamento no seu conjunto do Batch.

![Arquitetura do serviço de conjunto de batch.](./media/dedicated-capacity-for-bes-jobs/pool-architecture.png)

Criar conjuntos de chamando a operação Criar agrupamento o URL do serviço de agrupamento CSS que lhe são fornecidas. Quando cria um conjunto, especifique o número de VMs e o URL do swagger.json de um Gestor de recursos novo baseia serviço web Machine Learning. Este serviço web é fornecido para estabelecer a associação de faturação. O serviço de conjunto do Batch utiliza o swagger.json para associar o conjunto de um plano de faturação. Pode executar qualquer BES serviço web, ambos os novo Gestor de recursos com base e clássico, escolha no conjunto.

Pode utilizar qualquer serviço web do novo Gestor de recursos com base, mas tenha em atenção de que a faturação para as tarefas são cobradas conforme o plano de faturação associado a esse serviço. Poderá pretender criar um serviço web e um novo plano de faturação especificamente para o conjunto do Batch tarefas em execução.

Para obter mais informações sobre a criação de serviços web, consulte [implementar um serviço web do Azure Machine Learning](publish-a-machine-learning-web-service.md).

Assim que tiver criado um conjunto, submeter a tarefa de BES utilizando o URL de pedidos de Batch para o serviço web. Pode optar por submetê-las a um conjunto de ou para o processamento em lote clássico. Para submeter uma tarefa para processar o conjunto do Batch, adicione o seguinte parâmetro para o corpo de pedido de submissão da tarefa:

"AzureBatchPoolId": "&lt;agrupamento ID&gt;"

Se não adicionar o parâmetro, a tarefa é executada no ambiente de processo do batch clássico. Se o agrupamento tiver recursos disponíveis, a tarefa começa imediatamente a executar. Se o agrupamento de não ter recursos gratuitos, a tarefa é colocado em fila até está disponível um recurso.

Se achar que regularmente atingir a capacidade dos seus conjuntos e precisar de capacidade de aumento, pode chamar CSS e trabalhar com um representante para aumentar as quotas.

Exemplo de pedido:

https://ussouthcentral.Services.azureml.NET/subscriptions/80c77c7674ba4c8c82294c3b2957990c/Services/9fe659022c9747e3b9b7b923c3830623/Jobs?API-Version=2.0

```json
{

    "Input":{
    
        "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpoint
        =https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://zhguim
        l.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;;",
        
        "BaseLocation":null,
        
        "RelativeLocation":"testint/AdultCensusIncomeBinaryClassificationDataset.csv",
        
        "SasBlobToken":null
    
    },
    
    "GlobalParameters":{ },
    
    "Outputs":{
    
        "output1":{
        
            "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpo
            int=https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://sampleaccount.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;",
            "BaseLocation":null,
            "RelativeLocation":"testintoutput/testint\_results.csv",
            
            "SasBlobToken":null
        
        }
    
    },
    
    "AzureBatchPoolId":"8dfc151b0d3e446497b845f3b29ef53b"

}
```

## <a name="considerations-when-using-batch-pool-processing"></a>Considerações quando utilizar o processamento de conjunto do Batch

Processamento de conjunto do batch é um serviço de sujeito a faturação sempre ativado e que requer que a associar a um plano de faturação do Gestor de recursos com base. É-lhe faturado apenas para o número de horas de computação que do conjunto estiver em execução; independentemente do número de tarefas são executadas durante esse conjunto de tempo. Se criar um conjunto, é-lhe faturado durante as horas de computação de cada máquina virtual no conjunto até que o conjunto ser eliminado, mesmo que não existem tarefas de lote estão em execução no conjunto. Faturação para as máquinas virtuais é iniciado quando tiver terminado de aprovisionamento e para quando possam tem sido eliminados. Pode utilizar qualquer um dos planos encontrados no [página de preços do Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

Exemplo de faturação:

Se criar um conjunto do Batch com 2 máquinas virtuais e eliminá-lo após 24 horas o seu plano de faturação é debited 48 horas de computação; independentemente do número de tarefas foram executadas durante esse período.

Se criar um conjunto do Batch com 4 máquinas virtuais e eliminá-lo após 12 horas, o seu plano de faturação é também debited 48 horas de computação.

Recomendamos que consultar o estado da tarefa para determinar quando concluir tarefas. Quando tem concluído a todas as suas tarefas em execução, chame a operação de redimensionamento conjunto para definir o número de máquinas virtuais no agrupamento a zero. Se for abreviado nos recursos de agrupamento e terá de criar um novo conjunto, por exemplo bem ao faturar contra um plano de faturação, pode eliminar o conjunto em vez disso, quando tem concluído a todas as suas tarefas em execução.


| **Utilizar conjunto do Batch quando a processar**    | **Utilizar o batch clássico processamento quando**  |
|---|---|
|Tem de executar um grande número de tarefas<br>Ou<br/>É necessário saber que as tarefas serão executadas imediatamente<br/>Ou<br/>É necessário garantido débito. Por exemplo, terá de executar um número de tarefas num determinado período de tempo e pretende aumentar horizontalmente os recursos de computação para satisfazer as necessidades.    | Tarefas de alguns estão em execução<br/>E<br/> Não precisa de tarefas a executar imediatamente |
