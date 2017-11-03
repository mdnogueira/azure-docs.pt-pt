---
title: "Dimensionar automaticamente de computação nós num conjunto do Azure Batch | Microsoft Docs"
description: "Ative o dimensionamento automático num agrupamento de nuvem para ajustar dinamicamente o número de nós de computação no conjunto."
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: c624cdfc-c5f2-4d13-a7d7-ae080833b779
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: multiple
ms.date: 06/20/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f0e49cd8a64a48c53f5b6104703164a597c797f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-automatic-scaling-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Criar uma fórmula de dimensionamento automática para o dimensionamento de nós de computação de um conjunto do Batch

O Azure Batch pode dimensionar automaticamente com base nos parâmetros que define os conjuntos. Com dimensionamento automático, Batch dinamicamente adiciona nós a um conjunto de aumento de pedidos de tarefa e remove nós de computação, conforme se diminuir. Pode poupar tempo e dinheiro ao ajustar automaticamente o número de nós de computação utilizada pela sua aplicação do Batch. 

Ativar o dimensionamento automático num conjunto de nós de computação ao associar com o mesmo um *fórmula de dimensionamento automático* que definir. O serviço Batch utiliza esta fórmula de dimensionamento automático para determinar o número de nós de computação que são necessárias para executar a carga de trabalho. Nós podem estar dedicados de nós de computação ou [nós de prioridade baixa](batch-low-pri-vms.md). Batch responde aos dados de métricas de serviço que são recolhidos periodicamente. Utilizando estes dados de métricas, o Batch ajusta o número de nós de computação no conjunto com base na sua fórmula e num intervalo configurável.

Pode ativar o dimensionamento automático quando é criado um conjunto, ou num conjunto existente. Também pode alterar uma fórmula existente num agrupamento que esteja configurado para efetuar o dimensionamento automático. Batch permite-lhe avaliar as fórmulas antes de lhes atribuir agrupamentos e para monitorizar o estado de execução de dimensionamento automático.

Este artigo aborda as várias entidades que compõem as fórmulas de dimensionamento automático, incluindo funções, operações, operadores e variáveis. Vamos discutir como obter várias computação recursos e tarefas métricas em lote. Pode utilizar estas métricas para ajustar o número de nós do conjunto com base no estado de tarefa e de utilização de recursos. Em seguida, dita como construir uma fórmula e ativar o dimensionamento automático num agrupamento através de REST do Batch e APIs de .NET. Por fim, vamos concluir com alguns fórmulas de exemplo.

> [!IMPORTANT]
> Quando cria uma conta do Batch, pode especificar o [configuração da conta](batch-api-basics.md#account), que determina se os conjuntos são atribuídos de uma subscrição do serviço Batch (predefinição) ou na sua subscrição de utilizador. Se tiver criado a sua conta do Batch com a configuração predefinida do serviço Batch, em seguida, a conta está limitada a um número máximo de núcleos que podem ser utilizadas para processamento. O serviço Batch dimensiona nós de computação apenas até esse limite core. Por este motivo, o serviço Batch, poderá não atingir o número de destino de nós de computação especificado por uma fórmula de dimensionamento automático. Consulte [Quotas e limites para o serviço Azure Batch](batch-quota-limit.md) para obter informações sobre a visualização e aumentar as quotas de conta.
>
>Se tiver criado a sua conta com a configuração de subscrição de utilizador, a conta de partilha na quota de núcleos da subscrição. Para obter mais informações, veja [Virtual Machines limits](../azure-subscription-service-limits.md#virtual-machines-limits) (Limites das Máquinas Virtuais), em [Azure subscription and service limits, quotas, and constraints](../azure-subscription-service-limits.md) (Limites, quotas e limitações das subscrições e serviços do Azure).
>
>

## <a name="automatic-scaling-formulas"></a>Fórmulas de dimensionamento automáticas
Uma fórmula de dimensionamento automática é um valor de cadeia que definir que contém um ou mais instruções. A fórmula de dimensionamento automático é atribuída a um conjunto [autoScaleFormula] [ rest_autoscaleformula] elemento (REST do Batch) ou [CloudPool.AutoScaleFormula] [ net_cloudpool_autoscaleformula] propriedade (.NET do Batch). O serviço Batch utiliza a fórmula para determinar o número de destino de nós de computação no conjunto para o próximo intervalo de processamento. A cadeia de fórmula não pode exceder os 8 KB, pode incluir até 100 instruções que são separadas por ponto e vírgula e podem incluir as quebras de linha e os seus comentários.

Pode considerar as fórmulas de dimensionamento automáticas como um lote de dimensionamento automático "idioma". As instruções fórmulas são formados livre expressões que podem incluir variáveis definidas pelo serviço (variáveis definidas pelo serviço Batch) e variáveis definidas pelo utilizador (variáveis por si). Estes podem desempenhar várias operações estes valores através da utilização de tipos incorporados, os operadores e funções. Por exemplo, uma instrução poderá efetuar o seguinte formato:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

As fórmulas geralmente contém várias instruções que executam operações nos valores que são obtidos nas instruções anteriores. Por exemplo, vamos obter primeiro um valor para `variable1`, em seguida, transmita-o para uma função para preencher `variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Inclua estas instruções na sua fórmula de dimensionamento automático para chegam a um número de destino de nós de computação. Nós dedicado e nós de baixa prioridade têm as suas próprias definições de destino, para que pode definir um destino para cada tipo de nó. Uma fórmula de dimensionamento automático pode incluir um valor de destino para nós dedicado, um valor de destino para nós de prioridade baixa ou ambos.

O número de nós de destino pode ser superior, inferior ou igual ao número atual de nós desse tipo no conjunto. Batch avalia fórmula de dimensionamento automático de um conjunto num intervalo específico (consulte [intervalos de dimensionamento automático](#automatic-scaling-interval)). Batch ajusta o número de destino de cada tipo de nó no conjunto para o número que especifica a fórmula de dimensionamento automático no momento da avaliação.

### <a name="sample-autoscale-formula"></a>Fórmula de dimensionamento automático de exemplo

Eis um exemplo de uma fórmula de dimensionamento automático que pode ser ajustado para funcionar com a maioria dos cenários. As variáveis `startingNumberOfVMs` e `maxNumberofVMs` no exemplo fórmula pode ser ajustada para as suas necessidades. Esta fórmula dimensiona nós dedicado, mas pode ser modificada para aplicar a nós de prioridade baixa de escala bem. 

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
```

Com esta fórmula de dimensionamento automático, o conjunto for criado inicialmente com uma única VM. O `$PendingTasks` métrica define o número de tarefas que estão em execução ou em fila. A fórmula localiza o número médio de tarefas pendentes na última segundos 180 e define o `$TargetDedicatedNodes` variável em conformidade. A fórmula assegura que o número de destino de nós dedicado nunca excede 25 VMs. Como são submetidas novas tarefas, o conjunto que cresce automaticamente. As tarefas são concluídas, VMs fiquem livre um por um e a fórmula de dimensionamento automático diminui o conjunto.

## <a name="variables"></a>Variáveis
Pode utilizar ambos **definidas pelo serviço** e **definido pelo utilizador** variáveis nas suas fórmulas de dimensionamento automático. As variáveis definidas pelo serviço são criadas serviço Batch. Algumas variáveis definidas pelo serviço são leitura e escrita, e algumas são só de leitura. Variáveis definidas pelo utilizador são variáveis que definir. Na fórmula de exemplo apresentada na secção anterior, `$TargetDedicatedNodes` e `$PendingTasks` são variáveis definidas pelo serviço. Variáveis `startingNumberOfVMs` e `maxNumberofVMs` são variáveis definidas pelo utilizador.

> [!NOTE]
> As variáveis definidas pelo serviço são sempre precedidas por um cifrão ($). Para variáveis definidas pelo utilizador, o cifrão é opcional.
>
>

As tabelas seguintes mostram as variáveis de leitura / escrita e só de leitura que são definidas pelo serviço Batch.

Pode obter e definir os valores destas variáveis definidas pelo serviço para gerir o número de nós de computação num conjunto de:

| Variáveis definidas pelo serviço de leitura-escrita | Descrição |
| --- | --- |
| $TargetDedicatedNodes |O número de destino dedicado de nós de computação para o conjunto. O número de nós dedicada é especificado como um destino porque um conjunto pode não conseguir sempre o número de nós pretendido. Por exemplo, se o número de destino de nós dedicada é modificado por uma edição de avaliação de dimensionamento automático, antes do conjunto atingiu o destino inicial, em seguida, o conjunto poderá não atingir o destino. <br /><br /> Um agrupamento de uma conta criada com a configuração do serviço Batch pode não conseguir o destino se o destino exceder uma quota de nó ou principal da conta de Batch. Um agrupamento de uma conta criada com a configuração de subscrição de utilizador não poderá alcançar o destino se o destino excede a quota de núcleos partilhado para a subscrição.|
| $TargetLowPriorityNodes |O número de destino de prioridade baixa nós de computação para o conjunto. O número de nós de prioridade baixa é especificado como um destino porque um conjunto pode não conseguir sempre o número de nós pretendido. Por exemplo, se o número de destino de nós de prioridade baixa modificação por uma edição de avaliação de dimensionamento automático antes do conjunto atingiu o destino inicial, em seguida, o conjunto poderá não atingir o destino. Um agrupamento de poderá também não conseguir o destino se o destino excede uma quota de nó ou principal da conta de Batch. <br /><br /> Para obter mais informações sobre nós de computação de prioridade baixa, consulte [utilizar VMs de prioridade baixa, com o Batch (pré-visualização)](batch-low-pri-vms.md). |
| $NodeDeallocationOption |A ação que ocorre quando nós de computação são removidos de um conjunto. Os valores possíveis são:<ul><li>**requeue**– termina tarefas imediatamente e coloca-as novamente na fila de tarefas, para que estes são reagendadas.<li>**terminar**– termina tarefas imediatamente e remove-os da fila de tarefas.<li>**taskcompletion**– aguarda atualmente em execução de tarefas a concluir e, em seguida, remove o nó do conjunto.<li>**retaineddata**– aguarda para todos os locais retidos de tarefa de dados no nó para ser limpa antes de remover o nó do conjunto.</ul> |

Pode obter o valor destas variáveis definidas pelo serviço efetuar ajustes baseiam-se nas métricas do serviço Batch:

| Variáveis definidas pelo serviço de só de leitura | Descrição |
| --- | --- |
| $CPUPercent |A percentagem média de utilização da CPU. |
| $WallClockSeconds |O número de segundos consumidos. |
| $MemoryBytes |O número médio de megabytes utilizados. |
| $DiskBytes |O número médio de gigabytes utilizada nos discos locais. |
| $DiskReadBytes |O número de bytes lidos. |
| $DiskWriteBytes |O número de bytes escritos. |
| $DiskReadOps |O número de operações de leitura do disco efetuadas. |
| $DiskWriteOps |A contagem de disco das operações de escrita efetuada. |
| $NetworkInBytes |O número de bytes de entrada. |
| $NetworkOutBytes |O número de bytes de saída. |
| $SampleNodeCount |A contagem de nós de computação. |
| $ActiveTasks |O número de tarefas que estão prontos para execução, mas ainda não estão em execução. A contagem de $ActiveTasks inclui todas as tarefas que estão no Estado ativo e cumpriu cujas dependências. Todas as tarefas que estejam no estado de Active Directory, mas não tem sido satisfeitas cujas dependências são excluídas da contagem de $ActiveTasks.|
| $RunningTasks |O número de tarefas num Estado de execução. |
| $PendingTasks |A soma de $ActiveTasks e $RunningTasks. |
| $SucceededTasks |O número de tarefas que foi concluído com êxito. |
| $FailedTasks |O número de tarefas que falharam. |
| $CurrentDedicatedNodes |O número atual de dedicado nós de computação. |
| $CurrentLowPriorityNodes |O número atual de baixa prioridade de processamento de nós, incluindo quaisquer nós que foram substituídas. |
| $PreemptedNodeCount | O número de nós no conjunto que estão num Estado substituído. |

> [!TIP]
> As variáveis só de leitura, definidas pelo serviço que são apresentadas na tabela anterior são *objetos* que fornecem vários métodos para aceder a dados associados a cada um. Para obter mais informações, consulte [obter dados de exemplo](#getsampledata) posteriormente neste artigo.
>
>

## <a name="types"></a>Tipos de
Estes tipos são suportados numa fórmula:

* duplo
* doubleVec
* doubleVecList
* Cadeia
* Timestamp – timestamp é uma estrutura composta que contém os seguintes membros:

  * ano
  * mês (1-12)
  * dia (1-31)
  * dia da semana (no formato de número; por exemplo, 1 para a segunda)
  * hora (no formato de número de 24 horas; por exemplo, 13 significa 1 PM)
  * minutos (00-59)
  * segundo (00-59)
* TimeInterval

  * TimeInterval_Zero
  * TimeInterval_100ns
  * TimeInterval_Microsecond
  * TimeInterval_Millisecond
  * TimeInterval_Second
  * TimeInterval_Minute
  * TimeInterval_Hour
  * TimeInterval_Day
  * TimeInterval_Week
  * TimeInterval_Year

## <a name="operations"></a>Operações
Estas operações são permitidas em tipos que estão listados na secção anterior.

| Operação | Operadores suportados | Tipo de resultado |
| --- | --- | --- |
| duplo *operador* duplo |+, -, *, / |duplo |
| duplo *operador* timeinterval |* |TimeInterval |
| doubleVec *operador* duplo |+, -, *, / |doubleVec |
| doubleVec *operador* doubleVec |+, -, *, / |doubleVec |
| TimeInterval *operador* duplo |*, / |TimeInterval |
| TimeInterval *operador* timeinterval |+, - |TimeInterval |
| TimeInterval *operador* timestamp |+ |carimbo de data/hora |
| Timestamp *operador* timeinterval |+ |carimbo de data/hora |
| Timestamp *operador* timestamp |- |TimeInterval |
| *operador*duplo |-, ! |duplo |
| *operador*timeinterval |- |TimeInterval |
| duplo *operador* duplo |<, <=, ==, >=, >, != |duplo |
| cadeia *operador* cadeia |<, <=, ==, >=, >, != |duplo |
| Timestamp *operador* timestamp |<, <=, ==, >=, >, != |duplo |
| TimeInterval *operador* timeinterval |<, <=, ==, >=, >, != |duplo |
| duplo *operador* duplo |&&, &#124;&#124; |duplo |

Ao testar um valor de duplo com um operador ternary (`double ? statement1 : statement2`), diferentes de zero é **verdadeiro**, sendo zero **falso**.

## <a name="functions"></a>Funções
Estes predefinidas **funções** estão disponíveis para utilização na definição de uma fórmula de dimensionamento automática.

| Função | Tipo de retorno | Descrição |
| --- | --- | --- |
| AVG(doubleVecList) |duplo |Devolve o valor médio para todos os valores de doubleVecList. |
| Len(doubleVecList) |duplo |Devolve o comprimento do vetor que é criado a partir de doubleVecList. |
| LG(Double) |duplo |Devolve o registo de base 2 do valor de duplo. |
| LG(doubleVecList) |doubleVec |Devolve o registo component-wise 2 base do doubleVecList. Um vec(double) tem de ser explicitamente transmitido para o parâmetro. Caso contrário, é assumida a versão de lg(double) duplo. |
| ln(Double) |duplo |Devolve o registo de natural do valor de duplo. |
| ln(doubleVecList) |doubleVec |Devolve o registo component-wise 2 base do doubleVecList. Um vec(double) tem de ser explicitamente transmitido para o parâmetro. Caso contrário, é assumida a versão de lg(double) duplo. |
| log(Double) |duplo |Devolve o registo de base 10 do valor de duplo. |
| log(doubleVecList) |doubleVec |Devolve o registo component-wise base 10 do doubleVecList. Um vec(double) tem de ser explicitamente transmitido para o parâmetro duplo único. Caso contrário, é assumida a versão de log(double) duplo. |
| Max(doubleVecList) |duplo |Devolve o valor máximo de doubleVecList. |
| min(doubleVecList) |duplo |Devolve o valor mínimo de doubleVecList. |
| NORM(doubleVecList) |duplo |Devolve o dois normal de vetor que é criado a partir de doubleVecList. |
| percentil (v doubleVec, duplo p) |duplo |Devolve o elemento de percentil do vetor de v. |
| rand() |duplo |Devolve um valor aleatório entre 0,0 e 1,0. |
| Range(doubleVecList) |duplo |Devolve a diferença entre os valores máximas e mínimo a doubleVecList. |
| Std(doubleVecList) |duplo |Devolve o desvio-padrão de exemplo dos valores no doubleVecList. |
| STOP() | |Interrompe a avaliação da expressão de dimensionamento automático. |
| SUM(doubleVecList) |duplo |Devolve a soma de todos os componentes do doubleVecList. |
| hora (dateTime da cadeia = "") |carimbo de data/hora |Devolve o carimbo de hora da hora atual se não existem parâmetros são transmitidos ou o carimbo de hora da cadeia de dateTime se ser transmitido. Formatos dateTime suportados são W3C DTF e 1123 de RFC. |
| valor (v doubleVec, duplo i) |duplo |Devolve o valor do elemento que se encontra na localização i no vetor v, com um índice de início de zero. |

Algumas das funções que são descritas na tabela anterior podem aceitar uma lista como um argumento. A lista de valores separados por vírgulas é qualquer combinação de *duplo* e *doubleVec*. Por exemplo:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

O *doubleVecList* é converter o valor para um único *doubleVec* antes de avaliação. Por exemplo, se `v = [1,2,3]`, em seguida, ao chamar `avg(v)` é equivalente a chamar `avg(1,2,3)`. Chamar `avg(v, 7)` é equivalente a chamar `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Obter dados de exemplo
As fórmulas de dimensionamento automático agirem sobre dados de métricas (exemplos) que são fornecidos pelo serviço Batch. Uma fórmula aumenta ou diminui o tamanho do conjunto com base nos valores que obtém a partir do serviço. As variáveis definidas pelo serviço que foram descritas anteriormente são objetos que fornecem vários métodos para aceder aos dados que está associados esse objeto. Por exemplo, a seguinte expressão mostra um pedido para obter os últimos cinco minutos de utilização da CPU:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Método | Descrição |
| --- | --- |
| GetSample() |O `GetSample()` método devolve um vetor de amostras de dados.<br/><br/>Um exemplo é 30 segundos, dos dados de métricas. Por outras palavras, exemplos são obtidos a cada 30 segundos. Mas conforme indicado abaixo, não existe um atraso entre quando uma amostra é recolhida e quando estiver disponível uma fórmula. Como tal, nem todas as amostras para um determinado período de tempo podem estar disponíveis para avaliação por uma fórmula.<ul><li>`doubleVec GetSample(double count)`<br/>Especifica o número de amostras para obter exemplos mais recentes que foram recolhidos.<br/><br/>`GetSample(1)`Devolve a última amostra disponível. Para as métricas como `$CPUPercent`, no entanto, isto não deve ser utilizado porque não é possível saber *quando* amostra foi recolhida. Poderá ser recente ou devido a problemas de sistema, poderá ser muito mais antigo. É melhor nestes casos, para utilizar um intervalo de tempo, conforme mostrado abaixo.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Especifica um intervalo de tempo de recolha de dados de exemplo. Opcionalmente, também especifica a percentagem de amostras que devem estar disponíveis no intervalo de tempo de pedido.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)`devolvam 20 amostras se não estiverem presentes no histórico do CPUPercent todas as amostras de últimos 10 minutos. Se o último minuto do histórico não estava disponível, no entanto, apenas 18 amostras seriam devolvidas. Neste caso:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)`irão falhar porque apenas 90 por cento dos exemplos de estão disponíveis.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)`seria concluída com êxito.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Especifica um intervalo de tempo de recolha de dados, com uma hora de início e de uma hora de fim.<br/><br/>Tal como mencionado acima, há um atraso entre quando uma amostra é recolhida e quando estiver disponível uma fórmula. Considere este atraso quando utiliza o `GetSample` método. Consulte `GetSamplePercent` abaixo. |
| GetSamplePeriod() |Devolve o período de amostras que foram executadas num conjunto de dados históricos de exemplo. |
| Existente |Devolve o número total de amostras no histórico de métrico. |
| HistoryBeginTime() |Devolve o carimbo de hora da amostra de dados disponíveis mais antigo para a métrica. |
| GetSamplePercent() |Devolve a percentagem de amostras que estão disponíveis para um determinado intervalo de tempo. Por exemplo:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Porque o `GetSample` método falhar se a percentagem de amostras devolvido é inferior ao `samplePercent` especificado, pode utilizar o `GetSamplePercent` método para verificar primeiro. Em seguida, pode efetuar uma ação alternativa se insuficientes exemplos não estiverem presentes, sem halting a avaliação de dimensionamento automática. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Amostras, percentagem de exemplo e o *GetSample()* método
O funcionamento essencial de uma fórmula de dimensionamento automático é obter dados de métricos de tarefa e de recursos e, em seguida, ajustar o tamanho do conjunto com base nesses dados. Como tal, é importante ter uma compreensão clara da forma como as fórmulas de dimensionamento automático interagem com os dados de métricas (exemplos).

**Amostras**

O serviço Batch periodicamente demora amostras de métricas de tarefas e de recursos e disponibiliza-los para as fórmulas de dimensionamento automático. Estes exemplos são gravados cada 30 segundos pelo serviço Batch. No entanto, há normalmente um atraso entre quando esses exemplos registados e quando são disponibilizadas (e pode ser lida por) as fórmulas de dimensionamento automático. Além disso, devido a vários fatores, tais como a rede ou outros problemas de infraestrutura, os exemplos podem não serem gravados para um determinado intervalo.

**Percentagem de exemplo**

Quando `samplePercent` é passado para o `GetSample()` método ou `GetSamplePercent()` se chama o método, _por cento_ refere-se para uma comparação entre o número total possível de amostras que são registadas pelo serviço Batch e o número de amostras que estão disponíveis para a fórmula de dimensionamento automático.

Vamos ver um timespan de 10 minutos como exemplo. Porque os exemplos são registados cada 30 segundos dentro de um timespan de 10 minutos, o número máximo total de amostras que são registadas pelo Batch seria 20 amostras (2 por minuto). No entanto, devido à latência inerente o mecanismo de relatórios e outros problemas no Azure, pode haver apenas 15 exemplos que estão disponíveis para a fórmula de dimensionamento automático para leitura. Por isso, por exemplo, para esse período de 10 minutos, apenas 75% do número total de amostras registadas poderão estar disponíveis para a sua fórmula.

**Intervalos de amostra GetSample()**

As fórmulas de dimensionamento automático que vão possível crescer e redução dos seus conjuntos &mdash; adicionar nós ou remover nós. Porque nós custo dinheiro, pretender certificar-se de que as fórmulas utilizem um método inteligente da análise que é baseada em dados suficientes. Por conseguinte, recomendamos que utilize uma análise de tendências-type nas suas fórmulas. Este tipo cresce e diminui os conjuntos com base num intervalo de amostras recolhidos.

Para tal, utilize `GetSample(interval look-back start, interval look-back end)` para devolver um vetor de amostras:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Quando a linha acima é avaliada por lote, devolve um intervalo de amostras como um vetor de valores. Por exemplo:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Depois de ter recolhido o vetor de amostras, em seguida, pode utilizar funções como `min()`, `max()`, e `avg()` para valores significativos de derivar de recolhidos intervalo.

Para segurança adicional, pode forçar uma avaliação de fórmula para falhar caso inferior a uma determinada percentagem de exemplo está disponível para um período de tempo específico. Quando forçar uma avaliação de fórmula falhar, instruir lote mais cessar a avaliação da fórmula se a percentagem especificada de exemplos não está disponível. Neste caso, não é efetuada nenhuma alteração para o tamanho do conjunto. Para especificar uma percentagem necessária de exemplos para a avaliação com êxito, especificá-lo como o terceiro parâmetro `GetSample()`. Aqui, é especificado um requisito de 75 por cento de amostras:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Porque poderá haver um atraso na disponibilidade de exemplo, é importante sempre especificar um intervalo de tempo com uma hora de início de aspeto-back que é mais antiga do que um minuto. -Demora cerca de um minuto para exemplos propagar através do sistema, por isso, amostras no intervalo `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` poderão não estar disponíveis. Novamente, pode utilizar o parâmetro de percentagem de `GetSample()` para forçar um requisito de percentagem de determinado exemplo.

> [!IMPORTANT]
> Iremos **vivamente recomendável** que **evitar a entidade confiadora *apenas* no `GetSample(1)` nas suas fórmulas de dimensionamento automático**. Isto acontece porque `GetSample(1)` essencialmente diz ao serviço do Batch, "Conceder-me a última de exemplo tem, independentemente do há quanto tempo a obter." Uma vez que é apenas uma amostra único e poderá ser um exemplo anterior, não pode ser representativo da imagem maior de tarefas recentes ou estado do recurso. Se utilizar `GetSample(1)`, certifique-se de que faz parte de uma instrução maior e não do ponto de dados apenas depende da sua fórmula.
>
>

## <a name="metrics"></a>Métricas
Pode utilizar as métricas de recurso e a tarefa quando estiver a definir uma fórmula. Ajustar o número de destino de nós dedicado em conjunto com base nos dados de métricas que obtenha e avaliar. Consulte o [variáveis](#variables) secção acima para obter mais informações sobre cada métrica.

<table>
  <tr>
    <th>Métrica</th>
    <th>Descrição</th>
  </tr>
  <tr>
    <td><b>Recurso</b></td>
    <td><p>Métricas de recurso baseiam-se na CPU, a largura de banda, a utilização da memória de nós de computação e o número de nós.</p>
        <p> Estas variáveis definidas pelo serviço são úteis para efetuar ajustes com base no número de nós:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$preemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Estas variáveis definidas pelo serviço são úteis para efetuar ajustes com base na utilização de recursos de nó:</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Tarefa</b></td>
    <td><p>Métricas de tarefas são baseadas no estado de tarefas, como ativa, pendente e concluídas. As seguintes variáveis definidas pelo serviço são úteis para efetuar ajustes de tamanho do conjunto com base nas métricas de tarefas:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Escrever uma fórmula de dimensionamento automático
Criar uma fórmula de dimensionamento automático ao que formam instruções que utilizam os componentes acima e combinar as instruções para uma fórmula concluída. Nesta secção, iremos criar uma fórmula de dimensionamento automático de exemplo que pode efetuar algumas decisões de dimensionamento do mundo real.

Em primeiro lugar, vamos definir os requisitos para o nosso novo fórmula de dimensionamento automático. A fórmula deve:

1. Aumente o número de destino dedicado de nós de computação num conjunto se a utilização da CPU é elevada.
2. Reduzir o número de destino de nós de computação dedicada de um conjunto quando a utilização da CPU é baixa.
3. Sempre restringir o número máximo de nós dedicado para 400.

Para aumentar o número de nós durante a utilização elevada da CPU, definir a instrução que preenche uma variável definida pelo utilizador (`$totalDedicatedNodes`) com um valor que seja 110 percentagem do número de destino atual de nós dedicado, mas apenas se a utilização da CPU média mínimo durante os últimos 10 minutos foi superior a 70 por cento. Caso contrário, utilize o valor para o número atual de nós dedicado.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Para *diminuir* o número de nós dedicado durante a utilização da CPU baixa, a seguinte instrução na nossa fórmula define o mesmo `$totalDedicatedNodes` variável a 90 por cento do número de destino atual de nós dedicado se a utilização média da CPU nos últimos 60 minutos em foi 20 por cento. Caso contrário, utilize o valor atual do `$totalDedicatedNodes` que iremos preenchidos na instrução acima.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Agora, limite o número de destino de nós de computação dedicada para um máximo de 400:

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Segue-se a fórmula concluída:

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

## <a name="create-an-autoscale-enabled-pool-with-net"></a>Criar um conjunto de dimensionamento automático preparado com o .NET

Para criar um conjunto com dimensionamento automático ativado no .NET, siga estes passos:

1. Criar o conjunto com [BatchClient.PoolOperations.CreatePool](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
2. Definir o [CloudPool.AutoScaleEnabled](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) propriedade `true`.
3. Definir o [CloudPool.AutoScaleFormula](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) propriedade com a fórmula de dimensionamento automático.
4. (Opcional) Definir o [CloudPool.AutoScaleEvaluationInterval](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) propriedade (a predefinição é 15 minutos).
5. Consolidar o conjunto com [CloudPool.Commit](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commit) ou [CommitAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

O fragmento de código seguinte cria um conjunto de dimensionamento automático preparado no .NET. A fórmula de dimensionamento automático do conjunto define o número de destino de nós dedicado a 5 em segundas e 1 em todos os outros dias da semana. O [intervalo de dimensionamento automático](#automatic-scaling-interval) está definido como 30 minutos. Neste e os outros c# fragmentos neste artigo, `myBatchClient` é uma instância de foi inicializada correctamente o [BatchClient] [ net_batchclient] classe.

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "small", // single-core, 1.75 GB memory, 225 GB disk
                    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));    
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> Quando cria um conjunto de dimensionamento automático preparado, não especifique o _targetDedicatedComputeNodes_ parâmetro ou _targetLowPriorityComputeNodes_ parâmetro na chamada para **CreatePool**. Em vez disso, especifique o **AutoScaleEnabled** e **AutoScaleFormula** propriedades no conjunto. Os valores para estas propriedades determinam o número de destino de cada tipo de nó. Além disso, para manualmente um dimensionamento automático com capacidade de redimensionamento conjunto (por exemplo, com [BatchClient.PoolOperations.ResizePoolAsync][net_poolops_resizepoolasync]), primeiro **desativar** dimensionamento automático no conjunto, em seguida, redimensioná-la.
>
>

Além de .NET do Batch, pode utilizar qualquer um dos outros [SDKs do Batch](batch-apis-tools.md#azure-accounts-for-batch-development), [REST do Batch](https://docs.microsoft.com/rest/api/batchservice/), [cmdlets do Batch PowerShell](batch-powershell-cmdlets-get-started.md)e o [Batch CLI](batch-cli-get-started.md) para configurar o dimensionamento automático.


### <a name="automatic-scaling-interval"></a>Intervalo de dimensionamento automático
Por predefinição, o serviço Batch ajusta o tamanho de um conjunto, de acordo com a fórmula de dimensionamento automático a cada 15 minutos. Este intervalo é configurável ao utilizar as seguintes propriedades do agrupamento:

* [CloudPool.AutoScaleEvaluationInterval] [ net_cloudpool_autoscaleevalinterval] (.NET do Batch)
* [autoScaleEvaluationInterval] [ rest_autoscaleinterval] (REST API)

O intervalo mínimo é de cinco minutos e o máximo é 168 horas. Se não for especificado um intervalo fora deste intervalo, o serviço Batch devolve um erro de pedido incorreto (400).

> [!NOTE]
> Dimensionamento automático não atualmente destina-se para responder a alterações em menos de um minuto, mas em vez disso, foi concebido para ajustar o tamanho do seu agrupamento gradualmente como executar uma carga de trabalho.
>
>

## <a name="enable-autoscaling-on-an-existing-pool"></a>Ativar o dimensionamento automático num conjunto existente

Cada SDK do Batch é uma forma para ativar o dimensionamento automático. Por exemplo:

* [BatchClient.PoolOperations.EnableAutoScaleAsync] [ net_enableautoscaleasync] (.NET do Batch)
* [Ativar o dimensionamento automático num agrupamento] [ rest_enableautoscale] (REST API)

Quando ativar o dimensionamento automático num conjunto existente, tenha em consideração os seguintes pontos:

* Se o dimensionamento automático está desativado no agrupamento de quando emitir o pedido para ativar o dimensionamento automático, tem de especificar uma fórmula de dimensionamento automático válido quando emitir o pedido. Opcionalmente, pode especificar um intervalo de avaliação de dimensionamento automático. Se não especificar um intervalo, é utilizado o valor predefinido de 15 minutos.
* Se o dimensionamento automático estiver ativado no conjunto, pode especificar uma fórmula de dimensionamento automático, um intervalo de avaliação ou ambos. Tem de especificar pelo menos uma destas propriedades.

  * Se especificar um novo intervalo de avaliação de dimensionamento automático, em seguida, a agenda de avaliação existente está parada e é iniciada uma nova agenda. Hora de início da nova agenda é o tempo no qual o pedido para ativar o dimensionamento automático foi emitido.
  * Se omitir a fórmula de dimensionamento automático ou intervalo de avaliação, o serviço Batch continua a utilizar o valor atual dessa definição.

> [!NOTE]
> Se tiver especificado os valores para o *targetDedicatedComputeNodes* ou *targetLowPriorityComputeNodes* parâmetros a **CreatePool** método quando criou o conjunto no .NET, ou para os parâmetros comparáveis noutro idioma, em seguida, esses valores são ignoradas quando for avaliada a fórmula de dimensionamento automática.
>
>

Este fragmento de código c# utiliza o [.NET do Batch] [ net_api] biblioteca para ativar o dimensionamento automático num conjunto existente:

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Atualizar uma fórmula de dimensionamento automático

Para atualizar a fórmula num conjunto preparado para dimensionamento automático existente, chame a operação para ativar o dimensionamento automático novamente com a fórmula de novo. Por exemplo, se o dimensionamento automático já está ativado na `myexistingpool` quando o seguinte código de .NET é executado, a fórmula de dimensionamento automático é substituída com o conteúdo do `myNewFormula`.

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Atualizar o intervalo de dimensionamento automático

Para atualizar o intervalo de avaliação de dimensionamento automático de um conjunto de capacidade de dimensionamento automático de mensagens em fila existente, chame a operação para ativar o dimensionamento automático novamente com o intervalo de novo. Por exemplo, para definir o intervalo de avaliação de dimensionamento automático para 60 minutos para um conjunto que já está ativada para dimensionamento automático no .NET:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Avaliar uma fórmula de dimensionamento automático

Pode avaliar uma fórmula antes de aplicá-la a um conjunto. Desta forma, pode testar a fórmula para ver como o respetivas instruções avaliar antes de colocar a fórmula em produção.

Avaliar uma fórmula de dimensionamento automático, primeiro tem de ativar o dimensionamento automático num agrupamento com uma fórmula válida. Para testar uma fórmula num agrupamento que ainda não tem o dimensionamento automático ativado, utilize a fórmula de uma linha `$TargetDedicatedNodes = 0` quando ativar primeiro o dimensionamento automático. Em seguida, utilize um dos seguintes para avaliar a que pretende testar a fórmula:

* [BatchClient.PoolOperations.EvaluateAutoScale](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) ou [EvaluateAutoScaleAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Estes métodos de .NET do Batch requerem o ID de um conjunto existente e uma cadeia contendo a fórmula de dimensionamento automático a avaliar.

* [Avaliar uma fórmula de dimensionamento automática](https://docs.microsoft.com/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    Neste pedido de REST API, especifique o ID do conjunto no URI e a fórmula de dimensionamento automático no *autoScaleFormula* elemento de corpo do pedido. A resposta da operação contém quaisquer informações de erro que possam estar relacionados com a fórmula.

Neste [.NET do Batch] [ net_api] fragmento de código, iremos avaliar uma fórmula de dimensionamento automático. Se o conjunto não tem o dimensionamento automático ativado, iremos ativá-la primeiro.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = await batchClient.PoolOperations.GetPoolAsync("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    await pool.EnableAutoScaleAsync(
        autoscaleFormula: "$TargetDedicatedNodes = {pool.CurrentDedicatedNodes};",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScaleAsync calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    await pool.RefreshAsync();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this code does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        await batchClient.PoolOperations.EvaluateAutoScaleAsync(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        await batchClient.PoolOperations.EnableAutoScaleAsync(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

Avaliação com êxito de fórmula mostrada neste fragmento de código produz resultados semelhantes a:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Obter informações sobre a execução de dimensionamento automático

Para garantir a sua fórmula está a funcionar conforme esperado, é recomendável verificar periodicamente os resultados das execuções de dimensionamento automático Batch executa no seu conjunto. Por isso, obter (ou Atualize) uma referência ao agrupamento e examinar as propriedades de dimensionamento automático sua última execução.

No .NET do Batch, o [CloudPool.AutoScaleRun](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) propriedade tem várias propriedades que fornecem informações sobre o mais recente dimensionamento automático executar executadas no agrupamento de:

* [AutoScaleRun.Timestamp](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
* [AutoScaleRun.Results](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.results)
* [AutoScaleRun.Error](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.error)

Na REST API, o [obter informações sobre um conjunto](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) pedido devolve informações sobre o agrupamento, que inclui o dimensionamento automático mais recente executadas informações [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool#bk_autrun) propriedade.

O seguinte fragmento de código do c# utiliza a biblioteca .NET do Batch para obter informações sobre o dimensionamento último automático executar num agrupamento de impressão _myPool_:

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Saída de exemplo de fragmento anterior:

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicatedNodes=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="example-autoscale-formulas"></a>Fórmulas de dimensionamento automático de exemplo
Vamos ver alguns fórmulas que apresentam diferentes formas para ajustar a quantidade de recursos de computação num conjunto.

### <a name="example-1-time-based-adjustment"></a>Exemplo 1: Ajuste baseados no tempo
Suponha que pretende ajustar o tamanho do conjunto com base no dia da semana e hora do dia. Este exemplo mostra como aumentar ou reduzir o número de nós no conjunto em conformidade.

A fórmula primeiro obtém a hora atual. Se se tratar de um dia da semana (1-5) e dentro do horário de trabalho (8 AM para as 18: 00), o tamanho do conjunto de destino está definido para 20 nós. Caso contrário, é definido para 10 nós.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>Exemplo 2: Ajuste baseado em tarefas
Neste exemplo, o tamanho do conjunto é ajustado com base no número de tarefas na fila. Os comentários e quebras de linha são aceitáveis nas cadeias de fórmulas.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicatedNodes/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicatedNodes = max(0, min($targetVMs, 20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Exemplo 3: Gestão de contas para tarefas paralelas
Neste exemplo ajusta o tamanho do conjunto com base no número de tarefas. Esta fórmula também tem em consideração o [MaxTasksPerComputeNode] [ net_maxtasks] valor foi definido para o conjunto. Esta abordagem é útil em situações onde [tarefas execução paralela](batch-parallel-node-tasks.md) foi ativado no seu conjunto.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the
// MaxTasksPerComputeNode property on this pool is set to 4, adjust this number
// for your use case)
$cores = $TargetDedicatedNodes * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicatedNodes + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicatedNodes = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>Exemplo 4: Definir um tamanho de conjunto inicial
Este exemplo mostra um fragmento de código c# com uma fórmula de dimensionamento automático que define o tamanho do conjunto a um número especificado de nós para um período de tempo inicial. Em seguida, que ajusta o tamanho do conjunto com base no número de execução e Active Directory tarefas depois de decorrido o período de tempo inicial.

A fórmula no fragmento de código seguinte:

* Define o tamanho do conjunto inicial para quatro nós.
* Não é ajustar o tamanho do conjunto a primeira 10 minutos do ciclo de vida do conjunto.
* Após 10 minutos, obtém o valor máximo do número de tarefas em execução e Active Directory nos últimos 60 minutos.
  * Se ambos os valores são 0 (com a indicação de que não existem tarefas foram em execução ou Active Directory nos últimos 60 minutos), o tamanho do conjunto é definido para 0.
  * Se o valor é maior que zero, não é efetuada nenhuma alteração.

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicatedNodes = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicatedNodes = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicatedNodes) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>Passos seguintes
* [Maximizar a utilização de recursos de computação do Azure Batch com tarefas de nó simultâneas](batch-parallel-node-tasks.md) contém detalhes sobre como pode executar várias tarefas em simultâneo em nós de computação do conjunto. Para além de dimensionamento automático, esta funcionalidade pode ajudar a reduzir a duração de tarefa para algumas cargas de trabalho, poupando dinheiro.
* Para outro booster de eficiência, certifique-se de que a aplicação do Batch consulta o serviço Batch da forma ideal. Consulte [consultar o serviço Azure Batch de forma eficiente](batch-efficient-list-queries.md) para aprender a limitar a quantidade de dados que atravesse encriptado quando consultar o estado de potencialmente milhares de nós de computação ou tarefas.

[net_api]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch
[net_batchclient]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient
[net_cloudpool_autoscaleformula]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula
[net_cloudpool_autoscaleevalinterval]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval
[net_enableautoscaleasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync
[net_maxtasks]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.maxtaskspercomputenode
[net_poolops_resizepoolasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync

[rest_api]: https://docs.microsoft.com/rest/api/batchservice/
[rest_autoscaleformula]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
[rest_autoscaleinterval]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
[rest_enableautoscale]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
