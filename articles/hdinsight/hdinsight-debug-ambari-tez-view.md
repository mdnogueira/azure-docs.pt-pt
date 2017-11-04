---
title: Utilizar Ambari Tez vista com o HDInsight - Azure | Microsoft Docs
description: Saiba como utilizar a vista Ambari Tez para depurar tarefas Tez no HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 9c39ea56-670b-4699-aba0-0f64c261e411
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/04/2017
ms.author: larryfr
ms.openlocfilehash: e0df58c24653d60e7594a70b911540662548a236
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-ambari-views-to-debug-tez-jobs-on-hdinsight"></a>Utilizar vistas do Ambari para depurar tarefas Tez no HDInsight

IU da Web do Ambari para o HDInsight contém uma vista de Tez que pode ser utilizada para compreender e depurar tarefas que utilizam Tez. A vista de Tez permite-lhe visualizar a tarefa como um gráfico de itens ligados, explorar cada item e obter as estatísticas e as informações de registo.

> [!IMPORTANT]
> Os passos neste documento exigem um cluster do HDInsight que utiliza o Linux. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, consulte [controlo de versões do HDInsight componente](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do HDInsight baseado em Linux. Para obter os passos sobre como criar um cluster, consulte [começar a utilizar o HDInsight baseado em Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Um browser moderno que suporte HTML5.

## <a name="understanding-tez"></a>Noções sobre Tez

Tez é uma arquitetura extensível para processamento de dados no Hadoop que fornece maiores velocidades de processamento de MapReduce tradicional. Para clusters do HDInsight baseado em Linux, é o motor de predefinido para o ramo de registo.

Tez cria um direcionado Acíclico gráfico (DAG) que descreve a ordem das ações requeridas pelas tarefas. Ações individuais são denominadas vértices e executar uma parte da tarefa geral. A execução do trabalho descrita através de um vértice real denomina-se uma tarefa e pode ser distribuída por vários nós no cluster.

### <a name="understanding-the-tez-view"></a>Compreender a vista no Tez

A vista de Tez fornece informações e informação do histórico no processos em execução. Estas informações mostram como uma tarefa é distribuída entre clusters. Também apresenta contadores utilizados pelas tarefas e vértices e informações de erro relacionados com a tarefa. -Pode oferecer informações úteis nos seguintes cenários:

* Monitorização de execução longa processa, ver o progresso do mapa do e reduzir as tarefas.
* Analisar dados históricos para processos com êxito ou falhados saber como pode ser melhorado processamento ou por que falhou.

## <a name="generate-a-dag"></a>Gerar um DAG

A vista de Tez só contém dados se uma tarefa que utiliza o motor de Tez está atualmente em execução ou tiver sido executada anteriormente. As consultas do Hive simples podem ser resolvidas sem utilizar Tez. Consulta mais complexa que efetue a filtragem, agrupamento, ordenação, associações, etc. Utilize o motor de Tez.

Utilize os seguintes passos para executar uma consulta de Hive que utiliza Tez:

1. Num browser, navegue para https://CLUSTERNAME.azurehdinsight.net, onde **CLUSTERNAME** é o nome do cluster do HDInsight.

2. No menu na parte superior da página, selecione o **vistas** ícone. Este ícone assemelha-se uma série de quadrados. Na lista pendente que aparece, selecione **Hive vista**.

    ![Selecionar vista de ramo de registo](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. Quando carrega a vista de ramo de registo, cole a seguinte consulta para o Editor de consultas e, em seguida, clique em **executar**.

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

    Depois de concluída a tarefa, deverá ver a saída apresentada no **resultados do processo de consulta** secção. Os resultados devem ser semelhantes para o seguinte texto:

        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica

4. Selecione o **registo** separador. Consulte informações semelhantes para o seguinte texto:

        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    Guardar o **id da aplicação** valor, tal como este valor é utilizado na secção seguinte.

## <a name="use-the-tez-view"></a>Utilize a vista no Tez

1. No menu na parte superior da página, selecione o **vistas** ícone. Na lista pendente que aparece, selecione **Tez vista**.

    ![Selecionar Tez vista](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. Quando carrega a vista de Tez, pode ver uma lista de consultas do hive que estão atualmente em execução, ou que foram executadas no cluster.

    ![Todos os DAGS](./media/hdinsight-debug-ambari-tez-view/tez-view-home.png)

3. Se tiver apenas uma entrada, trata-se para a consulta que tenha sido executada na secção anterior. Se tiver várias entradas, pode procurar utilizando os campos na parte superior da página.

4. Selecione o **ID de consulta** para uma consulta do Hive. São apresentadas informações sobre a consulta.

    ![Detalhes do DAG](./media/hdinsight-debug-ambari-tez-view/query-details.png)

5. Os separadores desta página permitem-lhe ver as seguintes informações:

    * **Consultar detalhes**: detalhes sobre a consulta do Hive.
    * **Linha cronológica**: informações sobre quanto tempo demorou de cada fase do processamento.
    * **Configurações**: A configuração utilizada para esta consulta.

    De __detalhes de consulta__ pode utilizar as ligações para encontrar informações sobre o __aplicação__ ou __DAG__ para esta consulta.
    
    * O __aplicação__ ligação apresenta informações sobre a aplicação de YARN para esta consulta. Aqui pode aceder a registos de aplicação YARN.
    * O __DAG__ ligação apresenta informações sobre o acíclicos direcionados para esta consulta. Aqui pode ver uma representação gráfica do DAG. Também pode encontrar informações nos vértices dentro do DAG.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como utilizar a vista de Tez, saiba mais sobre [utilizando o Hive no HDInsight](hadoop/hdinsight-use-hive.md).

Para obter mais informações técnicas no Tez, consulte o [Tez página em Hortonworks](http://hortonworks.com/hadoop/tez/).

Para obter mais informações sobre como utilizar Ambari com o HDInsight, consulte [gerir clusters do HDInsight utilizando a IU da Web do Ambari](hdinsight-hadoop-manage-ambari.md)
