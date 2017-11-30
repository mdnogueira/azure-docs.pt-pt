---
title: Resolver problemas com o cluster do Apache Spark no Azure HDInsight | Microsoft Docs
description: Saiba mais sobre problemas relacionados com clusters do Apache Spark no Azure HDInsight e como resolver os.
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 610c4103-ffc8-4ec0-ad06-fdaf3c4d7c10
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: bb5557eb0672b9ad137bc5817e47bf4f89e1c34d
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Problemas conhecidos para o cluster do Apache Spark no HDInsight

Este documento mantém um registo dos problemas conhecidos para a pré-visualização pública do HDInsight Spark.  

## <a name="livy-leaks-interactive-session"></a>O Livy fugas de sessão interativa
Quando o Livy é reiniciado (do Ambari ou devido a reinício da máquina virtual de headnode 0), com uma sessão interativa ainda ativo, uma sessão interativa de tarefa será transmitida. Por este motivo, as novas tarefas podem bloqueada no Estado aceites e não podem ser iniciadas.

**Atenuação:**

Utilize o procedimento seguinte para resolver o problema:

1. SSH para headnode. Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Execute o seguinte comando para localizar os IDs de aplicação das tarefas de interativas a trabalhar com o Livy. 
   
        yarn application –list
   
    A tarefa predefinida nomes serão Livy se as tarefas foram iniciadas com uma sessão interativa de Livy com sem nomes explícitos especificado, iniciar sessão de para o Livy pelo bloco de notas do Jupyter, o nome da tarefa começará com remotesparkmagics_ *. 
3. Execute o seguinte comando para eliminar essas tarefas. 
   
        yarn application –kill <Application ID>

Novas tarefas iniciará a executar. 

## <a name="spark-history-server-not-started"></a>Servidor de histórico de Spark não foi iniciado
Servidor do histórico de Spark não está iniciado automaticamente depois de um cluster é criado.  

**Atenuação:** 

Inicie manualmente o servidor de histórico do Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Problema de permissão no diretório de registo do Spark
Quando hdiuser submete uma tarefa com spark-submit, há um java.io.FileNotFoundException de erro: não é escrito /var/log/spark/sparkdriver_hdiuser.log (permissão negada) e o registo de controlador. 

**Atenuação:**

1. Adicione hdiuser ao grupo de Hadoop. 
2. Forneça 777 permissões no /var/log/spark após a criação do cluster. 
3. Atualize a localização de registo do spark com o Ambari para ser um diretório com 777 permissões.  
4. O spark de execução-submeter como sudo.  

## <a name="spark-phoenix-connector-is-not-supported"></a>O Spark Phoenix conector não é suportado

Atualmente, o conector do Spark Phoenix não é suportado com um cluster do HDInsight Spark.

**Atenuação:**

Em vez disso, tem de utilizar o conector do HBase do Spark. Para obter instruções, consulte [como utilizar o conector do Spark HBase](https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/).

## <a name="issues-related-to-jupyter-notebooks"></a>Problemas relacionados com blocos de notas do Jupyter
Seguem-se alguns problemas conhecidos relacionados com blocos de notas do Jupyter.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Blocos de notas com carateres não ASCII em nomes de ficheiros
Blocos de notas Jupyter que podem ser utilizados em clusters do Spark HDInsight não devem ter carateres não ASCII em nomes de ficheiros. Se tentar carregar um ficheiro através da IU do Jupyter que tem um nome de ficheiro não ASCII, ocorrerá uma falha silenciosamente (ou seja, não permite Jupyter carregar o ficheiro, mas não irá gerar um erro de visível ou). 

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Erro ao carregar os blocos de notas de tamanhos superiores
Poderá ver um erro  **`Error loading notebook`**  quando carrega blocos de notas que são maiores de tamanho.  

**Atenuação:**

Se obtiver este erro, não significa que os dados estão danificados ou perdidos.  Os blocos de notas são ainda num disco em `/var/lib/jupyter`, e pode SSH para o cluster possam aceder às mesmas. Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](../hdinsight-hadoop-linux-use-ssh-unix.md).

Assim que tiver estabelecido ligação ao cluster através de SSH, pode copiar os blocos de notas do seu cluster no seu computador local (através de SCP ou WinSCP) de cópia de segurança para evitar a perda de quaisquer dados importantes no bloco de notas. Pode, em seguida, túnel SSH no seu headnode na porta 8001 aceder Jupyter sem passar o gateway.  A partir daí, pode desmarcar a saída do seu bloco de notas e voltar a guardar para minimizar o tamanho do bloco de notas.

Para impedir que este erro ocorrer no futuro, tem de seguir algumas melhores práticas:

* É importante manter o tamanho de bloco de notas pequeno. Qualquer saída das tarefas do Spark que é enviada para Jupyter é continuada no bloco de notas.  É melhor prática, com o Jupyter no geral para evitar a execução `.collect()` em grande RDD ou dataframes; em vez disso, se pretende observar no conteúdo de um RDD, considere executar `.take()` ou `.sample()` para que o resultado não demasiado grande.
* Além disso, quando guardar um bloco de notas, desmarque todos os saída células para reduzir o tamanho.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Arranque inicial do bloco de notas demora mais tempo do que o previsto
Primeira instrução de código no bloco de notas do Jupyter utilizando a magia do Spark pode demorar mais do que um minuto.  

**Explicação:**

Isto acontece porque quando a primeira célula do código é executada. Em segundo plano este inicia a configuração de sessão e Spark, SQL e contextos de ramo de registo estão definidos. Depois destas contextos estão definidos, a primeira instrução é executada e Isto permite que a impressão que a instrução demorava muito tempo a concluir.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Tempo limite de bloco de notas do Jupyter criar a sessão
Quando o cluster do Spark tem recursos insuficientes, os Spark e Pyspark kernels no bloco de notas do Jupyter serão tempo limite excedido ao criar a sessão. 

**Mitigações:** 

1. Liberte alguns recursos do cluster do Spark por:
   
   * A parar os outros blocos de notas do Spark ao aceder ao menu de fechar e Halt ou clicar encerramento no Explorador do bloco de notas.
   * A parar a outras aplicações do Spark do YARN.
2. Reinicie o bloco de notas que estava a tentar iniciar a cópia de segurança. Recursos suficientes devem estar disponíveis para criar uma sessão agora.

## <a name="see-also"></a>Consultar também
* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Spark com BI: Efetuar uma análise de dados interativa com o Spark no HDInsight com ferramentas do BI](apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura do edifício com dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](apache-spark-machine-learning-mllib-ipython.md)
* [Transmissão em Fluxo do Spark: Utilizar o Spark no HDInsight para criar aplicações de transmissão em fluxo em tempo real](apache-spark-eventhub-streaming.md)
* [Análise de registos de sites com o Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Spark com o Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para depurar aplicações do Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilizar blocos de notas do Zeppelin com um cluster do Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster do Spark para o HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)

