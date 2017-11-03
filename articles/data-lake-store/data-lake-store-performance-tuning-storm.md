---
title: "Desempenho do Azure Data Lake Store Storm otimização diretrizes | Microsoft Docs"
description: "Desempenho do Azure Data Lake Store Storm diretrizes de otimização"
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 1dfa93643f45a96ded3fd022aa8b1c71d487acb4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-store"></a>Orientações para Storm no HDInsight e o Azure Data Lake Store de otimização do desempenho

Compreenda os fatores que devem ser considerados ao otimizar o desempenho de uma topologia do Storm do Azure. Por exemplo, é importante compreender as características do trabalho efetuado os spouts e bolts (se o trabalho é exigente em termos de memória ou de e/s). Este artigo abrange uma série de diretrizes, incluindo a resolução de problemas comuns de otimização do desempenho.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do Azure Data Lake Store**. Para obter instruções sobre como criar um, consulte [introdução ao Azure Data Lake Store](data-lake-store-get-started-portal.md).
* **Um cluster do Azure HDInsight** com acesso a uma conta de Data Lake Store. Consulte [criar um cluster do HDInsight com o Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de que ativar o ambiente de trabalho remoto para o cluster.
* **Executar um cluster do Storm no Data Lake Store**. Para obter mais informações, consulte [Storm no HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-overview).
* **Desempenho de otimização diretrizes no Data Lake Store**.  Para os conceitos gerais de desempenho, consulte [guia de otimização de desempenho do Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-performance-tuning-guidance).  

## <a name="tune-the-parallelism-of-the-topology"></a>Otimizar o paralelismo da topologia

Poderá melhorar o desempenho ao aumentar a simultaneidade de e/s e de Data Lake Store. Uma topologia do Storm tem um conjunto de configurações que determinam o paralelismo:
* Número de processos de trabalho (os trabalhadores estão uniformemente distribuídos as VMs).
* Número de instâncias de executor de spout.
* Número de instâncias de executor bolt.
* Número de tarefas de spout.
* Número de tarefas bolt.

Por exemplo, num cluster com 4 VMs e 4 processos de trabalho, 32 spout executores e 32 spout tarefas e executores 256 bolt e tarefas de 512 bolt, considere o seguinte:

Cada supervisor, que é um nó de trabalho, tem uma função de trabalho único processo da máquina virtual (JVM) de Java. Este processo JVM gere 4 spout threads e 64 threads de bolt. Dentro de cada thread, são executadas sequencialmente. A configuração anterior, cada thread spout tem 1 tarefas e cada thread bolt tem 2 tarefas.

Storm, Eis os vários componentes envolvidos e como estas afetam o nível de paralelismo que tiver:
* O nó principal (denominado Nimbus no Storm) é utilizado para submeter e gerir tarefas. Estes nós tem nenhum impacto no grau de paralelismo.
* Os nós do supervisor. No HDInsight, isto corresponde a um nó de trabalho VM do Azure.
* As tarefas de trabalho são processos de Storm em execução em VMs. Cada tarefa de trabalho corresponde a uma instância JVM. Storm distribui o número de processos de trabalho que especificar para os nós de trabalho uniformemente quanto possível.
* Spout e instâncias de executor de bolt. Cada instância de executor corresponde a um thread em execução nos trabalhadores (JVMs).
* Tarefas do Storm. Estas são tarefas lógicas de cada um destes threads de execução. Esta não altera o nível de paralelismo, que deve avaliar se precisar de várias tarefas por executor ou não.

### <a name="get-the-best-performance-from-data-lake-store"></a>Obter o melhor desempenho do Data Lake Store

Ao trabalhar com o Data Lake Store, obter o melhor desempenho se que efetue o seguinte:
* Unir sua pequena acrescenta em tamanhos maiores (Idealmente 4 MB).
* Como muitas pedidos em simultâneo, pode fazê-lo. Porque cada thread bolt está a fazer o bloqueios de leituras, que pretende ter algures no intervalo de threads de 8-12 por núcleo. Este procedimento mantém a NIC e a CPU também são utilizados. Uma VM maior permite mais pedidos simultâneos.  

### <a name="example-topology"></a>Topologia de exemplo

Vamos assumir que tem um cluster de nó de 8 trabalho com uma VM do Azure D13v2. Este VM tem 8 núcleos, entre os nós de 8 trabalho, terá de 64 núcleos totais.

Vamos supor que o fazemos 8 bolt threads por núcleo. Fornecido 64 núcleos, isto significa que queremos 512 instâncias de executor de total bolt (ou seja, threads). Neste caso, vamos supor que iremos começar com um JVM por VM e utilize principalmente a simultaneidade de thread dentro as JVM para alcançar concorrência. Isto significa que precisamos 8 tarefas de trabalho (uma por VM do Azure) e 512 executores de bolt. Tendo em conta esta configuração, Storm tenta distribuir os trabalhadores uniformemente em nós de trabalho (também conhecido como nós de supervisor), cada nó de trabalho 1 a dar JVM. Agora no supervisors, Storm tenta distribuir o executor uniformemente entre supervisors, concedendo cada supervisor (ou seja, JVM) 8 threads cada.

## <a name="tune-additional-parameters"></a>Otimizar parâmetros adicionais
Depois de ter a topologia básica, pode considerar-se de que pretende otimizar qualquer um dos parâmetros:
* **Número de JVMs por nó de trabalho.** Se tiver uma estrutura de dados de grandes dimensões (por exemplo, uma tabela de pesquisa) anfitrião na memória, cada JVM requer uma cópia separada. Em alternativa, pode utilizar a estrutura de dados através de vários threads, se tiver menos JVMs. Para i / o bolt o, o número de JVMs não faz a maior quantidade de uma diferença como o número de threads adicionados entre essas JVMs. Para uma simplicidade é uma boa ideia ter um JVM por trabalho. Dependendo de que o bolt está a fazer ou as aplicações de processamento necessitam, embora, poderá ter de alterar este número.
* **Número de executor de spout.** Uma vez que o anterior exemplo utiliza bolts para escrever no Data Lake Store, o número de spouts não é diretamente relevante para o desempenho de bolt. No entanto, dependendo da quantidade de processamento ou e/s a acontecer no spout, é uma boa ideia para otimizar os spouts para um melhor desempenho. Certifique-se de que tem suficiente spouts para poder manter as bolts ocupado. As taxas de saída dos spouts devem corresponder ao débito dos bolts. A configuração real depende de spout.
* **Número de tarefas.** Cada bolt é executado como um único thread. Tarefas adicionais por bolt não fornecem qualquer simultaneidade adicional. O tempo apenas são vantagem é se o processo de confirmar a cadeia de identificação demora uma grande proporção do tempo de execução bolt. É uma boa ideia grupo que muitos cadeias de identificação para uma maior acrescentar antes de enviar uma confirmação de bolt. Por isso, na maioria dos casos, várias tarefas não fornecem existir nenhuma vantagem adicional.
* **Local ou shuffle agrupamento.** Quando esta definição estiver ativada, cadeias de identificação são enviadas para bolts no mesmo processo de trabalho. Isto reduz a chamadas de rede e de comunicação entre processos. Esta opção é recomendada para a maioria das topologias.

Este cenário básico é um ponto de partida. Testar com os seus dados para otimizar os parâmetros anteriores para alcançar um desempenho ideal.

## <a name="tune-the-spout"></a>Otimizar o spout

Pode modificar as seguintes definições para otimizar o spout.

- **Tempo limite de cadeia de identificação: topology.message.timeout.secs**. Esta definição determina a quantidade de tempo demora uma mensagem para concluir e receber confirmação, antes de ser considerada a falha.

- **Memória máxima por processo de trabalho: worker.childopts**. Esta definição permite-lhe especificar os parâmetros da linha de comandos adicionais para os trabalhadores de Java. A definição mais frequentemente utilizadas aqui é XmX, que determina o máximo de memória alocado a pilha de um JVM.

- **Spout máximo pendentes: topology.max.spout.pending**. Esta definição determina o número de cadeias de identificação no pode ser voo (ainda não confirmado em todos os nós da topologia) por thread de spout em qualquer altura.

 É um cálculo boa fazer para estimar o tamanho de cada uma das suas cadeias de identificação. Em seguida, descobrir tem de thread de spout uma quantidade de memória. Total de memória atribuída a um thread, dividido por este valor, deverá dar-lhe o limite superior para o máximo spout pendentes parâmetro.

## <a name="tune-the-bolt"></a>Otimizar o bolt
Quando estiver a escrever no arquivo Data Lake, defina uma política de sincronização de tamanho (memória intermédia do lado do cliente) para 4 MB. Um libertar ou hsync(), em seguida, é efetuado apenas quando o tamanho de memória intermédia é o neste valor. O controlador de Data Lake Store na função de trabalho VM automaticamente efetua Esta colocação em memória intermédia, a menos que efetua uma hsync() explicitamente.

Bolt Storm de arquivo Data Lake predefinida tem um parâmetro de política do sincronização tamanho (fileBufferSize) que pode ser utilizado para otimizar este parâmetro.

Topologias de I/O intensivo, é boa ideia ter cada thread bolt escrita para o seu próprio ficheiro e para definir uma política de rotação de ficheiro (fileRotationSize). Quando o ficheiro atinge um determinado tamanho, o fluxo é automaticamente removida da cache e é escrito um novo ficheiro. O tamanho do ficheiro recomendado para rotação é de 1 GB.

### <a name="handle-tuple-data"></a>Processar os dados de cadeia de identificação

No Storm, um spout deter na uma cadeia de identificação até explicitamente é confirmada pelo bolt. Se uma cadeia de identificação foram lidos pelo bolt, mas não tiver sido confirmada ainda, a spout poderá não ter continuada no back-end do Data Lake Store. Depois de uma cadeia de identificação é confirmada, a spout pode ser garantida persistência pelo bolt e, em seguida, pode eliminar a origem de dados de qualquer origem de leitura a partir do.  

Para melhor desempenho no Data Lake Store, ter bolt a memória intermédia de 4 MB de dados de cadeia de identificação. Em seguida, repetição de escrita para o Data Lake Store fim como uma escrita de 4 MB. Depois dos dados foi escritos com êxito para o arquivo (por chamada hflush()), bolt pode reconhecer os dados de volta para o spout. Este é o que faz o bolt de exemplo fornecido aqui. Também é aceitável para armazenar um grande número de cadeias de identificação antes da chamada de hflush() é efetuada e as cadeias de identificação confirmadas. No entanto, isto aumenta o número de cadeias de identificação em trânsito que tem de conter o spout e, por conseguinte, aumenta a quantidade de memória necessária por JVM.

> [!NOTE]
As aplicações podem ter um requisito para reconhecer cadeias de identificação com mais frequência (em tamanhos de dados inferiores a 4 MB) por outros motivos de desempenho não. No entanto, o que poderá afetar o débito de e/s para o armazenamento de back-end. Cuidadosamente pesar este compromisso contra o desempenho de e/s do bolt.

Se não for elevada taxa de receção de cadeias de identificação, por isso, a memória intermédia de 4 MB demora muito tempo a preencher, considere a mitigar este por:
* Reduzir o número de bolts, por isso, não existem memórias intermédias menos para preencher.
* Ter uma política com base em contagem ou baseados no tempo, em que um hflush() é acionada a cada x esvaziamentos ou cada milissegundos y e as cadeias de identificação acumuladas até ao momento são confirmadas anterior.

Tenha em atenção que neste caso, o débito é inferior, mas com uma velocidade de lenta de eventos, o débito máximo não é o objetivo maior mesmo assim. Estes mitigações ajudam a reduzir o tempo total que leva uma cadeia de identificação a passagem para o arquivo. Isto poderá importa se pretender que um pipeline em tempo real, mesmo com uma taxa de eventos de baixa. Tenha também em atenção que se a taxa de receção na cadeia de identificação é baixa, deverá ajustar o parâmetro topology.message.timeout_secs, pelo que as cadeias de identificação não o tempo limite enquanto está a obter colocado na memória intermédia ou processados.

## <a name="monitor-your-topology-in-storm"></a>Monitorizar a topologia de Storm  
Enquanto a topologia está em execução, pode monitorizá-lo na interface de utilizador do Storm. Seguem-se os parâmetros para observar os principais:

* **Latência de execução do processo total.** Este é o tempo médio de uma cadeia de identificação demora a ser emitidos pelo spout, processados pelo bolt e confirmada.

* **Latência de processo total bolt.** Este é o tempo médio despendido pela cadeia de identificação no bolt até receber uma confirmação.

* **Total bolt executar latência.** Este é o tempo médio despendido por bolt no método de execução.

* **Número de falhas.** Isto refere-se ao número de cadeias de identificação que falharam ao ser totalmente processados antes de poderem ultrapassou o tempo limite.

* **Capacidade.** Esta é uma medida é como ocupado do seu sistema. Se este número for 1, a sua bolts estão a funcionar como rápido podem. Se for inferior a 1, aumente o paralelismo. Se for superior a 1, reduza o paralelismo.

## <a name="troubleshoot-common-problems"></a>Resolver problemas comuns
Seguem-se alguns cenários comuns de resolução de problemas.
* **Cadeias de identificação muitos são exceder o tempo limite.** Observe a cada nó na topologia para determinar em que é o valor. A razão mais comum para esta situação é que os bolts não são possível manter-se com os spouts. Isto leva a identificação clogging memórias intermédias internas enquanto a aguardar processamento. Considere aumentar o valor de tempo limite ou diminua o máximo spout pendentes.

* **Há uma latência de execução do processo total elevada, mas a latência de processo bolt baixa.** Neste caso, é possível que as cadeias de identificação não estão a ser confirmadas suficientemente rápida. Verifique se existem um número suficiente de acknowledgers. Possibilidade de outra é que estes estão a aguardar na fila há demasiado tempo antes dos bolts iniciasse o processamento dos mesmos. Diminua o máximo spout pendentes.

* **Não há um bolt elevado executar latência.** Isto significa que o método Execute () para o bolt está a demorar demasiado tempo. Otimizar o código, ou observar os tamanhos de escrita e descarregar o comportamento.

### <a name="data-lake-store-throttling"></a>Limitação do Data Lake Store
Se atingiu os limites de largura de banda fornecida pelo Data Lake Store, poderá ver falhas de tarefas. Verifique os registos da tarefa para erros de limitação. Pode diminuir o paralelismo através do aumento do tamanho do contentor.    

Para verificar se a introdução limitada, ative a depuração de registo do lado do cliente:

1. No **Ambari** > **Storm** > **configuração** > **avançadas log4j de trabalho de storm**, alterar  **&lt;nível de raiz = "informações"&gt;**  para  **&lt;nível de raiz = "a depuração"&gt;**. Reinicie todos os nós/serviço para a configuração entrem em vigor.
2. Monitorizar a topologia do Storm os registos de nós de trabalho (em /var/log/storm/worker-artifacts /&lt;TopologyName&gt;/&lt;porta&gt;/worker.log) do Data Lake Store exceções de limitação.

## <a name="next-steps"></a>Passos seguintes
Otimização de desempenho adicional para Storm pode ser referenciado na [este blogue](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/).

Para obter um exemplo adicional executar, consulte [este um no GitHub](https://github.com/hdinsight/storm-performance-automation).
