---
title: "Executar cargas de trabalho do Azure Batch em VMs de prioridade baixa económicas | Microsoft Docs"
description: Saiba como aprovisionar VMs de prioridade baixa para reduzir o custo de cargas de trabalho do Azure Batch.
services: batch
author: mscurrell
manager: timlt
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 09/28/2017
ms.author: markscu
ms.openlocfilehash: b9e5181baedba7cc4783553221521f5b08a7bc4d
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="use-low-priority-vms-with-batch"></a>VMs de prioridade baixa utilização com o Batch

O Azure Batch proporciona baixa máquinas de virtuais (VMs) para reduzir o custo de cargas de trabalho do Batch. VMs de prioridade baixa tornar novos tipos de cargas de trabalho do Batch possíveis, fornecendo uma grande quantidade de capacidade de computação que também é económica.

Prioridade baixa VMs tirar partido da capacidade surplus no Azure. Quando especificar VMs de prioridade baixa nos seus conjuntos, o Azure Batch podem utilizar automaticamente esta surplus quando disponível.

O compromisso de utilização de VMs de prioridade baixa é que essas VMs podem ser impedidas quando não existem surplus capacidade está disponível no Azure. Por este motivo, as VMs de prioridade baixa são mais adequadas para determinados tipos de cargas de trabalho. Utilize as VMs de prioridade baixa para batch e cargas de trabalho de processamento assíncrono em que a hora de conclusão da tarefa é flexível e o trabalho é distribuído por várias VMs.

VMs de prioridade baixa são disponibilizadas um preço significativamente reduzida comparados com VMs dedicadas. Para detalhes de preços, consulte [preços do Batch](https://azure.microsoft.com/pricing/details/batch/).


> [!IMPORTANT]
> Prioridade baixa VMs estão atualmente disponíveis apenas para cargas de trabalho em execução no Batch. 
>
>

## <a name="use-cases-for-low-priority-vms"></a>Casos de utilização para VMs de baixa prioridade

Dada as características de VMs de prioridade baixa, cargas de trabalho que podem e não é possível utilizá-los? Em geral, as cargas de trabalho de processamento de lote são uma boa opção, como as tarefas são divididas em muitas tarefas paralelas ou existem muitas tarefas que são ampliadas e distribuídas por várias VMs.

-   Para maximizar a utilização da capacidade surplus no Azure, as tarefas adequadas podem aumentar horizontalmente.

-   Ocasionalmente, VMs podem não estar disponíveis ou são preemptadas, que resulta numa capacidade reduzida para tarefas e poderá provocar interrupções de tarefas e volta executar. As tarefas, por conseguinte, tem de ser flexíveis dentro do tempo que podem tomar para executar.

-   As tarefas com tarefas mais podem ser afetadas mais se interrompida. Se implementam a tarefas de ponto de verificação para guardar o progresso são executados execução longa, o impacto da interrupção é reduzido. Tarefas com os tempos de execução mais curtos tendem a funcionar melhor com VMs de prioridade baixa, porque o impacto da interrupção é muito inferior.

-   Trabalhos MPI de longa execução que utilizam várias VMs não são adequados para utilizar VMs de prioridade baixa, porque uma VM preemptada pode levar à ter de executar novamente a tarefa de toda.

Alguns exemplos de cenários de utilização de processamento de lote bem adequados ao utilizar VMs de prioridade baixa são:

-   **Desenvolvimento e teste**: em particular, se estiverem a ser desenvolvidas soluções em grande escala, podem realizadas reduções significativas. Todos os tipos de testes podem beneficiar, mas o teste de carga em grande escala e o teste de regressão são excelente utiliza.

-   **Complementar as capacidade a pedido**: VMs de prioridade baixa, podem ser utilizadas para complementar VMs dedicadas regular - se estiver disponível, as tarefas podem dimensionar e, por conseguinte, concluir mais rápida de custo mais baixo; se não estiver disponível, a linha de base de VMs dedicadas permanece disponível .

-   **Tempo de execução da tarefa flexível**: se houver flexibilidade das tarefas de tempo tem de concluir, em seguida, remoções potenciais de capacidade podem ser tolerated; no entanto, com a adição de VMs de prioridade baixa tarefas frequentemente executam mais rápida e para um custo mais baixo.

Conjuntos do batch podem ser configurados para utilizar VMs de prioridade baixa de algumas formas, consoante a flexibilidade no tempo de execução da tarefa:

-   VMs de prioridade baixa apenas podem ser utilizadas num conjunto. Neste caso, o Batch recupera qualquer capacidade preempted quando disponível. Esta configuração é a forma cheapest para executar tarefas, como VMs de prioridade baixa apenas são utilizadas.

-   Prioridade baixa VMs podem ser utilizadas em conjunto com uma linha de base fixo de VMs dedicadas. O número fixo de VMs dedicadas garante sempre há alguns capacidade para manter a evoluir uma tarefa.

-   Pode ser dinâmica mistura de VMs dedicadas e prioridade baixa, para que as VMs de prioridade baixa cheaper apenas são utilizadas quando disponível, mas as VMs dedicadas um preço completo são expandidas quando necessário. Esta configuração mantém uma quantidade mínima de capacidade disponível para manter as tarefas de evoluir.

## <a name="batch-support-for-low-priority-vms"></a>Suporte de batch para VMs de baixa prioridade

O Azure Batch permite várias capacidades de que o facilitam a consumir e beneficiar de VMs de prioridade baixa:

-   Conjuntos do batch podem conter VMs dedicadas e VMs de prioridade baixa. O número de cada tipo de VM pode ser especificado quando um conjunto é criado ou alterado em qualquer altura para um conjunto existente, utilizando a operação de redimensionamento explícita ou utilizando o dimensionamento automático. Submissão de tarefas e pode permanecem inalterado, independentemente dos tipos VM no conjunto. Também pode configurar um conjunto completamente utilizar VMs de prioridade baixa para executar tarefas cheaply como possíveis, mas giratórias VMS dedicadas, se a capacidade descerem abaixo de um limiar mínimo, para manter as tarefas em execução.

-   Conjuntos do batch procura automaticamente o número de destino de VMs de prioridade baixa. Se as VMs são preemptadas, o Batch tenta substituir a capacidade perdida e voltar para o destino.

-   Quando as tarefas são interrompidas, o Batch Deteta e requeues automaticamente as tarefas sejam executadas novamente.

-   VMs de prioridade baixa tem uma quota de vCPU separado, diferente para VMs dedicadas. 
    A quota para as VMs de prioridade baixa é superior à quota para VMs dedicadas, porque as VMs de prioridade baixa custo inferior. Para obter mais informações, consulte [serviço quotas e limites do Batch](batch-quota-limit.md#resource-quotas).    


## <a name="create-and-update-pools"></a>Criar e atualizar os agrupamentos

Um conjunto do Batch pode conter VMs dedicadas e baixa (também referidas como nós de computação). Pode definir o número de destino de nós de computação para VMs dedicadas e prioridade baixa. O número de destino de nós Especifica o número de VMs que pretende ter no conjunto.

Por exemplo, para criar um conjunto utilizar o serviço em nuvem do Azure VMs com um destino de 5 dedicado VMs e 20 VMs de prioridade baixa:

```csharp
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: "cspool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4") // WS 2012 R2
);
```

Para criar um agrupamento a utilizar máquinas virtuais do Azure (em VMs do Linux neste caso) com um destino de 5 dedicado VMs e 20 VMs de prioridade baixa:

```csharp
ImageReference imageRef = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "16.04.0-LTS",
    version: "latest");

// Create the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration("batch.node.ubuntu 16.04", imageRef);

pool = batchClient.PoolOperations.CreatePool(
    poolId: "vmpool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard\_D2\_v2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

Pode obter o número atual de nós para VMs dedicadas e prioridade baixa:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

Nós de agrupamento têm uma propriedade para indicar se o nó é uma VM dedicada ou baixa:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

Quando um ou mais nós num conjunto são preemptadas, uma operação de lista nós no conjunto devolve ainda em nós. O número atual de nós de prioridade baixa, permanece inalterado, mas os nós têm o respetivo estado definido como o **Preempted** estado. Batch tenta localizar a substituição VMs e, se tiver êxito, os nós percorrer **criar** e, em seguida, **inicial** Estados antes de se tornar disponível para execução de tarefas, tal como novos nós.

## <a name="scale-a-pool-containing-low-priority-vms"></a>Dimensionar um agrupamento que contém as VMs de baixa prioridade

Como com conjuntos consiste apenas em VMs dedicadas, é possível ao dimensionar a um agrupamento que contém as VMs de prioridade baixa, ao chamar o método de redimensionamento ou utilizando o dimensionamento automático.

A operação de redimensionamento de agrupamento demora um segundo parâmetro opcional que atualiza o valor de **targetLowPriorityNodes**:

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

A fórmula de dimensionamento automático do conjunto suporta VMs de prioridade baixa da seguinte forma:

-   Pode obter ou definir o valor da variável definidas pelo serviço **$TargetLowPriorityNodes**.

-   Pode obter o valor da variável definidas pelo serviço **$CurrentLowPriorityNodes**.

-   Pode obter o valor da variável definidas pelo serviço **$PreemptedNodeCount**. 
    Esta variável devolve o número de nós no Estado preempted e permite-lhe aumentar ou reduzir verticalmente o número de nós dedicado, dependendo do número de nós antecipada que não estão disponíveis.

## <a name="jobs-and-tasks"></a>Trabalhos e tarefas

Trabalhos e tarefas requerem pouca configuração adicional para nós de baixa prioridade o suporte só é o seguinte:

-   A propriedade de JobManagerTask de uma tarefa tem uma nova propriedade **AllowLowPriorityNode**. 
    Quando esta propriedade for verdadeira, a tarefa de gestão pode ser agendada num nó dedicado ou baixa. Se esta propriedade for falsa, a tarefa de gestão está agendada para apenas um nó dedicado.

-   Um [variável de ambiente](batch-compute-node-environment-variables.md) esteja disponível para uma aplicação de tarefa para determinar se está em execução num nó de prioridade baixa ou dedicado. A variável de ambiente é AZ_BATCH_NODE_IS_DEDICATED.

## <a name="handling-preemption"></a>Preempção de processamento

Ocasionalmente, poderão ser impedidas VMs; Quando preempção acontece, o Batch faz o seguinte:

-   As VMs preempted tem o respetivo estado atualizado para **Preempted**.
-   Se as tarefas foram em execução nas VMs nó antecipada, essas tarefas são colocadas em fila e execute novamente.
-   A VM é eliminada efetivamente, resultando em perda de todos os dados armazenados localmente na VM.
-   O conjunto continuamente tenta contactar o número de destino de nós de prioridade baixa disponíveis. Quando encontra-se a capacidade de substituição, os nós de manter os respetivos IDs, mas que são reinicializados passar **criar** e **inicial** Estados antes de serem disponíveis para o agendamento de tarefas.
-   Preempção contagens estão disponíveis como uma métrica no portal do Azure.

## <a name="metrics"></a>Métricas

Nova métrica está disponível no [portal do Azure](https://portal.azure.com) para nós de prioridade baixa. Estas métricas são:

- Número de nós de baixa prioridade
- Contagem de núcleos de baixa prioridade 
- Número de nós antecipada

Para ver as métricas no portal do Azure:

1. Navegue até à sua conta do Batch no portal e ver as definições para a sua conta do Batch.
2. Selecione **métricas** do **monitorização** secção.
3. Selecione as métricas pretendidos ao nível do **as métricas disponíveis** lista.

![Métricas de nós de baixa prioridade](media/batch-low-pri-vms/low-pri-metrics.png)

## <a name="next-steps"></a>Passos seguintes

* Leia a [Descrição geral da funcionalidade Batch para programadores](batch-api-basics.md), com informações essenciais para todos os utilizadores que se preparam para utilizar o Batch. O artigo contém informações mais detalhadas sobre recursos do serviço Batch, como conjuntos, nós, trabalhos e tarefas, e as várias funcionalidades de API que pode utilizar ao criar a sua aplicação Batch.
* Saiba mais sobre o [Ferramentas e APIs do Batch](batch-apis-tools.md) disponíveis para criação de soluções para o Batch.
