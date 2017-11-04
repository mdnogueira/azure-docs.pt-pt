---
title: Utilizar o Hive do Hadoop e ambiente de trabalho remoto no HDInsight - Azure | Microsoft Docs
description: Saiba como ligar ao cluster de Hadoop no HDInsight utilizando o ambiente de trabalho remoto e, em seguida, executar consultas do Hive, utilizando a Interface de linha de comandos do Hive.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 8c228e35-d58a-4f22-917a-1d20c9da89b4
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: ded90b0f94e545b4066a0220afffae26f1cd15ee
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-hive-with-hadoop-on-hdinsight-with-remote-desktop"></a>Utilizar o Hive com o Hadoop no HDInsight com o ambiente de trabalho remoto
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Neste artigo, irá aprender a ligar a um cluster do HDInsight utilizando o ambiente de trabalho remoto e, em seguida, executar consultas do Hive, utilizando o Hive Interface de linha de comandos (CLI).

> [!IMPORTANT]
> Ambiente de trabalho remoto só está disponível nos clusters do HDInsight que utilizam o Windows como o sistema operativo. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).
>
> Para o HDInsight 3.4 ou superior, consulte [utilizar o Hive com o HDInsight e Beeline](apache-hadoop-use-hive-beeline.md) para informações sobre como executar consultas do Hive diretamente no cluster de uma linha de comandos.

## <a id="prereq"></a>Pré-requisitos
Para concluir os passos neste artigo, irá precisar do seguinte:

* Um cluster de HDInsight (Hadoop no HDInsight) baseado em Windows
* Um computador cliente com Windows 10, Windows 8 ou Windows 7

## <a id="connect"></a>Estabelecer ligação com o ambiente de trabalho remoto
Ativar o ambiente de trabalho remoto para o cluster do HDInsight, em seguida, ligar-se ao mesmo, seguindo as instruções apresentadas em [ligar a clusters do HDInsight através de RDP](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="hive"></a>Utilize o comando de ramo de registo
Quando tiver estabelecido ligação ao ambiente de trabalho para o cluster do HDInsight, utilize os seguintes passos para trabalhar com o ramo de registo:

1. No ambiente de trabalho HDInsight, inicie o **linha de comandos do Hadoop**.
2. Introduza o seguinte comando para iniciar a CLI do Hive:

        %hive_home%\bin\hive

    Quando iniciou a CLI, verá a linha de comandos da ramo de registo CLI: `hive>`.
3. Com a CLI do AZURE, introduza as seguintes instruções para criar uma nova tabela com o nome **log4jLogs** utilizando dados de exemplo:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    As declarações de efetuar as seguintes ações:

   * **LARGAR a tabela**: elimina a tabela e o ficheiro de dados, se a tabela já existe.
   * **Criar tabela externa**: cria uma nova tabela 'externa' no ramo de registo. As tabelas externas armazenam a definição de tabela no ramo de registo (os dados for deixados na localização original).

     > [!NOTE]
     > As tabelas externas devem ser utilizadas ao espera os dados subjacentes ser atualizados por uma origem externa (por exemplo, o processo de carregamento de dados automática) ou por outra operação de MapReduce, mas quiser sempre consultas do Hive para utilizar os dados mais recentes.
     >
     > Remover uma tabela externa **não** eliminar os dados, a definição de tabela.
     >
     >
   * **FORMATO de linha**: indica ao ramo de registo como estão formatados corretamente os dados. Neste caso, os campos no registo de cada são separados por um espaço.
   * **LOCALIZAÇÃO de TEXTFILE AS ARMAZENADOS**: indica ao ramo de registo onde os dados são armazenados (o diretório de dados de exemplo /) e de que esta está armazenada como texto.
   * **SELECIONE**: seleciona uma contagem de todas as linhas onde coluna **t4** contém o valor **[erro]**. Isto deverá devolver um valor de **3** porque existem três linhas que contêm este valor.
   * **INPUT__FILE__NAME como '%.log'** -diz ao ramo de registo que iremos deverá devolver apenas dados de ficheiros que termina em. registo. Isto limita a pesquisa para o ficheiro de sample.log que contém os dados e mantém-de devolver dados a partir de outro exemplo ficheiros de dados que corresponde ao esquema definido.
4. Utilize as seguintes instruções para criar uma nova tabela 'interna' com o nome **foram**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    As declarações de efetuar as seguintes ações:

   * **Criar tabela não se existe**: cria uma tabela se ainda não existir. Porque o **externo** palavra-chave não é utilizada, esta é uma tabela interna, o que é armazenada no armazém de dados do Hive e for gerida por completo o Hive.

     > [!NOTE]
     > Ao contrário **externo** tabelas, remover uma tabela interna também elimina os dados subjacentes.
     >
     >
   * **ARMAZENADOS ORC de AS**: armazena os dados no formato de (ORC) columnar linha otimizada. Este é um formato altamente otimizado e eficiente para armazenar dados do Hive.
   * **SUBSTITUIR INSERT... SELECIONE**: seleciona as linhas do **log4jLogs** tabela que contenham **[erro]**, insere, em seguida, os dados para o **foram** tabela.

     Para verificar que apenas as linhas que contêm **[erro]** na coluna t4 foram armazenadas para o **foram** tabela, utilize a seguinte instrução para devolver todas as linhas da **foram**:

       SELECIONAR * de foram;

     Três linhas de dados devem ser devolvidas, todos os contentora **[erro]** na coluna t4.

## <a id="summary"></a>Resumo
Como pode ver, ao comando de ramo de registo fornece uma forma fácil de forma interativa para executar consultas do Hive num cluster do HDInsight, monitorizar o estado da tarefa e obter o resultado.

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





[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx
