---
title: Resolver problemas YARN utilizando o Azure HDInsight | Microsoft Docs
description: "Obtenha respostas a questões recorrentes sobre como trabalhar com Apache Hadoop YARN e o Azure HDInsight."
keywords: "FAQ do HDInsight, YARN, do Azure, manual, perguntas comuns de resolução de problemas"
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: 
editor: 
ms.assetid: F76786A9-99AB-4B85-9B15-CA03528FC4CD
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: arijitt
ms.openlocfilehash: 63f2d88ad59661b7fbcffd0aaeb94c58d40bdb73
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-yarn-by-using-azure-hdinsight"></a>Resolver problemas YARN utilizando o Azure HDInsight

Saiba mais sobre os principais problemas e as resoluções ao trabalhar com payloads do Apache Hadoop YARN no Apache Ambari.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Como posso criar uma nova fila YARN num cluster


### <a name="resolution-steps"></a>Passos de resolução 

Utilize os seguintes passos Ambari para criar uma nova fila YARN e, em seguida, equilibrar a alocação de capacidade entre todas as filas. 

Neste exemplo, dois filas existentes (**predefinido** e **thriftsvr**) ambos são alteradas na capacidade de 50% para a capacidade de 25%, o que lhe dá a nova capacidade de 50% da fila (spark).
| Filas | Capacidade | Capacidade máxima |
| --- | --- | --- | --- |
| predefinição | 25% | 50% |
| thrftsvr | 25% | 50% |
| spark | 50% | 50% |

1. Selecione o **vistas do Ambari** ícone e, em seguida, selecione o padrão de grelha. Em seguida, selecione **Gestor de filas YARN**.

    ![Selecione o ícone de vistas do Ambari](media/hdinsight-troubleshoot-yarn/create-queue-1.png)
2. Selecione o **predefinido** fila.

    ![Seleccione a fila de predefinido](media/hdinsight-troubleshoot-yarn/create-queue-2.png)
3. Para o **predefinido** fila, altere o **capacidade** de 50% 25%. Para o **thriftsvr** fila, altere o **capacidade** 25%.

    ![Altere a capacidade para 25% para as filas predefinidas e thriftsvr](media/hdinsight-troubleshoot-yarn/create-queue-3.png)
4. Para criar uma nova fila, selecione **adicionar fila**.

    ![Selecione adicionar fila](media/hdinsight-troubleshoot-yarn/create-queue-4.png)

5. Nome da nova fila.

    ![Nome da fila do Spark](media/hdinsight-troubleshoot-yarn/create-queue-5.png)  

6. Deixe o **capacidade** valores em 50% e, em seguida, selecione o **ações** botão.

    ![Selecione o botão de ações](media/hdinsight-troubleshoot-yarn/create-queue-6.png)  
7. Selecione **guarde e Atualize as filas**.

    ![Selecione guarde e Atualize as filas](media/hdinsight-troubleshoot-yarn/create-queue-7.png)  

Estas alterações são visíveis imediatamente na IU de programador do YARN.

### <a name="additional-reading"></a>Leitura adicional

- [YARN CapacityScheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)


## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Como transferir registos YARN a partir de um cluster


### <a name="resolution-steps"></a>Passos de resolução 

1. Liga ao cluster do HDInsight utilizando um cliente Secure Shell (SSH). Para obter mais informações, consulte [leitura adicional](#additional-reading-2).

2. Para listar todos os IDs de aplicação das aplicações YARN que estão atualmente em execução, execute o seguinte comando:

    ```apache
    yarn top
    ```
    Os IDs constam o **APPLICATIONID** coluna. Pode transferir os registos do **APPLICATIONID** coluna.

    ```apache
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved

                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

3. Para transferir os registos do contentor YARN para todos os mestres de aplicação, utilize o seguinte comando:
   
    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    Este comando cria um ficheiro de registo com o nome amlogs.txt. 

4. Para transferir os registos do contentor YARN para apenas o mestre de aplicação mais recente, utilize o seguinte comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    Este comando cria um ficheiro de registo com o nome latestamlogs.txt. 

4. Para transferir os registos do contentor YARN para modelos de estrutura mestres primeiro duas aplicações, utilize o seguinte comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt 
    ```

    Este comando cria um ficheiro de registo com o nome first2amlogs.txt. 

5. Para transferir todos os registos do contentor YARN, utilize o seguinte comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    Este comando cria um ficheiro de registo com o nome logs.txt. 

6. Para transferir o registo do contentor YARN para um contentor específico, utilize o seguinte comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt 
    ```

    Este comando cria um ficheiro de registo com o nome containerlogs.txt.

### <a name="additional-reading-2"></a>Leitura adicional

- [Ligar para o HDInsight (Hadoop), utilizando o SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)
- [Conceitos do Apache Hadoop YARN e aplicações](https://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/)







