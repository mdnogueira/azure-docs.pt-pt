---
title: Utilize o IU Tez com o HDInsight baseado em Windows - Azure | Microsoft Docs
description: Saiba como utilizar a IU de Tez para depurar tarefas Tez no HDInsight do HDInsight baseados em Windows.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: a55bccb9-7c32-4ff2-b654-213a2354bd5c
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 32f6a12544c05dbf4ac65dd386cd9dea18ca79b3
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-the-tez-ui-to-debug-tez-jobs-on-windows-based-hdinsight"></a>Utilizar a IU de Tez para depurar Tez tarefas do HDInsight baseado em Windows
A IU de Tez é uma página web que podem ser utilizadas para compreender e depurar tarefas que utilizam o Tez como o motor de execução nos clusters do HDInsight baseados em Windows. A IU de Tez permite-lhe visualizar a tarefa como um gráfico de itens ligados, explorar cada item e obter as estatísticas e as informações de registo.

> [!IMPORTANT]
> Os passos neste documento exigem um cluster do HDInsight que utiliza o Windows. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a name="prerequisites"></a>Pré-requisitos
* Um cluster do HDInsight baseados em Windows. Para obter os passos sobre como criar um novo cluster, consulte [começar a utilizar o HDInsight baseado em Windows](hdinsight-hadoop-tutorial-get-started-windows.md).

  > [!IMPORTANT]
  > A IU de Tez só está disponível nos clusters do HDInsight baseado em Windows criados após 8th de Fevereiro de 2016.
  >
  >
* Um cliente de ambiente de trabalho remoto baseado em Windows.

## <a name="understanding-tez"></a>Noções sobre Tez
Tez é uma arquitetura extensível para processamento de dados no Hadoop que fornece maiores velocidades de processamento de MapReduce tradicional. Para os clusters do HDInsight baseado em Windows, é um motor de opcional que pode ativar para o ramo de registo utilizando o seguinte comando como parte da sua consulta do Hive:

    set hive.execution.engine=tez;

Quando o trabalho é submetido para Tez, cria um direcionado Acíclico gráfico (DAG) que descreve a ordem de execução das ações necessárias por tarefa. Ações individuais são denominadas vértices e executar uma parte da tarefa geral. A execução do trabalho descrita através de um vértice real denomina-se uma tarefa e pode ser distribuída por vários nós no cluster.

### <a name="understanding-the-tez-ui"></a>Noções sobre a IU no Tez
A IU de Tez é que uma página web fornece informações sobre os processos que estão em execução ou ter sido executaram utilizando Tez. Permite-lhe ver o DAG gerado pelo Tez, como é distribuído entre clusters, contadores, tais como a memória utilizada por tarefas e vértices e as informações de erro. -Pode oferecer informações úteis nos seguintes cenários:

* Monitorização de execução longa processa, ver o progresso do mapa do e reduzir as tarefas.
* Analisar dados históricos para processos com êxito ou falhados saber como pode ser melhorado processamento ou por que falhou.

## <a name="generate-a-dag"></a>Gerar um DAG
A IU de Tez irá conter apenas dados se uma tarefa que utiliza o motor de Tez está atualmente em execução ou foi executada no passado. Simples consultas do Hive, normalmente, podem ser resolvidas sem utilizar Tez, no entanto mais complexas consultas que fazer filtragem, agrupamento, ordenação, associações, etc. normalmente, irá necessitar Tez.

Utilize os seguintes passos para executar uma consulta de Hive que irão executar utilizando Tez.

1. Num browser, navegue para https://CLUSTERNAME.azurehdinsight.net, onde **CLUSTERNAME** é o nome do cluster do HDInsight.
2. No menu na parte superior da página, selecione o **Editor do Hive**. Isto irá apresentar uma página com a seguinte consulta de exemplo.

        Select * from hivesampletable

    Apagar a consulta de exemplo e substitua-o com o seguinte.

        set hive.execution.engine=tez;
        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;
3. Selecione o **submeter** botão. O **tarefa sessão** secção na parte inferior da página irá apresentar o estado da consulta. Depois do estado muda para **concluído**, selecione o **ver detalhes** ligação para ver os resultados. O **resultado da tarefa** deve ser semelhante ao seguinte:

        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        en-GB   Nairobi Area    Kenya

## <a name="use-the-tez-ui"></a>Utilizar a IU no Tez
> [!NOTE]
> A IU de Tez só está disponível no ambiente de trabalho, nós de cluster head, pelo que deverá utilizar o ambiente de trabalho remoto para ligar a nós principais.
>
>

1. Do [portal do Azure](https://portal.azure.com), selecione o cluster do HDInsight. Na parte superior do painel do HDInsight, selecione o **ambiente de trabalho remoto** ícone. Este será apresentado o painel de ambiente de trabalho remoto

    ![Ícone de ambiente de trabalho remoto](./media/hdinsight-debug-tez-ui/remotedesktopicon.png)
2. A partir do painel do ambiente de trabalho remoto, selecione **Connect** para ligar ao nó principal do cluster. Quando lhe for pedido, utilize o nome de utilizador do ambiente de trabalho remoto de cluster e a palavra-passe para autenticar a ligação.

    ![Ícone de ligação de ambiente de trabalho remoto](./media/hdinsight-debug-tez-ui/remotedesktopconnect.png)

   > [!NOTE]
   > Se não tiver ativado a conectividade de ambiente de trabalho remoto, forneça um nome de utilizador, a palavra-passe e a data de expiração, em seguida, selecione **ativar** para ativar o ambiente de trabalho remoto. Assim que tiver sido ativada, utilize os passos anteriores para estabelecer a ligação.
   >
   >
3. Assim que estiver ligado, abra o Internet Explorer no ambiente de trabalho remoto, selecione o ícone de equipamento no canto superior direito do browser e, em seguida, selecione **ver definições de compatibilidade**.
4. Na parte inferior do **ver definições de compatibilidade**, desmarque a caixa de verificação **apresentar sites da intranet na vista de compatibilidade** e **apresenta uma lista de compatibilidade de utilização Microsoft**e, em seguida, selecione **fechar**.
5. No Internet Explorer, navegue até à http://headnodehost:8188/tezui / #/. Isto irá apresentar a IU no Tez

    ![Tez IU](./media/hdinsight-debug-tez-ui/tezui.png)

    Quando carrega a IU de Tez, verá uma lista de DAGs que estão atualmente em execução, ou que foram executadas no cluster. A vista predefinida inclui o nome do Dag, Id, submissor, estado, hora de início, hora de fim, duração, ID da aplicação e fila. É possível adicionar mais colunas utilizando o ícone de equipamento no lado direito da página.

    Se tiver apenas uma entrada, será para a consulta que tenha sido executada na secção anterior. Se tiver várias entradas, pode procurar introduzindo critérios de pesquisa nos campos acima DAGs, em seguida, prima **Enter**.
6. Selecione o **Dag nome** para a entrada mais recente do DAG. Esta ação apresenta informações sobre o DAG, bem como a opção para transferir um zip dos ficheiros JSON que contêm informações sobre o DAG.

    ![Detalhes do DAG](./media/hdinsight-debug-tez-ui/dagdetails.png)
7. Acima do **DAG detalhes** são várias ligações que podem ser utilizadas para apresentar informações sobre o DAG.

   * **Contadores do DAG** apresenta informações sobre os contadores para este DAG.
   * **Vista gráfica** apresenta uma representação gráfica deste DAG.
   * **Todos os vértices** apresenta uma lista de vértices neste DAG.
   * **Todas as tarefas** apresenta uma lista de tarefas para todos os vértices neste DAG.
   * **Todos os TaskAttempts** apresenta informações sobre as tentativas para executar tarefas para esta DAG.

     > [!NOTE]
     > Se se deslocar para a apresentação de coluna para vértices, tarefas e TaskAttempts, tenha em atenção que existem ligações para ver **contadores** e **ver ou transferir registos** para cada linha.
     >
     >

     Se Ocorreu uma falha com a tarefa, os detalhes do DAG apresentará um Estado de FALHADO, juntamente com hiperligações para informações sobre a tarefa falhada. Informações de diagnóstico serão apresentadas abaixo os detalhes do DAG.
8. Selecione **visualização gráfica**. Esta ação apresenta uma representação gráfica do DAG. É possível colocar o rato sobre cada vértice na vista para apresentar informações acerca do mesmo.

    ![Vista gráfica](./media/hdinsight-debug-tez-ui/dagdiagram.png)
9. Clicar num vértice carregará o **vértice detalhes** para esse item. Clique em de **mapa 1** vértice para apresentar os detalhes para este item. Selecione **confirmar** para confirmar o painel de navegação.

    ![Detalhes de vértice](./media/hdinsight-debug-tez-ui/vertexdetails.png)
10. Tenha em atenção que tem agora as ligações na parte superior da página que estão relacionados com vértices e tarefas.

    > [!NOTE]
    > Também pode chegam esta página por Retroceder para **DAG detalhes**, selecionando **vértice detalhes**e, em seguida, selecionar o **mapa 1** vértice.
    >
    >

    * **Contadores de vértice** apresenta informações do contador para este vértice.
    * **Tarefas** apresenta tarefas para esta vértice.
    * **Tentativas de tarefas** apresenta informações sobre as tentativas para executar tarefas para esta vértice.
    * **Origens & Sinks** mostra origens de dados e sinks para este vértice.

      > [!NOTE]
      > Como com o menu anterior, pode se deslocar para a apresentação de coluna para tarefas, as tentativas de tarefas e origens & Sinks__ apresentar ligações para obter mais informações para cada item.
      >
      >
11. Selecione **tarefas**e, em seguida, selecione o item com o nome **00_000000**. Isto irá apresentar **detalhes da tarefa** para esta tarefa. Neste ecrã, pode ver **tarefas contadores** e **tarefas tentativas**.

    ![Detalhes da tarefa](./media/hdinsight-debug-tez-ui/taskdetails.png)

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu como utilizar a vista de Tez, saiba mais sobre [utilizando o Hive no HDInsight](hadoop/hdinsight-use-hive.md).

Para obter mais informações técnicas no Tez, consulte o [Tez página em Hortonworks](http://hortonworks.com/hadoop/tez/).
