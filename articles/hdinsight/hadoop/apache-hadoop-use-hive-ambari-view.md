---
title: Utilizar vistas do Ambari para trabalhar com o Hive no HDInsight (Hadoop) - Azure | Microsoft Docs
description: Saiba como utilizar a vista do Hive do seu browser para submeter consultas do Hive. A vista do Hive faz parte da IU da Web do Ambari fornecido com o cluster do HDInsight baseado em Linux.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1abe9104-f4b2-41b9-9161-abbc43de8294
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/23/2017
ms.author: larryfr
ms.openlocfilehash: 8293da8c77725d051f295826d9a78bf81055dcb3
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-ambari-hive-view-with-hadoop-in-hdinsight"></a>Utilizar a vista Ambari Hive com o Hadoop no HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Saiba como executar consultas do Hive, utilizando a vista do Hive do Ambari. Ambari é uma gestão e monitorização utilitário fornecido com clusters do HDInsight baseado em Linux. Uma das funcionalidades fornecidas através do Ambari é uma IU da Web que pode ser utilizado para executar consultas do Hive.

> [!NOTE]
> Ambari tem muitas funcionalidades que não são debatidas neste documento. Para obter mais informações, consulte [gerir clusters do HDInsight utilizando a IU da Web do Ambari](../hdinsight-hadoop-manage-ambari.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do HDInsight baseado em Linux. Para obter informações sobre a criação de clusters, consulte [começar a utilizar o Hadoop no HDInsight](apache-hadoop-linux-tutorial-get-started.md).

> [!IMPORTANT]
> Os passos neste documento exigem um cluster do HDInsight do Azure que utiliza o Linux. Linux é o único sistema operativo utilizado no HDInsight versão 3.4 ou posterior. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a name="open-the-hive-view"></a>Abrir a vista do Hive

Pode abrir as vistas Ambari do portal do Azure. Selecione o cluster do HDInsight e, em seguida, selecione **vistas do Ambari** do **ligações rápidas** secção.

![secção de ligações rápidas do portal](./media/apache-hadoop-use-hive-ambari-view/quicklinks.png)

Na lista de vistas, selecione __vista do Hive__.

![A vista de ramo de registo selecionada](./media/apache-hadoop-use-hive-ambari-view/select-hive-view.png)

> [!NOTE]
> Quando estiver a aceder a Ambari, é-lhe pedido que autenticar para o site. Introduza o administrador (predefinição `admin`) conta nome e palavra-passe que utilizou quando criou o cluster.

Deverá ver uma página semelhante para a imagem seguinte:

![Imagem da folha de cálculo de consulta para a vista do Hive](./media/apache-hadoop-use-hive-ambari-view/ambari-hive-view.png)

## <a name="run-a-query"></a>Executar uma consulta

Para executar uma consulta do Hive, utilize os seguintes passos da vista do Hive.

1. Do __consulta__ separador, cole as seguintes declarações HiveQL na folha de cálculo:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;
    ```

    As declarações de efetuar as seguintes ações:

   * `DROP TABLE`: Elimina a tabela e o ficheiro de dados, no caso da tabela já existe.

   * `CREATE EXTERNAL TABLE`: Cria uma nova tabela de "external" no ramo de registo.
   As tabelas externas armazenam a definição de tabela no ramo de registo. Os dados for deixados na localização original.

   * `ROW FORMAT`: Mostra como estão formatados corretamente os dados. Neste caso, os campos no registo de cada são separados por um espaço.

   * `STORED AS TEXTFILE LOCATION`: Mostra onde os dados são armazenados e de que esta está armazenada como texto.

   * `SELECT`: Seleciona uma contagem de todas as linhas em que a coluna t4 contém o valor [erro].

     > [!NOTE]
     > Utilize as tabelas externas quando espera que os dados subjacentes ser atualizados por uma origem externa, tais como um dados automatizado carregar processo ou outra operação de MapReduce. Remover uma tabela externa *não* eliminar os dados, a definição de tabela.

    > [!IMPORTANT]
    > Deixe o __base de dados__ seleção no __predefinido__. Os exemplos neste documento utilizam a base de dados predefinida incluído com o HDInsight.

2. Para iniciar a consulta, utilize o **executar** no botão abaixo a folha de cálculo. O botão fica laranja e o texto muda para **parar**.

3. Após concluir a consulta, o **resultados** separador apresenta os resultados da operação. O seguinte texto é o resultado da consulta:

        sev       cnt
        [ERROR]   3

    Pode utilizar o **registos** separador para ver as informações de registo que criou a tarefa.

   > [!TIP]
   > Transferir ou guardar resultados do **guardar resultados** caixa de diálogo de lista pendente no canto superior esquerdo do **resultados do processo de consulta** secção.

4. Selecione as quatro primeiras linhas desta consulta e, em seguida, selecione **executar**. Tenha em atenção que existem não existem resultados quando a tarefa estiver concluída. Utilizar o **executar** botão quando é selecionada a parte da consulta apenas executa as instruções de selecionado. Neste caso, a seleção não tenha incluído a instrução final que obtém linhas da tabela. Se selecionar apenas dessa linha e utilizar **executar**, deverá ver os resultados esperados.

5. Para adicionar uma folha de cálculo, utilize o **nova folha de cálculo** na parte inferior do **Editor de consultas**. Na nova folha de cálculo, introduza as seguintes declarações HiveQL:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
    ```

  As declarações de efetuar as seguintes ações:

   * **Criar tabela não se existe**: cria uma tabela se ainda não existir. Porque o **externo** palavra-chave não é utilizada, é criada uma tabela interna. Uma tabela interna é armazenada no armazém de dados do Hive e for gerida por completo o Hive. Ao contrário com as tabelas externas, remover uma tabela interna elimina dados subjacentes.

   * **ARMAZENADOS ORC de AS**: armazena os dados no formato otimizada linha Columnar (ORC). ORC é um formato altamente otimizado e eficiente para armazenar dados do Hive.

   * **SUBSTITUIR INSERT... SELECIONE**: seleciona as linhas do **log4jLogs** tabela que contenham `[ERROR]`e, em seguida, insere os dados para o **foram** tabela.

Utilize o **executar** botão para executar esta consulta. O **resultados** separador não contém quaisquer informações quando a consulta devolve zero linhas. Deve mostrar o estado como **com êxito** após a conclusão da consulta.

### <a name="visual-explain"></a>Explicar Visual

Para apresentar uma visualização de plano de consulta, selecione o **explicam Visual** separador abaixo a folha de cálculo.

O **explicam Visual** vista da consulta poderá ser útil compreender o fluxo de consultas complexas. Pode ver um equivalente textual desta vista, utilizando o **explicativo** botão no Editor de consultas.

### <a name="tez-ui"></a>Tez IU

Para apresentar a IU de Tez para a consulta, selecione o **Tez** separador abaixo a folha de cálculo.

> [!IMPORTANT]
> Tez não é utilizado para resolver todas as consultas. Pode resolver demasiadas consultas sem utilizar Tez. 

Se Tez foi utilizado para resolver a consulta, é apresentado o direcionado Acíclico gráfico (DAG). Se pretender ver o DAG para consultas que já executou no passado, ou se pretende depurar o processo de Tez, utilize o [Tez vista](../hdinsight-debug-ambari-tez-view.md) em vez disso.

## <a name="view-job-history"></a>Ver histórico de tarefas

O __tarefas__ separador apresenta um histórico de consultas do Hive.

![Imagem de histórico da tarefa](./media/apache-hadoop-use-hive-ambari-view/job-history.png)

## <a name="database-tables"></a>Tabelas de base de dados

Pode utilizar o __tabelas__ separador para trabalhar com tabelas dentro de uma base de dados do Hive.

![Imagem de separador de tabelas](./media/apache-hadoop-use-hive-ambari-view/tables.png)

## <a name="saved-queries"></a>Consultas guardadas

Do **consulta** separador, opcionalmente, pode guardar consultas. Depois de guardar uma consulta, pode reutilizá-lo do __consultas guardadas__ separador.

![Imagem de separador de consultas guardadas](./media/apache-hadoop-use-hive-ambari-view/saved-queries.png)

## <a name="user-defined-functions"></a>Funções definidas pelo utilizador

Também pode expandir o ramo de registo através de funções definidas pelo utilizador (UDF). Utilize um UDF para implementar funcionalidades ou lógica que não é modelada facilmente no HiveQL.

Declarar e guardar um conjunto de UDFs utilizando o **UDF** separador no topo da vista do Hive. Estas UDFs podem ser utilizados com o **Editor de consultas**.

![Imagem de separador UDF](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

Depois de acrescentar um UDF para a vista do Hive, um **inserir udfs** botão aparece na parte inferior do **Editor de consultas**. Selecionar esta entrada apresenta uma lista de lista pendente de UDFs definido na vista de ramo de registo. Selecionar um UDF adiciona declarações HiveQL para a ativar UDF da sua consulta.

Por exemplo, se definiu um UDF com as seguintes propriedades:

* Nome do recurso: myudfs

* Caminho do recurso: /myudfs.jar

* Nome UDF: myawesomeudf

* Nome da classe UDF: com.myudfs.Awesome

Utilizar o **inserir udfs** botão apresenta uma entrada com o nome **myudfs**, com outra na lista pendente para cada UDF definida para esse recurso. Neste caso, é **myawesomeudf**. Selecionar esta entrada adiciona as seguintes para o início da consulta:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Em seguida, pode utilizar UDF na sua consulta. Por exemplo, `SELECT myawesomeudf(name) FROM people;`.

Para obter mais informações sobre como utilizar UDFs com o Hive no HDInsight, consulte os artigos seguintes:

* [Com o Python com o Hive e o Pig no HDInsight](python-udf-hdinsight.md)
* [Como adicionar um UDF ramo de registo personalizados para o HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Definições de ramo de registo

Pode alterar várias definições de ramo de registo, tal como alterar o motor de execução para o ramo de registo do Tez (predefinição) para MapReduce.

## <a id="nextsteps"></a>Passos seguintes

Para obter informações gerais sobre o Hive no HDInsight:

* [Utilizar o Hive com o Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras formas pode trabalhar com o Hadoop no HDInsight:

* [Utilizar o Pig com o Hadoop no HDInsight](hdinsight-use-pig.md)
* [Utilizar o MapReduce com o Hadoop no HDInsight](hdinsight-use-mapreduce.md)
