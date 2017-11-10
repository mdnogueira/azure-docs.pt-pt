---
title: "Introdução ao servidor R no HDInsight do Azure | Microsoft Docs"
description: "Saiba como utilizar o servidor R no HDInsight para criar aplicações para análise de macrodados."
services: hdinsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 6dc21bf5-4429-435f-a0fb-eea856e0ea96
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: 57e28215124bc0330517c541e4cb74a66d939ff5
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
#<a name="introduction-to-r-server-and-open-source-r-capabilities-on-hdinsight"></a>Introdução ao servidor R e as capacidades de R de open source do HDInsight

R Server da Microsoft está disponível como uma opção de implementação quando criar clusters do HDInsight no Azure. Esta nova funcionalidade fornece cientistas de dados, statisticians e programadores de R com acesso a pedido para dimensionável, distribuída métodos de análise no HDInsight.

Clusters podem ser dimensionados adequadamente para os projetos e tarefas em execução e, em seguida, foi desligados quando já não forem necessárias. Uma vez que o se estiver a parte do Azure HDInsight, esses clusters vêm com suporte de 24x7 de nível empresarial, um SLA de 99,9% de tempo e a capacidade para integrar com outros componentes do ecossistema do Azure.

Servidor R no HDInsight fornece funcionalidades mais recentes para análise R baseado em conjuntos de dados de praticamente qualquer tamanho, carregado para o armazenamento de Blobs do Azure ou de Data Lake. Uma vez que o servidor R está incorporado no código aberto R, as aplicações com base em R que cria podem tirar partido de qualquer dos pacotes de open source para R 8000 +. Rotinas no ScaleR, pacote de análise de macrodados da Microsoft incluído com o servidor R, também estão disponíveis.

O nó de extremidade de um cluster fornece um local conveniente para ligar ao cluster e executar os scripts de R. Com um nó de extremidade, tem a opção de executar as funções distribuídas parallelized do ScaleR em todos os núcleos do servidor edge nó. Também pode executá-los em todos os nós do cluster através Hadoop mapa reduzir do ScaleR ou contextos de computação de Spark.

Os modelos ou predições resultado de estatísticas pode ser transferido para utilizam no local. Estes podem também ser operationalized noutro local no Azure, em particular através de [Azure Machine Learning Studio](http://studio.azureml.net) [serviço web](../../machine-learning/studio/publish-a-machine-learning-web-service.md).

## <a name="get-started-with-r-on-hdinsight"></a>Introdução ao R no HDInsight
Para incluir o servidor R no cluster do HDInsight, tem de selecionar o tipo de cluster de servidor R ao criar um cluster do HDInsight no portal do Azure. O tipo de cluster de servidor R inclui R Server em nós do cluster de dados e no nó de extremidade, o que funciona como uma zona de destino para a análise baseada em servidor R. Consulte [introdução ao servidor R no HDInsight](r-server-get-started.md) para obter instruções sobre como criar o cluster.

## <a name="learn-about-data-storage-options"></a>Saiba mais sobre as opções de armazenamento de dados
Armazenamento de predefinido para o sistema de ficheiros do HDFS de clusters do HDInsight pode ser associado a uma conta de armazenamento do Azure ou um Azure Data Lake store. Esta associação garante que qualquer dados foi carregados para o cluster de armazenamento durante a análise é efetuado persistente. Existem várias ferramentas para processar a transferência de dados para a opção de armazenamento que selecionar, incluindo a instalações de carregamento com base no portal da conta de armazenamento e a [AzCopy](../../storage/common/storage-use-azcopy.md) utilitário.

Tem a opção de adição de acesso para o Blob adicional e armazena o Data lake durante o processo, independentemente da opção de armazenamento primário em utilização de aprovisionamento de cluster. Consulte [introdução ao servidor R no HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-get-started) para obter informações sobre a adição de acesso a contas adicionais. Consulte o suplementares [opções de armazenamento do Azure para o servidor R no HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) artigo para obter mais informações sobre como utilizar várias contas de armazenamento.

Também pode utilizar [ficheiros do Azure](../../storage/files/storage-how-to-use-files-linux.md) como uma opção de armazenamento para utilização no nó de extremidade. Ficheiros do Azure permite-lhe montar uma partilha de ficheiros que foi criada no armazenamento do Azure para o sistema de ficheiros do Linux. Para obter mais informações sobre estas opções de armazenamento de dados para o servidor R num cluster do HDInsight, consulte [as opções de armazenamento do Azure para clusters de servidor R no HDInsight](r-server-storage.md).

## <a name="access-r-server-on-the-cluster"></a>Acesso R Server no cluster
Pode ligar ao servidor R no nó de extremidade utilizando um browser. É instalado por predefinição durante a criação do cluster. Para obter mais informações, consulte [obter stared com o servidor R no HDInsight](r-server-get-started.md).

Também pode ligar ao servidor R da linha de comandos utilizando SSH/PuTTY para aceder à consola do R. 

## <a name="develop-and-run-r-scripts"></a>Desenvolver e executar scripts de R
Os scripts de R criar e executar podem utilizar qualquer um dos 8000 + open source para R pacotes além rotinas paralelizadas e distribuídas disponíveis na biblioteca ScaleR. Em geral, um script que é executado com o servidor R num nó de extremidade é executado dentro do interpretador de R nesse nó. As exceções são esses passos que tem de chamar uma função de ScaleR com um contexto de computação que está definido para o Hadoop mapa reduzir (RxHadoopMR) ou Spark (RxSpark). Neste caso, a função é executada de forma distribuída entre os nós do cluster de dados (tarefas) que estão associados com os dados referenciados. Para obter mais informações sobre as opções de contexto de computação diferentes, consulte [computação as opções de contexto para o servidor R no HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Operacionalize um modelo
Quando a modelação de dados estiver concluída, pode operacionalizar o modelo para fazer predições para novos dados a partir do Azure e no local. Este processo é conhecido como a classificação. A classificação pode ser feita no HDInsight, do Azure Machine Learning ou no local.

### <a name="score-in-hdinsight"></a>Pontuação no HDInsight
Para Pontuar no HDInsight, escreva uma função de R que chama o seu modelo para fazer predições para um novo ficheiro de dados tiver carregado para a sua conta de armazenamento. Em seguida, guarde serão as predições novamente para a conta de armazenamento. Pode executar a rotina a pedido no nó de extremidade do cluster ou utilizando uma tarefa agendada.  

### <a name="score-in-azure-machine-learning-aml"></a>Pontuação no Azure Machine Learning (AML)
Para pontuar utilizando um serviço web AML, utilize o pacote do Azure Machine Learning R de open source para conhecido como [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) para publicar o seu modelo como um serviço web do Azure. Para sua comodidade, este pacote é pré-instalados no nó de extremidade. Em seguida, utilize as instalações no Machine Learning para criar uma interface de utilizador para o serviço web e, em seguida, chame o serviço web, conforme necessário para classificação.

Se escolher esta opção, terá de converter os objetos de modelo ScaleR objetos equivalentes de open source de modelo para utilização com o serviço web. Utilize as funções de adesão ScaleR, tais como `as.randomForest()` para modelos com base em ensemble, para esta conversão.

### <a name="score-on-premises"></a>Pontuação no local
Para Pontuar no local depois de criar o seu modelo, pode serializar o modelo de R, transferi-lo, anular a serialização e, em seguida, utilizá-lo para a classificação de dados de novo. Pode Pontuar novos dados ao utilizar a abordagem descrita anteriormente no [classificação no HDInsight](#scoring-in-hdinsight) ou utilizando [DeployR](https://deployr.revolutionanalytics.com/).

## <a name="maintain-the-cluster"></a>Manter o cluster
### <a name="install-and-maintain-r-packages"></a>Instalar e manter os pacotes de R
A maioria dos pacotes de R que utilizar é necessárias no nó de extremidade desde a maior parte dos passos para os scripts de R executados não existe. Para instalar pacotes de R adicionais num nó de extremidade, pode utilizar o habitual `install.packages()` método em R.

Se estiver apenas a utilizar rotinas da biblioteca de ScaleR no cluster, não normalmente, tem de instalar os pacotes de R adicionais em nós de dados. No entanto, poderá ser necessário pacotes adicionais para suportar a utilização de **rxExec** ou **RxDataStep** execução em nós de dados.

Nestes casos, os pacotes adicionais podem ser instalados com uma ação de script depois de criar o cluster. Para obter mais informações, consulte [criar um cluster do HDInsight com o servidor R](r-server-get-started.md).   

### <a name="change-hadoop-map-reduce-memory-settings"></a>Alterar as definições da memória reduzir de mapa de Hadoop
Um cluster pode ser modificado para alterar a quantidade de memória que está disponível para o servidor R quando está a executar uma tarefa de reduzir o mapa. Para modificar um cluster, utilize a IU do Apache Ambari que está disponível através do painel do portal do Azure para o cluster. Para obter instruções sobre como aceder à IU do Ambari para o cluster, consulte [gerir clusters do HDInsight utilizando a IU da Web do Ambari](../hdinsight-hadoop-manage-ambari.md).

Também é possível alterar a quantidade de memória que está disponível para o servidor R utilizando comutadores do Hadoop na chamada para **RxHadoopMR** da seguinte forma:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Dimensionar o cluster
Um cluster existente pode ser escalado para cima ou para baixo através do portal. Ao aumentar, pode ter a capacidade adicional que poderá ser necessário para tarefas de processamento superiores ou, pode dimensionar novamente um cluster quando estiver inativo. Para obter instruções sobre como dimensionar um cluster, consulte [gerir clusters do HDInsight](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Manter o sistema
Manutenção para aplicar patches de SO e outras atualizações é efetuada nas VMs Linux subjacente de um cluster de HDInsight durante off-hours. Normalmente, manutenção é feita às 3:30 AM (com base na hora local para a VM), cada segunda-feira e a quinta-feira. As atualizações são executadas de forma a que não afetam mais de um trimestre do cluster de cada vez.  

Uma vez que os nós principais são redundantes e nem todos os nós de dados são afetados, as tarefas que estão em execução durante este tempo, poderão abrandar. Eles devem continuar a executar a conclusão, no entanto. Qualquer personalizadas de software ou dados locais que tiver são preservados entre estes eventos de manutenção ocorra uma falha catastrófica que necessita de uma reconstrução do cluster.

## <a name="learn-about-ide-options-for-r-server-on-an-hdinsight-cluster"></a>Saiba mais sobre as opções de IDE para o servidor R num cluster do HDInsight
O nó de extremidade de Linux de um cluster do HDInsight é a zona de destino para a análise baseada em R. Recentes versões do HDInsight fornecem uma instalação padrão do servidor do RStudio no nó de extremidade como um IDE baseada no browser. Utilização do RStudio Server como um IDE para o desenvolvimento e a execução de scripts de R podem ser consideravelmente mais produtivos e permite que apenas utilizando a consola do R.

Outra opção IDE completa é instalar um IDE de ambiente de trabalho e utilizá-lo para aceder ao cluster através da utilização de um contexto de computação de reduzir o mapa ou Spark remoto. As opções incluem da Microsoft [R Tools para Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS), do RStudio e Walware baseado no Eclipse [StatET](http://www.walware.de/goto/statet).

Por último, pode aceder à consola do servidor R no nó de extremidade, escrevendo **R** na linha de comandos do Linux depois de se ligar através de SSH ou PuTY. Quando utilizar a interface da consola, é conveniente executar um editor de texto para o desenvolvimento de script do R na outra janela e cortar e colar secções do seu script na consola do R conforme necessário.

## <a name="learn-about-pricing"></a>Saiba mais sobre os preços
As taxas associadas um cluster do HDInsight com o servidor de R estão estruturadas do mesmo modo para as taxas de standard para clusters do HDInsight. Têm como base o dimensionamento de VMs subjacentes entre o nome, dados e nós de limite, com a adição de um uplift horas de núcleo. Para obter mais informações sobre preços do HDInsight e a disponibilidade de uma avaliação gratuita de 30 dias, consulte [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre como utilizar o R Server com clusters do HDInsight, consulte os seguintes tópicos:

* [Introdução ao servidor R no HDInsight](r-server-get-started.md)
* [Compute context options for R Server on HDInsight (Opções do contexto de cálculo para o R Server no HDInsight)](r-server-compute-contexts.md)
* [Azure Storage options for R Server on HDInsight (Opções do Armazenamento do Azure para o R Server no HDInsight)](r-server-storage.md)
