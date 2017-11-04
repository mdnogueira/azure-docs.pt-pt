---
title: O Hive com ferramentas do Data Lake (Hadoop) para o Visual Studio - Azure HDInsight | Microsoft Docs
description: Saiba como utilizar as ferramentas do Data Lake para Visual Studio para executar consultas do Hive Apache com Apache Hadoop no Azure HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2b3e672a-1195-4fa5-afb7-b7b73937bfbe
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/28/2017
ms.author: larryfr
ms.openlocfilehash: b1d9fbfbf2fc1a17611842ff11409769058e9fba
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="run-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Executar consultas do Hive, utilizando as ferramentas do Data Lake para Visual Studio

Saiba como utilizar as ferramentas do Data Lake para Visual Studio para consulta Apache Hive. As ferramentas do Data Lake permitem-lhe criar facilmente, submeter e monitorizar as consultas do Hive ao Hadoop no Azure HDInsight.

## <a id="prereq"></a>Pré-requisitos

* Um cluster do Azure HDInsight (Hadoop no HDInsight)

  > [!IMPORTANT]
  > O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* Visual Studio (uma das seguintes versões):

    * Visual Studio 2013 Community/Professional/Premium/Ultimate com atualização 4

    * Visual Studio 2015 (qualquer edição)

    * Visual Studio 2017 (qualquer edição)

* Ferramentas do HDInsight para Visual Studio ou o Azure Data Lake tools para Visual Studio. Consulte [começar a utilizar as ferramentas Hadoop do Visual Studio para o HDInsight](apache-hadoop-visual-studio-tools-get-started.md) para obter informações sobre como instalar e configurar as ferramentas.

## <a id="run"></a>Executar consultas do Hive com o Visual Studio

1. Abra **Visual Studio** e selecione **novo** > **projeto** > **do Azure Data Lake** > **HIVE** > **aplicação do Hive**. Forneça um nome para este projeto.

2. Abra o **Script.hql** ficheiro criado com este projeto e cole as seguintes declarações HiveQL:

   ```hiveql
   set hive.execution.engine=tez;
   DROP TABLE log4jLogs;
   CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
   STORED AS TEXTFILE LOCATION '/example/data/';
   SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
   ```

    As declarações de efetuar as seguintes ações:

   * `DROP TABLE`: Se a tabela existe, esta instrução elimina-a.

   * `CREATE EXTERNAL TABLE`: Cria uma nova tabela 'externa' no ramo de registo. As tabelas externas apenas armazenam a definição da tabela no ramo de registo (os dados for deixados na localização original).

     > [!NOTE]
     > As tabelas externas devem ser utilizadas ao espera os dados subjacentes ser atualizados por uma origem externa. Por exemplo, uma tarefa de MapReduce ou serviço do Azure.
     >
     > Remover uma tabela externa **não** eliminar os dados, a definição de tabela.

   * `ROW FORMAT`: Indica ao ramo de registo como estão formatados corretamente os dados. Neste caso, os campos no registo de cada são separados por um espaço.

   * `STORED AS TEXTFILE LOCATION`: Indica ao ramo de registo que os dados são armazenados no diretório/dados de exemplo e que é armazenada como texto.

   * `SELECT`: Selecione uma contagem de todas as linhas onde coluna `t4` contém o valor `[ERROR]`. Esta declaração devolve um valor `3` porque existem três linhas que contêm este valor.

   * `INPUT__FILE__NAME LIKE '%.log'`-Diz ao ramo de registo que iremos deverá devolver apenas dados de ficheiros que termina em. registo. Esta cláusula restringe a pesquisa para o ficheiro de sample.log que contém os dados.

3. Na barra de ferramentas, selecione o **Cluster do HDInsight** que pretende utilizar para esta consulta. Selecione **submeter** para executar as instruções de como uma tarefa do Hive.

   ![Submeter barra](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

4. O **resumo da tarefa do Hive** aparece e apresenta informações sobre a tarefa em execução. Utilize o **atualizar** ligação para atualizar as informações da tarefa, até que o **estado da tarefa** alterações **concluído**.

   ![Resumo da tarefa apresentar uma tarefa concluída](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

5. Utilize o **resultado da tarefa** ligação para ver o resultado da tarefa. Apresenta `[ERROR] 3`, que é o valor devolvido por esta consulta.

6. Também pode executar consultas do Hive sem criar um projeto. Utilizar **Explorador de servidores**, expanda **Azure** > **HDInsight**, clique no seu servidor do HDInsight e, em seguida, selecione **escrever uma consulta do Hive**.

7. No **temp.hql** documento apresentado, adicione as seguintes declarações HiveQL:

   ```hiveql
   set hive.execution.engine=tez;
   CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
   INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
   ```

    As declarações de efetuar as seguintes ações:

   * `CREATE TABLE IF NOT EXISTS`: Cria uma tabela se ainda não existir. Porque o `EXTERNAL` palavra-chave não é utilizada, esta instrução cria uma tabela interna. Tabelas internas são armazenadas no armazém de dados do Hive e são geridas pelo ramo de registo.

     > [!NOTE]
     > Ao contrário `EXTERNAL` tabelas, remover uma tabela interna também elimina os dados subjacentes.

   * `STORED AS ORC`: Armazena os dados no formato de (ORC) columnar linha otimizada. ORC é um formato altamente otimizado e eficiente para armazenar dados do Hive.

   * `INSERT OVERWRITE ... SELECT`: Seleciona as linhas do `log4jLogs` tabela que contenham `[ERROR]`, insere, em seguida, os dados para o `errorLogs` tabela.

8. Na barra de ferramentas, selecione **submeter** para executar a tarefa. Utilize o **estado da tarefa** para determinar que a tarefa foi concluída com êxito.

9. Para verificar que a tarefa criada a tabela, utilize **Explorador de servidores** e expanda **Azure** > **HDInsight** > cluster do HDInsight > **bases de dados do Hive** > **predefinido**. O **foram** tabela e o **log4jLogs** tabela são listados.

## <a id="nextsteps"></a>Passos seguintes

Como pode ver, as ferramentas do HDInsight para Visual Studio fornecem uma forma fácil de trabalhar com consultas do Hive no HDInsight.

Para obter informações gerais sobre o Hive no HDInsight:

* [Utilizar o Hive com o Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras formas pode trabalhar com o Hadoop no HDInsight:

* [Utilizar o Pig com o Hadoop no HDInsight](hdinsight-use-pig.md)

* [Utilizar o MapReduce com o Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Para obter mais informações sobre as ferramentas do HDInsight para Visual Studio:

* [Introdução às ferramentas HDInsight para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)

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

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
