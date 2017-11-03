---
title: Monitorizar o desempenho de cluster - Azure HDInsight | Microsoft Docs
description: Como monitorizar um cluster do HDInsight para a capacidade e o desempenho.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: maxluk
ms.openlocfilehash: f2333202cdccc82edea649ff1c295752a414c8b8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-cluster-performance"></a>Monitorizar o desempenho do cluster

Monitorização do Estado de funcionamento e desempenho de um cluster do HDInsight é essencial para manter o máximo desempenho e utilização de recursos. Monitorização também pode ajudar a codificação possíveis do endereço ou erros de configuração de cluster.

As secções seguintes descrevem como otimizar o cluster carregar, eficiência de fila YARN e acessibilidade de armazenamento.

## <a name="cluster-loading"></a>Carregamento de cluster

Clusters do Hadoop devem balancear carregar em todos os nós do cluster. Este balanceamento impede que as tarefas de processamento de ficar restringida pelas RAM, CPU ou recursos de disco.

Para obter um alto nível de olhos os nós do cluster e os respetivos carregar, inicie sessão para o [IU da Web do Ambari](hdinsight-hadoop-manage-ambari.md), em seguida, selecione o **anfitriões** separador. Os anfitriões são listados pelos respetivos nomes de domínio completamente qualificado. Estado de funcionamento de cada anfitrião é apresentado por um indicador de estado de funcionamento coloridos:

| Cor | Descrição |
| --- | --- |
| Vermelho | Pelo menos um componente principal do anfitrião está inativo. Coloque o cursor para ver uma descrição que listas afetado componentes. |
| Orange | Componente, pelo menos, um subordinado do anfitrião está inativo. Coloque o cursor para ver uma descrição que listas afetado componentes. |
| Amarelo | Servidor do Ambari não recebido um heartbeat do anfitrião durante mais de 3 minutos. |
| Verde | Normal Estado em execução. |

Também verá as colunas que mostra o número de núcleos e quantidade de RAM para cada anfitrião e a utilização do disco e a carga média.

![Separador anfitriões](./media/hdinsight-key-scenarios-to-monitor/hosts-tab.png)

Selecione qualquer um dos nomes de anfitrião para ver detalhada componentes em execução nesse anfitrião e as métricas. As métricas são apresentadas como uma linha cronológica selecionável de utilização, o carregamento de CPU, utilização de disco, utilização de memória, utilização de rede e números de processos.

![Detalhes do anfitrião](./media/hdinsight-key-scenarios-to-monitor/host-details.png)

Consulte [gerir clusters do HDInsight utilizando a IU da Web do Ambari](hdinsight-hadoop-manage-ambari.md) para obter detalhes sobre a definição de alertas e ver as métricas.

## <a name="yarn-queue-configuration"></a>Configuração da fila YARN

Hadoop tem vários serviços em execução na respetiva plataforma distribuída. YARN (ainda outro recurso Negotiator) coordena estes serviços, atribui recursos de cluster e gere o acesso a um conjunto de dados comuns.

YARN divide as responsabilidades de JobTracker, gestão de recursos e tarefa agendamento/monitorização, dois em dois daemons: um ResourceManager global e um ApplicationMaster por aplicação (AM).

O ResourceManager é um *programador puro*e apenas arbitrates recursos disponíveis entre todas as aplicações concorrentes. O ResourceManager garante que todos os recursos estão sempre em utilização, otimizar para várias constantes como SLA, garante que a capacidade e assim sucessivamente. O ApplicationMaster negoceia recursos a partir de ResourceManager e funciona com o NodeManager(s) para executar e monitorizar os contentores e o respetivo consumo de recursos.

Quando vários inquilinos partilham um cluster de grandes dimensões, não há disputa pelos recursos do cluster. O CapacityScheduler é um programador incorporável que ajuda a partilha através da colocação dos pedidos de recursos. Também suporta o CapacityScheduler *filas hierárquicas* para garantir que os recursos são partilhados entre as filas de uma organização, antes de filas de outras aplicações estão autorizadas a utilizar recursos gratuitos secundárias.

YARN permite-nos alocar recursos a estas filas e mostra-lhe se todos os seus recursos disponíveis são atribuídos. Para ver informações sobre as filas, inicie sessão na IU da Web do Ambari, em seguida, selecione **Gestor de filas YARN** no menu superior.

![Gestor de filas YARN](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager.png)

A página de Gestor de filas YARN mostra uma lista das suas filas à esquerda, juntamente com a percentagem da capacidade atribuída a cada um.

![Página de detalhes do Gestor de filas YARN](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

Para ver mais detalhada as filas, a partir do Ambari dashboard, selecione o **YARN** serviço na lista à esquerda. Em seguida, sob o **ligações rápidas** menu pendente, selecione **ResourceManager IU** sob o nó ativo.

![Ligação de menu ResourceManager IU](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

Na IU do ResourceManager, selecione **programador** no menu esquerdo. É apresentada uma lista das suas filas, por baixo *filas de aplicação*. Aqui pode ver a capacidade utilizada para cada uma das suas filas, bem como as tarefas são distribuídas entre eles, e se qualquer uma das tarefas são restrita do recurso.

![Ligação de menu ResourceManager IU](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui.png)

## <a name="storage-throttling"></a>Limitação de armazenamento

Estrangulamento do desempenho de um cluster pode acontecer ao nível do armazenamento. Este tipo de congestionamento é frequentemente devido a *bloquear* operações (e/s), que ocorrem quando as tarefas em execução enviar e/s mais do que o serviço de armazenamento pode processar de entrada/saída. Este bloqueio cria uma fila de pedidos de e/s a aguardar processamento até depois de IOs atuais são processados. Os blocos são devido a *limitação de armazenamento*, que não é um limite físico, mas em vez de um limite imposto pelo serviço de armazenamento por um contrato de nível de serviço (SLA). Este limite assegura que nenhum cliente único ou inquilino pode monopolizar o serviço. O SLA limita o número de IOs por segundo (IOPS) para o armazenamento do Azure - para obter detalhes, consulte [metas de desempenho e escalabilidade do Storage do Azure](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Se estiver a utilizar o Storage do Azure, para obter informações sobre como monitorizar problemas relacionados com o armazenamento, incluindo limitação, consulte o artigo [monitorizar, diagnosticar e resolver problemas de armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Se o arquivo de cópia de segurança do seu cluster do Azure Data Lake armazenar (ADLS), a limitação é provavelmente devido a limites de largura de banda. Limitação neste caso, identificada por observar erros limitação nos registos de tarefas. Para ADLS, consulte a secção de limitação para o serviço apropriado nestes artigos:

* [Guia de sintonização de desempenho para o Hive no HDInsight e Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Guia de sintonização de desempenho para o MapReduce no HDInsight e Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Guia de sintonização de desempenho para o Storm no HDInsight e Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="next-steps"></a>Passos seguintes

Visite as seguintes ligações para mais informações sobre resolução de problemas e os clusters de monitorização:

* [Analisar registos do HDInsight](hdinsight-debug-jobs.md)
* [Depurar aplicações com registos YARN](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Ativar capturas de área dinâmica para dados de serviços do Hadoop no HDInsight baseado em Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
