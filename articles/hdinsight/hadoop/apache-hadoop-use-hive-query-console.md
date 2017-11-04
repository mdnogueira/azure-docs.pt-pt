---
title: Utilizar o Hive do Hadoop na consola de consulta no HDInsight - Azure | Microsoft Docs
description: Saiba como utilizar a consola de consulta baseada na web para executar consultas do Hive num cluster do Hadoop do HDInsight a partir do seu browser.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5ae074b0-f55e-472d-94a7-005b0e79f779
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: d6032b8a1e3d338b046c958804102aeb9efcf4ab
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="run-hive-queries-using-the-query-console"></a>Executar consultas do Hive, utilizando a consola de consulta
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Neste artigo, irá aprender a utilizar a consola de consulta do HDInsight para executar consultas do Hive num cluster do Hadoop do HDInsight a partir do seu browser.

> [!IMPORTANT]
> A consola de consulta do HDInsight só está disponível nos clusters do HDInsight baseados em Windows. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).
>
> Para o HDInsight 3.4 ou superior, consulte [executar consultas do Hive na vista do Hive do Ambari](apache-hadoop-use-hive-ambari-view.md) para informações sobre como executar consultas do Hive a partir de um browser web.

## <a id="prereq"></a>Pré-requisitos
Para concluir os passos neste artigo, irá precisar do seguinte.

* Um cluster de Hadoop do HDInsight baseados em Windows
* Um browser Moderno

## <a id="run"></a>Executar consultas do Hive, utilizando a consola de consulta
1. Abra um browser e navegue para **https://CLUSTERNAME.azurehdinsight.net**, onde **CLUSTERNAME** é o nome do cluster do HDInsight. Se lhe for solicitado, introduza o nome de utilizador e palavra-passe que utilizou quando criou o cluster.
2. A partir das ligações na parte superior da página, selecione **Editor do Hive**. Esta ação apresenta um formulário que pode ser utilizado para introduzir as declarações HiveQL que pretende executar no cluster do HDInsight.

    ![o editor do hive](./media/apache-hadoop-use-hive-query-console/queryconsole.png)

    Substitua o texto `Select * from hivesampletable` com as seguintes declarações HiveQL:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    As declarações de efetuar as seguintes ações:

   * **LARGAR a tabela**: elimina a tabela e o ficheiro de dados, se a tabela já existe.
   * **Criar tabela externa**: cria uma nova tabela 'externa' no ramo de registo. As tabelas externas armazenam a definição de tabela no ramo; os dados for deixados na localização original.

     > [!NOTE]
     > As tabelas externas devem ser utilizadas ao espera os dados subjacentes ser atualizados por uma origem externa (por exemplo, o processo de carregamento de dados automática) ou por outra operação de MapReduce, mas quiser sempre consultas do Hive para utilizar os dados mais recentes.
     >
     > Remover uma tabela externa **não** eliminar os dados, a definição de tabela.
     >
     >
   * **FORMATO de linha**: indica ao ramo de registo como estão formatados corretamente os dados. Neste caso, os campos no registo de cada são separados por um espaço.
   * **LOCALIZAÇÃO de TEXTFILE AS ARMAZENADOS**: indica ao ramo de registo onde os dados são armazenados (o diretório de dados de exemplo /) e de que esta está armazenada como texto
   * **SELECIONE**: selecione uma contagem de todas as linhas onde coluna **t4** contém o valor **[erro]**. Isto deverá devolver um valor de **3** porque existem três linhas que contêm este valor.
   * **INPUT__FILE__NAME como '%.log'** -diz ao ramo de registo que iremos deverá devolver apenas dados de ficheiros que termina em. registo. Isto limita a pesquisa para o ficheiro de sample.log que contém os dados e mantém-de devolver dados a partir de outro exemplo ficheiros de dados que corresponde ao esquema definido.
3. Clique em **submeter**. O **tarefa sessão** na parte inferior da página deverá apresentar os detalhes da tarefa.
4. Quando o **estado** campo é alterado para **concluído**, selecione **ver detalhes** para a tarefa. Na página de detalhes, o **resultado da tarefa** contém `[ERROR]    3`. Pode utilizar o **transferir** botão sob este campo para transferir um ficheiro que contém o resultado da tarefa.

## <a id="summary"></a>Resumo
Como pode ver, a consola de consulta fornece uma forma fácil de executar consultas do Hive num HDInsight cluster, monitorizar o estado da tarefa e obter o resultado.

Para obter mais informações sobre como utilizar a consola de consulta do Hive para executar tarefas do Hive, selecione **introdução** na parte superior da consola de consulta, em seguida, utilizar os exemplos que são fornecidos. Cada amostra explica o processo de utilizar o Hive para analisar dados, incluindo uma explicação sobre as declarações HiveQL utilizadas na amostra.

## <a id="nextsteps"></a>Passos seguintes
Para obter informações gerais sobre o Hive no HDInsight:

* [Utilizar o Hive com o Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras formas pode trabalhar com o Hadoop no HDInsight:

* [Utilizar o Pig com o Hadoop no HDInsight](hdinsight-use-pig.md)
* [Utilizar o MapReduce com o Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Se estiver a utilizar com o Hive no Tez, consulte os seguintes documentos para obter informações de depuração:

* [Utilizar a IU do Tez no HDInsight baseado em Windows](../hdinsight-debug-tez-ui.md)
* [Utilize a vista Ambari Tez no HDInsight baseado em Linux](../hdinsight-debug-ambari-tez-view.md)

[1]:apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
