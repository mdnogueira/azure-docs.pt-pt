---
title: "Os registos de aplicações do Hadoop YARN de acesso no HDInsight baseado em Linux - Azure | Microsoft Docs"
description: "Saiba como aceder a registos de aplicação YARN num cluster HDInsight (Hadoop) baseado em Linux através da linha de comandos e um browser."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 3ec08d20-4f19-4a8e-ac86-639c04d2f12e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: larryfr
ms.openlocfilehash: eea30e60a793563fbde96d11ab4bdb2d01241e60
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="access-yarn-application-logs-on-linux-based-hdinsight"></a>Os registos de aplicação de YARN de acesso no HDInsight baseado em Linux

Saiba como aceder a registos de aplicações de YARN (ainda outro recurso Negotiator) num cluster de Hadoop no Azure HDInsight.

> [!IMPORTANT]
> Os passos neste documento exigem um cluster do HDInsight que utiliza o Linux. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, consulte [controlo de versões do HDInsight componente](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="YARNTimelineServer"></a>Servidor de linha cronológica YARN

O [YARN linha cronológica servidor](http://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) fornece informações genéricas nas aplicações concluídas e informações de aplicação específica do framework através de duas interfaces de rede diferentes. Especificamente:

* Armazenamento e a obtenção de informações de aplicação genérica nos clusters do HDInsight foi ativado com a versão 3.1.1.374 ou superior.
* O componente de informações da aplicação de arquitetura específica do servidor a linha cronológica não está atualmente disponível nos clusters do HDInsight.

Informações genéricas em aplicações incluem o seguinte tipo de dados:

* O ID da aplicação, um identificador exclusivo de uma aplicação
* O utilizador que iniciou a aplicação
* Informações sobre as tentativas para concluir a aplicação
* Os contentores utilizados por qualquer tentativa de aplicação

## <a name="YARNAppsAndLogs"></a>Registos de aplicações de YARN e

YARN suporta vários modelos de programação (MapReduce que está a ser um dos atributos) ao desassociar a gestão de recursos de agendamento/monitorização de aplicações. YARN utiliza um global *ResourceManager* (RM), por--nó de trabalho *NodeManagers* (NMs) e por aplicação *ApplicationMasters* (AMs). O AM por aplicação negoceia recursos (CPU, memória, disco, rede) para executar a aplicação com o RM. O RM funciona com NMs para conceder estes recursos, que são concedidos como *contentores*. O AM é responsável por controlar o progresso dos contentores atribuída pelo RM. Uma aplicação pode necessitar de vários contentores dependendo da natureza da aplicação.

Cada aplicação pode consistir em várias *tentativas da aplicação*. Se uma aplicação falhar, pode ser repetida como uma nova tentativa. Cada tentativa é executado num contentor. Um sentido, um contentor fornece o contexto para a unidade básica de trabalho realizado por uma aplicação YARN. Todo o trabalho que é efetuado no contexto de um contentor é executado no nó de trabalho único no qual foi atribuído o contentor. Consulte [YARN conceitos] [ YARN-concepts] para obter mais referência.

Os registos de aplicações (e os registos do contentor associado) são críticos depurar aplicações do Hadoop problemáticas. O YARN fornece uma arquitetura nice para recolher, Agregar e armazenar os registos de aplicação com o [registo agregação] [ log-aggregation] funcionalidade. A funcionalidade de registo agregação faz com que os registos de aplicações aceder mais determinista. Isto agrega registos através de todos os contentores num nó de trabalho e armazena-os como um ficheiro de registo agregados por nó de trabalho. O registo é armazenado no sistema de ficheiros predefinido após a conclusão de uma aplicação. A aplicação pode utilizar centenas ou milhares de contentores, mas os registos para todos os contentores executados num nó único do worker sempre são agregados para um único ficheiro. Por isso apenas é 1 registo por nó de trabalho utilizada pela sua aplicação. Agregação de registo está ativada por predefinição em clusters do HDInsight versão 3.0 e posteriores. Agregados registos estão localizados no armazenamento de predefinido para o cluster. O caminho seguinte é o caminho do HDFS para os registos:

    /app-logs/<user>/logs/<applicationId>

No caminho, `user` é o nome do utilizador que iniciou a aplicação. O `applicationId` é o identificador exclusivo atribuído a uma aplicação pelo RM YARN.

Os registos de agregados não são diretamente legíveis, que são escritos [TFile][T-file], [formato binário] [ binary-format] indexado por contentor. Utilize os registos YARN ResourceManager ou ferramentas da CLI para ver estes registos como texto não encriptado para aplicações ou contentores de interesse.

## <a name="yarn-cli-tools"></a>Ferramentas da CLI do YARN

Para utilizar as ferramentas da CLI do YARN, tem primeiro de ligar ao cluster do HDInsight através de SSH. Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](hdinsight-hadoop-linux-use-ssh-unix.md).

Pode ver estes registos como texto não encriptado, executando um dos seguintes comandos:

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

Especifique o &lt;applicationId >, &lt;-quem-iniciado-a-aplicação do utilizador >, &lt;ID do contentor >, e &lt;endereço do nó do trabalho > informações quando executar estes comandos.

## <a name="yarn-resourcemanager-ui"></a>IU do YARN ResourceManager

A IU do YARN ResourceManager é executada em headnode o cluster. É acedido através da IU da web do Ambari. Utilize os seguintes passos para ver os registos YARN:

1. No seu browser, navegue para https://CLUSTERNAME.azurehdinsight.net. Substitua CLUSTERNAME com o nome do cluster do HDInsight.
2. Na lista de serviços no lado esquerdo, selecione **YARN**.

    ![Serviço de yarn selecionado](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)
3. Do **ligações rápidas** lista pendente, selecione um de nós principais do cluster e, em seguida, selecione **ResourceManager registo**.

    ![Ligações rápidas yarn](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)

    É-lhe apresentada uma lista de ligações para registos YARN.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
