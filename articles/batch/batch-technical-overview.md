---
title: "O Azure Batch executa soluções de computação paralelas em grande escala na cloud | Documentos do Microsoft"
description: "Saiba como utilizar o serviço Azure Batch para cargas de trabalho em larga e escala e de HPC"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 93e37d44-7585-495e-8491-312ed584ab79
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/05/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a99f96db0c1e8bcd0cf29c564e5badf0eb728e56
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="run-intrinsically-parallel-workloads-with-batch"></a>Executar cargas de trabalho paralelas intrinsecamente com o Batch

O Azure Batch é uma plataforma para executar aplicações de computação de alto desempenho (HPC) e paralelas em larga escala, de forma eficaz na cloud. O Azure Batch agenda trabalho de computação intensivo para ser executado numa coleção gerida de máquinas virtuais, e pode dimensionar automaticamente recursos de computação para satisfazer as necessidades das suas tarefas.

Com o Azure Batch, pode definir facilmente os recursos de computação do Azure para executar as aplicações em paralelo e com dimensionamento. Não precisa de criar, configurar e gerir at manualmente um cluster HPC, máquinas virtuais individuais, redes virtuais ou uma tarefa complexa e a infraestrutura de agendamento de tarefas. O Azure Batch automatiza ou simplifica estas tarefas por si.

## <a name="use-cases-for-batch"></a>Casos de utilização do Batch
O Batch é um serviço gerido do Azure, que é utilizado para *processamento em lotes* ou *computação em batch*, executando um grande volume de tarefas semelhantes para obter o resultado pretendido. A computação em batch é utilizada frequentemente por organizações que processam, transformam e analisam regularmente grandes volumes de dados.

O Batch funciona bem com aplicações e cargas de trabalho intrinsecamente paralelas (também conhecidas como constrangedoramente paralelas). As cargas de trabalho intrinsecamente paralelas são as que são facilmente divididas em várias tarefas que realizam tarefas simultaneamente em vários computadores.

![Tarefas paralelas][1]<br/>

Alguns exemplos de cargas de trabalho que são normalmente processadas com esta técnica são:

* Modelação de riscos financeiros
* Análise de dados climáticos e de hidrologia
* Composição, análise e processamentos de imagens
* Codificação e transcodificação de suporte de dados
* Análise de sequência genética
* Análise de tensão de engenharia
* Testes de software

O Batch também pode efetuar cálculos paralelos com um passo reduzido no final e executar cargas de trabalho HPC mais complexas, como aplicações de [Message Passing Interface (MPI)](batch-mpi.md).

Para uma comparação entre o Batch e outras opções de solução HPC no Azure, veja [HPC, Batch, and Big Compute solutions](../virtual-machines/linux/high-performance-computing.md) (Soluções de HPC, Batch e Macrocomputação).

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="scenario-scale-out-a-parallel-workload"></a>Cenário: aumentar horizontalmente uma carga de trabalho paralela
Uma solução comum que utiliza as APIs do Batch para interagir com o serviço Batch envolve aumentar horizontalmente o trabalho intrinsecamente paralelo - como a composição de imagens de cenas 3D - num conjunto de nós de computação. Este conjunto de nós de computação pode ser o "farm de composição" que fornece dezenas, centenas ou mesmo milhares de núcleos para a tarefa de composição, por exemplo.

O diagrama seguinte mostra um fluxo de trabalho comum do Batch, com uma aplicação de cliente ou serviço alojado, utilizando o Batch para executar uma carga de trabalho paralela.

![Fluxo de trabalho de solução Batch][2]

Neste cenário comum, a aplicação ou o serviço processa uma carga de trabalho computacional no Azure Batch, efetuando os seguintes passos:

1. Carrega os **ficheiros de entrada** e a **aplicação** que processará esses ficheiros para a sua conta de Armazenamento do Azure. Os ficheiros de entrada podem ser quaisquer dados que a sua aplicação irá processar, tais como dados de modelação financeira ou ficheiros de vídeo para transcodificação. Os ficheiros de aplicação podem ser de qualquer aplicação que é utilizada para processar os dados, tais como uma aplicação de composição 3D ou um transcodificador de suporte de dados.
2. Crie um **conjunto** de nós de computação do Batch na sua conta do Batch - estes nós são as máquinas virtuais que irão executar as tarefas. Especifique as propriedades, como o [tamanho do nó](../cloud-services/cloud-services-sizes-specs.md), o sistema operativo e a localização no armazenamento do Azure da aplicação a instalar quando os nós se associam ao conjunto (a aplicação que carregou no passo 1). Também pode configurar o conjunto para [dimensionar automaticamente](batch-automatic-scaling.md) em resposta à carga de trabalho que as suas tarefas geram. O autodimensionamento ajusta dinamicamente o número de nós de computação no conjunto.
3. Crie um **trabalho** do Batch para executar a carga de trabalho no conjunto de nós de computação. Quando cria um trabalho, associa-o a um conjunto do Batch.
4. Adicionar **tarefas** ao trabalho. Ao adicionar tarefas a um trabalho, o serviço Batch agenda automaticamente as tarefas para execução nos nós de computação do conjunto. Cada tarefa utiliza a aplicação que carregou para processar os ficheiros de entrada.
   
   * 4a. Antes de uma tarefa ser executada, pode transferir os dados (os ficheiros de entrada) a processar para o nó de computação ao qual foram atribuídos. Se a aplicação ainda não tiver sido instalada no nó (ver passo 2), pode ser transferida aqui em vez disso. Quando as transferências estiverem concluídas, as tarefas são executadas nos respetivos nós atribuídos.
5. À medida que as tarefas são executadas, pode consultar o Batch para monitorizar o progresso do trabalho e respetivas tarefas. A aplicação ou serviço de cliente comunica com o serviço Batch sobre o HTTPS. Uma vez que pode monitorizar milhares de tarefas em execução em milhares de nós de computação, certifique-se de que [consulta o serviço de Batch de forma eficiente](batch-efficient-list-queries.md).
6. À medida que as tarefas são concluídas, elas podem carregar os respetivos dados de resultados para o Armazenamento do Azure. Também pode obter ficheiros diretamente do sistema de ficheiros num nó de computação.
7. Quando a monitorização deteta que concluiu as tarefas no seu trabalho, a aplicação ou serviço de cliente pode transferir os dados de saída para processamento adicional ou avaliação.

Tenha em atenção que esta é apenas uma forma de utilizar o Batch, e este cenário descreve apenas algumas das funcionalidades disponíveis. Por exemplo, pode executar [várias tarefas em paralelo](batch-parallel-node-tasks.md) em cada nó de computação, e pode utilizar a [ preparação de trabalhos e tarefas de conclusão](batch-job-prep-release.md) para preparar nós para os trabalhos e limpar posteriormente.

## <a name="next-steps"></a>Passos seguintes
Agora que tem um elevado nível de descrição geral do serviço Batch, está na altura de aprofundar para saber como pode utilizá-lo para processar cargas de trabalho de computação intensivas e paralelas.

* Leia a [Descrição geral da funcionalidade Batch para programadores](batch-api-basics.md), com informações essenciais para todos os utilizadores que se preparam para utilizar o Batch. O artigo contém informações mais detalhadas sobre recursos do serviço Batch, como conjuntos, nós, trabalhos e tarefas, e as várias funcionalidades de API que pode utilizar ao criar a sua aplicação Batch.
* Saiba mais sobre o [Ferramentas e APIs do Batch](batch-apis-tools.md) disponíveis para criação de soluções para o Batch.
* [Introdução à biblioteca do Azure Batch para .NET](batch-dotnet-get-started.md) para saber como utilizar C# e a biblioteca .NET do Batch para executar uma carga de trabalho simples utilizando um fluxo de trabalho Batch comum. Este artigo deve ser uma das suas primeiras paragens ao aprender a utilizar o serviço Batch. Também existe a [versão Python](batch-python-tutorial.md) do tutorial.
* Transfira as [amostras de código no GitHub][github_samples] para ver como tanto o C# como o Python pode interagir com o Batch para agendar e processar amostras de cargas de trabalho.
* Consulte [Percurso de aprendizagem do Batch][learning_path] para ter uma ideia dos recursos disponíveis à medida que aprende a trabalhar com o Batch.


[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png
