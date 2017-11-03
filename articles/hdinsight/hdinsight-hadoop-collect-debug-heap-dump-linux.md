---
title: "Ativar capturas de área dinâmica para dados de serviços do Hadoop no HDInsight - Azure | Microsoft Docs"
description: "Ative a área dinâmica para dados de informações para serviços do Hadoop de clusters do HDInsight baseado em Linux para depuração e de análise."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 8f151adb-f687-41e4-aca0-82b551953725
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: larryfr
ms.openlocfilehash: d61da54fc3172a730740e2b94a89fe787c4f121d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-heap-dumps-for-hadoop-services-on-linux-based-hdinsight"></a>Ativar capturas de área dinâmica para dados de serviços do Hadoop no HDInsight baseado em Linux

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Área dinâmica para dados capturas contém um instantâneo de memória da aplicação, incluindo os valores das variáveis no momento que a captura foi criada. Por isso, são úteis para diagnosticar problemas que ocorram em tempo de execução.

> [!IMPORTANT]
> Os passos neste documento apenas trabalham com clusters do HDInsight que utilizam o Linux. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a name="whichServices"></a>Serviços

Pode ativar a área dinâmica para dados de informações para os seguintes serviços:

* **hcatalog** -tempelton
* **ramo de registo** -hiveserver2, metastore, derbyserver
* **mapreduce** -jobhistoryserver
* **yarn** -resourcemanager, nodemanager, timelineserver
* **hdfs** -datanode secondarynamenode, namenode

Também pode ativar a área dinâmica para dados de informações para o mapa e reduzir os processos executaram pelo HDInsight.

## <a name="configuration"></a>Configuração de informação de área dinâmica para dados de conhecimento

Capturas de área dinâmica para dados são ativadas por transmitir opções (por vezes conhecida como significa adesão, ou parâmetros) para a JVM quando um serviço é iniciado. Para a maioria dos serviços do Hadoop, pode modificar o script de shell utilizado para iniciar o serviço para transmitir estas opções.

Em cada script, há uma exportação para  **\* \_OPTS**, que contém as opções transmitidas para a JVM. Por exemplo, no **hadoop env.sh** script, a linha que começa com `export HADOOP_NAMENODE_OPTS=` contém opções para o serviço de NameNode.

Mapear e reduzir os processos são ligeiramente diferentes, como estas operações são um processo subordinado do serviço de MapReduce. Cada mapear ou reduzir o processo é executado num contentor subordinado e não existirem duas entradas que contêm as opções de JVM. Ambos contidas no **mapred site.xml**:

* **mapreduce.Admin.Map.child.Java.opts**
* **mapreduce.Admin.reduce.child.Java.opts**

> [!NOTE]
> É recomendável utilizar Ambari para modificar os scripts e de definições de mapred site.xml, como o identificador do Ambari replicar as alterações em nós do cluster. Consulte o [utilizando Ambari](#using-ambari) secção para obter passos específicos.

### <a name="enable-heap-dumps"></a>Ativar capturas de área dinâmica para dados

A seguinte opção permite capturas de área dinâmica para dados quando ocorre um OutOfMemoryError:

    -XX:+HeapDumpOnOutOfMemoryError

O  **+**  indica que esta opção está ativada. A predefinição está desativada.

> [!WARNING]
> Capturas de área dinâmica para dados não estão ativadas para serviços do Hadoop no HDInsight por predefinição, como os ficheiros de informação podem ser elevados. Se a ativá-los para resolução de problemas, lembre-se de desativá-las depois de ter reproduzido o problema e recolhidos os ficheiros de informação.

### <a name="dump-location"></a>Localização de captura

A localização predefinida para o ficheiro de informação é o atual diretório de trabalho. Pode controlar em que o ficheiro é armazenado utilizando a seguinte opção:

    -XX:HeapDumpPath=/path

Por exemplo, utilizando `-XX:HeapDumpPath=/tmp` faz com que as informações de estado ser armazenado no diretório /tmp dos.

### <a name="scripts"></a>Scripts

Também pode acionar um script quando um **OutOfMemoryError** ocorre. Por exemplo, a acionar uma notificação para saber que ocorreu o erro. Utilize a opção seguinte para acionar um script num __OutOfMemoryError__:

    -XX:OnOutOfMemoryError=/path/to/script

> [!NOTE]
> Uma vez que Hadoop um sistema distribuído, qualquer script utilizada tem de ser colocado em todos os nós do cluster que executa o serviço no.
> 
> O script deve também estar numa localização que seja acessível pela conta de serviço é executado como e tem de fornecer permissões de execução. Por exemplo, pode pretender armazenar scripts no `/usr/local/bin` e utilizar `chmod go+rx /usr/local/bin/filename.sh` para conceder a leitura e de executar permissões.

## <a name="using-ambari"></a>Com o Ambari

Para modificar a configuração para um serviço, utilize os seguintes passos:

1. Abra o web do Ambari da IU para o cluster. O URL é https://YOURCLUSTERNAME.azurehdinsight.net.

    Quando lhe for pedido, autenticar para o site utilizando o nome da conta HTTP (predefinição: admin) e palavra-passe para o cluster.

   > [!NOTE]
   > Poderá ser solicitado uma segunda vez pela Ambari para o nome de utilizador e palavra-passe. Se assim for, introduza o mesmo nome de conta e palavra-passe

2. Utilizando a lista de no lado esquerdo, selecione a área de serviço que pretende modificar. Por exemplo, **HDFS**. Na área de System center, selecione o **folhas** separador.

    ![Imagem da web do Ambari com separador folhas do HDFS selecionado](./media/hdinsight-hadoop-heap-dump-linux/serviceconfig.png)

3. Utilizar o **filtro...**  entrada, introduza **significa adesão**. Apenas os itens que contém este texto são apresentados.

    ![Lista filtrada](./media/hdinsight-hadoop-heap-dump-linux/filter.png)

4. Localizar o  **\* \_OPTS** entrada para o serviço que pretende ativar a área dinâmica para dados de informações para e adicionar as opções que pretende ativar. Na imagem seguinte, posso adicionou `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` para o **HADOOP\_NAMENODE\_OPTS** entrada:

    ![HADOOP_NAMENODE_OPTS com - XX: + HeapDumpOnOutOfMemoryError - XX: HeapDumpPath = / tmp /](./media/hdinsight-hadoop-heap-dump-linux/opts.png)

   > [!NOTE]
   > Quando ativar a área dinâmica para dados informações de estado para o mapa ou reduza Subprocesso, procure para os campos com o nome **mapreduce.admin.map.child.java.opts** e **mapreduce.admin.reduce.child.java.opts**.

    Utilize o **guardar** botão para guardar as alterações. Pode introduzir uma curta tenha em atenção que descreve as alterações.

5. Depois de tem sido aplicadas as alterações, o **reinício necessário** ícone é apresentado junto a um ou mais serviços.

    ![Reinicie o ícone necessária e reiniciar botão](./media/hdinsight-hadoop-heap-dump-linux/restartrequiredicon.png)

6. Selecione cada serviço que necessita de um reinício e utilize o **serviço ações** botão para **ativar no modo de manutenção**. Modo de manutenção impede que os alertas que está a ser gerados a partir do serviço quando reiniciá-la.

    ![Ativar o menu do modo de manutenção](./media/hdinsight-hadoop-heap-dump-linux/maintenancemode.png)

7. Assim que tiver ativado o modo de manutenção, utilize o **reiniciar** botão para o serviço **reiniciar todos os afetado**

    ![Reinicie a entrada de todos os afetados](./media/hdinsight-hadoop-heap-dump-linux/restartbutton.png)

   > [!NOTE]
   > as entradas para a **reiniciar** botão poderão ser diferente para outros serviços.

8. Assim que os serviços foram reiniciados, utilize o **serviço ações** botão para **Ativar desativar modo de manutenção**. Este Ambari para retomar a monitorização de alertas para o serviço.

